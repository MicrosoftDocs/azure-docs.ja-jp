---
title: Azure API for FHIR のカスタマー マネージド キーを構成する
description: Azure API for FHIR で Cosmos DB を通じてサポートされる Bring Your Own Key 機能
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: ginle
ms.openlocfilehash: ae78aa80594e46b02d77adcafed961e801780d4f
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430261"
---
# <a name="configure-customer-managed-keys-at-rest"></a>保存状態のカスタマー マネージド キーを構成する

新しい Azure API for FHIR アカウントを作成すると、既定では、Microsoft マネージド キーを使用してデータが暗号化されます。 これで、独自のキーを使用して第 2 のデータ暗号化レイヤーを追加できるようになります。このキーは、自分で選んで管理することが可能です。

Azure では通常、これを実現するために、お客様の Azure Key Vault にある暗号化キーが使用されます。 現在、その機能が提供される例として、Azure SQL、Azure Storage、Cosmos DB があります。 Azure API for FHIR は、このサポートを Cosmos DB から利用します。 アカウントを作成するときに、Azure Key Vault キーの URI を指定することもできます。 このキーは、DB アカウントのプロビジョニング時に、Cosmos DB に渡されます。 FHIR 要求が送信されると、Cosmos DB はキーを取得し、それを使用してデータの暗号化または暗号化解除を実行します。 まずは、次のリンク先を参照してください。

- [Azure サブスクリプション用の Azure Cosmos DB リソース プロバイダーを登録する](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Azure Key Vault インスタンスを構成する](../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
- [Azure Key Vault インスタンスにアクセス ポリシーを追加する](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)
- [Azure Key Vault でキーを生成する](../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

## <a name="specify-the-azure-key-vault-key"></a>Azure Key Vault キーを指定する

Azure portal で Azure API for FHIR アカウントを作成すると、[追加の設定] タブの [データベースの設定] に [データの暗号化] 構成オプションが表示されます。既定では、[サービス マネージド キー] オプションが選択されます。 

キーの選択から、自分のキーを選択することもできます。

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="キーの選択":::

または、ここで、[カスタマー マネージド キー] オプションを選択することで、自分の Azure Key Vault キーを指定できます。 ここには、キーの URI を入力できます。

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Azure API for FHIR の作成":::

既存の FHIR アカウントの場合は、次に示すように、[データベース] ブレードでキー暗号化の選択肢 (サービスまたはカスタマー マネージド キー) が表示されます。 この構成オプションは、選択後に変更することはできません。 ただし、キーを変更したり更新したりすることはできます。

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="[データベース]":::

さらに、指定したキーの新しいバージョンを作成することができます。その後、サービスが中断されることなく、既存のデータはその新しいバージョンで暗号化されます。 また、キーへのアクセス権を削除することで、データへのアクセス権を削除することもできます。 キーが無効になると、クエリがエラーになります。 キーが再度有効になると、クエリが再び正常に実行されるようになります。

## <a name="next-steps"></a>次のステップ

この記事では、保存状態のカスタマー マネージド キーを構成する方法について説明しました。 この後は、Azure Cosmos DB の FAQ セクションを参照してください。 
 
>[!div class="nextstepaction"]
>[Cosmos DB: カスタマー マネージド キーを設定する方法](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#frequently-asked-questions)
