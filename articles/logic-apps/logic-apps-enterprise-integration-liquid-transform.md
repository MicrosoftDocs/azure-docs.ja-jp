---
title: Liquid テンプレートを使用して JSON と XML を変換する
description: Liquid テンプレートを Azure Logic Apps のマップとして使用して JSON と XML を変換する
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 07/31/2020
ms.openlocfilehash: 0362c9ed4f736474dbd49e1bfaf1373e0f48acd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94992711"
---
# <a name="transform-json-and-xml-using-liquid-templates-as-maps-in-azure-logic-apps"></a>Liquid テンプレートを Azure Logic Apps のマップとして使用して JSON と XML を変換する

ロジック アプリ内で基本的な JSON 変換を実行する場合は、**Compose** や **Parse JSON** などのネイティブな [データ操作](../logic-apps/logic-apps-perform-data-operations.md)使用できます。 反復、制御フロー、変数などの要素を含む、高度で複雑な JSON から JSON への変換については、[Liquid](https://shopify.github.io/liquid/) オープンソース テンプレート言語を使用してこれらの変換を記述するテンプレートを作成して使用します。 また、JSON からテキスト、XML から JSON、XML からテキストなど、[その他の変換を実行する](#other-transformations)こともできます。

ロジック アプリ内で Liquid 変換を実行するには、まず、必要なマッピングを定義する Liquid テンプレートを作成する必要があります。 次に、[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)に、[テンプレートをマップとしてアップロード](../logic-apps/logic-apps-enterprise-integration-maps.md)します。 **[Transform JSON to JSON - Liquid]\(JSON を JSON に変換 - Liquid\)** アクションをロジック アプリに追加すると、使用するアクションのマップとして Liquid テンプレートを選択できます。

この記事では、次のタスクの実行方法について説明します。

* Liquid テンプレートを作成する。
* テンプレートを統合アカウントに追加する。
* Liquid 変換アクションをロジック アプリに追加する。
* 使用するマップとしてテンプレートを選択する。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* [統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* [Liquid テンプレートの言語](https://shopify.github.io/liquid/)に関する基本的な知識

  > [!NOTE]
  > **[Transform JSON to JSON - Liquid]\(JSON を JSON に変換 - Liquid\)** アクションは、[Liquid の DotLiquid 実装](https://github.com/dotliquid/dotliquid)に従います。これは、特定のケースにおいて [Liquid の Shopify 実装](https://shopify.github.io/liquid)とは異なります。 詳細については、「[Liquid テンプレートに関する考慮事項](#liquid-template-considerations)」を参照してください。

## <a name="create-the-template"></a>テンプレートを作成する

1. JSON 変換のマップとして使用する Liquid テンプレートを作成します。 必要な任意の編集ツールを使用できます。

   この例では、このセクションで説明するサンプル Liquid テンプレートを作成します。

   ```json
   {%- assign deviceList = content.devices | Split: ', ' -%}

   {
      "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
      "firstNameUpperCase": "{{content.firstName | Upcase}}",
      "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
      "devices" : [
         {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
         {%- endfor -%}
      ]
   }
   ```

1. `.liquid` 拡張子を使用してテンプレートを保存します。 この例では、`SimpleJsonToJsonTemplate.liquid` を使用します。

## <a name="upload-the-template"></a>テンプレートをアップロードする

1. Azure アカウントの資格情報で [Azure Portal](https://portal.azure.com) にサインインします。

1. Azure portal の検索ボックスに「`integration accounts`」と入力し、 **[統合アカウント]** を選択します。

   !["統合アカウント" を検索](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. 統合アカウントを探して選択します。

   ![統合アカウントを選択する](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. **[概要]** ウィンドウの **[コンポーネント]** で、 **[マップ]** を選択します。

    !["マップ" タイルを選択](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. **[マップ]** ウィンドウで、 **[追加]** を選択して、マップの詳細を入力します。

   | プロパティ | 値 | 説明 |
   |----------|-------|-------------|
   | **名前** | `JsonToJsonTemplate` | マップの名前 (この例では "JsonToJsonTemplate")。 |
   | **マップの種類** | **liquid** | マップの種類。 JSON から JSON への変換では、 **[liquid]** を選択する必要があります。 |
   | **Map** | `SimpleJsonToJsonTemplate.liquid` | 変換に使用する既存の Liquid テンプレートまたはマップ ファイル (この例では "SimpleJsonToJsonTemplate.liquid")。 このファイルを見つけるには、ファイル ピッカーを使用できます。 マップ サイズの制限については、[制限と構成](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)に関する情報を参照してください。 |
   |||

   ![Liquid テンプレートを追加する](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)

## <a name="add-the-liquid-transformation-action"></a>Liquid 変換アクションを追加する

1. Azure Portal で、次の手順に従って[空白のロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。

1. ロジック アプリ デザイナーで、ロジック アプリに[要求トリガー](../connectors/connectors-native-reqres.md#add-request)を追加します。

1. トリガーで、 **[新しいステップ]** を選択します。 検索ボックスに、フィルターとして「`liquid`」と入力し、アクションとして **[Transform JSON to JSON - Liquid]\(JSON から JSON への変換- Liquid\)** を選択します。

   ![Liquid アクションを見つけて選択する](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. **[マップ]** 一覧を開いて、Liquid テンプレート (この例では "JsonToJsonTemplate") を選択します。

   ![マップを選択する](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   マップ一覧が空の場合は、おそらくロジック アプリが統合アカウントにリンクされていません。 
   Liquid テンプレートまたはマップが含まれた統合アカウントにロジック アプリをリンクするには、次の手順に従います。

   1. ロジック アプリのメニューで、 **[ワークフロー設定]** を選択します。

   1. **[統合アカウントを選択してください]** の一覧から統合アカウントを選択し、 **[保存]** をクリックします。

      ![ロジック アプリを統合アカウントにリンクする](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. ここで、このアクションに **[コンテンツ]** プロパティを追加します。 **[新しいパラメーターの追加]** 一覧を開いて、 **[コンテンツ]** を選択します。

   ![アクションに "コンテンツ" プロパティを追加する](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. **[コンテンツ]** プロパティ値を設定するには、 **[コンテンツ]** ボックス内をクリックして、動的コンテンツ リストを表示します。 トリガーからの本文コンテンツ出力を表す **[本文]** トークンを選択します。

   !["コンテンツ" プロパティ値の "本文" トークンを選択する](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   作業が完了すると、アクションは次の例のようになります。

   ![[JSON を JSON に変換] アクションが完了](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

## <a name="test-your-logic-app"></a>ロジック アプリをテストする

[Postman](https://www.getpostman.com/postman) または同様のツールを使用して、JSON 入力をロジック アプリに送信します。 ロジック アプリからの変換された JSON 出力は、次の例のようになります。

![出力例](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

<a name="template-considerations"></a>

## <a name="liquid-template-considerations"></a>Liquid テンプレートに関する考慮事項

* Liquid テンプレートは、Azure Logic Apps における[マップのファイル サイズ制限](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)に従います。

* **[Transform JSON to JSON - Liquid]\(JSON を JSON に変換 - Liquid\)** アクションは、[Liquid の DotLiquid 実装](https://github.com/dotliquid/dotliquid)に従います。 この実装は、[Liquid の Shopify 実装](https://shopify.github.io/liquid/)からの .NET Framework へのポートであり、[特定のケース](https://github.com/dotliquid/dotliquid/issues)で異なります。

  既知の相違点を次に示します。

  * **[Transform JSON to JSON - Liquid]\(JSON を JSON に変換 - Liquid\)** アクションでは、JSON、XML、HTML などを含む文字列がネイティブに出力されます。 この Liquid アクションは、Liquid テンプレートから予期されるテキスト出力が JSON 文字列であることだけを示します。 このアクションは、入力を JSON オブジェクトとして解析し、Liquid が JSON 構造を解釈できるようにラッパーを適用することをロジック アプリに指示します。 変換後、アクションは、Liquid からのテキスト出力を解析して JSON に戻すようにロジック アプリに指示します。

    DotLiquid では JSON はネイティブに認識されないため、バックスラッシュ文字 (`\`) とその他の予約済み JSON 文字を必ずエスケープしてください。

  * テンプレートで [Liquid フィルター](https://shopify.github.io/liquid/basics/introduction/#filters)が使用されている場合は、"*文の先頭文字の大文字化*" が使用されている [DotLiquid と C# の名前付け規則](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers#filter-and-output-casing)に従っていることを確認してください。 すべての Liquid 変換で、テンプレート内のフィルター名でも文の先頭文字の大文字化が使用されていることを確認してください。 そうしないと、フィルターが機能しません。

    たとえば、`replace` フィルターを使用する場合は、`replace` ではなく `Replace` を使用します。 [DotLiquid オンライン](http://dotliquidmarkup.org/try-online)で例を試してみる場合も、同じ規則が適用されます。 詳細については、[Shopify Liquid フィルター](https://shopify.dev/docs/themes/liquid/reference/filters)と [DotLiquid Liquid フィルター](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Developers#create-your-own-filters)に関するページを参照してください。 Shopify の仕様には、各フィルターの例が含まれているため、比較のために、[DotLiquid をオンラインで試す](http://dotliquidmarkup.org/try-online)ページでそれらの例を試してみることができます。

  * 現在、Shopify 拡張機能フィルターの `json` フィルターは [DotLiquid では実装されていません](https://github.com/dotliquid/dotliquid/issues/384)。 通常は、このフィルターを使用して、JSON 文字列解析用のテキスト出力を準備できますが、代わりに `Replace` フィルターを使用する必要があります。

  * [DotLiquid 実装](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L425)の標準の `Replace` フィルターでは、[正規表現 (RegEx) による照合](/dotnet/standard/base-types/regular-expression-language-quick-reference)が使用され、[Shopify 実装](https://shopify.github.io/liquid/filters/replace/)では、[単純な文字列の照合](https://github.com/Shopify/liquid/issues/202)が使用されます。 両方の実装は、RegEx の予約文字、またはエスケープ文字を照合パラメーターで使用するまでは、同様に動作するように見えます。

    たとえば、RegEx の予約バックスラッシュ (`\`) エスケープ文字をエスケープするには、`| Replace: '\', '\\'` ではなく `| Replace: '\\', '\\'` を使用します。 次の例では、バックスラッシュ文字をエスケープしようとしたときに、`Replace` フィルターの動作がどのように異なるかを示します。 次の場合は正しく動作します。

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\\', '\\' | Replace: '"', '\"'}}"}`

    この結果は次のようになります。

    `{ "SampleText": "The quick brown fox \"jumped\" over the sleeping dog\\\\"}`

    次の場合は失敗します。

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\', '\\' | Replace: '"', '\"'}}"}`

    このようなエラーになります。

    `{ "SampleText": "Liquid error: parsing "\" - Illegal \ at end of pattern."}`

    詳細については、[Replace 標準フィルターでの RegEx パターン マッチングの使用](https://github.com/dotliquid/dotliquid/issues/385)に関する記事を参照してください。

  * [DotLiquid 実装](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L326)の `Sort` フィルターは、配列またはコレクション内の項目をプロパティによって並べ替えますが、次のような違いがあります。<p>

    * [Shopify の sort 動作](https://shopify.github.io/liquid/filters/sort/)ではなく、[Shopify の sort_natural 動作](https://shopify.github.io/liquid/filters/sort_natural/)に従います。

    * 文字列としての英数字の順序でのみ並べ替えを行います。 詳細については、[数値の並べ替え](https://github.com/Shopify/liquid/issues/980)に関する記事を参照してください。

    * 大文字と小文字を区別する順序ではなく、"*大文字と小文字を区別しない*" 順序が使用されます。 詳細については、[Sort フィルターが、Shopify の仕様にある大文字と小文字の区別についての動作に従わない]( https://github.com/dotliquid/dotliquid/issues/393)ことに関する記事を参照してください。

<a name="other-transformations"></a>

## <a name="other-transformations-using-liquid"></a>Liquid を使用したその他の変換

Liquid は、JSON 変換のみに限定されているわけではありません。 Liquid を使用して他の変換を実行することもできます。次に例を示します。

* [JSON からテキスト](#json-text)
* [XML から JSON](#xml-json)
* [XML からテキスト](#xml-text)

<a name="json-text"></a>

### <a name="transform-json-to-text"></a>JSON からテキストへの変換

この例で使用する Liquid テンプレートを次に示します。

```json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

サンプルの入力と出力を次に示します。

![JSON からテキストへの出力例](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

<a name="xml-json"></a>

### <a name="transform-xml-to-json"></a>XML から JSON への変換

この例で使用する Liquid テンプレートを次に示します。

``` json
[{% JSONArrayFor item in content -%}
      {{item}}
  {% endJSONArrayFor -%}]
```

`JSONArrayFor` ループは、XML 入力用のカスタム ループ機構です。これにより、末尾のコンマを回避する JSON ペイロードを作成できます。 また、このカスタム ループ機構の `where` 条件では、他の Liquid フィルターのように要素の値ではなく、XML 要素の名前が比較に使用されます。 詳細については、[「set-body ポリシーの詳細情報」の「もののコレクション」](https://azure.microsoft.com/blog/deep-dive-on-set-body-policy)を参照してください。

サンプルの入力と出力を次に示します。

![XML から JSON への出力例](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

<a name="xml-text"></a>

### <a name="transform-xml-to-text"></a>XML からテキストへの変換

この例で使用する Liquid テンプレートを次に示します。

``` json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

サンプルの入力と出力を次に示します。

![XML からテキストへの出力例](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>次のステップ

* [Shopify Liquid 言語と例](https://shopify.github.io/liquid/basics/introduction/)
* [DotLiquid](http://dotliquidmarkup.org/)
* [DotLiquid - オンラインで試す](http://dotliquidmarkup.org/try-online)
* [DotLiquid GitHub](https://github.com/dotliquid/dotliquid)
* [DotLiquid GitHub の問題](https://github.com/dotliquid/dotliquid/issues/)
* [マップ](../logic-apps/logic-apps-enterprise-integration-maps.md)についての詳細情報
