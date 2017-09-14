---
title: "Azure マネージ アプリケーションの使用 | Microsoft Docs"
description: "公開されているファイルから Azure マネージ アプリケーションを作成する方法について、顧客の視点で説明します。"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/23/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: ed8fbaf2a4546c8e31eeced11cd0b5627fd62c0c
ms.contentlocale: ja-jp
ms.lasthandoff: 08/25/2017

---
# <a name="consume-an-internal-managed-application"></a>内部マネージ アプリケーションの実行

組織のメンバーを対象とする Azure [マネージ アプリケーション](managed-application-overview.md)を使用できます。 たとえば、組織標準に確実に準拠する IT 部門のマネージ アプリケーションを選択できます。 こうしたマネージ アプリケーションは、Azure Marketplace ではなく、サービス カタログを利用して入手できます。

この記事を読み進める前に、サブスクリプションのサービス カタログで入手可能なマネージ アプリケーションが必要となります。 組織内でまだマネージ アプリケーションを作成していない場合は、「[サービス カタログ マネージ アプリケーションの作成と発行](managed-application-publishing.md)」をご覧ください。

マネージ アプリケーションの実行には現在、Azure CLI または Azure Portal を使うことができます。

## <a name="create-the-managed-application-by-using-the-portal"></a>ポータルを使ったマネージ アプリケーションの作成

ポータルを利用してマネージ アプリケーションをデプロイするには、次の手順を行ってください。

1. Azure Portal にアクセスします。 **サービス カタログ マネージ アプリケーション**の検索。

   ![サービス カタログ マネージ アプリケーション](./media/managed-application-consumption/create-service-catalog-managed-application.png)

1. 利用可能なソリューションの一覧から、作成するマネージ アプリケーションを選択します。 **[作成]**を選択します。

   ![マネージ アプリケーションの選択](./media/managed-application-consumption/select-offer.png)

1. リソースのプロビジョニングに必要なパラメーターの値を指定します。 場所は **[米国中西部]** を選択します。 **[OK]**を選択します。

   ![マネージ アプリケーションのパラメーター](./media/managed-application-consumption/input-parameters.png)

1. テンプレートは指定した値を検証します。 検証が成功したら、**[OK]** を選択してデプロイを開始します。

   ![マネージ アプリケーションの検証](./media/managed-application-consumption/validation.png)

デプロイが完了すると、テンプレートに定義されている適切なリソースが、指定したマネージ リソース グループにプロビジョニングされます。

## <a name="create-the-managed-application-by-using-azure-cli"></a>Azure CLI を使ったマネージ アプリケーションの作成

Azure CLI を使ってマネージ アプリケーションを作成する方法は 2 つあります。

* マネージ アプリケーションを作成するコマンドを使用する。
* 通常のテンプレート デプロイ コマンドを使用する。

### <a name="use-the-template-deployment-command"></a>テンプレート デプロイ コマンドを使用する

ベンダーによって作成された applianceMainTemplate.json ファイルをデプロイします。

次に、リソース グループを 2 つ作成します。 1 つ目は、マネージ アプリケーション リソース (Microsoft.Solutions/appliances) の作成先となるリソース グループです。 2 つ目は、mainTemplate.json で定義されているすべてのリソースを含むことになるリソース グループです。 このリソース グループは ISV が管理します。

```azurecli
az group create --name mainResourceGroup --location westcentralus
az group create --name managedResourceGroup --location westcentralus
```

> [!NOTE]
> リソース グループの場所には `westcentralus` を使用します。
>

applianceMainTemplate.json を mainResourceGroup にデプロイするには、次のコマンドを使用します。

```azurecli
az group deployment create --name managedAppDeployment --resourceGroup mainResourceGroup --templateUri
```

前述のテンプレートの実行後、テンプレートに定義されているパラメーターの値を入力するよう求められます。 テンプレート内のリソースをプロビジョニングするうえで必要なパラメーターのほか、次に示す 2 つの重要なパラメーターの値が必要です。

