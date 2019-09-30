---
title: Azure Cosmos DB の Advanced Threat Protection
description: Azure Cosmos DB で保存データが暗号化される方法と、その実装方法について説明します。
author: monhaber
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: v-mohabe
ms.custom: seodec18
ms.openlocfilehash: 035c7e9ed5ab33c89a2a55b1133294db434bfe81
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70123909"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db"></a>Azure Cosmos DB の Advanced Threat Protection

Azure Cosmos DB の Advanced Threat Protection は、通常と異なる潜在的に有害なアクセスまたはエクスプロイトが Azure Cosmos DB アカウントに対して試行されたことを検出するセキュリティ インテリジェンスを強化します。 この保護レイヤーにより、セキュリティの専門家でなくても脅威に対処でき、中央のセキュリティ監視システムでそれらを統合管理できます。

セキュリティ アラートは、アクティビティで異常が発生したときにトリガーされます。 これらのセキュリティ アラートは [Azure Security Center](https://azure.microsoft.com/services/security-center/) に統合され、不審なアクティビティの詳細および脅威を調査して修正する方法に関する推奨事項と共に、サブスクリプション管理者にメールで送信されます。

> [!NOTE]
>
> * Azure Cosmos DB の Advanced Threat Protection は、現時点では SQL API でのみ使用できます。
> * Azure Cosmos DB の Advanced Threat Protection は、現在、Azure Government およびソブリン クラウド リージョンで使用できません。

セキュリティ アラートの完全な調査エクスペリエンスでは、[Azure Cosmos DB の診断ログ](https://docs.microsoft.com/azure/cosmos-db/logging)を有効にすることをお勧めします。これにより、データベース自体に対する操作 (すべてのドキュメント、コンテナー、データベースに対する CRUD 操作を含む) がログに記録されます。

## <a name="set-up-advanced-threat-protection"></a>Advanced Threat Protection の設定

### <a name="set-up-atp-using-the-portal"></a>ポータルを使用した ATP の設定

1. Azure portal ([https://portal.azure.com](https://portal.azure.com/)) を起動します。

2. Azure Cosmos DB アカウントで、 **[設定]** メニューの **[高度なセキュリティ]** を選択します。

    ![ATP の設定](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. **[高度なセキュリティ]** 構成ブレード:

    * **[Advanced Threat Protection]** オプションをクリックして **[オン]** に設定します。
    * **[保存]** をクリックして、新しいまたは更新された Azure Storage ポリシーを保存します。   

### <a name="set-up-atp-using-rest-api"></a>REST API を使用した ATP の設定

Rest API のコマンドを使用して、特定の Azure Cosmos DB アカウントの Advanced Threat Protection 設定を作成、更新、または取得します。

* [Advanced Threat Protection - 作成](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Advanced Threat Protection - 取得](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>Azure PowerShell を使用した ATP の設定

次の PowerShell コマンドレットを使用します。

* [Advanced Threat Protection を有効にする](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Advanced Threat Protection を取得する](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [Advanced Threat Protection を無効にする](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

### <a name="using-azure-resource-manager-templates"></a>Azure リソース マネージャーのテンプレートを作成する

Azure Resource Manager テンプレートを使用して、Advanced Threat Protection が有効な Cosmos DB を設定します。
詳細については、「[Advanced Threat Protection を使用して CosmosDB アカウントを作成する](https://azure.microsoft.com/en-us/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/)」を参照してください。

### <a name="using-azure-policy"></a>Azure Policy の使用

Azure Policy を使用して、Cosmos DB の Advanced Threat Protection を有効にします。

1. Azure の **ポリシー - 定義** ページを開き、**Deploy Advanced Threat Protection for Cosmos DB\(Cosmos DB の Azure Advanced Threat Protection をデプロイする\)** ポリシーを探します。

    ![ポリシーを探す](./media/cosmos-db-advanced-threat-protection/cosmos-db.png) 

1. **[Deploy Advanced Threat Protection for CosmosDB]\(Cosmos DB の Azure Advanced Threat Protection をデプロイする\)** ポリシーをクリックし、 **[割り当て]** をクリックします。

    ![サブスクリプションまたはグループを選択する](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png)


1. **[スコープ]** フィールドから 3 つのドットをクリックし、Azure サブスクリプションまたはリソース グループを選択し、 **[選択]** をクリックします。

    ![[ポリシー定義] ページ](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png)


1. その他のパラメーターを入力し、 **[割り当て]** をクリックします。

## <a name="manage-atp-security-alerts"></a>ATP セキュリティ アラートの管理

Azure Cosmos DB のアクティビティで異常が発生すると、セキュリティ アラートがトリガーされて不審なセキュリティ イベントに関する情報が表示されます。 

 Azure Security Center で、現在の[セキュリティ アラート](../security-center/security-center-alerts-overview.md)を確認して管理することができます。  [Security Center](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) で特定のアラートをクリックすると、考えられる原因および潜在的な脅威を調査して緩和するための推奨アクションが表示されます。 次の図は、Security Center に表示されるアラートの詳細の例を示しています。

 ![脅威の詳細](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

アラートの詳細と推奨アクションを示すメール通知も送信されます。 次の図はアラート メールの例を示しています。

 ![[アラートの詳細]](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>Cosmos DB ATP のアラート

 Azure Cosmos DB アカウントの監視中に生成されるアラートの一覧については、Security Center のドキュメントで [Cosmos DB のアラート](../security-center/security-center-alerts-data-services.md#cosmos-db)に関するセクションを参照してください。

## <a name="next-steps"></a>次の手順

* [Azure Cosmos DB の診断ログ](https://docs.microsoft.com/azure/cosmos-db/logging#turn-on-logging-in-the-azure-portal)の詳細を確認します
* [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) の詳細について参照してください
