---
title: アプリの発行 - LUIS
titleSuffix: Azure Cognitive Services
description: アクティブな LUIS アプリの構築とテストが終了したら、それをエンドポイントに発行して、クライアント アプリケーションが使用できるようにします。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: f92776072038c5684e9334d2dda1690ebb7bcaa8
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257811"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>アクティブでトレーニング済みのアプリをステージング エンドポイントまたは運用環境エンドポイントに発行する

アクティブな LUIS アプリの構築とテストが終了したら、それをエンドポイントに発行して、クライアント アプリケーションが使用できるようにします。 

<a name="publish-your-trained-app-to-an-http-endpoint"></a>

## <a name="publishing"></a>発行

1. エンドポイントを発行するには、右パネル上部の **[Publish]\(発行)** を選択します。 

    ![右上のナビゲーション バーの [発行] ボタン](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. 発行された予測エンドポイントの設定を選択し、 **[発行]** を選択します。

    ![発行の設定を選択し、[発行] ボタンを選択する](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>発行スロット

ポップアップ ウィンドウが表示されたら、適切なスロットを選択します。 

* ステージング。
* 運用。 

両方の発行スロットを使用することで、2 つの異なるバージョンのアプリを発行されたエンドポイントで使用できるようになります。また、2 つの異なるエンドポイントで同じバージョンを使用することもできます。 

### <a name="publishing-regions"></a>公開リージョン

このアプリは、LUIS ポータルに追加された LUIS 予測エンドポイント リソースに関連付けられているすべてのリージョンに発行されます。 

たとえば、[www.luis.ai](https://www.luis.ai) で作成されたアプリの場合、**westus** と **eastus** の 2 つのリージョンで LUIS リソースを作成し、それらをリソースとしてアプリに追加すると、アプリは両方のリージョンに発行されます。 LUIS のリージョンの詳細については、[リージョン](luis-reference-regions.md)に関するページを参照してください。


## <a name="configuring-publish-settings"></a>発行の設定を構成する

スロットを選択したら、次のように発行の設定を構成します。

* センチメント分析
* スペル修正
* 音声認識の準備 

発行後、これらの設定は **[管理]** セクションの **[Publish settings]\(発行の設定\)** ページで確認できます。 発行ごとに設定を変更できます。 発行を取り消すと、発行中に加えた変更も取り消されます。 

### <a name="when-your-app-is-published"></a>アプリが発行されたとき

アプリが正常に発行されると、ブラウザーの上部に緑色の成功通知が表示されます。 緑色の通知バーには、エンドポイントへのリンクも含まれています。 

![発行のポップアップ ウィンドウとエンドポイントへのリンク](./media/luis-how-to-publish-app/publish-success.png)

エンドポイント URL が必要な場合は、リンクを選択します。 エンドポイント URL には、上部のメニューの **[管理]** を選択し、左側のメニューの **[Azure リソース]** を選択してもアクセスできます。 

## <a name="sentiment-analysis"></a>センチメント分析

<a name="enable-sentiment-analysis"></a>

感情分析を使用すると、LUIS を [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) と統合して、感情分析とキーフレーズ分析を提供できます。 

Text Analytics キーを指定する必要はなく、Azure アカウントに対するこのサービスの課金はありません。 

センチメント データは 1 と 0 の間のスコアで、1 に近いほどポジティブなセンチメントを示し、0 に近いほどネガティブな感情を示します。 `positive`、`neutral`、`negative` のセンチメント ラベルは、サポートされているカルチャによって異なります。 現時点では、センチメント ラベルがサポートされているのは英語のみです。 

感情分析での JSON エンドポイントの応答の詳細については、「[Sentiment analysis](luis-concept-data-extraction.md#sentiment-analysis)」(感情分析) をご覧ください。

## <a name="spelling-correction"></a>スペル修正

スペル修正は、LUIS ユーザーの発話予測の前に行われます。 応答では、元の発話 (スペルを含む) に対するすべての変更を確認できます。

## <a name="speech-priming"></a>音声認識の準備

音声認識の準備は、テキストを音声に変換する前に、音声サービスに LUIS モデルを送信するプロセスです。 これにより、音声サービスはモデルに対してより正確に音声変換を行うことができます。 これにより、1 回の音声通話で LUIS 応答を取得することで、ボットの音声と LUIS の要求と応答を 1 回の呼び出しで行うことができます。 そのため、全体的な待機時間が短くなります。

## <a name="next-steps"></a>次の手順

* LUIS に対する Azure サブスクリプションにキーを追加する方法と、Bing Spell Check キーを設定し、結果にすべての意図を含めるする方法については、[キーの管理](./luis-how-to-azure-subscription.md)に関するページを参照してください。
* テスト コンソールでの発行済みアプリのテスト方法については、[アプリのトレーニングとテスト](luis-interactive-test.md)に関するページをご覧ください。

