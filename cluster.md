# Cheat Sheet for the HSE Cluster

На кластере есть master нода, к которой вы подключаетесь и с которой отправляете задачи в очередь на ноды с вычислительными ресурсами. На ней можно выполнять простые вычисления и отлаживать код, но GPU там нет.

<!-- ## Содержание

- [Подключение](#подключение)
- [Работа на кластере](#работа-на-кластере) -->


## Подключение

Подключение к кластеру происходит по `ssh` с помощью команды:

```bash
ssh <username>@cluster.hpc.hse.ru -p 2222
```

После этого нужно ввести пароль.

Чтобы подключаться к кластеру без пароля (и избежать внезапных разрывов соединения), можно добавить свой SSH-ключ.  
Сначала сгенерируйте ключ:

```bash
ssh-keygen -t ed25519 -C "your_email@example.com" -f ~/.ssh/hse_cluster
```

Затем отправьте публичный ключ на кластер (потребуется ввести пароль):

```bash
ssh-copy-id -p 2222 -i ~/.ssh/hse_cluster <username>@cluster.hpc.hse.ru
```

После этого можно подключаться командой:

```bash
ssh -i ~/.ssh/hse_cluster -p 2222 <username>@cluster.hpc.hse.ru
```

Для удобства можно добавить `IdentityFile` в `~/.ssh/config`, чтобы подключаться короткой командой `ssh hse_cluster`.  
Итоговый конфиг будет выглядеть так:

```bash
Host hse_cluster
  HostName cluster.hpc.hse.ru
  User <username>
  Port 2222
  ForwardAgent yes
  IdentityFile ~/.ssh/hse_cluster
```

### Передача файлов

Для копирования файлов между локальной машиной и кластером используется `scp`.

Отправка файла с локальной машины на кластер выполняется командой:

```bash
scp /path/on/local hse_cluster:/path/on/cluster
```

## Работа на кластере

### ПО и компиляторы

Для подключения необходимого ПО нужно использовать команду `module load`, например:

```bash
module load Python  # Python
module load gnu14   # C++
```

### Conda

Для работы с окружениями используется `conda`. Минимальный список команд:

- `conda create <env_name>` – создание окружения; после активации можно устанавливать необходимые пакеты с помощью `pip`
- `conda env list` – список существующих окружений
- `conda activate <env_name>` – активация окружения
- `conda deactivate` – выход из окружения

## sbatch-скрипты

Вот пример базового скрипта для постановки задачи. Он задаёт директории для логов, запрашивает необходимые ресурсы, активирует рабочую среду и conda-окружение, а в конце запускает саму задачу:

```bash
#!/bin/bash

#SBATCH --job-name=single
#SBATCH --error=logs/single-%A-%a.err
#SBATCH --output=logs/single-%A-%a.log
#SBATCH --time=168:00:00
#SBATCH --nodes=1
#SBATCH --gpus=1

module load Python
module load gnu14
conda activate mrgsrec

python run_model.py -cn beauty_sasrec
```

Здесь:

- `#SBATCH --error` – файл для вывода ошибок  
- `#SBATCH --output` – файл для стандартного вывода  
- `#SBATCH --gpus` – количество необходимых GPU  

Всё остальное — обычный bash-скрипт, который отправляется на ноду и запускается.

### Базовые команды для работы

- `sbatch <sbatch_script>` – отправить задачу в очередь  
- `scancel --user=$USER` – отменить все свои задачи  
- `scancel <job_id>` – отменить конкретную задачу  
- `squeue --user=$USER` – посмотреть свою очередь задач  
