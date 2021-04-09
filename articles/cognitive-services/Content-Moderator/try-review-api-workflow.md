---
title: REST API コンソールを使用してモデレーション ワークフローを定義する - Content Moderator
titleSuffix: Azure Cognitive Services
description: Azure Content Moderator Review API シリーズを使用し、ご自分のコンテンツ ポリシーに基づいてカスタム ワークフローとしきい値を定義できます。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 79749533d636f4b73ff3bef6b12d9e842ac485ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96905172"
---
# <a name="define-and-use-moderation-workflows-api-console"></a>モデレーション ワークフローを定義して使用する (API コンソール)

ワークフローは、コンテンツをより効率的に処理するために使用できるクラウドベースのカスタマイズされたフィルターです。 ワークフローでは、さまざまなサービスに接続してさまざまな方法でコンテンツをフィルター処理した後、適切なアクションを実行することができます。 このガイドでは、API コンソールからワークフローの REST API を使用して、ワークフローを作成し、使用する方法を説明します。 API の構造を理解すれば、これらの呼び出しを REST と互換性のあるプラットフォームに簡単に移植することができます。

## <a name="prerequisites"></a>前提条件

- Content Moderator [レビュー ツール](https://contentmoderator.cognitive.microsoft.com/) サイトにサインインするか、アカウントを作成します。

## <a name="create-a-workflow"></a>ワークフローを作成する

ワークフローを作成または更新するには、「 **[ワークフロー - 作成または更新](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** 」 API リファレンス ページに移動し、お使いの主なリージョンのボタンを選択します。 お使いのリージョンは、[レビュー ツール](https://contentmoderator.cognitive.microsoft.com/)の「**資格情報**」ページのエンドポイント URL から見つけることができます。 これにより API コンソールが開始されます。ここで REST API 呼び出しを簡単に構築し実行することができます。

![[Workflow - Create Or Update]\(ワークフロー - 作成または更新\) ページのリージョンの選択](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>REST 呼び出しパラメーターを入力する

**[team]**、**[workflowname]**、**[Ocp-Apim-Subscription-Key]** に値を入力します。

- **team**: [レビュー ツール](https://contentmoderator.cognitive.microsoft.com/) アカウントの設定時に作成したチーム ID (レビュー ツールの [資格情報] 画面の **[ID]** フィールド内にあります)。
- **workflowname**: 追加する新しいワークフローの名前 (または、既存のワークフローを更新する場合は、既存の名前)。
- **Ocp-Apim-Subscription-Key**: ご自分の Content Moderator キー。 このキーは、[レビュー ツール](https://contentmoderator.cognitive.microsoft.com)の **[設定]** タブ内にあります。

![[Workflow - Create Or Update]\(ワークフロー - 作成または更新\) コンソールのクエリ パラメーターとヘッダー](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>ワークフローの定義を入力する

1. **[Request body]\(要求の本文\)** ボックスを編集して、**Description** および **Type** (`Image` または `Text`) の詳細を指定した JSON 要求を入力します。
2. **Expression** については、既定のワークフローの JSON 式をコピーします。 最終的な JSON 文字列は次のようになります。

```json
{
  "Description":"<A description for the Workflow>",
  "Type":"Text",
  "Expression":{
    "Type":"Logic",
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isAdult",
      "Operator":"eq",
      "Value":"true",
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    }
  }
}
```

> [!NOTE]
> この API を使用すると、ご自分のワークフローに単純な式、複雑な式、入れ子にされた式を定義することができます。 [Workflow - Create Or Update](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) のドキュメントには、より複雑なロジックの例が示されています。

### <a name="submit-your-request"></a>要求を送信する
  
**[Send]** を選択します。 操作が成功すると、**[Response status]\(応答の状態\)** は `200 OK` になり、**[Response Content]\(応答のコンテンツ\)** ボックスには `true` と表示されます。

### <a name="examine-the-new-workflow"></a>新しいワークフローを確認する

[レビュー ツール](https://contentmoderator.cognitive.microsoft.com/)で、**[設定]** > **[ワークフロー]** の順に選択します。 ご自分の新しいワークフローが一覧に表示されます。

![レビュー ツールのワークフローリスト](images/workflow-console-new-workflow.PNG)

ご自分のワークフローの **[編集]** オプションを選択し、 **[デザイナー]** タブに移動します。ここでは、JSON ロジックの直感的な表現を確認できます。

![選択したワークフローの [Designer]\(デザイナー\) タブ](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>ワークフローの詳細を取得する

既存のワークフローに関する詳細を取得するには、**[Workflow - Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** API リファレンス ページに移動し、ご自分のリージョン (ご自分のキーが管理されているリージョン) のボタンを選択します。

![[Workflow - Get]\(ワークフロー - 取得\) のリージョン選択](images/test-drive-region.png)

上記のセクションと同様に、REST 呼び出しパラメーターを入力します。 今度は、**[workflowname]** が既存のワークフローの名前であることを確認してください。

![クエリ パラメーターとヘッダーを取得する](images/workflow-get-default.PNG)

**[Send]** を選択します。 操作が成功すると、 **[Response status]\(応答の状態\)** は `200 OK` になり、 **[Response Content]\(応答のコンテンツ\)** ボックスには次の例のような JSON 形式のワークフローが表示されます。

```json
{
  "Name":"default",
  "Description":"Default",
  "Type":"Image",
  "Expression":{
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isadult",
      "Operator":"eq",
      "Value":"true",
      "AlternateInput":null,
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "Subteam":null,
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    },
    "Else":null,
    "Type":"Logic"
  }
}
```

## <a name="next-steps"></a>次のステップ

- [コンテンツ モデレーション ジョブ](try-review-api-job.md) でワークフローを使用する方法について学習します。