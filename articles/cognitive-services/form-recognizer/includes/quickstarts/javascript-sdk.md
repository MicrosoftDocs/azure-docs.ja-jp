---
title: クイック スタート:JavaScript 用 Form Recognizer クライアント ライブラリ
description: JavaScript 用の Form Recognizer クライアント ライブラリを使用して、カスタム ドキュメントからキーと値のペアとテーブル データを抽出するフォーム処理アプリを作成します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: a4d29dfb2a57dde2bb21244b2e5335f1a8ea1fcf
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948047"
---
> [!IMPORTANT]
> * この記事のコードでは、単純化するために、同期メソッドと、セキュリティで保護されていない資格情報の格納を使用しています。 以下のリファレンス ドキュメントを参照してください。 

[リファレンスのドキュメント](../../index.yml) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/) | [パッケージ (npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer) | [サンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* 最新バージョンの [Node.js](https://nodejs.org/)
* トレーニング データのセットを含む Azure Storage Blob。 トレーニング データ セットをまとめるためのヒントとオプションについては、「[カスタム モデルのトレーニング データ セットを作成する](../../build-training-data-set.md)」を参照してください。 このクイックスタートでは、[サンプル データ セット](https://go.microsoft.com/fwlink/?linkid=2090451)の **Train** フォルダーにあるファイルを使用できます (*sample_data.zip* をダウンロードして展開します)。
* Azure サブスクリプションを用意できたら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Form Recognizer リソースを作成"  target="_blank">Form Recognizer リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、自分のキーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * 自分のアプリケーションを Form Recognizer API に接続するには、作成したリソースのキーとエンドポイントが必要になります。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="setting-up"></a>設定

### <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。 

```console
mkdir myapp && cd myapp
```

`npm init` コマンドを実行し、`package.json` ファイルを使用して node アプリケーションを作成します。 

```console
npm init
```

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

`ai-form-recognizer` NPM パッケージをインストールします。

```console
npm install @azure/ai-form-recognizer
```

アプリの `package.json` ファイルが依存関係によって更新されます。

`index.js` という名前のファイルを作成して開き、次のライブラリをインポートします。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_imports)]


> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js) にあり、このクイックスタートのコード例が含まれています。

自分のリソースの Azure エンドポイントおよびキー用の変数を作成します。 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Azure Portal にアクセスします。 「**前提条件**」セクションで作成した Form Recognizer リソースが正常にデプロイされた場合、 **[次の手順]** の下にある **[リソースに移動]** ボタンをクリックします。 キーとエンドポイントは、リソースの **[key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。 
>
> 終わったらコードからキーを削除し、公開しないよう注意してください。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 詳細については、Cognitive Services の[セキュリティ](../../../cognitive-services-security.md)に関するページを参照してください。

## <a name="object-model"></a>オブジェクト モデル 

Form Recognizer で作成できるクライアントは 2 種類あります。 1 つは、`FormRecognizerClient` です。認識されたフォームのフィールドやコンテンツをサービスに照会するときに使用します。 もう 1 つは `FormTrainingClient` です。認識精度を高めるために使用できるカスタム モデルを作成したり管理したりするときに使用します。 

### <a name="formrecognizerclient"></a>FormRecognizerClient
`FormRecognizerClient` には、以下を目的とした操作が用意されています。

 * 対象のカスタム フォームを認識するようトレーニングされたカスタム モデルを使用して、フォームのフィールドやコンテンツを認識する。 これらの値は、`RecognizedForm` オブジェクトのコレクションとして返されます。
 * モデルをトレーニングせずにフォームのコンテンツ (表、行、単語など) を認識する。 フォームのコンテンツは、`FormPage` オブジェクトのコレクションとして返されます。
 * Form Recognizer サービスの事前トレーニング済みの領収書モデルを使用して、領収書から一般的なフィールドを認識する。 これらのフィールドとメタデータは、`RecognizedReceipt` のコレクションとして返されます。

### <a name="formtrainingclient"></a>FormTrainingClient
`FormTrainingClient` には、以下を目的とした操作が用意されています。

* カスタム モデルをトレーニングして、対象のカスタム フォームにあるすべてのフィールドと値を認識する。 モデルによって認識されるフォームの種類とそれぞれのフォームの種類で抽出されるフィールドを示す `CustomFormModel` が返されます。 トレーニング データセットの作成について詳しくは、[ラベル付けなしのモデル トレーニングに関するサービス ドキュメント](#train-a-model-without-labels)を参照してください。
* 対象のカスタム フォームにラベル付けすることによって指定した特定のフィールドと値を認識するように、カスタム モデルをトレーニングする。 モデルによって抽出されるフィールドと各フィールドの推定精度を示す `CustomFormModel` が返されます。 トレーニング データセットへのラベルの適用について詳しくは、[ラベル付けを使用したモデル トレーニングに関するサービス ドキュメント](#train-a-model-with-labels)を参照してください。
* アカウントに作成されたモデルを管理する。
* Form Recognizer リソース間でカスタム モデルをコピーする。

> [!NOTE]
> モデルのトレーニングは、[Form Recognizer のラベル付けツール](../../quickstarts/label-tool.md)など、グラフィカル ユーザー インターフェイスを使用して行うこともできます。


## <a name="code-examples"></a>コード例

これらのコード スニペットでは、JavaScript 用 Form Recognizer クライアント ライブラリを使用して次のタスクを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [レイアウトを分析する](#analyze-layout)
* [領収書を分析する](#analyze-receipts)
* [カスタム モデルをトレーニングする](#train-a-custom-model)
* [カスタム モデルを使用してフォームを分析する](#analyze-forms-with-a-custom-model)
* [カスタム モデルを管理する](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>クライアントを認証する



定義したサブスクリプション変数を使用してクライアント オブジェクトを認証します。 新しいクライアント オブジェクトを作成せずに、必要に応じて API キーを更新できるように、`AzureKeyCredential` オブジェクトを使用します。 また、トレーニング クライアント オブジェクトも作成します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_auth)]


## <a name="get-assets-for-testing"></a>テスト用のアセットを取得する

また、トレーニング データとテスト データの URL への参照を追加する必要もあります。
* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]
   
   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL の取得":::
* 以下のサンプルに含まれるフォームや領収書のサンプル画像を使用します ([GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/test-assets) から入手することもできます)。または、上記の手順を使用して、Blob Storage 内の各ドキュメントの SAS URL を取得することもできます。 


## <a name="analyze-layout"></a>レイアウトを分析する

Form Recognizer を使用すると、ドキュメント内の表、行、および単語を認識できます。モデルをトレーニングする必要はありません。 指定された URI にあるファイルの内容を認識するには、`beginRecognizeContentFromUrl` メソッドを使用します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_getcontent)]


> [!TIP]
> また、ローカルのファイルから内容を取得することもできます。 [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) のメソッドを参照してください (**beginRecognizeContent** など)。 また、ローカルの画像に関連したシナリオについては、[GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) 上のサンプル コードを参照してください。

### <a name="output"></a>出力

```console
Page 1: width 8.5 and height 11 with unit inch
cell [0,0] has text Invoice Number
cell [0,1] has text Invoice Date
cell [0,2] has text Invoice Due Date
cell [0,3] has text Charges
cell [0,5] has text VAT ID
cell [1,0] has text 34278587
cell [1,1] has text 6/18/2017
cell [1,2] has text 6/24/2017
cell [1,3] has text $56,651.49
cell [1,5] has text PT
```

## <a name="analyze-receipts"></a>領収書を分析する

このセクションでは、事前トレーニング済みの領収書モデルを使用して、米国の領収書から共通フィールドを認識して抽出する方法を示します。

URI からの領収書を認識するには、`beginRecognizeReceiptsFromUrl` メソッドを使用します。 次のコードは、指定された URI で領収書を処理し、主要なフィールドと値をコンソールに出力します。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_receipts)]

> [!TIP]
> ローカルにある領収書の画像を認識することもできます。 [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) のメソッドを参照してください (**beginRecognizeReceipts** など)。 また、ローカルの画像に関連したシナリオについては、[GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) 上のサンプル コードを参照してください。

### <a name="output"></a>出力

```console
status: notStarted
status: running
status: succeeded
First receipt:
  Receipt Type: 'Itemized', with confidence of 0.659
  Merchant Name: 'Contoso Contoso', with confidence of 0.516
  Transaction Date: 'Sun Jun 09 2019 17:00:00 GMT-0700 (Pacific Daylight Time)', with confidence of 0.985
    Item Name: '8GB RAM (Black)', with confidence of 0.916
    Item Name: 'SurfacePen', with confidence of 0.858
  Total: '1203.39', with confidence of 0.774
```

## <a name="train-a-custom-model"></a>カスタム モデルをトレーニングする

このセクションでは、独自のデータを使用してモデルをトレーニングする方法を示します。 トレーニング済みのモデルは、元のフォーム ドキュメント内のキー/値の関係を含む構造化データを出力できます。 モデルをトレーニングした後、モデルをテストおよび再トレーニングでき、最終的にはモデルを使用して、ニーズに従ってより多くのフォームから正確にデータを抽出できます。

> [!NOTE]
> また、[Form Recognizer のサンプル ラベル付けツール](../../quickstarts/label-tool.md)などのグラフィカル ユーザー インターフェイスを使用してモデルをトレーニングすることもできます。

### <a name="train-a-model-without-labels"></a>ラベルなしでモデルをトレーニングする

カスタム モデルをトレーニングして、トレーニング ドキュメントに手動でラベルを付けることなく、カスタム フォームにあるすべてのフィールドと値を認識できるようにします。

次の関数は、指定された一連のドキュメントでモデルをトレーニングし、モデルの状態をコンソールに出力します。 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_train)]


