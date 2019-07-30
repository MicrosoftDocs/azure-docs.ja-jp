---
title: Azure Portal を使用した Azure リソースのデプロイ | Microsoft Docs
description: Azure ポータルと Azure Resource Manager を使用して、リソースをデプロイします。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: tomfitz
ms.openlocfilehash: a171d9b4f054c942eebb08e7e11dd1164545f408
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67460280"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Resource Manager テンプレートと Azure ポータルを使用したリソースのデプロイ

[Azure portal](https://portal.azure.com) と [Azure Resource Manager](resource-group-overview.md) を使用して Azure リソースをデプロイする方法について説明します。 リソース管理の詳細については、「[Manage Azure resources by using the Azure portal (Azure portal を使用した Azure リソースの管理)](manage-resources-portal.md)」を参照してください。

Azure portal を使用して Azure リソースをデプロイするには、通常、次の 2 つの手順が必要です。

- リソース グループを作成します。
- リソースをリソース グループにデプロイします。

さらに、Azure Resource Manager テンプレートをデプロイして Azure リソースを作成することもできます。

このトピックでは、両方の方法を説明します。

## <a name="create-a-resource-group"></a>リソース グループの作成

1. 新しいリソース グループを作成するには、[Azure portal](https://portal.azure.com) から **[リソース グループ]** を選択します。

   ![リソース グループの選択](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. [リソース グループ] から **[追加]** を選択します。

   ![リソース グループの追加](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. 次のプロパティ値を選択または入力します。

    - **サブスクリプション**:Azure サブスクリプションを選択します。
    - **[リソース グループ]** :リソース グループに名前を付けます。
    - **[リージョン]** :Azure の場所を指定します。 これは、リソース グループによってリソースに関するメタデータが格納される場所です。 場合によっては、コンプライアンス上の理由から、そのメタデータが格納される場所を指定する必要があります。 一般に、ほとんどのリソースが存在する場所を指定することをお勧めします。 同じ場所を使用することで、テンプレートを簡素化できます。

   ![グループの値の設定](./media/resource-group-template-deploy-portal/set-group-properties.png)

1. **[Review + create]\(レビュー + 作成\)** を選択します。
1. 値を確認し、 **[作成]** を選択します。
1. **[更新]** を選択すると、一覧で新しいリソース グループを確認できます。

## <a name="deploy-resources-to-a-resource-group"></a>リソースをリソース グループにデプロイする

リソース グループを作成したら、そのグループに Marketplace からリソースをデプロイできます。 Marketplace には、一般的なシナリオに対応する事前定義されたソリューションが用意されています。

1. デプロイを開始するには、[Azure portal](https://portal.azure.com) から **[リソースの作成]** を選択します。

   ![新しいリソース](./media/resource-group-template-deploy-portal/new-resources.png)

1. デプロイするリソースの種類を検索します。 リソースはカテゴリに分類されています。 デプロイする特定のソリューションが表示されない場合は、Marketplace で検索できます。 Ubuntu サーバーが選択されているスクリーンショットを次に示します。

   ![リソースの種類の選択](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. 選択したリソースの種類によっては、デプロイ前に設定する必要がある、関連する一連のプロパティがあります。 すべての種類で、対象リソース グループを選択する必要があります。 次の画像は、Linux 仮想マシンを作成し、先ほど作成したリソース グループにデプロイする方法を示しています。

   ![リソース グループの作成](./media/resource-group-template-deploy-portal/select-existing-group.png)

   また、リソースをデプロイするときにリソース グループを作成することもできます。 **[新規作成]** を選択して、リソース グループに名前を付けます。

1. デプロイが開始されます。 デプロイには数分かかることがあります。 一部のリソースは他のリソースよりも時間がかかります。 デプロイが完了すると、通知が表示されます。 **[リソースに移動]** を選択して開きます

   ![通知の表示](./media/resource-group-template-deploy-portal/view-notification.png)

1. リソースのデプロイ後、 **[追加]** を選択して、リソース グループにリソースを追加することができます。

   ![リソースの追加](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>カスタム テンプレートからリソースをデプロイする

デプロイを実行するが、Marketplace 内のテンプレートを使用しない場合は、ソリューションのインフラストラクチャを定義するカスタマイズされたテンプレートを作成できます。 テンプレートの作成の詳細については、「[Azure Resource Manager テンプレートの構造と構文の詳細](resource-group-authoring-templates.md)」をご覧ください。

> [!NOTE]
> ポータル インターフェイスは、[Key Vault からのシークレット](resource-manager-keyvault-parameter.md)の参照をサポートしません。 代わりに、[PowerShell](resource-group-template-deploy.md) または [Azure CLI](resource-group-template-deploy-cli.md) を使用して、テンプレートをローカルにデプロイするか、外部 URI からデプロイします。

1. カスタマイズされたテンプレートをポータルからデプロイするには、 **[リソースの作成]** を選択し、**テンプレート**を探します。 次に **[テンプレートのデプロイ]** を選択します。

   ![テンプレートのデプロイの検索](./media/resource-group-template-deploy-portal/search-template.png)

1. **作成** を選択します。
1. テンプレートを作成するためのいくつかのオプションが表示されます。

    - **エディターで独自のテンプレートを作成する**:ポータル テンプレート エディターを使用してテンプレートを作成します。  エディターには、リソース テンプレート スキーマを追加する機能があります。
    - **一般的なテンプレート**:Linux 仮想マシン、Windows 仮想マシン、Web アプリケーション、および Azure SQL データベースを作成するための 4 つの一般的なテンプレートがあります。
    - **GitHub クイックスタート テンプレートを読み込む**: 既存の[クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/)を使用します。

   ![オプションの表示](./media/resource-group-template-deploy-portal/see-options.png)

    このチュートリアルでは、クイックスタート テンプレートを読み込む方法について説明します。

1. **[GitHub クイックスタート テンプレートを読み込む]** で、「**101-storage-account-create**」を入力するか選択します。

    2 つのオプションがあります。

    - **テンプレートの選択**: テンプレートをデプロイします。
    - **テンプレートの編集**: デプロイする前にクイックスタート テンプレートを編集します。

1. **[テンプレートの編集]** を選択してポータル テンプレート エディターを探索します。 テンプレートがエディターに読み込まれます。 **storageAccountType** と **location** という 2 つのパラメーターがあることに注目してください。

   ![テンプレートの作成](./media/resource-group-template-deploy-portal/show-json.png)

1. テンプレートに軽微な変更を加えます。 たとえば、**storageAccountName** 変数を次のように更新します。

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. **[保存]** を選択します。 これで、ポータル テンプレート デプロイ インターフェイスが表示されます。 テンプレートで定義した 2 つのパラメーターに注目してください。
1. プロパティ値を入力または選択します。

    - **サブスクリプション**:Azure サブスクリプションを選択します。
    - **[リソース グループ]** : **[新規作成]** を選択し、名前を付けます。
    - **[場所]** :Azure の場所を選択します。
    - **ストレージ アカウントの種類**:既定値を使用します。
    - **[場所]** :既定値を使用します。
    - **[上記の使用条件に同意する]** : (オン)

1. **[購入]** を選択します。

## <a name="next-steps"></a>次の手順

- 監査ログの表示については、「 [Resource Manager の監査操作](./resource-group-audit.md)」を参照してください。
- デプロイ エラーをトラブルシューティングするには、「[デプロイ操作の表示](./resource-manager-deployment-operations.md)」を参照してください。
- デプロイまたはリソース グループからテンプレートをエクスポートするには、[Azure Resource Manager テンプレートのエクスポート](./manage-resource-groups-portal.md#export-resource-groups-to-templates)に関する記事を参照してください。
- 複数のリージョン間で、サービスを安全にロール アウトするには、[Azure デプロイ マネージャー](./deployment-manager-overview.md)を参照してください。
