---
title: Azure SQL Database および SQL Data Warehouse のデータの検出と分類 | Microsoft Docs
description: Azure SQL Database およびデータの検出と分類
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 08/22/2019
ms.openlocfilehash: b85793223e23aa3d668d6f86494da3ee78c43e91
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009985"
---
# <a name="azure-sql-database-and-sql-data-warehouse-data-discovery--classification"></a>Azure SQL Database および SQL Data Warehouse のデータの検出と分類

データの検出と分類では、Azure SQL Database に組み込まれる、データベースの機微なデータの**検出**、**分類**、**ラベル付け** & **保護**を行う高度な機能が用意されます。

最も機微なデータの検出と分類 (ビジネス/金融、医療、個人を特定できる情報 (PII) など) は、組織の情報保護水準において極めて重要な役割を果たします。 これは、以下のケースのインフラストラクチャとして機能します。

- データのプライバシー基準および規制のコンプライアンス要件を満たす支援。
- さまざまなセキュリティ シナリオ (機微なデータに対する異常なアクセスの監視 (監査) とアラートなど)。
- 非常に機微なデータを含むデータベースへのアクセスの制御と、セキュリティの強化。

データの検出と分類は、高度な SQL セキュリティ機能の統合パッケージである [Advanced Data Security](sql-database-advanced-data-security.md) (ADS) オファリングの一部です。 データの検出と分類は、SQL ADS ポータルを使って一元的にアクセスおよび管理できます。

