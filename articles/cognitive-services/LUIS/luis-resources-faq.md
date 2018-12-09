---
title: FAQ - よく寄せられる質問 - Language Understanding (LUIS)
titleSuffix: Azure Cognitive Services
description: この記事では、Language Understanding (LUIS) についてよく寄せられる質問とその回答を紹介します。
author: diberry
manager: cgronlun
services: cognitive-services
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: db8fc0b6926930521f11285dc56c886c10c42fc2
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2018
ms.locfileid: "49649314"
---
# <a name="language-understanding-faq"></a>Language Understanding のよくあるご質問

この記事では、Language Understanding (LUIS) についてよく寄せられる質問とその回答を紹介します。

## <a name="luis-authoring"></a>LUIS の作成

### <a name="what-are-the-luis-best-practices"></a>LUIS のベスト プラクティスは何ですか?
[作成サイクル](luis-concept-app-iteration.md)で開始してから、[ベスト プラクティス](luis-concept-best-practices.md)を読んでください。

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>LUIS でアプリ作成を始める最善の方法は何ですか?

アプリ作成の最善の方法は、[増分プロセス](luis-concept-app-iteration.md)です。

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>アプリの意図をモデル化するよい方法は何ですか? もっと具体的または一般的な意図を作成する必要がありますか?

重複が発生するほど一般的ではなく、かといって LUIS が似た意図を区別するのが難しくなるほど具体的ではないように、意図を選択します。 弁別的に固有の意図を作成するのが、LUIS モデリングのベスト プラクティスの 1 つです。

### <a name="is-it-important-to-train-the-none-intent"></a>None 意図をトレーニングすることは重要ですか?

はい、他の意図にラベルを追加しながらより多くの発話で **None** 意図をトレーニングするのはよいことです。 適切な比率は、ある意図に 10 個のラベルが追加されるごとに、**None** 意図に 1 または 2 個のラベルが追加されるようにすることです。 この比率は、LUIS の弁別能力を高めます。

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>発話のスペル ミスはどのようにして修正できますか?

