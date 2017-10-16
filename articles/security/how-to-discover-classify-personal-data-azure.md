---
title: "Microsoft Azure で個人データを検出、特定、分類する | Microsoft Docs"
description: "データの検索、分類、検出、および特定について説明します"
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 6ccb064a9a76e7041d4f365b3997673dc9182e0b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="discover-identify-and-classify-personal-data-in-microsoft-azure"></a>Microsoft Azure で個人データを検出、特定、分類する

この記事では、Azure Data Catalog、Azure Active Directory、SQL Database、Azure HDInsight の Hadoop クラスター用 Power Query、Azure Information Protection、Azure Search、Azure Cosmos DB 用の SQL クエリなど、複数の Azure ツールおよびサービスで、個人データを検出、特定、分類する方法についてのガイダンスを提供します。

## <a name="scenario-problem-statement-and-goal"></a>シナリオ、問題の説明、および目標

米国に本拠を置くスポーツ会社が、個人データを含め、さまざまなデータを収集しています。 これには、顧客や従業員のデータも含まれます。 こうしたデータは複数のデータベースに保持され、Azure 環境の複数の場所に格納されています。 同社はスポーツ機器の販売だけでなく、EU を含め、世界の一流アスリートを対象としたスポーツ イベントの主催や登録管理を手掛けており、収集する顧客データには医療情報が含まれることがあります。

毎年多数の国際サイクリング ツアーを主催し、世界各地の派遣スタッフを採用しているため、データ セットのいくつかは極めて膨大です。 さらに、開発者作成のアプリケーションもあり、顧客と従業員の両方が利用しています。

同社は次の問題に対処する必要があります。

- 適切なアクセスとセキュリティを確保するために、顧客および従業員の個人データを分類し、他の収集データと区別しなければなりません。
- データ管理者が、Azure 環境のさまざまな領域にまたがる顧客の個人データの場所を容易に検出する必要があります。
- 共有ドキュメントや電子メール通信に表示される顧客および従業員の個人データの安全性を確保するために、こうしたデータにラベルを付けなければなりません。
- 社内のアプリ開発者に、自身の Web アプリおよびモバイル アプリで顧客や従業員の個人データを簡単に検索する手段が必要です。
- さらに、開発者が、自身のドキュメント データベースで、個人データに関するクエリを実行する必要があります。

### <a name="company-goals"></a>会社の目標

- すべての顧客および従業員の個人データに Azure Data Catalog でタグ/注釈を付けて、簡単に見つけられるようにします。 顧客と従業員の個人データは切り離してタグ/注釈を付けるのが理想です。
- Azure Active Directory に存在する勤務情報と顧客と従業員のユーザー プロファイルの個人データを、簡単に特定できる必要があります。
- 複数の SQL データベースにある個人データに対して、簡単にクエリを実行できる必要があります。 
- 会社の大規模なデータ セットの中には、Azure HDInsight を通じて管理され、Hadoop に格納されているものがあります。 こうしたデータ セットは、個人データにクエリを実行できるように、Excel にインポートする必要があります。
- ドキュメントと電子メール通信で共有される個人データは、Azure Information Protection で分類、ラベル付け、およびセキュリティ保護する必要があります。
- 会社のアプリ開発者は、自身で開発したアプリで、顧客および従業員の個人データを検出できなければなりません。これは、Azure Search で行うことができます。
- 開発者は、自身のドキュメント データベースで、個人データを検索できなければなりません。

