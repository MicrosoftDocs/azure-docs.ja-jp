---
title: Cognitive Services をスキルセットにアタッチする
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search で Cognitive Search オールインワン サブスクリプションを AI エンリッチメント パイプラインにアタッチする方法について説明します。
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 77735166fafe9d39dff483baa89a4b31db31275d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100577940"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Azure Cognitive Search で Cognitive Services リソースをスキルセットにアタッチする

Azure Cognitive Search で [AI エンリッチメント パイプライン](cognitive-search-concept-intro.md)を構成するとき、限られた数のドキュメントを無料でエンリッチできます。 ワークロードが大きくなる場合や頻繁に発生する場合は、有料の "オールインワン" Cognitive Services リソースをアタッチしてください。 "オールインワン" サブスクリプションでは、個々のサービスではなくオファリングとして "Cognitive Services" を参照し、1 つの API キーを使用してアクセス権が付与されます。

"オールインワン" Cognitive Services リソースは、スキルセットに含めることができる[定義済みのスキル](cognitive-search-predefined-skills.md)を備えています。

+ イメージ分析と光学式文字認識 (OCR) のための [Computer Vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/)
+ 言語検出、エンティティ認識、感情分析、およびキー フレーズ抽出のための [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/)
+ [テキストの変換](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)

スキルセット定義では、"オールインワン" Cognitive Services のキーは省略可能です。 1 日あたりのトランザクション数が 20 未満の場合、コストは吸収されます。 ただし、トランザクションがこの数を超える場合は、処理を続行するために有効なリソース キーが必要です。

任意の "オールインワン" リソース キーが有効です。 内部的には、"オールインワン" キーが別のリージョンのリソースに対するものである場合でも、検索サービスは同じ物理リージョンに併置されているリソースを使用します。 [利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=search)のページに、使用可能なリージョンが横並びで示されています。

> [!NOTE]
> スキルセットで定義済みのスキルを省略した場合、Cognitive Services にはアクセスされず、スキルセットでキーが指定されていても、料金は発生しません。

## <a name="how-billing-works"></a>請求体系について

+ Azure Cognitive Search では、画像とテキストのエンリッチメントに対して課金する目的でスキルセットに指定する Cognitive Services リソース キーが使用されます。 課金対象スキルの実行は、[Cognitive Services の従量課金制の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)になります。

