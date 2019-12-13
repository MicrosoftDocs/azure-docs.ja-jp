---
title: Form Recognizer とは
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services Form Recognizer を使用すると、フォーム ドキュメントからキーと値のペアやテーブル データを識別して抽出できます。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 5b8628a8a235e89614ab87dcc2020915db459f38
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978426"
---
# <a name="what-is-form-recognizer"></a>Form Recognizer とは

Azure Form Recognizer は、機械学習テクノロジを使用して、フォーム ドキュメントからキーと値のペアおよびテーブル データを識別して抽出するコグニティブ サービスです。 Form Recognizer は、元のファイル内の関係を含む構造化データを出力します。 複雑さを軽減し、ワークフローまたはアプリケーションに簡単に統合するために、単純な REST API を使用してカスタム Form Recognizer モデルを呼び出すことができます。 開始するうえで必要になるのは、入力素材と同じ種類の 5 つの入力済みフォーム ドキュメントか、または 2 つの入力済みフォーム ドキュメントと 1 つの空のフォームだけです。 手作業による操作やデータ サイエンスに関する専門知識があまりなくても、特定のコンテンツに合わせた正確な結果がすばやく得られます。

## <a name="custom-models"></a>カスタム モデル

Form Recognizer のカスタム モデルでは、独自のデータでトレーニングを行います。また、5 つのサンプル入力フォームだけで開始できます。 入力データを送信すると、アルゴリズムによって種類別にフォームが分類されて、存在するキーとテーブルが検出されます。また、値がキーに、エントリがテーブルに関連付けられます。 Form Recognizer は、元のファイル内の関係を含む構造化データを出力します。 モデルをトレーニングした後、モデルをテストおよび再トレーニングでき、最終的にはモデルを使用して、ニーズに従ってより多くのフォームから正確にデータを抽出できます。

教師なし学習では、手作業によるデータのラベル付けまたは集中的なコーディングとメンテナンスを行うことなく、モデルはフィールドとエントリ間のレイアウトと関係を理解できます。 これに対し、事前トレーニング済みの機械学習モデルでは、標準化されたデータが必要です。 これらは、業界固有のフォームなど、従来の形式から逸脱する入力素材を使用するため、精度が低くなります。

## <a name="prebuilt-receipt-model"></a>あらかじめ構築されたレシート モデル

Form Recognizer には、レシートを読み取るためのモデルもあります。 このモデルでは、取引日時、販売店情報、税金と合計金額などの主要な情報が抽出されます。 さらに、あらかじめ構築されたレシート モデルは、レシート内のすべてのテキストを認識して返すようにトレーニングされています。

## <a name="what-it-includes"></a>備えている機能

Form Recognizer は、REST API として利用できます。 これらの API を呼び出すことによって、カスタム モデルを作成、トレーニング、およびスコア付けしたり、あらかじめ構築されたモデルにアクセスしたりできます。 必要な場合は、カスタム モデルをローカルの Docker コンテナー内でトレーニングおよび実行できます。

## <a name="input-requirements"></a>入力の要件
### <a name="custom-model"></a>カスタム モデル

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt-receipt-model"></a>あらかじめ構築されたレシート モデル

レシート モデルの入力要件は少し異なります。

* 形式は、JPEG、PNG、BMP、PDF (テキストまたはスキャン)、または TIFF である必要があります。
* ファイル サイズは 20 MB 未満である必要があります。
* 画像の寸法は、50 x 50 ピクセルから 10,000 x 10,000 ピクセルの間である必要があります。 
* PDF の寸法は、最大で 17 x 17 インチにする必要があります (リーガル サイズまたは A3 サイズ以下の用紙に対応します)。
* PDF および TIFF の場合、最初の 200 ページのみが処理されます (Free レベルのサブスクリプションでは、最初の 2 ページのみが処理されます)。

## <a name="request-access"></a>アクセスの要求

Form Recognizer は、アクセスが制限されたプレビューで利用可能です。 プレビューへのアクセスを取得するには、[Form Recognizer アクセス要求](https://aka.ms/FormRecognizerRequestAccess)フォームに記入して送信します。 このフォームでは、ユーザー、会社、Form Recognizer を使用するユーザー シナリオに関する情報が要求されます。 要求が Azure Cognitive Services チームによって承認されると、サービスへのアクセス手順を説明したメールが届きます。

## <a name="where-do-i-start"></a>どこから始めるか

**手順 1:** Azure portal で Form Recognizer リソースを作成します。

**手順 2:** REST API を使用するクイック スタートに従います。
* [クイック スタート:cURL で REST API を使用して Form Recognizer モデルをトレーニングし、フォーム データを抽出する](quickstarts/curl-train-extract.md)
* [クイック スタート:Python で REST API を使用して Form Recognizer モデルをトレーニングし、フォーム データを抽出する](quickstarts/python-train-extract.md)
* [クイック スタート:cURL を使用してレシートのデータを抽出する](quickstarts/curl-receipts.md)
* [クイック スタート:Python を使用してレシートのデータを抽出する](quickstarts/python-receipts.md)

テクノロジを学習している場合は、無料のサービスを使用することをお勧めします。 無料のページは 1 か月あたり 500 ページに制限されていることに注意してください。

**手順 3:** REST API を確認します

次の API を使用して、トレーニングを行い、フォームから構造化データを抽出します。

|||
|---|---|
| モデルのトレーニング| 同じ種類の 5 つのフォームを使用して、フォームを分析する新しいモデルをトレーニングします。 または、1 つの空のフォームと 2 つの入力済みフォームを使用してトレーニングします。  |
| フォームの分析 |ストリームとして渡された単一のドキュメントを分析し、カスタム モデルを使用してフォームからキーと値のペアとテーブルを抽出します。  |
| レシートの分析 |単一のレシート ドキュメントを分析し、レシート内の主要な情報とその他のテキストを抽出します。|

詳しくは、[REST API のリファレンス ドキュメント](https://aka.ms/form-recognizer/api)をご覧ください。 

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

このサービスは、[オンライン サービス条件](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)に基づいて、Azure サービスの[プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)として提供されます。 Cognitive Services 全般に言えることですが、Form Recognizer サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 詳細については、Microsoft セキュリティ センターの [Cognitive Services のページ](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)を参照してください。

## <a name="next-steps"></a>次の手順

[クイック スタート](quickstarts/curl-train-extract.md)をすべて終え、[Form Recognizer API シリーズ](https://aka.ms/form-recognizer/api)の使用を開始します。