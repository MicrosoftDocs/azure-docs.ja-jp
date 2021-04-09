---
title: データの検出と分類
description: Azure SQL Database、Azure SQL Managed Instance、および Azure Synapse Analytics のデータの検出と分類
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=1
titleSuffix: Azure SQL Database, Azure SQL Managed Instance, and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/17/2021
tags: azure-synapse
ms.openlocfilehash: f47303a953dbb06b4c9efdfbbae41715cad81d2a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101690806"
---
# <a name="data-discovery--classification"></a>データの検出と分類
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

データの検出と分類は、Azure SQL Database、Azure SQL Managed Instance、および Azure Synapse Analytics に組み込まれています。 これにより、データベース内の機密データの検出、分類、ラベル付け、およびレポート作成を行うための基本的な機能が提供されます。

最も機密性の高いデータには、ビジネス、財務、医療、個人情報などが含まれる場合があります。 このようなデータの検出と分類は、組織の情報保護アプローチにおいて極めて重要な役割を果たすことができます。 次のような場合にインフラストラクチャとして使用できます。

- データのプライバシーと規制コンプライアンスの要件への基準を満たすための支援。
- さまざまなセキュリティ シナリオ (機密データに対するアクセスの監視 (監査) など)。
- 機密性の高いデータを含むデータベースへのアクセスの制御と、セキュリティの強化。

> [!NOTE]
> オンプレミスの SQL Server については、「[SQL データの検出と分類](/sql/relational-databases/security/sql-data-discovery-and-classification)」をご覧ください。

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>データの検出と分類とは

データの検出と分類には、Azure の一連の基本的なサービスと新機能が導入されています。 これにより、データベースだけでなくデータの保護も目的として、SQL Database、SQL Managed Instance、および Azure Synapse の新しい情報保護パラダイムが形成されます。 このパラダイムには次のものが含まれます。

- **検出と推奨事項:** 分類エンジンは、データベースをスキャンして、機密データが含まれる可能性のある列を識別します。 その後、Azure portal を使って、推奨される分類を確認して適用するための簡単な方法を提供します。

- **ラベル付け:** SQL Server データベース エンジンに追加された新しいメタデータ属性を使用して、機密度の分類ラベルを列に永続的に適用できます。 このメタデータは、機密度に基づく監査と保護のシナリオに利用できます。

- **クエリ結果セットの機密度:** 監査のため、クエリ結果セットの機密度がリアルタイムで計算されます。

- **可視性:** データベースの分類状態は、Azure portal の詳細ダッシュボードで見ることができます。 また、コンプライアンスと監査の目的や他のニーズのために使用される Excel 形式のレポートをダウンロードできます。

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>機密性の高い列の検出、分類、ラベル付け

このセクションでは、次の手順について説明します。

- データベース内の機密データを含む列の検出、分類、ラベル付け。
- データベースの現在の分類状態の表示と、レポートのエクスポート。

分類には、次の 2 つのメタデータ属性が含まれます。

- **ラベル**:列に格納されるデータの機密レベルを定義するために使用される、主な分類属性。  
- **情報の種類**:列に格納されているデータの型に関するより詳細な情報を提供する属性。

### <a name="define-and-customize-your-classification-taxonomy"></a>分類法を定義してカスタマイズする

データの検出と分類には、組み込みの機密ラベル セット、および組み込みの情報の種類と検出ロジック セットが付属しています。 この分類法をカスタマイズし、一連の分類コンストラクトとその優先度をお使いの環境に合わせて定義できるようになりました。

Azure 組織全体の分類法の定義とカスタマイズは 1 か所で行われます。 この処理は、[Azure Security Center](../../security-center/security-center-introduction.md) で、セキュリティ ポリシーの一環として実施されます。 このタスクは、組織のルート管理グループの管理者権限を持つユーザーのみが実行できます。

情報保護のポリシー管理の一環として、カスタム ラベルを定義し、優先度を設定し、選択した一連の情報の種類に関連付けることができます。 独自のカスタム情報の種類を追加し、文字列パターンで構成することもできます。 パターンは、データベース内のこの種類のデータを識別するための検出ロジックに追加されます。

