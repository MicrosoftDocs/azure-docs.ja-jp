---
title: "条件付きロジックを追加してワークフローを開始する - Azure Logic Apps | Microsoft Docs"
description: "条件付きロジック、トリガー、アクション、およびパラメーターを追加することで Azure Logic Apps でのワークフローの実行を制御します。"
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e4e24de4-049a-4b3a-a14c-3bf3163287a8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2017
ms.author: stepsic
translationtype: Human Translation
ms.sourcegitcommit: 9f7d623ec213de6d46f59547aff9d4417ac95ede
ms.openlocfilehash: 41aafe94d24f0e22fe2256ab213c7668b670764c
ms.lasthandoff: 02/15/2017


---
# <a name="use-logic-apps-features"></a>Logic Apps の機能を使用する
[前のトピック](../logic-apps/logic-apps-create-a-logic-app.md)では、初めてのロジック アプリを作成しました。 ここでは、Azure Logic Apps の詳細なプロセスを作成します。 このトピックでは、次の新しい Azure Logic Apps の概念について説明します。

* 条件付きロジック。特定の条件が満たされる場合にのみアクションを実行します。
* 既存のロジック アプリを編集するためのコード ビュー。
* ワークフローを開始するためのオプション。

このトピックを完了する前に、「 [新しいロジック アプリを作成する](../logic-apps/logic-apps-create-a-logic-app.md)」の手順を完了する必要があります。 [Azure Portal] でロジック アプリを参照し、概要の **[トリガーとアクション]** をクリックしてロジック アプリの定義を編集します。

## <a name="reference-material"></a>参考資料
次の資料が役立つ場合があります。

