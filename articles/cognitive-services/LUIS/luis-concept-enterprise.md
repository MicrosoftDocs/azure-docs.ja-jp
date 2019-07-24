---
title: エンタープライズの概念
titleSuffix: Language Understanding - Azure Cognitive Services
description: 大規模な LUIS アプリや、LUIS と QnA Maker の両方を含んだ複数のアプリの設計概念を理解します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: e5d7e2bfe1ee4e3ca248f40701aa65e757fc4d74
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795504"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>LUIS アプリのエンタープライズ戦略
エンタープライズ アプリについて以下の設計戦略を確認してください。

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>クォータを超える LUIS 要求が予想される場合
LUIS アプリの要求レートが[クォータ レート](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)の許容値を超える場合、[同じアプリ定義](#use-multiple-apps-with-same-app-definition)を持つより多くの LUIS アプリに負荷を分散するか、[複数のキーを作成してアプリに割り当て](#assign-multiple-luis-keys-to-same-app)ます。 

### <a name="use-multiple-apps-with-same-app-definition"></a>同じアプリ定義を持つ複数のアプリを使用する
元の LUIS アプリをエクスポートし、そのアプリを個別のアプリにインポートします。 アプリごとに固有のアプリ ID があります。 公開するときに、すべてのアプリ間で同じキーを使用する代わりに、アプリごとに別々のキーを作成します。 1 つのアプリに負荷がかからないように、すべてのアプリに負荷を分散します。 [Application Insights](luis-tutorial-bot-csharp-appinsights.md) を追加して使用量を監視します。 

すべてのアプリで取得される最上位の意図が同じになるようにするには、LUIS が混乱しないように 1 番目と 2 番目の意図の予測に開きがあり、発話内のわずかな違いによってアプリ間で異なる結果が返されるようにしてください。 

1 つのアプリをマスターとして指定します。 確認用として推奨される発話をマスター アプリに追加してから、その他のすべてのアプリに移動する必要があります。 これは、アプリを完全にエクスポートするか、ラベル付き発話をマスターから子に読み込むことで行います。 読み込みは、[単一の発話](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08)または[バッチ](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)に対して、[LUIS](luis-reference-regions.md) Web サイトまたはオーサリング API のどちらからでも実行できます。 

アクティブ ラーニングのために定期的な (2 週間ごとなど) [エンドポイントの発話の確認](luis-how-to-review-endpoint-utterances.md)をスケジュール設定し、再トレーニングして再公開します。 

### <a name="assign-multiple-luis-keys-to-same-app"></a>同じアプリに複数の LUIS キーを割り当てる
LUIS アプリで、1 つのキーのクォータで許可されているよりも多くのエンドポイント ヒット数を受け取る場合は、さらにキーを作成して LUIS アプリに割り当てます。 エンドポイント キー間でエンドポイント クエリを管理するには、トラフィック マネージャーまたはロード バランサーを作成します。 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>モノリシック アプリから間違った意図が返ってくる場合
アプリで多様なユーザー発話を予測する場合は、[ディスパッチ モデル](#dispatch-tool-and-model)の実装を検討します。 モノリシック アプリを分割すると、LUIS では、親アプリと子アプリ間で意図を混同することなく、意図の検出に集中できます。 

アクティブ ラーニングのために定期的な (2 週間ごとなど) [エンドポイントの発話の確認](luis-how-to-review-endpoint-utterances.md)をスケジュール設定し、再トレーニングして再公開します。 

## <a name="when-you-need-to-have-more-than-500-intents"></a>500 を超える意図が必要な場合
たとえば、500 以上の意図を含むオフィス アシスタントを開発しているとします。 200 の意図は会議のスケジュール関連、200 がリマインダーについて、200 が同僚に関する情報の取得、200 がメール送信用である場合、各グループが 1 つのアプリに収まるように意図をグループ化し、各意図を含むトップ レベルのアプリを作成します。 [ディスパッチ ツールとアーキテクチャ](#dispatch-tool-and-model)を使用して、トップ レベルのアプリを構築します。 [ディスパッチのチュートリアル][dispatcher-application-tutorial]で示されているように、カスケード型の呼び出しを使用するようにボットを変更します。 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>複数の LUIS と QnA Maker のアプリを結合する必要がある場合
ボットに応答する必要がある LUIS アプリと QnA Maker アプリが複数ある場合、[ディスパッチ ツール](#dispatch-tool-and-model)を使用してトップ レベルのアプリを構築します。 [ディスパッチのチュートリアル][dispatcher-application-tutorial]で示されているように、カスケード型の呼び出しを使用するようにボットを変更します。 

## <a name="dispatch-tool-and-model"></a>ディスパッチ ツールとモデル
[BotBuilder ツール](https://github.com/Microsoft/botbuilder-tools)内にある[ディスパッチ][dispatch-tool] コマンドライン ツールを使用して、複数の LUIS アプリや QnA Maker アプリを親 LUIS アプリに結合します。 この方法を使用すると、すべてのサブジェクトを含む親ドメインと、さまざまな子サブジェクト ドメインを、別々のアプリに指定できます。 

![ディスパッチ アーキテクチャの概念図](./media/luis-concept-enterprise/dispatch-architecture.png)

LUIS では、親ドメインはアプリ一覧で `Dispatch` という名前のバージョンで示されます。 

チャットボットは発話を受信し、予測するために親 LUIS アプリに送信します。 親アプリで予測されたトップの意図によって、次に呼び出す子 LUIS アプリが決定されます。 チャットボットは、より具体的な予測を得るために、発話を子アプリに送信します。

この呼び出しの階層が、Bot Builder v4 [ディスパッチャー アプリケーション チュートリアル][dispatcher-application-tutorial]で、どのように作成されているかを理解します。  

### <a name="intent-limits-in-dispatch-model"></a>ディスパッチ モデルでの意図の制限
ディスパッチ アプリケーションには最大 500 のディスパッチ ソースが用意されており、これは 500 の意図に相当します。 

## <a name="next-steps"></a>次の手順

* [バッチをテストする](luis-how-to-batch-test.md)方法を学習します。

[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool
