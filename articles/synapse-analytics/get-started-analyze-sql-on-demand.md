---
title: チュートリアル:SQL オンデマンドを使用したデータ分析の概要
description: このチュートリアルでは、Spark データベースに格納されたデータを使用して、SQL オンデマンドでデータを分析する方法について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 4011cd93879d9203d8231f24bbf531d14e6e815a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093820"
---
# <a name="analyze-data-with-sql-on-demand"></a>SQL オンデマンドを使用してデータを分析する

このチュートリアルでは、Spark データベースに格納されたデータを使用して、SQL オンデマンドでデータを分析する方法について説明します。 

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>SQL オンデマンドを使用して Spark データベース内の NYC タクシーのデータを分析する

Spark データベース内のテーブルは自動的に表示され、SQL オンデマンドによるクエリが可能です。

1. Synapse Studio で **[開発]** ハブに移動し、新しい SQL スクリプトを作成します。
1. **[接続先]** を **[SQL on-demand]\(SQL オンデマンド\)** に設定します。
1. スクリプトに次のテキストを貼り付けて、スクリプトを実行します。

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > SQL オンデマンドを使用するクエリを初めて実行する場合は、クエリの実行に必要な SQL リソースを SQL オンデマンドで収集するのに 10 秒ほどかかります。 それ以降のクエリの実行時間は大幅に短縮されます。
  


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Spark を使用して分析する](get-started-analyze-spark.md)
