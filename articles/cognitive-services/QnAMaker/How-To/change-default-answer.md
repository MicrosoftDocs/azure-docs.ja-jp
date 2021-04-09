---
title: 既定の回答を取得する - QnA Maker
description: 規定の回答は、質問に対する回答が見つからなかった場合に返されます。 標準の既定の回答から既定の応答を変更することもできます。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 11/09/2020
ms.openlocfilehash: 5aab021ab5194b4af18e3ff1b2c154ed74710353
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96346122"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>QnA Maker リソースの既定の回答を変更する

ナレッジ ベースの既定の回答は、回答が見つからない場合に返されることを意図しています。 [Azure Bot Service](/azure/bot-service/bot-builder-howto-qna) などのクライアント アプリケーションを使用している場合は、スコアしきい値を満たす回答がないことを示す別個の既定の回答が用意されている場合もあります。

## <a name="types-of-default-answer"></a>既定の回答の種類

ナレッジ ベースには既定の回答が 2 種類あります。 予測クエリからそれぞれが返される方法とタイミングを理解することが重要です。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

|既定の回答の種類|回答の説明|
|--|--|
|回答が決定されないときの KB 回答|`No good match found in KB.` - [GenerateAnswer API](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) で質問に対応する回答が見つからないとき、App Service の `DefaultAnswer` 設定が返されます。 同じ QnA Maker リソース内のすべてのナレッジ ベースは、同じ既定の回答テキストを共有します。<br>Azure portal、App Service、あるいは設定を[取得](/rest/api/appservice/webapps/listapplicationsettings)または[更新](/rest/api/appservice/webapps/updateapplicationsettings)するための REST API を利用し、設定を管理できます。|
|フォローアップ プロンプトの指示テキスト|会話フローでフォローアップ プロンプトを使用するとき、ユーザーにフォローアップ プロンプトから選択してもらうため、QnA ペアの回答は場合によっては不要です。 この場合、フォローアップ プロンプトの予測ごとに返される既定の回答テキストを設定することで特定のテキストを設定します。 このテキストは、フォローアップ プロンプトの選択時、指示テキストとして表示されます。 この既定の回答テキストの例が `Please select from the following choices` です。 この構成については、このドキュメントの後続セクションで説明しています。 [REST API](/rest/api/cognitiveservices/qnamaker/knowledgebase/create) を使用し、`defaultAnswerUsedForExtraction` のナレッジ ベース定義の一部としても設定できます。|

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

|既定の回答の種類|回答の説明|
|--|--|
|回答が決定されないときの KB 回答|`No good match found in KB.` - [GenerateAnswer API](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) で質問に対応する回答が見つからないとき、既定のテキスト応答が表示されます。 QnA Maker マネージド (プレビュー) では、ナレッジ ベースの **[設定]** でこのテキストを設定できます。 <br><br> ![QnA Maker マネージド (プレビュー) 既定の回答を設定する](../media/qnamaker-how-change-default-answer/qnamaker-v2-change-default-answer.png)|
|フォローアップ プロンプトの指示テキスト|会話フローでフォローアップ プロンプトを使用するとき、ユーザーにフォローアップ プロンプトから選択してもらうため、QnA ペアの回答は場合によっては不要です。 この場合、フォローアップ プロンプトの予測ごとに返される既定の回答テキストを設定することで特定のテキストを設定します。 このテキストは、フォローアップ プロンプトの選択時、指示テキストとして表示されます。 この既定の回答テキストの例が `Please select from the following choices` です。 この構成については、このドキュメントの後続セクションで説明しています。 `defaultAnswerUsedForExtraction` でナレッジ ベースを定義するとき、[REST API](/rest/api/cognitiveservices/qnamaker/knowledgebase/create) を使用してこれを設定することもできます。|

---

### <a name="client-application-integration"></a>クライアント アプリケーション統合

**Azure Bot サービス** のボットなど、クライアント アプリケーションの場合、共通となる次のシナリオから選択できます。

* ナレッジ ベースの設定を使用する
* 回答が返されるが、スコアしきい値を満たさないとき、クライアント アプリケーションで別のテキストを使用して区別する。 このテキストは、コード内に格納されている静的テキストにするか、クライアント アプリケーションの設定リストに格納することができます。

## <a name="set-follow-up-prompts-default-answer-when-you-create-knowledge-base"></a>ナレッジ ベースを作成するときに、フォローアップ プロンプトの既定の回答を設定する

新しいナレッジ ベースを作成するとき、既定の回答テキストは設定の 1 つです。 作成プロセス中にこの設定をしなかった場合は、後で次の手順で変更できます。

## <a name="change-follow-up-prompts-default-answer-in-qna-maker-portal"></a>QnA Maker ポータルでフォローアップ プロンプトの既定の回答を変更する

QnA Maker サービスから回答が返されない場合、ナレッジ ベースの既定の回答が返されます。

1. [QnA Maker ポータル](https://www.qnamaker.ai/)にサインインし、一覧からご利用のナレッジ ベースを選択します。
1. ナビゲーション バーから **[設定]** を選択します。
1. **[Manage knowledge base]\(ナレッジ ベースの管理\)** セクションで **[Default answer text]\(既定の回答テキスト\)** の値を変更します。

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="既定の回答テキスト ボックスが強調表示されている QnA Maker ポータルの [設定] ページのスクリーンショット。":::

1. **[保存してトレーニング]** を選択して変更を保存します。

## <a name="next-steps"></a>次のステップ

* [ナレッジ ベースの作成](../How-to/manage-knowledge-bases.md)