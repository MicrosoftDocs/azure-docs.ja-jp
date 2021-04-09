---
title: 'チュートリアル: サーバーレス SQL プールを使用したデータ分析の概要'
description: このチュートリアルでは、Spark データベース内のデータを使用して、サーバーレス SQL プールでデータを分析する方法について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 5f0a7477df2e281748c053ea8c7e7d3e79626296
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588020"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>サーバーレス SQL プールを使用してデータを分析する

このチュートリアルでは、Spark データベース内のデータを使用して、サーバーレス SQL プールでデータを分析する方法について説明します。 

## <a name="the-built-in-serverless-sql-pool"></a>組み込みのサーバーレス SQL プール

サーバーレス SQL プールでは、容量を予約せずに SQL を使用できます。 サーバーレス SQL プールの課金は、クエリの実行に使用されたノードの数ではなく、クエリを実行するために処理されたデータの量に基づきます。

各ワークスペースは、**組み込み** と呼ばれる、事前構成されたサーバーレス SQL プールを備えています。 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>サーバーレス SQL プールを使用して Blob Storage の NYC タクシー データを分析する

このセクションでは、サーバーレス SQL プールを使用して、Azure Blob Storage アカウント内の NYC タクシーのデータを分析します。

1. Synapse Studio で、 **[開発]** ハブに移動します。
1. 新しい SQL スクリプトを作成します。
1. 以下のコードをスクリプトに貼り付けます。

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. **[実行]**

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [サーバーレス Spark プールを使用してデータを分析する](get-started-analyze-spark.md)
