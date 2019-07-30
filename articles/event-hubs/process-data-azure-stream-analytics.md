---
title: Azure に Stream Analytics を使用して Event Hubs からのデータを処理する | Microsoft Docs
description: この記事では、Azure Stream Analytics のジョブを使用して、Azure イベント ハブからのデータを処理する方法を示します。
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: f179687b0983e145244e228a3d3b06b4eabead48
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723327"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Azure Stream Analytics を使用してイベント ハブからのデータを処理する
Azure Stream Analytics サービスを利用すると、簡単に Azure Event Hubs からストリーミング データを取り込み、処理し、分析できるようになり、優れた分析情報を利用してリアルタイム操作を始動できます。 この統合によって、ホットパス分析パイプラインの迅速な作成が可能になります。 Azure portal を使用して、受信データを視覚化し、Stream Analytics クエリを記述できます。 クエリを準備できたら、数回のクリック操作だけで運用環境に移行できます。 

## <a name="key-benefits"></a>主な利点
Azure Event Hubs と Azure Stream Analytics の統合の主な利点を次に示します。 
- **データのプレビュー** – Azure portal 内でイベント ハブからの受信データをプレビューできます。
- **クエリのテスト** – 変換クエリを準備して、Azure portal 内で直接テストします。 クエリ言語の構文については、[Stream Analytics クエリ言語](/stream-analytics-query/built-in-functions-azure-stream-analytics)に関するドキュメントをご覧ください。
- **運用環境へのクエリのデプロイ** – Azure Stream Analytics ジョブを作成して起動することで、クエリを運用環境にデプロイできます。

## <a name="end-to-end-flow"></a>エンド ツー エンドのフロー

1. [Azure Portal](https://portal.azure.com) にサインインします。 
1. **Event Hubs の名前空間**に移動して、その**イベント ハブ**に移動します。受信データが保持されています。 
1. イベント ハブのページ上で **[データの処理]** を選択します。  

    ![[データの処理] タイル](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. **[Enable real-time insights from events]\(イベントからのリアルタイム分析を有効にする\)** タイル上で **[探索]** を選択します。 

    ![Stream Analytics の選択](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. 次のフィールドに値が既に設定された状態で、クエリ ページが表示されます。
    1. クエリへの入力としての**イベント ハブ**。
    1. SELECT ステートメントによるサンプルの **SQL クエリ**。 
    1. テスト結果を参照するための**出力**のエイリアス。 

        ![クエリ エディター](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  初めてこの機能を使用するとき、このページでは、お使いのイベント ハブによって受信データをプレビューするために、コンシューマー グループとポリシーを作成するためのアクセス許可が求められます。
1. 前の画像に示したように、 **[入力のプレビュー]** ウィンドウにある **[作成]** を選択します。 
1. このタブ内に最新の受信データのスナップショットが、すぐに表示されます。
    - お使いのデータでのシリアル化の種類が、自動検出されます (JSON/CSV)。 JSON/CSV/AVRO に手動で変更することも可能です。
    - 表形式または未加工の形式で受信データをプレビューできます。 
    - 表示されたデータが最新でない場合、 **[更新]** を選択して最新のイベントを表示します。 

        **表形式**でのデータの例を次に示します。 ![表形式での結果](./media/process-data-azure-stream-analytics/snapshot-results.png)

        **未加工の形式**でのデータの例を次に示します。 

        ![未加工の形式での結果](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. **[クエリのテスト]** を選択して、 **[テスト結果]** タブにクエリのテスト結果のスナップショットを表示します。結果をダウンロードすることもできます。

    ![クエリのテスト結果](./media/process-data-azure-stream-analytics/test-results.png)
1. 独自のクエリを作成してデータを変換します。 「[Stream Analytics Query Language reference (Stream Analytics クエリ言語リファレンス)](/stream-analytics-query/stream-analytics-query-language-reference)」をご覧ください。
1. クエリをテストし終えて、運用環境に移行するには、 **[Deploy query]\(クエリのデプロイ\)** を選択します。 クエリをデプロイするには、ジョブ用に出力を設定してそのジョブを開始できる Azure Stream Analytics ジョブを作成します。 Stream Analytics ジョブを作成するには、そのジョブの名前を指定して、 **[作成]** を選択します。

      ![Azure Stream Analytics ジョブの作成](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  [Event Hubs] ページから作成する新しい各 Azure Stream Analytics ジョブに対してコンシューマー グループとポリシーを作成することをお勧めします。 コンシューマー グループでは、同時実行の閲覧者は 5 人しか許可されないので、ジョブごとに専用のコンシューマー グループを指定することで、その上限を超過した場合に発生するエラーを回避します。 専用ポリシーを利用すると、他のリソースに影響を及ぼさずに、キーを交代で利用したりアクセス許可を取り消したりできます。 
1. これで、クエリがテストしたものと同一で、入力がお使いのイベント ハブになっているStream Analytics ジョブが作成されました。 

9.  パイプラインを完了するには、クエリの **[出力]** を設定し、 **[開始]** を選択してジョブを開始します。

    > [!NOTE]
    > ジョブを開始する前に、outputalias を Azure Stream Analytics 上で作成した出力名に置き換えることを忘れないでください。

      ![出力を設定してジョブを開始する](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>既知の制限事項
クエリをテストしている間、テスト結果の読み込みには約 6 秒かかります。 Microsoft では、テストのパフォーマンス向上に取り組んでいます。 しかし、運用環境にデプロイされると、Azure Stream Analytics に秒未満の待機時間が発生します。

## <a name="streaming-units"></a>[ストリーミング ユニット]
Azure Stream Analytics ジョブは既定で、3 つのストリーミング ユニット (SU) になっています。 この設定を調整するには、Azure portal の **[Stream Analytics ジョブ]** 上で、左側のメニューにある **[スケール]** を選択します。 ストリーミング ユニットの詳細を確認するには、「[ストリーミング ユニットの理解と調整](../stream-analytics/stream-analytics-streaming-unit-consumption.md)」をご覧ください。

![ストリーミング ユニットのスケール](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>次の手順
Stream Analytics クエリの詳細を確認するには、[Stream Analytics クエリ言語](/stream-analytics-query/built-in-functions-azure-stream-analytics)に関するページをご覧ください