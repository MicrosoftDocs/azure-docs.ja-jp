---
title: "Azure Logic Apps のさまざまなアクションを行うための Switch ステートメント | Microsoft Docs"
description: "Switch ステートメントを使用して、ロジック アプリで式の値に基づいて実行するさまざまなアクションを選択します。"
services: logic-apps
keywords: "Switch ステートメント"
author: derek1ee
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: LADocs; deli
ms.openlocfilehash: 338b6a5b549d7bf81186550295608438ac4aee32
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="perform-different-actions-in-logic-apps-with-a-switch-statement"></a>switch ステートメントを使用してロジック アプリでさまざまなアクションを実行する

ワークフローを作成するときは、多くの場合、オブジェクトまたは式の値に基づいて異なるアクションを実行する必要があります。 たとえば、HTTP 要求の状態コードや電子メールで選択されたオプションに基づいて、ロジック アプリに異なる動作をさせたい場合があります。

Switch ステートメントを使用して、これらのシナリオを実装できます。 ロジック アプリは、トークンまたは式を評価し、同じ値を持つケースを選択して指定されたアクションを実行できます。 Switch ステートメントと一致するケースは、1 つのみである必要があります。

> [!TIP]
> すべてのプログラミング言語と同様に、Switch ステートメントは等値演算子のみをサポートします。 その他の関係演算子 ("より大きい" など) が必要な場合は、条件付きステートメントを使用します。
> 確定的な実行動作が確実に行われるようにするには、ケースに動的トークンまたは式ではなく、一意で静的な値を含める必要があります。

## <a name="prerequisites"></a>前提条件

- 有効な Azure サブスクリプション アクティブな Azure サブスクリプションを持っていない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/)するか、[無料の Logic Apps](https://tryappservice.azure.com/) をお試しください。
- [ロジック アプリの基本的な知識](logic-apps-what-are-logic-apps.md)。

## <a name="add-a-switch-statement-to-your-workflow"></a>Switch ステートメントをワークフローに追加する

Switch ステートメントがどのように機能するかを示すために、この例では、Dropbox にアップロードされるファイルを監視するロジック アプリを作成します。 新しいファイルがアップロードされると、ロジック アプリは、これらのファイルを SharePoint に転送するかどうかの選択を行う承認者に電子メールを送信します。 アプリは、承認者が選択した値に基づいて異なるアクションを実行する Switch ステートメントを使用します。

1. ロジック アプリを作成し、**[Dropbox - ファイルが作成されたとき]** トリガーを選択します。

   ![Dropbox の [ファイルが作成されたとき] トリガーを使用する](./media/logic-apps-switch-case/dropbox-trigger.jpg)

2. トリガーの下に **[Outlook.com - 承認の電子メールを送信します]** アクションを追加します。

   > [!TIP]
   > Logic Apps では、Office 365 の Outlook アカウントから承認電子メールを送信するシナリオもサポートしています。

   - 既存の接続がない場合は、作成するよう求めるメッセージが表示されます。
   - 必須フィールドに必要事項を入力します。 たとえば、**[宛先]** に承認の電子メールの送信先となる電子メール アドレスを指定します。
   - **[ユーザー オプション]** で「`Approve, Reject`」と入力します。

   ![接続の構成](./media/logic-apps-switch-case/send-approval-email-action.jpg)

3. Switch ステートメントを追加します。

   - 選択**+ 新しいステップ** > **しています.詳細** > **スイッチ ケースを追加**です。 
   - "*承認の電子メールを送信します*" アクションからの `SelectedOptions` の出力に基づいて実行するアクションを選択します。 
   このフィールドは、**動的コンテンツ追加**セレクターで検索できます。
   - *Case 1* を使用して、承認者が`Approve` を選択したときの処理を設定します。
     - 承認された場合は、[**[SharePoint Online  - ファイルの作成]**](../connectors/connectors-create-api-sharepointonline.md) アクションによって元のファイルを SharePoint Online にコピーします。
     - SharePoint 上に新しいファイルがあることをユーザーに通知する別のアクションを、ケース内に追加します。
   - ユーザーが `Reject` を選択したときに処理を行う別のケースを追加します。
     - 拒否された場合、ファイルが拒否されたことと、それ以上のアクションが必要ないことをその他の承認者に知らせる通知の電子メールを送信します。
   - `SelectedOptions` にはオプションが 2 つだけ用意されているため、**Default** のケースは空のままにしておくことができます。

   ![Switch ステートメント](./media/logic-apps-switch-case/switch.jpg)

   > [!NOTE]
   > Switch ステートメントでは、既定のケースに加えて、少なくとも 1 つのケースが必要です。

4. Switch ステートメントが完了した後、**[Dropbox - ファイルの削除]** アクションを追加することで、Dropbox にアップロードされた元のファイルを削除します。

5. ロジック アプリを保存し、 Dropbox にファイルをアップロードすることでアプリをテストします。 すぐに承認電子メールを受信するはずです。 オプションを選択し、動作を観察します。

   > [!TIP]
   > [Logic Apps の監視](logic-apps-monitor-your-logic-apps.md)に関するページで方法を確認してください。

## <a name="understand-the-code-behind-switch-statements"></a>Switch ステートメントの背後にあるコードを理解する

Switch ステートメントを使用してロジック アプリを正しく作成しました。ここで、Switch ステートメントの背後にあるコード定義を見てみましょう。

```json
"Switch": {
    "type": "Switch",
    "expression": "@body('Send_approval_email')?['SelectedOption']",
    "cases": {
        "Case 1" : {
            "case" : "Approved",
            "actions" : {}
        },
        "Case 2" : {
            "case" : "Rejected",
            "actions" : {}
        }
    },
    "default": {
        "actions": {}
    },
    "runAfter": {
        "Send_approval_email": [
            "Succeeded"
        ]
    }
}
```

* `"Switch"` は、Switch ステートメントの名前です。読みやすくするために変更することができます。 
* `"type": "Switch"` は、アクションが Switch ステートメントであることを示します。 
* `"expression"` は、この例でユーザーが選択するオプションであり、定義の中で後で宣言される各ケースに照らして評価されます。 
* `"cases"` は、任意の数のケースを含めることができます。 各ケースで、`"Case *"` はケースの既定の名前です。読みやすくするために名前を変更できます。 
`"case"` はケースのラベルを指定します。これは Switch 式の比較対象であり、一意の定数値にする必要があります。 Switch 式と一致するケースがない場合は、`"default"` に指定されたアクションが実行されます。

## <a name="get-help"></a>問い合わせ

[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)では、質問の投稿や質問への回答を行うことができるほか、他の Azure Logic Apps ユーザーがどのようなことを行っているかがわかります。

[Azure Logic Apps ユーザー フィードバック サイト](http://aka.ms/logicapps-wish)でアイデアへの投票やアイデアの投稿を行って、Azure Logic Apps とコネクタの改善にご協力ください。

## <a name="next-steps"></a>次のステップ

- [条件の追加](logic-apps-use-logic-app-features.md)方法を確認する
- [エラーと例外の処理](logic-apps-exception-handling.md)を確認する
- [ワークフロー言語の機能](logic-apps-author-definitions.md)をさらに詳しく確認する