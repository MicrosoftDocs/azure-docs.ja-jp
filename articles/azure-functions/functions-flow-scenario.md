---
title: Microsoft Flow から Azure 関数を呼び出す | Microsoft Docs
description: カスタム コネクタを作成し、そのコネクタを使用して関数を呼び出します。
services: functions
keywords: クラウド アプリ, クラウド サービス, Microsoft Flow, ビジネス プロセス, ビジネス アプリケーション
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: glenga
ms.reviewer: sunayv
ms.custom: ''
ms.openlocfilehash: d3e777b5611dec382dc4eaaac5ec1594abcdab31
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65787687"
---
# <a name="call-a-function-from-microsoft-flow"></a>Microsoft Flow から関数を呼び出す

[Microsoft Flow](https://flow.microsoft.com/) は、ユーザーが好むアプリとサービスの間でワークフローとビジネス プロセスを簡単に自動化できるようにします。 プロフェッショナルな開発者は Azure Functions を使用して Microsoft Flow の機能を拡張して構いませんが、フロー作成者は技術の詳細を把握する必要はありません。

このトピックでは、風力タービンのメンテナンス シナリオに基づいてフローを作成します。 ここで説明するのは、「[関数の OpenAPI 定義の作成](functions-openapi-definition.md)」で定義した関数を呼び出す方法です。 この関数によって、風力タービンの応急処理がコスト効率に優れているかどうかを確認します。 コスト効率に優れている場合、フローから応急処理を勧める電子メールが送信されます。

PowerApps から同じ関数を呼び出す方法について詳しくは、「[PowerApps から関数を呼び出す](functions-powerapps-scenario.md)」をご覧ください。

このトピックでは、次の方法について説明します。

> [!div class="checklist"]
> * SharePoint でリストを作成する。
> * API 定義をエクスポートする。
> * 文字列を API に追加する。
> * 応急処理がコスト効率に優れている場合に電子メールを送信するフローを作成する。
> * フローを実行する。

[!INCLUDE [functions-openapi-note](../../includes/functions-openapi-note.md)]

## <a name="prerequisites"></a>前提条件

+ ご使用の Azure アカウントと同じサインイン資格情報を使用するアクティブな [Microsoft Flow アカウント](https://flow.microsoft.com/documentation/sign-up-sign-in/)。 
+ SharePoint。このフローのデータ ソースとして使用します。 SharePoint をまだお持ちでない場合、[Office 365 試用版](https://signup.microsoft.com/Signup?OfferId=467eab54-127b-42d3-b046-3844b860bebf&dl=O365_BUSINESS_PREMIUM&ali=1)へのサインアップ。
+ 「[関数の OpenAPI 定義の作成](functions-openapi-definition.md)」を完了している。

## <a name="create-a-sharepoint-list"></a>SharePoint リストを作成する
最初に、フローのデータ ソースとして使用するリストを作成します。 リストには、次の列が含まれます。

| リストの列     | データ型           | メモ                                    |
|-----------------|---------------------|------------------------------------------|
| **Title**           | 1 行のテキスト | タービンの名前                      |
| **LastServiceDate** | Date                |                                          |
| **MaxOutput**       | Number              | タービンの出力 (KwH 単位)            |
| **ServiceRequired** | はい/いいえ              |                                          |
| **EstimatedEffort** | Number              | 応急処理の推定所要時間 (時間単位) |

1. ご使用の SharePoint サイトで、 **[新規]** 、 **[リスト]** の順にクリックまたはタップします。

    ![新しい SharePoint リストを作成する](./media/functions-flow-scenario/new-list.png)

2. `Turbines` という名前を入力してから、 **[作成]** をクリックまたはタップします。

    ![新しいリストの名前を指定する](./media/functions-flow-scenario/create-list.png)

    既定の **"Title"** フィールドがある **Turbines** リストが作成されます。

    ![Turbines リスト](./media/functions-flow-scenario/initial-list.png)

3. ![新しいアイテム アイコン](./media/functions-flow-scenario/icon-new.png)、 **[日付]** の順にクリックまたはタップします。

    ![1 行のテキスト フィールドを追加する](./media/functions-flow-scenario/add-column.png)

4. `LastServiceDate` という名前を入力してから、 **[作成]** をクリックまたはタップします。

    ![LastServiceDate 列を作成する](./media/functions-flow-scenario/date-column.png)

5. リスト内の他の 3 列についても、次のように最後の 2 つの手順を繰り返します。

    1. **[数値]** > "MaxOutput"

    2. **[はい/いいえ]** > "ServiceRequired"

    3. **[数値]** > "EstimatedEffort"

ここではここまでにしておきましょう。次の図のような空のリストが作成されます。 フローを作成した後、このリストにデータを追加します。

![空のリスト](media/functions-flow-scenario/empty-list.png)

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>文字列を API に追加する
カスタム API (カスタム コネクタとも呼ばれる) は Microsoft Flow で使用できますが、フローで使用する前に、この API に接続しておく必要があります。

1. [flow.microsoft.com](https://flow.microsoft.com) で、歯車アイコン (右上にある)、 **[接続]** の順にクリックします。

    ![フローの接続](media/functions-flow-scenario/flow-connections.png)

1. **[接続の作成]** をクリックし、 **[Turbine Repair]\(タービン修復\)** コネクタまでスクロール ダウンしてそれをクリックします。

    ![接続を作成する](media/functions-flow-scenario/create-connection.png)

1. API キーを入力して、 **[接続の作成]** をクリックします。

    ![API キーを入力して作成する](media/functions-flow-scenario/api-key.png)

> [!NOTE]
> フローを他のユーザーと共有する場合は、そのフローを操作または使用する各ユーザーも API キーを入力して API に接続する必要があります。 この動作は将来変更される可能性があります。このトピックは、その変更に合わせて更新されます。


## <a name="create-a-flow"></a>フローを作成する

これで、作成した SharePoint リストとカスタム コネクタを使用するフローを作成する準備ができました。

### <a name="add-a-trigger-and-specify-a-condition"></a>トリガーを追加して条件を指定する

まず一から (テンプレートなしで) フローを作成し、SharePoint リストでアイテム作成時に発生する*トリガー*を追加します。 その後、次に起こる動作を決定する*条件*を追加します。

1. [flow.microsoft.com](https://flow.microsoft.com) で、 **[マイ フロー]** 、 **[一から作成]** の順にクリックします。

    ![一から作成する](media/functions-flow-scenario/create-from-blank.png)

2. SharePoint トリガー **[項目が作成されたとき]** をクリックします。

    ![トリガーを選択する](media/functions-flow-scenario/choose-trigger.png)

    SharePoint にまだサインインしていない場合は、サインインを求めるメッセージが表示されます。

3. **[サイトのアドレス]** にご使用の SharePoint サイト名を入力し、 **[リスト名]** にタービン データが含まれるリストを入力します。

    ![トリガーを選択する](media/functions-flow-scenario/site-list.png)

4. **[新しいステップ]** 、 **[条件の追加]** の順にクリックします。

    ![条件を追加する](media/functions-flow-scenario/add-condition.png)

    Microsoft Flow により、次の 2 つの分岐がフローに追加されます。 **[If yes]\(はいの場合\)** と **[If no]\(いいえの場合\)** です。 マッチングする条件を定義した後、一方または両方の分岐にステップを追加します。

    ![条件分岐](media/functions-flow-scenario/condition-branches.png)

5. **[条件]** カードで、1 つ目のボックスをクリックし、 **[動的なコンテンツ]** ダイアログ ボックスから **[ServiceRequired]** を選択します。

    ![条件用のフィールドを選択する](media/functions-flow-scenario/condition1-field.png)

6. 条件で値 `True` を入力します。

    ![条件で「true」を入力する](media/functions-flow-scenario/condition1-yes.png)

    この値は、SharePoint リストでは `Yes` または `No` として表示されますが、`True` または `False` の*ブール値* として保存されます。 

7. ページの上部にある **[フローの作成]** をクリックします。 定期的に **[フローの更新]** をクリックするようにしてください。

リスト内に作成されたアイテムについて、フローは **"ServiceRequired"** フィールドが `Yes` に設定されているかどうかをチェックし、適宜 **[If yes] \(はいの場合\)** 分岐か **[If no] \(いいえの場合\)** 分岐に進みます。 このトピックでは、時間の節約のために **[If yes] \(はいの場合\)** 分岐のアクションのみを指定します。

### <a name="add-the-custom-connector"></a>カスタム コネクタを追加する

次は、Azure 内の関数を呼び出すカスタム コネクタを追加します。 標準コネクタと同様に、フローにカスタム コネクタを追加します。 

1. **[If yes] \(はいの場合\)** 分岐で、 **[アクションの追加]** をクリックします。

    ![アクションを追加する](media/functions-flow-scenario/condition1-yes-add-action.png)

2. **[アクションを選択してください]** ダイアログ ボックスで、`Turbine Repair` を検索してから、アクション **[Turbine Repair - Calculates costs]\(タービン修復 - コストの計算\)** を選択します。

    ![アクションを選択する](media/functions-flow-scenario/choose-turbine-repair.png)

    次の図は、フローに追加されているカードを示しています。 フィールドと説明は、コネクタの OpenAPI 定義から取得されます。

    ![コストの計算の既定値](media/functions-flow-scenario/calculates-costs-default.png)

3. **[Calculates costs]\(コストの計算\)** カードで、 **[動的なコンテンツ]** ダイアログ ボックスを使用して、この関数の入力内容を選択します。 Microsoft Flow では、数値フィールドは表示されますが、日付フィールドは表示されません。それは、OpenAPI 定義で **[時間]** と **[容量]** が数値に指定されているためです。

    **[時間]** に **[EstimatedEffort]** を選択し、 **[容量]** に **[MaxOutput]** を選択します。

    ![アクションを選択する](media/functions-flow-scenario/calculates-costs-fields.png)

     次は、関数の出力に基づいて別の条件を追加します。

4. **[If yes] \(はいの場合\)** 分岐の下部で、 **[More] \(詳細\)** 、 **[条件の追加]** の順にクリックします。

    ![条件を追加する](media/functions-flow-scenario/condition2-add.png)

5. **[条件 2]** カードで、1 つ目のボックスをクリックし、 **[動的なコンテンツ]** ダイアログ ボックスから **[メッセージ]** を選択します。

    ![条件用のフィールドを選択する](media/functions-flow-scenario/condition2-field.png)

6. 値 `Yes` を入力します。 関数から返されるメッセージが "はい" (修復する) か "いいえ" (修復しない) かに基づいて、次の **[If yes] \(はいの場合\)** 分岐または **[If no] \(いいえの場合\)** 分岐に進みます。 

    ![条件で「はい」を入力する](media/functions-flow-scenario/condition2-yes.png)

これで、フローは次の図のようになります。

![条件で「はい」を入力する](media/functions-flow-scenario/flow-checkpoint1.png)

### <a name="send-email-based-on-function-results"></a>関数の結果に基づいて電子メールを送信する

フローのこの時点で、関数は関数からの**メッセージ**値 `Yes` または`No` と、コストと収益可能性に関するその他の情報を返します。 2 つ目の条件の **[If yes] \(はいの場合\)** 分岐で電子メールを送信しますが、SharePoint リストへの書き戻しや[承認プロセス](https://flow.microsoft.com/documentation/modern-approvals/)の開始など、さまざまなことをいくつでも実行できます。

1. 2 つ目の条件の **[If yes] \(はいの場合\)** 分岐で、 **[アクションの追加]** をクリックします。

    ![アクションを追加する](media/functions-flow-scenario/condition2-yes-add-action.png)

2. **[アクションを選択してください]** ダイアログ ボックスで、`email` を検索してから、使用する電子メール システム (ここでは Outlook) に基づいて電子メール送信アクションを選択します。

    ![Outlook で電子メールを送信する](media/functions-flow-scenario/outlook-send-email.png)

3. **[電子メールの送信]** カードで、電子メールを作成します。 **"宛先"** フィールドに、ご自分の組織内の有効な名前を入力します。 次の図では、 **[動的なコンテンツ]** ダイアログ ボックスのテキストとトークンの組み合わせからなるその他のフィールドを示します。 

    ![電子メールのフィールド](media/functions-flow-scenario/email-fields.png)

    **Title** トークンが SharePoint リストから取得され、関数から **CostToFix** と **RevenueOpportunity** が返されます。

    完成したフローは次の図のようになります (最初の **[If no] \(いいえの場合\)** 分岐はスペースの節約のために省略します)。

    ![フローを完成させる](media/functions-flow-scenario/complete-flow.png)

4. ページ上部で **[フローの更新]** をクリックしてから、 **[完了]** をクリックします。

## <a name="run-the-flow"></a>フローを実行する

これで、フローが完成しました。SharePoint リストに行を追加して、フローがどのように応答するかをご確認ください。

1. SharePoint リストに戻り、 **[クイック編集]** をクリックします。

    ![クイック編集](media/functions-flow-scenario/quick-edit.png)

2. 編集グリッドで次の値を入力します。

    | リストの列     | 値           |
    |-----------------|---------------------|
    | **タイトル**           | Turbine 60 |
    | **LastServiceDate** | 08/04/2017 |
    | **MaxOutput**       | 2500 |
    | **ServiceRequired** | はい |
    | **EstimatedEffort** | 10 |

3. **[Done]** をクリックします。

    ![クイック編集の完了](media/functions-flow-scenario/quick-edit-done.png)

    アイテムを追加すると、フローがトリガーされます。次はこれを少し見てみましょう。

4. [flow.microsoft.com](https://flow.microsoft.com) で、 **[マイ フロー]** をクリックしてから、作成したフローをクリックします。

    ![マイ フロー](media/functions-flow-scenario/my-flows.png)

5. **[実行履歴]** の下で、[フロー実行] をクリックします。

    ![実行履歴](media/functions-flow-scenario/run-history.png)

    実行が成功した場合は、次のページでフローの処理を確認できます。 何らかの理由で実行が失敗した場合は、次のページでトラブルシューティング情報が提供されます。

6. フローの間に起こったことを確認するには、カードを展開します。 たとえば、関数への入力と関数からの出力を確認するには、 **[Calculates costs]\(コストの計算\)** カードを展開します。 

    ![コストの計算の入力と出力](media/functions-flow-scenario/calculates-costs-outputs.png)

7. **[電子メールの送信]** カードの **[宛先]** フィールドで指定したユーザーの電子メール アカウントを確認します。 フローから送信される電子メールは、次の図のようになります。

    ![フローによる電子メール](media/functions-flow-scenario/flow-email.png)

    トークンがどのようにして、SharePoint リストと関数から得た正しい値に置き換えられたかを確認できます。

## <a name="next-steps"></a>次の手順
このトピックで学習した内容は次のとおりです。

> [!div class="checklist"]
> * SharePoint でリストを作成する。
> * API 定義をエクスポートする。
> * 文字列を API に追加する。
> * 応急処理がコスト効率に優れている場合に電子メールを送信するフローを作成する。
> * フローを実行する。

Microsoft Flow について詳しくは、「[Microsoft Flow の概要](https://flow.microsoft.com/documentation/getting-started/)」をご覧ください。

Azure Functions を使用するその他の興味深いシナリオについては、「[PowerApps から関数を呼び出す](functions-powerapps-scenario.md)」と「[Azure Logic Apps と統合される関数を作成する](functions-twitter-email.md)」をご覧ください。