[Bing Spell Check API V7](luis-tutorial-bing-spellcheck.md) のチュートリアルをご覧ください。 LUIS は、Bing Spell Check API V7 による制限を強制します。

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>プログラムで LUIS アプリを編集するにはどうすればよいですか?
LUIS アプリをプログラムで編集するには、[Authoring API](https://aka.ms/luis-authoring-apis) を使います。 Authoring API の呼び出し方法の例は、[LUIS Authoring API の呼び出し](./luis-quickstart-node-add-utterance.md)に関するページ、および「[Build a LUIS app programmatically using Node.js](./luis-tutorial-node-import-utterances-csv.md)」(Node.js を使用してプログラムで LUIS アプリを作成する) をご覧ください。 Authoring API を使うには、エンドポイント キーではなく[オーサリング キー](luis-concept-keys.md#authoring-key)を使用する必要があります。 プログラムで作成すると、1 か月あたり最大 1,000,000 呼び出し、および 1 秒あたり最大 5 トランザクションが可能です。 キーおよび LUIS でのキーの使用の詳細については、[キーの管理](./luis-concept-keys.md)に関するページをご覧ください。

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>正規表現のマッチングを提供していたパターン機能はどこにありますか?
以前の**パターン機能**は現在は非推奨になっており、**[パターン](luis-concept-patterns.md)** に置き換えられています。

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>エンティティを使って正しいデータを取得するにはどうすればよいですか?
[エンティティ](luis-concept-entity-types.md)および[データ抽出](luis-concept-data-extraction.md)に関するページをご覧ください。

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>発話例のバリエーションは句読点を含む必要がありますか?
発話例として異なるバリエーションを意図に追加するか、句読点を[無視する構文](luis-concept-patterns.md#pattern-syntax)を使用して発話例のパターンを追加します。

### <a name="does-luis-currently-support-cortana"></a>LUIS は現在 Cortana をサポートしていますか。

Cortana 事前構築済みアプリは 2017 年に非推奨になりました。 これらはサポートされなくなりました。

## <a name="luis-endpoint"></a>LUIS エンドポイント

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>LUIS がクエリの単語の周りまたは途中にスペースを追加するのはなぜですか?
LUIS は、[カルチャ](luis-language-support.md#tokenization)に基づいて発話を[トークン化](luis-glossary.md#token)します。 元の値とトークン化された値の両方を、[データ抽出](luis-concept-data-extraction.md#tokenized-entity-returned)に使用できます。

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>LUIS エンドポイント キーを作成して割り当てるにはどうすればよいですか?
Azure で[サービス](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) レベルの[エンドポイント キーを作成](luis-how-to-azure-subscription.md#create-luis-endpoint-key)します。 **[[Keys and endpoints]\(キーとエンドポイント\)](luis-how-to-manage-keys.md)** ページで[キーを割り当て](luis-how-to-manage-keys.md#assign-endpoint-key)ます。 このアクションに対応する API はありません。 その後、エンドポイントへの HTTP 要求を変更して、[新しいエンドポイント キーを使用する](luis-concept-keys.md#use-endpoint-key-in-query)必要があります。

### <a name="how-do-i-interpret-luis-scores"></a>LUIS のスコアを解釈するにはどうすればよいですか?
システムは、その値に関係なく、最高のスコアの意図を使用する必要があります。 たとえば、スコアが 0.5 より低くても (50% 未満)、それは必ずしも LUIS の信頼度が低いことを意味するものではありません。 より多くのトレーニング データを提供すると、最も可能性の高い意図のスコアを上げるのに役立ちます。

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>アプリのダッシュボードにエンドポイントのヒットが表示されないのはなぜですか?
アプリのダッシュボードの合計エンドポイント ヒット数は定期的に更新されますが、Azure Portal で LUIS エンドポイント キーに関連付けられているメトリックの方がより頻繁に更新されます。

更新されたエンドポイント ヒット数がダッシュボードに表示されない場合は、Azure Portal にログインし、LUIS エンドポイント キーに関連付けられているリソースを検索し、**[メトリック]** を開いて **[呼び出し合計]** メトリックを選択してください。 エンドポイント キーが複数の LUIS アプリに使用されている場合、Azure Portal のメトリックには、それを使用するすべての LUIS アプリからの呼び出しの集計数が示されます。

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>昨日は動いていた LUIS アプリで、今日は 403 エラーが発生します。 アプリは変更していません。 どのように修正すればよいですか
[こちらの FAQ の説明](#how-do-i-create-and-assign-a-luis-endpoint-key)に従って LUIS エンドポイント キーを作成し、それをアプリに割り当てます。 その後、エンドポイントへの HTTP 要求を変更して、[新しいエンドポイント キーを使用する](luis-concept-keys.md#use-endpoint-key-in-query)必要があります。

### <a name="how-do-i-secure-my-luis-endpoint"></a>LUIS エンドポイントをセキュリティで保護するにはどうすればよいですか?
「[Securing the endpoint](luis-concept-security.md#securing-the-endpoint)」(エンドポイントのセキュリティ保護) をご覧ください。

## <a name="working-within-luis-limits"></a>LUIS 制限内での作業

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>LUIS アプリがサポートできる意図とエンティティの最大数はどれくらいですか?
[境界](luis-boundaries.md)に関するページをご覧ください。

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>作成したい LUIS アプリが意図の最大数を超えます。 どうすればよいですか。

[意図のベスト プラクティス](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents)に関するページをご覧ください。

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>作成したい LUIS アプリがエンティティの最大数を超えます。 どうすればよいですか。

[エンティティのベスト プラクティス](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)に関するページをご覧ください。

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>フレーズ リストの数とサイズにはどのような制限がありますか?
[フレーズ リスト](./luis-concept-feature.md)の最大長については、[境界](luis-boundaries.md)に関するページをご覧ください。

### <a name="what-are-the-limits-on-example-utterances"></a>発話の例にはどのような制限がありますか?
[境界](luis-boundaries.md)に関するページをご覧ください。

## <a name="testing-and-training"></a>テストとトレーニング

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>アプリの一部のモデルでバッチ テスト ウィンドウにエラーが表示されます。 この問題を解決するにはどうすればよいですか?

このエラーは、モデルのラベルと予測の間に不一致があることを示します。 この問題を解決するには、次のタスクの一方または両方を行います。
* LUIS による意図間の区別を向上させるには、さらにラベルを追加します。
* LUIS による学習速度を上げるには、ドメイン固有のボキャブラリを導入するフレーズ リスト機能を追加します。

[バッチ テスト](luis-tutorial-batch-testing.md)に関するチュートリアルをご覧ください。

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>アプリをエクスポートした後、(新しいアプリ ID を持つ) 新しいアプリに再インポートすると、LUIS の予測スコアが異なります。 なぜこのようになるのですか?

[同じアプリのコピー間での予測の相違](luis-concept-prediction-score.md#differences-with-predictions)に関するページをご覧ください。

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>アプリを変更した後、いくつかの発話は、間違った意図になります。 問題は、ランダムに表示されなくなるようです。 どのように修正すればよいですか 

「[すべてのデータでトレーニングする](luis-how-to-train.md#train-with-all-data)」を参照してください。

## <a name="app-publishing"></a>アプリの発行

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>[Add a key to your app]\(アプリにキーを追加する\) ウィンドウのテナント ID とは何ですか?
Azure では、テナントはサービスに関連付けられているクライアントまたは組織を表します。 Azure portal でテナント ID を調べるには、**[Azure Active Directory]** > **[管理]** > **[プロパティ]** の順に選択して、**[ディレクトリ ID]** ボックスを確認します。

![Azure portal でのテナント ID](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>割り当てた数より多くのエンドポイント キーがアプリに割り当てられているのはなぜですか?
利便性のため、それぞれの LUIS アプリには、オーサリング/スターター キーがエンドポイント リストにあります。 このキーでは、LUIS を試用できるよう、数個のエンドポイント ヒットのみが許可されます。  

LUIS の一般公開 (GA) よりも前からアプリが存在していた場合、お使いのサブスクリプション内のエンドポイント キーが自動的に割り当てられます。 これは、GA の移行を容易にするために行われました。 Azure portal 内の新しい LUIS エンドポイント キーが自動的に LUIS に割り当てられることは _ありません_。

## <a name="app-management"></a>アプリの管理

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>LUIS アプリの所有権を譲渡するにはどうすればよいですか?
LUIS アプリを別の Azure サブスクリプションに譲渡するには、LUIS アプリをエクスポートした後、新しいアカウントを使用してインポートします。 LUIS アプリを呼び出しているクライアント アプリケーションで、LUIS アプリ ID を更新します。 新しいアプリは、元のアプリとは若干異なる LUIS スコアを返す可能性があります。

### <a name="how-do-i-download-a-log-of-user-utterances"></a>ユーザーの発話のログをダウンロードするにはどうすればよいですか?
既定では、LUIS アプリはユーザーからの発話をログに記録します。 ユーザーが LUIS アプリに送信した発話のログをダウンロードするには、**[マイ アプリ]** に移動し、アプリを選択します。 コンテキストのツールバーで **[Export endpoint logs]\(エンドポイント ログのエクスポート\)** を選択します。 ログは、コンマ区切り値 (CSV) ファイルとして書式設定されています。

### <a name="how-can-i-disable-the-logging-of-utterances"></a>発話のログ記録を無効にするにはどうすればよいですか?
クライアント アプリケーションが LUIS のクエリに使用しているエンドポイント URL で `log=false` を設定することにより、ユーザー発話のログ記録をオフにできます。 ただし、ログ記録をオフにすると、[アクティブ ラーニング](luis-concept-review-endpoint-utterances.md#what-is-active-learning)に基づいて発話を提案したりパフォーマンスを向上させたりする LUIS アプリの機能が無効になります。 データのプライバシーを考慮して `log=false` を設定した場合、LUIS からユーザーの発話の記録をダウンロードしたり、発話を使用してアプリの品質を向上させたりすることはできません。

発話が保存されるのはログだけです。

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>エンドポイントの発話をログに記録する必要がないのはどのような場合ですか?
予測分析にログを使用する場合、テスト用の発話はログにキャプチャしないでください。

## <a name="data-management"></a>[データ管理]

### <a name="can-i-delete-data-from-luis"></a>LUIS からデータを削除できますか?

* LUIS のトレーニングに使用した発話例はいつでも削除できます。 LUIS アプリから発話例を削除すると、LUIS Web サービスから削除されて、エクスポートに使用できなくなります。
* **[Review endpoint utterances]\(エンドポイントの発話の確認\)** ページで LUIS が提案するユーザー発話のリストから、発話を削除できます。 このリストから削除した発話は提案されなくなりますが、ログからは削除されません。
* アカウントを削除すると、発話例およびログと共に、すべてのアプリが削除されます。 データは、サーバーに 60 日間保持された後、完全に削除されます。

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>LUIS に送信されたデータを Microsoft はどのように管理しますか?

Microsoft の取り組みと、Azure サービスでのデータ管理とアクセスに対するお客様のオプションについては、[セキュリティ センター](https://www.microsoft.com/trustcenter)の説明をご覧ください。

## <a name="language-and-translation-support"></a>言語と翻訳のサポート

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>ある言語で作成されているアプリを、別の言語でも作成する必要があります。 これを行う最も簡単な方法は何ですか?
1. アプリをエクスポートします。
2. エクスポートされたアプリの JSON ファイルで、ラベルの付いた発話をターゲット言語に翻訳します。
3. 意図とエンティティの名前を変更する必要がある場合と、そのままにしてもかまわない場合があります。
4. 最後に、アプリをインポートして、対象言語の LUIS アプリにします。

## <a name="app-notification"></a>アプリの通知

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>クォータを使い切りそうですというメールを受け取りましたが、なぜですか?
お使いのオーサリング/スターター キーで許可されるのは、1 か月あたり 1,000 エンドポイント クエリだけです。 エンドポイント クエリを実行する場合は、LUIS エンドポイント キー (無料または有料) を作成し、そのキーを使用してください。 ボットまたは別のクライアント アプリケーションからエンドポイントのクエリを行っている場合は、そこで LUIS エンドポイント キーを変更する必要があります。

## <a name="integrating-luis"></a>LUIS の統合

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Azure Web アプリ ボット サブスクリプション プロセスの間に、LUIS アプリはどこに作成されますか?
LUIS テンプレートを選択し、テンプレート ウィンドウで **[選択]** を選択した場合、左側のウィンドウが変更されてそのテンプレートの種類が含まれるようになり、LUIS テンプレートを作成するリージョンの指定を求められます。 ただし、Web アプリのボット プロセスでは LUIS サブスクリプションは作成されません。

![LUIS テンプレートの Web アプリ ボット リージョン](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Bot Framework の音声認識の準備がサポートされているのはどの LUIS リージョンですか?
[音声認識の準備](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)は、米国中部インスタンスの LUIS アプリに対してのみサポートされます。

## <a name="luis-service"></a>LUIS サービス

### <a name="is-luis-available-on-premises-or-in-private-cloud"></a>オンプレミスまたはプライベート クラウドで LUIS を使用できますか?
いいえ。


### <a name="at-the-build-2018-conference-i-heard-about-a-language-understanding-feature-or-demo-but-i-dont-remember-what-it-was-called"></a>Build 2018 Conference で Language Understanding の機能またはデモについて聞きましたが、名前を忘れました。

Build 2018 Conference では、次の機能がリリースされました。

|Name|コンテンツ|
|--|--|
|強化された機能|[正規表現](luis-concept-data-extraction.md##regular-expression-entity-data)エンティティと[キー フレーズ](luis-concept-data-extraction.md#key-phrase-extraction-entity-data) エンティティ
|パターン|パターンの[概念](luis-concept-patterns.md)、[チュートリアル](luis-tutorial-pattern.md)、[ハウツー](luis-how-to-model-intent-pattern.md)<br>例外に対する[明示的なリスト](luis-concept-patterns.md#explicit-lists)を含む [Patterns.Any](luis-concept-entity-types.md) エンティティの概念<br>[ロール](luis-concept-roles.md)の概念|
|統合|[感情分析](luis-how-to-publish-app.md#enable-sentiment-analysis)の[テキスト分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)の統合<br>[Speech SDK](https://aka.ms/SpeechSDK) との組み合わせでの音声認識の準備の[音声](https://docs.microsoft.com/azure/cognitive-services/speech)の統合|
|ディスパッチ ツール|[BotBuilder ツール](https://github.com/Microsoft/botbuilder-tools)の一部、ボットでの意図認識向上のために単一 LUIS アプリに複数の LUIS アプリと QnA Maker アプリを結合するためのディスパッチ コマンド ライン [ツール](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps)

追加のオーサリング [API ルート](https://github.com/Microsoft/LUIS-Samples/blob/master/authoring-routes.md)が組み込まれました。

ビデオ:
* [Build 2018 での Azure Friday: Cognitive Services - Language (LUIS)](https://channel9.msdn.com/Shows/Azure-Friday/At-Build-2018-Cognitive-Services-Language-LUIS/player)
* [Build 2018 の AI ショー - Language Understanding Intelligent Service の新機能](https://channel9.msdn.com/Shows/AI-Show/Whats-New-with-Language-Understanding-Service-LUIS/player)
* [Build 2018 のセッション - ボット インテリジェンス、音声機能、NLU のベスト プラクティス](https://channel9.msdn.com/events/Build/2018/BRK3208)
* [Build 2018 - LUIS 更新](https://channel9.msdn.com/events/Build/2018/THR3118/player)

プロジェクト:
* [Contoso Cafe ボット](https://github.com/botbuilderbuild2018/build2018demo) デモ - Github 上のソース コード

## <a name="next-steps"></a>次の手順

LUIS の詳細については、次のリソースを参照してください。
* [LUIS のタグが付いた Stack Overflow での質問](https://stackoverflow.com/questions/tagged/luis)
* [MSDN Language Understanding Intelligent Services (LUIS) フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS)
