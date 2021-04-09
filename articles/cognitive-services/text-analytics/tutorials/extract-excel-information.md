---
title: Text Analytics と Power Automate を使用して Excel 内の情報を抽出する
titleSuffix: Azure Cognitive Services
description: Text Analytics と Power Automate を使用して、コードを記述せずに Excel のテキストを抽出する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/27/2019
ms.author: aahi
ms.openlocfilehash: 197d28b2ac3d94b6639a6611b2919bdeb2b182e2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93359903"
---
# <a name="extract-information-in-excel-using-text-analytics-and-power-automate"></a>Text Analytics と Power Automate を使用して Excel 内の情報を抽出する 

このチュートリアルでは、コードを記述せずに Excel スプレッドシート内のテキストを抽出する Power Automate フローを作成します。 

このフローでは、あるアパートについて報告された問題のスプレッドシートを取得して、それらを 2 つのカテゴリ (配管工事とその他) に分類します。 また、送信元のテナントの名前と電話番号も抽出します。 フローでは最後に、この情報を Excel シートに追加します。 

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Power Automate を使用してフローを作成する
> * OneDrive for Business から Excel データをアップロードする
> * Excel からテキストを抽出し、それを Text Analytics API に送信する 
> * API の情報を使用して、Excel シートを更新する

## <a name="prerequisites"></a>前提条件

