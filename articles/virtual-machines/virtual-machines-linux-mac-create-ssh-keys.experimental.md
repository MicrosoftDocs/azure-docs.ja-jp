---
title: "Azure での Linux VM 用の SSH キー ペアの作成 | Microsoft Docs"
description: "Azure Linux VM 用の SSH 公開キーと秘密キーのペアを安全に作成します。"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: 
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: rasquill
experiment_id: rasquill-ssh-20170308
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 878b297c4c9e5ef4a7177e9bd5dc564a0c83680b
ms.lasthandoff: 03/10/2017



---

# <a name="create-an-ssh-public-and-private-key-pair-for-linux-vms"></a>Linux VM 用の SSH 公開キーと秘密キーのペアの作成

この記事では、Linux VM で使用する SSH プロトコル バージョン 2 RSA の公開キー ファイルと秘密キー ファイルのペアを生成する方法について説明します。  SSH キー ペアを使用すると、既定で認証に SSH キーを使用する仮想マシンを Azure に作成できます。そのため、ログインするためのパスワードが不要になります。  パスワードは推測できる場合もあるため、VM が執拗なブルート フォース攻撃にさらされる危険性が生じかねません。 Azure テンプレートまたは `azure-cli` で作成された VM には、デプロイの一部として SSH 公開キーを含めることができるため、SSH のパスワード ログインを無効にするというデプロイ後の構成手順を省略できます。

## <a name="quick-commands"></a>クイック コマンド

例を独自の値に置き換えて、Bash シェルから次のコマンドを実行します。

SSH 公開キー ファイルは既定で `~/.ssh/id_rsa.pub` に作成されます。 次のコマンドを実行し、プロンプトが表示されたら、秘密キーを保護するために "パスフレーズ" を作成してください  (パスフレーズとは、秘密キーの暗号化に使用するパスワードです)。

```bash
ssh-keygen -t rsa -b 2048 
```

新しく作成されたキーを `ssh-agent` に追加します。

```bash
ssh-add ~/.ssh/id_rsa
```

> [!IMPORTANT] 
> 上記のコマンドは、ほとんどすべてのディストリビューションの Linux オペレーティング システムで機能しますが、環境が大幅に制限される可能性があるため、必ずしもコンテナーで動作するとは限りません。 

## <a name="detailed-walkthrough"></a>詳細なチュートリアル

