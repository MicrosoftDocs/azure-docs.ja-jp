---
title: クイック スタート:JavaScript 用 Form Recognizer クライアント ライブラリ
description: このクイックスタートでは、JavaScript 用の Form Recognizer クライアント ライブラリの使用を開始します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/08/2020
ms.author: pafarley
ms.custom: devx-track-javascript
ms.openlocfilehash: 17b0ede8d917ea24bf6fa6fbcda49b1860ef83b9
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2020
ms.locfileid: "88246439"
---
[リファレンスのドキュメント](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/) | [パッケージ (npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer) | [サンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* トレーニング データのセットを含む Azure Storage Blob。 トレーニング データ セットをまとめるためのヒントとオプションについては、[カスタム モデル用のトレーニング データ セットの作成](../../build-training-data-set.md)に関する記事を参照してください。 このクイックスタートでは、[サンプル データ セット](https://go.microsoft.com/fwlink/?linkid=2090451)の **Train** フォルダーにあるファイルを使用できます。
* 最新バージョンの [Node.js](https://nodejs.org/)

## <a name="setting-up"></a>設定

### <a name="create-a-form-recognizer-azure-resource"></a>Form Recognizer Azure リソースを作成する

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>環境変数を作成する

[!INCLUDE [environment-variables](../environment-variables.md)]

### <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。 

```console
mkdir myapp && cd myapp
```

`npm init` コマンドを実行し、`package.json` ファイルを使用して node アプリケーションを作成します。 

```console
npm init
```

`index.js` という名前のファイルを作成して開き、次のライブラリをインポートします。

```javascript
const { FormRecognizerClient, AzureKeyCredential } = require("@azure/ai-form-recognizer");
const fs = require("fs");
```

また、環境変数ファイルも読み込みます。

```javascript
// Load the .env file if it exists
require("dotenv").config();
```

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

`ai-form-recognizer` および `dotenv` NPM パッケージをインストールします。

```console
npm install @azure/ai-form-recognizer dotenv
```

アプリの `package.json` ファイルが依存関係によって更新されます。


<!-- 
    Object model
-->

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、JavaScript 用 Form Recognizer クライアント ライブラリを使用して次のタスクを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [フォーム コンテンツを認識する](#recognize-form-content)
* [領収書を認識する](#recognize-receipts)
* [カスタム モデルをトレーニングする](#train-a-custom-model)
* [カスタム モデルを使用してフォームを分析する](#analyze-forms-with-a-custom-model)
* [カスタム モデルを管理する](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>クライアントを認証する

`main` 関数で、リソースの Azure エンドポイントおよびキー用の変数を作成します。 アプリケーションの起動後に環境変数を作成した場合、その変数にアクセスするには、エディター、IDE、またはシェルを閉じて、もう一度開く必要があります。

```javascript
// You will need to set these environment variables or edit the following values
const endpoint = process.env["FORM_RECOGNIZER_ENDPOINT"] || "<cognitive services endpoint>";
const apiKey = process.env["FORM_RECOGNIZER_KEY"] || "<api key>";
```

次に、定義したサブスクリプション変数を使用してクライアント オブジェクトを認証します。 新しいクライアント オブジェクトを作成せずに、必要に応じて API キーを更新できるように、**AzureKeyCredential** オブジェクトを使用します。 また、トレーニング クライアント オブジェクトも作成します。

```javascript
const trainingClient = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));

const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));
```

### <a name="call-client-specific-functions"></a>クライアント固有の関数を呼び出す

`main` の次のコード ブロックでは、クライアント オブジェクトを使用して、Form Recognizer SDK の主なタスクごとに関数を呼び出します。 これらの関数は、後で定義します。

また、トレーニング データとテスト データの URL への参照を追加する必要もあります。
* カスタム モデルのトレーニング データの SAS URL を取得するには、Microsoft Azure Storage Explorer を開き、ご利用のコンテナーを右クリックし、 **[Shared Access Signature の取得]** を選択します。 アクセス許可の **[読み取り]** と **[表示]** がオンになっていることを確認し、 **[作成]** をクリックします。 次に、その値を **URL** セクションにコピーします。 それは次の書式になります`https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`。
* テストするフォームの URL を取得するには、上記の手順を使用して、BLOB ストレージ内の個々のドキュメントの SAS URL を取得できます。 または、別の場所にあるドキュメントの URL を取得します。
* 上記のメソッドを使用して、領収書の画像の URL を取得するか、指定されたサンプル画像の URL を使用します。

> [!NOTE]
> このガイドのコード スニペットでは、URL でアクセスされるリモート フォームが使用されます。 ローカル フォーム ドキュメントを代わりに処理する場合は、[リファレンス ドキュメント](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/)の関連するメソッドを参照してください。


```javascript
const trainingDataUrl = "<url/path to the labeled training documents>";
const formUrl = "<SAS-URL-of-a-form-in-blob-storage>";
const receiptUrl = "https://docs.microsoft.com/azure/cognitive-services/form-recognizer/media/contoso-allinone.jpg";


// Call Form Recognizer scenarios:
await GetContent(recognizerClient, formUrl);
await AnalyzeReceipt(recognizerClient, receiptUrl);
modelId = await TrainModel(trainingClient, trainingDataUrl);
await AnalyzePdfForm(recognizerClient, modelId, formUrl);
await ManageModels(trainingClient, trainingDataUrl);
```

## <a name="recognize-form-content"></a>フォーム コンテンツを認識する

Form Recognizer を使用すると、ドキュメント内の表、行、および単語を認識できます。モデルをトレーニングする必要はありません。 指定された URI にあるファイルの内容を認識するには、**beginRecognizeContentFromUrl** メソッドを使用します。

```javascript
async function GetContent( recognizerClient, invoiceUri)
{
    const poller = await client.beginRecognizeContentFromUrl(invoiceUri);
    await poller.pollUntilDone();
    const response = poller.getResult();
```

返される値は **FormPage** オブジェクトのコレクションで、送信されたドキュメント内のページごとに 1 つですす。 次のコードでは、これらのオブジェクトを反復処理し、抽出されたキー/値のペアとテーブル データを出力します。

```javascript
    for (const page of response.pages) {
    console.log(
        `Page ${page.pageNumber}: width ${page.width} and height ${page.height} with unit ${page.unit}`
    );
        for (const table of page.tables) {
            for (const row of table.rows) {
                for (const cell of row.cells) {
                    console.log(`cell [${cell.rowIndex},${cell.columnIndex}] has text ${cell.text}`);
                }
            }
        }
    }
}
```

## <a name="recognize-receipts"></a>領収書を認識する

このセクションでは、事前トレーニング済みの領収書モデルを使用して、米国の領収書から共通フィールドを認識して抽出する方法を示します。

URI からの領収書を認識するには、**beginRecognizeReceiptsFromUrl** メソッドを使用します。 返される値は **RecognizedReceipt** オブジェクトのコレクションで、送信されたドキュメント内のページごとに 1 つです。 次のコードは、指定された URI で領収書を処理し、主要なフィールドと値をコンソールに出力します。

```javascript
async function AnalyzeReceipt( client, receiptUri)
{
    const poller = await client.beginRecognizeReceiptsFromUrl(url, {
        includeTextDetails: true,
        onProgress: (state) => {
            console.log(`analyzing status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();


    const receipt = receipts[0];
    console.log("First receipt:");
    // For supported fields recognized by the service, please refer to https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeReceiptResult.
    const receiptTypeField = receipt.recognizedForm.fields["ReceiptType"];
    if (receiptTypeField.valueType === "string") {
        console.log(`  Receipt Type: '${receiptTypeField.value || "<missing>"}', with confidence of ${receiptTypeField.confidence}`);
    }
    const merchantNameField = receipt.recognizedForm.fields["MerchantName"];
    if (merchantNameField.valueType === "string") {
        console.log(`  Merchant Name: '${merchantNameField.value || "<missing>"}', with confidence of ${merchantNameField.confidence}`);
    }
    const transactionDate = receipt.recognizedForm.fields["TransactionDate"];
    if (transactionDate.valueType === "date") {
        console.log(`  Transaction Date: '${transactionDate.value || "<missing>"}', with confidence of ${transactionDate.confidence}`);
    }
```

次のコード ブロックは、領収書で検出された個々の項目を反復処理し、その詳細をコンソールに出力します。

```javascript
    const itemsField = receipt.recognizedForm.fields["Items"];
    if (itemsField.valueType === "array") {
        for (const itemField of itemsField.value || []) {
            if (itemField.valueType === "object") {
                const itemNameField = itemField.value["Name"];
                if (itemNameField.valueType === "string") {
                    console.log(`    Item Name: '${itemNameField.value || "<missing>"}', with confidence of ${itemNameField.confidence}`);
                }
            }
        }
    }
}
```


## <a name="train-a-custom-model"></a>カスタム モデルをトレーニングする

このセクションでは、独自のデータを使用してモデルをトレーニングする方法を示します。 トレーニング済みのモデルは、元のフォーム ドキュメント内のキー/値の関係を含む構造化データを出力できます。 モデルをトレーニングした後、モデルをテストおよび再トレーニングでき、最終的にはモデルを使用して、ニーズに従ってより多くのフォームから正確にデータを抽出できます。

> [!NOTE]
> また、[Form Recognizer のサンプル ラベル付けツール](../../quickstarts/label-tool.md)などのグラフィカル ユーザー インターフェイスを使用してモデルをトレーニングすることもできます。

### <a name="train-a-model-without-labels"></a>ラベルなしでモデルをトレーニングする

カスタム モデルをトレーニングして、トレーニング ドキュメントに手動でラベルを付けることなく、カスタム フォームにあるすべてのフィールドと値を認識できるようにします。

次の関数は、指定された一連のドキュメントでモデルをトレーニングし、モデルの状態をコンソールに出力します。 

```javascript
async function TrainModel(trainingClient, trainingDataUrl)
{
    const poller = await trainingClient.beginTraining(trainingDataUrl, false, {
        onProgress: (state) => {
            console.log(`training status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();
    
    if (!response) {
        throw new Error("Expecting valid response!");
    }
    
    console.log(`Model ID: ${response.modelId}`);
    console.log(`Status: ${response.status}`);
    console.log(`Created on: ${response.requestedOn}`);
    console.log(`Last modified: ${response.completedOn}`);
```

返された **CustomFormModel** オブジェクトには、モデルが認識できるフォームの種類と、それぞれのフォームの種類から抽出できるフィールドに関する情報が含まれています。 次のコード ブロックは、この情報をコンソールに出力します。

```javascript
    if (response.submodels) {
        for (const submodel of response.submodels) {
            // since the training data is unlabeled, we are unable to return the accuracy of this model
            console.log("We have recognized the following fields");
            for (const key in submodel.fields) {
                const field = submodel.fields[key];
                console.log(`The model found field '${field.name}'`);
            }
        }
    }
```

最後に、このメソッドはモデルの一意の ID を返します。

```csharp
    return response.modelId;
}
```

### <a name="train-a-model-with-labels"></a>ラベルを使用してモデルをトレーニングする

トレーニング ドキュメントに手動でラベルを付けて、カスタム モデルをトレーニングすることもできます。 ラベルを使用してトレーニングを行うと、一部のシナリオでパフォーマンスの向上につながります。 ラベルを使用してトレーニングするには、トレーニング ドキュメントと共に、BLOB ストレージ コンテナーに特別なラベル情報ファイル ( *\<filename\>.pdf.labels.json*) を用意する必要があります。 [Form Recognizer のサンプル ラベル付けツール](../../quickstarts/label-tool.md)では、これらのラベル ファイルの作成を支援する UI が提供されています。 それらの用意ができたら、*uselabels* パラメーターを `true` に設定して **beginTraining** メソッドを呼び出すことができます。

```javascript
async function TrainModelWithLabelsAsync(
    const poller = await trainingClient.beginTraining(trainingDataUrl, true, {
        onProgress: (state) => {
            console.log(`training status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const model = poller.getResult();
    
    return model.modelId;
}
```

## <a name="analyze-forms-with-a-custom-model"></a>カスタム モデルを使用してフォームを分析する

このセクションでは、独自のフォームでトレーニングしたモデルを使用して、カスタムのフォームの種類からキー/値の情報やその他のコンテンツを抽出する方法について説明します。

> [!IMPORTANT]
> このシナリオを実装するには、モデルのトレーニングが完了している必要があります。それにより、次のメソッドにその ID を渡すことができます。 [モデルのトレーニング](#train-a-model-without-labels)に関するセクションを参照してください。

**beginRecognizeCustomFormsFromUrl** メソッドを使用します。 返される値は **RecognizedForm** オブジェクトのコレクションで、送信されたドキュメント内のページごとに 1 つあります。

```javascript
// Analyze PDF form document at an accessible URL
async function AnalyzePdfForm(client, modelId, formUrl)
{    
    const poller = await client.beginRecognizeCustomFormsFromUrl(modelId, formUrl, {
        onProgress: (state) => {
            console.log(`status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();
```

次のコードは、分析結果をコンソールに出力します。 認識された各フィールドと対応する値が、信頼度スコアと共に出力されます。

```javascript
    console.log("Fields:");
    for (const fieldName in form.fields) {
        // each field is of type FormField
        const field = form.fields[fieldName];
        console.log(
            `Field ${fieldName} has value '${field.value}' with a confidence score of ${field.confidence}`
        );
    }
}
```

## <a name="manage-your-custom-models"></a>カスタム モデルを管理する

このセクションでは、アカウントに格納されているカスタム モデルを管理する方法について説明します。 次のコードは、例として、1 つの関数ですべてのモデル管理タスクを実行します。 まず、次の関数シグネチャをコピーします。

```javascript
async function ManageModels(trainingClient, trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>FormRecognizer リソース アカウント内のモデルの数を確認する

次のコード ブロックは、Form Recognizer アカウントに保存したモデルの数を確認し、アカウントの制限と比較します。

```csharp
    // First, we see how many custom models we have, and what our limit is
    const accountProperties = await trainingClient.getAccountProperties();
    console.log(
        `Our account has ${accountProperties.customModelCount} custom models, and we can have at most ${accountProperties.customModelLimit} custom models`
    );
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>リソース アカウントに現在格納されているモデルを一覧表示する

次のコード ブロックは、アカウント内の現在のモデルを一覧表示し、その詳細をコンソールに出力します。 また、最初のモデルへの参照も保存します。

```javascript
    // Next, we get a paged async iterator of all of our custom models
    const result = trainingClient.listCustomModels();

    // We could print out information about first ten models
    // and save the first model id for later use
    let i = 0;
    let firstModel;
    for await (const model of result) {
        console.log(`model ${i++}:`);
        console.log(model);
        if (i === 1) {
            firstModel = model;
        }
        if (i > 10) {
            break;
        }
    }
```

### <a name="get-a-specific-model-using-the-models-id"></a>モデルの ID を使用して特定のモデルを取得する

次のコード ブロックは、前のセクションで保存したモデル ID を使用して、モデルに関する詳細を取得します。

```csharp
    // Now we'll get the first custom model in the paged list
    const model = await trainingClient.getModel(firstModel.modelId);
    console.log(`Model Id: ${model.modelId}`);
    console.log(`Status: ${model.status}`);
    console.log("Documents used in training: [");
    for (const doc of model.trainingDocuments || []) {
        console.log(`  ${doc.documentName}`);
    }
    console.log("]");
```

### <a name="delete-a-model-from-the-resource-account"></a>リソース アカウントからモデルを削除する

ID を参照して、アカウントからモデルを削除することもできます。 このコードは、前のセクションで使用したモデルを削除します。

```javascript
    await client.deleteModel(firstModel.modelId);
    try {
        const deleted = await trainingClient.deleteModel(firstModel.modelId);
        console.log(deleted);
    } catch (err) {
        // Expected
        console.log(`Model with id ${firstModel.modelId} has been deleted`);
    }
}
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

このクイックスタートでは、Form Recognizer Python クライアント ライブラリを使用してモデルをトレーニングし、さまざまな方法でフォームを分析しました。 次に、より適切なトレーニング データ セットを作成し、より正確なモデルを生成するためのヒントについて学習します。

> [!div class="nextstepaction"]
> [トレーニング データ セットの作成](../../build-training-data-set.md)

* [Form Recognizer とは](../../overview.md)
* このガイドのサンプル コード (およびその他の情報) については、[GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) を参照してください。