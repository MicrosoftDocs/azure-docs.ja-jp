---
title: クイック スタート:LUIS ポータルで新しいアプリを作成する
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、LUIS ポータルで新しいアプリを作成します。 アプリ、意図、エンティティの基本パーツを作成します。 インタラクティブ テスト パネルでユーザーの発話の例を入力し、予測意図を取得することにより、アプリをテストします。 アプリの構築は無料で行うことができます。Azure のサブスクリプションは不要です。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: ff666437790a1e32dde83f9e3be90b4c62637181
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307752"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>クイック スタート:LUIS ポータルで新しいアプリを作成する

このクイック スタートでは、[LUIS ポータル](https://www.luis.ai)で新しいアプリを構築します。 最初に、アプリ、**意図**、**エンティティ**の基本パーツを作成します。 その後インタラクティブ テスト パネルでユーザーの発話の例を入力し、予測意図を取得することにより、アプリをテストします。

アプリの構築は無料で行うことができます。Azure のサブスクリプションは不要です。 アプリをデプロイする準備ができたら、[アプリをデプロイするためのクイック スタート](get-started-portal-deploy-app.md)を参照します。 この記事では、Azure Cognitive Services リソースを作成し、アプリに割り当てる方法が説明されています。

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>アプリを作成する

1. コンテキスト ツールバーから **[新しいアプリの作成]** を選択します。

   [![LUIS ポータルで新しいアプリを作成する](./media/get-started-portal-build-app/create-app-in-portal.png)](./media/get-started-portal-build-app/create-app-in-portal.png#lightbox)

1. ポップアップ ウィンドウで、以下の設定を使用してアプリを構成した後、 **[完了]** を選択します。

   |設定名| 値 | 目的|
   |--|--|--|
   |名前|`myEnglishApp`|LUIS アプリの一意の名前<br>必須|
   |カルチャ|**英語**|ユーザーの発話の言語、**en-us**<br>必須|
   |説明|`App made with LUIS Portal`|アプリの説明<br>省略可能|
   | | | |

   ![新しいアプリ設定を入力する](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>意図の作成

LUIS アプリを作成したら、意図を作成する必要があります。 意図は、ユーザーから発せられたテキストを分類する手段です。 たとえば、人事管理アプリに 2 つの機能を含めるとします。 ユーザーが次のことをできるようにします。

 1. 仕事を探し、応募する
 1. 仕事に応募するためのフォームを探す

アプリの 2 つの_目的_は次の意図に対応しています。

|Intent|ユーザーから発せられたテキストの例<br>"_発話_" と呼ばれる|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

意図を作成するには、次の手順を実行します。

1. アプリの作成が完了すると、 **[ビルド]** セクションに **[意図]** ページが表示されます。 **[Create new intent]\(意図の新規作成\)** を選択します。

   [![[Create new intent]\(意図の新規作成\) ボタンを選択する](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. 意図の名前として `FindForm` を入力し、 **[完了]** を選択します。

   ![意図の名前として「FindForm」を入力する](./media/get-started-portal-build-app/create-new-intent-dialog.png)

## <a name="add-an-example-utterance"></a>発話の例を追加する

意図を作成したら、発話の例を追加します。 発話の例は、ユーザーがチャット ボットや他のクライアント アプリケーションに入力するテキストです。 ユーザーのテキストの意図を、LUIS 意図にマッピングします。

このアプリケーション例の `FindForm` 意図では、発話の例にフォーム番号を含めます。 ユーザーの要求を実行するためにクライアント アプリケーションはフォーム番号が必要なので、発話にそれを含めることは重要です。

[![FindForm 意図に発話の例を入力する](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

`FindForm` 意図に次の 15 の発話の例を追加します。

|#|発話の例|
|--|--|
|1|hrf-123456 を探しています|
|2|人事フォーム hrf-234591 はどこにありますか?|
|3|hrf-345623 はどこにありますか|
|4|hrf-345794 を私に送付していただくことは可能ですか|
|5|内部のジョブに申し込むには hrf-234695 が必要ですか?|
|6|hrf-234091 を使用して私がジョブに申し込みをしていることを、マネージャーは知る必要はありますか|
|7|hrf-234918 はどこに送信しますか? メールを受信したという応答は届きますか?|
|8|hrf-234555|
|9|hrf-234987 はいつ更新されましたか?|
|10|エンジニア リング職に申し込む際は、フォーム hrf-876345 を使用しますか|
|11|私のオープン要求に対して、新しいバージョンの hrf-765234 が送信されましたか?|
|12|国際的なジョブには、hrf-234234 を使用しますか?|
|13|hrf-234598 のスペル ミス|
|14|hrf-234567 は、新しい要件に応じて編集されますか|
|15|hrf-123456、hrf-123123、hrf-234567|

これらの発話の例は、次の点を意図的に変化させています。

* 発話の長さ
* 句読点
* 単語の選択
* 動詞の時制 (現在、過去、未来)
* 単語の順序



## <a name="create-a-regular-expression-entity"></a>正規表現エンティティを作成する

実行時の予測応答としてフォーム番号を返すには、フォームをエンティティとしてマークする必要があります。 フォーム番号のテキストは、高度に構造化されているため、正規表現エンティティを使用してマークできます。 エンティティを作成するには、以下の手順に従います。

1. 左側のメニューから **[エンティティ]** を選択します。

1. **[エンティティ]** ページ上で **[新しいエンティティを作成する]** を選択します。

1. 名前として「`Human Resources Form Number`」を入力し、エンティティ型として **[正規表現]** を選択します。また、正規表現として「`hrf-[0-9]{6}`」を入力します。 これは、リテラル文字 `hrf-` と同じで、6 桁に対応します。

   ![正規表現エンティティのエンティティ情報を入力する](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. **[完了]** を選択します。

## <a name="add-example-utterances-to-the-none-intent"></a>発話の例を None 意図に追加する

**None** 意図は、fallback 意図であるため、空のままにしないでください。 この意図には、アプリの他の意図に追加した 10 の発話の例それぞれに対して 1 つの発話を含める必要があります。

**None** 意図の発話の例は、クライアント アプリケーションのドメイン外にある必要があります。

1. 左側のメニューから **[意図]** を選択した後、意図の一覧から **[None]** を選択します。

1. 以下の発話の例を意図に追加します。

   |None 意図の発話の例|
   |--|
   |吠える犬はうっとうしい|
   |ピザを注文して|
   |海の中のペンギン|

   この人事管理アプリの場合、これらの発話の例は、ドメイン外です。 人事ドメインに動物、食品、または海洋が含まれる場合、**None** 意図には別の発話の例を使用する必要があります。

## <a name="train-the-app"></a>アプリをトレーニングする

右上のナビゲーションで **[トレーニング]** を選択すると、意図およびエンティティ モデルの変更が現在のバージョンのアプリに適用されます。

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>発話の例内の正規表現エンティティを見る

1. 左側のメニューから **[意図]** を選択して、エンティティが **FindForm** 意図内にあることを確認します。 その後、**FindForm** 意図を選択します。

   エンティティは、発話の例の中の出現箇所でマークされます。 エンティティ名ではなく、元のテキストを表示する場合は、ツールバーから **[エンティティ ビュー]** を切り替えます。

   [![エンティティによってマークが付けられたすべての発話の例](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>インタラクティブな [テスト] ウィンドウを使用して新しいアプリをテストする

LUIS ポータル内のインタラクティブな **[テスト]** ウィンドウを使用して、アプリでまだ確認されていない新しい発話からエンティティが抽出されることを確認します。

1. 右上のメニューから **[テスト]** を選択します。

1. 新しい発話を追加して、Enter キーを押します。

   ```Is there a form named hrf-234098```

   ![[テスト] ウィンドウで新しい発話をテストする](./media/get-started-portal-build-app/test-new-utterance.png)

   予測意図の上位は正しく **FindForm** になっており、信頼度は 90% を超えています (0.977)。 抽出された **Human Resources Form Number** エンティティの値は hrf-234098 です。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイック スタートが完了し、次のクイック スタートに進まない場合は、上部のナビゲーション メニューから **[マイ アプリ]** を選択します。 次に、一覧からアプリの左側にあるチェック ボックスをオンにし、一覧の上にあるコンテキスト ツール バーから **[削除]** を選択します。

[![[マイ アプリ] 一覧からアプリを削除する](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [2.アプリのデプロイ](get-started-portal-deploy-app.md)
