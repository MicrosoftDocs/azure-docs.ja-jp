---
title: クイック スタート - Azure CLI を使用して Windows VM を作成する
description: このクイックスタートでは、Azure CLI を使用して Windows 仮想マシンを作成する方法について説明します
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 07/02/2019
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 769b42133fb894c916ecaa3c42dd7de85206c765
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556249"
---
# <a name="quickstart-create-a-windows-virtual-machine-with-the-azure-cli"></a>クイック スタート:Azure CLI で Windows 仮想マシンを作成する

Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このクイック スタートでは、Azure CLI を使用して、Windows Server 2016 を実行する仮想マシン (VM) を Azure に展開する方法を示します。 次に、VM の動作を確認するために、VM に RDP 接続し、IIS Web サーバーをインストールします。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/bash](https://shell.azure.com/bash) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けます。その後、**Enter** キーを押してそれを実行します。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[az group create](/cli/azure/group) コマンドを使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>仮想マシンの作成

[az vm create](/cli/azure/vm) を使用して VM を作成します。 次の例では、*myVM* という名前の VM を作成します。 この例では、管理ユーザーの名前に *azureuser* を使用します。 

[Azure VM のパスワード要件](./faq.md#what-are-the-password-requirements-when-creating-a-vm
)を満たしているパスワードを指定する必要があります。 以下の例を実行すると、コマンド ラインでパスワードを入力するように求められます。 `--admin-password` パラメーターを追加して、パスワードの値を指定することもできます。 後で VM に接続するときに、このユーザー名とパスワードが使用されます。

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser 
```

VM とサポートするリソースを作成するには数分かかります。 次の出力例では、成功した VM 作成操作を示します。

```output
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

作成した VM からの出力に含まれる実際の `publicIpAddress` を記録しておいてください。 このアドレスは、次の手順で VM にアクセスするために使います。

## <a name="open-port-80-for-web-traffic"></a>Web トラフィック用にポート 80 を開く

既定では、Azure において Windows VM を作成すると RDP 接続のみが開かれます。 IIS Web サーバーで使うために TCP ポート 80 を開くには、[az vm open-port](/cli/azure/vm) を使います。

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>仮想マシンへの接続

次のコマンドを使って、お使いのローカル コンピューターからリモート デスクトップ セッションを作成します。 IP アドレスは、実際の VM のパブリック IP アドレスに置き換えてください。 メッセージが表示されたら、VM の作成時に使った資格情報を入力します。

```powershell
mstsc /v:publicIpAddress
```

## <a name="install-web-server"></a>Web サーバーのインストール

VM の動作を確認するために、IIS Web サーバーをインストールします。 VM で PowerShell プロンプトを開き、次のコマンドを実行します。

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

終了したら、VM への RDP 接続を閉じます。

## <a name="view-the-web-server-in-action"></a>動作中の Web サーバーを表示する

IIS をインストールし、VM のポート 80 をインターネットから開いたら、任意の Web ブラウザーを使用して IIS の既定のウェルカム ページを表示することができます。 前の手順で取得した VM のパブリック IP アドレスを使用します。 次の例は、既定の IIS Web サイトを示しています。

![IIS の既定のサイト](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[az group delete](/cli/azure/group) コマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、単純な仮想マシンをデプロイし、Web トラフィック用のネットワーク ポートを開き、基本的な Web サーバーをインストールしました。 Azure 仮想マシンの詳細については、Windows VM のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Azure Windows 仮想マシンのチュートリアル](./tutorial-manage-vm.md)
