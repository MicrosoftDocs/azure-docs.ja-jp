---
title: クイックスタート - ARM テンプレートを使用してプライベート エンドポイントを作成する
description: このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用してプライベート エンドポイントを作成します。
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: e80adc16e08e676ac2daabec01a11c10d537c547
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562947"
---
# <a name="quickstart-create-a-private-endpoint-by-using-an-arm-template"></a>クイック スタート:ARM テンプレートを使用してプライベート エンドポイントを作成する

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用してプライベート エンドポイントを作成します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

このクイックスタートは、[Azure portal](create-private-endpoint-portal.md)、[Azure PowerShell](create-private-endpoint-powershell.md)、または [Azure CLI](create-private-endpoint-cli.md) を使用して完了することもできます。

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

アクティブなサブスクリプションを含む Azure アカウントが必要です。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>テンプレートを確認する

このテンプレートでは、Azure SQL Database のインスタンスに使用するプライベート エンドポイントを作成します。

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-private-endpoint-sql/)からのものです。

:::code language="json" source="~/quickstart-templates/101-private-endpoint-sql/azuredeploy.json":::

このテンプレートには、次の複数の Azure リソースが定義されています。

- [**Microsoft.Sql/servers**](/azure/templates/microsoft.sql/servers): サンプル データベースを含む SQL Database のインスタンス。
- [**Microsoft.Sql/servers/databases**](/azure/templates/microsoft.sql/servers/databases): サンプル データベース。
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): プライベート エンドポイントがデプロイされる仮想ネットワーク。
- [**Microsoft.Network/privateEndpoints**](/azure/templates/microsoft.network/privateendpoints): SQL Database のインスタンスにアクセスするためのプライベート エンドポイント。
- [**Microsoft.Network/privateDnsZones**](/azure/templates/microsoft.network/privatednszones): プライベート エンドポイントの IP アドレス解決に使用されるゾーン。
- [**Microsoft.Network/privateDnsZones/virtualNetworkLinks**](/azure/templates/microsoft.network/privatednszones/virtualnetworklinks)
- [**Microsoft.Network/privateEndpoints/privateDnsZoneGroups**](/azure/templates/microsoft.network/privateendpoints/privateDnsZoneGroups): プライベート エンドポイントをプライベート DNS ゾーンに関連付けるためのゾーン グループ。
- [**Microsoft.Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses): 仮想マシンにアクセスするためのパブリック IP アドレス。
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): 仮想マシンのネットワーク インターフェイス。
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): SQL Database インスタンスへのプライベート エンドポイントとのプライベート接続をテストするための仮想マシン。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

ARM テンプレートを Azure にデプロイする方法を次に示します。

1. Azure にサインインしてテンプレートを開くために、 **[Azure に配置する]** を選択します。 このテンプレートによって、プライベート エンドポイント、SQL Database のインスタンス、ネットワーク インフラストラクチャ、および検証する仮想マシンが作成されます。

   [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. リソース グループを選択または作成します。
3. SQL 管理者のサインインとパスワードを入力します。
4. 仮想マシン管理者のユーザー名とパスワードを入力します。
5. 使用条件を読みます。 同意する場合は **[上記の使用条件に同意する]**  >  **[購入]** を選択します。 このデプロイの完了には、20 分以上かかる場合があります。

## <a name="validate-the-deployment"></a>デプロイの検証

> [!NOTE]
> ARM テンプレートにより、仮想マシン myVm<b>{uniqueid}</b> リソースと SQL Database sqlserver<b>{uniqueid}</b> リソースの一意の名前が生成されます。 **{uniqueid}** は、実際に生成された値に置き換えてください。

### <a name="connect-to-a-vm-from-the-internet"></a>インターネットから VM に接続する

次のように、インターネットから VM _myVm{uniqueid}_ に接続します。

1. ポータルの検索バーに、「_myVm{uniqueid}_ 」と入力します。

2. **[接続]** を選択します。 **[仮想マシンに接続する]** が開きます。

3. **[RDP ファイルのダウンロード]** を選択します。 リモート デスクトップ プロトコル ( _.rdp_) ファイルが作成され、お使いのコンピューターにダウンロードされます。

4. ダウンロードされた rdp ファイルを開きます。

   a. メッセージが表示されたら、 **[Connect]** を選択します。

   b. VM の作成時に指定したユーザー名とパスワードを入力します。

      > [!NOTE]
      > 場合によっては、 **[その他]**  >  **[別のアカウントを使用する]** の順に選択して、VM の作成時に入力した資格情報を指定する必要があります。

5. **[OK]** を選択します。

6. サインイン処理中に証明書の警告が表示される場合があります。 証明書の警告を受信する場合は、 **[はい]** または **[続行]** を選択します。

7. VM デスクトップが表示されたら最小化してローカル デスクトップに戻ります。

### <a name="access-the-sql-database-server-privately-from-the-vm"></a>VM から SQL Database サーバーにプライベートにアクセスする

プライベート エンドポイントを使用して VM から SQL Database サーバーに接続する方法を次に示します。

1.  _myVM{uniqueid}_ のリモート デスクトップで、PowerShell を開きます。
2.  「nslookup sqlserver{uniqueid}.database.windows.net」のように入力します。 
    次のようなメッセージが返されます。

    ```
      Server:  UnKnown
      Address:  168.63.129.16
      Non-authoritative answer:
      Name:    sqlserver.privatelink.database.windows.net
      Address:  10.0.0.5
      Aliases:  sqlserver.database.windows.net
    ```

3.  SQL Server Management Studio をインストールします。
4.   **[サーバーに接続]** で、次の情報を入力または選択します。
    - **[サーバーの種類]** : **データベース エンジン** を選択します。
    - **[サーバー名]** : **[sqlserver{uniqueid}.database.windows.net]** を選択します。
    - **[ユーザー名]** : 作成時に指定したユーザー名を入力します。
    - **パスワード**:作成時に指定したパスワードを入力します。
    - **[パスワードを記憶する]** :  **[はい]** を選択します。

5.  **[接続]** を選択します。
6.  左側のメニューから **[データベース]** に移動します。
7.  (省略可能) _sample-db_ の情報を作成または照会することができます。
8.  _myVm{uniqueid}_ へのリモート デスクトップ接続を閉じます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

プライベート エンドポイントと共に作成したリソースが不要になった場合は、リソース グループを削除します。 これにより、プライベート エンドポイントとすべての関連リソースが削除されます。

リソース グループを削除するには、`Remove-AzResourceGroup` コマンドレットを呼び出します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>次のステップ

プライベート エンドポイントをサポートするサービスの詳細については、以下を参照してください。
> [!div class="nextstepaction"]
> [Private Link の可用性](private-link-overview.md#availability)
