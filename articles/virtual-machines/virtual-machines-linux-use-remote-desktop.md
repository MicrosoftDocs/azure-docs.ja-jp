---
title: "Azure の Linux VM にリモート デスクトップを使用する | Microsoft Docs"
description: "リモート デスクトップ (xrdp) をインストールして、Azure の Linux VM に接続するように構成する方法を説明します。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 1b8ad526ee59bd1547cbd7f4a208720fb6557fc2
ms.lasthandoff: 03/27/2017


---
# <a name="install-and-configure-remote-desktop-to-connect-to-a-linux-vm-in-azure"></a>リモート デスクトップをインストールして Azure の Linux VM に接続するように構成する
Azure の Linux 仮想マシン (VM) は、通常、セキュリティで保護された shell (SSH) 接続を使用してコマンド ラインから管理します。 Linux にまだ慣れていない場合や、簡単にトラブルシューティングする場合などは、リモート デスクトップを使用する方が操作が簡単なことがあります。 この記事では、Resource Manager デプロイメント モデルを使用して、Linux VM のデスクトップ環境 ([xfce](https://www.xfce.org)) とリモート デスクトップ ([xrdp](http://www.xrdp)) をインストールして構成する方法を詳しく説明します。 また、[クラシック デプロイメント モデルを使用して VM でこれらの手順を実行](linux/classic/remote-desktop.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)することもできます。


## <a name="prerequisites"></a>前提条件
この記事は、Azure に Linux VM があることを前提としています。 VM を作成する必要がある場合は、次のいずれかの方法を実行してください。

- [Azure CLI 2.0](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) または [Azure CLI 1.0](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure Portal](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

また、[アクティブな Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)にログインしている必要があります。


## <a name="quick-commands"></a>クイック コマンド
タスクをすばやく実行する必要がある場合のために、次のセクションでは、VM でリモート デスクトップをインストール、構成するための基本的なコマンドの詳細について説明します。 詳細な情報と各手順のコンテキストが、ドキュメントの残りの部分に記載されています。[ここからお読みください](#install-graphical-environment-on-linux-vm)。

次の例では、軽量 [xfce4](https://www.xfce.org/) デスクトップ環境を Ubuntu VM にインストールします。 その他のディストリビューションとデスクトップ環境ではコマンドが若干異なります。 たとえば、Red Hat Enterprise Linux をインストールし、適切な **selinux** 規則をする場合は **yum** を使用し、SUSE にインストールするには **zypper** を使用します。

VM に SSH 接続します。 xfce デスクトップ環境を次のようにインストールします。

```bash
sudo apt-get update
sudo apt-get install xfce4
```

xrdp を次のようにインストールします。

```bash
sudo apt-get install xrdp
```

xfce をデスクトップ環境として使用するように xrdp を構成します。

```bash
echo xfce4-session >~/.xsession
```

xrdp サービスを再起動します。

```bash
sudo service xrdp restart
```

現在、認証に SSH キーのみを使用している場合は、ユーザー アカウントのパスワードを設定します。

```bash
sudo passwd ops
```

Linux VM の SSH セッションを終了します。 ネットワーク セキュリティ グループ ルールを作成して、リモート デスクトップのトラフィックを許可するには、ローカル コンピューターで Azure CLI を使用します。 Azure CLI 2.0 で [az network nsg rule create](/cli/azure/network/nsg/rule#create) を使用します。 次の例では、`myNetworkSecurityGroup` に `myNetworkSecurityGroupRule` という名前の規則を作成して、TCP ポート 3389 でのトラフィックを許可します。
    
```azurecli
az network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1010 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 3389 \
    --access allow
```

または、Azure CLI 1.0 を使用します。

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1010 \
    --destination-port-range 3389 --access allow
```

特定のリモート デスクトップ クライアントを使用して Linux VM に接続します。

![リモート デスクトップ クライアントを使用して xrdp に接続する](./media/virtual-machines-linux-use-remote-desktop/remote-desktop-client.png)

## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Linux VM にデスクトップ環境をインストールする
Azure のほとんどの Linux VM では、デスクトップ環境は既定でインストールされていません。 通常、Linux VM は、デスクトップ環境ではなく、SSH 接続を使用して管理されます。 Linux で利用できるデスクトップ環境にはさまざまな種類があります。 選択したデスクトップ環境によっては、ディスク容量を 1 ～ 2 GB 使用し、必要なパッケージのすべてのインストールと構成が完了するまでに 5 ～ 10 分かかるものもあります。

次の例では、軽量 [xfce4](https://www.xfce.org/) デスクトップ環境を Ubuntu VM にインストールします。 他のディストリビューションではコマンドが若干異なります (たとえば、Red Hat Enterprise Linux をインストールし、適切な **selinux** 規則をする場合は **yum** を使用し、SUSE にインストールするには **zypper** を使用します)。

最初に、VM に SSH 接続します。 次の例では、`myvm.westus.cloudapp.azure.com` という名前の VM に `ops` のユーザー名を使用して接続しています。

```bash
ssh ops@myvm.westus.cloudapp.azure.com ~/.ssh/id_rsa.pub
```

Windows を使用し、SSH の使用に関する詳細が必要な場合は、[Windows での SSH キーの使用方法](virtual-machines-linux-ssh-from-windows.md)に関するページをご覧ください。

次に、`apt` を使用して xfce を以下のようにインストールします。

```bash
sudo apt-get update
sudo apt-get install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>リモート デスクトップ サーバーをインストールして構成する
デスクトップ環境がインストールできましたので、リモート デスクトップ サービスを構成して着信接続をリッスンします。 [xrdp](http://xrdp.org) はオープン ソースのリモート デスクトップ プロトコル (RDP) サーバーであり、ほとんどの Linux ディストリビューションで使用でき、xfce も問題なく使用できます。 Ubuntu VM に次のように xrdp をインストールします。

```bash
sudo apt-get install xrdp
```

セッションの開始時に使用するデスクトップ環境を xrdp に指定します。 xfce をデスクトップ環境として使用するように xrdp を構成します。

```bash
echo xfce4-session >~/.xsession
```

次のように xrdp サービスを再起動して、変更内容を有効にします。

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>ローカル ユーザー アカウントのパスワードを設定する
VM の作成時にユーザー アカウントのパスワードを作成した場合は、この手順をスキップします。 SSH キー認証のみを使用し、ローカル アカウントのパスワードを設定していない場合は、xrdp を使用して VM にログインする前にパスワードを指定します。 xrdp では認証に SSH キーを使用できません。 次の例は、ユーザー アカウント `ops` にパスワードを指定しています。

```bash
sudo passwd ops
```

> [!NOTE]
> 現在、sshd 構成でパスワードによるログインを許可していない場合は、パスワードを指定してしても、sshd 構成は更新されません。 セキュリティの観点から、キー ベースの認証を使用して SSH トンネルで VM に接続し、xrdp に接続する必要がある場合があります。 そのような場合は、リモート デスクトップのトラフィックを許可するネットワーク セキュリティ グループ ルールの作成に関する次の手順をスキップします。


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>リモート デスクトップ トラフィックにネットワーク セキュリティ グループ ルールを作成する
リモート デスクトップのトラフィックを Linux VM に接続できるようにするには、ポート 3389 で VM への TCP の接続を許可するネットワーク セキュリティ グループ ルールを作成する必要があります。 ネットワーク セキュリティ グループ ルールの詳細については、「[ネットワーク セキュリティ グループ (NSG) について](../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 また、[Azure Portal を使用してネットワーク セキュリティ グループ ルールを作成することもできます](virtual-machines-windows-nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

次の例では、`myNetworkSecurityGroupRule` という名前のネットワーク セキュリティ グループ ルールを作成し、`3389` ポートの `tcp` でのトラフィックを `allow` しています。

- Azure CLI 2.0 で [az network nsg rule create](/cli/azure/network/nsg/rule#create) を使用します。
    
    ```azurecli
    az network nsg rule create --resource-group myResourceGroup \
        --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
        --protocol tcp --direction inbound --priority 1010 \
        --source-address-prefix '*' --source-port-range '*' \
        --destination-address-prefix '*' --destination-port-range 3389 \
        --access allow
    ```

- または、Azure CLI 1.0 を使用します。

    ```azurecli
    azure network nsg rule create --resource-group myResourceGroup \
        --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
        --protocol tcp --direction inbound --priority 1010 \
        --destination-port-range 3389 --access allow
    ```

## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Linux VM をリモート デスクトップ クライアントに接続する
ローカルのリモート デスクトップ クライアントを開き、Linux VM の DNS 名または IP アドレスに接続します。 VM でユーザー アカウントのユーザー名とパスワードを次のように入力します。

![リモート デスクトップ クライアントを使用して xrdp に接続する](./media/virtual-machines-linux-use-remote-desktop/remote-desktop-client.png)

認証後、xfce デスクトップ環境が読み込まれ、次の例のように表示されます。

![xrdp を介した xfce デスクトップ環境](./media/virtual-machines-linux-use-remote-desktop/xfce-desktop-environment.png)


## <a name="troubleshoot"></a>トラブルシューティング
リモート デスクトップ クライアントを使用して Linux VM に接続できない場合は、次のように Linux VM で `netstat` を使用して、VM が次のように RDP 接続をリッスンしていることを確認します。

```bash
sudo netstat -plnt | grep rdp
```

次の例では、期待通りに TCP ポート 3389 で VM がリッスンしています。

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

xrdp サービスがリッスンしていない場合は、次のように Ubuntu VM でサービスを再開させます。

```bash
sudo service xrdp restart
```

サービスが応答していない理由を確認するには、Ubuntu VM の `/var/log` のログを確認します。 また、リモート デスクトップ接続の試行中に syslog を監視すれば、エラーを確認できます。

```bash
tail -f /var/log/syslog
```

Red Hat Enterprise Linux SUSE など他の Linux ディストリビューションでは、サービスの再開方法や確認するログ ファイルの場所が異なる場合があります。

リモート デスクトップ クライアントから応答がなく、システム ログにもイベントが表示されない場合は、リモート デスクトップ トラフィックが VM に到達できないことを示しています。 ネットワーク セキュリティ グループ ルールを確認し、ポート 3389 で TCP を許可するルールが設定されていることをご確認ください。 詳細については、[アプリケーションの接続の問題のトラブルシューティング](virtual-machines-linux-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページをご覧ください。


## <a name="next-steps"></a>次のステップ
Linux VM で SSH キーを作成、使用する方法の詳細については、[Azure での Linux VM の SSH キーの作成](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページをご覧ください。

Windows から SSH を使用する方法の詳細については、[Windows での SSH キーの使用方法](virtual-machines-linux-ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページをご覧ください。


