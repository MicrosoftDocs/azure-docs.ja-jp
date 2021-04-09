---
title: よく寄せられる質問 (FAQ) - LUIS
description: この記事では、Language Understanding (LUIS) についてよく寄せられる質問とその回答を紹介します。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: troubleshooting
ms.date: 05/06/2020
ms.openlocfilehash: b5e25e9ed25ced96d38994928bcb6275ce79420f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102612798"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Language Understanding についてよくあるご質問 (FAQ)

この記事では、Language Understanding (LUIS) についてよく寄せられる質問とその回答を紹介します。

## <a name="whats-new"></a>新機能

Language Understanding (LUIS) の詳細については、[こちら](whats-new.md)をご覧ください。

<a name="luis-authoring"></a>

## <a name="authoring"></a>Authoring

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
LUIS アプリをプログラムで編集するには、[Authoring API](https://go.microsoft.com/fwlink/?linkid=2092087) を使います。 Authoring API の呼び出し方法の例は、[LUIS Authoring API の呼び出し](./get-started-get-model-rest-apis.md)に関するページ、および「[Build a LUIS app programmatically using Node.js](./luis-tutorial-node-import-utterances-csv.md)」(Node.js を使用してプログラムで LUIS アプリを作成する) をご覧ください。 Authoring API を使うには、エンドポイント キーではなく[オーサリング キー](luis-how-to-azure-subscription.md#azure-resources-for-luis)を使用する必要があります。 プログラムで作成すると、1 か月あたり最大 1,000,000 呼び出し、および 1 秒あたり最大 5 トランザクションが可能です。 キーおよび LUIS でのキーの使用の詳細については、[キーの管理](./luis-how-to-azure-subscription.md)に関するページをご覧ください。

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>正規表現のマッチングを提供していたパターン機能はどこにありますか?
以前の **パターン機能** は現在は非推奨になっており、 **[パターン](luis-concept-patterns.md)** に置き換えられています。

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>エンティティを使って正しいデータを取得するにはどうすればよいですか?
[エンティティ](luis-concept-entity-types.md)および[データ抽出](luis-concept-data-extraction.md)に関するページをご覧ください。

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>発話例のバリエーションは句読点を含む必要がありますか?
次のいずれかのソリューションを使用します。
* [句読点](luis-reference-application-settings.md#punctuation-normalization)を無視します
* 発話例として異なるバリエーションを意図に追加します
* 句読点を[無視する構文](luis-concept-patterns.md#pattern-syntax)を使用して発話例のパターンを追加します。

### <a name="does-luis-currently-support-cortana"></a>LUIS は現在 Cortana をサポートしていますか。

Cortana 事前構築済みアプリは 2017 年に非推奨になりました。 これらはサポートされなくなりました。

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>LUIS アプリの所有権を譲渡するにはどうすればよいですか?
LUIS アプリを別の Azure サブスクリプションに譲渡するには、LUIS アプリをエクスポートした後、新しいアカウントを使用してインポートします。 LUIS アプリを呼び出しているクライアント アプリケーションで、LUIS アプリ ID を更新します。 新しいアプリは、元のアプリとは若干異なる LUIS スコアを返す可能性があります。

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>発話の例では、自分のカスタム エンティティではなく事前構築済みのエンティティにタグ付けされています。 これをどのように修正すればよいですか?

LUIS ポータルでは、抽出するエンティティのテキストにラベルを付けることができます。 LUIS ポータルに正しいエンティティ予測が表示されない場合は、必要に応じて、さらに発話を追加し、テキスト内でそのエンティティにラベルを付けるか、特徴を追加します。

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>アプリまたはバージョン ファイルをインポートしようとしましたが、エラーが発生しました。なぜでしょうか?

[バージョン インポート エラー](luis-how-to-manage-versions.md#import-errors)について確認してください。

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>コラボレーションと投稿

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-azure-role-based-access-control-azure-rbac"></a>Azure Active Directory (Azure AD) または Azure ロールベースのアクセス制御 (Azure RBAC) で、コラボレーターに LUIS へのアクセス権を付与するにはどうすればよいですか?

コラボレーターにアクセス権を付与する方法については、「[Azure Active Directory リソース](luis-how-to-collaborate.md#azure-active-directory-resources)」および「[Azure Active Directory テナント ユーザー](luis-how-to-collaborate.md#azure-active-directory-tenant-user)」を参照してください。

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>エンドポイント

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>HTTP 403 エラー状態コードを受け取りました。 どのように修正すればよいですか

価格レベルの 1 秒あたりのトランザクション数または 1 か月あたりのトランザクション数を超えると、403 および 429 のエラー状態コードを取得します。 価格レベルを上げるか、Language Understanding [コンテナー](luis-container-howto.md)を使用してください。

無料の 1000 エンドポイント クエリをすべて使用しているか、価格レベルの 1 か月あたりのトランザクションのクォータを超えた場合は、HTTP 403 エラー状態コードが表示されます。

このエラーを解決するには、高いレベルに[価格レベルを変更](luis-how-to-azure-subscription.md#change-the-pricing-tier)するか、[新しいリソースを作成](get-started-portal-deploy-app.md#create-the-endpoint-resource)して[アプリに割り当てる](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal)必要があります。

このエラーの解決方法は次のとおりです。

* [Azure portal](https://portal.azure.com) で、Language Understanding リソースの **[リソース管理] -> [価格レベル]** で、価格レベルをより高い TP レベルに変更します。 リソースが既に Language Understanding アプリに割り当てられている場合、Language Understanding ポータルでは何もする必要はありません。
*  使用量が最も高い価格レベルを超えた場合は、ロード バランサーを使って Language Understanding リソースをその前に追加します。 これには、Kubernetes または Docker Compose を含む [Language Understanding コンテナー](luis-container-howto.md)が役立ちます。

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>HTTP 429 エラー状態コードを受け取りました。 どのように修正すればよいですか

価格レベルの 1 秒あたりのトランザクション数または 1 か月あたりのトランザクション数を超えると、403 および 429 のエラー状態コードを取得します。 価格レベルを上げるか、Language Understanding [コンテナー](luis-container-howto.md)を使用してください。

この状態コードは、1 秒あたりのトランザクションが価格レベルを超えると返されます。

解決方法は次のとおりです。

* 最高レベルではない場合は、[価格レベルを上げる](luis-how-to-azure-subscription.md#change-the-pricing-tier)ことができます。
* 使用量が最も高い価格レベルを超えた場合は、ロード バランサーを使って Language Understanding リソースをその前に追加します。 これには、Kubernetes または Docker Compose を含む [Language Understanding コンテナー](luis-container-howto.md)が役立ちます。
* この状態コードが表示された場合は、自分で実装する[再試行ポリシー](/azure/architecture/best-practices/transient-faults#general-guidelines)を使用してクライアント アプリケーションの要求をゲートすることができます。

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>エンドポイントのクエリで予期していなかった結果が返されました。 どうすればよいですか。

クエリで予期しない予測結果が返される原因は、公開されているモデルの状態にあります。 モデルを修正するには、モデルを変更し、トレーニングしてからもう一度公開することが必要になる場合があります。

モデルの修正は、[アクティブ ラーニング](luis-how-to-review-endpoint-utterances.md)から始まります。

トレーニング データをすべて使用するために、[アプリケーションのバージョン設定 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) を更新して非決定論的トレーニングを削除することができます。

他のヒントについては、[ベスト プラクティス](luis-concept-best-practices.md)を参照してください。

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>LUIS がクエリの単語の周りまたは途中にスペースを追加するのはなぜですか?
LUIS は、[カルチャ](luis-language-support.md#tokenization)に基づいて発話を[トークン化](luis-glossary.md#token)します。 元の値とトークン化された値の両方を、[データ抽出](luis-concept-data-extraction.md#tokenized-entity-returned)に使用できます。

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>LUIS エンドポイント キーを作成して割り当てるにはどうすればよいですか?
Azure で[サービス](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) レベルの[エンドポイント キーを作成](luis-how-to-azure-subscription.md)します。 **[[Azure リソース]](luis-how-to-azure-subscription.md)** ページで [キーを割り当て](luis-how-to-azure-subscription.md)ます。 このアクションに対応する API はありません。 その後、エンドポイントへの HTTP 要求を変更して、[新しいエンドポイント キーを使用する](luis-how-to-azure-subscription.md)必要があります。

### <a name="how-do-i-interpret-luis-scores"></a>LUIS のスコアを解釈するにはどうすればよいですか?
システムは、その値に関係なく、最高のスコアの意図を使用する必要があります。 たとえば、スコアが 0.5 より低くても (50% 未満)、それは必ずしも LUIS の信頼度が低いことを意味するものではありません。 より多くのトレーニング データを提供すると、最も可能性の高い意図の[スコア](luis-concept-prediction-score.md)を上げるのに役立ちます。

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>アプリのダッシュボードにエンドポイントのヒットが表示されないのはなぜですか?
アプリのダッシュボードの合計エンドポイント ヒット数は定期的に更新されますが、Azure Portal で LUIS エンドポイント キーに関連付けられているメトリックの方がより頻繁に更新されます。

更新されたエンドポイント ヒット数がダッシュボードに表示されない場合は、Azure portal にサインインし、LUIS エンドポイント キーに関連付けられているリソースを検索し、 **[メトリック]** を開いて **[呼び出し合計]** メトリックを選択してください。 エンドポイント キーが複数の LUIS アプリに使用されている場合、Azure Portal のメトリックには、それを使用するすべての LUIS アプリからの呼び出しの集計数が示されます。

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>エンドポイントのクォータを取得する PowerShell コマンドはありますか?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

PowerShell コマンドを使用して、エンドポイントのクォータを確認できます。

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
```

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>昨日は動いていた LUIS アプリで、今日は 403 エラーが発生します。 アプリは変更していません。 どのように修正すればよいですか
こちらの[説明](#how-do-i-create-and-assign-a-luis-endpoint-key)に従って LUIS エンドポイント キーを作成し、それをアプリに割り当てます。 その後、エンドポイントへのクライアント アプリケーションの HTTP 要求を変更して、[新しいエンドポイント キーを使用する](luis-how-to-azure-subscription.md)必要があります。 別のリージョンに新しいリソースを作成した場合、HTTP クライアント要求のリージョンも変更します。

### <a name="how-do-i-secure-my-luis-endpoint"></a>LUIS エンドポイントをセキュリティで保護するにはどうすればよいですか?
「[Securing the endpoint](luis-how-to-azure-subscription.md#securing-the-endpoint)」(エンドポイントのセキュリティ保護) をご覧ください。

## <a name="working-within-luis-limits"></a>LUIS 制限内での作業

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>LUIS アプリがサポートできる意図とエンティティの最大数はどれくらいですか?
[境界](luis-limits.md)に関するページをご覧ください。

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>作成したい LUIS アプリが意図の最大数を超えます。 どうすればよいですか。

[意図のベスト プラクティス](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents)に関するページをご覧ください。

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>作成したい LUIS アプリがエンティティの最大数を超えます。 どうすればよいですか。

[エンティティのベスト プラクティス](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)に関するページをご覧ください。

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>フレーズ リストの数とサイズにはどのような制限がありますか?
[フレーズ リスト](./luis-concept-feature.md)の最大長については、[境界](luis-limits.md)に関するページをご覧ください。

### <a name="what-are-the-limits-on-example-utterances"></a>発話の例にはどのような制限がありますか?
[境界](luis-limits.md)に関するページをご覧ください。

## <a name="testing-and-training"></a>テストとトレーニング

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>アプリの一部のモデルでバッチ テスト ウィンドウにエラーが表示されます。 この問題を解決するにはどうすればよいですか?

このエラーは、モデルのラベルと予測の間に不一致があることを示します。 この問題を解決するには、次のタスクの一方または両方を行います。
* LUIS による意図間の区別を向上させるには、さらにラベルを追加します。
* LUIS による学習速度を上げるには、ドメイン固有のボキャブラリを導入するフレーズ リスト機能を追加します。

[バッチ テスト](./luis-how-to-batch-test.md)に関するチュートリアルをご覧ください。

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>アプリをエクスポートした後、(新しいアプリ ID を持つ) 新しいアプリに再インポートすると、LUIS の予測スコアが異なります。 なぜこのようになるのですか?

[同じアプリのコピー間での予測の相違](luis-concept-prediction-score.md#review-intents-with-similar-scores)に関するページをご覧ください。

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>アプリを変更した後、いくつかの発話は、間違った意図になります。 問題は、ランダムに表示されなくなるようです。 どのように修正すればよいですか

「[すべてのデータでトレーニングする](luis-how-to-train.md#train-with-all-data)」を参照してください。

## <a name="app-publishing"></a>アプリの発行

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>[Add a key to your app]\(アプリにキーを追加する\) ウィンドウのテナント ID とは何ですか?
Azure では、テナントはサービスに関連付けられているクライアントまたは組織を表します。 Azure portal でテナント ID を調べるには、 **[Azure Active Directory]**  >  **[管理]**  >  **[プロパティ]** の順に選択して、 **[ディレクトリ ID]** ボックスを確認します。

![Azure portal でのテナント ID](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>割り当てた数より多くのエンドポイント キーがアプリに割り当てられているのはなぜですか?
利便性のため、それぞれの LUIS アプリには、オーサリング/スターター キーがエンドポイント リストにあります。 このキーでは、LUIS を試用できるよう、数個のエンドポイント ヒットのみが許可されます。

LUIS の一般公開 (GA) よりも前からアプリが存在していた場合、お使いのサブスクリプション内のエンドポイント キーが自動的に割り当てられます。 これは、GA の移行を容易にするために行われました。 Azure portal 内の新しい LUIS エンドポイント キーが自動的に LUIS に割り当てられることは _ありません_。

## <a name="key-management"></a>キー管理

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>どのキーが必要で、それをどこで取得し、それで何を行うかを知るにはどうすればよいですか?

オーサリング キーと予測ランタイム キーの違いの詳細については、[LUIS のオーサリング キーとクエリ予測エンドポイント キー](luis-how-to-azure-subscription.md)に関する記事を参照してください。

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>クォータ不足に関するエラーを受信しました。 どのように修正すればよいですか

詳細については、HTTP 状態コード [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) および [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) の解決に関する記事を参照してください。

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>さらに多くのエンドポイント クエリを処理する必要があります。 どうすればよいですか?

詳細については、HTTP 状態コード [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) および [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) の解決に関する記事を参照してください。

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>オーサリング キーを作成しましたが、LUIS ポータルに表示されません。 なぜでしょうか?

オーサリング キーは、[オーサリング キーを移行後に](luis-migration-authoring.md) LUIS ポータルで利用できるようになります。

## <a name="app-management"></a>アプリの管理

### <a name="how-do-i-download-a-log-of-user-utterances"></a>ユーザーの発話のログをダウンロードするにはどうすればよいですか?
既定では、LUIS アプリはユーザーからの発話をログに記録します。 ユーザーが LUIS アプリに送信した発話のログをダウンロードするには、 **[マイ アプリ]** に移動し、アプリを選択します。 コンテキストのツールバーで **[Export endpoint logs]\(エンドポイント ログのエクスポート\)** を選択します。 ログは、コンマ区切り値 (CSV) ファイルとして書式設定されています。

### <a name="how-can-i-disable-the-logging-of-utterances"></a>発話のログ記録を無効にするにはどうすればよいですか?
クライアント アプリケーションが LUIS のクエリに使用しているエンドポイント URL で `log=false` を設定することにより、ユーザー発話のログ記録をオフにできます。 ただし、ログ記録をオフにすると、[アクティブ ラーニング](luis-concept-review-endpoint-utterances.md#what-is-active-learning)に基づいて発話を提案したりパフォーマンスを向上させたりする LUIS アプリの機能が無効になります。 データのプライバシーを考慮して `log=false` を設定した場合、LUIS からユーザーの発話の記録をダウンロードしたり、発話を使用してアプリの品質を向上させたりすることはできません。

発話が保存されるのはログだけです。

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>エンドポイントの発話をログに記録する必要がないのはどのような場合ですか?
予測分析にログを使用する場合、テスト用の発話はログにキャプチャしないでください。

## <a name="data-management"></a>データ管理

### <a name="can-i-delete-data-from-luis"></a>LUIS からデータを削除できますか?

* LUIS のトレーニングに使用した発話例はいつでも削除できます。 LUIS アプリから発話の例を削除すると、LUIS Web サービスから削除されて、エクスポートに使用できなくなります。
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

## <a name="bots"></a>ボット

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>LUIS ボットが機能しません。 どうすればよいですか。

最初に、問題が LUIS に関連するものか、または LUIS ミドルウェアの外で起きているものかを切り分ける必要があります。

#### <a name="resolve-issue-in-luis"></a>LUIS での問題を解決する
[LUIS エンドポイント](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint)から LUIS に同じ発話を渡します。 エラーが発生する場合は、エラーが返されなくなるまで、LUIS の問題を解決します。 一般的なエラーの理由は、次のとおりです。

* `Out of call volume quota. Quota will be replenished in <time>.` - この問題は、オーサリング キーから[エンドポイント キー](luis-how-to-azure-subscription.md)に変更する必要があること、または[サービス レベル](luis-how-to-azure-subscription.md#change-the-pricing-tier)を変更する必要があることを、示しています。

#### <a name="resolve-issue-in-azure-bot-service"></a>Azure Bot Service で問題を解決する

Azure Bot Service を使用していて、 **[Test in Web Chat]\(Web チャットでのテスト\)** から `Sorry, my bot code is having an issue` が返ることが問題である場合は、ログをチェックします。

1. Azure portal で、お使いのボットの **[ボットの管理]** セクションから **[ビルド]** を選択します。
1. オンライン コード エディターを開きます。
1. 上部の青いナビゲーション バーで、ボットの名前 (右側の 2 番目の項目) を選択します。
1. 結果のドロップダウン リストで、 **[Kudu コンソールを開く]** を選択します。
1. **LogFiles** を選択し、**Application** を選択します。 すべてのログ ファイルを確認します。 アプリケーション フォルダーにエラーが見あたらない場合は、**LogFiles** ですべてのログ ファイルを確認します。
1. C# などのコンパイル言語を使用している場合は、プロジェクトを忘れずにリビルドします。

> [!Tip]
> コンソールでは、パッケージをインストールすることもできます。

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Bot Framework を使用してローカル コンピューターでデバッグしながら問題を解決します。

ボットのローカル デバッグについて詳しくは、「[ボットをデバッグする](/azure/bot-service/bot-service-debug-bot)」をご覧ください。

## <a name="integrating-luis"></a>LUIS の統合

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Azure Web アプリ ボット サブスクリプション プロセスの間に、LUIS アプリはどこに作成されますか?
LUIS テンプレートを選択し、テンプレート ウィンドウで **[選択]** を選択した場合、左側のウィンドウが変更されてそのテンプレートの種類が含まれるようになり、LUIS テンプレートを作成するリージョンの指定を求められます。 ただし、Web アプリのボット プロセスでは LUIS サブスクリプションは作成されません。

![LUIS テンプレートの Web アプリ ボット リージョン](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Bot Framework の音声認識の準備がサポートされているのはどの LUIS リージョンですか?
[音声認識の準備](/bot-framework/bot-service-manage-speech-priming)は、米国中部インスタンスの LUIS アプリに対してのみサポートされます。

## <a name="api-programming-strategies"></a>API プログラミング戦略

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>リソースの LUIS リージョンをプログラムで取得するには、どうすればよいですか?

LUIS のサンプルを使用して、プログラムで[リージョンを検索](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region)するには、C# または Node.Js を使用します。

## <a name="luis-service"></a>LUIS サービス

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>オンプレミスまたはプライベート クラウドで Language Understanding (LUIS) を使用できますか?

はい。使用量を測定するために必要な接続があれば、それらのシナリオで LUIS [コンテナー](luis-container-howto.md)を使用できます。

## <a name="migrating-to-the-next-version"></a>次のバージョンへの移行

### <a name="how-do-i-migrate-to-preview-v3-api"></a>V3 API に移行して、プレビューするにはどうすればいいですか?

[LUIS アプリの API v2 から v3 への移行ガイド](luis-migration-api-v3.md)を参照してください

## <a name="build-2019-conference-announcements"></a>Build 2019 Conference のお知らせ

Build 2019 Conference では、次の機能が公開されました。

* [V3 API 移行ガイドのプレビュー](luis-migration-api-v3.md)
* [改善された分析ダッシュ ボード](luis-how-to-use-dashboard.md)
* [改善された事前構築済みドメイン](luis-reference-prebuilt-domains.md)
* [動的なリスト エンティティ](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [外部エンティティ](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

ビデオ:

* [How to use Azure Conversational AI to scale your business for the next generation (Azure の対話型 AI を使用して次世代のビジネスを拡大する方法)](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>次のステップ

LUIS の詳細については、次のリソースを参照してください。
* [LUIS のタグが付いた Stack Overflow での質問](https://stackoverflow.com/questions/tagged/luis)
* [MSDN Language Understanding Intelligent Services (LUIS) の Microsoft Q&A 質問ページ](/answers/topics/azure-language-understanding.html)