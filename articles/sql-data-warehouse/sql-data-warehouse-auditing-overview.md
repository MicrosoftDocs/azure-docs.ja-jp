---
title: "Azure SQL Data Warehouse での監査 | Microsoft Docs"
description: "Azure SQL Data Warehouse での監査の概要"
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: jhubbard
editor: 
ms.assetid: 0e6af148-b218-4b43-bb5f-907917d20330
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: rortloff;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bd892d5f15a8d95664ef0666cd35e434e773bbce


---
# <a name="auditing-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse での監査
> [!div class="op_single_selector"]
> * [監査](sql-data-warehouse-auditing-overview.md)
> * [脅威の検出](sql-data-warehouse-security-threat-detection.md)
> 
> 

SQL Data Warehouse の監査により、Azure Storage アカウントの監査ログにデータベースのイベントを記録できます。 監査により、規定遵守の維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違犯の疑いを示す差異や異常に対する洞察が容易になります。 また SQL Data Warehouse の監査を Microsoft Power BI と統合することにより、詳細なレポートと分析が容易になります。

監査ツールは、標準準拠を強化し促進しますが、準拠を保証するものではありません。 標準準拠をサポートする Azure プログラムの詳細については、「<a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Microsoft Azure のトラスト センター</a>」を参照してください。

* [データベース監査の基本]
* [データベースに対する監査を設定する]
* [監査ログとレポートを分析する]

## <a name="a-idsubheading-1aazure-sql-data-warehouse-database-auditing-basics"></a><a id="subheading-1"></a>Azure SQL Data Warehouse データベース監査の基本
SQL Data Warehouse データベース監査により、以下のことが可能になります。

* **保持** 。 監査するデータベース活動のカテゴリを定義できます。
* **レポート** 。 事前に構成したレポートとダッシュボードを使用して、活動とイベントのレポートをすぐに使用できます。
* **分析** 。 疑わしいイベント、異常な活動、および傾向を発見できます。

次のイベント カテゴリの監査を構成できます。

収集された監査ログが次のように分類される**プレーンな SQL** および**パラメーター化された SQL**  

* **データへのアクセス**
* **スキーマの変更 (DDL)**
* **データの変更 (DML)**
* **アカウント、ロール、アクセス許可 (DCL)**
* **ストアド プロシージャ**、**ログイン**、**トランザクション管理**。

各イベント カテゴリに対し、**成功**および**失敗**した操作の監査が個別に構成されます。

監査対象の活動やイベントの詳細については、<a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">監査ログ形式のリファレンス (doc ファイルのダウンロード)</a> を参照してください。

監査ログは、Azure ストレージ アカウントに格納されます。 監査ログの保有期間を定義することができます。

特定のデータベースに対して、または既定のサーバー ポリシーとして、監査ポリシーを定義できます。 既定のサーバー監査ポリシーは、特定のオーバーライド データベース監査ポリシーが定義されていないサーバー上のすべてのデータベースに適用されます。

監査をセットアップする前に、 ["ダウンレベル クライアント"](sql-data-warehouse-auditing-downlevel-clients.md)を使用しているかどうかを確認します。

## <a name="a-idsubheading-2aset-up-auditing-for-your-database"></a><a id="subheading-2"></a>データベースに対する監査を設定する
1. <a href="https://portal.azure.com" target="_blank">Azure ポータル</a>を開きます。
2. 監査する SQL Data Warehouse データベース/SQL Server の構成ブレードに移動します。 上部の **[設定]** ボタンをクリックし、[設定] ブレードで **[監査]** を選択します。
   
    ![][1]
3. 監査構成ブレードで、最初に **[監査設定をサーバーから継承]** チェック ボックスをオフにします。 これにより、特定のデータベースの設定を指定できます。
   
    ![][2]
4. 次に、 **[オン]** ボタンをクリックして監査を有効にします。
   
    ![][3]
5. 監査構成ブレードで、 **[ストレージの詳細]** を選択して [監査ログ ストレージ] ブレードを選択します。 ログを保存する Azure ストレージ アカウントを選択し、保持期間を選択します。 **ヒント:** すべての監査済みデータベースに同じストレージ アカウントを使用して、事前に構成したレポートのテンプレートを活用します。
   
    ![][4]