### <a name="output"></a>出力

これは、[JavaScript SDK](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer) から入手できるトレーニング データを使用してトレーニングされたモデルの出力です。このサンプル出力は、読みやすくするために一部が省略されています。

```console
training status: creating
training status: ready
Model ID: 9d893595-1690-4cf2-a4b1-fbac0fb11909
Status: ready
Training started on: Thu Aug 20 2020 20:27:26 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:27:37 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'field-0'
The model found field 'field-1'
The model found field 'field-2'
The model found field 'field-3'
The model found field 'field-4'
The model found field 'field-5'
The model found field 'field-6'
The model found field 'field-7'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: 
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: 
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: 
...
```

### <a name="train-a-model-with-labels"></a>ラベルを使用してモデルをトレーニングする

トレーニング ドキュメントに手動でラベルを付けて、カスタム モデルをトレーニングすることもできます。 ラベルを使用してトレーニングを行うと、一部のシナリオでパフォーマンスの向上につながります。 ラベルを使用してトレーニングするには、トレーニング ドキュメントと共に、自分の Blob Storage コンテナーに特別なラベル情報ファイル (`\<filename\>.pdf.labels.json`) を用意する必要があります。 [Form Recognizer のサンプル ラベル付けツール](../../quickstarts/label-tool.md)では、これらのラベル ファイルの作成を支援する UI が提供されています。 それらの用意ができたら、`uselabels` パラメーターを `true` に設定して `beginTraining` メソッドを呼び出すことができます。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_trainlabels)]


