---
title: Synapse Studio を使用して Apache Spark アプリケーションを監視する
description: Synapse Studio を使用して Apache Spark アプリケーションを監視します。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 4d6c50436ddf68e2610aeb10ddfaaab0a5d060f3
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387355"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Synapse Studio を使用して Apache Spark アプリケーションを監視する

Azure Synapse Analytics では、Apache Spark を使用して、ワークスペース内の Apache Spark プールでノートブック、ジョブ、およびその他の種類のアプリケーションを実行できます。

この記事では、Apache Spark アプリケーションを監視する方法を説明し、最新の状態、問題、および進捗状況を監視できるようにします。

このチュートリアルに含まれるタスクは次のとおりです。

* 実行中の Apache Spark アプリケーションを監視する
* 完了した Apache Spark アプリケーションを表示する
* 取り消された Apache Spark アプリケーションを表示する
* 失敗した Apache Spark アプリケーションをデバッグする

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、次の要件を満たしてください。

- Synapse Studio ワークスペース。 手順については、[Synapse Studio ワークスペースの作成](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace#create-a-workspace)に関する記事を参照してください。

- Apache Spark プール。

## <a name="monitor-running-apache-spark-application"></a>実行中の Apache Spark アプリケーションを監視する

**[モニター]** を開き、次に **[Apache Spark アプリケーション]** を選択します。 実行中の Apache Spark アプリケーションの詳細を表示するには、送信している Apache Spark アプリケーションを選択し、詳細を表示します。 Apache Spark アプリケーションがまだ実行中の場合は、進捗状況を監視できます。

  ![実行中のジョブを選択する](./media/how-to-monitor-spark-applications/select-running-job.png)

1. **完了したタスク**、**状態**、および**合計実行時間**を確認します。

2. Apache Spark アプリケーションを取り消します。

3. ログ クエリを更新します。

4. グラフを表示します。

5. **概要**情報を確認します。

6. **ログ**を確認します。 ログ情報は、実行中は空です。

    ![実行中のジョブを表示する](./media/how-to-monitor-spark-applications/view-running-job.png)

## <a name="view-completed-apache-spark-application"></a>完了した Apache Spark アプリケーションを表示する

**[モニター]** を開き、次に **[Apache Spark アプリケーション]** を選択します。 完了した Apache Spark アプリケーションの詳細を表示するには、Apache Spark アプリケーションを選択し、詳細を表示します。

  ![完了したジョブを選択する](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. **完了したタスク**、**状態**、および**合計実行時間**を確認します。

2. ログ クエリを更新します。

3. **[Spark History Server]** をクリックして、Apache Spark History Server のリンクを開きます。

4. グラフ内のアイコンをクリックして、**概要**情報を確認します。

5. **ログ**を確認します。 ドロップダウン リストからさまざまな種類のログを選択できます。また、 **[ログのダウンロード]** をクリックすることで、ログ情報をダウンロードできます。

6. 生成されたジョブ グラフで、ジョブの概要を表示できます。 既定で、グラフにはすべてのジョブが表示されます。 **ジョブ ID**で、このビューをフィルター処理できます。

7. 既定で、 **[進行状況]** 表示が選択されています。 **[表示]** ドロップダウン リストで **[読み取り]** または **[書き込み]** を選択して、データ フローを確認できます。

8. ジョブを再生するには、 **[再生]** を選択します。 いつでも **[停止]** を選択して停止できます。

9. ジョブ グラフを拡大または縮小するために、マウス スクロールを使用するか、画面に合わせるために、 **[ウィンドウのサイズに合わせて大きさを変更]** を選択します。

10. ジョブ グラフ ノードには、各ステージの次の情報が表示されます。

    * ID。

    * 名前または説明。

    * 合計タスク数。

    * データの読み取り: 入力サイズとシャッフル読み取りサイズの合計。

    * データ書き込み: 出力サイズとシャッフル書き込みサイズの合計。

    * 実行時間: 最初の試行の開始時刻と最後の試行の完了時刻の間の時間。

    * 行数: 入力レコード、出力レコード、シャッフル読み取りレコード、シャッフル書き込みレコードの合計。

    * 進行状況。

     ![完了したジョブを表示する](./media/how-to-monitor-spark-applications/view-completed-job.png)
    
11. グラフをクリックすると、ステージの詳細が表示されます。

   ![ステージの詳細](./media/how-to-monitor-spark-applications/details-for-stage.png)

## <a name="view-canceled-apache-spark-application"></a>取り消された Apache Spark アプリケーションを表示する

**[モニター]** を開き、次に **[Apache Spark アプリケーション]** を選択します。 取り消された Apache Spark アプリケーションの詳細を表示するには、Apache Spark アプリケーションを選択し、詳細を表示します。

 ![取り消されたジョブを選択する](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. **完了したタスク**、**状態**、および**合計実行時間**を確認します。

2. ログ クエリを更新します。

3. **[Spark History Server]** をクリックして、Apache Spark History Server のリンクを開きます。

4. グラフを表示します。

5. **概要**情報を確認します。

6. **ログ**を確認します。 ドロップダウン リストからさまざまな種類のログを選択できます。また、 **[ログのダウンロード]** をクリックすることで、ログ情報をダウンロードできます。

   ![取り消されたジョブを表示する](./media/how-to-monitor-spark-applications/view-cancelled-job.png)

## <a name="debug-failed-apache-spark-application"></a>失敗した Apache Spark アプリケーションをデバッグする

**[モニター]** を開き、次に **[Apache Spark アプリケーション]** を選択します。 失敗した Apache Spark アプリケーションの詳細を表示するには、Apache Spark アプリケーションを選択し、詳細を表示します。

![失敗したジョブを選択する](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. **完了したタスク**、**状態**、および**合計実行時間**を確認します。

2. ログ クエリを更新します。

3. **[Spark History Server]** をクリックして、Apache Spark History Server のリンクを開きます。

4. グラフを表示します。

5. **概要**情報を確認します。

6. エラー情報を確認します。

   ![失敗したジョブ情報](./media/how-to-monitor-spark-applications/failed-job-info.png)

## <a name="next-steps"></a>次のステップ

パイプラインの実行の監視に関する詳細については、[Synapse Studio を使用したパイプラインの実行の監視](how-to-monitor-pipeline-runs.md)に関する記事を参照してください。  
