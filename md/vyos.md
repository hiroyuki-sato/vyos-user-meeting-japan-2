

## VyOS開発あれこれ

2014/07/27

佐藤 博之 @hiroysato

VyOS Users Meeting Japan #1

---


## この資料の目的

これからVyOSを触ろうという皆様へバグ修正や開発の仕方をさらっとお伝えする。

---

## VyOSを改造する動機

* 新しい機能を追加したい。
* バグを修正したい。
* なんとなく面白そう

---

## 開発ができるようになるまで流れ

* 開発に必要なツールや言語はなにか？
* 開発環境を構築する
* 実際に開発をしてみる(ケーススタディ)

---

## 開発に必要なツールや言語

* 開発用のOSについて知る
* 利用する言語やツール
* 主な情報収集サイト

---

## 開発用のOS

* Debian 6 (squeeze)
* [インストールイメージ](http://ftp.riken.jp/Linux/debian/debian/dists/squeeze/main/installer-amd64/20110106+squeeze4+b5/images/netboot/)
* ネットにアクセスできれば最小構成でOK

---

## 利用する言語やツール

* Perl: コマンド関連
* Python: 次期バックエンドで利用されている。
* bash sedなど: 簡単な設定ファイルの修正
* C言語: カーネルや、ルーティングソフトウェアなど
* Git: 分散バージョン管理(http://progit-ja.github.io)

---

## Git学習オススメ

![Pro Git](/images/git-pro.png)

---

## 主なサイト #1

* メインサイト: http://www.vyos.net
* 開発リポジトリ: http://github.com/vyos
* バグ・不具合一覧: http://bugzilla.vyos.net
* フォーラム: http://forum.vyos.net
* Twitter: vyos_dev
* IRC: #vyos on Freenode

---

## 主なサイト #2 日本語

* メインサイト: http://www.vyos-users.jp
* 翻訳サイト: http://wiki.vyos-users.jp/メインページ
* Googleグループ: https://groups.google.com/forum/#!forum/vyos-users-jp
* twitter: @vyosjp
* Debian 新メンテナーガイド: https://www.debian.org/doc/manuals/maint-guide/index.ja.html

---

## メインサイト

![www.vyos.net](/images/vyos_net.png)

---

## github

https://www.github.com/vyos

![https://www.github.com/vyos](/images/github.png)

---

## Bugzilla

http://bugzilla.vyos.net

![http://bugzilla.vyos.net](/images/bugzilla1.png)

--

## Bugzilla2

![http://bugzilla.vyos.net](/images/bugzilla2.png)

--

## Bugzilla3

![http://bugzilla.vyos.net](/images/bugzilla3.png)

--

## Bugzilla4

![http://bugzilla.vyos.net](/images/bugzilla4.png)

--

## Bugzilla5

![http://bugzilla.vyos.net](/images/bugzilla5.png)

--

## Bugzilla6

![http://bugzilla.vyos.net](/images/bugzilla6.png)

--

## Bugzilla7

![http://bugzilla.vyos.net](/images/bugzilla7.png)

---

## IRC

![IRC](/images/IRC1.png)

---

## 実際の構築

---


## 環境構築の流れ

* OSの導入
* 開発環境の設定

---

## Debianの導入

![Debian6](/images/debian.png)

---

## 開発環境の設定

setup-vyos-build-env

* VyOSのISOイメージ作成環境構築用コマンド
* 実行すると、ISOイメージ作成に必要なコマンドが入る。
* build-isoの中のtoolsディレクトリに入っている。

```
wget https://github.com/vyos/build-iso/blob/helium/tools/\# =>
     setup-vyos-build-env
sudo sh setup-vyos-build-env
```

---

## 開発のケーススタディ

* ISOイメージを作る
* linuxのカーネルをいじる
* コマンドの修正
* 0からのパッケージ作成

---

## ISOイメージを作る

* build-isoパッケージを取得する
* READMEに書いてあるコマンドを実行する
* make実行後
* livecd/binary.isoができる

```
git clone https://github.com/vyos/build-iso
# 証明書エラーでる場合
# GIT_SSL_NO_VERIFY=true git clone https://github.com/vyos/build-iso

export PATH=/sbin:/usr/sbin:$PATH
autoreconf -i
./configure
# heliumをビルドする人の場合
rm -f livecd/config.vyatta/chroot_sources/vyatta.chroot.gpg
wget -qO livecd/config.vyatta/chroot_sources/vyatta.chroot.gpg \
      http://dev.packages.vyos.net/vyos-root-gpg
sudo make iso

```

---

## カーネルを改造

* 参考サイト: vyos.net—Rebuild_VyOS_kernel_Step
* http://vyos.net/wiki/Rebuild_VyOS_kernel_Step

```
branch=helium
flavor=amd64-vyos

git submodule update --init pkgs/linux-image
cd pkgs/linux-image
git checkout $branch
```


---

## カーネルを改造

```
debian/bin/build-flavour.sh $flavor

...
make[1]: *** [debian/control-real] Error 1
make[1]: Leaving directory `/home/arch/build-iso/pkgs/linux-image'
make: *** [debian/control] Error 2

cd ../../ # Top directory in build-iso.

apt-get kernel-package


make linux-image
```

---

## コマンドを拡張する

---

## VyOS/Vyatta改造のバイブル

http://www.slideshare.net/m-asama/vyatta
![Vyatta改造入門](/images/vyatta_kaizou.png)

---

## 0からのパッケージ作成


---

## zabbix-agents

![zabbix-agents](/images/zabbix_agents1.png)

---

## zabbix-agents
https://github.com/hiroyuki-sato/vyos-zabbix-agents
![zabbix-agents](/images/zabbix_agents2.png)

---

## zabbix-agents

バイナリと設定ファイルだけなのでコピーすれば動きそう..

```
./bin/zabbix_get
./bin/zabbix_sender
./conf/zabbix_agent.conf
./conf/zabbix_agentd/userparameter_examples.conf
./conf/zabbix_agentd/userparameter_mysql.conf
./conf/zabbix_agentd.conf
./sbin/zabbix_agent
./sbin/zabbix_agentd
```

---

## 作成例 (zabbix-agents)

dh-makeの環境をいれて、メールアドレスと名前を設定する。

```
apt-get install dh-make

$ cat >>~/.bashrc <<EOF
DEBEMAIL="your.email.address@example.org"
DEBFULLNAME="Firstname Lastname"
export DEBEMAIL DEBFULLNAME
EOF
$ . ~/.bashrc
```

---

## 作成例 (zabbix-agents)

zabbix-agents用のディレクトリを作成

```
% mkdir vyos-zabbix-agents
% cd vyos-zabbix-agents
```
---

## 作成例 (zabbix-agents)

* debianの設定ファイルを作成する。_の後ろにバージョン番号を記述する。
* 質問に回答

```
% dh_make --native -p vyos-zabbix-agents_2.2.1

Type of package: single binary, indep binary, multiple binary, library, kernel module, kernel patch or cdbs?
 [s/i/m/l/k/n/b] s

Maintainer name : Hiroyuki Sato
Email-Address	: hiroysato@gmail.com 
Date				: Mon, 21 Apr 2014 20:32:44 +0900
Package Name	 : vyos-zabbix-agents
Version			: 2.2.1
License			: gpl3
Using dpatch	 : no
Type of Package : Single
Hit <enter> to confirm: 
Currently there is no top level Makefile. This may require additional tuning.
Done. Please edit the files in the debian/ subdirectory now. You should also
check that the vyos-zabbix-agents Makefiles install into $DESTDIR and not in / .
```

---

## 作成例 (zabbix-agents)

* debianディレクトリができる。
* 拡張子*.{ex,Ex}のファイルは不要なので削除する

```
% ls
debian

% cd debian

% ls
changelog           manpage.1.ex     README.Debian
compat              manpage.sgml.ex  README.source
control             manpage.xml.ex   rules
copyright           menu.ex          source
docs                postinst.ex      vyos-zabbix-agents.cron.d.ex
emacsen-install.ex  postrm.ex        vyos-zabbix-agents.default.ex
emacsen-remove.ex   preinst.ex       vyos-zabbix-agents.doc-base.EX
emacsen-startup.ex  prerm.ex         watch.ex
init.d.ex           README

% rm -f *.{ex,EX}
```

---

## 作成例 (zabbix-agents)

* READMEなどを適当に修正する

```
% ls
changelog  control    docs    README.Debian  rules
compat     copyright  README  README.source  source
```

---

## Makefile

Makefileを作る。やっていることはwgetしてファイルをコピーするだけ

```
VERSION="2.2.1"
BASE_URL="http://www.zabbix.com/downloads/${VERSION}"

OSTYPE=$(shell uname -a | grep -o x86_64 )

ifeq ($(OSTYPE),x86_64)
  ARCH=amd64  
  FILE="zabbix_agents_${VERSION}.linux2_6.amd64.tar.gz"
else
  ARCH=i386
  FILE="zabbix_agents_${VERSION}.linux2_6.i386.tar.gz"
endif

all: 
#	test -f ${FILE} || wget ${BASE_URL}/${FILE}
	wget ${BASE_URL}/${FILE}

install:
	@tar xvfz ${FILE} -C ${DESTDIR} 
	mkdir -p ${DESTDIR}/usr
	mv ${DESTDIR}/bin ${DESTDIR}/sbin ${DESTDIR}/usr
	mv ${DESTDIR}/conf ${DESTDIR}/etc


clean:
	rm -f ${FILE}
```

---

## debuildの実行

debuildコマンドを実行してパッケージを作る

```
debuild -uc -us
cd ..
ls *.deb
vyos-zabbix-agents_2.2.1-1_amd64.deb
```
---

## パッケージの組み込み

作成したパッケージをVyOSで読込みできるように準備

* build-iso/pkgs内にパッケージをコピー
* パッケージ名を追加
* 例: livecd/config.vyatta/chroot_local-packageslists/vyatta-extra.list
* 拡張子がlistで終っていれば何でも良い

---

## パッケージの組み込み

```
cat livecd/config.vyatta/chroot_local-packageslists/vyatta-extra.list 
vim-common
...
screen
vyos-zabbix-agents  # <<-- この行
```

---

## ご清聴ありがとうございました。

