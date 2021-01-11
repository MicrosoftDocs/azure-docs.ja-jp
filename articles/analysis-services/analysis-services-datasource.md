---
title: Azure Analysis Services でサポートされるデータ ソース | Microsoft Docs
description: Azure Analysis Services の表形式 1200 以上のデータ モデルでサポートされるデータ ソースとコネクタについて説明します。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b99ac957c9c5030b484b244223847be4aa53a39d
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2020
ms.locfileid: "88749095"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Azure Analysis Services でサポートされるデータ ソース

Analysis Services プロジェクトを使用した Visual Studio の [データの取得] または[テーブルのインポート ウィザード] で表示されるデータ ソースとコネクタは、Azure Analysis Services と SQL Server Analysis Services の両方に対するものです。 ただし、表示されているすべてのデータ ソースとコネクタが、Azure Analysis Services でサポートされているわけではありません。 接続できるデータ ソースの種類は、モデルの互換性レベル、使用可能なデータ コネクタ、認証の種類、オンプレミスのデータ ゲートウェイのサポートなど、さまざまな要因に依存します。 次の表では、Azure Analysis Services でサポートされているデータ ソースについて説明します。

## <a name="azure-data-sources"></a>Azure データ ソース

|データ ソース  |メモリ内  |DirectQuery  |Notes |
|---------|---------|---------|---------|
|Azure SQL Database      |   はい      |    はい      |<sup>[2](#azprovider)</sup>、<sup>[3](#azsqlmanaged)</sup>|
|Azure Synapse Analytics (SQL DW)      |   はい      |   はい       |<sup>[2](#azprovider)</sup>|
|Azure Blob Storage      |   はい       |    いいえ      | <sup>[1](#tab1400a)</sup> |
|Azure Table Storage     |   はい       |    いいえ      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  はい        |  いいえ        |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen1      |   はい       |    いいえ      |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   はい       |    いいえ      |<sup>[1](#tab1400a)</sup>、<sup>[5](#gen2)</sup>|
|Azure HDInsight HDFS    |     はい     |   いいえ       |<sup>[1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   はい       |   いいえ       |<sup>[1](#tab1400a)</sup>、<sup>[4](#databricks)</sup>|
||||

**注:**

<a name="tab1400a">1</a> - 1400 以上の表形式モデルのみ。  
<a name="azprovider">2</a> - 表形式 1200 以上のモデルで "*プロバイダー*" データ ソースとして指定されている場合は、インメモリと DirectQuery の両方のモデルで、Microsoft OLE DB Driver for SQL Server MSOLEDBSQL (推奨)、SQL Server Native Client 11.0、または .NET Framework Data Provider SQL Server が必要です。  
<a name="azsqlmanaged">3</a> - Azure SQL Managed Instance がサポートされています。 SQL Managed Instance はプライベート IP アドレスを使用して Azure VNet 内で実行されるため、インスタンスでパブリックエンド ポイントを有効にする必要があります。 有効になっていない場合は、[オンプレミスのデータ ゲートウェイ](analysis-services-gateway.md)が必要です。  
<a name="databricks">4</a> - Spark コネクタを使用する Azure Databricks は現在サポートされていません。  
<a name="gen2">5</a> - ADLS Gen2 コネクタは現在サポートされていませんが、ADLS Gen2 データ ソースでは Azure Blob Storage コネクタを使用できます。

## <a name="other-data-sources"></a>他のデータ ソース

|データ ソース | メモリ内 | DirectQuery |Notes   |
|  --- | --- | --- | --- |
|Access データベース     |  はい | いいえ |  |
|Active Directory     |  はい | いいえ | <sup>[6](#tab1400b)</sup>  |
|Analysis Services     |  はい | いいえ |  |
|分析プラットフォーム システム     |  はい | いいえ |  |
|CSV ファイル  |はい | いいえ |  |
|Dynamics 365     |  はい | いいえ | <sup>[6](#tab1400b)</sup> |
|Excel ブック     |  はい | いいえ |  |
|Exchange      |  はい | いいえ | <sup>[6](#tab1400b)</sup> |
|Folder      |はい | いいえ | <sup>[6](#tab1400b)</sup> |
|IBM Informix  |はい | いいえ |  |
|JSON ドキュメント      |  はい | いいえ | <sup>[6](#tab1400b)</sup> |
|バイナリからの行      | はい | いいえ | <sup>[6](#tab1400b)</sup> |
|MySQL データベース     | はい | いいえ |  |
|OData フィード      |  はい | いいえ | <sup>[6](#tab1400b)</sup> |
|ODBC クエリ     | はい | いいえ |  |
|OLE DB (OLE DB)     |   はい | いいえ |  |
|Oracle  | はい  |はい  | <sup>[9](#oracle)</sup> |
|PostgreSQL データベース   | はい | いいえ | <sup>[6](#tab1400b)</sup> |
|Salesforce オブジェクト|  はい | いいえ | <sup>[6](#tab1400b)</sup> |
|Salesforce レポート |はい | いいえ | <sup>[6](#tab1400b)</sup> |
|SAP HANA     |  はい | いいえ |  |
|SAP Business Warehouse    |  はい | いいえ | <sup>[6](#tab1400b)</sup> |
|SharePoint リスト      |   はい | いいえ | <sup>[6](#tab1400b)</sup>、<sup>[11](#filesSP)</sup> |
|SQL Server |はい   | はい  | <sup>[7](#sqlim)</sup>、<sup>[8](#instgw)</sup> |
|SQL Server Data Warehouse |はい   | はい  | <sup>[7](#sqlim)</sup>、<sup>[8](#instgw)</sup> |
|Sybase データベース     |  はい | いいえ |  |
|Teradata | はい  | はい  | <sup>[10](#teradata)</sup> |
|TXT ファイル  |はい | いいえ |  |
|XML テーブル    |  はい | いいえ | <sup>[6](#tab1400b)</sup> |
| | | |

**注:**  
<a name="tab1400b">6</a> - 表形式 1400 以上のモデルのみ。  
<a name="sqlim">7</a> - 表形式 1200 以上のモデルで "*プロバイダー*" データ ソースとして指定されている場合は、Microsoft OLE DB Driver for SQL Server MSOLEDBSQL (推奨)、SQL Server Native Client 11.0、または .NET Framework Data Provider SQL Server を指定します。  
<a name="instgw">8</a> - データ プロバイダーとして MSOLEDBSQL を指定する場合は、オンプレミス データ ゲートウェイと同じコンピューターに、[Microsoft OLE DB Driver for SQL Server](https://docs.microsoft.com/sql/connect/oledb/oledb-driver-for-sql-server) をダウンロードしてインストールすることが必要になる場合があります。  
<a name="oracle">9</a> - 表形式 1200 モデルの場合、または表形式 1400 以上のモデルの "*プロバイダー*" データ ソースとしては、Oracle Data Provider for .NET を指定します。 構造化データ ソースとして指定した場合は、必ず [Oracle マネージド プロバイダーを有効](#enable-oracle-managed-provider)にしてください。   
<a name="teradata">10</a> - 表形式 1200 モデルの場合、または表形式 1400 以上のモデルの "*プロバイダー*" データ ソースとしては、Teradata Data Provider for .NET を指定します。  
<a name="filesSP">11</a> - オンプレミスの SharePoint 内のファイルはサポートされていません。

Azure Analysis Services サーバーからオンプレミスのデータ ソースに接続するには、[オンプレミスのゲートウェイ](analysis-services-gateway.md)が必要です。 ゲートウェイを使うときは、64 ビットのプロバイダーが必要です。

## <a name="understanding-providers"></a>プロバイダーについて

Visual Studio で表形式 1400 以上のモデル プロジェクトを作成する場合、既定では **[データの取得]** を使用してデータ ソースに接続するときにデータ プロバイダーを指定しません。 表形式 1400 以上のモデルでは、[Power Query](/power-query/power-query-what-is-power-query) コネクタを使用して、データ ソースと Analysis Services の間の接続、データ クエリ、およびマッシュアップを管理します。 これらは、接続プロパティの設定が自動で行われていることから、"*構造化*" データ ソース接続と呼ばれることもあります。 ただし、Visual Studio のモデル プロジェクトのレガシ データ ソースを有効にすることはできます。 有効にすると、 **[テーブルのインポート ウィザード]** を使用して、"*レガシ*" または "*プロバイダー*" のデータ ソースとして、表形式 1200 以下のモデルで従来サポートされていた特定のデータ ソースに接続できます。 プロバイダー データ ソースとして指定した場合は、特定のデータ プロバイダーとその他の詳細な接続プロパティを指定できます。 たとえば、SQL Server Data Warehouse インスタンスに接続することも、レガシ データ ソースとして Azure SQL Database に接続することもできます。 その後、OLE DB Driver for SQL Server MSOLEDBSQL データ プロバイダーを選択できます。 この場合、OLE DB データ プロバイダーを選択すると、Power Query コネクタよりもパフォーマンスが向上する可能性があります。 

Visual Studio で [テーブルのインポート ウィザード] を使用する場合、任意のデータ ソースへの接続にはデータ プロバイダーが必要です。 既定のデータ プロバイダーが自動的に選択されます。 必要に応じて、データ プロバイダーは変更できます。 選択するプロバイダーの種類は、パフォーマンス、モデルがインメモリ ストレージまたは DirectQuery を使用しているかどうか、モデルをデプロイする Analysis Services プラットフォームによって異なる場合があります。

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>表形式 1400 以上のモデル プロジェクトでプロバイダー データ ソースを指定する

プロバイダー データ ソースを有効にするには、Visual Studio で **[ツール]**  >  **[オプション]**  >  **[Analysis Services 表形式]**  >  **[データのインポート]** を順にクリックし、 **[レガシ データ ソースを有効にする]** を選択します。

![レガシ データ ソースを有効にする](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

レガシ データ ソースを有効にした状態で、 **[表形式モデル エクスプローラー]** で **[データ ソース]** (右クリック) >  **[データ ソースからのインポート (レガシ)]** を選択します｡

![[表形式モデル エクスプローラー] のレガシ データ ソース](media/analysis-services-datasource/aas-import-legacy-datasources.png)

表形式 1200 のモデル プロジェクトと同様に、 **[テーブルのインポート ウィザード]** を使用してデータ ソースに接続します。 接続ページで、 **[詳細設定]** をクリックします。 **[詳細プロパティの設定]** で、データ プロバイダーとその他の接続設定を指定します。

![レガシ データ ソースの詳細プロパティ](media/analysis-services-datasource/aas-import-legacy-advanced.png)

## <a name="impersonation"></a>権限借用
場合によっては、異なる権限借用アカウントの指定が必要になることがあります。 権限借用アカウントは、Visual Studio または SQL Server Management Studio (SSMS) で指定できます。

オンプレミスのデータ ソースの場合:

* SQL 認証を使っている場合、権限借用にはサービス アカウントを使う必要があります。
* Windows 認証を使っている場合は、Windows ユーザーとパスワードを設定します。 SQL Server では、特定の権限借用アカウントでの Windows 認証は、メモリ内データ モデルに対してのみサポートされます。

クラウド データ ソースの場合:

* SQL 認証を使っている場合、権限借用にはサービス アカウントを使う必要があります。

## <a name="oauth-credentials"></a>OAuth 資格情報

インメモリ モードを使用する 1400 以上の互換性レベルの表形式モデルでは、Azure SQL Database、Azure Synapse (旧称 SQL Data Warehouse)、Dynamics 365、SharePoint リストで OAuth 資格情報がサポートされています。 Azure Analysis Services では、実行時間の長い更新操作のタイムアウトを避けるために、OAuth データ ソースのトークン更新を管理します。 有効なトークンを生成するには、Power Query を使用して資格情報を設定します。

直接クエリモードは OAuth 資格情報ではサポートされていません。

## <a name="enable-oracle-managed-provider"></a>Oracle マネージド プロバイダーを有効にする

場合によっては、Oracle データ ソースに対する DAX クエリで予期しない結果が返されることがあります。 これは、データ ソース接続に使用されているプロバイダーが原因である可能性があります。

「[プロバイダーについて](#understanding-providers)」セクションで説明したように、表形式モデルは、"*構造化*" データ ソースまたは "*プロバイダー*" データ ソースとしてデータ ソースに接続します。 Oracle データ ソースがプロバイダー データ ソースとして指定されているモデルの場合は、指定されたプロバイダーが Oracle Data Provider for .NET (Oracle.DataAccess.Client) であることを確認してください。 

Oracle データ ソースが構造化データ ソースとして指定されている場合は、**MDataEngine\UseManagedOracleProvider** サーバー プロパティを有効にします。 このプロパティを設定すると、推奨される Oracle Data Provider for .NET マネージド プロバイダーを使用して、モデルが Oracle データ ソースに確実に接続されます。
 
Oracle マネージド プロバイダーを有効にするには、次のようにします。

1. SQL Server Management Studio で、お使いのサーバーに接続します。
2. 次のスクリプトを使用して、XMLA クエリを作成します。 **ServerName** を完全なサーバー名に置き換えてから、クエリを実行します。

    ```xml
    <Alter AllowCreate="true" ObjectExpansion="ObjectProperties" xmlns="http://schemas.microsoft.com/analysisservices/2003/engine">
        <Object />
        <ObjectDefinition>
            <Server xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:ddl2="http://schemas.microsoft.com/analysisservices/2003/engine/2" xmlns:ddl2_2="http://schemas.microsoft.com/analysisservices/2003/engine/2/2" 
    xmlns:ddl100_100="http://schemas.microsoft.com/analysisservices/2008/engine/100/100" xmlns:ddl200="http://schemas.microsoft.com/analysisservices/2010/engine/200" xmlns:ddl200_200="http://schemas.microsoft.com/analysisservices/2010/engine/200/200" 
    xmlns:ddl300="http://schemas.microsoft.com/analysisservices/2011/engine/300" xmlns:ddl300_300="http://schemas.microsoft.com/analysisservices/2011/engine/300/300" xmlns:ddl400="http://schemas.microsoft.com/analysisservices/2012/engine/400" 
    xmlns:ddl400_400="http://schemas.microsoft.com/analysisservices/2012/engine/400/400" xmlns:ddl500="http://schemas.microsoft.com/analysisservices/2013/engine/500" xmlns:ddl500_500="http://schemas.microsoft.com/analysisservices/2013/engine/500/500">
                <ID>ServerName</ID>
                <Name>ServerName</Name>
                <ServerProperties>
                    <ServerProperty>
                        <Name>MDataEngine\UseManagedOracleProvider</Name>
                        <Value>1</Value>
                    </ServerProperty>
                </ServerProperties>
            </Server>
        </ObjectDefinition>
    </Alter>
    ```

3. サーバーを再起動します。


## <a name="next-steps"></a>次のステップ

* [オンプレミス ゲートウェイ](analysis-services-gateway.md)
* [サーバーの管理](analysis-services-manage.md)