- **managedResourceGroupId**: applianceMainTemplate.json に定義されているリソースを含むリソース グループの ID。 この ID は `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}` という形式です。 先ほどの例では、`managedResourceGroup` の ID が該当します。
- **applianceDefinitionId**: マネージ アプリケーション定義リソースの ID。 この値は、ISV から提供されます。

> [!NOTE]
> 発行元は、マネージ アプリケーションの定義を含むリソース グループへのアクセスを許可する必要があります。 定義リソースは、発行元のサブスクリプションに作成されます。 したがって顧客のテナント内のユーザー、ユーザー グループ、アプリケーションには、このリソースへの読み取りアクセス権が必要となります。

デプロイが正常に完了すると、マネージ アプリケーションが mainResourceGroup に作成されていることを確認できます。 storageAccount リソースは、managedResourceGroup に作成されます。

### <a name="use-the-create-command"></a>作成コマンドを使用する

マネージ アプリケーションは、`az managedapp create` コマンドを使ってマネージ アプリケーション定義から作成することができます。

```azurecli
az managedapp create --name ravtestappliance401 --location "westcentralus"
    --kind "Servicecatalog" --resource-group "ravApplianceCustRG401"
    --managedapp-definition-id "/subscriptions/{guid}/resourceGroups/ravApplianceDefRG401/providers/Microsoft.Solutions/applianceDefinitions/ravtestAppDef401"
    --managed-rg-id "/subscriptions/{guid}/resourceGroups/ravApplianceCustManagedRG401"
    --parameters "{\"storageAccountName\": {\"value\": \"ravappliancedemostore1\"}}"
    --debug
```

* **appliance-definition-Id**: 前の手順で作成したマネージ アプリケーション定義のリソース ID。 この ID を取得するには、次のコマンドを実行します。

  ```azurecli
  az appliance definition show -n ravtestAppDef1 -g ravApplianceRG2
  ```

  このコマンドによって、マネージ アプリケーション定義が返されます。 必要になるのは、ID プロパティの値です。

* **managed-rg-id**: applianceMainTemplate.json に定義されているリソースを含むリソース グループの名前。 このリソース グループはマネージ リソース グループであり、 発行元が管理します。 存在しない場合は、自動的に作成されます。
* **resource-group**: マネージ アプリケーション リソースの作成先となるリソース グループ。 Microsoft.Solutions/appliance リソースは、このリソース グループに存在します。
* **parameters**: applianceMainTemplate.json に定義されているリソースに必要なパラメーター。

## <a name="known-issues"></a>既知の問題

このプレビュー リリースには、次の問題が確認されています。

* マネージ アプリケーションの作成中、500 内部サーバー エラーが表示されます。 これは、多くの場合、断続的な問題です。 操作をやり直してください。
* マネージ リソース グループには新しいリソース グループを使う必要があります。 既存のリソース グループを使うと、デプロイに失敗します。
* Microsoft.Solutions/appliances リソースを含むリソース グループは、**westcentralus** に作成する必要があります。

## <a name="next-steps"></a>次のステップ

* マネージ アプリケーションの概要については、[マネージ アプリケーションの概要](managed-application-overview.md)に関するページをご覧ください。
* サービス カタログ マネージ アプリケーションの発行については、「[サービス カタログ マネージ アプリケーションの作成と発行](managed-application-publishing.md)」を参照してください。
* マネージ アプリケーションを Azure Marketplace に発行する方法については、「[Marketplace の Azure マネージ アプリケーション](managed-application-author-marketplace.md)」を参照してください。
* Marketplace からマネージ アプリケーションを使用する方法については、「[Consume Azure managed applications in the Marketplace (Marketplace での Azure マネージ アプリケーションの使用)](managed-application-consume-marketplace.md)」を参照してください。

