---
title: "Azure Data Catalog でサポートされるデータ ソース | Microsoft Docs"
description: "この記事では、現在サポートされているデータ ソースの仕様を示します。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: jstevens
editor: 
tags: 
ms.assetid: fd4345ca-2ed8-4c5e-9c4b-f954be2fc9f9
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: d6867c73bc6ea3c238cceef8a68466d451f3365c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="supported-data-sources-in-azure-data-catalog"></a>Azure Data Catalog でサポートされるデータ ソース

ユーザーは、パブリック API またはワンクリック登録ツールを使って、または Azure Data Catalog の Web ポータルに直接情報を手動で入力して、メタデータを公開できます。 以下の表は、現在カタログでサポートされているすべてのデータ ソースと、それぞれの公開機能をまとめたものです。 また、各データ ソースを弊社のポータルから「オープンイン」で起動できる外部ツールも一覧表示されています。 2 番目の表には、各データ ソースの接続プロパティの詳細な技術仕様があります。


## <a name="list-of-supported-data-sources"></a>サポートされるデータ ソースの一覧

<table>
    <tr>
       <td><b>データ ソース オブジェクト</b></td>
       <td><b>API</b></td>
       <td><b>手動入力</b></td>
       <td><b>登録ツール</b></td>
       <td><b>オープンイン ツール</b></td>
       <td><b>メモ</b></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store のディレクトリ</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store のファイル</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure BLOB ストレージ</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure Storage ディレクトリ</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure Storage テーブル</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td>
        <font size="2"></font>
      </td>
      <td>
        <font size="2"></font>
      </td>
    </tr>
    <tr>
      <td>HDFS ディレクトリ</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>HDFS ファイル</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Hive テーブル</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Hive ビュー</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>MySQL テーブル</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel、Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>MySQL ビュー</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel、Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Oracle Database テーブル</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel、Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Oracle Database ビュー</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel、Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>その他 (汎用アセット)</td>
      <td>✓ </td>
      <td>✓ </td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure SQL Data Warehouse テーブル</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel、Power BI、SQL Server データ ツール</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Data Warehouse ビュー</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel、Power BI、SQL Server データ ツール</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services ディメンション</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel、Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services KPI</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel、Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services メジャー</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel、Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services テーブル</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel、Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services レポート</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>ブラウザー</font></td>
      <td><font size=2>ネイティブ モードのサーバーのみ。SharePoint モードはサポートされていません。</font></td>
    </tr>
    <tr>
      <td>SQL Server テーブル</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel、Power BI、SQL Server データ ツール</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server ビュー</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel、Power BI、SQL Server データ ツール</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Teradata テーブル</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Teradata ビュー</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SAP HANA ビュー</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>DB2 テーブル</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>DB2 ビュー</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>ファイル システムのファイル</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>FTP ディレクトリ</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>FTP ファイル</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>HTTP レポート</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>HTTP エンドポイント</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>HTTP ファイル</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>OData エンティティ セット</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>OData 関数</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>PostgreSQL テーブル</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>PostgreSQL ビュー</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SAP HANA ビュー</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td> Salesforce オブジェクト</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SharePoint リスト </td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure Cosmos DB コレクション</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>汎用 ODBC テーブル</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>汎用 ODBC ビュー</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Cassandra テーブル</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2>汎用 ODBC 資産として発行</font></td>
    </tr>
    <tr>
      <td>Cassandra ビュー</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2>汎用 ODBC 資産として発行</font></td>
    </tr>
    <tr>
      <td>Sybase テーブル</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Sybase ビュー</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>MongoDB テーブル</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2>汎用 ODBC 資産として発行</font></td>
    </tr>
    <tr>
      <td>MongoDB ビュー</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2>汎用 ODBC 資産として発行</font></td>
    </tr>
</table>

その他のソースのサポートが必要な場合は、 [Azure Data Catalog フォーラム](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)に機能のリクエストを送信してください。