6. **[OK]** ボタンをクリックして、記憶域の詳細構成を保存します。
7. **[イベントごとのログ記録]** で、**[成功]** および **[失敗]** を、すべてのイベントについて、または個別のイベント カテゴリについて記録します。
8. データベースの監査を構成している場合は、データの監査が正しくキャプチャされるように、クライアントの接続文字列を変更する必要がある場合があります。 ダウンレベル クライアントの接続については、 [接続文字列でのサーバーの FDQN の変更](sql-data-warehouse-auditing-downlevel-clients.md) に関するトピックを参照してください。
9. **[OK]**をクリックします。

## <a name="a-idsubheading-3analyze-audit-logs-and-reportsa"></a><a id="subheading-3">監査ログとレポートを分析する</a>
監査ログは、設定時に選択した Azure ストレージ アカウントで、 **SQLDBAuditLogs** というプレフィックスを使用してストア テーブルのコレクションに集計されます。 <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Azure ストレージ エクスプローラー</a>などのツールを使用してログ ファイルを表示できます。

事前に構成されたダッシュボード レポート テンプレートが<a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">ダウンロードできる Excel スプレッドシート</a>として用意されているので、ログ データをすぐに分析できます。 監査ログでテンプレートを使用するには、Excel 2013 またはそれ以降、および Power Query が必要です (<a href="http://www.microsoft.com/download/details.aspx?id=39379">ここ</a>でダウンロードできます)。

テンプレートでは架空のサンプル データを使用しています。Power Query を設定して Azure ストレージ アカウントから監査ログを直接インポートできます。

レポート テンプレートの使用手順の詳細については、<a href="http://go.microsoft.com/fwlink/?LinkId=506731">方法 (doc のダウンロード)</a> を参照してください。

![][5]

## <a name="a-idsubheading-4practices-for-usage-in-productiona"></a><a id="subheading-4">運用環境での使用手法</a>
このセクションでは、上のスクリーン キャプチャーについて説明します。 <a href="https://portal.azure.com" target="_blank">Azure ポータル</a>または<a href= "https://manage.windowsazure.com/" target="_bank">従来の Azure クラシック ポータル</a>を使用できます。

## <a name="a-idsubheading-5astorage-key-regeneration"></a><a id="subheading-5"></a>ストレージ キーの再生成
運用環境では、ストレージ キーを最新の情報に定期的に更新することが推奨されます。 鍵を最新の情報に更新する場合は、ポリシーを再度保存する必要があります。 このプロセスは次のとおりです。

1. 監査構成ブレードで (監査を設定する前セクションの説明を参照してください)、**[ストレージ アクセス キー]** を、*[プライマリ]* から *[セカンダリ]* に切り替え、**[保存]** をクリックします。
   ![][4]
2. ストレージ構成ブレードに移動し、 **プライマリ アクセス キー** を *再生成*します。
3. 監査構成ブレードに戻り、**[ストレージ アクセス キー]** を *[セカンダリ]* から *[プライマリ]* に切り替え、**[保存]** をクリックします。
4. ストレージの UI に戻り、 **セカンダリ アクセス キー** を *再生成* (次のキー更新サイクルの準備として) します。

## <a name="a-idsubheading-6aautomation"></a><a id="subheading-6"></a>Automation
Azure SQL Database で監査を構成する際、使用できる PowerShell コマンドレットがいくつかあります。 auditing コマンドレットにアクセスにするには、Azure リソース マネージャー モードで PowerShell を実行する必要があります。

> [!NOTE]
> [Azure リソース マネージャー](https://msdn.microsoft.com/library/dn654592.aspx) モジュールは、現在プレビュー段階にあります。 Azure モジュールと同じ管理機能を提供しないことがあります。
> 
> 

Azure リソース マネージャー モードを実行中に、 `Get-Command *AzureSql*` を実行して、使用できるコマンドレットの一覧を表示します。

<!--Anchors-->
[データベース監査の基本]: #subheading-1
[データベースに対する監査を設定する]: #subheading-2
[監査ログとレポートを分析する]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png


<!--Link references-->



<!--HONumber=Nov16_HO3-->


