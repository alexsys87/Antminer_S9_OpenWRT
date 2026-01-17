Сборка OpenWRT для Antminer S9
Инструкция по сборке прошивки OpenWRT для майнера Antminer S9. Тестировалось на Ubuntu 24.04.1 LTS.

для WSL создайте отдельного пользователя не root

```
sudo adduser user
su - user
```

Или, как альтернатива, установите переменную окружения:

```
export FORCE_UNSAFE_CONFIGURE=1
```

Для корректной работы в WSL выполните:

```
export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

Установка зависимостей

1. Обновление системы:

```
sudo apt update
```

Ошибка с репозиториями Docker (если возникает)
Если при sudo apt update появляются ошибки, связанные с Docker:

```
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "$UBUNTU_CODENAME") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Затем повторите sudo apt update.

```
sudo apt dist-upgrade
```

2. Установка зависимостей для сборки:

```
sudo apt install build-essential clang flex bison g++ gawk \
gcc-multilib g++-multilib gettext git libncurses5-dev libssl-dev \
python3-setuptools rsync swig unzip zlib1g-dev file wget
```

Сборка OpenWRT

1.Клонирование репозитория OpenWRT (выполнять не под root):

```
git clone https://github.com/openwrt/openwrt.git -b v24.10.5
cd openwrt
```

2.Очистка исходников:

```
make distclean
```

3.Обновление и установка пакетов:

```
./scripts/feeds update -a
./scripts/feeds install -a
```

4.Загрузка конфигурации для Antminer S9:

```
wget -O archive.tar.xz https://github.com/alexsys87/Antminer_S9_OpenWRT/raw/refs/heads/main/openwrt.tar.xz && tar -xJf archive.tar.xz --overwrite && rm archive.tar.xz
```

5.Конфигурация сборки:

```
make menuconfig
```

В меню конфигурации выберите нужные опции для вашего устройства

6.Сборка тулчейна:

```
make toolchain/install -j$(nproc) V=s
```

7.Сборка прошивки:

```
make -j$(nproc) V=s
```

Установка в nand

Выполнете в консоле

```
nand_install
```