## <a name="data-source-reference-specification"></a>データ ソース参照の仕様
> [!NOTE]
> 次の表の**DSL 構造体**列には、Azure Data Catalog で使われる "address" プロパティ バッグの接続プロパティのみが一覧表示されています。 つまり、"address" プロパティ バッグは、Azure Data Catalog が永続化しても使わないデータ ソースの他の接続プロパティを含むことができます。

<table>
    <tr>
       <td><b>ソースの種類</b></td>
       <td><b>資産の種類</b></td>
       <td><b>オブジェクトの種類</b></td>
       <td><b>DSL 構造体<b></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>コンテナー</td>
      <td>Data Lake</td>
      <td>
        <font size=2> Protocol: webhdfs <br>Authentication: {basic, oauth} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>テーブル</td>
      <td>ディレクトリ、ファイル</td>
      <td>
        <font size=2> Protocol: webhdfs <br>Authentication: {basic, oauth} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage (Azure Storage)</td>
      <td>コンテナー</td>
      <td>コンテナー</td>
      <td>
        <font size=2> Protocol: azure-blobs <br>Authentication: {azure-access-key} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ドメイン <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; アカウント <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; コンテナー </font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage (Azure Storage)</td>
      <td>テーブル</td>
      <td>BLOB、ディレクトリ</td>
      <td>
        <font size=2> Protocol: azure-blobs <br>Authentication: {azure-access-key} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ドメイン <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; アカウント <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; コンテナー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 名前 </font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage (Azure Storage)</td>
      <td>コンテナー</td>
      <td>コンテナー</td>
      <td>
        <font size=2> Protocol: azure-tables <br>Authentication: {azure-access-key} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ドメイン <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; アカウント </font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage (Azure Storage)</td>
      <td>テーブル</td>
      <td>テーブル</td>
      <td>
        <font size=2> Protocol: azure-tables <br>Authentication: {azure-access-key} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ドメイン <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; アカウント <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 名前 </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>コンテナー</td>
      <td>仮想クラスター</td>
      <td>
        <font size=2> Protocol: cosmos <br>Authentication: {basic, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>テーブル</td>
      <td>ストリーム、ストリーム セット、ビュー</td>
      <td>
        <font size=2> Protocol: cosmos <br>Authentication: {basic, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>コンテナー</td>
      <td>サイト</td>
      <td>
        <font size=2> Protocol: http <br>Authentication: {none, basic, windows, oauth} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>レポート</td>
      <td>レポート、ダッシュボード</td>
      <td>
        <font size=2> Protocol: http <br>Authentication: {none, basic, windows, oauth} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> Protocol: db2 <br>Authentication: {basic, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース </font>
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>テーブル</td>
      <td>テーブル、ビュー</td>
      <td>
        <font size=2> Protocol: db2 <br>Authentication: {basic, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; スキーマ </font>
      </td>
    </tr>
    <tr>
      <td>ファイル システム</td>
      <td>テーブル</td>
      <td>ファイル</td>
      <td>
        <font size=2> Protocol: file <br>Authentication: {none, basic, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; パス </font>
      </td>
    </tr>
    <tr>
      <td>FTP</td>
      <td>テーブル</td>
      <td>ディレクトリ、ファイル</td>
      <td>
        <font size=2> Protocol: ftp <br>Authentication: {none, basic, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Hadoop 分散ファイル システム</td>
      <td>コンテナー</td>
      <td>クラスター</td>
      <td>
        <font size=2> Protocol: webhdfs <br>Authentication: {basic, oauth} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Hadoop 分散ファイル システム</td>
      <td>テーブル</td>
      <td>ディレクトリ、ファイル</td>
      <td>
        <font size=2> Protocol: webhdfs <br>Authentication: {basic, oauth} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> Protocol: hive <br>Authentication: {HDInsight, basic, username, none} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>テーブル</td>
      <td>テーブル、ビュー</td>
      <td>
        <font size=2> Protocol: hive <br>Authentication: {HDInsight, basic, username, none} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>コンテナー</td>
      <td>サイト</td>
      <td>
        <font size=2> Protocol: http <br>Authentication: {none, basic, windows, oauth} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>レポート</td>
      <td>レポート、ダッシュボード</td>
      <td>
        <font size=2> Protocol: http <br>Authentication: {none, basic, windows, oauth} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>テーブル</td>
      <td>エンドポイント、ファイル</td>
      <td>
        <font size=2> Protocol: http <br>Authentication: {none, basic, windows, oauth} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> Protocol: mysql <br>Authentication: {protocol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>テーブル</td>
      <td>テーブル、ビュー</td>
      <td>
        <font size=2> Protocol: mysql <br>Authentication: {protocol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト </font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>コンテナー</td>
      <td>エンティティ コンテナー</td>
      <td>
        <font size=2> Protocol: odata <br>Authentication: {none, basic, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>テーブル</td>
      <td>エンティティ セット、関数</td>
      <td>
        <font size=2> Protocol: odata <br>Authentication: {none, basic, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; リソース </font>
      </td>
    </tr>
    <tr>
      <td>Oracle Database</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> Protocol: oracle <br>Authentication: {protocol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース </font>
      </td>
    </tr>
    <tr>
      <td>Oracle Database</td>
      <td>テーブル</td>
      <td>テーブル、ビュー</td>
      <td>
        <font size=2> Protocol: oracle <br>Authentication: {protocol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; スキーマ <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト </font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> Protocol: postgresql <br>Authentication: {basic, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース </font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>テーブル</td>
      <td>テーブル、ビュー</td>
      <td>
        <font size=2> Protocol: postgresql <br>Authentication: {basic, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; スキーマ <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>コンテナー</td>
      <td>サイト</td>
      <td>
        <font size=2> Protocol: http <br>Authentication: {none, basic, windows, oauth} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>レポート</td>
      <td>レポート、ダッシュボード</td>
      <td>
        <font size=2> Protocol: http <br>Authentication: {none, basic, windows, oauth} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Power Query</td>
      <td>テーブル</td>
      <td>データのマッシュアップ</td>
      <td>
        <font size=2> Protocol: power-query <br>Authentication: {oauth} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Salesforce</td>
      <td>テーブル</td>
      <td>オブジェクト</td>
      <td>
        <font size=2> Protocol: salesforce-com <br>Authentication: {basic, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; loginServer <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; class <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; itemName </font>
      </td>
    </tr>
    <tr>
      <td>SAP HANA</td>
      <td>コンテナー</td>
      <td>サーバー</td>
      <td>
        <font size=2> Protocol: sap-hana-sql <br>Authentication: {protocol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー </font>
      </td>
    </tr>
    <tr>
      <td>SAP HANA</td>
      <td>テーブル</td>
      <td>表示</td>
      <td>
        <font size=2> Protocol: sap-hana-sql <br>Authentication: {protocol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; スキーマ <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト </font>
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>テーブル</td>
      <td>一覧表示</td>
      <td>
        <font size=2> Protocol: sharepoint-list <br>Authentication: {basic, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>コマンド</td>
      <td>ストアド プロシージャ</td>
      <td>
        <font size=2> Protocol: tds <br>Authentication: {protocol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; スキーマ <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>TableValuedFunction</td>
      <td>テーブル値関数</td>
      <td>
        <font size=2> Protocol: tds <br>Authentication: {protocol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; スキーマ <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> Protocol: tds <br>Authentication: {protocol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>テーブル</td>
      <td>テーブル、ビュー</td>
      <td>
        <font size=2> Protocol: tds <br>Authentication: {protocol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; スキーマ <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>コマンド</td>
      <td>ストアド プロシージャ</td>
      <td>
        <font size=2> Protocol: tds <br>Authentication: {protocol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; スキーマ <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>テーブル値関数</td>
      <td>
        <font size=2> Protocol: tds <br>Authentication: {protocol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; スキーマ <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> Protocol: tds <br>Authentication: {protocol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>テーブル</td>
      <td>テーブル、ビュー</td>
      <td>
        <font size=2> Protocol: tds <br>Authentication: {protocol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; スキーマ <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 多次元</td>
      <td>コンテナー</td>
      <td>モデル</td>
      <td>
        <font size=2> Protocol: analysis-services <br>Authentication: {windows, basic, anonymous, none} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; モデル </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 多次元</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> Protocol: analysis-services <br>Authentication: {windows, basic, anonymous, none} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; モデル <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 多次元</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        <font size=2> Protocol: analysis-services <br>Authentication: {windows, basic, anonymous, none} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; モデル <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Measure} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 多次元</td>
      <td>テーブル</td>
      <td>Dimension</td>
      <td>
        <font size=2> Protocol: analysis-services <br>Authentication: {windows, basic, anonymous, none} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; モデル <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Dimension} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 表形式</td>
      <td>コンテナー</td>
      <td>モデル</td>
      <td>
        <font size=2> Protocol: analysis-services <br>Authentication: {windows, basic, anonymous, none} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; モデル </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 表形式</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> Protocol: analysis-services <br>Authentication: {windows, basic, anonymous, none} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; モデル <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 表形式</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        <font size=2> Protocol: analysis-services <br>Authentication: {windows, basic, anonymous, none} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; モデル <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Measure} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 表形式</td>
      <td>テーブル</td>
      <td>テーブル</td>
      <td>
        <font size=2> Protocol: analysis-services <br>Authentication: {windows, basic, anonymous, none} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; モデル <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Table} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>コンテナー</td>
      <td>サーバー</td>
      <td>
        <font size=2> Protocol: reporting-services <br>Authentication: {windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>レポート</td>
      <td>レポート</td>
      <td>
        <font size=2> Protocol: reporting-services <br>Authentication: {windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; パス <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> Protocol: teradata <br>Authentication: {protocol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>テーブル</td>
      <td>テーブル、ビュー</td>
      <td>
        <font size=2> Protocol: teradata <br>Authentication: {protocol, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>コンテナー</td>
      <td>モデル</td>
      <td>
        <font size="2"> Protocol: mssql-mds <br>Authentication: {windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; モデル <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; バージョン </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>テーブル</td>
      <td>エンティティ</td>
      <td>
        <font size="2"> Protocol: mssql-mds <br>Authentication: {windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; モデル <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; バージョン <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; エンティティ </font>
      </td>
    </tr>
    <tr>
      <td>Azure Cosmos DB</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> Protocol: document-db <br>Authentication: {azure-access-key} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース </font>
      </td>
    </tr>
    <tr>
      <td>Azure Cosmos DB</td>
      <td>コレクション</td>
      <td>コレクション</td>
      <td>
        <font size=2> Protocol: document-db <br>Authentication: {azure-access-key} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; コレクション </font>
      </td>
    </tr>
    <tr>
      <td>汎用 ODBC</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> Protocol: odbc <br>Authentication: {basic, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オプション <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース </font>
      </td>
    </tr>
    <tr>
      <td>汎用 ODBC</td>
      <td>テーブル</td>
      <td>テーブル、ビュー</td>
      <td>
        <font size=2> Protocol: odbc <br>Authentication: {basic, windows} <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オプション <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; スキーマ </font>
      </td>
    </tr>
    <tr>
      <td>Sybase</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2>プロトコル: sybase <br>authentication: {basic, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース </font>
      </td>
    </tr>
    <tr>
      <td>Sybase</td>
      <td>テーブル</td>
      <td>テーブル、ビュー</td>
      <td>
        <font size=2>プロトコル: sybase <br>authentication: {basic, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; スキーマ <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト </font>
      </td>
    </tr>
    <tr>
      <td>その他 (上記以外)</td>
      <td>\*</td>
      <td>\*</td>
      <td>
        <font size=2> Protocol: generic-asset <br>Address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; assetId </font>
      </td>
    </tr>
</table>
