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
ms.date: 06/22/2017
ms.author: iainfou
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: d8d6130a270285c84c1dd057a3512cdeb39287f6
ms.contentlocale: ja-jp
ms.lasthandoff: 08/09/2017

---
# <a name="install-and-configure-remote-desktop-to-connect-to-a-linux-vm-in-azure"></a>リモート デスクトップをインストールして Azure の Linux VM に接続するように構成する
Azure の Linux 仮想マシン (VM) は、通常、セキュリティで保護された shell (SSH) 接続を使用してコマンド ラインから管理します。 Linux にまだ慣れていない場合や、簡単にトラブルシューティングする場合などは、リモート デスクトップを使用する方が操作が簡単なことがあります。 この記事では、Resource Manager デプロイメント モデルを使用して、Linux VM のデスクトップ環境 ([xfce](https://www.xfce.org)) とリモート デスクトップ ([xrdp](http://www.xrdp.org)) をインストールして構成する方法を詳しく説明します。


## <a name="prerequisites"></a>前提条件
この記事は、Azure に Linux VM があることを前提としています。 VM を作成する必要がある場合は、次のいずれかの方法を実行してください。

- [Azure CLI 2.0](quick-create-cli.md)
- [Azure Portal](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Linux VM にデスクトップ環境をインストールする
Azure のほとんどの Linux VM では、デスクトップ環境は既定でインストールされていません。 通常、Linux VM は、デスクトップ環境ではなく、SSH 接続を使用して管理されます。 Linux で利用できるデスクトップ環境にはさまざまな種類があります。 選択したデスクトップ環境によっては、ディスク容量を 1 ～ 2 GB 使用し、必要なパッケージのすべてのインストールと構成が完了するまでに 5 ～ 10 分かかるものもあります。

次の例では、軽量 [xfce4](https://www.xfce.org/) デスクトップ環境を Ubuntu VM にインストールします。 他のディストリビューションではコマンドが若干異なります (たとえば、Red Hat Enterprise Linux をインストールし、適切な `selinux` 規則をする場合は `yum` を使用し、SUSE にインストールするには `zypper` を使用します)。

最初に、VM に SSH 接続します。 次の例では、*myvm.westus.cloudapp.azure.com* という名前の VM に *azureuser* のユーザー名を使用して接続しています。

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Windows を使用し、SSH の使用に関する詳細が必要な場合は、[Windows での SSH キーの使用方法](ssh-from-windows.md)に関するページをご覧ください。

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
VM の作成時にユーザー アカウントのパスワードを作成した場合は、この手順をスキップします。 SSH キー認証のみを使用し、ローカル アカウントのパスワードを設定していない場合は、xrdp を使用して VM にログインする前にパスワードを指定します。 xrdp では認証に SSH キーを使用できません。 次の例は、ユーザー アカウント *azureuser* にパスワードを指定しています。

```bash
sudo passwd azureuser
```

> [!NOTE]
> 現在、SSHD 構成でパスワードによるログインを許可していない場合は、パスワードを指定しても、SSHD 構成は更新されません。 セキュリティの観点から、キー ベースの認証を使用して SSH トンネルで VM に接続し、xrdp に接続する必要がある場合があります。 そのような場合は、リモート デスクトップのトラフィックを許可するネットワーク セキュリティ グループ ルールの作成に関する次の手順をスキップします。


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>リモート デスクトップ トラフィックにネットワーク セキュリティ グループ ルールを作成する
リモート デスクトップのトラフィックを Linux VM に接続できるようにするには、ポート 3389 で VM への TCP の接続を許可するネットワーク セキュリティ グループ ルールを作成する必要があります。 ネットワーク セキュリティ グループ ルールの詳細については、「[ネットワーク セキュリティ グループ (NSG) について](../../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 また、[Azure Portal を使用してネットワーク セキュリティ グループ ルールを作成することもできます](../windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

次の例では、*tcp* ポート *3389* に対するトラフィックを "*許可*" する *myNetworkSecurityGroupRule* という名前のネットワーク セキュリティ グループ ルールを [az network nsg rule create](/cli/azure/network/nsg/rule#create) で作成します。

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1010 \
    --destination-port-range 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Linux VM をリモート デスクトップ クライアントに接続する
ローカルのリモート デスクトップ クライアントを開き、Linux VM の DNS 名または IP アドレスに接続します。 VM でユーザー アカウントのユーザー名とパスワードを次のように入力します。

![リモート デスクトップ クライアントを使用して xrdp に接続する](./media/use-remote-desktop/remote-desktop-client.png)

認証後、xfce デスクトップ環境が読み込まれ、次の例のように表示されます。

![xrdp を介した xfce デスクトップ環境](./media/use-remote-desktop/xfce-desktop-environment.png)


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

サービスが応答していない理由を確認するには、Ubuntu VM の */var/log*Thug  のログを確認します。 また、リモート デスクトップ接続の試行中に syslog を監視すれば、エラーを確認できます。

```bash
tail -f /var/log/syslog
```

Red Hat Enterprise Linux SUSE など他の Linux ディストリビューションでは、サービスの再開方法や確認するログ ファイルの場所が異なる場合があります。

リモート デスクトップ クライアントから応答がなく、システム ログにもイベントが表示されない場合は、リモート デスクトップ トラフィックが VM に到達できないことを示しています。 ネットワーク セキュリティ グループ ルールを確認し、ポート 3389 で TCP を許可するルールが設定されていることをご確認ください。 詳細については、[アプリケーションの接続の問題のトラブルシューティング](../windows/troubleshoot-app-connection.md)に関するページをご覧ください。


## <a name="next-steps"></a>次のステップ
Linux VM で SSH キーを作成、使用する方法の詳細については、[Azure での Linux VM の SSH キーの作成](mac-create-ssh-keys.md)に関するページをご覧ください。

Windows から SSH を使用する方法の詳細については、[Windows での SSH キーの使用方法](ssh-from-windows.md)に関するページをご覧ください。


