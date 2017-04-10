---
title: "SQL Server から Azure SQL Data Warehouse へのデータの読み込み (SSIS) | Microsoft Docs"
description: "さまざまなデータ ソースから SQL Data Warehouse にデータを移動する SQL Server Integration Services (SSIS) パッケージを作成する方法について説明します。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: e2c252e9-0828-47c2-a808-e3bea46c134a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
ms.custom: loading
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: a8c557ea07cbccb913bc47c510f6759dd832c861
ms.lasthandoff: 12/08/2016


---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-ssis"></a>SQL Server から Azure SQL Data Warehouse へのデータの読み込み (SSIS)
> [!div class="op_single_selector"]
> * [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
> * [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
> * [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)
> 
> 

SQL Server Integration Services (SSIS) パッケージを作成して、SQL Server から Azure SQL Data Warehouse にデータを読み込みます。 SSIS データ フローを通過するときに、必要に応じて、データを再構築、変換、クレンジングできます。

このチュートリアルでは、次のことについて説明します。

* Visual Studio で新しい Integration Services プロジェクトを作成する。
* SQL Server (変換元) と SQL Data Warehouse (変換先) を含む、データ ソースに接続する。
* 変換元から変換先にデータを読み込む SSIS パッケージを設計する。
* SSIS パッケージを実行してデータを読み込む。

このチュートリアルでは、データ ソースとして SQL Server を使用します。 SQL Server は、オンプレミスまたは Azure の仮想マシンで実行できます。

## <a name="basic-concepts"></a>基本的な概念
パッケージは、SSIS の作業単位です。 関連するパッケージは、プロジェクトにグループ化されます。 プロジェクトの作成とパッケージの設計は、Visual Studio で SQL Server Data Tools を使って行います。 設計プロセスは、ツールボックスからデザイン画面にコンポーネントをドラッグ アンド ドロップし、それらを接続して、プロパティを設定する視覚的なプロセスです。 パッケージが完成したら、必要に応じて、包括的な管理、監視、セキュリティを実現するためにそのパッケージを SQL Server にデプロイできます。

## <a name="options-for-loading-data-with-ssis"></a>SSIS を使用してデータを読み込むためのオプション
SQL Server Integration Services (SSIS) は、SQL Data Warehouse に接続し、データを読み込むためのさまざまなオプションが用意されている、柔軟なツール セットです。

1. ADO NET 変換先を使用して、SQL Data Warehouse に接続する。 このチュートリアルでは、使用する構成オプションが最小限のため、ADO NET 変換先を使用します。
2. OLE DB 変換先を使用して、SQL Data Warehouse に接続する。 このオプションは、ADO NET 変換先よりもパフォーマンスが若干高くなる場合があります。
3. Azure BLOB アップロード タスクを使用して Azure Blob Storage でデータをステージングした後、 SSIS の SQL 実行タスクを使用して、SQL Data Warehouse にデータを読み込む Polybase スクリプトを起動する。 このオプションを使用すると、ここに示した 3 つのオプションの中で最も高いパフォーマンスが得られます。 Azure BLOB アップロード タスクを取得するには、[Microsoft SQL Server 2016 Integration Services Feature Pack for Azure][Microsoft SQL Server 2016 Integration Services Feature Pack for Azure] をダウンロードします。 Polybase の詳細については、[PolyBase ガイド][PolyBase Guide]をご覧ください。

## <a name="before-you-start"></a>開始する前に
このチュートリアルを進めるには、次が必要です。

1. **SQL Server Integration Services (SSIS)**。 SSIS は SQL Server のコンポーネントであるため、SQL Server の評価版またはライセンス版が必要です。 SQL Server 2016 Preview の評価版を入手するには、「[SQL Server 評価版ソフトウェア][SQL Server Evaluations]」をご覧ください。
2. **Visual Studio**。 無料の Visual Studio 2015 Community Edition を入手するには、「[Visual Studio Community][Visual Studio Community]」をご覧ください。
3. **SQL Server Data Tools for Visual Studio (SSDT)**。 SQL Server Data Tools for Visual Studio 2015 を入手するには、「[SQL Server Data Tools (SSDT) のダウンロード][Download SQL Server Data Tools (SSDT)]」をご覧ください。
4. **サンプル データ**。 このチュートリアルでは、SQL Data Warehouse に読み込むソース データとして SQL Server の AdventureWorks サンプル データベースに格納されているサンプル データを使用します。 AdventureWorks サンプル データベースを入手するには、「[AdventureWorks 2014 Sample Databases (AdventureWorks 2014 サンプル データベース)][AdventureWorks 2014 Sample Databases]」をご覧ください。
5. **SQL Data Warehouse データベースとアクセス許可**。 このチュートリアルでは、SQL Data Warehouse インスタンスに接続し、そのインスタンスにデータを読み込みます。 テーブルを作成し、データを読み込むためのアクセス許可が必要です。
6. **ファイアウォール規則**。 SQL Data Warehouse にデータをアップロードする前に、SQL Data Warehouse でローカル コンピューターの IP アドレスを指定したファイアウォール規則を作成する必要があります。

## <a name="step-1-create-a-new-integration-services-project"></a>手順 1: 新しい Integration Services プロジェクトを作成する
1. Visual Studio 2015 を起動します。
2. **[ファイル]** メニューの **[新規作成]、[プロジェクト]** の順にクリックします。
3. **[インストール済み]、[テンプレート]、[ビジネス インテリジェンス]、[Integration Services]** プロジェクトの種類の順に移動します。
4. **[Integration Services プロジェクト]**を選択します。 **[名前]** と **[場所]** に値を指定し、**[OK]** をクリックします。

Visual Studio で、新しい Integration Services (SSIS) プロジェクトが作成され、開きます。 プロジェクト内の 1 つの新しい SSIS パッケージ (Package.dtsx) のデザイナーが開きます。 次の画面領域が表示されます。

* 左側には、SSIS コンポーネントの **ツールボックス** があります。
* 中央には、複数のタブを備えたデザイン画面があります。 通常、少なくとも **[制御フロー]** タブと **[データ フロー]** タブを使用します。
* 右側には、**ソリューション エクスプローラー**と **[プロパティ]** ウィンドウがあります。
  
    ![][01]

## <a name="step-2-create-the-basic-data-flow"></a>手順 2: 基本的なデータ フローを作成する
1. ツールボックスからデザイン画面の中央 ( **[制御フロー]** タブ) に [データ フロー タスク] をドラッグします。
   
    ![][02]
2. [データ フロー タスク] をダブルクリックして、[データ フロー] タブに切り替えます。
3. ツールボックスの [その他の変換元] の一覧から [ADO NET 変換元] をデザイン画面にドラッグします。 変換元アダプターが選択されたままの状態で、**[プロパティ]** ウィンドウで名前を **SQL Server source** に変更します。
4. ツールボックスの [その他の変換先] の一覧から [ADO NET 変換先] をデザイン画面の ADO.NET 変換元の下にドラッグします。 変換先アダプターが選択されたままの状態で、**[プロパティ]** ウィンドウで名前を **SQL DW destination** に変更します。
   
    ![][09]

## <a name="step-3-configure-the-source-adapter"></a>手順 3: 変換元アダプターを構成する
1. 変換元アダプターをダブルクリックして、 **[ADO.NET 変換元エディター]**を開きます。
   
    ![][03]
2. **[ADO.NET 変換元エディター]** の **[接続マネージャー]** タブで、**[ADO.NET 接続マネージャー]** ボックスの横にある **[新規]** ボタンをクリックして、**[ADO.NET の接続マネージャーの構成]** ダイアログ ボックスを開き、このチュートリアルのデータの読み込み元となる SQL Server データベースの接続設定を作成します。
   
    ![][04]
3. **[ADO.NET の接続マネージャーの構成]** ダイアログ ボックスで、**[新規]** をクリックして、**[接続マネージャー]** ダイアログ ボックスを開き、新しいデータ接続を作成します。
   
    ![][05]
4. **[接続マネージャー]** ダイアログ ボックスで、次の手順を実行します。
   
   1. **[プロバイダー]**で、SqlClient データ プロバイダーを選択します。
   2. **[サーバー名]**で、SQL Server の名前を入力します。
   3. **[サーバーへのログオン]** セクションで、認証情報を選択するか、入力します。
   4. **[データベースへの接続]** セクションで、AdventureWorks サンプル データベースを選択します。
   5. **[接続テスト]**をクリックします。
      
       ![][06]
   6. 接続テストの結果を報告するダイアログ ボックスで、**[OK]** をクリックして、**[接続マネージャー]** ダイアログ ボックスに戻ります。
   7. **[接続マネージャー]** ダイアログ ボックスで、**[OK]** をクリックして、**[ADO.NET の接続マネージャーの構成]** ダイアログ ボックスに戻ります。
5. **[ADO.NET の接続マネージャーの構成]** ダイアログ ボックスで、**[OK]** をクリックして、**[ADO.NET 変換元エディター]** に戻ります。
6. **[ADO.NET 変換元エディター]** で、**[テーブル名またはビュー名]** ボックスの一覧の **Sales.SalesOrderDetail** テーブルを選択します。
   
    ![][07]
7. **[プレビュー]** をクリックして、**[クエリ結果のプレビュー]** ダイアログ ボックスで変換元テーブル内のデータの最初の 200 行を確認します。
   
    ![][08]
8. **[クエリ結果のプレビュー]** ダイアログ ボックスで、**[閉じる]** をクリックして、**[ADO.NET 変換元エディター]** に戻ります。
9. **[ADO.NET 変換元エディター]** で、**[OK]** をクリックして、データ ソースの構成を完了します。

## <a name="step-4-connect-the-source-adapter-to-the-destination-adapter"></a>手順 4: 変換元アダプターを変換先アダプターに接続する
1. デザイン画面で変換元アダプターを選択します。
2. 変換元アダプターから延びている青い矢印を選択し、所定の位置に固定されるまで変換先エディターにドラッグします。
   
    ![][10]
   
    一般的な SSIS パッケージでは、変換元と変換先の間に [SSIS ツールボックス] から他の多数のコンポーネントを使用して、SSIS データ フローを通過するときにデータを再構築、変換、クレンジングします。 この例はできるだけ簡単にするために、変換元を変換先に直接接続しています。

## <a name="step-5-configure-the-destination-adapter"></a>手順 5: 変換先アダプターを構成する
1. 変換先アダプターをダブルクリックして、 **[ADO.NET 変換先エディター]**を開きます。
   
    ![][11]
2. **[ADO.NET 変換先エディター]** の **[接続マネージャー]** タブで、**[接続マネージャー]** ボックスの横にある **[新規]** ボタンをクリックして、**[ADO.NET の接続マネージャーの構成]** ダイアログ ボックスを開き、このチュートリアルのデータの読み込み先となる Azure SQL Data Warehouse データベースの接続設定を作成します。
3. **[ADO.NET の接続マネージャーの構成]** ダイアログ ボックスで、**[新規]** をクリックして、**[接続マネージャー]** ダイアログ ボックスを開き、新しいデータ接続を作成します。
4. **[接続マネージャー]** ダイアログ ボックスで、次の手順を実行します。
   1. **[プロバイダー]**で、SqlClient データ プロバイダーを選択します。
   2. **[サーバー名]**で、SQL Data Warehouse の名前を入力します。
   3. **[サーバーへのログオン]** セクションで、**[SQL Server 認証を使用する]** を選択し、認証情報を入力します。
   4. **[データベースへの接続]** セクションで、既存の SQL Data Warehouse データベースを選択します。
   5. **[接続テスト]**をクリックします。
   6. 接続テストの結果を報告するダイアログ ボックスで、**[OK]** をクリックして、**[接続マネージャー]** ダイアログ ボックスに戻ります。
   7. **[接続マネージャー]** ダイアログ ボックスで、**[OK]** をクリックして、**[ADO.NET の接続マネージャーの構成]** ダイアログ ボックスに戻ります。
5. **[ADO.NET の接続マネージャーの構成]** ダイアログ ボックスで、**[OK]** をクリックして、**[ADO.NET 変換先エディター]** に戻ります。
6. **[ADO.NET 変換先エディター]** で、**[テーブルまたはビューを使用]** ボックスの横にある **[新規]** をクリックして、**[テーブルの作成]** ダイアログ ボックスを開き、変換元テーブルと一致する列一覧を備えた新しい変換先テーブルを作成します。
   
    ![][12a]
7. **[テーブルの作成]** ダイアログ ボックスで、次の手順を実行します。
   
   1. 変換先テーブルの名前を **SalesOrderDetail**に変更します。
   2. **rowguid** 列を削除します。 **uniqueidentifier** データ型は、SQL Data Warehouse ではサポートされていません。
   3. **LineTotal** 列のデータ型を **money** に変更します。 **decimal** データ型は、SQL Data Warehouse ではサポートされていません。 サポートされるデータ型については、「[CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse) (CREATE TABLE (Azure SQL Data Warehouse、Parallel Data Warehouse))][CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)]」をご覧ください。
      
       ![][12b]
   4. **[OK]** をクリックして、テーブルを作成し、**[ADO.NET 変換先エディター]** に戻ります。
