---
title: Azure Cosmos DB の Advanced Threat Protection
description: Azure Cosmos DB で保存データが暗号化される方法と、その実装方法について説明します。
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: b73e7f8c13f621bc359a2ae79a725829420a3ecc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455597"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>Azure Cosmos DB の Advanced Threat Protection (プレビュー)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB の Advanced Threat Protection は、通常と異なる潜在的に有害なアクセスまたはエクスプロイトが Azure Cosmos DB アカウントに対して試行されたことを検出するセキュリティ インテリジェンスを強化します。 この保護レイヤーにより、セキュリティの専門家でなくても脅威に対処でき、中央のセキュリティ監視システムでそれらを統合管理できます。

セキュリティ アラートは、アクティビティで異常が発生したときにトリガーされます。 これらのセキュリティ アラートは [Azure Security Center](https://azure.microsoft.com/services/security-center/) に統合され、不審なアクティビティの詳細および脅威を調査して修正する方法に関する推奨事項と共に、サブスクリプション管理者にメールで送信されます。

> [!NOTE]
>
> * Azure Cosmos DB の Advanced Threat Protection は、現時点では SQL API でのみ使用できます。
> * Azure Cosmos DB の Advanced Threat Protection は、現在、Azure Government およびソブリン クラウド リージョンで使用できません。

セキュリティ アラートの完全な調査エクスペリエンスでは、[Azure Cosmos DB の診断ログ](./monitor-cosmos-db.md)を有効にすることをお勧めします。これにより、データベース自体に対する操作 (すべてのドキュメント、コンテナー、データベースに対する CRUD 操作を含む) がログに記録されます。

## <a name="threat-types"></a>脅威の種類

Azure Cosmos DB の Advanced Threat Protection では、データベースへのアクセスやデータベースの悪用を試みる、害を及ぼす可能性のある異常なアクティビティが検出されます。 現在、これにより次のアラートがトリガーされる場合があります。

- **通常と異なる場所からのアクセス**:このアラートは、だれかが通常とは異なる地理的な場所から Azure Cosmos DB エンドポイントに接続したことで Azure Cosmos アカウントへのアクセス パターンに変化が生じたときにトリガーされます。 このアラートによって、正当なアクション、つまり新しいアプリケーションや開発者によるメンテナンス操作が検出されることがあります。 また、元従業員、外部の攻撃者などからの悪意のあるアクションが検出されることもあります。

- **通常と異なるデータの抽出**: このアラートは、クライアントが Azure Cosmos DB アカウントから通常とは異なる量のデータを抽出しているときにトリガーされます。 これは、データ流出が発生し、アカウントに格納されているすべてのデータが外部データ ストアに転送されている可能性があることを示します。



## <a name="configure-advanced-threat-protection"></a>Advanced Threat Protection の構成

Advanced Threat Protection は、次のセクションで説明するいくつかの方法で構成することができます。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. Azure portal ([https://portal.azure.com](https://portal.azure.com/)) を起動します。

2. Azure Cosmos DB アカウントで、 **[設定]** メニューの **[高度なセキュリティ]** を選択します。

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png" alt-text="ATP の設定":::

3. **[高度なセキュリティ]** 構成ブレード:

    * **[Advanced Threat Protection]** オプションをクリックして **[オン]** に設定します。
    * **[保存]** をクリックして、新しいまたは更新された Azure Storage ポリシーを保存します。   

### <a name="rest-api"></a>[REST API](#tab/rest-api)

Rest API のコマンドを使用して、特定の Azure Cosmos DB アカウントの Advanced Threat Protection 設定を作成、更新、または取得します。

* [Advanced Threat Protection - 作成](/rest/api/securitycenter/advancedthreatprotection/create)
* [Advanced Threat Protection - 取得](/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

次の PowerShell コマンドレットを使用します。

* [Advanced Threat Protection を有効にする](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [Advanced Threat Protection を取得する](/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [Advanced Threat Protection を無効にする](/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

### <a name="arm-template"></a>[ARM テンプレート](#tab/arm-template)

Azure Resource Manager (ARM) テンプレートを使用して、Advanced Threat Protection が有効な Cosmos DB を設定します。
詳細については、「[Advanced Threat Protection を使用して CosmosDB アカウントを作成する](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/)」を参照してください。

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Azure Policy を使用して、Cosmos DB の Advanced Threat Protection を有効にします。

1. Azure の **ポリシー - 定義** ページを開き、**Deploy Advanced Threat Protection for Cosmos DB\(Cosmos DB の Azure Advanced Threat Protection をデプロイする\)** ポリシーを探します。

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db.png" alt-text="ポリシーを探す"::: 

1. **[Deploy Advanced Threat Protection for CosmosDB]\(Cosmos DB の Azure Advanced Threat Protection をデプロイする\)** ポリシーをクリックし、 **[割り当て]** をクリックします。

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png" alt-text="サブスクリプションまたはグループを選択する":::


1. **[スコープ]** フィールドから 3 つのドットをクリックし、Azure サブスクリプションまたはリソース グループを選択し、 **[選択]** をクリックします。

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png" alt-text="[ポリシー定義] ページ":::


1. その他のパラメーターを入力し、 **[割り当て]** をクリックします。




## <a name="manage-atp-security-alerts"></a>ATP セキュリティ アラートの管理

Azure Cosmos DB のアクティビティで異常が発生すると、セキュリティ アラートがトリガーされて不審なセキュリティ イベントに関する情報が表示されます。 

 Azure Security Center で、現在の[セキュリティ アラート](../security-center/security-center-alerts-overview.md)を確認して管理することができます。  [Security Center](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) で特定のアラートをクリックすると、考えられる原因および潜在的な脅威を調査して緩和するための推奨アクションが表示されます。 次の図は、Security Center に表示されるアラートの詳細の例を示しています。

 :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png" alt-text="脅威の詳細":::

アラートの詳細と推奨アクションを示すメール通知も送信されます。 次の図はアラート メールの例を示しています。

 :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png" alt-text="アラートの詳細":::

## <a name="cosmos-db-atp-alerts"></a>Cosmos DB ATP のアラート

 Azure Cosmos DB アカウントの監視中に生成されるアラートの一覧については、Security Center のドキュメントで [Cosmos DB のアラート](../security-center/alerts-reference.md#alerts-azurecosmos) に関するセクションをご参照ください。

## <a name="next-steps"></a>次のステップ

* [Azure Cosmos DB の診断ログ](cosmosdb-monitor-resource-logs.md)の詳細を確認します
* [Azure Security Center](../security-center/security-center-introduction.md) の詳細について参照してください