---
title: クイック スタート:Python 用 Form Recognizer クライアント ライブラリ
description: Python 用の Form Recognizer クライアント ライブラリを使用して、カスタム ドキュメントからキーと値のペアとテーブル データを抽出するフォーム処理アプリを作成します。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 10/26/2020
ms.author: lajanuar
ms.openlocfilehash: 445fdea4cb92ed8080528a41cf19ad774751b9ce
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510836"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!IMPORTANT]
>
> * この記事のコードでは、単純化するために、同期メソッドと、セキュリティで保護されていない資格情報の格納を使用しています。 以下のリファレンス ドキュメントを参照してください。 

[リファレンス ドキュメント](/python/api/azure-ai-formrecognizer) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer) | [パッケージ (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/) | [サンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
  * Python のインストールには、[pip](https://pip.pypa.io/en/stable/) が含まれている必要があります。 pip がインストールされているかどうかを確認するには、コマンド ラインで `pip --version` を実行します。 最新バージョンの Python をインストールして pip を入手してください。
* トレーニング データのセットを含む Azure Storage Blob。 トレーニング データ セットをまとめるためのヒントとオプションについては、「[カスタム モデルのトレーニング データ セットを作成する](../../build-training-data-set.md)」を参照してください。 このクイックスタートでは、[サンプル データ セット](https://go.microsoft.com/fwlink/?linkid=2090451)の **Train** フォルダーにあるファイルを使用できます (*sample_data.zip* をダウンロードして展開します)。
* Azure サブスクリプションを用意できたら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Form Recognizer リソースを作成"  target="_blank">Form Recognizer リソースを作成</a>し、自分のキーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
  * 自分のアプリケーションを Form Recognizer API に接続するには、作成したリソースのキーとエンドポイントが必要になります。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
  * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="setting-up"></a>設定

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

Python をインストールしたら、次のコマンドを使用して最新バージョンの Form Recognizer クライアント ライブラリをインストールすることができます。

#### <a name="v21-preview"></a>[v2.1 プレビュー](#tab/preview)

```console
pip install azure-ai-formrecognizer --pre
```

> [!NOTE]
> Form Recognizer 3.1.0 SDK は、API バージョン 2.1 プレビューを反映しています

#### <a name="v20"></a>[v2.0](#tab/ga)

```console
pip install azure-ai-formrecognizer
```

> [!NOTE]
> Form Recognizer 3.0.0 SDK は、API v2.0 を反映しています

---

### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

お気に入りのエディターまたは IDE で、新しい Python アプリケーションを作成します。 次に、次のライブラリをインポートします。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_imports)]

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py) にあり、このクイックスタートのコード例が含まれています。

自分のリソースの Azure エンドポイントおよびキー用の変数を作成します。 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_creds)]

## <a name="object-model"></a>オブジェクト モデル

Form Recognizer で作成できるクライアントは 2 種類あります。 1 つは、`form_recognizer_client` です。認識されたフォームのフィールドやコンテンツをサービスに照会するときに使用します。 もう 1 つは `form_training_client` です。認識精度を高めるために使用できるカスタム モデルを作成したり管理したりするときに使用します。 

### <a name="formrecognizerclient"></a>FormRecognizerClient

`form_recognizer_client` には、以下を目的とした操作が用意されています。

* 対象のカスタム フォームを分析するようトレーニングされたカスタム モデルを使用して、フォームのフィールドやコンテンツを認識する。
* モデルをトレーニングせずにフォームのコンテンツ (表、行、単語など) を認識する。
* Form Recognizer サービスの事前トレーニング済みの領収書モデルを使用して、領収書から一般的なフィールドを認識する。

### <a name="formtrainingclient"></a>FormTrainingClient

`form_training_client` には、以下を目的とした操作が用意されています。

