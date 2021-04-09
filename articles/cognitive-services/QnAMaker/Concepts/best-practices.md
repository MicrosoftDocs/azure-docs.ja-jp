---
title: ベスト プラクティス - QnA Maker
description: 以下のベスト プラクティスを利用してナレッジ ベースを改善し、アプリケーション/チャット ボットのエンド ユーザーにより良い情報を提供します。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: b09c36f1dce3c394ff24e3e601d773279a6ac510
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102123311"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>QnA Maker ナレッジ ベースのベスト プラクティス

[ナレッジ ベース開発ライフサイクル](../Concepts/development-lifecycle-knowledge-base.md)では、ナレッジ ベースを始まりから終わりまで管理する方法を紹介しています。 以下のベスト プラクティスを利用してナレッジ ベースを改善し、対象のアプリケーションまたはチャット ボットのエンド ユーザーにより良い情報を提供します。

## <a name="extraction"></a>抽出

QnA Maker サービスは継続的に、コンテンツから QnA を抽出するアルゴリズムを改善し、サポートされるファイルおよび HTML 形式のリストを拡大しています。 ドキュメントの種類に応じた、データ抽出の[ガイドライン](../Concepts/data-sources-and-content.md)に従ってください。

一般的に、FAQ ページは独立させ、他の情報と組み合わせないでください。 製品マニュアルにはわかりやすい見出しを付けてください。索引ページを付けることもお勧めします。

### <a name="configuring-multi-turn"></a>複数ターンの構成

複数ターン抽出を有効にして[ナレッジ ベースを作成](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure)します。 ナレッジ ベースで質問階層をサポートしている場合や質問階層をサポートする必要がある場合は、この階層を、ドキュメントから抽出するか、ドキュメントの抽出後に作成することができます。

## <a name="creating-good-questions-and-answers"></a>優れた質問と回答の作成

### <a name="good-questions"></a>優れた質問

最高の質問は単純です。 各質問のキーワードまたはキー フレーズを検討した後、そのキーワードまたはキー フレーズに関する単純な質問を作成します。

同じような意味の質問を必要な数だけ追加しますが、変更は単純にしておきます。 質問の主な目標の一部ではないワードまたはフレーズを追加しても、QnA Maker で一致するものを検索するための助けにはなりません。


### <a name="add-relevant-alternative-questions"></a>関連する他の質問を追加する

ユーザーは、`How do I add a toner cartridge to my printer?` のような会話形式のテキストか、`toner cartridge` のようなキーワード検索のどちらかで質問を入力する可能性があります。 最善の回答を正しく返すためには、ナレッジ ベースにこの両方のスタイルの質問が用意されている必要があります。 顧客がどのキーワードを入力しているのかわからない場合は、Application Insights のデータを使用してクエリを分析します。

### <a name="good-answers"></a>優れた回答