Linux サーバーにログインするための最も簡単な方法は、SSH の公開キーと秘密キーを使用する方法です。 [公開キー暗号化](https://en.wikipedia.org/wiki/Public-key_cryptography) を使用した方が安全です。

> [!IMPORTANT]
> 公開キーはだれとでも共有できますが、秘密キーを所有するのは自分 (またはローカル セキュリティ インフラストラクチャ) だけです。  SSH 秘密キーには、それを保護するための、[セキュリティで高度に保護されたパスワード](https://www.xkcd.com/936/) (ソース: [xkcd.com](https://xkcd.com)) が必要です。  このパスワードは、秘密 SSH キーにアクセスするためだけのものであり、ユーザー アカウント パスワード **ではありません** 。  SSH キーにパスワードを追加すると、128 ビット AES を使用して秘密キーが暗号化されるため、暗号化解除するパスワードなしでは秘密キーを使用できなくなります。  攻撃者によって盗まれた秘密キーにパスワードがないと、その秘密キーが使用され、対応する公開キーを持つ任意のサーバーにログインされてしまいます。  秘密キーがパスワードで保護されている場合、攻撃者は秘密キーを使用できないため、Azure 上のインフラストラクチャにセキュリティ レイヤーが追加されたことになります。

この記事では、SSH プロトコル バージョン 2 RSA の公開キー ファイルと秘密キー ファイルを作成します。このファイルは、Resource Manager でのデプロイに推奨されています。  *ssh-rsa* キーは、クラシック デプロイと Resource Manager デプロイの両方の[ポータル](https://portal.azure.com)で必要です。

## <a name="disable-ssh-passwords-by-using-ssh-keys"></a>SSH キーを使用して SSH パスワードを無効にする

Azure では、長さ 2,048 ビット以上の ssh-rsa 形式の公開キーと秘密キーを必須としています。 このキーを作成するには、`ssh-keygen` を使用します。一連の質問に答えることによって、秘密キーと対応する公開キーが出力されます。 Azure VM が作成されると、公開キーが `~/.ssh/authorized_keys` にコピーされます。  `~/.ssh/authorized_keys` の SSH キーは、クライアントが SSH ログイン接続の対応する秘密キーと一致することを確認するために使用されます。  認証に SSH キーを使用する Azure Linux VM が作成されると、Azure は、パスワード ログインを禁止して SSH キーのみを許可するよう SSHD サーバーを構成します。  そのため、SSH キーで Azure Linux VM を作成すると、VM のデプロイをセキュリティで保護し、一般的なデプロイ後の構成手順である sshd_config 構成ファイルでのパスワードの無効化を省略することができます。

## <a name="using-ssh-keygen"></a>ssh-keygen の使用

次に示すのは、2,048 ビット RSA を使用してパスワードで保護された (暗号化された) SSH キー ペアを作成するコマンドです。識別しやすいようにコメントを付けています。  

SSH キーは既定で `~/.ssh` ディレクトリに保持されます。  `~/.ssh` ディレクトリがない場合、`ssh-keygen` コマンドによって、適切なアクセス許可で作成されます。

```bash
ssh-keygen \
-t rsa \
-b 2048 
```

*コマンドの説明*

`ssh-keygen` = キーの作成に使用するプログラム。

`-t rsa` = 作成するキーの種類。RSA 形式 [wikipedia](https://en.wikipedia.org/wiki/RSA_(cryptosystem) で指定します。

`-b 2048` = キーのビット数。


## <a name="classic-portal-and-x509-certs"></a>クラシック ポータルと X.509 証明書

Azure [クラシック ポータル](https://manage.windowsazure.com/)を使用している場合は、SSH キーのために X.509 証明書が必要です。  他の種類の SSH 公開キーは許可されず、X.509 証明書である "*必要があります*"。

既存の SSH-RSA 秘密キーから X.509 証明書を作成するには、次のようにします。

```bash
openssl req -x509 \
-key ~/.ssh/id_rsa \
-nodes \
-days 365 \
-newkey rsa:2048 \
-out ~/.ssh/id_rsa.pem
```

## <a name="classic-deploy-using-asm"></a>`asm` を使用するクラシック デプロイ

クラシック デプロイメント モデル (Azure サービス管理 CLI `asm`) を使用している場合は、**.pem** コンテナーで SSH-RSA 公開キーまたは RFC4716 形式のキーを使用できます。  SSH-RSA 公開キーは、この記事の前半で `ssh-keygen` を使用して作成したものです。

既存の SSH 公開キーから RFC4716 形式のキーを作成するには、次のようにします。

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>ssh-keygen の例

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ahmet/.ssh/id_rsa.
Your public key has been saved in /home/ahmet/.ssh/id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 ahmet@myserver
The keys randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

保存するキー ファイル:

`Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): ~/.ssh/id_rsa`

この記事で使用するキー ペアの名前です。  既定では、キー ペアの名前に **id_rsa** が割り当てられます。秘密キーのファイル名として **id_rsa** を想定しているツールもあるため、そのようにすることをお勧めします。 ディレクトリ `~/.ssh/` は、SSH キー ペアと SSH 構成ファイルの既定の場所です。  完全パスで指定されていない場合、`ssh-keygen` は、既定の `~/.ssh` ではなく、現在の作業ディレクトリにキーを作成します。

`~/.ssh` ディレクトリの一覧です。

```bash
ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 rsa.pub
```

キーのパスワード:

`Enter passphrase (empty for no passphrase):`

`ssh-keygen` では、秘密キーの暗号化に使用するパスワードのことを "パスフレーズ" と言います。  キー ペアにはパスフレーズを追加することを " *強く* " お勧めします。 秘密キーを保護するパスフレーズがないと、キー ファイルを持っている人はだれでも、そのファイルを使用して、対応する公開キーのあるサーバーにログインすることができます。 パスフレーズを追加すれば、第三者に秘密キー ファイルへのアクセスを許してしまった場合でも、認証用のキーを変更する時間ができるので、保護のレベルが上がります。

## <a name="using-ssh-agent-to-store-your-private-key-password"></a>ssh-agent を使用して秘密キーのパスワードを格納する

SSH ログインのたびに秘密キー ファイルのパスフレーズを入力しなくて済むように、`ssh-agent` を使用して秘密キー ファイルのパスフレーズをキャッシュできます。 Mac を使用している場合、 `ssh-agent`を呼び出すと、秘密キーのパスワードは OSX キーチェーンによって安全に保存されます。

パスフレーズを対話的に使用しなくてもよいように、`ssh-agent` と `ssh-add` を確認して使用し、キー ファイルに関する情報を SSH システムに伝えます。

```bash
eval "$(ssh-agent -s)"
```

ここで、`ssh-add` コマンドを使用して、`ssh-agent` に秘密キーを追加します。

```bash
ssh-add ~/.ssh/id_rsa
```

これで、秘密キーのパスワードが `ssh-agent` に格納されました。

## <a name="using-ssh-copy-id-to-install-the-new-key"></a>`ssh-copy-id` を使用した新しいキーのインストール
既に VM を作成済みの場合は、次のコマンドを使用して、Linux VM に新しい SSH 公開キーをインストールできます。VM ユーザー名とサーバー アドレスは実際の値に置き換えてください。

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub ahmet@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>SSH 構成ファイルの作成と構成

ログインを高速化し、SSH クライアントの動作を最適化するために、`~/.ssh/config` ファイルを作成して構成することをお勧めします。

標準的な構成の例を次に示します。

### <a name="create-the-file"></a>ファイルの作成

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>ファイルを編集して新しい SSH 構成を追加する

```bash
vim ~/.ssh/config
```

### <a name="example-sshconfig-file"></a>`~/.ssh/config` ファイルの例:

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User ahmet
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=yes
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath ~/.ssh/SSHConnections/ssh-%r@%h:%p
  ControlPersist 4h
  IdentityFile ~/.ssh/id_rsa
```

この SSH 構成には、サーバーごとのセクションがあり、各サーバーに専用のキー ペアを割り当てることができます。 既定の設定 (`Host *`) は、構成ファイルで特定のどのホストとも一致しないすべてのホスト用です。

### <a name="config-file-explained"></a>構成ファイルについて

`Host` = 端末で呼び出すホストの名前。  `ssh fedora22` は、`Host fedora22` という名称の設定ブロック内の値を使用するよう `SSH` に伝えています。注: このラベルは、サーバーの実際のホスト名を表すものではありません。実際の用途に合っていれば、どのような名称を使用してもかまいません。

`Hostname 102.160.203.241` = アクセスされるサーバーの IP アドレスまたは DNS 名。

`User ahmet` = サーバーにログインするときに使用するリモート ユーザー アカウント。

`PubKeyAuthentication yes` = SSH キーを使ってログインする意思を SSH に伝えています。

`IdentityFile /home/ahmet/.ssh/id_id_rsa` = 認証に使用する SSH 秘密キーおよび対応する公開キー。

## <a name="ssh-into-linux-without-a-password"></a>パスワードを使わずに SSH で Linux にログインする

SSH キー ペアを作成し、SSH 構成ファイルを構成したので、すばやく安全に Linux VM にログインできるようになりました。 SSH キーを使用して初めてサーバーにログインするとき、キー ファイルのパスフレーズを入力するように求められます。

```bash
ssh fedora22
```

### <a name="command-explained"></a>コマンドの説明

`ssh fedora22` が実行されると SSH はまず、`Host fedora22` ブロックの設定を見つけて読み込んだ後、最後のブロック `Host *` から残りの設定をすべて読み込みます。

## <a name="next-steps"></a>次のステップ

次のステップでは、新しい SSH 公開キーを使用して Azure Linux VM を作成します。  Azure VM は、SSH 公開キーをログインとして作成した方が、既定のログイン方法であるパスワードを使って作成するよりも高いセキュリティが得られます。  SSH キーを使用して作成された Azure VM は、既定ではパスワードが無効にされます。そのため、推測によるブルート フォース攻撃を回避できます。 SSH キー ペアの作成に関する詳しい説明や、クラシック ポータルで使用する証明書については、[SSH キー ペアと証明書を作成するための詳細な手順](virtual-machines-linux-create-ssh-keys-detailed.md)に関するページをご覧ください。

* [Azure テンプレートを使用して安全な Linux VM を作成する](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure ポータルを使用して安全な Linux VM を作成する](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure CLI を使用して安全な Linux VM を作成する](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

