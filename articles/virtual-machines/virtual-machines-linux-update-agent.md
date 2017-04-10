---
title: "GitHub から Azure Linux エージェントを更新する | Microsoft Docs"
description: "Azure の Linux VM で使用する Azure Linux エージェントを GitHub の最新バージョンに更新する方法について説明します"
services: virtual-machines-linux
documentationcenter: 
author: SuperScottz
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/14/2015
ms.author: mingzhan
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: d35e70c1d50bd4cb2d47188585354aa3e3d740d4
ms.lasthandoff: 03/29/2017


---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm-to-the-latest-version-from-github"></a>VM 上の Azure Linux エージェントを GitHub で最新バージョンに更新する方法
Azure 上の Linux VM の [Azure Linux エージェント](https://github.com/Azure/WALinuxAgent) を更新するには、既に次の環境が整っている必要があります。

1. Linux VM が Azure で実行されている。
2. SSH を使用してその Linux VM に接続している。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

<br>

> [!NOTE]
> Windows コンピューターでこのタスクを実行する場合は、PuTTY を使用して Linux マシンに SSH 接続できます。 詳細については、「[Linux を実行する仮想マシンにログオンする方法](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。
> 
> 

Azure での動作保証済み Linux ディストリビューションは、そのリポジトリに Azure Linux エージェント パッケージを格納しているため、可能な場合は、そのディストリビューションのリポジトリを確認して最新のバージョンをインストールしてください。  

Ubuntu の場合は、次のように入力するだけでかまいません。

```bash
sudo apt-get install walinuxagent
```

また、CentOS に次のように入力します。

```bash
sudo yum install waagent
```

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
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
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
enabled=0
```

次を入力します。

```bash
sudo yum update WALinuxAgent
```

通常、必要な手順はこれですべてですが、何らかの理由がある場合は、これを https://github.com から直接インストールする必要があります。その場合は、次の手順を実行してください。

## <a name="install-wget"></a>wget のインストール
SSH を使用して VM にログインします。

コマンド ラインで「 `#sudo yum install wget` 」と入力して、wget をインストールします (Redhat、CentOS、Oracle Linux Version 6.4、6.5 のように既定ではインストールされないディストリビューションもあります)。

## <a name="download-the-latest-version"></a>最新バージョンをダウンロードする
[GitHub の Azure Linux エージェントのリリース](https://github.com/Azure/WALinuxAgent/releases) が記載されている Web ページを開き、最新バージョンを見つけます。 (「 `#waagent --version`」と入力すると最新のバージョンを検索できます)。

### <a name="for-version-20x-type"></a>バージョン 2.0.x の場合は次のように入力します:

```bash
wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent
```

次は、バージョン 2.0.14 を例として使用しています。

```bash
wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent
```

### <a name="for-version-21x-or-later-type"></a>バージョン 2.1.x 以降の場合は次のように入力します:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
unzip WALinuxAgent-[version].zip
cd WALinuxAgent-[version]
```

次の行は、バージョン 2.1.0 を例として使用しています。

```bash
wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
unzip WALinuxAgent-2.1.0.zip  
cd WALinuxAgent-2.1.0
```

## <a name="install-the-azure-linux-agent"></a>Azure Linux エージェントをインストールします。
### <a name="for-version-20x-use"></a>バージョン 2.0.x の場合は次のコマンドを使用します。
waagent を実行可能にする:

```bash
chmod +x waagent
```

新しい実行可能ファイルを /usr/sbin にコピーする。

ほとんどの Linux では、次のコマンドを使用します。

```bash
sudo cp waagent /usr/sbin
```

CoreOs の場合は、次のコマンドを使用します。

```bash
sudo cp waagent /usr/share/oem/bin/
```

Azure Linux エージェントを新規にインストールの場合は、次を実行します。

```bash
sudo /usr/sbin/waagent -install -verbose
```

### <a name="for-version-21x-use"></a>バージョン 2.1.x の場合は次を使用します。
パッケージ `setuptools` を先にインストールする必要がある場合は、 [こちら](https://pypi.python.org/pypi/setuptools)をご覧ください。 次に、以下を実行します。

```bash
sudo python setup.py install
```

## <a name="restart-the-waagent-service"></a>waagent サービスを再起動します
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

## <a name="confirm-the-azure-linux-agent-version"></a>Azure Linux エージェントのバージョンを確認する
    
```bash
waagent -version
```

CoreOS では、上記のコマンドが機能しない場合があります。

これで、Azure Linux エージェントのバージョンが新しいバージョンに更新されたことが確認できます。

Azure Linux エージェントの詳細については、 [Azure Linux エージェントの README](https://github.com/Azure/WALinuxAgent)を参照してください。