最善の回答はシンプルな回答ですが、シンプルすぎてはいけません。 `yes` や `no` などの回答は使用してはいけません。 回答を他のソースにリンクしたり、メディアとリンクを使用してリッチ エクスペリエンスを提供したりする必要がある場合は、[メタデータ タグ付け](../how-to/edit-knowledge-base.md#add-metadata)を使用して回答を識別した後、`strictFilters` プロパティにそのメタデータ タグを含む[クエリを送信](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration)して、正しい回答のバージョンを取得します。

|回答|フォローアップ プロンプト|
|--|--|
|キーボードの電源ボタンを押して Surface ノート PC の電源を切ってください。|* スリープ、シャットダウン、再起動を行うキーの組み合わせ。<br>* Surface ノート PC 本体を起動する方法<br>* Surface ノート PC の BIOS を変更する方法<br>* スリープ、シャットダウン、再起動の違い|
|カスタマー サービスは、電話、Skype、テキスト メッセージで 24 時間ご利用いただけます。|* 販売に関する連絡先情報。<br> * 直接訪問できるオフィスと店舗の場所と営業時間。<br> * Surface ノート PC のアクセサリ。|

## <a name="chit-chat"></a>おしゃべり
少ない労力でボットをより話し上手で魅力的にするために、おしゃべりをボットに追加します。 ナレッジ ベースの作成時に、事前定義済みの性格に対応したおしゃべりデータ セットを簡単に追加し、いつでも性格を変更することができます。 [おしゃべりをナレッジ ベースに追加](../How-To/chit-chat-knowledge-base.md)する方法を学びます。

おしゃべりは[多くの言語](../how-to/chit-chat-knowledge-base.md#language-support)でサポートされています。

### <a name="choosing-a-personality"></a>性格の選択
おしゃべりでは、次のいくつかの事前定義済みの性格がサポートされています。

|パーソナリティ |QnA Maker データセット ファイル |
|---------|-----|
|2 次元形式 |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Friendly |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Witty |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Caring (温かい) |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Enthusiastic (熱心) |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

改まった応答からくだけた応答、非礼な応答まで可能です。 ボットに演じさせたい雰囲気に最も近い性格を選択する必要があります。 [データセット](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)を表示し、ボットの基本として機能するデータセットを選択した後、応答をカスタマイズすることができます。

### <a name="edit-bot-specific-questions"></a>ボット固有の質問の編集
おしゃべりデータ セットの一部であり、一般的な回答が入力されているボット固有の質問がいくつかあります。 ボットの詳細を最も的確に反映するように、これらの回答を変更します。

以下のおしゃべり QnA をより具体的にすることをお勧めします。

* ユーザーはだれか
* 何を実行できるか
* 何歳ですか?
* あなたを創造したのは誰ですか?
* こんにちは

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>メタデータ タグを使用したカスタムのおしゃべりの追加

独自のおしゃべり QnA ペアを追加する場合は、必ずメタデータを追加して、これらの回答が返されるようにします。 メタデータの名前と値のペアは `editorial:chitchat` です。

## <a name="searching-for-answers"></a>回答の検索

GenerateAnswer API では、質問と回答の両方を使用して、ユーザーのクエリに対する最善の回答を検索します。

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>回答が適切でない場合にのみ質問を検索する

回答を検索しない場合は、[`RankerType=QuestionOnly`](#choosing-ranker-type) を使用します。

この例としては、ナレッジ ベースが、質問としての頭字語と完全形としての回答を含むカタログである場合があります。 回答の値は、適切な回答を検索するのに役立ちません。

## <a name="rankingscoring"></a>ランキング/スコア
QnA Maker でサポートされている優先度付け機能を効果的に活用してください。 それにより、ユーザーからの問い合わせに適切な回答が与えられる可能性が上がります。

### <a name="choosing-a-threshold"></a>しきい値の選択

しきい値として使用される既定の[信頼度スコア](confidence-score.md)は 0 ですが、ニーズに基づいてナレッジ ベースの[しきい値を変更](confidence-score.md#set-threshold)できます。 すべてのナレッジ ベースは異なっているため、ナレッジ ベースに最も適したしきい値をテストして選択する必要があります。

### <a name="choosing-ranker-type"></a>ランカーの種類の選択
既定では、QnA Maker は質問と回答を検索します。 質問のみを検索して、回答を生成する場合、GenerateAnswer 要求の POST 本文で `RankerType=QuestionOnly` を使用します。

### <a name="add-alternate-questions"></a>代わりの質問を追加する
[代わりの質問](../How-To/edit-knowledge-base.md)を用意すること (別の表現で言い換えること) で、ユーザーからの問い合わせに一致する可能性が向上します。 代わりの質問は、同じ質問にさまざまな表現が使われるときに役立ちます。 これには、文の構造や言葉遣いの変化が含まれることがあります。

|元の質問|代わりの質問|Change|
|--|--|--|
|駐車場は空いていますか?|駐車場はありますか?|文の構造|
 |やあ|よう<br>おい!|言葉遣いまたはスラング|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>メタデータ タグを使用して質問と回答をフィルター処理する

[メタデータ](../How-To/edit-knowledge-base.md)は、クライアント アプリケーションがすべての回答を取得する代わりにメタデータ タグに基づいてユーザー クエリの結果を絞り込む必要があることを認識する機能を追加します。 ナレッジ ベースの回答は、問い合わせが同じであっても、メタデータ タグに基づいて異なる場合があります。 たとえば、*"where is parking located"* (駐車場はどこですか) という問いに対し、レストランの支店の場所が違えば、つまり、*Location: Seattle* (所在地: シアトル) のときと、*Location: Redmond* (所在地: レドモンド) のときで答えが変わります。

### <a name="use-synonyms"></a>同義語を使用する
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)
英語では同意語をある程度サポートしていますが、さまざま言い方があるキーワードに同意語を追加するには、[Alterations API](/rest/api/cognitiveservices/qnamaker/alterations/replace) を介して、大文字と小文字が区別されない言葉の変更機能を使用します。 同義語は QnA Maker サービスレベルで追加され、**サービス内の全ナレッジ ベースで共有されます**。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)
英語では同意語をある程度サポートしていますが、さまざま言い方があるキーワードに同意語を追加するには、[Alterations API](/rest/api/cognitiveservices/qnamaker/alterations/replace) を介して、大文字と小文字が区別されない言葉の変更機能を使用します。 QnA Maker マネージド (プレビュー) の同意語は **ナレッジ ベースごとに追加** されます。

|元の語|シノニム|
|--|--|
|買う|購入する<br>net 銀行<br>ネット バンキング|

---

### <a name="use-distinct-words-to-differentiate-questions"></a>質問を区別する目的で明確な言葉を使う
QnA Maker のランク付けアルゴリズムはユーザーからの問い合わせとナレッジ ベースに登録されている質問を一致させるアルゴリズムですが、このアルゴリズムはそれぞれの質問が異なるニーズに対処する場合に最も効果を発揮します。 複数の質問で同じ言葉が繰り返し使われていると、その言葉が含まれる問い合わせが届いたとき、正しい回答が選ばれる可能性が下がります。

たとえば、2 つの個別 QnA があり、次のような質問内容であるとします。

|QnA|
|--|
|駐車 *場所* はどこですか|
|ATM の "*場所*" はどこですか|

これら 2 つの QnA は非常によく似た語で表現されていますが、この類似性のために、「*`<x>` の場所はどこですか*」のように表現されるユーザーの問い合わせの多くが非常に近いスコアになる可能性があります。 代わりに、ナレッジ ベース内の多くの質問に存在するかもしれない「場所」のような語を避け、"*駐車場はどこですか*" や "*ATM はどこですか*" のような質問で違いを明確にしてください。

## <a name="collaborate"></a>協力
QnA Maker では、ユーザーはナレッジ ベースに協力できます。 ナレッジ ベースにアクセスするためには、Azure QnA Maker リソース グループへのアクセス権がユーザーに必要です。 ナレッジ ベースの編集と保守管理を外注する組織もありますが、その場合でも Azure リソースへのアクセスを保護できます。 この編集者/承認者モデルは、異なるサブスクリプションで同じ [QnA Maker サービス](../How-to/set-up-qnamaker-service-azure.md)を 2 つ設定し、その 1 つを編集/テスト サイクルに選択することで実現されます。 テストが終了したら、[インポート/エクスポート](../Tutorials/migrate-knowledge-base.md) プロセスを利用して、承認者の QnA Maker サービスにナレッジ ベース コンテンツを転送します。この承認者が最終的にナレッジ ベースを公開し、エンドポイントを更新します。



## <a name="active-learning"></a>アクティブ ラーニング

[アクティブ ラーニング](../How-to/use-active-learning.md)は、幅広い質と量のユーザー ベースのクエリがある場合に、代替の質問を提案するという最高の仕事をします。 クライアント アプリケーションのユーザー クエリが、検閲なしのアクティブ ラーニングのフィードバック ループに参加できるようにすることが重要です。 QnA Maker ポータルで質問が提案されたら、**[提案によるフィルター処理](../How-To/improve-knowledge-base.md)** を行い、それらの提案をレビューして、承認または拒否する必要があります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ナレッジ ベースを編集する](../How-to/edit-knowledge-base.md)
