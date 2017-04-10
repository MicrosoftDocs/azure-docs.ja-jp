---
title: "Azure Data Catalog でサポートされるデータ ソース | Microsoft Docs"
description: "現在サポートされているデータ ソースの仕様。"
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
ms.date: 03/14/2017
ms.author: maroche
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: b92db8dd81af0c58861644cc02ca9e56b6a6a832
ms.lasthandoff: 03/17/2017


---

# <a name="azure-data-catalog-supported-data-sources"></a>Azure Data Catalog でサポートされるデータ ソース

ユーザーは、ワンクリック登録ツールのパブリック API を使って、または Data Catalog の Web ポータルに直接情報を手動で入力して、メタデータを公開できます。 以下の表は、現在カタログでサポートされているすべてのソースと、それぞれの公開機能をまとめたものです。  また、各ソースを当社のポータルから「オープンイン」で起動できる外部ツールも一覧表示されています。 2 番目の表には、各データ ソースの接続プロパティの詳細な技術仕様があります。


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
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure Data Lake Store のファイル</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure Storage BLOB</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure Storage ディレクトリ</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure Storage Table</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td>
        <font size="2"></font>
      </td>
      <td>
        <font size="2"></font>
      </td>
    </tr>

    <tr>
      <td>HDFS ディレクトリ</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>HDFS ファイル</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Hive テーブル</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Hive ビュー</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>MySQL テーブル</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>MySQL ビュー</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Oracle Database テーブル</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Oracle Database ビュー</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>その他 (汎用アセット)</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Data Warehouse テーブル</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI、SQL Server データ ツール</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Data Warehouse ビュー</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI、SQL Server データ ツール</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services ディメンション</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services KPI</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services メジャー</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services テーブル</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Reporting Services レポート</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>ブラウザー</font></td>
      <td><font size=2>ネイティブ モードのサーバーのみ。SharePoint モードはサポートされていません。</font></td>
    </tr>

    <tr>
      <td>SQL Server テーブル</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI、SQL Server データ ツール</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server ビュー</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI、SQL Server データ ツール</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Teradata テーブル</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Teradata ビュー</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SAP Hana ビュー</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Db2 テーブル</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Db2 ビュー</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>ファイル システム ファイル</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Ftp ディレクトリ</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Ftp ファイル</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Http レポート</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Http エンドポイント</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Http ファイル</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Odata エンティティ セット</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Odata 関数</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Postgresql テーブル</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Postgresql ビュー</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SAP Hana ビュー</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td> Salesforce オブジェクト</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SharePoint リスト </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
  
    <tr>
      <td>Azure DocumentDB コレクション</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

</table>

その他のソースのサポートが必要な場合は、 [Azure Data Catalog フォーラム](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)を使用して機能のリクエストを送信してください。