* [Management and runtime REST APIs (管理 REST API およびランタイム REST API)](https://msdn.microsoft.com/library/azure/mt643787.aspx) - Logic Apps を直接起動する方法が記載されています。
* [Language reference (言語リファレンス)](https://msdn.microsoft.com/library/azure/mt643789.aspx) - サポートされるすべての関数/式の包括的な一覧
* [Trigger and action types (トリガーおよびアクションのタイプ)](https://msdn.microsoft.com/library/azure/mt643939.aspx) - 各種アクションとそれらで使用される入力値
* [Overview of App Service (App Service の概要)](../app-service/app-service-value-prop-what-is.md) - ソリューションを作成するときに選択するコンポーネントの説明

## <a name="add-conditional-logic-to-your-logic-app"></a>条件付きロジックをロジック アプリに追加する

ロジック アプリの元のフローが機能していても、改良できる領域もあります。

### <a name="conditional"></a>条件付き

初めてのロジック アプリの場合、メールの受信数が多くなりすぎる可能性があります。 次の手順では、条件付きロジックを追加して、ツイートが特定の数のフォロワーを持つユーザーから届いた場合にのみ電子メールを受信するようにします。

0. Logic Apps デザイナーで、**[新しいステップ]** (+) > **[アクションの追加]** を選択します。
0.    Twitter の**[ユーザーの取得]** アクションを検索して追加します。
0. Twitter ユーザーに関する情報を取得するには、トリガーから **[Tweeted by]** フィールドを見つけて追加します。

    ![ユーザーの取得](media/logic-apps-use-logic-app-features/getuser.png)

0. **[新しいステップ]** (+) > **[条件の追加]** を選択します。
0. ユーザーのフォロワー数をフィルター処理するには、**[オブジェクト名]** で **[動的なコンテンツの追加]** を追加します。 
0.    検索ボックスで、**[Followers count]** (フォロワー数) フィールドを見つけて追加します。
0. **[リレーションシップ]** で、**[より大きい]** を選択します。
0. **[値]** ボックスで、ユーザーの目的のフォロワー数を入力します。

    ![条件付き](media/logic-apps-use-logic-app-features/conditional.png)

0. 最後に、**[電子メールの送信]** ボックスを **[If Yes]** (はいの場合) ボックスにドラッグします。 

これで、フォロワー数が条件を満たしている場合にのみ、電子メールを受け取るようになります。

## <a name="repeat-actions-over-a-list-with-foreach"></a>forEach を使用してリストに対してアクションを繰り返す

ForEach ループでは、アクションを繰り返す配列を指定します。 配列ではない場合、フローは失敗します。 たとえば、メッセージの配列を出力する action1 があり、各メッセージを送信する場合は、アクションのプロパティに `forEach : "@action('action1').outputs.messages"` のような forEach ステートメントを組み込むことができます。

## <a name="edit-the-code-definition-for-a-logic-app"></a>ロジック アプリのコード定義を編集する

Logic App デザイナーがある場合でも、ロジック アプリを定義するコードを直接編集することができます。

1. コマンド バーで、**[コード ビュー]** を選択します。

    全画面のエディターが開き、編集した定義が表示されます。

    ![[コード ビュー]](media/logic-apps-use-logic-app-features/codeview.png)

    テキスト エディターで、同じロジック アプリ内やロジック アプリ間で任意の数のアクションをコピーして貼り付けることができます。 
    定義からセクション全体を追加または削除することも簡単です。他のユーザーと定義を共有することもできます。

2. 編集内容を保存するには、**[保存]** を選択します。

### <a name="parameters"></a>parameters

パラメーターなどの一部の Logic App 機能は、コード ビューでのみ使用できます。 パラメーターを使用すると、ロジック アプリ全体にわたって簡単に値を再利用できます。 たとえば、いくつかの操作で使用する電子メール アドレスがある場合、その電子メール アドレスをパラメーターとして定義する必要があります。

パラメーターは、よく変更する可能性がある値を抜き出すのに適しています。 さまざまな環境でパラメーターを上書きする必要がある場合に特に便利です。 環境に基づいてパラメーターを上書きする方法については、[REST API のドキュメント](https://docs.microsoft.com/rest/api/logic)を参照してください。

この例では、クエリ用語でパラメーターを使用できるように、既存のロジック アプリを更新する方法を示します。

1. コード ビューで、`parameters : {}` オブジェクトを見つけて、トピック オブジェクトを追加します。

        "topic" : {
            "type" : "string",
            "defaultValue" : "MicrosoftAzure"
        }

2. `twitterconnector` アクションに移動して、クエリの値を検索し、その値を `#@{parameters('topic')}` で置き換えます。 

    2 つ以上の文字列を結合する場合、`concat` 関数を使用することもできます。 
    たとえば、`@concat('#',parameters('topic'))` は上記と同じように動作します。

3.    完了したら、**[保存]** を選択します。 

    これで、1 時間ごとに、6 回以上リツイートされた新しいツイートが Dropbox の **tweets** というフォルダーに配信されます。

Logic App 定義の詳細については、 [「Author Logic App definitions (Logic App 定義のオーサリング](../logic-apps/logic-apps-author-definitions.md)」を参照してください。

## <a name="start-logic-app-workflows"></a>ロジック アプリ ワークフローを開始する

ロジック アプリで定義されているワークフローを開始するためのさまざまなオプションがあります。 ワークフローはいつでも、[Azure Portal] からオンデマンドで開始できます。

### <a name="recurrence-triggers"></a>定期実行のトリガー

定期実行のトリガーは、指定した間隔で実行されます。 トリガーに条件付きロジックが設定されている場合は、トリガーによって、ワークフローを実行する必要があるかどうかが判断されます。 トリガーから `200` の状態コードが返された場合は、ワークフローを実行する必要があることを示しています。 ワークフローを実行する必要がない場合、トリガーから `202` の状態コードが返されます。

### <a name="callback-using-rest-apis"></a>REST API を使用したコールバック

サービスでロジック アプリのエンドポイントを呼び出して、ワークフローを開始することができます。 この種のロジック アプリをオンデマンドで開始するには、コマンド バーの **[今すぐ実行]** を選択します。 [トリガーとしてのロジック アプリのエンドポイントの呼び出しによるワークフローの開始](../logic-apps/logic-apps-http-endpoint.md)に関するページを参照してください。 

<!-- Shared links -->
[Azure Portal]: https://portal.azure.com

