---
title: Azure Maps Search Service を使用して場所を検索する
description: Azure Maps Search Service について説明します。 この一連の API を使用して、ジオコーディング、逆ジオコーディング、あいまい検索、および交差点住所の逆引き検索を行う方法について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/19/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: dddf56edf2037d87a28589a59834db32f8d04a4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98598360"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Azure Maps Search Service を使用して場所を検索する

Azure Maps [Search Service](/rest/api/maps/search) は、開発者が住所、場所、名前またはカテゴリ別の事業の一覧、およびその他の地理情報を検索できるように設計された RESTful API のセットです。 サービスでは、従来のジオコーディングをサポートするだけでなく、緯度と経度に基づいて住所や交差道路の逆ジオコーディングを行うこともできます。 検索で返された緯度と経度の値は、[Route](/rest/api/maps/route) サービスや [Weather](/rest/api/maps/weather) サービスなどの他の Azure Maps サービスでパラメーターとして使用できます。


この記事では、次の方法について学習します。

* [住所検索 API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) を使用して、住所 (ジオコード アドレスの場所) の緯度と経度の座標を要求する
* [あいまい検索 API](/rest/api/maps/search/getsearchfuzzy) を使用して、住所または目的地 (POI) を検索する
* 座標位置を番地に変換するために [住所の逆引き検索](/rest/api/maps/search/getsearchaddressreverse)を行う
* [交差点住所の逆引き検索 API](/rest/api/maps/search/getsearchaddressreversecrossstreet) を使用して、座標位置を人間が理解できる交差道路に変換する  多くの場合、これは、デバイスまたはアセットから GPS フィードを受信し、座標が配置されている場所を知る必要があるアプリケーションをトラッキングする場合に必要です。

## <a name="prerequisites"></a>前提条件