<br>
<br>
## <a name="data-source-reference-specification"></a>データ ソース参照の仕様
> [!NOTE]
> 次の表の「DSL 構造体」列には、Azure Data Catalog で使われる "address" プロパティ バッグの接続プロパティの一覧だけが示されています。 つまり、"address" プロパティ バッグは、Azure Data Catalog が永続化しても使わないデータ ソースの他の接続プロパティを含むことができます。
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
        <font size=2> protocol: webhdfs <br>authentication: {basic, oauth} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>テーブル</td>
      <td>ディレクトリ、ファイル</td>
      <td>
        <font size=2> protocol: webhdfs <br>authentication: {basic, oauth} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage (Azure Storage)</td>
      <td>コンテナー</td>
      <td>コンテナー</td>
      <td>
        <font size=2> protocol: azure-blobs <br>authentication: {azure-access-key} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ドメイン <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; アカウント <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; コンテナー </font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage (Azure Storage)</td>
      <td>テーブル</td>
      <td>BLOB、ディレクトリ</td>
      <td>
        <font size=2> protocol: azure-blobs <br>authentication: {azure-access-key} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ドメイン <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; アカウント <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; コンテナー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 名前 </font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage (Azure Storage)</td>
      <td>コンテナー</td>
      <td>コンテナー</td>
      <td>
        <font size=2> protocol: azure-tables <br>authentication: {azure-access-key} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ドメイン <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; アカウント </font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage (Azure Storage)</td>
      <td>テーブル</td>
      <td>テーブル</td>
      <td>
        <font size=2> protocol: azure-tables <br>authentication: {azure-access-key} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ドメイン <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; アカウント <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 名前 </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>コンテナー</td>
      <td>仮想クラスター</td>
      <td>
        <font size=2> protocol: cosmos <br>authentication: {basic, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>テーブル</td>
      <td>ストリーム、ストリーム セット、ビュー</td>
      <td>
        <font size=2> protocol: cosmos <br>authentication: {basic, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>コンテナー</td>
      <td>サイト</td>
      <td>
        <font size=2> protocol: http <br>authentication: {none, basic, windows, oauth} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>レポート</td>
      <td>レポート、ダッシュボード</td>
      <td>
        <font size=2> protocol: http <br>authentication: {none, basic, windows, oauth} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Db2</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> protocol: db2 <br>authentication: {basic, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース </font>
      </td>
    </tr>
    <tr>
      <td>Db2</td>
      <td>テーブル</td>
      <td>テーブル、ビュー</td>
      <td>
        <font size=2> protocol: db2 <br>authentication: {basic, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; スキーマ </font>
      </td>
    </tr>
    <tr>
      <td>ファイル システム</td>
      <td>テーブル</td>
      <td>ファイル</td>
      <td>
        <font size=2> protocol: file <br>authentication: {none, basic, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; パス </font>
      </td>
    </tr>
    <tr>
      <td>Ftp</td>
      <td>テーブル</td>
      <td>ディレクトリ、ファイル</td>
      <td>
        <font size=2> protocol: ftp <br>authentication: {none, basic, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Hadoop 分散ファイル システム</td>
      <td>コンテナー</td>
      <td>クラスター</td>
      <td>
        <font size=2> protocol: webhdfs <br>authentication: {basic, oauth} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Hadoop 分散ファイル システム</td>
      <td>テーブル</td>
      <td>ディレクトリ、ファイル</td>
      <td>
        <font size=2> protocol: webhdfs <br>authentication: {basic, oauth} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> protocol: hive <br>authentication: {hdinsight, basic, username, none} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>テーブル</td>
      <td>テーブル、ビュー</td>
      <td>
        <font size=2> protocol: hive <br>authentication: {hdinsight, basic, username, none} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>コンテナー</td>
      <td>サイト</td>
      <td>
        <font size=2> protocol: http <br>authentication: {none, basic, windows, oauth} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>レポート</td>
      <td>レポート、ダッシュボード</td>
      <td>
        <font size=2> protocol: http <br>authentication: {none, basic, windows, oauth} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>テーブル</td>
      <td>エンド ポイント、ファイル</td>
      <td>
        <font size=2> protocol: http <br>authentication: {none, basic, windows, oauth} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> protocol: mysql <br>authentication: {protocol, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>テーブル</td>
      <td>テーブル、ビュー</td>
      <td>
        <font size=2> protocol: mysql <br>authentication: {protocol, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト </font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>コンテナー</td>
      <td>エンティティ コンテナー</td>
      <td>
        <font size=2> protocol: odata <br>authentication: {none, basic, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>テーブル</td>
      <td>エンティティ セット、関数</td>
      <td>
        <font size=2> protocol: odata <br>authentication: {none, basic, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; リソース </font>
      </td>
    </tr>
    <tr>
      <td>Oracle Database</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> protocol: oracle <br>authentication: {protocol, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース </font>
      </td>
    </tr>
    <tr>
      <td>Oracle Database</td>
      <td>テーブル</td>
      <td>テーブル、ビュー</td>
      <td>
        <font size=2> protocol: oracle <br>authentication: {protocol, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; スキーマ <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト </font>
      </td>
    </tr>
    <tr>
      <td>Postgresql</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> protocol: postgresql <br>authentication: {basic, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース </font>
      </td>
    </tr>
    <tr>
      <td>Postgresql</td>
      <td>テーブル</td>
      <td>テーブル、ビュー</td>
      <td>
        <font size=2> protocol: postgresql <br>authentication: {basic, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; スキーマ <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>コンテナー</td>
      <td>サイト</td>
      <td>
        <font size=2> protocol: http <br>authentication: {none, basic, windows, oauth} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>レポート</td>
      <td>レポート、ダッシュボード</td>
      <td>
        <font size=2> protocol: http <br>authentication: {none, basic, windows, oauth} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Power Query</td>
      <td>テーブル</td>
      <td>データのマッシュアップ</td>
      <td>
        <font size=2> protocol: power-query <br>authentication: {oauth} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Salesforce</td>
      <td>テーブル</td>
      <td>オブジェクト</td>
      <td>
        <font size=2> protocol: salesforce com <br>authentication: {basic, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; loginServer <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; class <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; itemName </font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>コンテナー</td>
      <td>サーバー</td>
      <td>
        <font size=2> protocol: sap hana sql <br>authentication: {protocol, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー </font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>テーブル</td>
      <td>表示</td>
      <td>
        <font size=2> protocol: sap hana sql <br>authentication: {protocol, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; スキーマ <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト </font>
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>テーブル</td>
      <td>一覧表示</td>
      <td>
        <font size=2> protocol: sharepoint-list <br>authentication: {basic, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>コマンド</td>
      <td>ストアド プロシージャ</td>
      <td>
        <font size=2> protocol: tds <br>authentication: {protocol, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; スキーマ <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>TableValuedFunction</td>
      <td>テーブル値関数</td>
      <td>
        <font size=2> protocol: tds <br>authentication: {protocol, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; スキーマ <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> protocol: tds <br>authentication: {protocol, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>テーブル</td>
      <td>テーブル、ビュー</td>
      <td>
        <font size=2> protocol: tds <br>authentication: {protocol, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; スキーマ <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>コマンド</td>
      <td>ストアド プロシージャ</td>
      <td>
        <font size=2> protocol: tds <br>authentication: {protocol, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; スキーマ <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>テーブル値関数</td>
      <td>
        <font size=2> protocol: tds <br>authentication: {protocol, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; スキーマ <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> protocol: tds <br>authentication: {protocol, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>テーブル</td>
      <td>テーブル、ビュー</td>
      <td>
        <font size=2> protocol: tds <br>authentication: {protocol, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; スキーマ <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Multidimensional</td>
      <td>コンテナー</td>
      <td>モデル</td>
      <td>
        <font size=2> protocol: analysis-services <br>authentication: {windows, basic, anonymous, none} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; モデル </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Multidimensional</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> protocol: analysis-services <br>authentication: {windows, basic, anonymous, none} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; モデル <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Multidimensional</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        <font size=2> protocol: analysis-services <br>authentication: {windows, basic, anonymous, none} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; モデル <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Measure} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services Multidimensional</td>
      <td>テーブル</td>
      <td>Dimension</td>
      <td>
        <font size=2> protocol: analysis-services <br>authentication: {windows, basic, anonymous, none} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; モデル <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Dimension} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 表形式</td>
      <td>コンテナー</td>
      <td>モデル</td>
      <td>
        <font size=2> protocol: analysis-services <br>authentication: {windows, basic, anonymous, none} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; モデル </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 表形式</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> protocol: analysis-services <br>authentication: {windows, basic, anonymous, none} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; モデル <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 表形式</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        <font size=2> protocol: analysis-services <br>authentication: {windows, basic, anonymous, none} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; モデル <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Measure} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 表形式</td>
      <td>テーブル</td>
      <td>テーブル</td>
      <td>
        <font size=2> protocol: analysis-services <br>authentication: {windows, basic, anonymous, none} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; モデル <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Table} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>コンテナー</td>
      <td>サーバー</td>
      <td>
        <font size=2> protocol: reporting services <br>authentication: {windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>レポート</td>
      <td>レポート</td>
      <td>
        <font size=2> protocol: reporting services <br>authentication: {windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; パス <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> protocol: teradata <br>authentication: {protocol, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>テーブル</td>
      <td>テーブル、ビュー</td>
      <td>
        <font size=2> protocol: teradata <br>authentication: {protocol, windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; サーバー <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; オブジェクト </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>コンテナー</td>
      <td>モデル</td>
      <td>
        <font size="2"> protocol: mssql-mds <br>authentication: {windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; モデル <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; バージョン </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>テーブル</td>
      <td>エンティティ</td>
      <td>
        <font size="2"> protocol: mssql-mds <br>authentication: {windows} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; モデル <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; バージョン <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; エンティティ </font>
      </td>
    </tr>
    <tr>
      <td>Azure DocumentDB</td>
      <td>コンテナー</td>
      <td>データベース</td>
      <td>
        <font size=2> protocol: document-db <br>authentication: {azure-access-key} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース </font>
      </td>
    </tr>
    <tr>
      <td>Azure DocumentDB</td>
      <td>コレクション</td>
      <td>コレクション</td>
      <td>
        <font size=2> protocol: document-db <br>authentication: {azure-access-key} <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; データベース <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; コレクション </font>
      </td>
    </tr>
    <tr>
      <td>その他 (上記以外)</td>
      <td>\*</td>
      <td>\*</td>
      <td>
        <font size=2> protocol: generic-asset <br>address: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; assetId </font>
      </td>
    </tr>
</table>

