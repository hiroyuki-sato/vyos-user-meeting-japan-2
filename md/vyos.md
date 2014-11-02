

## VyOS Meeting #2

2014/11/02

Hiroyuki Sato @hiroysato

VyOS Users Meeting Japan #2

---

## About me

* Hiroyuki Sato
* twitter: @hiroysato
* GitHUB: https://github.com/hiroyuki-sato
* Made Infiniband commands for Vyatta.
* Sales

---

## Contributions for VyOS

* Fix scp poblem [Bug #180]
* Create setup-vyos-build-env
* Fix vyatta-cfg-qos shaper help [Bug #218] (Reported by @ebiken)
* Write Wiki Rebuild VyOS kernel Step (also Japanese)



---

## Agenda

* Setup Build environment on Debian jessie.
* ssh-keygen segmentation fault bug #345
* Zabbix agent for VyOS.


---

## Previous meeting agenda.

* VyOS development environment on Debian squeeze.
* Development example zabbix-agent for VyOS

---

## Feedback VyOS development

Feedback after previous meeting [#1](https://twitter.com/henrich/status/493300065822191617) [#2](https://twitter.com/hiroysato/status/495235218089848832)

![](images/pbuilder1.png)


---

## Debian *Squeeze* problem

After meeting, Debian guy adviced me. 

* It is EOL (31/May/2014)
* It is like CentOS4 or Windows XP *too*
* You should use modern Debian environment 
* You can use cowbuilder, pbuilder

---

### What is cowbulder and pbuilder

* cowbuilder Executes the specified pbuilder operation with cowdancer
* pbuilder - personal package builder 

---

### Debian jessie VyOS environment

![Overview](images/pbuilder2.png)

---

### Step#1 Install Debian jessie

Install Debian jessie

* [Jessie ISO](http://ftp.debian.org/debian/dists/jessie/main/installer-amd64/current/images/netboot/mini.iso)

And Install cowbuilder

    jessie# apt-get install cowbuilder

---

### Step#2 make debian squeeze and VyOS environment.

Create cowbuiler environment

    jessie# sudo cowbuilder \
      --create --distribution squeeze \
      --basepath /var/cache/pbuilder/base-vyos-squeeze-amd64.cow

* Create the base.cow image
* basepath: path of VyOS environment
* distribution: distribution name

---

### Step#3 Download VyOS setup script.

    jessie# cd /tmp
    jessie# wget https://github.com/vyos/build-iso/blob/helium/tools/\
            setup-vyos-build-env 
    jessie# chmod 755 setup-vyos-build-env
    

* setup-vyos-build-env : VyOS environment setup script on debian squeeze.

---

### Step#4 update squeeze image

* Login squeeze
* --save-after-exec is important option for save update image.

 

    jessie# sudo cowbuilder \
      --login \
      --save-after-exec \
      --bindmount /tmp \
      --basepath /var/cache/pbuilder/base-vyos-squeeze-amd64.cow/

---

### Step#5 execute setup-vyos-build-env

* And exec ./setup-vyos-build-env script
* Additonal package needed. (cpio and automake)


  
    squeeze# cd /tmp
    squeeze# ./setup-vyos-build-env
    squeeze# apt-get install cpio automake
    squeeze# exit 

---

### Step#6 Download build-iso on jesssie.

    jessie# git clone https://github.com/vyos/build-iso 
    

---

### make iso image 

login squeeze environment 

    jessie# sudo cowbuilder \
      --login \
      --bindmount /tmp \
      --basepath /var/cache/pbuilder/base-vyos-squeeze-amd64.cow/

---

prepare build
    
    squeeze# cd /tmp/build-iso 
    squeeze# export PATH=/sbin:/usr/sbin:$PATH
    squeeze# autoreconf -i
    squeeze# ./configure
    

Replace gpg key (*development branch only*)

    squeeze# rm -f livecd/config.vyatta/chroot_sources/vyatta.chroot.gpg
    squeeze# wget -qO livecd/config.vyatta/chroot_sources/vyatta.chroot.gpg \
         http://dev.packages.vyos.net/vyos-root-gpg

And make iso

    squeeze# make iso
    

---

## Build log

[Build log sample](https://gist.github.com/hiroyuki-sato/f730a246a9b5f7b577f2)

---

## VyOS Bug #345

* Filed Bug #345 by @lina_taso [2014/10/12]
* Tweet similar issue by @twovs [2014/10/29]
* Contact them [2014/10/29]
* Try to fix problem. (execute strace, install images) 
* Try same command on VyOS 1.0.4 by (@twovs) 
* Post gooogle group same issue by hide [2014/10/30]
* Contact dmbaturin and report this problem. [2014/10/31]
* Summarize [VyOS Bug#345 SSH command returns "Segmentation fault"](https://gist.github.com/hiroyuki-sato/6cfac97d21a26e3d5cc9)

---


![Bug345 filed](images/bug345_1.png)

---


![Bug345 detail](images/bug345_2.png)

---


![Bug345 detail](images/bug345_3.png)

---


![Bug345 detail](images/bug345_4.png)

---

### Try to fix Bug#345

* [Try to Fix Bug#345](https://gist.github.com/hiroyuki-sato/6fa390e2ca7dd42fe9aa)
* [Try to Fix](https://gist.github.com/hiroyuki-sato/6cfac97d21a26e3d5cc9)

---

### Zabbix agent for VyOS

* Bundled snmp agent can't collect much information from VyOS.
* That's why to use Zabbix-agent.

* [Zabbix agent commands for VyOS](https://github.com/hiroyuki-sato/vyos-cfg-zabbix-agent)
* [Zabbix agent demo](http://komeiy.hatenablog.com/entry/2014/10/24/002107)
* [How to setup](http://www.downtown.jp/~soukaku/archives/2014/0802_145203.html)