8. **[ADO.NET 変換先エディター]** で、**[マッピング]** タブをクリックして、変換元の列と変換先の列がどのようにマッピングされているかを確認します。
   
    ![][13]
9. **[OK]** をクリックして、データ ソースの構成を完了します。

## <a name="step-6-run-the-package-to-load-the-data"></a>手順 6: パッケージを実行してデータを読み込む
パッケージを実行するには、ツール バーの **[開始]** をクリックするか、**[デバッグ]** メニューのいずれかの**実行**オプションを選択します。

パッケージの実行が開始されると、アクティビティを示す黄色の糸車と、これまでに処理された行数が表示されます。

![][14]

パッケージの実行が完了すると、成功したことを示す緑色のチェック マークと、変換元から変換先に読み込まれたデータの合計行数が表示されます。

![][15]

ご利用ありがとうございます。 SQL Server Integration Services を使用して、Azure SQL Data Warehouse にデータを問題なく読み込むことができました。

## <a name="next-steps"></a>次のステップ
* SSIS データ フローの詳細を確認します。 まずは、「[データ フロー][Data Flow]」をご覧ください。
* デザイン環境で直接パッケージのデバッグおよびトラブルシューティングを行う方法について確認します。 まずは、「[パッケージ開発のトラブルシューティング ツール][Troubleshooting Tools for Package Development]」をご覧ください。
* Integration Services サーバーまたは別のストレージの場所に SSIS プロジェクトとパッケージをデプロイする方法について確認します。 まずは、「[プロジェクトとパッケージの展開][Deployment of Projects and Packages]」をご覧ください。