### <a name="output"></a>出力 

これは、[JavaScript SDK](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) から入手できるトレーニング データを使用してトレーニングされたモデルの出力です。このサンプル出力は、読みやすくするために一部が省略されています。

```console
training status: creating
training status: ready
Model ID: 789b1b37-4cc3-4e36-8665-9dde68618072
Status: ready
Training started on: Thu Aug 20 2020 20:30:37 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:30:43 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'CompanyAddress'
The model found field 'CompanyName'
The model found field 'CompanyPhoneNumber'
The model found field 'DatedAs'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
...
```

## <a name="analyze-forms-with-a-custom-model"></a>カスタム モデルを使用してフォームを分析する

このセクションでは、独自のフォームでトレーニングしたモデルを使用して、カスタムのフォームの種類からキー/値の情報やその他のコンテンツを抽出する方法について説明します。

> [!IMPORTANT]
> このシナリオを実装するには、モデルのトレーニングが完了している必要があります。それにより、次のメソッドにその ID を渡すことができます。 [モデルのトレーニング](#train-a-model-without-labels)に関するセクションを参照してください。

`beginRecognizeCustomFormsFromUrl` メソッドを使用します。 返される値は `RecognizedForm` オブジェクトのコレクションで、送信されたドキュメント内のページごとに 1 つあります。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_analyze)]

> [!TIP]
> ローカルのファイルを分析することもできます。 [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) のメソッドを参照してください (**beginRecognizeCustomForms** など)。 また、ローカルの画像に関連したシナリオについては、[GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) 上のサンプル コードを参照してください。


### <a name="output"></a>出力