* カスタム モデルをトレーニングして、対象のカスタム フォームにあるすべてのフィールドと値を分析する。 トレーニング データセットの作成について詳しくは、[ラベル付けなしのモデル トレーニングに関するサービス ドキュメント](#train-a-model-without-labels)を参照してください。
* 対象のカスタム フォームにラベル付けすることによって指定した特定のフィールドと値を分析するように、カスタム モデルをトレーニングする。 トレーニング データセットへのラベルの適用について詳しくは、[ラベル付けを使用したモデル トレーニングに関するサービス ドキュメント](#train-a-model-with-labels)を参照してください。
* アカウントに作成されたモデルを管理する。
* Form Recognizer リソース間でカスタム モデルをコピーする。

> [!NOTE]
> モデルのトレーニングは、[Form Recognizer のラベル付けツール](../../quickstarts/label-tool.md)など、グラフィカル ユーザー インターフェイスを使用して行うこともできます。

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Python 用 Form Recognizer クライアント ライブラリを使用して次のタスクを実行する方法を示します。
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
#### <a name="v21-preview"></a>[v2.1 プレビュー](#tab/preview)

* [クライアントを認証する](#authenticate-the-client)
* [レイアウトを分析する](#analyze-layout)
* [領収書を分析する](#analyze-receipts)
* [名刺を分析する](#analyze-business-cards)
* [請求書を分析する](#analyze-invoices)
* [カスタム モデルをトレーニングする](#train-a-custom-model)
* [カスタム モデルを使用してフォームを分析する](#analyze-forms-with-a-custom-model)
* [カスタム モデルを管理する](#manage-your-custom-models)

#### <a name="v20"></a>[v2.0](#tab/ga)

* [クライアントを認証する](#authenticate-the-client)
* [レイアウトを分析する](#analyze-layout)
* [領収書を分析する](#analyze-receipts)
* [カスタム モデルをトレーニングする](#train-a-custom-model)
* [カスタム モデルを使用してフォームを分析する](#analyze-forms-with-a-custom-model)
* [カスタム モデルを管理する](#manage-your-custom-models)

---

## <a name="authenticate-the-client"></a>クライアントを認証する

ここでは、上で定義したサブスクリプション変数を使用して 2 つのクライアント オブジェクトを認証します。 必要に応じて、新しいクライアント オブジェクトを作成せずに API キーを更新できるように、**AzureKeyCredential** オブジェクトを使用します。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_auth)]

## <a name="get-assets-for-testing"></a>テスト用のアセットを取得する

トレーニングとテスト データの URL への参照を追加する必要があります。

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]
  
   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL の取得":::
* 以下のサンプルに含まれるフォームや領収書のサンプル画像を使用します ([GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms) から入手することもできます)。または、上記の手順を使用して、Blob Storage 内の各ドキュメントの SAS URL を取得することもできます。 

> [!NOTE]
> このガイドのコード スニペットでは、URL でアクセスされるリモート フォームが使用されます。 ローカル フォーム ドキュメントを代わりに処理する場合は、[リファレンス ドキュメント](/python/api/azure-ai-formrecognizer)の関連するメソッドと[サンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)を参照してください。

## <a name="analyze-layout"></a>レイアウトを分析する

Form Recognizer を使用すると、ドキュメント内の表、行、および単語を分析できます。モデルをトレーニングする必要はありません。 レイアウトの抽出の詳細については、[レイアウトの概念ガイド](../../concept-layout.md)を参照してください。

指定された URL にあるファイルの内容を分析するには、`begin_recognize_content_from_url` メソッドを使用します。 返される値は `FormPage` オブジェクトのコレクションで、送信されたドキュメント内のページごとに 1 つあります。 次のコードでは、これらのオブジェクトを反復処理し、抽出されたキー/値のペアとテーブル データを出力します。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_getcontent)]

> [!TIP]
> また、ローカルの画像から内容を取得することもできます。 [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) のメソッドを参照してください (`begin_recognize_content` など)。 また、ローカルの画像に関連したシナリオについては、[GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) 上のサンプル コードを参照してください。

### <a name="output"></a>出力

```console
Table found on page 1:
Cell text: Invoice Number
Location: [Point(x=0.5075, y=2.8088), Point(x=1.9061, y=2.8088), Point(x=1.9061, y=3.3219), Point(x=0.5075, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Date
Location: [Point(x=1.9061, y=2.8088), Point(x=3.3074, y=2.8088), Point(x=3.3074, y=3.3219), Point(x=1.9061, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Due Date
Location: [Point(x=3.3074, y=2.8088), Point(x=4.7074, y=2.8088), Point(x=4.7074, y=3.3219), Point(x=3.3074, y=3.3219)]
Confidence score: 1.0

Cell text: Charges
Location: [Point(x=4.7074, y=2.8088), Point(x=5.386, y=2.8088), Point(x=5.386, y=3.3219), Point(x=4.7074, y=3.3219)]
Confidence score: 1.0
...

```

## <a name="analyze-invoices"></a>請求書を分析する

#### <a name="v21-preview"></a>[v2.1 プレビュー](#tab/preview)

このセクションでは、事前トレーニング済みのモデルを使用して、売上請求書から共通フィールドを分析、抽出する方法を示します。 請求書の分析の詳細については、[請求書の概念ガイド](../../concept-invoices.md)を参照してください。 URL から請求書を分析するには、`begin_recognize_invoices_from_url` メソッドを使用します。 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_invoice)]

> [!TIP]
> ローカルの請求書画像を分析することもできます。 [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) のメソッドを参照してください (`begin_recognize_invoices` など)。 また、ローカルの画像に関連したシナリオについては、[GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) 上のサンプル コードを参照してください。

#### <a name="v20"></a>[v2.0](#tab/ga)

> [!IMPORTANT]
> この機能は、選択した API バージョンでは使用できません。

---

## <a name="train-a-custom-model"></a>カスタム モデルをトレーニングする

このセクションでは、独自のデータを使用してモデルをトレーニングする方法を示します。 トレーニング済みのモデルは、元のフォーム ドキュメント内のキー/値の関係を含む構造化データを出力できます。 モデルをトレーニングした後、モデルをテストおよび再トレーニングでき、最終的にはモデルを使用して、ニーズに従ってより多くのフォームから正確にデータを抽出できます。

> [!NOTE]
> また、[Form Recognizer のサンプル ラベル付けツール](../../quickstarts/label-tool.md)などのグラフィカル ユーザー インターフェイスを使用してモデルをトレーニングすることもできます。

### <a name="train-a-model-without-labels"></a>ラベルなしでモデルをトレーニングする

カスタム モデルをトレーニングして、トレーニング ドキュメントに手動でラベルを付けることなく、カスタム フォームにあるすべてのフィールドと値を分析できるようにします。

次のコードは、トレーニング クライアントを `begin_training` 関数と共に使用して、特定のドキュメント セットでモデルをトレーニングします。 返される `CustomFormModel` オブジェクトには、モデルが分析できるフォームの種類と、それぞれのフォームの種類から抽出できるフィールドに関する情報が含まれています。 次のコード ブロックは、この情報をコンソールに出力します。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_train)]


### <a name="output"></a>出力

これは、[Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training) から入手できるトレーニング データを使用してトレーニングされたモデルの出力です。

```console
Model ID: 628739de-779c-473d-8214-d35c72d3d4f7
Status: ready
Training started on: 2020-08-20 23:16:51+00:00
Training completed on: 2020-08-20 23:16:59+00:00

Recognized fields:
The submodel with form type 'form-0' has recognized the following fields: Additional Notes:, Address:, Company Name:, Company Phone:, Dated As:, Details, Email:, Hero Limited, Name:, Phone:, Purchase Order, Purchase Order #:, Quantity, SUBTOTAL, Seattle, WA 93849 Phone:, Shipped From, Shipped To, TAX, TOTAL, Total, Unit Price, Vendor Name:, Website:
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

### <a name="train-a-model-with-labels"></a>ラベルを使用してモデルをトレーニングする

トレーニング ドキュメントに手動でラベルを付けて、カスタム モデルをトレーニングすることもできます。 ラベルを使用してトレーニングを行うと、一部のシナリオでパフォーマンスの向上につながります。 返される `CustomFormModel` は、モデルが抽出できるフィールドを、各フィールドの予測精度と共に示します。 次のコード ブロックは、この情報をコンソールに出力します。

> [!IMPORTANT]
> ラベルを使用してトレーニングするには、トレーニング ドキュメントと共に、自分の Blob Storage コンテナーに特別なラベル情報ファイル (`\<filename\>.pdf.labels.json`) を用意する必要があります。 [Form Recognizer のサンプル ラベル付けツール](../../quickstarts/label-tool.md)では、これらのラベル ファイルの作成を支援する UI が提供されています。 それらの用意ができたら、*use_training_labels* パラメーターを `true` に設定して、`begin_training` 関数を呼び出すことができます。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_trainlabels)]

### <a name="output"></a>出力

これは、[Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training) から入手できるトレーニング データを使用してトレーニングされたモデルの出力です。

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91

Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00

Recognized fields:
The submodel with form type 'form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91' has recognized the following fields: CompanyAddress, CompanyName, CompanyPhoneNumber, DatedAs, Email, Merchant, PhoneNumber, PurchaseOrderNumber, Quantity, Signature, Subtotal, Tax, Total, VendorName, Website
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

## <a name="analyze-forms-with-a-custom-model"></a>カスタム モデルを使用してフォームを分析する

このセクションでは、独自のフォームでトレーニングしたモデルを使用して、カスタムのフォームの種類からキー/値の情報やその他のコンテンツを抽出する方法について説明します。

> [!IMPORTANT]
> このシナリオを実装するには、モデルのトレーニングが完了している必要があります。それにより、次のメソッドにその ID を渡すことができます。 [モデルのトレーニング](#train-a-model-without-labels)に関するセクションを参照してください。

`begin_recognize_custom_forms_from_url` メソッドを使用します。 返される値は `RecognizedForm` オブジェクトのコレクションで、送信されたドキュメント内のページごとに 1 つあります。 次のコードは、分析結果をコンソールに出力します。 認識された各フィールドと対応する値が、信頼度スコアと共に出力されます。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_analyze)]

> [!TIP]
> ローカルの画像を分析することもできます。 [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) のメソッドを参照してください (`begin_recognize_custom_forms` など)。 また、ローカルの画像に関連したシナリオについては、[GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) 上のサンプル コードを参照してください。


### <a name="output"></a>出力

前の例のモデルを使用すると、次の出力が得られます。

```console
Form type: form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Field 'Merchant' has label 'Merchant' with value 'Invoice For:' and a confidence score of 0.116
Field 'CompanyAddress' has label 'CompanyAddress' with value '1 Redmond way Suite 6000 Redmond, WA' and a confidence score of 0.258
Field 'Website' has label 'Website' with value '99243' and a confidence score of 0.114
Field 'VendorName' has label 'VendorName' with value 'Charges' and a confidence score of 0.145
Field 'CompanyPhoneNumber' has label 'CompanyPhoneNumber' with value '$56,651.49' and a confidence score of 0.249
Field 'CompanyName' has label 'CompanyName' with value 'PT' and a confidence score of 0.245
Field 'DatedAs' has label 'DatedAs' with value 'None' and a confidence score of None
Field 'Email' has label 'Email' with value 'None' and a confidence score of None
Field 'PhoneNumber' has label 'PhoneNumber' with value 'None' and a confidence score of None
Field 'PurchaseOrderNumber' has label 'PurchaseOrderNumber' with value 'None' and a confidence score of None
Field 'Quantity' has label 'Quantity' with value 'None' and a confidence score of None
Field 'Signature' has label 'Signature' with value 'None' and a confidence score of None
Field 'Subtotal' has label 'Subtotal' with value 'None' and a confidence score of None
Field 'Tax' has label 'Tax' with value 'None' and a confidence score of None
Field 'Total' has label 'Total' with value 'None' and a confidence score of None
```

## <a name="analyze-receipts"></a>領収書を分析する

このセクションでは、事前トレーニング済みの領収書モデルを使用して、米国のレシートから共通フィールドを分析、抽出する方法を示します。 レシートの分析の詳細については、[レシートの概念ガイド](../../concept-receipts.md)を参照してください。 URL からレシートを分析するには、`begin_recognize_receipts_from_url` メソッドを使用します。 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_receipts)]

> [!TIP]
> ローカルにあるレシートの画像を分析することもできます。 [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) のメソッドを参照してください (`begin_recognize_receipts` など)。 また、ローカルの画像に関連したシナリオについては、[GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) 上のサンプル コードを参照してください。

### <a name="output"></a>出力

```console
ReceiptType: Itemized has confidence 0.659
MerchantName: Contoso Contoso has confidence 0.516
MerchantAddress: 123 Main Street Redmond, WA 98052 has confidence 0.986
MerchantPhoneNumber: None has confidence 0.99
TransactionDate: 2019-06-10 has confidence 0.985
TransactionTime: 13:59:00 has confidence 0.968
Receipt Items:
...Item #1
......Name: 8GB RAM (Black) has confidence 0.916
......TotalPrice: 999.0 has confidence 0.559
...Item #2
......Quantity: None has confidence 0.858
......Name: SurfacePen has confidence 0.858
......TotalPrice: 99.99 has confidence 0.386
Subtotal: 1098.99 has confidence 0.964
Tax: 104.4 has confidence 0.713
Total: 1203.39 has confidence 0.774
```

## <a name="analyze-business-cards"></a>名刺を分析する

#### <a name="v21-preview"></a>[v2.1 プレビュー](#tab/preview)

このセクションでは、事前トレーニング済みのモデルを使用して、英語の名刺から共通フィールドを分析、抽出する方法を示します。 名刺の分析の詳細については、[名刺の概念ガイド](../../concept-business-cards.md)を参照してください。 URL から名刺を分析するには、`begin_recognize_business_cards_from_url` メソッドを使用します。 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_bc)]

> [!TIP]
> ローカルにある名刺の画像を分析することもできます。 [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) のメソッドを参照してください (`begin_recognize_business_cards` など)。 また、ローカルの画像に関連したシナリオについては、[GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) 上のサンプル コードを参照してください。

#### <a name="v20"></a>[v2.0](#tab/ga)

> [!IMPORTANT]
> この機能は、選択した API バージョンでは使用できません。

---

## <a name="manage-your-custom-models"></a>カスタム モデルを管理する

このセクションでは、アカウントに格納されているカスタム モデルを管理する方法について説明します。 

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>FormRecognizer リソース アカウント内のモデルの数を確認する

次のコード ブロックは、Form Recognizer アカウントに保存したモデルの数を確認し、アカウントの制限と比較します。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_count)]


### <a name="output"></a>出力

```console
Our account has 5 custom models, and we can have at most 5000 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>リソース アカウントに現在格納されているモデルを一覧表示する

次のコード ブロックは、アカウント内の現在のモデルを一覧表示し、その詳細をコンソールに出力します。 また、最初のモデルへの参照も保存します。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_list)]


### <a name="output"></a>出力

これは、テスト アカウントのサンプル出力です。

```console
We have models with the following ids:
453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
628739de-779c-473d-8214-d35c72d3d4f7
ae636292-0b14-4e26-81a7-a0bfcbaf7c91
b4b5df77-8538-4ffb-a996-f67158ecd305
c6309148-6b64-4fef-aea0-d39521452699
```

### <a name="get-a-specific-model-using-the-models-id"></a>モデルの ID を使用して特定のモデルを取得する

次のコード ブロックは、前のセクションで保存したモデル ID を使用して、モデルに関する詳細を取得します。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_getmodel)]


### <a name="output"></a>出力

これは、前の例で作成したカスタム モデルのサンプル出力です。

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00
```

### <a name="delete-a-model-from-the-resource-account"></a>リソース アカウントからモデルを削除する

ID を参照して、アカウントからモデルを削除することもできます。 このコードは、前のセクションで使用したモデルを削除します。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_delete)]


## <a name="run-the-application"></a>アプリケーションの実行

クイック スタート ファイルで `python` コマンドを使用して、アプリケーションを実行します。

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="general"></a>全般

Form Recognizer クライアント ライブラリにより、[Azure Core](https://aka.ms/azsdk-python-azure-core) で定義されている例外が発生します。

### <a name="logging"></a>ログ記録

このライブラリでは、ログ記録に[標準のログ ライブラリ](https://docs.python.org/3/library/logging.html)を使用します。 HTTP セッション (URL、ヘッダーなど) に関する基本的な情報は、情報レベルでログに記録されます。

要求/応答本文と編集されていないヘッダーを含む詳細なデバッグ レベルのログ記録は、次のように `logging_enable` キーワード引数を使用してクライアントで有効にすることができます。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_logging)]


同様に、`logging_enable` は、詳細なログ記録がクライアントで有効になっていない場合でも、1 回の操作のために有効にすることができます。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_example)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Form Recognizer Python クライアント ライブラリを使用してモデルをトレーニングし、さまざまな方法でフォームを分析しました。 次に、より適切なトレーニング データ セットを作成し、より正確なモデルを生成するためのヒントについて学習します。

> [!div class="nextstepaction"]
> [トレーニング データ セットの作成](../../build-training-data-set.md)

* [Form Recognizer とは](../../overview.md)
* このガイドのサンプル コードについては、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py) を参照してください。
