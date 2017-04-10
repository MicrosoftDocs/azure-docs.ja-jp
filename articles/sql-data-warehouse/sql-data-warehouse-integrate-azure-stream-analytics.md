---
title: "SQL Data Warehouse での Azure Stream Analytics の使用 | Microsoft Docs"
description: "ソリューション開発のための、Azure SQL Data Warehouse での Azure Stream Analytics の使用に関するヒント。"
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: barbkess
editor: 
ms.assetid: 8aeb2247-20c5-4a29-b327-30a8ce09dfdc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: kevin;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f0d7faede5df839c699f562b3aa21b8bb9836a3b


---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>SQL Data Warehouse での Azure Stream Analytics の使用
Azure Stream Analytics は、待機時間の短縮、高可用性、クラウド内のデータのストリーミング データに対する拡張性の高い複雑なイベント処理を実現する、十分に管理されたサービスです。 基本事項については、[「Azure Stream Analytics の概要」][Azure Stream Analytics の概要] をお読みください。 その後で、[「Azure Stream Analytics の使用」][Azure Stream Analytics の使用] チュートリアルに従って、Stream Analytics を使用してエンド ツー エンド ソリューションを作成する方法を学びます。

この記事では、Azure SQL Data Warehouse データベースを、Stream Analytics ジョブの出力シンクとして使用する方法について説明します。

## <a name="prerequisites"></a>前提条件
最初に、[「Azure Stream Analytics の使用」][Azure Stream Analytics の使用] チュートリアルの次の手順を通読します。  

1. Event Hub 入力の作成
2. イベント ジェネレーター アプリケーションの構成と起動
3. Stream Analytics のジョブの準備
4. ジョブの入力とクエリの指定

次に、Azure SQL Data Warehouse データベースを作成します

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>ジョブの出力の指定: Azure SQL Data Warehouse データベース
### <a name="step-1"></a>手順 1
Stream Analytics ジョブで、ページ上部の **[出力]** をクリックし、**[出力の追加]** をクリックします。

### <a name="step-2"></a>手順 2.
SQL Database を選択し、[次へ] をクリックします。

![][add-output]

### <a name="step-3"></a>手順 3.
次の値を次のページに入力します。

* *出力のエイリアス*: このジョブの出力のフレンドリ名を入力します。
* *サブスクリプション*:
  * SQL Data Warehouse データベースが Stream Analytics ジョブと同じサブスクリプション内に存在する場合は、[現在のサブスクリプションの SQL データベースを使用] を選択します。
  * データベースが別のサブスクリプション内にある場合は、[別のサブスクリプションの SQL データベースを使用] を選択します。
* *データベース*: 宛先データベースの名前を指定します。
* *サーバー名*: 指定したばかりのデータベース用のサーバー名を指定します。 これは、Azure クラシック ポータルを使用して見つけることができます。

![][server-name]

* *ユーザー名*: データベースの書き込みアクセス許可を持つアカウントのユーザー名を指定します。
* *パスワード*: 指定したユーザー アカウントのパスワードを入力します。
* *テーブル*: データベース内の対象テーブルの名前を指定します。

![][add-database]

### <a name="step-4"></a>手順 4.
チェック ボタンをクリックして、このジョブ出力を追加し、Stream Analytics がデータベースに適切に接続できることを確認します。

![][test-connection]

データベースへの接続が成功すると、ポータルの下部に通知が表示されます。 下部にある [接続のテスト] をクリックすると、データベースへの接続をテストできます。

## <a name="next-steps"></a>次のステップ
統合の概要については、[「SQL Data Warehouse の統合の概要」][SQL Data Warehouse の統合の概要] をご覧ください。

開発に関するその他のヒントについては、[SQL Data Warehouse development overview (SQL Data Warehouse の開発の概要) (SQL Data Warehouse の開発の概要)][SQL Data Warehouse development overview (SQL Data Warehouse の開発の概要) (SQL Data Warehouse の開発の概要)]に関するページを参照してください。

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Azure Stream Analytics の概要]: ../stream-analytics/stream-analytics-introduction.md
[Azure Stream Analytics の使用]: ../stream-analytics/stream-analytics-get-started.md
[SQL Data Warehouse development overview (SQL Data Warehouse の開発の概要) (SQL Data Warehouse の開発の概要)]:  ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse の統合の概要]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics のドキュメント]: http://azure.microsoft.com/documentation/services/stream-analytics/



<!--HONumber=Nov16_HO3-->


