# Как установить VMware Workstation на Linux-системы

## Для ОС RedHat

```
yum update
```

Скачай [VMware](https://www.vmware.com/products/workstation-pro/workstation-pro-evaluation.html?ClickID=c4nsxpzwqqla7qlapiv7lkspfip4f4xqvnea)
И запускай установщик

```
chmod a+x VMware-Workstation-Full-14.1.1-7528167.x86_64.bundle
./VMware-Workstation-Full-14.1.1-7528167.x86_64.bundle
```

Установи VMware и дополни окружение

```
yum groupinstall "Development tools"
rpm -qa | grep kernel-headers
yum install kernel-headers
yum install kernel-devel 
```

Вроде всё ок, можно запускать

```
vmware
```

***

## Для ОС Debian

```
apt-get update && apt-get upgrade
```

Скачай [VMware](https://www.vmware.com/products/workstation-pro/workstation-pro-evaluation.html?ClickID=c4nsxpzwqqla7qlapiv7lkspfip4f4xqvnea)
И также запускай установщик

```
chmod a+x VMware-Workstation-Full-14.1.1-7528167.x86_64.bundle
./VMware-Workstation-Full-14.1.1-7528167.x86_64.bundle
```

Также надо установить VMware и дополнить окружение

```
apt-get install build-essential
dpkg -l | grep linux-headers
apt-get install linux-headers-`uname -r`
```

Вроде всё ок, можно запускать

```
vmware
```
