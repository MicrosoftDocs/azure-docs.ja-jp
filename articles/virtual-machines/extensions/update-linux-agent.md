---
title: GitHub から Azure Linux エージェントを更新する
description: Azure の Linux VM で使用する Azure Linux エージェントを更新する方法について説明します
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 08/02/2017
ms.author: akjosh
ms.openlocfilehash: 2215136c02d9cf967e0184af7588ce8d48362009
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072937"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>VM で Azure Linux エージェントを更新する方法

Azure 上の Linux VM の [Azure Linux エージェント](https://github.com/Azure/WALinuxAgent) を更新するには、既に次の環境が整っている必要があります。

- Linux VM が Azure で実行されている。
- SSH を使用してその Linux VM に接続している。

まずは常に Linux ディストリビューション リポジトリでパッケージを確認することをお勧めします。 使用できるパッケージが最新のバージョンでない可能性もありますが、自動更新を有効にすると Linux エージェントが常に最新の更新プログラムを入手します。 パッケージ マネージャーからのインストールに問題がある場合は、ディストリビューション ベンダーにサポートを依頼してください。

## <a name="minimum-virtual-machine-agent-support-in-azure"></a>Azure での仮想マシン エージェントの最小バージョンのサポート
先に進む前に、[Azure での仮想マシン エージェントの最小バージョンのサポート](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)を確認してください。

## <a name="updating-the-azure-linux-agent"></a>Azure Linux エージェントを更新する

## <a name="ubuntu"></a>Ubuntu

#### <a name="check-your-current-package-version"></a>現在のパッケージのバージョンを確認する

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>パッケージ キャッシュを更新する

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>最新バージョンのパッケージをインストールする

```bash
sudo apt-get install walinuxagent
```

#### <a name="ensure-auto-update-is-enabled"></a>自動更新が有効になっていることを確認する

まず、次を実行して自動更新が有効になっているかどうかを確認します。

```bash
cat /etc/waagent.conf
```

'AutoUpdate.Enabled' を検索する。 この出力がある場合、自動更新は有効になっています。

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

有効にするには、次を実行します。

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>waagent サービスを再起動します

#### <a name="restart-agent-for-1404"></a>14.04 のエージェントをリセットする

```bash
initctl restart walinuxagent
```

#### <a name="restart-agent-for-1604--1704"></a>16.04 / 17.04 のエージェントをリセットする

```bash
systemctl restart walinuxagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-wheezy"></a>Debian 7 “Wheezy”

#### <a name="check-your-current-package-version"></a>現在のパッケージのバージョンを確認する

```bash
dpkg -l | grep waagent
```

#### <a name="update-package-cache"></a>パッケージ キャッシュを更新する

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>最新バージョンのパッケージをインストールする

```bash
sudo apt-get install waagent
```

#### <a name="enable-agent-auto-update"></a>エージェントの自動更新を有効にする
このバージョンの Debian には 2.0.16 以上のバージョンがないため、自動更新は使用できません。 上記のコマンドの出力で、パッケージが最新であるかどうかがわかります。

### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 “Jessie” / Debian 9 “Stretch”

#### <a name="check-your-current-package-version"></a>現在のパッケージのバージョンを確認する

```bash
apt list --installed | grep waagent
```

#### <a name="update-package-cache"></a>パッケージ キャッシュを更新する

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>最新バージョンのパッケージをインストールする

```bash
sudo apt-get install waagent
```
#### <a name="ensure-auto-update-is-enabled"></a>自動更新が有効になっていることを確認する 

まず、次を実行して自動更新が有効になっているかどうかを確認します。

```bash
cat /etc/waagent.conf
```

'AutoUpdate.Enabled' を検索する。 この出力がある場合、自動更新は有効になっています。

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

有効にするには、次を実行します。

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>waagent サービスを再起動します

```
sudo systemctl restart walinuxagent.service
```

## <a name="red-hat--centos"></a>Red Hat / CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

#### <a name="check-your-current-package-version"></a>現在のパッケージのバージョンを確認する

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>使用できる更新プログラムを確認する

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>最新バージョンのパッケージをインストールする

```bash
sudo yum install WALinuxAgent
```

#### <a name="ensure-auto-update-is-enabled"></a>自動更新が有効になっていることを確認する 

まず、次を実行して自動更新が有効になっているかどうかを確認します。

```bash
cat /etc/waagent.conf
```

'AutoUpdate.Enabled' を検索する。 この出力がある場合、自動更新は有効になっています。

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

有効にするには、次を実行します。

```bash
sudo sed -i 's/\# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>waagent サービスを再起動します

```
sudo service waagent restart
```

### <a name="rhelcentos-7"></a>RHEL/CentOS 7

#### <a name="check-your-current-package-version"></a>現在のパッケージのバージョンを確認する

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>使用できる更新プログラムを確認する

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>最新バージョンのパッケージをインストールする

```bash
sudo yum install WALinuxAgent  
```

#### <a name="ensure-auto-update-is-enabled"></a>自動更新が有効になっていることを確認する 

まず、次を実行して自動更新が有効になっているかどうかを確認します。

```bash
cat /etc/waagent.conf
```

'AutoUpdate.Enabled' を検索する。 この出力がある場合、自動更新は有効になっています。

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

有効にするには、次を実行します。

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>waagent サービスを再起動します

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

#### <a name="check-your-current-package-version"></a>現在のパッケージのバージョンを確認する

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>使用できる更新プログラムを確認する

上記の出力で、パッケージが最新であるかどうかがわかります。

#### <a name="install-the-latest-package-version"></a>最新バージョンのパッケージをインストールする

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>自動更新が有効になっていることを確認する 

まず、次を実行して自動更新が有効になっているかどうかを確認します。

```bash
cat /etc/waagent.conf
```

'AutoUpdate.Enabled' を検索する。 この出力がある場合、自動更新は有効になっています。

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

有効にするには、次を実行します。

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>waagent サービスを再起動します

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

#### <a name="check-your-current-package-version"></a>現在のパッケージのバージョンを確認する

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>使用できる更新プログラムを確認する

上記のコマンドの出力で、パッケージが最新であるかどうかがわかります。

#### <a name="install-the-latest-package-version"></a>最新バージョンのパッケージをインストールする

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>自動更新が有効になっていることを確認する 

まず、次を実行して自動更新が有効になっているかどうかを確認します。

```bash
cat /etc/waagent.conf
```

'AutoUpdate.Enabled' を検索する。 この出力がある場合、自動更新は有効になっています。

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

有効にするには、次を実行します。

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>waagent サービスを再起動します

```bash
sudo systemctl restart waagent.service
```

## <a name="oracle-6-and-7"></a>Oracle 6 および 7

Oracle Linux の場合、 `Addons` リポジトリが有効になっていることを確認します。 `/etc/yum.repos.d/public-yum-ol6.repo` ファイル (Oracle Linux 6) または`/etc/yum.repos.d/public-yum-ol7.repo` ファイル (Oracle Linux) を選択して編集し、このファイルの **[ol6_addons]** または **[ol7_addons]** の下の行 `enabled=0` を `enabled=1` に変更します。

次に、最新バージョンの Azure Linux エージェントをインストールし、次のように入力します。

```bash
sudo yum install WALinuxAgent
```

アドオンのリポジトリが見つからない場合、Oracle Linux リリースに応じて、.repo ファイルの末尾に次の行を追加してください。

Oracle Linux 6 仮想マシンの場合:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=https://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=https://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Oracle Linux 7 仮想マシンの場合:

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=1
```

次を入力します。

```bash
sudo yum update WALinuxAgent
```

通常、必要な手順はこれですべてですが、何らかの理由がある場合は、これを https://github.com から直接インストールする必要があります。その場合は、次の手順を実行してください。


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>ディストリビューションにエージェントのパッケージが存在しない場合に Linux エージェントを更新する

コマンド ラインで「`sudo yum install wget`」と入力して、wget をインストールします (Red Hat、CentOS、Oracle Linux Version 6.4、6.5 のように既定ではインストールされないディストリビューションもあります)。

### <a name="1-download-the-latest-version"></a>1.最新バージョンをダウンロードする
[GitHub の Azure Linux エージェントのリリース](https://github.com/Azure/WALinuxAgent/releases) が記載されている Web ページを開き、最新バージョンを見つけます。 (「 `waagent --version`」と入力すると最新のバージョンを検索できます)。

#### <a name="for-version-22x-or-later-type"></a>バージョン 2.2.x 以降の場合は次のように入力します:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

次の行はバージョン 2.2.0 を例として使用しています。

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2.Azure Linux エージェントをインストールします。

#### <a name="for-version-22x-use"></a>バージョン 2.2.x の場合は次を使用します。
パッケージ `setuptools` を先にインストールする必要がある場合は、 [こちら](https://pypi.python.org/pypi/setuptools)をご覧ください。 次に、以下を実行します。

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>自動更新が有効になっていることを確認する

まず、次を実行して自動更新が有効になっているかどうかを確認します。

```bash
cat /etc/waagent.conf
```

'AutoUpdate.Enabled' を検索する。 この出力がある場合、自動更新は有効になっています。

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

有効にするには、次を実行します。

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3.waagent サービスを再起動します
ほとんどの Linux ディストリビューションでは、次のコマンドを使用します。

```bash
sudo service waagent restart
```

Ubuntu の場合は、次のコマンドを使用します。

```bash
sudo service walinuxagent restart
```

CoreOs の場合は、次のコマンドを使用します。

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4.Azure Linux エージェントのバージョンを確認する
    
```bash
waagent -version
```

CoreOS では、上記のコマンドが機能しない場合があります。

これで、Azure Linux エージェントのバージョンが新しいバージョンに更新されたことが確認できます。

Azure Linux エージェントの詳細については、 [Azure Linux エージェントの README](https://github.com/Azure/WALinuxAgent)を参照してください。
