---
title: SQL Data Warehouse のデータを Power BI で視覚化する | Microsoft Azure
description: SQL Data Warehouse のデータを Power BI で視覚化します。
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: b2377466ba830eed9fed0e9a4033b3eca3d351c4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57834608"
---
# <a name="visualize-data-with-power-bi"></a>Power BI でデータを視覚化する
このチュートリアルでは、Power BI を使用して、SQL Data Warehouse に接続し、いくつかの基本的な視覚化を作成する方法を示します。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>前提条件
このチュートリアルを進めるには、次が必要です。

* AdventureWorksDW データベースが事前に読み込まれている SQL Data Warehouse。 データ ウェアハウスをプロビジョニングするには、[SQL Data Warehouse の作成](create-data-warehouse-portal.md)に関するページを参照し、サンプル データを読み込んでください。 データ ウェアハウスは既にあるもののサンプル データがない場合は、[WideWorldImportersDW を読み込む](load-data-wideworldimportersdw.md)ことができます。

## <a name="1-connect-to-your-database"></a>1.データベースに接続する
Power BI を開き、AdventureWorksDW データベースに接続するには:

1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. **[SQL Database]** をクリックし、AdventureWorks SQL Data Warehouse データベースを選択します。
   
    ![データベースの検索](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. [Power BI で開く] ボタンをクリックします。
   
    ![Power BI ボタン](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. SQL Data Warehouse の接続ページにデータベースの Web アドレスが表示されます。 [次へ] をクリックします。
   
    ![Power BI connection](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Azure SQL Server のユーザー名とパスワードを入力します。
   
    ![Power BI のサインイン](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. データベースを開くには、左側のブレードの AdventureWorksDW データセットをクリックします。
   
    ![Power BI open AdventureWorksDW](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2.レポートの作成
Power BI を使用して AdventureWorksDW サンプル データを分析する準備が整いました。 分析を実行するため、AdventureWorksDW には AggregateSales という名前のビューがあります。 このビューには、会社の売上高を分析するための重要なメトリックがいくつか含まれています。

1. 郵便番号に基づく売上高の地図を作成するには、右側のフィールド ペインにある [AggregateSales] ビューをクリックして展開します。 [PostalCode] 列と [SalesAmount] 列をクリックして選択します。
   
    ![Power BI が AggregateSales を選択する](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    Power BI は地理データを自動的に認識し、マップに配置します。
   
    ![Power BI マップ](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. この手順では、顧客の所得ごとの売上高を表示する棒グラフを作成します。 棒グラフを作成するには、展開した AggregateSales ビューにアクセスします。 [SalesAmount] フィールドをクリックします。 [Customer Income] フィールドを左方向へドラッグし、[軸] にドロップします。
   
    ![Power BI が軸を選択する](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    左側の棒グラフ。
   
    ![Power BI バー](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. この手順では、発注日ごとの売上高を表示する折れ線グラフを作成します。 折れ線グラフを作成するには、展開した AggregateSales ビューにアクセスします。 [SalesAmount] と [OrderDate] をクリックします。 [視覚化] 列で、折れ線グラフのアイコンをクリックします。視覚化の下の 2 行目先頭にあるアイコンです。
   
    ![Power BI が折れ線グラフを選択する](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    これで、同じデータを 3 とおりの方法で視覚化したレポートが完成しました。
   
    ![Power BI ライン](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

**[ファイル]** をクリックして **[保存]** を選択すると、作業をいつでも保存できます。

## <a name="using-direct-connect"></a>直接接続の使用
Azure SQL Database の場合と同様、SQL Data Warehouse の直接接続を使用すると、論理プッシュダウンと共に Power BI の分析機能を利用できます。 直接接続では、データを検索する際に、クエリがリアルタイムで Azure SQL Data Warehouse に送信されます。  この機能を SQL Data Warehouse のスケールと組み合わせることにより、テラバイト単位のデータに対して動的レポートを数分で作成できます。 さらに、[Power BI で開く] ボタンが導入されたため、Azure の他の部分から情報を収集しなくても、Power BI を直接 SQL Data Warehouse に接続できるようになりました。

直接接続を使用するときは、次の点に注意してください。

* 接続するときに、サーバーの完全修飾名を指定します。
* データベースのファイアウォール ルールでは、必ず Azure サービスへのアクセスを許可するように構成します。
* 列の選択やフィルターの追加などの操作を行うたびに、データ ウェアハウスに対して直接クエリが実行されます。
* タイルは約 15 分ごとに自動的に更新されます。
* Q&A は、直接接続データセットでは使用できません。
* スキーマの変更は自動的に組み込まれます。
* 直接接続のクエリはすべて 2 分後にタイムアウトになります。

これらの制限および注意事項は、エクスペリエンスの改善に伴って変更される場合があります。

## <a name="next-steps"></a>次の手順
いくつかサンプル データを挙げて、どのようにして[開発](sql-data-warehouse-overview-develop.md)、[読み込み](design-elt-data-loading.md)、[移行](sql-data-warehouse-overview-migrate.md)するかを確認しながらウォーミングアップしていきます。 または、[Power BI の Web サイト](https://www.powerbi.com/)を参照してください。