詳細については、「[Azure Security Center 上で SQL 情報保護ポリシーをカスタマイズする (プレビュー)](../../security-center/security-center-info-protection-policy.md)」を参照してください。

組織全体のポリシーを定義したら、カスタマイズしたポリシーを使用して個別のデータベースの分類を続行できます。

### <a name="classify-your-database"></a>データベースを分類する

> [!NOTE]
> 次の例では Azure SQL Database を使用しますが、データの検出と分類を構成する適切な製品を選択する必要があります。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。

1. [Azure SQL Database] ペインの **[セキュリティ]** 見出しの下の **[データの検出と分類]** に移動します。 [概要] タブには、データベースの現在の分類状態の概要が表示されます。 この概要には、分類済みのすべての列の詳細なリストが含まれ、これをフィルター処理して、特定のスキーマの部分、情報の種類、ラベルのみを表示することもできます。 まだどの列も分類していない場合は、[手順 4. に進みます](#step-4)。

    ![概要](./media/data-discovery-and-classification-overview/data-discovery-and-classification.png)

1. Excel 形式でレポートをダウンロードするには、ペイン上部のメニューの **[エクスポート]** を選択します。

1. <a id="step-4"></a>データの分類を開始するには、 **[データの検出と分類]** ページの **[分類]** タブを選択します。

    分類エンジンによって、機密データが含まれる可能性のある列についてデータベースがスキャンされ、推奨される列の分類のリストが提供されます。

1. 分類の推奨事項を表示して適用します。

   - 推奨される列の分類のリストを表示するには、ペインの下部にある推奨事項パネルを選択します。

   - 特定の列の推奨事項を受け入れるには、関連する行の左側の列のチェック ボックスをオンにします。 推奨事項テーブル ヘッダーの左端のチェック ボックスをオンにして、すべての推奨事項を受け入れ済みとしてマークすることもできます。

   - 選択した推奨事項を適用するには、 **[選択した推奨事項を受け入れます]** を選択します。

   ![分類に関する推奨事項](./media/data-discovery-and-classification-overview/recommendation.png)

1. 代わりに、列を手動で分類することもできます。さらに、推奨事項ベースの分類について、次の操作を実行することもできます。

   1. ペインの上部のメニューで **[分類の追加]** を選択します。

   1. 表示されたコンテキスト ウィンドウで、分類するスキーマ、テーブル、列と、情報の種類および機密ラベルを選択します。

   1. コンテキスト ウィンドウの下部にある **[分類の追加]** をクリックします。

   ![分類を手動で追加する](./media/data-discovery-and-classification-overview/manually-add-classification.png)


1. 分類を完了し、新しい分類メタデータでデータベース列に永続的にラベル (タグ) を付けるには、 **[分類]** ページで **[保存]** を選択します。

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>機密データへのアクセスを監査する

機密データへのアクセスを監視できることは、情報保護パラダイムの重要な要素の 1 つです。 [Azure SQL Auditing](../../azure-sql/database/auditing-overview.md) は拡張され、`data_sensitivity_information` という新しいフィールドが監査ログに追加されました。 このフィールドには、クエリーによって返されたデータの機密度の分類 (ラベル) が記録されます。 次に例を示します。

![監査ログ](./media/data-discovery-and-classification-overview/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>アクセス許可

次の組み込みロールでは、データベースのデータ分類を読み取ることができます。

- 所有者
- Reader
- Contributor
- SQL Security Manager
- User Access Administrator

次の組み込みロールでは、データベースのデータ分類を変更できます。

- 所有者
- Contributor
- SQL Security Manager

ロールベースのアクセス許可の詳細については、[Azure RBAC](../../role-based-access-control/overview.md) に関する記事を参照してください。

## <a name="manage-classifications"></a><a id="manage-classification"></a>分類の管理

分類の管理には、T-SQL、REST API、または PowerShell を使用できます。

### <a name="use-t-sql"></a>T-SQL の使用

T-SQL を使って、列の分類を追加または削除したり、データベース全体のすべての分類を取得したりできます。

> [!NOTE]
> T-SQL を使ってラベルを管理するときに、列に追加するラベルが組織の情報保護ポリシー (ポータルの推奨事項に表示されるラベルのセット) に存在することの検証は行われません。 そのため、この検証はユーザーが行う必要があります。

T-SQL を使用した分類の詳細については、次のリファレンスを参照してください。

- 1 つ以上の列の分類を追加または更新するには:[ADD SENSITIVITY CLASSIFICATION](/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- 1 つ以上の列の分類を削除するには:[DROP SENSITIVITY CLASSIFICATION](/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- データベースのすべての分類を表示するには: [sys.sensitivity_classifications](/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-powershell-cmdlets"></a>PowerShell コマンドレットの使用
PowerShell を使用して、Azure SQL Database と Azure SQL Managed Instance の分類および推奨事項を管理します。

#### <a name="powershell-cmdlets-for-azure-sql-database"></a>Azure SQL Database 用の PowerShell コマンドレット

- [Get-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Enable-AzSqlDatabaSesensitivityRecommendation](/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Disable-AzSqlDatabaseSensitivityRecommendation](/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance 用の PowerShell コマンドレット

- [Get-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Enable-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Disable-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

### <a name="use-the-rest-api"></a>REST API を使用する

REST API を使用して、分類および推奨事項をプログラムで管理できます。 公開された REST API では、次の操作がサポートされます。

- [作成または更新](/rest/api/sql/sensitivitylabels/createorupdate):指定された列の機密ラベルを作成または更新します。
- [[削除]](/rest/api/sql/sensitivitylabels/delete):指定された列の機密ラベルを削除します。
- [推奨の無効化](/rest/api/sql/sensitivitylabels/disablerecommendation):指定された列の機密度の推奨を無効にします。
- [推奨の有効化](/rest/api/sql/sensitivitylabels/enablerecommendation):指定された列の機密度の推奨を有効にします。 (推奨は、すべての列に対して既定で有効になっています。)
- [取得](/rest/api/sql/sensitivitylabels/get):指定された列の機密ラベルを取得します。
- [現在の内容をデータベース別に一覧表示](/rest/api/sql/sensitivitylabels/listcurrentbydatabase):指定されたデータベースの現在の機密ラベルを取得します。
- [推奨される内容をデータベース別に一覧表示](/rest/api/sql/sensitivitylabels/listrecommendedbydatabase):指定されたデータベースの推奨される機密ラベルを取得します。


## <a name="faq---advanced-classification-capabilities"></a>FAQ - 高度な分類機能

**質問**: SQL データの検出と分類は [Azure Purview](../../purview/overview.md) によって置き換えられますか、または SQL データの検出と分類は間もなく廃止されますか?
**回答**: SQL データ検出と分類は引き続きサポートされます。また、高度な分類機能とデータ ガバナンスを推進するための、より豊富な機能を備えた [Azure Purview](../../purview/overview.md) を導入することをお勧めします。 サービス、機能、API、または SKU の廃止が決定された場合は、移行または切り替えパスを含む事前通知がお客様に送信されます。 Microsoft ライフサイクル ポリシーの詳細については、こちらをご覧ください。


## <a name="next-steps"></a><a id="next-steps"></a>次のステップ

- 分類済みの機密データへのアクセスを監視および監査するように [Azure SQL Auditing](../../azure-sql/database/auditing-overview.md) を構成することを検討します。
- データの検出と分類に関するプレゼンテーションについては、「[SQL データの検出、分類、ラベル付け、保護 | | Data Exposed](https://www.youtube.com/watch?v=itVi9bkJUNc)」を参照してください。
- T-SQL コマンドを使用し、Azure SQL Databases と Azure Synapse Analytics を Azure Purview ラベルで分類する方法については、「[Azure Purview ラベルを使用して Azure SQL データを分類する](../../sql-database/scripts/sql-database-import-purview-labels.md)」を参照してください。