<!-- Image references -->
[01]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-designer-01.png
[02]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-data-flow-task-02.png
[03]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-03.png
[04]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-manager-04.png
[05]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-05.png
[06]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/test-connection-06.png
[07]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-07.png
[08]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/preview-data-08.png
[09]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/source-destination-09.png
[10]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/connect-source-destination-10.png
[11]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-destination-11.png
[12a]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-before-12a.png
[12b]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-after-12b.png
[13]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/column-mapping-13.png
[14]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-running-14.png
[15]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-success-15.png

<!-- Article references -->

<!-- MSDN references -->
[PolyBase Guide]: https://msdn.microsoft.com/library/mt143171.aspx
[Download SQL Server Data Tools (SSDT)]: https://msdn.microsoft.com/library/mt204009.aspx
[CREATE TABLE (Azure SQL Data Warehouse, Parallel Data Warehouse)]: https://msdn.microsoft.com/library/mt203953.aspx
[Data Flow]: https://msdn.microsoft.com/library/ms140080.aspx
[Troubleshooting Tools for Package Development]: https://msdn.microsoft.com/library/ms137625.aspx
[Deployment of Projects and Packages]: https://msdn.microsoft.com/library/hh213290.aspx

<!--Other Web references-->
[Microsoft SQL Server 2016 Integration Services Feature Pack for Azure]: http://go.microsoft.com/fwlink/?LinkID=626967
[SQL Server Evaluations]: https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016
[Visual Studio Community]: https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx
[AdventureWorks 2014 Sample Databases]: https://msftdbprodsamples.codeplex.com/releases/view/125550