> [!NOTE]
> このドキュメントは、Azure SQL Database と Azure SQL Data Warehouse に関するものです。 わかりやすいように、SQL Database という言葉で SQL Database と SQL Data Warehouse の両方を言い表します。 SQL Server (オンプレミス) については、「[SQL Data Discovery and Classification (SQL のデータの検出と分類)](https://go.microsoft.com/fwlink/?linkid=866999)」をご覧ください。

## <a id="subheading-1"></a>データの検出と分類とは

データの検出と分類では、一連の高度なサービスと新しい SQL 機能が導入され、データベースだけでなくデータの保護も目的とする新しい SQL Information Protection パラダイムが形成されます。

- **検出と推奨事項**

  分類エンジンは、データベースをスキャンして、機密データが含まれる可能性のある列を識別します。 その後、Azure Portal を使ってレビューし、適切な分類の推奨事項を適用するための簡単な方法を提供します。

- **ラベル付け**

  機密度分類ラベルは、SQL エンジンに導入された新しい分類メタデータ属性を使って、列に永続的にタグを付けることができます。 このメタデータは、機密度に基づく高度な監査と保護のシナリオに利用できます。

- **クエリ結果セットの機密度**

  監査のため、クエリ結果セットの機密度がリアルタイムで計算されます。

- **表示**

  データベースの分類状態は、ポータルの詳細ダッシュボード上で見ることができます。 さらに、コンプライアンスと監査の目的や他のニーズに使われる Excel 形式のレポートをダウンロードできます。

## <a id="subheading-2"></a>機密性の高い列の検出、分類、ラベル付け

次のセクションでは、データベースの機密データを含む列を検出、分類、ラベル付けする手順、およびデータベースの現在の分類状態を表示してレポートをエクスポートする手順について説明します。

分類には 2 つのメタデータ属性が含まれます。

- ラベル – メインの分類属性であり、列に格納されているデータの機密度レベルを定義するために使われます。  
- 情報の種類 – 列に格納されているデータの種類に対する追加の細分性を提供します。

## <a name="define-and-customize-your-classification-taxonomy"></a>分類法を定義してカスタマイズする

SQL データの検出と分類には、組み込みの機密ラベル セット、および組み込みの情報の種類と検出ロジック セットが付属しています。 この分類法をカスタマイズし、一連の分類コンストラクトとその優先度を、ご自身の環境に合わせて定義できるようになりました。

Azure テナント全体の分類法の定義とカスタマイズは 1 か所で行われます。 この処理は、[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) で、セキュリティ ポリシーの一環として実施されます。 このタスクは、テナントのルート管理グループの管理者権限を持つユーザーのみが実行できます。

Information Protection ポリシー管理の一環として、カスタム ラベルを定義し、優先度を設定し、そのラベルを、選択した一連の情報の種類に関連付けることができます。 独自のカスタム情報の種類を追加し、文字列パターンで構成することもできます。これは、ご自身のデータベース内のこの種類のデータを特定するために検出ロジックに追加されます。
ご自身のポリシーのカスタマイズと管理の詳細については、[Information Protection ポリシーの攻略ガイド](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409)に関するページをご覧ください。

テナント全体のポリシーが定義されたら、カスタマイズしたポリシーを使用して個別のデータベースの分類を続行できます。

## <a name="classify-your-sql-database"></a>SQL Database を分類する

1. [Azure ポータル](https://portal.azure.com)にアクセスします。

2. [Azure SQL Database] ウィンドウの [セキュリティ] で、 **[Advanced Data Security]** に移動します。 [Advanced Data Security を有効にします] をクリックして、 **[データの検出と分類]** カードをクリックします。

   ![データベースのスキャン](./media/sql-data-discovery-and-classification/data_classification.png)

3. **[概要]** タブには、データベースの現在の分類状態の概要が表示されます。これには分類済みのすべての列の詳細な一覧が含まれ、これをフィルター処理して、特定のスキーマの部分、情報の種類、ラベルだけを表示することもできます。 まだどの列も分類していない場合は、[手順 5 に進みます](#step-5)。

   ![現在の分類状態の概要](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Excel 形式でレポートをダウンロードするには、ウィンドウ上部のメニューの **[エクスポート]** オプションをクリックします。

   ![Excel へのエクスポート](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png)

5. <a id="step-5"></a>データの分類を始めるには、ウィンドウの上部にある **[分類]** タブをクリックします。

    ![データを分類する](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png)

6. 分類エンジンがデータベースで機密データを含む可能性のある列をスキャンし、**推奨される列の分類**のリストを提供します。 分類の推奨事項を表示して適用するには:

   - 推奨される列の分類のリストを表示するには、ウィンドウの下部にある推奨事項パネルをクリックします。

      ![データを分類する](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png)

   - 推奨事項の一覧を確認し、特定の列に対する推奨事項を受け入れるには、該当する行の左側にあるチェック ボックスをオンにします。 推奨テーブルのヘッダーのチェック ボックスをオンにして、"*すべての推奨事項*" を受け入れるようにマークすることもできます。

       ![推奨事項の一覧を確認する](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - 選んだ推奨事項を適用するには、青い **[Accept selected recommendations]\(選択した推奨事項を受け入れる\)** ボタンをクリックします。

      ![推奨事項の適用](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png)

7. また、推奨事項に基づく分類の代わりに、または推奨事項に基づく分類に加えて、列を**手動で分類**することもできます。

   - ウィンドウの上部メニューの **[Add classification]\(分類の追加\)** をクリックします。

      ![分類を手動で追加する](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png)

   - 表示されるコンテキスト ウィンドウで、分類するスキーマ > テーブル > 列、情報の種類、機密度ラベルを選びます。 コンテキスト ウィンドウの下部にある青い **[Add classification]\(分類の追加\)** ボタンをクリックします。

      ![分類する列を選択する](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. 分類を完了し、新しい分類メタデータでデータベースの列に永続的にラベル (タグ) を付けるには、ウィンドウの上部メニューの **[保存]** をクリックします。

   ![保存](./media/sql-data-discovery-and-classification/10_data_classification_save.png)

## <a id="subheading-3"></a>機密データへのアクセスの監査

機密データへのアクセスを監視できることは、情報保護パラダイムの重要な要素の 1 つです。 [Azure SQL Database Auditing](sql-database-auditing.md) が拡張され、監査ログに *data_sensitivity_information* という名前の新しいフィールドが追加されています。このフィールドには、クエリによって返された実際のデータの重大度の分類 (ラベル) が記録されます。

![監査ログ](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a id="subheading-4"></a>T-SQL を使用してデータ分類を管理する

T-SQL を使って、列の分類を追加/削除し、データベース全体のすべての分類を取得することができます。

> [!NOTE]
> T-SQL を使ってラベルを管理するとき、列に追加されるラベルが組織の情報保護ポリシー (ポータルのレコメンデーションに表示されるラベルのセット) に存在することの検証は行われません。 したがって、その検証はユーザーが行う必要があります。

- 1 つ以上の列の分類の追加/更新:[ADD SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- 1 つ以上の列の分類の削除:[DROP SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- データベースのすべての分類の表示: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="manage-classifications-using-rest-apis"></a>Rest API を使用して分類を管理する

REST API を使用して、分類をプログラムで管理することもできます。 公開された REST API は、次の操作をサポートします。

- [作成または更新](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) - 指定された列の機密ラベルを作成または更新します
- [削除](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete): 指定された列の機密ラベルを削除します
- [推奨の無効化](https://docs.microsoft.com/en-us/rest/api/sql/sensitivitylabels/disablerecommendation) - 指定された列に対する秘密度の推奨を無効にします
- [推奨の有効化](https://docs.microsoft.com/en-us/rest/api/sql/sensitivitylabels/enablerecommendation) - 指定された列に対する秘密度の推奨を有効にします (既定では、すべての列で推奨事項が有効になっています)
- [取得](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get): 指定された列の機密ラベルを取得します
- [現在の内容をデータベース別に一覧表示](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase) - 指定されたデータベースの現在の機密ラベルを取得します

- [推奨される内容をデータベース別に一覧表示](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) - 指定されたデータベースの推奨される機密ラベルを取得します

## <a name="manage-data-discovery-and-classification-using-azure-powershell"></a>Azure PowerShell を使用してデータの検出と分類を管理する

PowerShell を使用して、Azure SQL データベースおよびマネージド インスタンスで推奨されるすべての列を取得できます。

### <a name="powershell-cmdlets-for-azure-sql-database"></a>Azure SQL データベース用の PowerShell コマンドレット

- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)

### <a name="powershell-cmdlets-for-managed-instance"></a>マネージド インスタンス用の PowerShell コマンドレット

- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)

## <a name="permissions"></a>アクセス許可

`Owner`、`Reader`、`Contributor`、`SQL Security Manager`、`User Access Administrator` の組み込みロールでは Azure SQL データベースのデータ分類を読み取ることができます。

`Owner`、`Contributor`、`SQL Security Manager` の組み込みロールでは Azure SQL データベースのデータ分類を変更することができます。

[Azure リソースの RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)の詳細はこちら

## <a id="subheading-5"></a>次のステップ

- [Advanced Data Security](sql-database-advanced-data-security.md) の詳細について学習します。
- 分類済みの機密データへのアクセスを監視および監査するように [Azure SQL Database Auditing](sql-database-auditing.md) を構成することを検討します。

<!--Anchors-->
[SQL data discovery & classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Automated/Programmatic classification]: #subheading-4
[Next Steps]: #subheading-5