## <a name="azure-active-directory-data-discovery"></a>Azure Active Directory: データ検出

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) は、マイクロソフトが提供する、クラウドベースの、マルチテナント対応ディレクトリおよび ID 管理サービスです。 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) (AAD) 環境では、[Azure Portal](https://portal.azure.com/) を使用して、個人データを含む顧客および従業員のユーザー プロファイルとユーザーの勤務情報を見つけることができます。

これは、特定のユーザーの個人データを検索または変更したい場合に特に便利です。 また、ユーザー プロファイルと勤務情報を追加または変更することもできます。 サインインするときは、ディレクトリの全体管理者であるアカウントを使用する必要があります。

### <a name="how-do-i-locate-or-view-user-profile-and-work-information"></a>ユーザー プロファイルと勤務情報を検索および表示する方法

1. ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。

2. **[その他のサービス]** を選択し、テキスト ボックスに「**ユーザーとグループ**」と入力して、**Enter** キーを押します。

   ![ユーザー プロファイルと勤務情報を検索する方法](media/how-to-discover-classify-personal-data-azure/user-profile.png)

3. **[ユーザーとグループ]** ブレードで、**[ユーザー]** を選択します。

  ![ユーザーとグループを開く](media/how-to-discover-classify-personal-data-azure/users-groups.png)

4. **[ユーザーとグループ - ユーザー]** ブレードで、一覧からユーザーを選択し、選択したユーザーのブレードで **[プロファイル]** を選択して、個人データが含まれる可能性があるユーザー プロファイル情報を表示します。

  ![select user](media/how-to-discover-classify-personal-data-azure/select-user.png)

5. ユーザー プロファイル情報を追加または変更する必要がある場合は、追加または変更してから、コマンド バーの **[保存]** をクリックします。
6. 選択したユーザーのブレードで **[ワーク情報]** を選択して、個人データが含まれる可能性のあるユーザーの勤務情報を表示します。

 ![勤務情報の表示](media/how-to-discover-classify-personal-data-azure/work-info.png)

7. ユーザーの勤務情報を追加または変更する必要がある場合は、追加または変更してから、コマンド バーの **[保存]** をクリックします。

## <a name="azure-sql-database-data-discovery"></a>Azure SQL Database: データ検出

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) は、開発者がアプリケーションを作成および管理するときに便利なクラウド データベースです。 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) で個人データを見つけるには、標準の SQL クエリを使用します。 Azure SQL エラスティック クエリ (プレビュー) では、ユーザーが複数データベース間でクエリを実行できます。

詳細な [SQL データベース](../sql-database/sql-database-technical-overview.md) チュートリアルでは、SQL データベースの作成方法、データ クエリの実行方法など、SQL データベースの使用についてさまざまな側面から説明します。 次に示すのは、チュートリアルで使用できる情報の概要です。また、特定のセクションへのリンクも含まれています。

### <a name="how-do-i-build-a-sql-database"></a>SQL データベースを作成する方法

3 つの方法があります。

