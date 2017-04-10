---
title: "Azure の Linux VM へのユーザーの追加 | Microsoft Docs"
description: "Azure 上の Linux VM にユーザーを追加します。"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f8aa633b-8b75-45d7-b61d-11ac112cedd5
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/18/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 3bb2032ed54ffc05214c771f17af81dd8b4864ab


---
# <a name="add-a-user-to-an-azure-vm"></a>Azure VM へのユーザーの追加
新しい Linux VM で最初に行うタスクの 1 つは、新しいユーザーの作成です。  この記事では、sudo ユーザー アカウントを作成し、パスワードを設定して、SSH 公開キーを追加した後、最後に `visudo` を使用してパスワードなしで sudo を許可するまでの手順を説明します。

前提条件としては、[Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)、[SSH の公開キーと秘密キー](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)、Azure リソース グループがあり、Azure CLI がインストールされていて、`azure config mode arm` を使用して Azure Resource Manager モードに切り替えてあることです。

## <a name="quick-commands"></a>クイック コマンド
```bash
# Add a new user on RedHat family distros
sudo useradd -G wheel exampleUser

# Add a new user on Debian family distros
sudo useradd -G sudo exampleUser

# Set a password
sudo passwd exampleUser
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully

# Copy the SSH Public Key to the new user
ssh-copy-id -i ~/.ssh/id_rsa exampleuser@exampleserver

# Change sudoers to allow no password
# Execute visudo as root to edit the /etc/sudoers file
visudo

# On RedHat family distros uncomment this line:
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

# to this
## Same thing without a password
%wheel        ALL=(ALL)       NOPASSWD: ALL

# On Debian family distros change this line:
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# to this
%sudo   ALL=(ALL) NOPASSWD:ALL

# Verify everything
# Verify the SSH keys & User account
bill@slackware$ ssh -i ~/.ssh/id_rsa exampleuser@exampleserver

# Verify sudo access
sudo top
```

## <a name="detailed-walkthrough"></a>詳細なチュートリアル
### <a name="introduction"></a>はじめに
新しいサーバーで最初に行う最も一般的なタスクの 1 つは、ユーザー アカウントの追加です。  ルート ログインは無効にする必要があり、ルート アカウント自体も Linux サーバーでは使わないようにし、sudo のみを使用する必要があります。  sudo を使用してユーザーにルート昇格特権を与えるのが、Linux を管理および使用する適切な方法です。

`useradd` コマンドを使用して、ユーザー アカウントを Linux VM に追加します。  `useradd` を実行すると、`/etc/passwd`、`/etc/shadow`、`/etc/group`、および `/etc/gshadow` が変更されます。  また、コマンド ライン フラグを `useradd` コマンドに追加して、新しいユーザーを Linux の適切な sudo グループに追加します。  `useradd` は `/etc/passwd` にエントリを作成しますが、新しいユーザー アカウントへのパスワードの設定は行いません。  簡単な `passwd` コマンドを使用して、新しいユーザーの初期パスワードを作成します。  最後に、sudo 規則を変更して、ユーザーがすべてのコマンドに対してパスワードを入力しなくても sudo 特権でコマンドを実行できるようにします。  秘密キーを使用してログインしているので、ユーザー アカウントは不正なアクターに対して安全であり、パスワードなしでの sudo アクセスが許可されるものと想定しています。  

### <a name="adding-a-single-sudo-user-to-an-azure-vm"></a>Azure VM への 1 人の sudo ユーザーの追加
SSH キーを使用して Azure VM にログインします。  SSH 公開キー アクセスをセットアップしていない場合は、最初に「 [Using Public Key Authentication with Azure](http://link.to/article)」 (Azure での公開キー認証の使用) を参照してください。  

`useradd` コマンドは次の処理を行います。

* 新しいユーザー アカウントを作成します
* 同じ名前で新しいユーザー グループを作成します
* 空白のエントリを `/etc/passwd`
* 空白のエントリを `/etc/gpasswd`

`-G` コマンド ライン フラグは、適切な Linux グループに新しいユーザー アカウントを追加して、新しいユーザー アカウントにルート昇格特権を与えます。

#### <a name="add-the-user"></a>ユーザーを追加する
```bash
# On RedHat family distros
sudo useradd -G wheel exampleUser

# On Debian family distros
sudo useradd -G sudo exampleUser
```

#### <a name="set-a-password"></a>パスワードを設定する
`useradd` コマンドは、ユーザーを作成し、`/etc/passwd` と `/etc/gpasswd` の両方にエントリを追加しますが、実際にはパスワードを設定しません。  パスワードは、 `passwd` コマンドを使用してエントリに追加されます。

```bash
sudo passwd exampleUser
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
```

サーバーでの sudo 特権を持つユーザーが作成されました。

### <a name="add-your-ssh-public-key-to-the-new-user-account"></a>新しいユーザー アカウントに SSH 公開キーを追加する
コンピューターから、 `ssh-copy-id` コマンドを使用して、新しいパスワードを指定します。

```bash
ssh-copy-id -i ~/.ssh/id_rsa exampleuser@exampleserver
```

### <a name="using-visudo-to-allow-sudo-usage-without-a-password"></a>visudo を使用してパスワードなしでの sudo の使用を許可する
`visudo` を使用して `/etc/sudoers` ファイルを編集し、この重要なファイルの不正変更に対する保護階層をいくつか追加します。  `visudo` を実行すると、`/etc/sudoers` ファイルはロックされて、アクティブな編集中は他のユーザーはそのファイルを変更できなくなります。  保存または終了時にも、`visudo` によって `/etc/sudoers` ファイルの誤りがチェックされるので、破損した sudoers ファイルを保存することはできません。

既に sudo アクセス用の適切な既定グループにユーザーがいます。  次に、このグループがパスワードなしで sudo を使用できるようにします。

```bash
# Execute visudo as root to edit the /etc/sudoers file
visudo

# On RedHat family distros uncomment this line:
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

# to this
## Same thing without a password
%wheel        ALL=(ALL)       NOPASSWD: ALL

# On Debian family distros change this line:
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# to this
%sudo   ALL=(ALL) NOPASSWD:ALL
```

### <a name="verify-the-user-ssh-keys-and-sudo"></a>ユーザー、SSH キー、sudo を検証する
```bash
# Verify the SSH keys & User account
ssh -i ~/.ssh/id_rsa exampleuser@exampleserver

# Verify sudo access
sudo top
```



<!--HONumber=Nov16_HO3-->


