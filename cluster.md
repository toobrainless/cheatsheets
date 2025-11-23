# CheatSheet for HSE cluster



#### Активация Python среды

```bash
module load Python
```

#### Conda

Список настроенных окружений

```bash
conda env list
```

Активировать среду

```bash
conda activate <env_name>
```

#### Отмена

```bash
scancel --user=$USER
scancel <job_id>
```

#### Очередь

```bash
squeue --user=$USER
```

#### Запуск

```bash
sbatch <sbatch_script>
```

#### Пример sbatch скрипта

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
- `#SBATCH --output` – файл для всего остального вывода
- `#SBATCH --gpus` – кол-во необходимых GPU

все остальное просто баш скрипт, который отправляется на ноду и запускается