1. [Azure Maps アカウントを作成します](quick-demo-map-app.md#create-an-azure-maps-account)
2. [プライマリ サブスクリプション キー (主キーまたはサブスクリプション キーとも呼ばれます) を取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account)。

このチュートリアルでは [Postman](https://www.postman.com/) アプリケーションを使用していますが、別の API 開発環境を選択することもできます。

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>住所の緯度と経度を要求する (ジオコーディング)

この例では、Azure Maps の [Get Search Address API](/rest/api/maps/search/getsearchaddress) を使用して、住所を緯度および経度の座標に変換します。 このプロセスは *ジオコーディング* とも呼ばれています。 応答では、座標が返されるだけでなく、番地、郵便番号、地方自治体、国または地域の情報などの詳細な住所プロパティも返されます。

>[!TIP]
>ジオコーディングする住所のセットがある場合は、[Post Search Address Batch API](/rest/api/maps/search/postsearchaddressbatch) を使用して、クエリのバッチを単一の API 呼び出しで送信できます。

1. Postman アプリを開きます。 Postman アプリの上部付近で **[新規]** を選択します。 **[新規作成]** ウィンドウで **[コレクション]** を選択します。  コレクションに名前を付け、 **[作成]** ボタンを選択します。 このコレクションは、このドキュメントの残りの例で使用します。

2. 要求を作成するには、 **[新規]** をもう一度選択します。 **[新規作成]** ウィンドウで **[要求]** を選択します。 要求の **[要求名]** を入力します。 前の手順で作成したコレクションを選択し、 **[Save]\(保存\)** を選択します。

3. ビルダー タブで **GET** HTTP メソッドを選択し、次の URL を入力します。 この要求では、特定の住所 (`400 Braod St, Seattle, WA 98109`) を検索します。 この要求と、この記事で触れられているその他の要求では、`{Azure-Maps-Primary-Subscription-key}` をプライマリ サブスクリプション キーに置き換えます。

    ```http
    https://atlas.microsoft.com/search/address/json?&subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&language=en-US&query=400 Broad St, Seattle, WA 98109
    ```

4. 青い **[送信]** ボタンをクリックします。 応答本文には、1 つの場所のデータが含まれます。

5. 今度は、場所の候補が複数個ある住所を検索します。 **[パラメーター]** セクションで、`query` キーを `400 Broad, Seattle` に変更します。 青い **[送信]** ボタンをクリックします。

    :::image type="content" source="./media/how-to-search-for-address/search-address.png" alt-text="住所の検索":::

6. 次に、`query` キーを `400 Broa` に設定してみます。

7. **[送信]** ボタンをクリックします。 応答に複数の国からの応答が含まれていることがわかります。 ユーザーの関連領域に結果をジオバイアスするには、常にできるだけ多くの場所の詳細を要求に追加してください。

## <a name="using-fuzzy-search-api"></a>あいまい検索 API の使用

Azure Maps [Fuzzy Search API](/rest/api/maps/search/getsearchfuzzy) では、標準的な単一行の自由形式検索がサポートされています。 検索要求に対するユーザー入力の種類がわからない場合は、Azure Maps Search Fuzzy API を使用することをお勧めします。  クエリには、完全なアドレスまたはアドレスの一部を入力できます。 また、目的地 (POI) の名前、POI カテゴリ、ブランドの名前などの POI トークンを入力することもできます。 さらに、検索結果の関連性を高めるため、座標位置と半径を指定するか、境界ボックスを定義して、クエリ結果を制限できます。

>[!TIP]
>ほとんどの検索クエリは、より優れたパフォーマンスを得て想定外の結果を減らすために、既定で maxFuzzyLevel=1 に設定されます。 あいまいさのレベルを調整するには、`maxFuzzyLevel` または `minFuzzyLevel` パラメーターを使用します。 `maxFuzzyLevel` の詳細と省略可能なパラメーターの完全な一覧については、[あいまい検索の URI パラメーター](/rest/api/maps/search/getsearchfuzzy#uri-parameters)に関するセクションをご覧ください

### <a name="search-for-an-address-using-fuzzy-search"></a>あいまい検索を使用した住所の検索

この例では、あいまい検索を使用して、全世界で `pizza` を検索します。  次に、特定の国を範囲として検索する方法について説明します。 最後に、座標位置と半径を使用して検索の範囲を特定の領域に設定し、返される結果の数を制限する方法について説明します。

>[!IMPORTANT]
>ユーザーの関連領域に結果をジオバイアスするには、常にできるだけ多くの場所の詳細を追加してください。 詳細については、[検索のベスト プラクティス](how-to-use-best-practices-for-search.md#geobiased-search-results)に関するセクションをご覧ください。

1. Postman アプリで、 **[new]\(新規\)** をクリックし、 **[Request]\(\要求\)** を選択します。 要求の **[要求名]** を入力します。 前のセクションで作成したコレクションを選択するか、新しいコレクションを作成して、 **[Save]\(保存\)** を選択します。

2. ビルダー タブで **GET** HTTP メソッドを選択し、次の URL を入力します。 この要求と、この記事で触れられているその他の要求では、`{Azure-Maps-Primary-Subscription-key}` をプライマリ サブスクリプション キーに置き換えます。

    ```http
   https://atlas.microsoft.com/search/fuzzy/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=pizza
    ```

    >[!NOTE]
    >URL パスの _json_ 属性で、応答形式が決定まります。 この記事では、使いやすく、読みやすい json を使用します。 サポートされている他の応答形式については、[URI パラメーターのリファレンス ドキュメント](/rest/api/maps/search/getsearchfuzzy#uri-parameters)の `format` パラメーターの定義をご覧ください。

3. **[送信]** をクリックして、応答の本体を確認します。

    あいまいなクエリ文字列 "pizza" からは結果として、"pizza" と "restaurant" のカテゴリに含まれる 10 か所の[目的地](/rest/api/maps/search/getsearchpoi#searchpoiresponse) (POI) が返されました。 各結果には、該当の場所の番地、緯度値と経度値、ビュー ポート、エントリ ポイントなどの詳細が含まれています。 このクエリでは結果が雑多で、特定の参照場所には関連付けられません。
  
    次の手順では、`countrySet` パラメーターを使用して、アプリケーションの対象にする必要がある国または地域のみを指定します。 サポートされている国または地域の完全な一覧については、[検索範囲](./geocoding-coverage.md)に関するセクションをご覧ください。

4. 既定の動作では全世界が検索されるので、必要のない結果が返される可能性があります。 次に、米国のみで pizza を検索します。 **[パラメーター]** セクションに `countrySet` キーを追加し、その値を `US` に設定します。 `countrySet` キーを `US` に設定すると、結果が米国に制限されます。

    :::image type="content" source="./media/how-to-search-for-address/search-fuzzy-country.png" alt-text="米国で pizza を検索する":::

    これで、結果が国コードによってバインドされ、クエリでは米国のピザ レストランが返されます。

5. さらに絞り込んだ検索結果を取得するには、緯度と経度の座標ペアを範囲として検索できます。 座標ペア。 この例では、シアトル スペース ニードルの緯度と経度を使用します。 (訳文なし) 半径 400 メートル以内の結果のみを取得したいので、`radius` パラメーターを追加します。 また、`limit` パラメーターを追加して、最寄りの 5 つのピザ屋に結果を制限します。

    **[パラメーター]** セクションに次のキー/値のペアを追加します。

     | キー | 値 |
    |-----|------------|
    | lat | 47.620525 |
    | lon | -122.349274 |
    | radius | 400 |
    | limit | 5|

6. **[送信]** をクリックします。 応答には、シアトル スペース ニードルの近くにあるピザ レストランの結果が含まれています。

## <a name="search-for-a-street-address-using-reverse-address-search"></a>住所の逆引き検索を使用した所在地住所の検索

Azure Maps [Get Search Address Reverse API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) は、座標を人が判読できる番地に変換します。 この API は、GPS フィードを利用して特定の座標点の住所を検出する必要があるアプリケーションでよく使用されます。

>[!IMPORTANT]
>ユーザーの関連領域に結果をジオバイアスするには、常にできるだけ多くの場所の詳細を追加してください。 詳細については、[検索のベスト プラクティス](how-to-use-best-practices-for-search.md#geobiased-search-results)に関するセクションをご覧ください。

>[!TIP]
>リバース ジオコーディングする座標セットがある場合は、[Post Search Address Batch API](/rest/api/maps/search/postsearchaddressreversebatch) を使用して、クエリのバッチを単一の API 呼び出しで送信できます。

この例では、利用可能ないくつかのオプション パラメーターを使用して、逆方向の検索を行います。 省略可能なパラメーターの完全な一覧については、[逆方向の検索のパラメーター](/rest/api/maps/search/getsearchaddressreverse#uri-parameters)に関するセクションをご覧ください。

1. Postman アプリで、 **[new]\(新規\)** をクリックし、 **[Request]\(\要求\)** を選択します。 要求の **[要求名]** を入力します。 最初のセクションで作成したコレクションを選択するか、新しいコレクションを作成して、 **[Save]\(保存\)** を選択します。

2. ビルダー タブで **GET** HTTP メソッドを選択し、次の URL を入力します。 この要求と、この記事で触れられているその他の要求では、`{Azure-Maps-Primary-Subscription-key}` をプライマリ サブスクリプション キーに置き換えます。 要求は次の URL のようになります。

    ```http
    https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700&number=1
    ```

3. **[送信]** をクリックして、応答の本体を確認します。 1 つのクエリ結果が表示されます。 応答には、セーフコ フィールドのキー アドレス情報が含まれます。
  
4. 今度は、 **[パラメーター]** セクションに次のキー/値のペアを追加します。

    | キー | 値 | 戻り値
    |-----|------------|------|
    | number | 1 |通りの側 (左/右) とその数値からのオフセット位置を応答に含めることができます。|
    | returnSpeedLimit | true | その住所での制限速度が返されます。|
    | returnRoadUse | true | その住所での道路用途の種類が返されます。 使用できるすべての道路用途の種類については、[道路用途の種類](/rest/api/maps/search/getsearchaddressreverse#uri-parameters)に関するセクションをご覧ください。|
    | returnMatchType | true| 一致の種類を返します。 使用できるすべての値については、[住所の逆引き検索の結果](/rest/api/maps/search/getsearchaddressreverse#searchaddressreverseresult)に関するセクションをご覧ください

   :::image type="content" source="./media/how-to-search-for-address/search-reverse.png" alt-text="逆引き検索":::

5. **[送信]** をクリックして、応答の本体を確認します。

6. 次に、`entityType` キーを追加し、その値を `Municipality` に設定します。 `entityType` キーを使用すると、前の手順の `returnMatchType` キーがオーバーライドされます。 地方自治体に関する情報を要求しているので、`returnSpeedLimit` と `returnRoadUse` も削除する必要があります。  使用できるすべてのエンティティ型については、[エンティティ型](/rest/api/maps/search/getsearchaddressreverse#entitytype)に関するセクションをご覧ください。

    :::image type="content" source="./media/how-to-search-for-address/search-reverse-entity-type.png" alt-text="エンティティ型の逆引き検索":::

7. **[送信]** をクリックします。 この結果を手順 5 で返された結果と比較します。  要求されたエンティティ型が `municipality` であるため、応答に番地の情報は含まれていません。 また、返された `geometryId` は、Azure Maps Get [Search Polygon API](/rest/api/maps/search/getsearchpolygon) で境界多角形を要求するときに使用できます。

>[!TIP]
>これらのパラメーターの詳細と関連情報については、[逆引き検索のパラメーターに関するセクション](/rest/api/maps/search/getsearchaddressreverse#uri-parameters)をご覧ください。

## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>交差点住所の逆引き検索を使用して交差点を検索する

この例では、住所の座標に基づいて交差道路を検索します。

1. Postman アプリで、 **[new]\(新規\)** をクリックし、 **[Request]\(\要求\)** を選択します。 要求の **[要求名]** を入力します。 最初のセクションで作成したコレクションを選択するか、新しいコレクションを作成して、 **[Save]\(保存\)** を選択します。

2. ビルダー タブで **GET** HTTP メソッドを選択し、次の URL を入力します。 この要求と、この記事で触れられているその他の要求では、`{Azure-Maps-Primary-Subscription-key}` をプライマリ サブスクリプション キーに置き換えます。 要求は次の URL のようになります。
  
    ```http
   https://atlas.microsoft.com/search/address/reverse/crossstreet/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700
    ```

    :::image type="content" source="./media/how-to-search-for-address/search-address-cross.png" alt-text="交差道路の検索":::
  
3. **[送信]** をクリックして、応答の本体を確認します。 応答に `South Atlantic Street` という `crossStreet` の値が含まれていることがわかります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Maps Search Service REST API](/rest/api/maps/search)

> [!div class="nextstepaction"]
> [Azure Maps Search Service のベスト プラクティス](how-to-use-best-practices-for-search.md)