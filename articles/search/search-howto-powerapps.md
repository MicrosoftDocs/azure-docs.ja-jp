---
title: チュートリアル:Power Apps からのクエリ
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search インデックスに接続し、クエリを送信し、結果を表示する Power App を構築するためのステップ バイ ステップ ガイダンスです。
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: tutorial
ms.date: 11/17/2020
ms.openlocfilehash: e8c16f02cf6b77fa54d2a19abac48e9914aa99bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008567"
---
# <a name="tutorial-query-a-cognitive-search-index-from-power-apps"></a>チュートリアル:Power Apps から Cognitive Search インデックスに対してクエリを実行する

Power Apps の迅速なアプリケーション開発環境を活用して、Azure Cognitive Search の検索可能なコンテンツ用のカスタム アプリを作成します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Cognitive Search に接続する
> * クエリ要求を設定する
> * キャンバス アプリで結果を視覚化する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

* [Power Apps アカウント](https://make.powerapps.com)

* 検索サービスでホストされている [Hotels-sample インデックス](search-get-started-portal.md)

* [クエリ API キー](search-security-api-keys.md#find-existing-keys)

## <a name="1---create-a-custom-connector"></a>1 - カスタム コネクタを作成する

Power Apps のコネクタは、データ ソース接続です。 この手順では、クラウド内の検索インデックスに接続するカスタム コネクタを作成します。

1. Power Apps に[サインイン](https://make.powerapps.com)します。

1. 左側で **[データ]**  >  **[カスタム コネクタ]** の順に展開します。
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="[カスタム コネクタ] メニュー" border="true":::

1. **[+ New custom connector]\(+ 新しいカスタム コネクタ\)** を選択し、 **[一から作成]** を選択します。

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="[一から作成] メニュー" border="true":::

1. カスタム コネクタに名前を付け (たとえば *AzureSearchQuery*)、 **[続行]** をクリックします。

1. [全般] ページで情報を入力します。

   * [アイコンの背景色] (たとえば、#007ee5)
   * [説明] (例:「Azure Cognitive Search へのコネクタ」)
   * [ホスト] には、検索サービスの URL (たとえば `<yourservicename>.search.windows.net`) を入力する必要があります
   * [ベース URL] には、単に「/」と入力します

    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="[全般情報] ダイアログ" border="true":::

1. [セキュリティ] ページで、 **[認証の種類]** として *[API キー]* を設定し、パラメーター ラベルとパラメーター名の両方を「*api-key*」に設定します。 **[パラメーターの場所]** については、次に示すように *[ヘッダー]* を選択します。

    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="[認証の種類] オプション" border="true":::

1. [定義] ページで **[+ 新しいアクション]** を選択して、インデックスに対してクエリを実行するアクションを作成します。 概要および操作 ID の名前として「Query」という値を入力します。 *「検索インデックスに対してクエリを実行する」* のような説明を入力します。

    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="[新しいアクション] オプション" border="true":::

1. 下へスクロールします。 [要求] で **[+ サンプルからのインポート]** ボタンを選択し、検索サービスへのクエリ要求を構成します。

   * [動詞] は `GET` を選択します

   * [URL] には、検索インデックスに対するサンプル クエリを入力します (`search=*` ではすべてのドキュメントが返され、`$select=` ではフィールドを選択できます)。 API バージョンは必須です。 すべて指定した URL は、`https://mydemo.search.windows.net/indexes/hotels-sample-index/docs?search=*&$select=HotelName,Description,Address/City&api-version=2020-06-30` のようになります。

   * [ヘッダー] には、「`Content-Type`」と入力します。 この値は、後の手順で `application/json` に設定します。

     **Power Apps** は、URL の構文を使用して、クエリからパラメーターを抽出します。検索、選択、および api-version パラメーターは、ウィザードの進行に応じて構成可能になります。

       :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="[サンプルからのインポート]" border="true":::

1. **[インポート]** をクリックして、要求を自動入力します。 各パラメータの横にある **...** シンボルをクリックして、パラメーターのメタデータの設定を完了します。 各パラメーターの更新後、 **[戻る]** をクリックして、要求ページに戻ります。

   :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="サンプル ダイアログからのインポート" border="true":::

1. *search* では: **[既定値]** を `*` に設定し、 **[必須]** を *[いいえ]* に設定して、 **[可視性]** を *[なし]* に設定します。 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="検索パラメーターのメタデータ" border="true":::

1. *select* では、次のようにします。 **[既定値]** を `HotelName,Description,Address/City` に設定し、 **[必須]** を *[いいえ]* に設定して、 **[可視性]** を *[なし]* に設定します。  

    :::image type="content" source="./media/search-howto-powerapps/1-10-4-parameter-metadata-select.png" alt-text="選択パラメーターのメタデータ" border="true":::

1. *api-version* では: **[既定値]** を `2020-06-30` に設定し、 **[必須]** を *[はい]* に設定して、 **[可視性]** を *[内部]* に設定します。  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="バージョン パラメーターのメタデータ" border="true":::

1. *[Content-Type]* では、`application/json` を設定します。

1. これらの変更を行った後、**Swagger エディター** ビューに切り替えます。 [parameters] セクションには、次の構成が表示されます。

    ```JSON
    parameters:
      - {name: search, in: query, required: false, type: string, default: '*'}
      - {name: $select, in: query, required: false, type: string, default: 'HotelName,Description,Address/City'}
      - {name: api-version, in: query, required: true, type: string, default: '2020-06-30',
        x-ms-visibility: internal}
      - {name: Content-Type, in: header, required: false, type: string}
    ```

1. ウィザードに戻り、 **[3. 要求]** の手順に戻ります。 [応答] セクションまで下へスクロールします。 **[既定の応答を追加する]** をクリックします。 これは Power Apps に応答のスキーマを理解させるのに役立つため、非常に重要です。 

1. 応答のサンプルを貼り付けます。 サンプルの応答をキャプチャする簡単な方法は、Azure portal の Search エクスプローラーを使用することです。 Search エクスプローラーでは、要求と同じクエリを入力する必要がありますが、 **$top = 2** を追加して、結果を 2 つのドキュメントに制限します (`search=*&$select=HotelName,Description,Address/City&$top=2`)。 

   Power Apps では、スキーマを検出するために必要な結果はわずかです。 hotels-sample-index を使用している場合、次の応答をここでウィザードにコピーできます。

    ```JSON
    {
        "@odata.context": "https://mydemo.search.windows.net/indexes('hotels-sample-index')/$metadata#docs(*)",
        "value": [
            {
                "@search.score": 1,
                "HotelName": "Arcadia Resort & Restaurant",
                "Description": "The largest year-round resort in the area offering more of everything for your vacation – at the best value!  What can you enjoy while at the resort, aside from the mile-long sandy beaches of the lake? Check out our activities sure to excite both young and young-at-heart guests. We have it all, including being named “Property of the Year” and a “Top Ten Resort” by top publications.",
                "Address": {
                    "City": "Seattle"
                }
            },
            {
                "@search.score": 1,
                "HotelName": "Travel Resort",
                "Description": "The Best Gaming Resort in the area.  With elegant rooms & suites, pool, cabanas, spa, brewery & world-class gaming.  This is the best place to play, stay & dine.",
                "Address": {
                    "City": "Albuquerque"
                }
            }
        ]
    }
    ```

    > [!TIP] 
    > 入力できる JSON の応答には文字数制限があります。そのため、JSON を貼り付ける前に簡略化することをお勧めします。 応答のスキーマと形式は、値自体よりも重要です。 たとえば、[説明] フィールドは、最初の文だけに単純化することができます。

1. **[インポート]** をクリックして、既定の応答を追加します。

1. 右上の **[コネクタの作成]** をクリックして、定義を保存します。

1. **[閉じる]** をクリックしてコネクタを閉じます。

## <a name="2---test-the-connection"></a>2 - 接続をテストする

コネクタが最初に作成されたときに、それをテストするには、[カスタム コネクタ] の一覧から再度開く必要があります。 後で追加の更新を行う場合は、ウィザード内からテストできます。

このタスクには、[クエリ API キー](search-security-api-keys.md#find-existing-keys)が必要になります。 接続が作成されるたびに、テストの実行のためかアプリに含めるためかにかかわらず、コネクタには Azure Cognitive Search に接続するために使用されるクエリ API キーが必要です。

1. 左端の **[カスタム コネクタ]** をクリックします。

1. 一覧でコネクタ (このチュートリアルでは "AzureSearchQuery") を探します。

1. コネクタを選択し、アクションの一覧を展開して、 **[ビューのプロパティ]** を選択します。

    :::image type="content" source="./media/search-howto-powerapps/1-11-1-test-connector.png" alt-text="プロパティの表示" border="true":::

1. 右上の **[編集]** を選択します。

1. **[4. テスト]** を選択して、テスト ページを開きます。

1. [テスト操作] で、 **[+ 新しい接続]** をクリックします。

1. クエリ API キーを入力します。 これは、インデックスへの読み取り専用アクセスを行うための Azure Cognitive Search クエリです。 Azure portal で[キーを確認](search-security-api-keys.md#find-existing-keys)できます。 

1. [操作] で、 **[テスト操作]** ボタンをクリックします。 成功した場合は、200 のステータスが表示され、応答の本文では検索結果を示す JSON を確認できます。

    :::image type="content" source="./media/search-howto-powerapps/1-11-2-test-connector.png" alt-text="JSON 応答" border="true":::

## <a name="3---visualize-results"></a>3 - 結果を視覚化する

この手順では、検索ボックス、検索ボタン、および結果の表示領域を備えた Power App を作成します。 Power App は先ほど作成したカスタム コネクタに接続して、Azure Search からデータを取得します。

1. 左側で、 **[アプリ]**  >  **[+ 新しいアプリ]**  >  **[キャンバス]** の順に展開します。

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="キャンバス アプリの作成" border="true":::

1. アプリケーションの種類を選択します。 このチュートリアルでは、 **[携帯電話レイアウト]** を使用して **[空のアプリ]** を作成します。 **[Power Apps Studio]** が表示されます。

1. Studio で **[データ ソース]** タブを選択し、先ほど作成した新しいコネクタをクリックします。 この例では、*AzureSearchQuery* という名前です。 **[接続の追加]** をクリックします。

   クエリ API キーを入力します。

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="コネクタの接続" border="true":::

    これで *AzureSearchQuery* が、アプリケーションから使用できるデータ ソースとなりました。

1. **[挿入]** タブで、キャンバスにいくつかのコントロールを追加します。

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="コントロールの挿入" border="true":::

1. 次の要素を挿入します。

   * 「Query:」 という値を持つテキスト ラベル
   * テキスト入力要素 (*txtQuery* と呼びます。既定値は「*」です)
   * 「検索」 というテキストが表示されたボタン 
   * 呼び出された垂直ギャラリー (*galleryResults* と呼びます。)

    キャンバスは次のようになります。

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="コントロールのレイアウト" border="true":::

1. **[検索]** ボタンにクエリを実行させるには、次のアクションを **OnSelect** に貼り付けます。

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Query({search: txtQuery.Text}).value))
    ```

   次のスクリーンショットは、**OnSelect** アクションの数式バーを示しています。

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="ボタンの OnSelect" border="true":::

   この操作により、 *[txtQuery]* テキスト ボックス内のテキストをクエリ語句として使用した検索クエリの結果によって、*azResult* という名前の新しいコレクションが更新されます。

   > [!NOTE]
   > "The function 'ClearCollect' has some invalid functions (関数 'ClearCollect' に無効な関数が含まれています)" という式の構文エラーが表示される場合は、次のようにしてください。
   > 
   > * まず、コネクタ参照が正しいことを確認します。 コネクタ名をクリアし、コネクタの名前の入力を開始します。 Intellisense によって、適切なコネクタと動詞が提示されるはずです。
   > 
   > * 問題が解決されない場合は、コネクタを削除して、再作成してください。 コネクタのインスタンスが複数ある場合、アプリが間違ったインスタンスを使用している可能性があります。
   > 

1. 前の手順を完了したときに作成された *azResult* コレクションに垂直ギャラリー コントロールをリンクします。 

   ギャラリー コントロールを選択し、[プロパティ] ペインで次の操作を実行します。

   * **[データソース]** を *azResult* に設定します。
   * インデックス内のデータの種類に基づいて、適切な **[レイアウト]** を選択します。 この例では、 *[Title, subtitle and body]\(タイトル、サブタイトル、および本文\)* レイアウトを使用しています。
   * **[フィールド] を編集** し、視覚化するフィールドを選択します。

    コネクタを定義したときにサンプルの結果を提供しているため、アプリによりインデックスで使用できるフィールドが認識されます。
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="ギャラリー フィールド" border="true":::   
 
1. **F5** キーを押して、アプリをプレビューします。  

    :::image type="content" source="./media/search-howto-powerapps/2-8-3-final.png" alt-text="最終的なアプリ" border="true":::    

<!--     Remember that the fields can be set to calculated values.

    For the example, setting using the *"Image, Title and Subtitle"* layout and specifying the *Image* function as the concatenation of the root path for the data and the file name (for instance, `"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`) will produce the result below.

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="Final app" border="true":::         -->

## <a name="clean-up-resources"></a>リソースをクリーンアップする

独自のサブスクリプションを使用している場合は、プロジェクトの最後に、作成したリソースがまだ必要かどうかを確認してください。 リソースを実行したままにすると、お金がかかる場合があります。 リソースは個別に削除することも、リソース グループを削除してリソースのセット全体を削除することもできます。

ポータルの左側のナビゲーション ウィンドウにある **[すべてのリソース]** または **[リソース グループ]** リンクを使って、リソースを検索および管理できます。

無料サービスを使っている場合は、3 つのインデックス、インデクサー、およびデータソースに制限されることに注意してください。 ポータルで個別の項目を削除して、制限を超えないようにすることができます。

## <a name="next-steps"></a>次のステップ

Power Apps を使用すると、カスタム アプリの迅速なアプリケーション開発が可能になります。 これで、検索インデックスに接続する方法がわかりました。次は、カスタム Power App で豊富な視覚エクスペリエンスを作成する方法について説明します。

> [!div class="nextstepaction"]
> [Power Apps の学習カタログ](/powerapps/learning-catalog/get-started)