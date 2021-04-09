---
title: Form Recognizer とは
titleSuffix: Azure Cognitive Services
description: Azure Form Recognizer サービスを使用すると、フォーム ドキュメントからキーと値のペアとテーブル データを識別して抽出することや、領収書や名刺から主要な情報を抽出することができます。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: 自動データ処理、ドキュメント処理、自動データ入力、フォーム処理
ms.openlocfilehash: fdd482a6b0d6ca53d99cd17076ccd9a3545f7879
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467338"
---
# <a name="what-is-form-recognizer"></a>Form Recognizer とは

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Form Recognizer は、機械学習テクノロジを利用して自動データ処理ソフトウェアを構築ことを可能にするコグニティブ サービスです。 ドキュメントからテキスト、キーと値のペア、選択マーク、テーブル、構造を特定して抽出します。このサービスによって、元のファイル、境界ボックス、信頼度などのリレーションシップを含む構造化データが出力されます。 手作業による操作やデータ サイエンスに関する専門知識があまりなくても、特定のコンテンツに合わせた正確な結果がすばやく得られます。 Form Recognizer を使用して、アプリケーションのデータ入力を自動化し、ドキュメントの検索機能を強化します。

Form Recognizer は、カスタム ドキュメント処理モデルと、請求書、レシート、ID、名刺の事前構築済みモデル、およびレイアウト モデルで構成されています。 REST API またはクライアント ライブラリ SDK を使用して Form Recognizer モデルを呼び出すことにより、複雑さを軽減し、自分のワークフローやアプリケーションに統合することができます。

Form Recognizer は、次のサービスで構成されています。

