---
title: Power BI Professional への接続
description: このチュートリアルでは、Power BI Desktop を SQL オンデマンド (プレビュー) に接続する手順について説明します。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 497c6f669699f1e1ce879c0c7da2593256b97448
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87501101"
---
# <a name="connect-to-synapse-sql-with-power-bi-professional"></a>Power BI Professional で Synapse SQL に接続する

> [!div class="op_single_selector"]
>
> - [Azure Data Studio](get-started-azure-data-studio.md)
> - [Power BI](get-started-power-bi-professional.md)
> - [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> - [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> - [SSMS](get-started-ssms.md)

このチュートリアルでは、Power BI Desktop を SQL オンデマンド (プレビュー) に接続する手順について説明します。

## <a name="prerequisites"></a>前提条件

クエリを発行するツール:

- 任意の SQL クライアント:

  - Azure Data Studio
  - SQL Server Management Studio

- インストール済みの Power BI Desktop

パラメーター:

| パラメーター                                 | 説明                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| SQL オンデマンド サービス エンドポイント アドレス    | サーバー名として使用されます                                   |
| SQL オンデマンド サービス エンドポイント リージョン     | サンプルで使用するストレージを決定するために使用されます |
| エンドポイント アクセスのユーザー名とパスワード | エンドポイントへのアクセスに使用されます                               |
| ビューの作成に使用するデータベース     | このデータベースはサンプルの開始点として使用されます       |

## <a name="first-time-setup"></a>初回セットアップ

サンプルを使用する前に、次の 2 つの手順を実行します。

1. ビューのデータベースを作成します
2. SQL オンデマンドがストレージ内のファイルにアクセスするために使用する資格情報を作成します

### <a name="create-database"></a>データベースの作成

この入門記事では、デモとして使用する独自のデータベースを作成する必要があります。 データベースは、ビューを作成するために必要です。 このデータベースは、このドキュメントの一部のサンプル クエリで使用します。

> [!NOTE]
> データベースは、実際のデータではなくメタデータを表示するためにのみ使用されます。
>
> 使用するデータベース名を書き留めておきます。後で必要になります。

```sql
DROP DATABASE IF EXISTS demo;
```

### <a name="create-credentials"></a>資格情報を作成する

クエリを実行する前に、資格情報を作成する必要があります。 この資格情報は、SQL オンデマンド サービスがストレージ内のファイルにアクセスするために使用されます。

> [!NOTE]
> ストレージ アカウントにアクセスするための資格情報を作成する必要があります。 SQL オンデマンドは異なるリージョンからストレージにアクセスできますが、ストレージと Azure Synapse ワークスペースを同じリージョンにすると、パフォーマンスが向上します。

**国勢調査データ コンテナーの資格情報を作成する方法についてのコード スニペット**:

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need secret
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = '';
GO
```

## <a name="create-a-power-bi-desktop-report"></a>Power BI Desktop レポートを作成する

Power BI Desktop アプリケーションを開き、 **[データの取得]** オプションを選択します。

![Power BI Desktop アプリケーションを開き、[データの取得] を選択する。](./media/get-started-power-bi-professional/step-0-open-powerbi.png)

### <a name="step-1---select-data-source"></a>ステップ 1 - データ ソースの選択

メニューから **[Azure]** を選択し、次に **[Azure SQL Database]** を選択します。
![データ ソースを選択する。](./media/get-started-power-bi-professional/step-1-select-data-source.png)

### <a name="step-2---select-database"></a>ステップ 2 - データベースの選択

ビューが存在するデータベースの URL とデータベースの名前を書き込みます。
![エンドポイントでデータベースを選択する。](./media/get-started-power-bi-professional/step-2-db.png)

## <a name="next-steps"></a>次のステップ

Azure Data Studio を使用して SQL オンデマンドに接続する方法については、[ストレージ ファイルに対するクエリ](get-started-azure-data-studio.md)に関する記事に進んでください。
 