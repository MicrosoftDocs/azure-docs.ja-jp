---
title: "Azure Analysis Services でサポートされるデータ ソース | Microsoft Docs"
description: "Azure Analysis Services のデータ モデルでサポートされるデータ ソースについて説明します。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 6ec63319-ff9b-4b01-a1cd-274481dc8995
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/15/2017
ms.author: owend
ms.openlocfilehash: 8bd6c3b5a923ce2f3cd0f60af82e59c5cc27cbb4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Azure Analysis Services でサポートされるデータ ソース
Azure Analysis Services サーバーでは、組織のクラウドおよびオンプレミスにあるデータ ソースへの接続がサポートされます。 サポート対象のデータ ソースは随時追加されていますので、 こちらを頻繁にご確認ください。 

現在、次のデータ ソースがサポートされています。

| クラウド  |
|---|
| Azure Blob Storage*  |
| Azure SQL Database  |
| Azure データ ウェアハウス |


| オンプレミスの  |   |   |   |
|---|---|---|---|
| Access データベース  | フォルダー* | Oracle Database  | Teradata データベース |
| Active Directory*  | JSON ドキュメント*  | Postgre SQL データベース*  |XML テーブル* |
| Analysis Services  | バイナリからの行*  | SAP HANA*  |
| Analytics Platform System  | MySQL Database  | SAP Business Warehouse*  | |
| Dynamics CRM*  | OData フィード*  | SharePoint*  |
| Excel ブック  | ODBC クエリ  | SQL Database  |
| Exchange*  | OLE DB  | Sybase データベース  |

\* 表形式 1400 モデルのみ。 

> [!IMPORTANT]
> オンプレミスのデータ ソースに接続するには、ご利用の環境内のコンピューターに[オンプレミスのデータ ゲートウェイ](analysis-services-gateway.md)がインストールされている必要があります。

## <a name="data-providers"></a>データ プロバイダー

Azure Analysis Services のデータ モデルでは、特定のデータ ソースに接続するときに異なるデータ プロバイダーが必要になる場合があります。 場合によっては、SQL Server Native Client (SQLNCLI11) などのネイティブ プロバイダーを使ってデータ ソースに接続する表形式モデルがエラーを返すことがあります。

Azure SQL Database などのクラウド データ ソースに接続するデータ モデルでは、SQLOLEDB 以外のネイティブ プロバイダーを使用すると、**"プロバイダー 'SQLNCLI11.1' は登録されていません"** というエラー メッセージが表示される場合があります。 または、オンプレミスのデータ ソースに接続する直接クエリ モデルがある場合、ネイティブ プロバイダーを使うと、**"Error creating OLE DB row set.Incorrect syntax near 'LIMIT'"** (OLE DB 行セットの作成でエラーが発生しました。'LIMIT' の周辺に正しくない構文があります") というエラー メッセージが表示される場合があります。

クラウドまたはオンプレミスのデータ ソースに接続する場合、メモリ内データ モデルまたは DirectQuery データ モデルに対しては次のデータ ソース プロバイダーがサポートされます。

### <a name="cloud"></a>クラウド
| **データ ソース** | **メモリ内** | **DirectQuery** |
|  --- | --- | --- |
| Azure SQL Data Warehouse |SQL Server 用の .NET Framework データ プロバイダー |SQL Server 用の .NET Framework データ プロバイダー |
| Azure SQL Database |SQL Server 用の .NET Framework データ プロバイダー |SQL Server 用の .NET Framework データ プロバイダー | |

### <a name="on-premises-via-gateway"></a>オンプレミス (ゲートウェイ経由)
|**データ ソース** | **メモリ内** | **DirectQuery** |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0 |SQL Server 用の .NET Framework データ プロバイダー |
| SQL Server |Microsoft OLE DB Provider for SQL Server |SQL Server 用の .NET Framework データ プロバイダー | |
| SQL Server |SQL Server 用の .NET Framework データ プロバイダー |SQL Server 用の .NET Framework データ プロバイダー | |
| Oracle |Microsoft OLE DB Provider for Oracle |Oracle Data Provider for .NET | |
| Oracle |Oracle Data Provider for .NET |Oracle Data Provider for .NET | |
| Teradata |OLE DB Provider for Teradata |Teradata Data Provider for .NET | |
| Teradata |Teradata Data Provider for .NET |Teradata Data Provider for .NET | |
| Analytics Platform System |SQL Server 用の .NET Framework データ プロバイダー |SQL Server 用の .NET Framework データ プロバイダー | |

> [!NOTE]
> オンプレミスのゲートウェイを使う場合は、64 ビット プロバイダーがインストールされていることを確認します。
> 
> 

オンプレミスの SQL Server Analysis Services 表形式モデルを Azure Analysis Services に移行する場合は、プロバイダーの変更が必要になることがあります。

**データ ソース プロバイダーを指定するには**

1. SSDT > **表形式モデル エクスプローラー** > **[データ ソース]** で、データ ソース接続を右クリックし、**[データ ソースの編集]** をクリックします。
2. **[接続の編集]** で、**[詳細]** をクリックして詳細プロパティ ウィンドウを開きます。
3. **[詳細プロパティの設定]** > **[プロバイダー]** で、適切なプロバイダーを選びます。

## <a name="impersonation"></a>権限の借用
場合によっては、異なる権限借用アカウントの指定が必要になることがあります。 権限借用アカウントは SSDT または SSMS でを指定できます。

オンプレミスのデータ ソースの場合:

* SQL 認証を使っている場合、権限借用にはサービス アカウントを使う必要があります。
* Windows 認証を使っている場合は、Windows ユーザーとパスワードを設定します。 SQL Server では、特定の権限借用アカウントでの Windows 認証は、メモリ内データ モデルに対してのみサポートされます。

クラウド データ ソースの場合:

* SQL 認証を使っている場合、権限借用にはサービス アカウントを使う必要があります。

## <a name="next-steps"></a>次のステップ
オンプレミスのデータ ソースがある場合は、[オンプレミスのゲートウェイ](analysis-services-gateway.md)をインストールする必要があります。   
SSDT または SSMS でのサーバーの管理について詳しくは、[サーバーの管理に関する記事](analysis-services-manage.md)をご覧ください。

