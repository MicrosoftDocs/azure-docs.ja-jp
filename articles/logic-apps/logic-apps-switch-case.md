---
title: "Azure Logic Apps で Switch ステートメントを使用する | Microsoft Docs"
description: "Switch ステートメントを使用すると、Logic Apps の式の値に基づいて異なるアクションを簡単に実行できます。"
services: logic-apps
documentationcenter: dev-center-name
manager: erikre
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: 3a028507f9bbf15c8fd52ccc7c22a5763a9b1b3e
ms.openlocfilehash: 284cfca17b5abf785f7af9569c518c4400fe36fd


---
# <a name="use-switch-statement-in-logic-apps"></a>Logic Apps で Switch ステートメントを使用する
ワークフローを作成するときは、多くの場合、オブジェクトまたは式の値に基づいて異なるアクションを実行する必要があります。 たとえば、HTTP 要求の状態コードまたは承認の電子メールの選択されたオプションに基づいて、ロジック アプリに異なる動作をさせたい場合があります。

これらのシナリオは、Switch ステートメントを使用して実現できます。ロジック アプリがトークンまたは式を評価し、同じ値を持つケースを選択してアクションを実行します。 Switch ステートメントと一致するケースは、1 つのみである必要があります。

 > [!TIP]
 > すべてのプログラミング言語と同様に、Switch ステートメントは等値演算子のみをサポートします。 その他の関係演算子 (たとえば、"より大きい" など) が必要な場合は、条件付きステートメントを使用します。
 >
 > 確定的な実行動作が確実に行われるようにするには、ケースに動的トークンまたは式ではなく、一意で静的な値を含める必要があります。

## <a name="prerequisites"></a>前提条件

- アクティブな Azure サブスクリプション。
    - アクティブな Azure サブスクリプションを持っていない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/)するか、[無料の Logic Apps](https://tryappservice.azure.com/) をお試しください。
- [Logic Apps の基本的な知識](logic-apps-what-are-logic-apps.md)。

## <a name="working-with-switch-statement-in-designer"></a>デザイナーで Switch ステートメントを使用する
Switch ステートメントの使い方を確認するために、Dropbox にアップロードされたファイルを監視するロジック アプリを作成します。 ロジック アプリは、これを SharePoint に転送する必要があるかどうかを決定するために、承認の電子メールを送信します。 承認者によって選択された値に応じて異なるアクションを実行するために、Switch ステートメントを使用します。

1. まず、ロジック アプリを作成し、**Dropbox の [When a file is created (ファイルが作成されたとき)]** トリガーを選択します。

 ![Dropbox の [When a file is created (ファイルが作成されたとき)] トリガーを使用する](./media/logic-apps-switch-case/dropbox-trigger.jpg)

2. このトリガーに、**Outlook.com の [Send approval email (承認の電子メールの送信)]** アクションを追加します。

 > [!TIP]
 > Logic Apps では、Office 365 の Outlook アカウントから承認の電子メールを送信するシナリオもサポートしています。

 - 既存の接続がない場合は、作成するよう求めるメッセージが表示されます。
 - 必要なフィールドに入力すると、approvers@contoso.com に電子メールが送信されます。
 - *[ユーザー オプション]* で「`Approve, Reject`」と入力します。

 ![接続の構成](./media/logic-apps-switch-case/send-approval-email-action.jpg)

3. Switch ステートメントを追加します。
 - **[+ 新しいステップ]**、**[...More (追加)]**、**[Add a switch statement (Switch ステートメントの追加)]** の順にクリックします。
 - *[Send approval email (承認の電子メールの送信)]* アクションの `SelectedOptions` 出力に基づいて、実行内容を選択します。この出力は **[動的なコンテンツの追加]** セレクターで見つけることができます。
 - ユーザーが `Approve` を選択した場合、"*ケース 1*" を使用して処理します。
    - 承認された場合、**SharePoint Online の [Create file (ファイルの作成)]** アクションで、元のファイルを SharePoint Online にコピーします。
    - SharePoint 上に新しいファイルがあることをユーザーに通知する別のアクションを、ケース内に追加します。
 - ユーザーが `Reject` を選択した場合、それを処理する別のケースを追加します。
    - 拒否された場合、ファイルが拒否されたことと、それ以上のアクションが必要ないことをその他の承認者に知らせる通知の電子メールを送信します。
 - `SelectedOptions` には&2; つのオプションしかないので、"*既定*" のケースは空のままにします。

 ![Switch ステートメント](./media/logic-apps-switch-case/switch.jpg)

 > [!NOTE]
 > Switch ステートメントでは、既定のケースに加えて少なくとももう&1; つのケースが必要です。

4. Switch ステートメントが完了した後、Dropbox にアップロードされた元のファイルを、**Dropbox の [Delete file (ファイルの削除)]** アクションで削除します。

5. Logic App を保存し、Dropbox にファイルをアップロードしてテストします。 その直後、承認の電子メールが届くので、オプションを選択して動作を確認します。
 > [!TIP]
 > [Logic Apps を監視する](logic-apps-monitor-your-logic-apps.md)方法を確認してください。

## <a name="understanding-code-behind"></a>分離コードを理解する
これで、Switch ステートメントを使用して、ロジック アプリを正常に作成できました。 次の分離コードを見てみましょう。

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

`"Switch"` は、Switch ステートメントの名前です。読みやすくするために変更することができます。 `"type": "Switch"` は、アクションが Switch ステートメントであることを示します。 このケースでの `"expression"` はユーザーが選択するオプションを指し、後の定義で宣言される各ケースに照らして評価されます。 `"cases"` にはケースをいくつでも含めることができます。Switch 式と一致するケースがない場合は、`"default"` 内のアクションが実行されます。

`"cases"` 内には、ケースをいくつでも含めることができます。 各ケースにおいて、`"Case 1"` はケースの名前を示しており、読みやすくするために変更することができます。 `"case"` はケースのラベルを指定します。これは Switch 式の比較対象であり、一意の定数値にする必要があります。  

## <a name="next-steps"></a>次のステップ
- その他の [Logic Apps の機能](logic-apps-use-logic-app-features.md)を試す。
- [エラーと例外の処理](logic-apps-exception-handling.md)について確認する。
- [ワークフロー言語の機能](logic-apps-author-definitions.md)をさらに詳しく確認する。
- 質問やフィードバック、または [Logic Apps の改善点](https://feedback.azure.com/forums/287593-logic-apps)がございましたら、コメントを投稿してお知らせください。


<!--HONumber=Feb17_HO2-->


