

## VyOS開発あれこれ

2014/07/27

佐藤 博之 @hiroysato

VyOS Users Meeting Japan #1

---


## この資料の目的

これからVyOSを触ろうという皆様へバグ修正や開発の仕方をさらっとお伝えする。

---

## 動機

* 新しい機能を追加したい。
* バグを修正したい。
* なんとなく面白そう

---

## 開発ツールやサイト

* 開発用のOS
* 利用する言語やツール
* 主なサイト

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

---

## 実際の構築

---


## 環境構築の流れ

* OSの導入
* 開発環境の設定
* イメージのビルド
* コマンドの修正

---

## setup-vyos-build-env

* VyOSのISOイメージ作成環境構築用コマンド
* 実行すると、ISOイメージ作成に必要なコマンドが入る。
* build-isoの中のtoolsディレクトリに入っている。

```
wget https://github.com/vyos/build-iso/blob/helium/tools/\# =>
     setup-vyos-build-env
sudo sh setup-vyos-build-env
```

---

## ISOイメージの取得

* build-isoパッケージを取得する
* READMEに書いてあるコマンドを実行する
* make実行後livecd/binary.isoというイメージファイルができます。

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
make linux-image
```

---

## コマンドを拡張する

---
