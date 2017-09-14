---
title: "SQL Data Warehouse での Azure Stream Analytics の使用 | Microsoft Docs"
description: "ソリューション開発のための、Azure SQL Data Warehouse での Azure Stream Analytics の使用に関するヒント。"
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 8aeb2247-20c5-4a29-b327-30a8ce09dfdc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d1c328ec609cc27a42fb6b30897d7f0b466134e7
ms.contentlocale: ja-jp
ms.lasthandoff: 04/03/2017


---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>SQL Data Warehouse での Azure Stream Analytics の使用
Azure Stream Analytics は、待機時間の短縮、高可用性、クラウド内のデータのストリーミング データに対する拡張性の高い複雑なイベント処理を実現する、十分に管理されたサービスです。 基本事項については、「[Stream Analytics とは][Introduction to Azure Stream Analytics]」を参照してください。 その後で、「[Azure Stream Analytics の使用][Get started using Azure Stream Analytics]」チュートリアルに従って、Stream Analytics を使用してエンド ツー エンド ソリューションを作成する方法を知ることができます。

この記事では、Azure SQL Data Warehouse データベースを、Stream Analytics ジョブの出力シンクとして使用する方法について説明します。

## <a name="prerequisites"></a>前提条件
最初に、「[Azure Stream Analytics の使用][Get started using Azure Stream Analytics]」チュートリアルの次の手順を通読します。  

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
統合の概要については、「[SQL Data Warehouse の設計上の決定と コーディング技法][SQL Data Warehouse integration overview]」を参照してください。

開発に関するその他のヒントについては、[SQL Data Warehouse の開発の概要][SQL Data Warehouse development overview]に関する記事をご覧ください。

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introduction to Azure Stream Analytics]: ../stream-analytics/stream-analytics-introduction.md
[Get started using Azure Stream Analytics]: ../stream-analytics/stream-analytics-real-time-fraud-detection.md
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/

