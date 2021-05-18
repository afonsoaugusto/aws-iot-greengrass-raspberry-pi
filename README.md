# aws-iot-greengrass-raspberry-pi

* [Tutorial](https://docs.aws.amazon.com/pt_br/greengrass/v1/developerguide/setup-filter.rpi.html)

## 1

```sh
pi@raspberrypi:~ $ hostname
raspberrypi
pi@raspberrypi:~ $ uname -a
Linux raspberrypi 5.10.17-v7+ #1403 SMP Mon Feb 22 11:29:51 GMT 2021 armv7l GNU/Linux
pi@raspberrypi:~ $ 
```

## 2

```sh
sudo adduser --system ggc_user
sudo addgroup --system ggc_group

pi@raspberrypi:~ $ sudo adduser --system ggc_user
Adding system user `ggc_user' (UID 115) ...
Adding new user `ggc_user' (UID 115) with group `nogroup' ...
Creating home directory `/home/ggc_user' ...
pi@raspberrypi:~ $ sudo addgroup --system ggc_group
Adding group `ggc_group' (GID 124) ...
Done.
pi@raspberrypi:~ $ 

```

## 3

```sh
pi@raspberrypi:~ $ cat  /etc/sysctl.d/98-rpi.conf
kernel.printk = 3 4 1 3
vm.min_free_kbytes = 16384
pi@raspberrypi:~ $ 
```

```sh
echo "kernel.printk = 3 4 1 3
vm.min_free_kbytes = 16384
fs.protected_hardlinks = 1
fs.protected_symlinks = 1" | sudo tee /etc/sysctl.d/98-rpi.conf

cat /etc/sysctl.d/98-rpi.conf
sudo reboot
sudo sysctl -a 2> /dev/null | grep fs.protected
```

```sh
pi@raspberrypi:~ $ sudo sysctl -a 2> /dev/null | grep fs.protected
fs.protected_fifos = 0
fs.protected_hardlinks = 1
fs.protected_regular = 0
fs.protected_symlinks = 1
pi@raspberrypi:~ $ 
```

## 4

```sh
pi@raspberrypi:/boot $ cat /boot/cmdline.txt 
console=serial0,115200 console=tty1 root=PARTUUID=d8fef53d-02 rootfstype=ext4 elevator=deadline fsck.repair=yes rootwait quiet splash plymouth.ignore-serial-consoles
pi@raspberrypi:/boot $ 
```

```sh
pi@raspberrypi:/boot $ cat /boot/cmdline.txt 
console=serial0,115200 console=tty1 root=PARTUUID=d8fef53d-02 rootfstype=ext4 elevator=deadline fsck.repair=yes rootwait quiet splash plymouth.ignore-serial-consoles cgroup_enable=memory cgroup_memory=1
pi@raspberrypi:/boot $ 
sudo reboot
```

## 5

```sh
sudo apt install openjdk-8-jdk -y
```

## 6

```sh
cd /home/pi/Downloads
mkdir greengrass-dependency-checker-GGCv1.11.x
cd greengrass-dependency-checker-GGCv1.11.x
wget https://github.com/aws-samples/aws-greengrass-samples/raw/master/greengrass-dependency-checker-GGCv1.11.x.zip
unzip greengrass-dependency-checker-GGCv1.11.x.zip
cd greengrass-dependency-checker-GGCv1.11.x
sudo modprobe configs
sudo ./check_ggc_dependencies | more
```

* Python 3.8: Not found
* NodeJS 12.x: Not found
* Java 8: Not found

### Python 3.8

```sh
sudo apt-get install -y build-essential tk-dev libncurses5-dev libncursesw5-dev libreadline6-dev libdb5.3-dev libgdbm-dev libsqlite3-dev libssl-dev libbz2-dev libexpat1-dev liblzma-dev zlib1g-dev libffi-dev tar wget vim

wget https://www.python.org/ftp/python/3.8.10/Python-3.8.10.tgz

tar zxf Python-3.8.10.tgz
cd Python-3.8.10
sudo ./configure --enable-optimizations
sudo make -j 4
sudo make altinstall
```

### Node

```sh
wget https://nodejs.org/dist/v14.17.0/node-v14.17.0-linux-armv7l.tar.xz
sudo mkdir -p /usr/local/lib/nodejs
sudo tar xJvf node-v14.17.0-linux-armv7l.tar.xz  -C /usr/local/lib/nodejs 
export PATH=/usr/local/lib/nodejs/node-v14.17.0-linux-armv7l/bin:$PATH
```

### Java

```sh
sudo apt install openjdk-8-jre-headless
```

## 7

```sh
pip3 install awscli
aws configure
```

```sh
curl -s https://d2s8p88vqu9w66.cloudfront.net/releases/greengrass-nucleus-latest.zip > greengrass-nucleus-latest.zip && unzip greengrass-nucleus-latest.zip -d GreengrassCore

sudo -E java -Droot="/greengrass/v2" -Dlog.store=FILE -jar ./GreengrassCore/lib/Greengrass.jar --aws-region us-east-1 --thing-name GreengrassQuickStartCore-17971d9d09a --thing-group-name GreengrassQuickStartGroup --component-default-user ggc_user:ggc_group --provision true --setup-system-service true --deploy-dev-tools true
```
