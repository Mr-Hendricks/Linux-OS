﻿# Настройка окружения

Необходимо настроить окружение для недавно созданного пользователя.
Во-первых, создадим `.bash_profile`:

```bash
cat > ~/.bash_profile << "EOF"
exec env -i HOME=$HOME TERM=$TERM PS1='\u:\w\$ ' /bin/bash
EOF
```

Далее создадим базовый `.bashrc`:

```bash
cat > ~/.bashrc << "EOF"
set +h
umask 022
LIN=/mnt/lin
LC_ALL=C
LIN_TGT=$(uname -m)-lin-linux-gnu
PATH=/usr/bin
if [ ! -L /bin ]; then PATH=/bin:$PATH; fi
PATH=$LIN/tools/bin:$PATH
export LIN LC_ALL LIN_TGT PATH CONFIG_SITE
EOF
```

## CFLAGS и CXXFLAGS

Использовать оптимизацию на данном этапе не стоит, однако вы можете добавить флаг `-s`, чтобы сразу после сборки автоматически удалялись ненужные и отладочные символы, а также `-O2` во избежание ошибки сборки glibc:

```bash
echo "export CFLAGS=\"-s -O2\" " >> ~/.bashrc
echo "export CXXFLAGS=\"-s -O2\" " >> ~/.bashrc
```

Это может сэкономить несколько гигабайт места на диске.

## Bash-completion

Если вы используете программу `bash-completion`, то можете добавить её поддержку для пользователя `lin`:

```bash
echo " . /etc/bash_completion" >> ~/.bashrc
```

## MAKEFLAGS

Для экономии времени на многоядерном процессоре используйте параллельную сборку. Чтобы её включить, надо добавить для `make` переменную `-jN`, где `N` - число потоков вашего процессора.
Это можно сделать двумя способами:

- Указывать при каждом вызове `make` аргумент `-jN`
- Добавить переменную окружения `MAKEFLAGS`

Замените `N` на число потоков вашего процессора.

О дополнительной информации о потоках процессора читайте [здесь](prepare/about-threads).

## Для multilib

Для multilib выполните:

```bash
echo "export LIN_TGT32=i686-lin-linux-gnu" >> ~/.bashrc
```

Эта переменная используется для сборки i386 библиотек.

## Применение изменений

Для того чтобы применить изменения, выполните:

```bash
source ~/.bash_profile
```

## Значения параметров базового bashrc

`set +h` - Данный параметр отключает сохранение путей к исполняемым файлам в памяти bash. Это необходимо для того чтобы новые исполняемые файлы становились доступны немедленно.

`umask 022` - Гарантирует, что для новых файлов будут установлены права 644.

`LIN=/mnt/lin` - Задает путь к корню собираемый системы. `mnt/lin` взят в качестве образца.

`LC_ALL=C` - Исключает связанные с локализацией ошибки.

`PATH=/usr/bin if [ ! -L /bin ]; then PATH=/bin:$PATH; fi` - Задаёт пути поиска исполняемых файлов в хост-системе.

`PATH=$LIN/tools/bin:$PATH` - Необходимо для обнаружения исполняемых файлов кросс-компилятора.
