---
title: 'クイックスタート: Azure Private Link でプライベート リンク サービスを作成する'
description: このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用してプライベート リンク サービスを作成します。
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/29/2020
ms.author: allensu
ms.openlocfilehash: 34993ad3d3d0494f89bd264a8b7194f52129ad7c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555059"
---
# <a name="quickstart-create-a-private-link-service-by-using-an-arm-template"></a>クイック スタート:ARM テンプレートを使用してプライベート リンク サービスを作成する

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用してプライベート リンク サービスを作成します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

このクイックスタートは、[Azure portal](create-private-link-service-portal.md)、[Azure PowerShell](create-private-link-service-powershell.md)、または [Azure CLI](create-private-link-service-cli.md) を使用して完了することもできます。

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

アクティブなサブスクリプションを含む Azure アカウントが必要です。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>テンプレートを確認する

このテンプレートは、プライベート リンク サービスを作成します。

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-privatelink-service/)からのものです。

:::code language="json" source="~/quickstart-templates/101-privatelink-service/azuredeploy.json":::

このテンプレートには、次の複数の Azure リソースが定義されています。

- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): 仮想マシンごとに 1 つの仮想ネットワークが存在します。
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers): サービスをホストする仮想マシンを公開するロード バランサー。
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): 2 つのネットワーク インターフェイス (仮想マシンごとに 1 つ) が存在します。
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): 2 台の仮想マシンが存在します (1 台はサービスをホストし、もう 1 台はプライベート エンドポイントへの接続をテストします)。
- [**Microsoft.Compute/virtualMachines/extensions**](/azure/templates/Microsoft.Compute/virtualMachines/extensions): Web サーバーをインストールする拡張機能。
- [**Microsoft.Network/privateLinkServices**](/azure/templates/microsoft.network/privateLinkServices): サービスを公開するプライベート リンク サービス。
- [**Microsoft.Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses): 2 つのパブリック IP アドレスが存在します (仮想マシンごとに 1 つ)。
- [**Microsoft.Network/privateendpoints**](/azure/templates/microsoft.network/privateendpoints): サービスにアクセスするためのプライベート エンドポイント。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

ARM テンプレートを Azure にデプロイする方法を次に示します。

1. Azure にサインインしてテンプレートを開くために、 **[Azure に配置する]** を選択します。 テンプレートは、仮想マシン、標準ロード バランサー、プライベート リンク サービス、プライベート エンドポイント、ネットワーク、および検証する仮想マシンを作成します。

   [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

2. リソース グループを選択または作成します。
3. 仮想マシン管理者のユーザー名とパスワードを入力します。
4. 使用条件を読みます。 同意する場合は **[上記の使用条件に同意する]**  >  **[購入]** を選択します。

## <a name="validate-the-deployment"></a>デプロイの検証

> [!NOTE]
> ARM テンプレートにより、仮想マシン myConsumerVm<b>{uniqueid}</b> リソースの一意の名前が生成されます。 **{uniqueid}** は、実際に生成された値に置き換えてください。

### <a name="connect-to-a-vm-from-the-internet"></a>インターネットから VM に接続する

次のように、インターネットから VM _myConsumerVm{uniqueid}_ に接続します。

1.  ポータルの検索バーに、「_myConsumerVm{uniqueid}_ 」と入力します。

2.  **[接続]** を選択します。 **[仮想マシンに接続する]** が開きます。

3.  **[RDP ファイルのダウンロード]** を選択します。 リモート デスクトップ プロトコル ( _.rdp_) ファイルが作成され、お使いのコンピューターにダウンロードされます。

4.  ダウンロードされた rdp ファイルを開きます。

    a. メッセージが表示されたら、 **[Connect]** を選択します。

    b. VM の作成時に指定したユーザー名とパスワードを入力します。
    
    > [!NOTE]
    > 場合によっては、 **[その他]**  >  **[別のアカウントを使用する]** の順に選択して、VM の作成時に入力した資格情報を指定する必要があります。

5.  **[OK]** を選択します。

6.  サインイン処理中に証明書の警告が表示される場合があります。 証明書の警告を受信する場合は、 **[はい]** または **[続行]** を選択します。

7.  VM デスクトップが表示されたら最小化してローカル デスクトップに戻ります。

### <a name="access-the-http-service-privately-from-the-vm"></a>VM から HTTP サービスにプライベートにアクセスする

プライベート エンドポイントを使用して VM から HTTP サービスに接続する方法を次に示します。

1.  _myConsumerVm{uniqueid}_ のリモート デスクトップに移動します。
2.  ブラウザーを開いて、プライベート エンドポイント アドレス `http://10.0.0.5/` を入力します。
3.  既定の IIS ページが表示されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

プライベート リンク サービスと共に作成したリソースが不要になった場合は、リソース グループを削除します。 これにより、プライベート リンク サービスとすべての関連リソースが削除されます。

リソース グループを削除するには、`Remove-AzResourceGroup` コマンドレットを呼び出します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>次のステップ


プライベート エンドポイントをサポートするサービスの詳細については、以下を参照してください。
> [!div class="nextstepaction"]
> [Private Link の可用性](private-link-overview.md#availability)