- [Azure Portal](https://portal.azure.com/) で Azure SQL データベースを作成できます。 このチュートリアルでは、リソース グループおよび論理サーバー内で、特定のコンピューティング リソースとストレージ リソースのセットを使用します。 使用するのは AdventureWorks という架空の会社のサンプル データです。 サーバー レベルのファイアウォール規則も作成します。 これを行う方法を確認するには、「[Azure Portal で Azure SQL データベースを作成する](../sql-database/sql-database-get-started-portal.md)」チュートリアルにアクセスしてください。

  ![Azure SQL Database を作成する](media/how-to-discover-classify-personal-data-azure/create-database.png)
- ブラウザー ベースのコマンド ライン ツール、[Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) CLI で、SQL データベースを作成することもできます。 ツールは Azure Portal で使用でき、そこから直接実行できます。 このチュートリアルでは、ツールを起動し、スクリプト変数を定義した後、リソース グループと論理サーバーを作成し、サーバーのファイアウォール規則を構成します。 その後、サンプル データを使用して、データベースを作成します。 この方法でデータベースを作成するには、「[Azure CLI で Azure SQL データベースを作成する](../sql-database/sql-database-get-started-cli.md)」チュートリアルにアクセスしてください。

  ![CLIT チュートリアル](media/how-to-discover-classify-personal-data-azure/cli-tutorial.png)

>[!NOTE]
Azure CLI は、Linux の管理者と開発者がよく使用します。 一部のユーザーにとっては、3 番目のオプションである PowerShell よりも簡単かつ直観的です。

- 最後は PowerShell を使って SQL データベースを作成する方法です。これは、Azure やその他のリソースを作成して管理する際に使用するコマンド ライン/スクリプト ツールです。 このチュートリアルでは、ツールを起動し、スクリプト変数を定義した後、リソース グループと論理サーバーを作成し、サーバーのファイアウォール規則を構成します。 その後、サンプル データを使用して、データベースを作成します。

チュートリアルには、Azure PowerShell モジュール バージョン 4.0 以降が必要です。 使用しているバージョンを確認するには、Get-Module -ListAvailable AzureRM を実行します。 インストールまたはアップグレードする必要がある場合は、Azure PowerShell モジュールのインストールに関するページを参照してください。

```PowerShell
New-AzureRmSQLDatabase -ResourceGroupName $resourcegroupname `
-ServerName $servername `
-DatabaseName $databasename `
-RequestedServiceObjectiveName "s0"
```

この方法でデータベースを作成するには、「[PowerShell を使用して単一の Azure SQL データベースを作成する](../sql-database/sql-database-get-started-powershell.md)」チュートリアルにアクセスしてください。

>[!Note]
Windows 管理者は PowerShell を使用する傾向がありますが、人によっては Azure CLI を選ぶ場合もあります。

### <a name="how-do-i-search-for-personal-data-in-sql-database-in-the-azure-portal"></a>Azure Portal で SQL データベースの個人情報を検索する方法**

Azure Portal に組み込まれているクエリ エディター ツールを使用して、個人データを検索できます。 SQL サーバー管理者のログインとパスワードを使用してツールにログインし、クエリを入力します。

  ![ポータルを使用して SQL を検索する](media/how-to-discover-classify-personal-data-azure/search-sql-portal.png)

チュートリアルの手順 5. はクエリ エディター ウィンドウのサンプル クエリを示していますが、個人情報や機密情報には重点を置いていません (2 つのテーブルのデータが結合され、返されたデータ セットでソース列の別名も作成されます)。 次のスクリーン ショットは、手順 5. のクエリと、返された結果ペインを示しています。

  ![クエリ エディター](media/how-to-discover-classify-personal-data-azure/query-editor.png)

データベースが MyTable と呼ばれていた場合、個人情報のサンプル クエリは、名前、社会保障番号、ID 番号を含む場合があり、次のようになります。

"SELECT Name, SSN, ID number FROM MyTable"

クエリを実行すると、**結果**ペインに結果が表示されます。

Azure Portal で SQL データベースにクエリを実行する方法の詳細については、このチュートリアルの [SQL データベースのクエリ](../sql-database/sql-database-get-started-portal.md)に関するセクションをご覧ください。

### <a name="how-do-i-search-for-data-across-multiple-databases"></a>複数データベース間でデータを検索する方法

SQL エラスティック クエリ (プレビュー) を使用すると、複数データベース間および複数データベースでクエリを実行し、1 つの結果を返すことができます。 [チュートリアルの概要](../sql-database/sql-database-elastic-query-overview.md)に関するページでは、シナリオの詳細と、データベースの行方向のパーティション分割と列分割の違いについて説明します。 行方向のパーティション分割は、"シャーディング" と呼ばれます。

  ![垂直的パーティション分割](media/how-to-discover-classify-personal-data-azure/vertical-partition.png)

  ![行方向のパーティション分割](media/how-to-discover-classify-personal-data-azure/horizontal.png)

作業を開始するには、「[Azure SQL Database のエラスティック クエリの概要 (プレビュー)](../sql-database/sql-database-elastic-query-overview.md)」ページにアクセスしてください。

#### <a name="power-query-for-importing-azure-hdinsight-hadoop-clusters-data-discovery-for-large-data-sets"></a>Power Query (Azure HDInsight Hadoop クラスターのインポート用): 大規模データ セットのデータ検出

Hadoop は、Hadoop クラスターで分析および格納される、大規模データ セット用のオープン ソース Apache ストレージおよび処理サービスです。 [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) を使用すると、ユーザーが Azure で Hadoop クラスターを使用できます。 Power Query は Excel アドインで、特に、ユーザーがさまざまなソースからデータを検出するときに役立ちます。

Azure HDInsight で Hadoop クラスターに関連付けられている個人データは、Power Query で Excel にインポートできます。 Excel に取り込まれたデータは、クエリを使用して特定できます。

#### <a name="how-do-i-use-excel-power-query-to-import-hadoop-clusters-in-azure-hdinsight-into-excel"></a>Excel Power Query を使用して Azure HDInsight で Hadoop クラスターを Excel にインポートする方法

HDInsight チュートリアルで、このプロセス全体を説明します。 このチュートリアルは前提条件について説明しており、[Azure HDInsight の概要](../hdinsight/hdinsight-hadoop-linux-tutorial-get-started.md)チュートリアルへのリンクが含まれます。 説明は Excel 2016 だけでなく 2013 と 2010 にも適用されます (以前のバージョンの Excel では手順が少し異なります)。 Excel Power Query アドインをお持ちでない方は、チュートリアルで入手する方法を確認できます。 チュートリアルは Excel で開始します。また、クラスターに関連付けられている Azure Blob Storage アカウントが必要です。

  ![Excel でのクエリ](media/how-to-discover-classify-personal-data-azure/excel.png)

これを行う方法を確認するには、「[Power Query を使用した Excel から Hadoop への接続](../hdinsight/hdinsight-connect-excel-power-query.md)」チュートリアルにアクセスしてください。

ソース: [Power Query を使用した Excel から Hadoop への接続](../hdinsight/hdinsight-connect-excel-power-query.md)

## <a name="azure-information-protection-personal-data-classification-for-documents-and-email"></a>Azure Information Protection: ドキュメントおよび電子メール用の個人データの分類

[Azure Information Protection](https://www.microsoft.com/cloud-platform/azure-information-protection) は、Azure のお客様が、分類ラベルを適用し、内部または外部共有ドキュメントと電子メール通信を保護するときに役立ちます。 こうした項目の一部には、顧客や従業員の個人情報が含まれる可能性があります。 そこで、規則や条件を、管理者またはユーザーが自動または手動で定義できます。 たとえば、ユーザーがクレジット カード情報を含むドキュメントを保存する場合、そのユーザーには、管理者によって構成された推奨ラベルが表示されます。

### <a name="how-do-i-try-it"></a>操作方法

Azure Information Protection でラベルが組織に適合するかどうかを確認するには、[クイックスタート チュートリアル](https://docs.microsoft.com/information-protection/get-started/infoprotect-quick-start-tutorial)にアクセスしてください。 チュートリアルは、インストール、ポリシーの構成、分類の表示、ラベル付け、共有の 5 つの基本的な手順で構成され、30 分もかからずに実施できます。

### <a name="how-do-i-deploy-it"></a>デプロイ方法

組織に対して Azure Information Protection をデプロイする場合は、[分類、ラベル付け、および保護のデプロイ ロードマップ](https://docs.microsoft.com/information-protection/plan-design/deployment-roadmap)に関するページをご覧ください。

### <a name="is-there-anything-else-i-should-know"></a>その他の考慮事項

設定方法を検討する際に役立つ補足情報については、[準備、設定、保護](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/21/azure-information-protection-ready-set-protect/)に関するページと、
ブログの投稿をご覧ください。 また、以下のリンクで、Azure Information Protection の詳細情報を確認することもできます。

## <a name="azure-search-data-discovery-for-developer-apps"></a>Azure Search: 開発者アプリのデータ検出

[Azure Search](https://azure.microsoft.com/services/search/) は開発者向けのクラウド検索ソリューションで、アプリケーションに対して豊富なデータ検索エクスペリエンスを提供します。 Azure Search を使用すると、Azure Cosmo DB、Azure SQL Database、Azure Blob Storage、Azure テーブル ストレージ、またはカスタム顧客 JSON データをソースとして、ユーザー定義インデックスで、データを検索することができます。 また、Azure Search REST API を使用して Lucene クエリを構成し、個人データの種類や特定ユーザーの個人データを検索することもできます。 機能には、フルテキスト検索、単純なクエリ構文、Lucene クエリ構文などがあります。 

## <a name="how-do-i-use-sql-to-query-data"></a>SQL を使用してデータにクエリを実行する方法

基本から始めるには、「[Azure Cosmos DB: SQL を使用してクエリを実行する方法](../cosmos-db/tutorial-query-documentdb.md)」チュートリアルにアクセスしてください。 このチュートリアルには、1 つのサンプル ドキュメントと 2 つのサンプル SQL クエリおよび結果が含まれます。

SQL クエリの作成に関する詳細なガイダンスについては、「[Azure Cosmos DB DocumentDB API の SQL クエリ](../cosmos-db/documentdb-sql-query.md)」を参照してください。

Azure Cosmos DB を初めてご利用になる場合、データベースの作成方法、コレクションやデータの追加方法を確認するには、[Azure Cosmos DB: DocumentDB API Web アプリの構築](../cosmos-db/create-documentdb-dotnet.md)クイックスタート チュートリアルにアクセスしてください。 Java、Python など、.NET 以外の言語でこれを行う必要がある場合は、サイトにアクセスして、目的の言語を選択するだけです。

## <a name="next-steps"></a>次のステップ

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50)

[SQL Database とは](../sql-database/sql-database-technical-overview.md)

[Azure Portal で使用できる SQL Database クエリ エディター] (https://azure.microsoft.com/blog/t-sql-query-editor-in-browser-azure-portal/)

[Azure Information Protection とは](https://docs.microsoft.com/information-protection/understand-explore/what-is-information-protection)

[Azure Rights Management とは](https://docs.microsoft.com/information-protection/understand-explore/what-is-azure-rms)

[Azure Information Protection: 準備、設定、保護](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/21/azure-information-protection-ready-set-protect/)
