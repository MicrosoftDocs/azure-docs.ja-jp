---
title: "Redgate を使用して Azure データ ウェアハウスにデータを読み込む | Microsoft Docs"
description: "Redgate の Data Platform Studio をデータ ウェアハウジングのシナリオで使う方法について説明します。"
services: sql-data-warehouse
documentationcenter: NA
author: twounder
manager: jhubbard
editor: 
ms.assetid: 670aef98-31f7-4436-86c0-cc989a39fe7f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: mausher;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2548f779767635865daf790d301d86feff573a29
ms.openlocfilehash: cb0b5489ccfabb0e2a4ee412162a18930073b309
ms.lasthandoff: 01/24/2017



---
# <a name="load-data-with-redgate-data-platform-studio"></a>Redgate Data Platform Studio を使用したデータの読み込み
> [!div class="op_single_selector"]
> * [Redgate](sql-data-warehouse-load-with-redgate.md)
> * [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
> * [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
> * [BCP](sql-data-warehouse-load-with-bcp.md)
> 
> 

このチュートリアルでは、[Redgate の Data Platform Studio](http://www.red-gate.com/products/azure-development/data-platform-studio/) (DPS) を使用してオンプレミスの SQL Server から Azure SQL Data Warehouse にデータを移動する方法について説明します。 Data Platform Studio では、最も適切な互換性修正プログラムと最適化が適用されるため、SQL Data Warehouse をごく手軽に使い始めることができます。

> [!NOTE]
> [Redgate](http://www.red-gate.com) は、さまざまな SQL Server ツールを提供する、長年の Microsoft パートナーです。 Data Platform Studio のこの機能は、商用と非商用のどちらの目的にも自由に使用できます。
> 
> 

## <a name="before-you-begin"></a>開始する前に
### <a name="create-or-identify-resources"></a>リソースを作成または識別する
このチュートリアルを開始する前に、次のものを用意する必要があります。

* **オンプレミスの SQL Server データベース**: SQL Data Warehouse にインポートするデータは、オンプレミスの SQL Server (バージョン 2008R2 以上) から取得する必要があります。 Data Platform Studio では、データを Azure SQL Database から直接インポートすることや、テキスト ファイルからインポートすることはできません。
* **Azure Storage アカウント**: Data Platform Studio は、データを SQL Data Warehouse に読み込む前に Azure Blob Storage にステージングします。 ストレージ アカウントでは、"クラシック" デプロイメント モデルではなく "Resource Manager" デプロイメント モデル (既定値) を使用している必要があります。 ストレージ アカウントがない場合は、ストレージ アカウントの作成方法に関するトピックを参照してください。 
* **SQL Data Warehouse**: このチュートリアルでは、オンプレミスの SQL Server から SQL Data Warehouse にデータを移動します。そのため、オンラインのデータ ウェアハウスが必要になります。 データ ウェアハウスがまだない場合は、Azure SQL Data Warehouse の作成方法に関するトピックを参照してください。

> [!NOTE]
> ストレージ アカウントとデータ ウェアハウスが同じリージョンに作成されていると、パフォーマンスが向上します。
> 
> 

## <a name="step-1-sign-in-to-data-platform-studio-with-your-azure-account"></a>手順 1. Azure アカウントを使用して Data Platform Studio にサインインする
Web ブラウザーを開き、[Data Platform Studio](https://www.dataplatformstudio.com/) の Web サイトに移動します。 ストレージ アカウントとデータ ウェアハウスを作成するときに使用したのと同じ Azure アカウントでサインインします。 電子メール アドレスが職場または学校アカウントと Microsoft アカウントの両方に関連付けられている場合は、対象のリソースにアクセスできるアカウントを選択してください。

> [!NOTE]
> Data Platform Studio を初めて使用する場合は、Azure リソースを管理する権限をアプリケーションに付与するよう求められます。
> 
> 

## <a name="step-2-start-the-import-wizard"></a>手順 2. インポート ウィザードを開始する
DPS のメイン画面で、[Import to Azure SQL Data Warehouse (Azure SQL Data Warehouse へのインポート)] リンクを選択してインポート ウィザードを起動します。

![][1]

## <a name="step-3-install-the-data-platform-studio-gateway"></a>手順 3. Data Platform Studio Gateway をインストールする
オンプレミスの SQL Server データベースに接続するには、DPS Gateway をインストールする必要があります。 このゲートウェイは、オンプレミス環境へのアクセスを提供し、データを抽出してストレージ アカウントにアップロードすることを可能にするクライアント エージェントです。 データは Redgate のサーバーを経由しません。 ゲートウェイをインストールするには、次の手順に従います。

1. **[Create Gateway (ゲートウェイの作成)]** リンクをクリックします。
2. 提供されたインストーラーを使用して、ゲートウェイをダウンロードおよびインストールします。

![][2]

> [!NOTE]
> ゲートウェイは、ソース SQL Server データベースにネットワーク経由でアクセスできる任意のコンピューターにインストールできます。 ゲートウェイは、現在のユーザーの資格情報による Windows 認証を使用して SQL Server データベースにアクセスします。
> 
> 

インストールされると、ゲートウェイの状態が [Connected (接続済み)] に変わります。ここで、[Next (次へ)] を選択します。

## <a name="step-4-identify-the-source-database"></a>手順 4. ソース データベースを識別する
*[Enter Server Name (サーバー名を入力してください)]* ボックスに、データベースをホストしているサーバーの名前を入力し、**[Next (次へ)]** を選択します。 次に、ドロップダウン メニューから、データをインポートするデータベースを選択します。

![][3]

DPS により、選択したデータベースにインポートの対象となるテーブルが存在するかどうかが確認されます。 既定では、DPS により、データベース内のすべてのテーブルがインポートされます。 [All Tables (すべてのテーブル)] リンクを展開し、テーブルを選択または選択解除できます。 [Next (次へ)] ボタンを選択して続行します。

## <a name="step-5-choose-a-storage-account-to-stage-the-data"></a>手順 5. データをステージングするストレージ アカウントを選択する
DPS により、データをステージングする場所を指定するよう求められます。 サブスクリプションの既存のストレージ アカウントを選択し、**[Next (次へ)]** を選択します。

> [!NOTE]
> DPS によって、選択したストレージ アカウントに新しい BLOB コンテナーが作成されます。インポートごとに別個のフォルダーが使用されます。
> 
> 

![][4]

## <a name="step-6-select-a-data-warehouse"></a>手順 6. データ ウェアハウスを選択する
次に、データをインポートするオンラインの [Azure SQL Data Warehouse](http://aka.ms/sqldw) データベースを選択します。 データベースを選択した後、そのデータベースに接続するための資格情報を入力し、**[Next (次へ)]** を選択します。

![][5]

> [!NOTE]
> DPS により、ソース データ テーブルがデータ ウェアハウスにマージされます。 テーブル名に基づいてデータ ウェアハウス内の既存のテーブルが上書きされる場合は、警告が出力されます。 オプションで [Delete all existing objects before import (インポート前に既存のオブジェクトをすべて削除する)] をオンにすると、データ ウェアハウス内の既存のオブジェクトをすべて削除できます。
> 
> 

## <a name="step-7-import-the-data"></a>手順 7. データをインポートする
データをインポートするかどうかを確認するメッセージが表示されます。 [Start import (インポートの開始)] ボタンをクリックして、データのインポートを開始します。

![][6]

DPS により、オンプレミスの SQL Server からのデータの抽出およびアップロード処理の進行状況と、SQL Data Warehouse へのインポート処理の進行状況が視覚的に表示されます。

![][7]

インポートが完了すると、データのインポートの概要と、実行された互換性修正プログラムの変更レポートが表示されます。

![][8]

## <a name="next-steps"></a>次のステップ
SQL Data Warehouse 内のデータを探索するには、次のトピックを参照してください。

* [Azure SQL Data Warehouse に対するクエリ (Visual Studio)][Query Azure SQL Data Warehouse (Visual Studio)]
* [Power BI でデータを視覚化する][Visualize data with Power BI]

Redgate の Data Platform Studio についてさらに詳しい情報を得るには:

* [DPS のホーム ページにアクセスする](http://www.dataplatformstudio.com/)
* [Channel9 で DPS のデモを見る](https://channel9.msdn.com/Blogs/cloud-with-a-silver-lining/Loading-data-into-Azure-SQL-Datawarehouse-with-Redgate-Data-Platform-Studio)

データを移行して SQL Data Warehouse に読み込むための他の方法の概要については、次のトピックを参照してください。

* [SQL Data Warehouse へのソリューションの移行][Migrate your solution to SQL Data Warehouse]
* [Azure SQL Data Warehouse へのデータの読み込み](sql-data-warehouse-overview-load.md)

開発に関するその他のヒントについては、[SQL Data Warehouse の開発の概要](sql-data-warehouse-overview-develop.md)に関するトピックを参照してください。

<!--Image references-->
[1]: media/sql-data-warehouse-redgate/2016-10-05_15-59-56.png
[2]: media/sql-data-warehouse-redgate/2016-10-05_11-16-07.png
[3]: media/sql-data-warehouse-redgate/2016-10-05_11-17-46.png
[4]: media/sql-data-warehouse-redgate/2016-10-05_11-20-41.png
[5]: media/sql-data-warehouse-redgate/2016-10-05_11-31-24.png
[6]: media/sql-data-warehouse-redgate/2016-10-05_11-32-20.png
[7]: media/sql-data-warehouse-redgate/2016-10-05_11-49-53.png
[8]: media/sql-data-warehouse-redgate/2016-10-05_12-57-10.png

<!--Article references-->
[Query Azure SQL Data Warehouse (Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md
[Visualize data with Power BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md
[Migrate your solution to SQL Data Warehouse]: ./sql-data-warehouse-overview-migrate.md
[Load data into Azure SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md

