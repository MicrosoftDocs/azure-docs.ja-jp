---
title: B2B 統合アカウントを作成または管理する
description: Azure Logic Apps とのエンタープライズ統合のための統合アカウントを作成、リンク、および管理する
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: 51059dd1c4c5c93e155cd7a2d34c3cbaf29db6e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101705587"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Azure Logic Apps で B2B エンタープライズ統合用の統合アカウントを作成および管理する

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) を使用して[エンタープライズ統合および B2B ソリューション](../logic-apps/logic-apps-enterprise-integration-overview.md)をビルドする前に、統合アカウントを作成する必要があります。これは、ロジック アプリ ワークフローで定義して使用する統合成果物のためのセキュリティで保護され、スケーラブルで管理可能なコンテナーを提供する個別の Azure リソースです。

たとえば、取引先、契約、マップ、スキーマ、証明書、バッチ構成などの B2B 成果物を作成、保存、管理できます。 また、ロジック アプリでこれらの成果物を操作し、Logic Apps B2B コネクタを使用できるようにするには、お使いのロジック アプリと[ご自分の統合アカウントをリンクさせる](#link-account)必要があります。 統合アカウントとロジック アプリは、どちらも *同じ* 場所またはリージョンに存在する必要があります。

> [!IMPORTANT]
> 選択した統合アカウントの種類に基づいて、統合アカウントを作成するとコストが発生します。 詳細については、「[Logic Apps の価格モデルと課金モデル](logic-apps-pricing.md#integration-accounts)」と「[Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps/)」をご覧ください。

このトピックでは、次のタスクの実行方法について説明します。

* 統合アカウントの作成

  > [!TIP]
  > [統合サービス環境](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)内で統合アカウントを作成するには、「[ISE で統合アカウントを作成する](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)」を参照してください。

* ロジック アプリへの統合アカウントのリンク

* 統合アカウントの価格レベルを変更する。

* ロジック アプリから統合アカウントのリンクを解除する。

* 別の Azure リソース グループまたはサブスクリプションへの統合アカウントの移動

* 統合アカウントの削除

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

## <a name="create-integration-account"></a>統合アカウントを作成する

### <a name="portal"></a>[ポータル](#tab/azure-portal)

このタスクでは、このセクションの手順に従って Azure portal を使用するか、[Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)、または [Azure CLI](/cli/azure/resource#az-resource-create) のいずれかを使用できます。

1. Azure アカウントの資格情報で [Azure Portal](https://portal.azure.com) にサインインします。

1. Azure のメイン メニューで **[リソースの作成]** を選択します。 検索ボックスにフィルターとして「統合アカウント」と入力し、 **[統合アカウント]** を選択します。

   ![新しい統合アカウントの作成](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. **[統合アカウント]** で **[作成]** を選択します。

   ![[追加] を選択して統合アカウントを作成](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. 統合アカウントに関する次の情報を指定します。

   ![統合アカウントの詳細の指定](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **名前** | はい | <*integration-account-name*> | 統合アカウントの名前。文字、数字、ハイフン (`-`)、アンダースコア (`_`)、かっこ (`(`、`)`)、およびピリオド (`.`) のみを含めることができます。 この例では、"Fabrikam-Integration" を使用します。 |
   | **サブスクリプション** | はい | <*Azure サブスクリプション名*> | Azure サブスクリプションの名前 |
   | **リソース グループ** | はい | <*Azure-resource-group-name*> | 関連するリソースを整理するために使用する [Azure リソース グループ](../azure-resource-manager/management/overview.md)の名前。 この例では、"FabrikamIntegration-RG" という名前の新しいリソース グループを作成します。 |
   | **価格レベル** | はい | <*pricing-level*> | 統合アカウントの価格レベル。後で変更できます。 この例では **[Free]** を選択します。 詳細については、以下のトピックを参照してください。 <p>- [Logic Apps の価格モデル](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [Logic Apps の制限と構成](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **場所** | はい | <*Azure-region*> | 統合アカウント メタデータの保存先となるリージョン。 ロジック アプリと同じ場所を選択するか、統合アカウントと同じ場所でロジック アプリを作成します。 この例では、[米国西部] を使用します。 <p>**注**:[統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 内で統合アカウントを作成するには、その ISE を場所として選択します。 詳細については、「[ISE で統合アカウントを作成する](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)」を参照してください。 |
   | **Log Analytics** | いいえ | Off、On | この例では、 **[Off]** の設定のままにします。 |
   |||||

1. 完了したら、 **[作成]** をクリックします。

   デプロイが完了すると、Azure によって統合アカウントが開かれます。

   ![Azure によって開かれた統合アカウント](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. ロジック アプリで統合アカウントを使用するには、次の手順に従って、事前に統合アカウントとロジック アプリをリンクします。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

このセクションの Azure CLI コマンドを使用すると、統合アカウントを作成できます。

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-an-integration-account"></a>統合アカウントの作成

統合アカウントを作成するには、こちらのコマンドを使用します。

1. [az logic integration-account](/cli/azure/ext/logic/logic/integration-account) 拡張機能を追加するには、[az extension add](/cli/azure/extension#az_extension_add)コマンドを使用します。

   ```azurecli
   az extension add –-name logic
   ```

1. リソース グループを作成するか、既存のリソース グループを使用するには、[az group create](/cli/azure/group#az_group_create) コマンドを実行します。

   ```azurecli
   az group create --name myresourcegroup --location westus
   ```

   リソース グループの統合アカウントの一覧を表示するには、[az logic integration-account list](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_list) コマンドを使用します。

   ```azurecli
   az logic integration-account list --resource-group myresourcegroup
   ```

1. 統合アカウントを作成するには、[az logic integration-account create](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_create) コマンドを実行します。

   ```azurecli
   az logic integration-account create --resource-group myresourcegroup \
       --name integration_account_01 --location westus --sku name=Standard
   ```

   統合アカウントの名前には、文字、数字、ハイフン (-)、アンダースコア (_)、かっこ ((、))、およびピリオド (.) のみを含めることができます。

   > [!TIP]
   > [統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 内で統合アカウントを作成するには、その ISE を場所として選択します。 詳細については、「[ISE で統合アカウントを作成する](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)」を参照してください。

   特定の統合アカウントを表示するには、[az logic integration-account show](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_show) コマンドを使用します。

   ```azurecli
   az logic integration-account show --name integration_account_01 --resource-group myresourcegroup
   ```

   [az logic integration-account update](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_update) コマンドを使用すると、SKU または価格レベルを変更できます。

   ```azurecli
   az logic integration-account update --sku name=Basic --name integration_account_01 \
       --resource-group myresourcegroup
   ```

   価格の詳細については、こちらのリソースを参照してください。

   * [Logic Apps の価格モデル](../logic-apps/logic-apps-pricing.md#integration-accounts)
   * [Logic Apps の制限と構成](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)
   * [Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps/)

JSON ファイルを使用して統合アカウントをインポートするには、[az logic integration-account import](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_import) コマンドを使用します。

```azurecli
az logic integration-account import --name integration_account_01 \
    --resource-group myresourcegroup --input-path integration.json
```

[az logic integration-account delete](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_delete) コマンドを使用すると、統合アカウントを削除できます。

```azurecli
az logic integration-account delete --name integration_account_01 --resource-group myresourcegroup
```

ロジック アプリで統合アカウントを使用するには、事前に統合アカウントとロジック アプリをリンクします。 次のセクションではリンクについて説明します。

---
<a name="link-account"></a>

## <a name="link-to-logic-app"></a>ロジック アプリにリンクする

ロジック アプリに B2B 成果物が含まれている統合アカウントへのアクセスを付与するには、最初に統合アカウントとロジック アプリをリンクさせる必要があります。 ロジック アプリと統合アカウントはどちらも、同じリージョンに存在している必要があります。 このタスクを完了するには、Azure portal を使用します。 Visual Studio を使用していて、お使いのロジック アプリが [Azure リソース グループ プロジェクト](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)に含まれている場合は、[Visual Studio を使用してロジック アプリを統合アカウントにリンクさせる](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account)ことができます。

1. Azure Portal で、ご利用のロジック アプリを探して選択します。

1. [Azure portal](https://portal.azure.com)で、既存のロジック アプリを開くか、新しいロジック アプリを作成します。

1. ロジック アプリのメニューの **[設定]** で、 **[ワークフロー設定]** を選択します。 **[統合アカウント]** の下で、 **[統合アカウントを選択してください]** の一覧を開きます。 ロジック アプリにリンクする統合アカウントを選択します。

   ![統合アカウントを選択する](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. リンクを終了するには、 **[保存]** を選択します。

   ![統合アカウントを選択するために [保存] を選択する場所を示すスクリーンショット。](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   統合アカウントが正常にリンクされると、Azure によって確認メッセージが表示されます。

   ![Azure による正常なリンクの確認](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

これで、ロジック アプリが、統合アカウントと B2B コネクタで成果物を使用できるようになりました (XML 検証、フラット ファイルのエンコードまたはデコードなど)。  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>価格レベルの変更

統合アカウントの[制限](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)を引き上げるために、より高い[価格レベル](#upgrade-pricing-tier)にアップグレードすることができます (使用可能な場合)。 たとえば、Free レベルから Basic レベルまたは Standard レベルにアップグレードできます。 また、使用可能な場合は、[下位レベルにダウングレード](#downgrade-pricing-tier)することもできます。 価格情報の詳細については、次のトピックを参照してください。

* [Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Logic Apps の価格モデル](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>価格レベルのアップグレード

この変更を行うには、Azure portal または Azure CLI のいずれかを使用できます。

#### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. Azure アカウントの資格情報で [Azure Portal](https://portal.azure.com) にサインインします。

1. Azure のメイン検索ボックスに、フィルターとして「統合アカウント」と入力し、 **[統合アカウント]** を選択します。

   ![統合アカウントの検索](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure で、Azure サブスクリプションのすべての統合アカウントが表示されます。

1. **[統合アカウント]** で、移動する統合アカウントを選びます。 統合アカウント メニューで、 **[概要]** を選択します。

   ![統合アカウント メニューで、[概要] を選択](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. [概要] ウィンドウで、 **[価格レベルのアップグレード]** を選択します。これにより、利用可能な上位レベルがすべて一覧表示されます。 レベルを選択すると、変更がすぐに反映されます。

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI の前提条件をインストールします](/cli/azure/get-started-with-azure-cli) (まだインストールしていない場合)。

1. Azure portal で、[Azure Cloud Shell](../cloud-shell/overview.md) 環境を開きます。

   ![Azure Cloud Shell を開く](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. コマンド プロンプトで [**az resource** コマンド](/cli/azure/resource#az-resource-update)を入力し、`skuName` を目的の上位レベルに設定します。

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   たとえば、Basic レベルの場合は、`skuName` を `Standard` に設定できます。

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

---

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>価格レベルのダウングレード

この変更を行うには、[Azure CLI](/cli/azure/get-started-with-azure-cli) を使用します。

1. [Azure CLI の前提条件をインストールします](/cli/azure/get-started-with-azure-cli) (まだインストールしていない場合)。

1. Azure portal で、[Azure Cloud Shell](../cloud-shell/overview.md) 環境を開きます。

   ![Azure Cloud Shell を開く](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. コマンド プロンプトで、[**az resource** コマンド](/cli/azure/resource#az-resource-update)を入力し、`skuName` を目的のレベルに設定します。

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   たとえば、Standard レベルを使用している場合は、`skuName` を `Basic` に設定できます。

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>ロジック アプリのリンクを解除する

ロジック アプリを別の統合アカウントにリンクする場合、またはロジック アプリで統合アカウントの使用を止める場合は、Azure Resource Explorer を使用してリンクを削除します。

1. ブラウザー ウィンドウを開き、[Azure Resource Explorer (https://resources.azure.com)](https://resources.azure.com) にアクセスします。 同じ Azure アカウントの資格情報でサインインします。

   ![Azure Resource Explorer](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. ロジック アプリを検索して選択できるように、検索ボックスにロジック アプリの名前を入力します。

   ![ロジック アプリの検索と選択](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. エクスプローラーのタイトル バーで、 **[読み取り/書き込み]** を選択します。

   !["読み取り/書き込み" モードを有効にする](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. **[データ]** タブで、 **[編集]** を選択します。

   ![[データ] タブで、[編集] を選択](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. エディターで、`integrationAccount` オブジェクトを見つけて、そのプロパティを削除します。形式は以下のとおりです。

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   次に例を示します。

   !["integrationAccount" オブジェクトの検索](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. **[データ]** タブで **[Put]** を選択して変更を保存します。

   ![変更を保存するために [Put] を選択](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. Azure portal で、ご利用のロジック アプリを探して選択します。 アプリの **ワークフロー設定** で、**統合アカウント** プロパティが空になっていることを確認します。

   ![統合アカウントがリンクされていないことを確認](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>統合アカウントの移動

統合アカウントを別の Azure リソース グループまたは Azure サブスクリプションに移動できます。 リソースを移動すると、Azure によって新しいリソース ID が作成されるため、代わりに新しい ID を使用し、移動したリソースに関連付けられているスクリプトまたはツールを更新してください。 サブスクリプションを変更する場合は、既存のリソース グループまたは新しいリソース グループも指定する必要があります。

このタスクでは、このセクションの手順に従って Azure portal を使用するか、[Azure CLI](/cli/azure/resource#az-resource-move) のいずれかを使用できます。

1. Azure アカウントの資格情報で [Azure Portal](https://portal.azure.com) にサインインします。

1. Azure のメイン検索ボックスに、フィルターとして「統合アカウント」と入力し、 **[統合アカウント]** を選択します。

   ![統合アカウントの検索](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure で、Azure サブスクリプションのすべての統合アカウントが表示されます。

1. **[統合アカウント]** で、移動する統合アカウントを選びます。 統合アカウント メニューで、 **[概要]** を選択します。

   ![統合アカウント メニューで、[概要] を選択](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. **[リソース グループ]** または **[サブスクリプション名]** の横にある **[変更]** を選択します。

   ![リソース グループまたはサブスクリプションの変更](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. 移動する関連リソースを選択します。

1. 選択した内容に基づいて、次の手順に従ってリソース グループまたはサブスクリプションを変更します。

   * リソース グループ: **[リソース グループ]** の一覧から、移動先のリソース グループを選択します。 または、別のリソース グループを作成するには、 **[新しいリソース グループの作成]** を選択します。

   * サブスクリプション: **[サブスクリプション]** の一覧から、移動先のサブスクリプションを選択します。 **[リソース グループ]** の一覧から、移動先のリソース グループを選択します。 または、別のリソース グループを作成するには、 **[新しいリソース グループの作成]** を選択します。

1. 移動したリソースに関連付けられているスクリプトやツールは、新しいリソース ID で更新されるまで機能しないことを理解していることを確認するために、[確認] ボックスを選択してから、 **[OK]** を選択します。

1. 完了したら、すべてのスクリプトを移動したリソースの新しいリソース ID で必ず更新してください。  

## <a name="delete-integration-account"></a>統合アカウントの削除

このタスクでは、このセクションの手順に従って Azure portal を使用するか、[Azure CLI](/cli/azure/resource#az-resource-delete)、または [Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount) のいずれかを使用できます。

1. Azure アカウントの資格情報で [Azure Portal](https://portal.azure.com) にサインインします。

1. Azure のメイン検索ボックスに、フィルターとして「統合アカウント」と入力し、 **[統合アカウント]** を選択します。

   ![統合アカウントの検索](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure で、Azure サブスクリプションのすべての統合アカウントが表示されます。

1. **[統合アカウント]** で、削除する統合アカウントを選びます。 統合アカウント メニューで、 **[概要]** を選択します。

   ![統合アカウント メニューで、[概要] を選択](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. [概要] ウィンドウで、 **[削除]** を選択します。

   ![[概要] ウィンドウで、[削除] を選択](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. 統合アカウントを削除することを確認するために **[はい]** を選択します。

   ![削除を確定するために [はい] を選択](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>次のステップ

* [統合アカウントで取引先を作成する](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [統合アカウントで取引先間の契約を作成する](../logic-apps/logic-apps-enterprise-integration-agreements.md)
