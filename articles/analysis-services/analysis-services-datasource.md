---
title: "データ ソースの接続 | Microsoft Docs"
description: "Azure Analysis Services のデータ モデルへのデータ ソースの接続について説明します。"
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
ms.date: 02/27/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: a287ebd634a9305229424d0efea266146f88a952
ms.openlocfilehash: 234032630cb3911deb7c7d32cfc4963ad6aee43f
ms.lasthandoff: 01/18/2017


---
# <a name="datasource-connections"></a>データ ソースの接続
Azure Analysis Services のデータ モデルでは、特定のデータ ソースに接続するときに異なるデータ プロバイダーが必要になる場合があります。 場合によっては、SQL Server Native Client (SQLNCLI11) などのネイティブ プロバイダーを使ってデータ ソースに接続する表形式モデルがエラーを返すことがあります。

たとえば、Azure SQL Database などのクラウド データ ソースに接続するメモリ内データ モデルまたは直接クエリ データ モデルがある場合、SQLOLEDB 以外のネイティブ プロバイダーを使うと、**"The provider 'SQLNCLI11.1' is not registered"** (プロバイダー 'SQLNCLI11.1' は登録されていません) というエラー メッセージが表示される場合があります。

または、オンプレミスのデータ ソースに接続する直接クエリ モデルがある場合、ネイティブ プロバイダーを使うと、**"Error creating OLE DB row set.Incorrect syntax near 'LIMIT'"** (OLE DB 行セットの作成でエラーが発生しました。'LIMIT' の周辺に正しくない構文があります") というエラー メッセージが表示される場合があります。

## <a name="data-source-providers"></a>データ ソース プロバイダー
クラウドまたはオンプレミスのデータ ソースに接続する場合、メモリ内データ モデルまたは直接クエリ データ モデルに対しては次のデータ ソース プロバイダーがサポートされます。

### <a name="cloud"></a>クラウド
| **データ ソース** | **メモリ内** | **直接クエリ** |
|  --- | --- | --- |
| Azure SQL Data Warehouse |SQL Server 用の .NET Framework データ プロバイダー |SQL Server 用の .NET Framework データ プロバイダー |
| Azure SQL Database |SQL Server 用の .NET Framework データ プロバイダー |SQL Server 用の .NET Framework データ プロバイダー | |

### <a name="on-premises-via-gateway"></a>オンプレミス (ゲートウェイ経由)
|**データ ソース** | **メモリ内** | **直接クエリ** |
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
オンプレミスのデータ ソースがある場合は、[オンプレミスのゲートウェイ](analysis-services-gateway.md)をインストールする必要があります。 SSDT または SSMS でのサーバーの管理について詳しくは、[サーバーの管理に関する記事](analysis-services-manage.md)をご覧ください。