* **[Layout API](#layout-api)** - テキスト、選択マーク、およびテーブルの構造を、対応する境界ボックスの座標と共にドキュメントから抽出します。
* **[カスタム モデル](#custom-models)** - フォームからテキスト、キーと値のペア、選択マーク、およびテーブル データを抽出します。 これらのモデルは自分が用意した独自のデータでトレーニングされるため、実際のフォームに合わせて調整されます。

* **[事前構築済みモデル](#prebuilt-models)** - 事前構築済みモデルを使用して、一意のドキュメントの種類からデータを抽出します。 現在使用できるのは、次の事前構築済みモデルです。

  * [Invoices](./concept-invoices.md)
  * [レシート](./concept-receipts.md)
  * [名刺](./concept-business-cards.md)
  * [ID カード](./concept-identification-cards.md)

## <a name="try-it-out"></a>試してみる

Form Recognizer サービスを試してみるには、オンラインのサンプル UI ツールにアクセスしてください。
<!-- markdownlint-disable MD025 -->
<!-- markdownlint-disable MD024 -->

### <a name="v21-preview"></a>[v2.1 プレビュー](#tab/v2-1)

> [!div class="nextstepaction"]
> [Form Recognizer を試す](https://fott-preview.azurewebsites.net/)

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!div class="nextstepaction"]
> [Form Recognizer を試す](https://fott.azurewebsites.net/)

---

Form Recognizer サービスを試すには、Azure サブスクリプション ([無料で作成](https://azure.microsoft.com/free/cognitive-services)) と [Form Recognizer リソース](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) エンドポイントおよびキーが必要です。

## <a name="layout-api"></a>Layout API

Form Recognizer では、高精細の光学式文字認識 (OCR) とドキュメントからの高度なディープ ラーニング モデルを使用して、テキスト、選択マーク、およびテーブル構造 (テキストに関連付けられた行および列番号) を抽出できます。 詳細については、[レイアウト](./concept-layout.md)の概念ガイドを参照してください。

:::image type="content" source="./media/tables-example.jpg" alt-text="テーブルの例" lightbox="./media/tables-example.jpg":::

## <a name="custom-models"></a>カスタム モデル

Form Recognizer のカスタム モデルでは、独自のデータでトレーニングを行います。また、5 つのサンプル入力フォームだけで開始できます。 トレーニング済みのドキュメント処理モデルは、元の form ドキュメント内の関係を含む構造化データを出力できます。 モデルをトレーニングした後、モデルをテストおよび再トレーニングでき、最終的にはモデルを使用して、ニーズに従ってより多くのフォームから正確にデータを抽出できます。

カスタム モデルをトレーニングする際は、ラベル付けされたデータを使用したトレーニングとラベル付けされたデータを使用しないトレーニングのどちらかを選択できます。

### <a name="train-without-labels"></a>ラベルを使用しないトレーニング

Form Recognizer では、教師なし学習を使用して、フォーム内でのレイアウトおよびフィールドやエントリ間の関係を把握します。 入力フォームを送信すると、アルゴリズムによって種類別にフォームが分類されて、存在するキーとテーブルが検出されます。また、値がキーに、エントリがテーブルに関連付けられます。 ラベルを使用しないトレーニングでは、手動によるデータのラベル付けや大量のコーディングとメンテナンスは必要ありません。この方法を最初に試してみることをお勧めします。

トレーニング ドキュメントを収集する方法のヒントについては、「[トレーニング データ セットの作成](./build-training-data-set.md)」を参照してください。

### <a name="train-with-labels"></a>ラベルを使用したトレーニング

ラベル付けされたデータによるモデルのトレーニングでは、ラベル付けされた指定のフォームを使用して、教師あり学習を使って、目的とする値が抽出されます。 ラベル付きデータにより、性能の高いモデルが得られ、複雑なフォームやキーのない値を含んだフォームでも機能するモデルを作成できます。

Form Recognizer では、[Layout API](#layout-api) を使用して、印刷または手書きのテキストの要素について予想されるサイズや位置を学習し、テーブルを抽出します。 その後、ユーザーによって指定されたラベルを使用して、ドキュメントに含まれるキーと値の関係およびテーブルを学習します。 新しいモデルをトレーニングする際はまず、手動でラベル付けされた同じタイプ (同じ構造) のフォーム 5 つを使用し、そのうえで、モデルの精度を改善するために必要であれば、ラベル付けされたデータを追加することをお勧めします。 Form Recognizer を使用すると、教師あり学習機能を使用してキーと値のペアおよびテーブルを抽出するようにモデルをトレーニングできます。 

[ラベルを使用したトレーニングを開始する](./quickstarts/label-tool.md)

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="prebuilt-models"></a>事前構築済みのモデル

Form Recognizer には、固有のフォームの種類を持つ自動データ処理用の事前構築済みモデルも含まれています。

### <a name="prebuilt-invoice-model"></a>事前構築済みの請求書モデル

事前構築済みの請求書モデルでは、さまざまな形式の請求書からデータが抽出されて、構造化データが返されます。 このモデルでは、請求書 ID、顧客の詳細、仕入先の詳細、出荷先、請求先、合計金額、税金、小計、品目などの主要な情報が抽出されます。 さらに、あらかじめ構築された請求書モデルは、請求書にあるすべてのテキストとテーブルを分析して返すようにトレーニングされています。 詳細については、[請求書](./concept-invoices.md)の概念ガイドを参照してください。

:::image type="content" source="./media/overview-invoices.jpg" alt-text="サンプル請求書" lightbox="./media/overview-invoices.jpg":::

### <a name="prebuilt-receipt-model"></a>事前構築済みのレシート モデル

事前構築済みのレシート モデルは、オーストラリア、カナダ、英国、インド、および米国のレストランやガソリン スタンド、小売店などで使用されている種類の英語のレシートを読み取るために使用されます。 このモデルでは、取引日時、販売店情報、税額、明細項目、合計金額などの主要な情報が抽出されます。 さらに、あらかじめ構築されたレシート モデルは、レシート内のすべてのテキストを分析して返すようにトレーニングされています。 詳細については、[レシート](./concept-receipts.md)の概念ガイドを参照してください。

:::image type="content" source="./media/overview-receipt.jpg" alt-text="サンプルのレシート" lightbox="./media/overview-receipt.jpg":::

### <a name="prebuilt-identification-id-cards-model"></a>事前構築済み ID カード モデル

ID カード モデルを使用すると、世界中のパスポートと米国の運転免許証から重要な情報を抽出できます。 ドキュメント ID、生年月日、有効期限日、名前、国、地域、コンピューターで読み取り可能なゾーンなどのデータが抽出されます。 詳細については、[ID カード](./concept-identification-cards.md)の概念ガイドを参照してください。

:::image type="content" source="./media/overview-id.jpg" alt-text="ID カードのサンプル" lightbox="./media/overview-id.jpg":::

### <a name="prebuilt-business-cards-model"></a>事前構築済みの名刺モデル

名刺モデルを使用すると、個人の名前、役職、住所、電子メール、会社、電話番号などの情報を英語の名刺から抽出できます。 詳細については、[名刺](./concept-business-cards.md)の概念ガイドを参照してください。

:::image type="content" source="./media/overview-business-card.jpg" alt-text="サンプルの名刺" lightbox="./media/overview-business-card.jpg":::

## <a name="get-started"></a>はじめに

Form Recognizer Tool のサンプルを使用して、お使いのドキュメントでレイアウト、モデルの事前構築、およびカスタム モデルのトレーニングを試すことができます。  

### <a name="v21-preview"></a>[v2.1 プレビュー](#tab/v2-1)

> [!div class="nextstepaction"]
> [Form Recognizer を試す](https://fott-preview.azurewebsites.net/)

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!div class="nextstepaction"]
> [Form Recognizer を試す](https://fott.azurewebsites.net/)

---
[クライアント ライブラリまたは REST API のクイックスタート](./quickstarts/client-library.md)の記事に従って、ドキュメントからのデータ抽出を開始します。 テクノロジを学習している場合は、無料のサービスを使用することをお勧めします。 無料のページは 1 か月あたり 500 ページに制限されていることに注意してください。

また、REST サンプル (GitHub) を使用して作業を開始することもできます。 

* ドキュメントからテキスト、選択マーク、およびテーブル構造を抽出する
  * [レイアウト データを抽出する - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-layout.md)
* カスタム モデルをトレーニングしてフォーム データを抽出する
  * [ラベルを使用しないトレーニング - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md)
  * [ラベルを使用したトレーニング - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)
* 請求書からデータを抽出する
  * [請求書データを抽出する - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-invoices.md)
* レシートからデータを抽出する
  * [レシートのデータを抽出する - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-receipts.md)
* 名刺からデータを抽出する
  * [名刺データを抽出する - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-business-cards.md)

### <a name="review-the-rest-apis"></a>REST API を確認します

次の API を使用して、モデルのトレーニングを行い、フォームから構造化データを抽出します。

|名前 |説明 |
|---|---|
| **Analyze Layout** | ストリームとして渡されたドキュメントを分析して、ドキュメントからテキスト、選択マーク、テーブル、および構造を抽出します。 |
| **Train Custom Model**| 同じ種類の 5 つのフォームを使用して、フォームを分析する新しいモデルをトレーニングします。 手動でラベル付けしたデータを使ってトレーニングを行うには、_useLabelFile_ パラメーターを `true` に設定します。 |
| **Analyze Form** |ストリームとして渡されたフォームを分析して、カスタム モデルを使用してフォームからテキスト、キーと値のペア、およびテーブルを抽出します。  |
| **Analyze Invoice** | 請求書を分析して、主要な情報、テーブル、およびその他の請求書テキストを抽出します。|
| **Analyze Receipt** | レシート ドキュメントを分析して、主要な情報と、レシートのその他のテキストを抽出します。|
| **Analyze ID** | ID カード ドキュメントを分析して、主要な情報やその他の ID カード テキストを抽出します。|
| **名刺を分析する** | 名刺を分析し、主要な情報とテキストを抽出します。|

### <a name="v21-preview"></a>[v2.1 プレビュー](#tab/v2-1)

詳しくは、[REST API のリファレンス ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)をご覧ください。 以前のバージョンの API をご利用の方は、「[新機能](./whats-new.md)」の記事で、レシートに関する最新の変更点をご確認ください。

### <a name="v20"></a>[v2.0](#tab/v2-0)

詳しくは、[REST API のリファレンス ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)をご覧ください。 以前のバージョンの API をご利用の方は、「[新機能](./whats-new.md)」の記事で、レシートに関する最新の変更点をご確認ください。

---

## <a name="input-requirements"></a>入力の要件

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>Docker コンテナーを使用してオンプレミスにデプロイする

[Form Recognizer コンテナー (プレビュー) を使用](form-recognizer-container-howto.md)して、API 機能をオンプレミスにデプロイします。 この Docker コンテナーを使用すると、コンプライアンス、セキュリティ、またはその他の運用上の理由により、データにより近いところでサービスを使用できます。

## <a name="service-availability-and-redundancy"></a>サービスの可用性と冗長性

### <a name="is-form-recognizer-service-zone-resilient"></a>Form Recognizer サービスにゾーン回復性はありますか?

はい。 Form Recognizer サービスには、ゾーン回復性が既定で備わっています。

### <a name="how-do-i-configure-the-form-recognizer-service-to-be-zone-resilient"></a>Form Recognizer サービスにゾーン回復性を構成するにはどうすればよいですか?

ゾーン回復性を有効にするために、顧客による構成は必要ありません。 Form Recognizer リソースのゾーン回復性は、既定で使用できるようになっており、サービス自体によって管理されます。

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

Cognitive Services 全般に言えることですが、Form Recognizer サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 詳細については、Microsoft セキュリティ センターの [Cognitive Services のページ](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)を参照してください。

## <a name="next-steps"></a>次のステップ

Form Recognizer サービスの詳細については、オンライン ツールとクイックスタートをお試しください。

* [**Form Recognizer Tool**](https://fott-preview.microsoft.com/)
* [**クライアント ライブラリと REST API のクイックスタート**](quickstarts/client-library.md)