```console
status: notStarted
status: succeeded
Forms:
custom:form, page range: [object Object]
Pages:
Page number: 1
Tables
cell (0,0) Invoice Number
cell (0,1) Invoice Date
cell (0,2) Invoice Due Date
cell (0,3) Charges
cell (0,5) VAT ID
cell (1,0) 34278587
cell (1,1) 6/18/2017
cell (1,2) 6/24/2017
cell (1,3) $56,651.49
cell (1,5) PT
Fields:
Field Merchant has value 'Invoice For:' with a confidence score of 0.116
Field CompanyPhoneNumber has value '$56,651.49' with a confidence score of 0.249
Field VendorName has value 'Charges' with a confidence score of 0.145
Field CompanyAddress has value '1 Redmond way Suite 6000 Redmond, WA' with a confidence score of 0.258
Field CompanyName has value 'PT' with a confidence score of 0.245
Field Website has value '99243' with a confidence score of 0.114
Field DatedAs has value 'undefined' with a confidence score of undefined
Field Email has value 'undefined' with a confidence score of undefined
Field PhoneNumber has value 'undefined' with a confidence score of undefined
Field PurchaseOrderNumber has value 'undefined' with a confidence score of undefined
Field Quantity has value 'undefined' with a confidence score of undefined
Field Signature has value 'undefined' with a confidence score of undefined
Field Subtotal has value 'undefined' with a confidence score of undefined
Field Tax has value 'undefined' with a confidence score of undefined
Field Total has value 'undefined' with a confidence score of undefined
```

## <a name="manage-your-custom-models"></a>カスタム モデルを管理する

このセクションでは、アカウントに格納されているカスタム モデルを管理する方法について説明します。 次のコードは、例として、1 つの関数ですべてのモデル管理タスクを実行します。 

### <a name="get-number-of-models"></a>モデルの数を取得する

次のコード ブロックは、現在アカウントに存在するモデルの数を取得するものです。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_count)]


### <a name="get-list-of-models-in-account"></a>アカウントにあるモデルの一覧を取得する

次のコード ブロックを使用すると、モデルが作成された時期とモデルの現在の状態に関する情報を含め、自分のアカウントにある利用可能なモデルの全一覧を出力できます。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_list)]


### <a name="output"></a>出力

```console
model 0:
{
  modelId: '453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e',
  status: 'invalid',
  trainingStartedOn: 2020-08-20T22:28:52.000Z,
  trainingCompletedOn: 2020-08-20T22:28:53.000Z
}
model 1:
{
  modelId: '628739de-779c-473d-8214-d35c72d3d4f7',
  status: 'ready',
  trainingStartedOn: 2020-08-20T23:16:51.000Z,
  trainingCompletedOn: 2020-08-20T23:16:59.000Z
}
model 2:
{
  modelId: '789b1b37-4cc3-4e36-8665-9dde68618072',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:30:37.000Z,
  trainingCompletedOn: 2020-08-21T03:30:43.000Z
}
model 3:
{
  modelId: '9d893595-1690-4cf2-a4b1-fbac0fb11909',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:27:26.000Z,
  trainingCompletedOn: 2020-08-21T03:27:37.000Z
}
```

### <a name="get-list-of-model-ids-by-page"></a>モデル ID の一覧をページ分割して取得する

このコード ブロックを使用すると、モデルとモデル ID の一覧をページ分割して出力できます。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_listpages)]


### <a name="output"></a>出力

```console
model 1: 453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
model 2: 628739de-779c-473d-8214-d35c72d3d4f7
model 3: 789b1b37-4cc3-4e36-8665-9dde68618072
```

### <a name="get-model-by-id"></a>ID でモデルを取得する

次の関数は、モデル ID を受け取り、一致するモデル オブジェクトを取得します。 この関数は、既定では呼び出されません。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_getmodel)]


### <a name="delete-a-model-from-the-resource-account"></a>リソース アカウントからモデルを削除する

ID を参照して、アカウントからモデルを削除することもできます。 この関数は、指定された ID のモデルを削除します。 この関数は、既定では呼び出されません。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_delete)]


### <a name="output"></a>出力

```console
Model with id 789b1b37-4cc3-4e36-8665-9dde68618072 has been deleted
```

## <a name="run-the-application"></a>アプリケーションの実行

クイック スタート ファイルで `node` コマンドを使用して、アプリケーションを実行します。

```console
node index.js
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="enable-logs"></a>ログの有効化

このライブラリの使用時にデバッグ ログを表示するには、次の環境変数を設定します。

```
export DEBUG=azure*
```

ログを有効にする方法の詳細については、[@azure/logger パッケージに関するドキュメント](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger)を参照してください。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Form Recognizer JavaScript クライアント ライブラリを使用してモデルをトレーニングし、さまざまな方法でフォームを分析しました。 次に、より適切なトレーニング データ セットを作成し、より正確なモデルを生成するためのヒントについて学習します。

> [!div class="nextstepaction"]
> [トレーニング データ セットの作成](../../build-training-data-set.md)

## <a name="see-also"></a>関連項目

* [Form Recognizer とは](../../overview.md)
* このガイドのサンプル コードについては、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js) を参照してください。
