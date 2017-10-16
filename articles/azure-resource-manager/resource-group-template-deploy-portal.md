---
title: "Azure Portal を使用した Azure リソースのデプロイ | Microsoft Docs"
description: "Azure ポータルと Azure Resource Manager を使用して、リソースをデプロイします。"
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: tomfitz
ms.openlocfilehash: ea91fdd58dd3b5c118fe390afe1eb355e3c26570
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Resource Manager テンプレートと Azure ポータルを使用したリソースのデプロイ

このトピックでは、[Azure Portal](https://portal.azure.com) と [Azure Resource Manager](resource-group-overview.md) を使用して Azure リソースをデプロイする方法について説明します。 リソース管理の詳細については、「 [ポータルを使用した Azure リソースの管理](resource-group-portal.md)」を参照してください。

## <a name="create-resource-group"></a>Create resource group

1. 空のリソース グループを作成するには、**[リソース グループ]** を選択します。

   ![リソース グループの選択](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. [リソース グループ] から **[追加]** を選択します。

   ![リソース グループの追加](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. 名前と場所を指定し、必要に応じてサブスクリプションを選択します。 リソース グループにはリソースに関するメタデータが格納されるため、リソース グループの場所を入力する必要があります。 場合によっては、コンプライアンス上の理由から、そのメタデータが格納される場所を指定する必要があります。 一般に、ほとんどのリソースが存在する場所を指定することをお勧めします。 同じ場所を使用することで、テンプレートを簡素化できます。

   ![グループの値の設定](./media/resource-group-template-deploy-portal/set-group-properties.png)

   プロパティの設定が完了したら、**[作成]** を選択します。

1. 新しいリソース グループを表示するには、**[更新]** を選択します。

   ![リソース グループの更新](./media/resource-group-template-deploy-portal/refresh-resource-groups.png)

## <a name="deploy-resources-from-marketplace"></a>Marketplace からリソースをデプロイする

リソース グループを作成したら、それに Marketplace からリソースをデプロイできます。 Marketplace には、一般的なシナリオに対応する事前定義されたソリューションが用意されています。

1. デプロイを開始するには、**[新規]** を選択します。

   ![新しいリソース](./media/resource-group-template-deploy-portal/new-resources.png)

1. デプロイするリソースの種類を検索します。

   ![リソースの種類の選択](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. デプロイする特定のソリューションが表示されない場合は、Marketplace で検索できます。 たとえば、Wordpress ソリューションを検索するには、「**Wordpress**」と入力して、目的のオプションを選択します。

   ![Marketplace の検索](./media/resource-group-template-deploy-portal/search-resource.png)

1. 選択したリソースの種類によっては、デプロイ前に設定する必要がある、関連する一連のプロパティがあります。 すべての種類で、対象リソース グループを選択する必要があります。 次の画像は、Web アプリを作成し、先ほど作成したリソース グループにデプロイする方法を示しています。

   ![Create resource group](./media/resource-group-template-deploy-portal/select-existing-group.png)

   また、リソースをデプロイするときにリソース グループを作成することもできます。 **[新規作成]** を選択して、リソース グループに名前を付けます。

   ![新しいリソース グループの作成](./media/resource-group-template-deploy-portal/select-new-group.png)

1. デプロイが開始されます。 デプロイには数分かかる場合があります。 デプロイが完了すると、通知が表示されます。

   ![通知の表示](./media/resource-group-template-deploy-portal/view-notification.png)

1. リソースのデプロイ後、**[追加]** を選択して、リソース グループにリソースを追加することができます。

   ![リソースの追加](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>カスタム テンプレートからリソースをデプロイする

デプロイを実行するが、Marketplace 内のテンプレートを使用しない場合は、ソリューションのインフラストラクチャを定義するカスタマイズされたテンプレートを作成できます。 テンプレートの作成の詳細については、「[Azure Resource Manager テンプレートの構造と構文の詳細](resource-group-authoring-templates.md)」をご覧ください。

1. カスタマイズしたテンプレートをポータルからデプロイするには、**[新規]** を選択し、**[テンプレートのデプロイ]** を検索してオプションに表示します。

   ![テンプレートのデプロイの検索](./media/resource-group-template-deploy-portal/search-template.png)

1. **[作成]**を選択します。

   ![作成の選択](./media/resource-group-template-deploy-portal/show-template-option.png)

1. テンプレートを作成するためのいくつかのオプションが表示されます。 **[Build your own template in the editor] \(エディターで独自のテンプレートをビルド\)** を選択します。

   ![オプションの表示](./media/resource-group-template-deploy-portal/see-options.png)

1. カスタマイズに利用できる空のテンプレートがあります。

   ![テンプレートの作成](./media/resource-group-template-deploy-portal/blank-template.png)

1. JSON 構文を手動で編集するか、または[クイックスタート テンプレート ギャラリー](https://azure.microsoft.com/resources/templates/)から構築済みのテンプレートを選択することができます。 ただし、この記事では **[リソースの追加]** オプションを使用します。

   ![テンプレートの編集](./media/resource-group-template-deploy-portal/select-add-resource.png)

1. **[ストレージ アカウント]** を選択し、名前を入力します。 値を入力したら、**[OK]** を選択します。

   ![ストレージ アカウントを選択する](./media/resource-group-template-deploy-portal/add-storage-account.png)

1. エディターのリソースの種類に JSON が自動的に追加されます。 ストレージ アカウントの種類を定義するためのパラメーターが含まれていることを確認します。 [ **保存**] を選択します。

   ![テンプレートの表示](./media/resource-group-template-deploy-portal/show-json.png)

1. テンプレートに定義されたリソースをデプロイするオプションがあります。 デプロイするには、使用条件に同意して、**[購入]** を選択します。

   ![テンプレートのデプロイ](./media/resource-group-template-deploy-portal/provide-custom-template-values.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>アカウントに保存されたテンプレートからリソースをデプロイする

ポータルでは、Azure アカウントにテンプレートを保存し、後で再デプロイを行うことができます。 保存したテンプレートの操作の詳細については、「 [Azure ポータルでプライベート テンプレートを使ってみる](../marketplace-consumer/mytemplates-getstarted.md)」を参照してください。

1. 保存したテンプレートを検索するには、**[その他のサービス]** を選択します。

   ![その他のサービス](./media/resource-group-template-deploy-portal/more-services.png)

1. **テンプレート**を検索し、そのオプションを選択します。

   ![テンプレートの検索](./media/resource-group-template-deploy-portal/find-templates.png)

1. アカウントに保存されたテンプレートの一覧から、使用するテンプレートを選択します。

   ![保存されたテンプレート](./media/resource-group-template-deploy-portal/saved-templates.png)

1. **[デプロイ]** を選択し、保存されたテンプレートを再デプロイします。

   ![保存されたテンプレートのデプロイ](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>次のステップ
* 監査ログの表示については、「 [Resource Manager の監査操作](resource-group-audit.md)」を参照してください。
* デプロイ エラーをトラブルシューティングするには、「[デプロイ操作の表示](resource-manager-deployment-operations.md)」を参照してください。
* デプロイまたはリソース グループからテンプレートを取得するには、「 [既存のリソースから Azure Resource Manager テンプレートをエクスポートする](resource-manager-export-template.md)」を参照してください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](resource-manager-subscription-governance.md)」を参照してください。
