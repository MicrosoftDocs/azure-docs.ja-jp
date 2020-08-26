---
title: Azure App Configuration を使用した VM の自動デプロイのクイックスタート
description: このクイックスタートでは、Azure PowerShell モジュールと Azure Resource Manager テンプレートを使用して Azure App Configuration ストアをデプロイする方法を示します。 次に、ストア内の値を使用して VM をデプロイします。
author: lisaguthrie
ms.author: lcozzens
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom:
- mvc
- subject-armqs
ms.openlocfilehash: 9b609d4571d6240f428a0210aa5108ff19dc753b
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235181"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration-and-resource-manager-template-arm-template"></a>クイック スタート:App Configuration と Resource Manager テンプレートを使用した VM の自動デプロイ (ARM テンプレート)

Azure Resource Manager テンプレートと Azure PowerShell を使用して、Azure App Configuration ストアをデプロイする方法やストアにキーと値を追加する方法のほか、ストア内のキーと値を使用して Azure リソース (この例では Azure 仮想マシン) をデプロイする方法について説明します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="review-the-templates"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/)からのものです。 [1 つ目のテンプレート](https://azure.microsoft.comresources/templates/101-app-configuration-store/)は、App Configuration ストアを作成するものです。

:::code language="json" source="~/quickstart-templates/101-app-configuration-store/azuredeploy.json" range="1-37" highlight="27-35":::

テンプレートには、1 つの Azure リソースが定義されています。

- [Microsoft.AppConfiguration/configurationStores](/azure/templates/microsoft.appconfiguration/2019-10-01/configurationstores): App Configuration ストアを作成します。

[2 つ目のテンプレート](https://azure.microsoft.com/resources/templates/101-app-configuration/)は、ストア内のキーと値を使用して仮想マシンを作成するものです。 この手順の前に、ポータルまたは Azure CLI を使用してキーと値を追加する必要があります。

:::code language="json" source="~/quickstart-templates/101-app-configuration/azuredeploy.json" range="1-217" highlight="77, 181,189":::

## <a name="deploy-the-templates"></a>テンプレートの配備

### <a name="create-an-app-configuration-store"></a>App Configuration ストアを作成する

1. Azure にサインインし、テンプレートを開くには次のイメージを選択します。 このテンプレートによって App Configuration ストアが作成されます。

    [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store%2Fazuredeploy.json)

1. 次の値を選択または入力します。

    - **[サブスクリプション]** : App Configuration ストアの作成に使用された Azure サブスクリプションを選択します。
    - **[リソース グループ]** : 既存のリソース グループを使用する場合を除き、 **[新規作成]** を選択して新しいリソース グループを作成します。
    - **[リージョン]** : リソース グループの場所を選択します。  たとえば、**East US** などとします。
    - **[Config Store Name]\(構成ストアの名前\)** : 新しい App Configuration ストアの名前を入力します。
    - **[場所]** : App Configuration ストアの場所を指定します。  既定値を使用します。
    - **[SKU 名]** : App Configuration ストアの SKU 名を指定します。 既定値を使用します。

1. **[Review + create]\(レビュー + 作成\)** を選択します。
1. "**検証に成功しました**" と表示されていることを確認して、 **[作成]** を選択します。

リソース グループの名前と App Configuration ストアの名前を書き留めてください。  仮想マシンをデプロイするときに、これらの値が必要となります。
### <a name="add-vm-configuration-key-values"></a>VM 構成のキー値を追加する

App Configuration ストアの作成後、Azure portal または Azure CLI を使用して、キーと値をストアに追加します。

1. [Azure portal](https://portal.azure.com) にサインインし、新しく作成された App Configuration ストアに移動します。
1. 左側のメニューから **[構成エクスプローラー]** を選択します。
1. **[作成]** を選択して、次のキーと値のペアを追加します。

   |キー|値|Label|
   |-|-|-|
   |windowsOsVersion|2019-Datacenter|template|
   |diskSizeGB|1023|template|

   **[コンテンツ タイプ]** は空のままにしてください。

Azure CLI を使用するには、「[Azure App Configuration ストアに格納されているキー/値を操作する](./scripts/cli-work-with-keys.md)」を参照してください。

### <a name="deploy-vm-using-stored-key-values"></a>格納されているキー値を使用して VM をデプロイする

キー値がストアに追加されたので、Azure Resource Manager テンプレートを使用して VM をデプロイする準備ができました。 このテンプレートでは、作成した **windowsOsVersion** キーと **diskSizeGB** キーを参照します。

> [!WARNING]
> ARM テンプレートは、Private Link が有効になっている App Configuration ストア内のキーを参照できません。

1. Azure にサインインし、テンプレートを開くには次のイメージを選択します。 このテンプレートにより、App Configuration ストアに格納されているキーと値を使用して仮想マシンが作成されます。

    [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration%2Fazuredeploy.json)

1. 次の値を選択または入力します。

    - **[サブスクリプション]** : 仮想マシンの作成に使用される Azure サブスクリプションを選択します。
    - **[リソース グループ]** : App Configuration ストアと同じリソース グループを指定するか、または **[新規作成]** を選択して新しいリソース グループを作成します。
    - **[リージョン]** : リソース グループの場所を選択します。  たとえば、**East US** などとします。
    - **[場所]** : 仮想マシンの場所を指定します。 既定値を使用してください。
    - **[管理者ユーザー名]** : 仮想マシンの管理者ユーザー名を指定します。
    - **[管理者パスワード]** : 仮想マシンの管理者パスワードを指定します。
    - **[ドメイン名ラベル]** : 一意のドメイン名を指定します。
    - **[ストレージ アカウント名]** : 仮想マシンに関連付けられるストレージ アカウントの一意の名前を指定します。
    - **[App Config Store Resource Group]\(App Config ストアのリソース グループ\)** : App Configuration ストアを含むリソース グループを指定します。
    - **[App Config Store Name]\(App Config ストア名\)** : Azure App Configuration ストアの名前を指定します。
    - **[VM Sku Key]\(VM SKU キー\)** : **windowsOsVersion** を指定します。  これは、ストアに追加したキー値の名前です。
    - **[Disk Size Key]\(ディスク サイズ キー\)** : **diskSizeGB** を指定します。 これは、ストアに追加したキー値の名前です。

1. **[Review + create]\(レビュー + 作成\)** を選択します。
1. "**検証に成功しました**" と表示されていることを確認して、 **[作成]** を選択します。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

1. [Azure portal](https://portal.azure.com) にサインインし、新しく作成された仮想マシンに移動します。
1. 左側のメニューから **[概要]** を選択し、 **[SKU]** が **2019-Datacenter** であることを確認します。
1. 左側のメニューから **[ディスク]** を選択し、データ ディスクのサイズが **2013** であることを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、リソース グループ、App Configuration ストア、VM、および関連するすべてのリソースを削除します。 App Configuration ストアまたは VM を今後使用する予定がある場合は、削除をスキップできます。 このジョブを今後使用しない場合には、次のコマンドレットを実行して、このクイック スタートで作成したすべてのリソースを削除してください。

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Resource Manager テンプレートと Azure App Configuration のキー値を使用して VM をデプロイしました。

Azure App Configuration を使用して他のアプリケーションを作成する方法については、次の記事に進んでください。

> [!div class="nextstepaction"]
> [クイック スタート: Azure App Configuration を使用して ASP.NET Core アプリを作成する](quickstart-aspnet-core-app.md)