+ 画像抽出は、エンリッチメントに先立ってドキュメントがクラックされるときに行われる Azure Cognitive Search 操作です。 画像抽出は課金対象です。 画像抽出の価格については、「[Azure Cognitive Search の価格ページ](https://azure.microsoft.com/pricing/details/search/)」をご覧ください。

+ テキスト抽出はドキュメントのクラック段階でも行われます。 それは課金対象ではありません。

+ Conditional、Shaper、Text Merge、Text Split といったスキルなど、Cognitive Services を呼び出さないスキルは課金されません。

## <a name="same-region-requirement"></a>同一リージョンの要件

Cognitive Search と Cognitive Services は両方とも、[利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=search)のページで示されているように、同じ物理リージョン内に存在する必要があります。 Cognitive Search を提供するほとんどのリージョンでは Cognitive Services も提供されています。

両方のサービスがないリージョンで AI エンリッチメントを試行した場合、"Provided key is not a valid CognitiveServices type key for the region of your search service (指定されたキーは、検索サービスのリージョンに対して有効な CognitiveServices タイプのキーではありません)" というメッセージが表示されます。

> [!NOTE]
> 一部の組み込みスキルは、非リージョンの Cognitive Services ([テキスト翻訳スキル](cognitive-search-skill-text-translation.md)など) をベースとしています。 非リージョン スキルを使用すると、Azure Cognitive Search リージョン以外のリージョンで要求に対してサービスが提供される可能性があります。 非リージョン サービスの詳細については、[Cognitive Services のリージョン別製品](https://aka.ms/allinoneregioninfo)に関するページを参照してください。

## <a name="use-free-resources"></a>無料リソースを使用する

AI エンリッチメントのチュートリアルとクイック スタートの演習を完了するため、制限された無料の処理オプションを使用することができます。

無料 (制限付きのエンリッチメント) リソースは、インデクサーごとに、1 日あたり 20 ドキュメントに制限されます。 [インデクサーをリセット](search-howto-run-reset-indexers.md)してカウンターをリセットできます。

AI エンリッチメントの **データのインポート** ウィザードを使用している場合は、 **[Add AI enrichment (Optional)]\(AI エンリッチメントの追加 (省略可能)\)** ページの [Cognitive Services をアタッチする] オプションが表示されます。

![展開された [Cognitive Services をアタッチする] セクション](./media/cognitive-search-attach-cognitive-services/attach1.png "展開された [Cognitive Services をアタッチする] セクション")

## <a name="use-billable-resources"></a>課金対象のリソースを使用する

1 日に 20 を超えるエンリッチメントを作成するワークロードでは、課金対象の Cognitive Services リソースを必ずアタッチしてください。 Cognitive Services API を呼び出す予定がない場合でも、課金対象の Cognitive Services リソースを常にアタッチしておくことをお勧めします。 リソースをアタッチすることで、1 日の制限がオーバーライドされます。

Cognitive Services API を呼び出すスキルに対してのみ課金されます。 [カスタム スキル](cognitive-search-create-custom-skill-example.md)、または[テキスト マージャー](cognitive-search-skill-textmerger.md)、[テキスト スプリッター](cognitive-search-skill-textsplit.md)、[Shaper](cognitive-search-skill-shaper.md) などの API ベースでないスキルについては課金されません。

**データのインポート** ウィザードを使用している場合は、 **[Add AI enrichment (Optional)\(AI エンリッチメントの追加 (省略可能)\)]** ページで課金対象のリソースを構成できます。

1. **[Cognitive Services をアタッチする]** を展開してから、**[新しい Cognitive Services リソースを作成します]** を選択します。 新しいタブが開き、リソースを作成できるようになります。

   ![Cognitive Services リソースを作成する](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Cognitive Services リソースの作成")

1. **[場所]** の一覧で、お使いの検索サービスと同じリージョンを選択します。

1. **[価格レベル]** 一覧で **[S0]** を選択して、Cognitive Services の機能のオールインワン コレクション (Azure Cognitive Search で提供される組み込みスキルをサポートする Vision および Language の機能を含む) を取得します。

   S0 レベルの場合、特定のワークロードの料金は、[Cognitive Services の価格ページ](https://azure.microsoft.com/pricing/details/cognitive-services/)で確認できます。
  
   + **[プランの選択]** 一覧で **[Cognitive Services]** が選択されていることを確認します。
   + **[言語]** 機能の下では、 **[Text Analytics Standard]** の料金が AI インデックスに適用されます。
   + **[Vision]** 機能の下では、**[Computer Vision S1]** の料金が適用されます。

1. **[作成]** を選択して、新しい Cognitive Services リソースをプロビジョニングします。

1. 前のタブに戻ります。 **[更新]** を選択して Cognitive Services リソースを表示し、リソースを選択します。

   ![Cognitive Services リソースを選択する](./media/cognitive-search-attach-cognitive-services/attach2.png "Cognitive Services リソースを選択する")

1. **[Add cognitive skills]\(コグニティブ スキルの追加\)** セクションを展開して、データに対して実行する特定のコグニティブ スキルを選択します。 ウィザードの残りを完了します。

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>既存のスキルセットを Cognitive Services リソースにアタッチする

既存のスキルがある場合は、新規または別の Cognitive Services リソースにアタッチできます。

1. 検索サービスの概要ページで、 **[スキルセット]** を選択します。

   ![スキルセット タブ](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "スキルセット タブ")

1. スキルセットの名前を選択し、既存のリソースを選択するか、新しいリソースを作成します。 **[OK]** を選択して変更を確認します。

   ![スキルセット リソースの一覧](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "スキルセット リソースの一覧")

   **[無料 (制限付きのエンリッチメント)]** オプションでは 1 日あたり 20 ドキュメントに制限されていること、および **[新しい Cognitive Services リソースを作成します]** を使用して新しい課金対象のリソースをプロビジョニングできることを思い出してください。 新しいリソースを作成した場合は、**[更新]** を選択して Cognitive Services リソースのリストを更新し、リソースを選択します。

## <a name="attach-cognitive-services-programmatically"></a>Cognitive Services をプログラムでアタッチする

プログラムでスキルセットを定義するときは、`cognitiveServices` セクションをスキルセットに追加します。 そのセクションで、スキルセットに関連付ける Cognitive Services リソースのキーを含めます。 リソースは Azure Cognitive Search リソースと同一リージョンに存在する必要があることを思い出してください。 `@odata.type` も追加し、それを `#Microsoft.Azure.Search.CognitiveServicesByKey` に設定します。

次の例は、このパターンを示しています。 定義の最後にある `cognitiveServices` セクションに留意してください。

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="example-estimate-costs"></a>例: コストの見積もり

コグニティブ検索のインデックス作成に関連するコストを見積もるには、まず、いくつかの試算ができるように、平均的なドキュメントがどのようなものかを把握することから開始します。 たとえば、次のように概算することができます。

+ 1000 PDF。
+ それぞれ 6 ページ。
+ 1 ページに 1 画像 (6000 画像)。
+ 1 ページあたり 3,000 文字。

各 PDF のドキュメント解析、画像とテキストの抽出、画像の光学式文字認識 (OCR)、組織のエンティティ認識からなるパイプラインを想定します。

この記事に示されている価格は仮定上のものです。 これらは、見積もりプロセスを説明するために使用されています。 お客様のコストはこれより低い可能性があります。 トランザクションの実際の価格については、[Cognitive Services の価格](https://azure.microsoft.com/pricing/details/cognitive-services)に関するページをご覧ください。

1. テキストと画像のコンテンツを含むドキュメント解読の場合、現在、テキスト抽出は無料です。 6,000 個の画像の場合、1,000 個の画像が抽出されるごとに 1 ドルと想定します。 このステップの場合、6.00 ドルのコストになります。

2. 6,000 画像の英語での OCR については、OCR コグニティブ スキルでは最適なアルゴリズム (DescribeText) が使用されます。 1,000 個のイメージを分析するたびに $2.50 かかるとすると、この手順に対して $15.00 が課金されることになります。

3. エンティティの抽出では、1 ページごとに合計 3 個のテキスト レコードがあります。 各レコードは 1,000 文字です。 1 ページあたり 3 個のテキスト レコードに 6,000 ページを掛けると、18,000 個のテキスト レコードになります。 1,000 個のテキスト レコードごとに $2.00 かかるとすると、この手順のコストは $36.00 になります。

まとめると、示されたスキルセットでこの種類の PDF ドキュメントを 1,000 個取り込むには、約 $57.00 を支払うことなります。

## <a name="next-steps"></a>次のステップ

+ [Azure Cognitive Search の価格ページ](https://azure.microsoft.com/pricing/details/search/)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [スキルセットを作成する (REST)](/rest/api/searchservice/create-skillset)
+ [エンリッチされたフィールドをマップする方法](cognitive-search-output-field-mapping.md)