- Microsoft Azure アカウント。 [無料アカウントを作成](https://azure.microsoft.com/free/cognitive-services/)するか、[サインイン](https://portal.azure.com/)してください。
- Text Analytics リソース。 お持ちでない場合は、[Azure portal で作成](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)し、Free レベルを使用してこのチュートリアルを完了することができます。
- サインアップ時に生成された[キーとエンドポイント](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)。
- テナントの問題を含むスプレッドシート。 サンプル データは GitHub で提供されています。
- Microsoft 365 (OneDrive for Business)。

## <a name="add-the-excel-file-to-onedrive-for-business"></a>OneDrive for Business に Excel ファイルを追加する

[GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx)からサンプルの Excel ファイルをダウンロードします。 このファイルは、ご自分の OneDrive for Business アカウントで保存する必要があります。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Excel ファイルの例。":::

問題は、未加工のテキストで報告されます。 ここでは、Text Analytics API の名前付きエンティティ認識を使用して、個人名と電話番号を抽出します。 次に、フローで、説明の中の "plumbing" (配管工事) という語を検索して、問題を分類します。 

## <a name="create-a-new-power-automate-workflow"></a>新しい Power Automate ワークフローを作成する

[Power Automate サイト](https://preview.flow.microsoft.com/)にアクセスし、ログインします。 次に、 **[作成]** 、 **[予定フロー]** の順にクリックします。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="フローの作成画面。":::


**[予定フローを作成]** ページで、次のフィールドを使用してフローを初期化します。

|フィールド |値  |
|---------|---------|
|**フロー名**     | **スケジュールされたレビュー** または別の名前。         |
|**開始中**     |  現在の日付と時刻を入力します。       |
|**繰り返し間隔**     | **1 時間**        |

## <a name="add-variables-to-the-flow"></a>フローに変数を追加する

> [!NOTE]
> 完成したフローのイメージをご覧になりたい場合は、[GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/TextAnalytics/flow-diagrams) からダウンロードできます。 

Excel ファイルに追加する情報を表す変数を作成します。 **[新しいステップ]** をクリックし、**変数を初期化する** を検索します。 4 つの変数を作成するには、この操作を 4 回行います。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="変数を初期化する。":::

作成した変数に次の情報を追加します。 これらは、Excel ファイルの列を表します。 折りたたまれている変数がある場合は、それらをクリックして展開できます。

| アクション |名前   | Type | 値 |
|---------|---------|---|---|
| 変数を初期化する | var_person | String | Person |
| 変数を初期化する 2 | var_phone | String | Phone_Number |
| 変数を初期化する 3 | var_plumbing | String | plumbing |
| 変数を初期化する 4 | var_other | String | その他 | 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="フロー変数に含まれる情報":::

## <a name="read-the-excel-file"></a>Excel ファイルを読み取る

**[新しいステップ]** をクリックし、「**Excel**」と入力します。次に、アクションのリストから **[表内に存在する行を一覧表示]** を選択します。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="Excel の行を追加する。":::

このアクションのフィールドに入力して、Excel ファイルをフローに追加します。 このチュートリアルでは、ファイルが OneDrive for Business にアップロードされている必要があります。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="Excel の行に入力します":::

**[新しいステップ]** をクリックし、**Apply to each** アクションを追加します。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="適用コマンドを追加する。":::

**[以前の手順から出力を選択]** をクリックします。 表示される動的コンテンツのボックスで、 **[値]** を選択します。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="Excel ファイルの出力を選択する。":::

## <a name="send-a-request-to-the-text-analytics-api"></a>Text Analytics API に要求を送信する

[Text Analytics リソース](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)をまだ作成していない場合は、Azure portal で作成する必要があります。

### <a name="create-a-text-analytics-connection"></a>Text Analytics 接続を作成する

**[Apply to each]** で、 **[アクションの追加]** をクリックします。 Azure portal で Text Analytics リソースの **[キーとエンドポイント]** ページに移動し、Text Analytics リソースのキーとエンドポイントを取得します。

フローで、次の情報を入力して、新しい Text Analytics 接続を作成します。

> [!NOTE]
> Text Analytics 接続を既に作成していて、接続の詳細を変更する場合は、右上隅にある省略記号をクリックし、 **[+ 新しい接続の追加]** をクリックします。

| フィールド           | 値                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| 接続名 | 自分の Text Analytics リソースへの接続の名前。 たとえば、「 `TAforPowerAutomate` 」のように入力します。 |
| アカウント キー     | 自分の Text Analytics リソースのキー。                                                                                   |
| サイトの URL        | 自分の Text Analytics リソースのエンドポイント。                                                       |

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="Text Analytics 資格情報をフローに追加する。":::

## <a name="extract-the-excel-content"></a>Excel コンテンツを抽出する 

接続が作成されたら、**Text Analytics** を検索し、 **[エンティティ]** を選択します。 これにより、問題の説明列から情報が抽出されます。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="Text Analytics のエンティティを追加します。":::

**[テキスト]** フィールドをクリックし、表示される動的コンテンツ ウィンドウから **[Description]** を選択します。 [言語] には `en` を入力します ([言語] が表示されない場合は [詳細オプションの表示] をクリックします)。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="Text Analytics の設定を追加します。":::


## <a name="extract-the-person-name"></a>個人名を抽出する

次に、Text Analytics 出力で個人のエンティティ型を見つけます。 **[Apply to each]** 内で、 **[アクションの追加]** をクリックし、別の **Apply to each** アクションを作成します。 テキスト ボックス内をクリックし、表示される動的コンテンツ ウィンドウで **[エンティティ]** を選択します。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="Text Analytics 資格情報をフローに追加します。2":::

新しく作成した **[Apply to each 2]** アクション内で、 **[アクションの追加]** をクリックして、**条件** コントロールを追加します。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="Text Analytics 資格情報をフローに追加します。3":::

[条件] ウィンドウで、最初のテキスト ボックスをクリックします。 動的コンテンツ ウィンドウで、 **[エンティティ型]** を検索して選択します。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="Text Analytics 資格情報をフローに追加します。4":::

2 番目のボックスが **[次の値と等しい]** に設定されていることを確認します。 次に、3 番目のボックスを選択し、動的コンテンツ ウィンドウで `var_person` を検索します。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="Text Analytics 資格情報をフローに追加します。5":::

**[はいの場合]** 条件で、「Excel」 を入力し、 **[行の更新]** を選択します。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="Text Analytics 資格情報をフローに追加します。6":::

Excel の情報を入力し、 **[Key Column]\(キー列\)** 、 **[Key Value]\(キー値\)** 、 **[PersonName]\(人名\)** フィールドを更新します。 これにより、API によって検出された名前が Excel シートに追加されます。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="Text Analytics 資格情報をフローに追加します。7":::

## <a name="get-the-phone-number"></a>電話番号を取得する

名前をクリックして、**Apply to each 2** アクションを最小化します。 次に、前と同じように、別の **Apply to each** アクションを追加します。 これには **Apply to each 3** という名前が付けられます。 テキスト ボックスを選択し、このアクションの出力として **エンティティ** を追加します。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-3.png" alt-text="Text Analytics 資格情報をフローに追加します。8":::

**Apply to each 3** 内で、**条件** コントロールを追加します。 **条件 2** という名前になります。 最初のテキスト ボックスで、動的コンテンツ ウィンドウから **エンティティ型** を検索して追加します。 中央のボックスが **[次の値と等しい]** に設定されていることを確認します。 次に、右側のテキスト ボックスに「`var_phone`」と入力します。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="Text Analytics 資格情報をフローに追加します。9":::

**[はいの場合]** 条件で、**行の更新** アクションを追加します。 次に、Excel シートの電話番号の列に、上記で行ったように情報を入力します。 これにより、API によって検出された電話番号が Excel シートに追加されます。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="Text Analytics 資格情報をフローに追加します。10":::


## <a name="get-the-plumbing-issues"></a>配管工事の問題を取得する

名前をクリックして、**Apply to each 3** を最小化します。 次に、親アクションで別の **Apply to each** を作成します。 テキスト ボックスを選択し、動的コンテンツ ウィンドウからこのアクションの出力として **エンティティ** を追加します。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-4.png" alt-text="Text Analytics 資格情報をフローに追加します。11":::


次に、Excel テーブルの行の問題の説明に "plumbing" (配管工事) という語が含まれているかどうかがフローによって確認されます。 含まれている場合は、"plumbing" が [IssueType] 列に追加されます。 そうでない場合は、「other」(その他) と入力します。

**Apply to each 4** 内に、**条件** コントロールを追加します。 **条件 3** という名前になります。 動的コンテンツ ウィンドウを使用して、最初のテキスト ボックスで、Excel ファイルから **[Description]** を検索して追加します。 中央のボックスが **[を含む]** になっていることを確認します。 次に、右側のテキストボックスで、`var_plumbing` を見つけて選択します。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="Text Analytics 資格情報をフローに追加します。12":::


**[はいの場合]** 条件で、 **[アクションを追加]** をクリックし、 **[行の更新]** を選択します。 次に、前と同じように情報を入力します。 [IssueType] 列で、[`var_plumbing`] を選択します。 これにより、行に "plumbing" ラベルが適用されます。

**[いいえの場合]** 条件で、 **[アクションを追加]** をクリックし、 **[行の更新]** を選択します。 次に、前と同じように情報を入力します。 [IssueType] 列で、[`var_other`] を選択します。 これにより、行に "other" ラベルが適用されます。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="Text Analytics 資格情報をフローに追加します。13":::

## <a name="test-the-workflow"></a>ワークフローをテストする

画面の右上隅にある **[保存]** をクリックし、次に **[テスト]** をクリックします。 **[トリガーアクションを実行する]** を選択します。 **[Save & Test]\(保存してテスト\)** 、 **[フローの実行]** 、 **[完了]** の順にクリックします。

Excel ファイルは、ご自分の OneDrive アカウントで更新されます。 次のようになります。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="更新された Excel スプレッドシート。":::

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [その他のソリューションを探す](../text-analytics-user-scenarios.md)
