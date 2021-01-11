---
title: クイック スタート:Java 用 Form Recognizer クライアント ライブラリ
description: このクイックスタートでは、Java 用の Form Recognizer クライアント ライブラリの使用を開始します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/15/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 30c486ccb7bf3b7d537cd1ed3475a8dadc5b4f6d
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89326877"
---
> [!IMPORTANT]
> * Form Recognizer SDK は現在、From Recognizer サービスの v2.0 を対象としています。
> * この記事のコードでは、単純化するために、同期メソッドと、セキュリティで保護されていない資格情報の格納を使用しています。 以下のリファレンス ドキュメントを参照してください。 

[リファレンスのドキュメント](https://docs.microsoft.com/java/api/overview/azure/ai-formrecognizer-readme-pre?view=azure-java-preview) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src) | [パッケージ (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) | [サンプル](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* トレーニング データのセットを含む Azure Storage Blob。 トレーニング データ セットをまとめるためのヒントとオプションについては、「[カスタム モデルのトレーニング データ セットを作成する](../../build-training-data-set.md)」を参照してください。 このクイックスタートでは、[サンプル データ セット](https://go.microsoft.com/fwlink/?linkid=2090451)の **Train** フォルダーにあるファイルを使用できます。
* 最新バージョンの [Java Development Kit(JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle ビルド ツール](https://gradle.org/install/)、または別の依存関係マネージャー。
* Azure サブスクリプションを用意できたら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Form Recognizer リソースを作成"  target="_blank">Form Recognizer リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、自分のキーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * 自分のアプリケーションを Form Recognizer API に接続するには、作成したリソースのキーとエンドポイントが必要になります。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="setting-up"></a>設定

### <a name="create-a-new-gradle-project"></a>新しい Gradle プロジェクトを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。 

```console
mkdir myapp && cd myapp
```

作業ディレクトリから `gradle init` コマンドを実行します。 このコマンドによって、Gradle 用の重要なビルド ファイルが作成されます。たとえば、実行時にアプリケーションを作成して構成するために使用される *build.gradle.kts* などです。

```console
gradle init --type basic
```

**DSL** を選択するよう求められたら、**Kotlin** を選択します。

作業ディレクトリから、次のコマンドを実行します。

```console
mkdir -p src/main/java
```

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

このクイックスタートでは、Gradle 依存関係マネージャーを使用します。 クライアント ライブラリとその他の依存関係マネージャーの情報については、[Maven Central Repository](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) を参照してください。

プロジェクトの *build.gradle.kts* ファイルに、必要なプラグインと設定と共に、クライアント ライブラリを `implementation` ステートメントとして含めます。

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.0.0")
}
```

新しい **src/main/java** フォルダーに移動し、*Management.java* という名前のファイルを作成します。 それを任意のエディターまたは IDE で開き、以下の `import` ステートメントを追加します。

```java
import com.azure.ai.formrecognizer.*;
import com.azure.ai.formrecognizer.training.*;
import com.azure.ai.formrecognizer.models.*;
import com.azure.ai.formrecognizer.training.models.*;

import java.util.concurrent.atomic.AtomicReference;
import java.util.List;
import java.util.Map;
import java.time.LocalDate;

import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.util.Context;
import com.azure.core.util.polling.SyncPoller;
```

クラスと `main` メソッドを追加し、対象のリソースの Azure エンドポイントとキーのための変数を作成します。 アプリケーションの起動後に環境変数を作成した場合、その変数にアクセスするには、エディター、IDE、またはシェルを閉じて、もう一度開く必要があります。 これらのメソッドは後で定義します。


```java
public class FormRecognizer {
    public static void main(String[] args)
    {
        String key = "<replace-with-your-form-recognizer-key>";
        String endpoint = "<replace-with-your-form-recognizer-endpoint>";
    }
}
```

## <a name="object-model"></a>オブジェクト モデル 

Form Recognizer で作成できるクライアントは 2 種類あります。 1 つは、`FormRecognizerClient` です。認識されたフォームのフィールドやコンテンツをサービスに照会するときに使用します。 もう 1 つは `FormTrainingClient` です。認識精度を高めるために使用できるカスタム モデルを作成したり管理したりするときに使用します。 

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` には、以下を目的とした操作が用意されています。

- カスタム フォームを認識するようトレーニングされたカスタム モデルを使用して、フォームのフィールドやコンテンツを認識する。  これらの値は、`RecognizedForm` オブジェクトのコレクションとして返されます。 [カスタム フォームを分析する](#analyze-forms-with-a-custom-model)例を参照してください。
- モデルをトレーニングせずにフォームのコンテンツ (表、行、単語など) を認識する。  フォームのコンテンツは、`FormPage` オブジェクトのコレクションとして返されます。 [フォーム コンテンツを認識する](#recognize-form-content)例を参照してください。
- Form Recognizer サービスの事前トレーニング済みの領収書モデルを使用して、米国の領収書から一般的なフィールドを認識する。  これらのフィールドとメタデータは、`RecognizedForm` オブジェクトのコレクションとして返されます。 [領収書を認識する](#recognize-receipts)例を参照してください。

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` には、以下を目的とした操作が用意されています。

- カスタム モデルをトレーニングして、対象のカスタム フォームにあるすべてのフィールドと値を認識する。  モデルによって認識されるフォームの種類とそれぞれのフォームの種類で抽出されるフィールドを示す `CustomFormModel` が返されます。
- 対象のカスタム フォームにラベル付けすることによって指定した特定のフィールドと値を認識するように、カスタム モデルをトレーニングする。  モデルによって抽出されるフィールドと各フィールドの推定精度を示す `CustomFormModel` が返されます。
- アカウントに作成されたモデルを管理する。
- Form Recognizer リソース間でカスタム モデルをコピーする。

モデルのトレーニングは、[Form Recognizer のラベル付けツール](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/label-tool)など、グラフィカル ユーザー インターフェイスを使用して行うこともできることに注意してください。

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Java 用 Form Recognizer クライアント ライブラリを使用して次のタスクを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [フォーム コンテンツを認識する](#recognize-form-content)
* [領収書を認識する](#recognize-receipts)
* [カスタム モデルをトレーニングする](#train-a-custom-model)
* [カスタム モデルを使用してフォームを分析する](#analyze-forms-with-a-custom-model)
* [カスタム モデルを管理する](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>クライアントを認証する

`Main` メソッド内に次のコードを追加します。 ここでは、上で定義したサブスクリプション変数を使用して 2 つのクライアント オブジェクトを認証します。 必要に応じて、新しいクライアント オブジェクトを作成せずに API キーを更新できるように、**AzureKeyCredential** オブジェクトを使用します。

> [!IMPORTANT]
> Azure portal からキーとエンドポイントを取得します。 「**前提条件**」セクションで作成した Form Recognizer リソースが正常にデプロイされた場合、 **[次の手順]** の下にある **[リソースに移動]** ボタンをクリックします。 キーとエンドポイントは、リソースの **[key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。 
>
> 終わったらコードからキーを削除し、公開しないよう注意してください。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 たとえば、[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) が考えられます。

```java
    FormRecognizerClient recognizerClient = new FormRecognizerClientBuilder()
    .credential(new AzureKeyCredential(key))
    .endpoint(endpoint)
    .buildClient();
    
    FormTrainingClient trainingClient = new FormTrainingClientBuilder()
    .credential(new AzureKeyCredential(key))
    .endpoint(endpoint)
    .buildClient();
```

### <a name="call-client-specific-methods"></a>クライアント固有のメソッドを呼び出す

次のコード ブロックは、クライアント オブジェクトを使用して、Form Recognizer SDK の主要タスクごとにメソッドを呼び出します。 これらのメソッドは後で定義します。

また、トレーニング データとテスト データの URL への参照を追加する必要もあります。

* カスタム モデルのトレーニング データの SAS URL を取得するには、Microsoft Azure Storage Explorer を開き、ご利用のコンテナーを右クリックし、 **[Shared Access Signature の取得]** を選択します。 アクセス許可の **[読み取り]** と **[表示]** がオンになっていることを確認し、 **[作成]** をクリックします。 次に、その値を **URL** セクションにコピーします。 それは次の書式になります`https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`。
* テストするフォームの URL を取得するには、上記の手順を使用して、BLOB ストレージ内の個々のドキュメントの SAS URL を取得できます。 または、別の場所にあるドキュメントの URL を取得します。
* 上記のメソッドを使用して、領収書の画像の URL も取得します。

> [!NOTE]
> このガイドのコード スニペットでは、URL でアクセスされるリモート フォームが使用されます。 ローカル フォーム ドキュメントを代わりに処理する場合は、[リファレンス ドキュメント](https://docs.microsoft.com/java/api/overview/azure/ai-formrecognizer-readme-pre?view=azure-java-preview)の関連するメソッドを参照してください。

```java
    String trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    String formUrl = "<SAS-URL-of-a-form-in-blob-storage>";
    String receiptUrl = "https://docs.microsoft.com/azure/cognitive-services/form-recognizer/media"
    + "/contoso-allinone.jpg";

    // Call Form Recognizer scenarios:
    System.out.println("Get form content...");
    GetContent(recognizerClient, formUrl);

    System.out.println("Analyze receipt...");
    AnalyzeReceipt(recognizerClient, receiptUrl);

    System.out.println("Train Model with training data...");
    String modelId = TrainModel(trainingClient, trainingDataUrl);

    System.out.println("Analyze PDF form...");
    AnalyzePdfForm(recognizerClient, modelId, formUrl);

    System.out.println("Manage models...");
    ManageModels(trainingClient, trainingDataUrl);
```


## <a name="recognize-form-content"></a>フォーム コンテンツを認識する

Form Recognizer を使用すると、ドキュメント内の表、行、および単語を認識できます。モデルをトレーニングする必要はありません。

指定された URI にあるファイルの内容を認識するには、**beginRecognizeContentFromUrl** メソッドを使用します。

```java
private static void GetContent(
    FormRecognizerClient recognizerClient, String invoiceUri)
{
    String analyzeFilePath = invoiceUri;
    SyncPoller<FormRecognizerOperationResult, List<FormPage>> recognizeContentPoller =
        recognizerClient.beginRecognizeContentFromUrl(analyzeFilePath);
    
    List<FormPage> contentResult = recognizeContentPoller.getFinalResult();
```

返される値は **FormPage** オブジェクトのコレクションで、送信されたドキュメント内のページごとに 1 つあります。 次のコードでは、これらのオブジェクトを反復処理し、抽出されたキー/値のペアとテーブル データを出力します。

```java
    contentResult.forEach(formPage -> {
        // Table information
        System.out.println("----Recognizing content ----");
        System.out.printf("Has width: %f and height: %f, measured with unit: %s.%n", formPage.getWidth(),
            formPage.getHeight(),
            formPage.getUnit());
        formPage.getTables().forEach(formTable -> {
            System.out.printf("Table has %d rows and %d columns.%n", formTable.getRowCount(),
                formTable.getColumnCount());
            formTable.getCells().forEach(formTableCell -> {
                System.out.printf("Cell has text %s.%n", formTableCell.getText());
            });
            System.out.println();
        });
    });
}
```

### <a name="output"></a>出力

```console
Get form content...
----Recognizing content ----
Has width: 8.500000 and height: 11.000000, measured with unit: inch.
Table has 2 rows and 6 columns.
Cell has text Invoice Number.
Cell has text Invoice Date.
Cell has text Invoice Due Date.
Cell has text Charges.
Cell has text VAT ID.
Cell has text 458176.
Cell has text 3/28/2018.
Cell has text 4/16/2018.
Cell has text $89,024.34.
Cell has text ET.
```

## <a name="recognize-receipts"></a>領収書を認識する

このセクションでは、事前トレーニング済みの領収書モデルを使用して、米国の領収書から共通フィールドを認識して抽出する方法を示します。

URI からの領収書を認識するには、**beginRecognizeReceiptsFromUrl** メソッドを使用します。 返される値は **RecognizedReceipt** オブジェクトのコレクションで、送信されたドキュメント内のページごとに 1 つあります。

```java
private static void AnalyzeReceipt(
    FormRecognizerClient recognizerClient, String receiptUri)
{
    SyncPoller<FormRecognizerOperationResult, List<RecognizedForm>> syncPoller =
    recognizerClient.beginRecognizeReceiptsFromUrl(receiptUri);
    List<RecognizedForm> receiptPageResults = syncPoller.getFinalResult();
```

次のコード ブロックは、領収書を反復処理し、その詳細をコンソールに出力します。

```java
    for (int i = 0; i < receiptPageResults.size(); i++) {
        RecognizedForm recognizedForm = receiptPageResults.get(i);
        Map<String, FormField> recognizedFields = recognizedForm.getFields();
        System.out.printf("----------- Recognized Receipt page %d -----------%n", i);
        FormField merchantNameField = recognizedFields.get("MerchantName");
        if (merchantNameField != null) {
            if (FieldValueType.STRING == merchantNameField.getValue().getValueType()) {
                String merchantName = merchantNameField.getValue().asString();
                System.out.printf("Merchant Name: %s, confidence: %.2f%n",
                    merchantName, merchantNameField.getConfidence());
            }
        }
        FormField merchantAddressField = recognizedFields.get("MerchantAddress");
        if (merchantAddressField != null) {
            if (FieldValueType.STRING == merchantAddressField.getValue().getValueType()) {
                String merchantAddress = merchantAddressField.getValue().asString();
                System.out.printf("Merchant Address: %s, confidence: %.2f%n",
                    merchantAddress, merchantAddressField.getConfidence());
            }
        }
        FormField transactionDateField = recognizedFields.get("TransactionDate");
        if (transactionDateField != null) {
            if (FieldValueType.DATE == transactionDateField.getValue().getValueType()) {
                LocalDate transactionDate = transactionDateField.getValue().asDate();
                System.out.printf("Transaction Date: %s, confidence: %.2f%n",
                    transactionDate, transactionDateField.getConfidence());
            }
        }
```
次のコード ブロックは、領収書で検出された個々の項目を反復処理し、その詳細をコンソールに出力します。

```java
        FormField receiptItemsField = recognizedFields.get("Items");
        if (receiptItemsField != null) {
            System.out.printf("Receipt Items: %n");
            if (FieldValueType.LIST == receiptItemsField.getValue().getValueType()) {
                List<FormField> receiptItems = receiptItemsField.getValue().asList();
                receiptItems.stream()
                    .filter(receiptItem -> FieldValueType.MAP == receiptItem.getValue().getValueType())
                    .map(formField -> formField.getValue().asMap())
                    .forEach(formFieldMap -> formFieldMap.forEach((key, formField) -> {
                        if ("Name".equals(key)) {
                            if (FieldValueType.STRING == formField.getValue().getValueType()) {
                                String name = formField.getValue().asString();
                                System.out.printf("Name: %s, confidence: %.2fs%n",
                                    name, formField.getConfidence());
                            }
                        }
                        if ("Quantity".equals(key)) {
                            if (FieldValueType.FLOAT == formField.getValue().getValueType()) {
                                Float quantity = formField.getValue().asFloat();
                                System.out.printf("Quantity: %f, confidence: %.2f%n",
                                    quantity, formField.getConfidence());
                            }
                        }
                        if ("Price".equals(key)) {
                            if (FieldValueType.FLOAT == formField.getValue().getValueType()) {
                                Float price = formField.getValue().asFloat();
                                System.out.printf("Price: %f, confidence: %.2f%n",
                                    price, formField.getConfidence());
                            }
                        }
                        if ("TotalPrice".equals(key)) {
                            if (FieldValueType.FLOAT == formField.getValue().getValueType()) {
                                Float totalPrice = formField.getValue().asFloat();
                                System.out.printf("Total Price: %f, confidence: %.2f%n",
                                    totalPrice, formField.getConfidence());
                            }
                        }
                }));
            }
        }
    }
}
```

### <a name="output"></a>出力 

```console
Analyze receipt...
----------- Recognized Receipt page 0 -----------
Merchant Name: Contoso Contoso, confidence: 0.62
Merchant Address: 123 Main Street Redmond, WA 98052, confidence: 0.99
Transaction Date: 2020-06-10, confidence: 0.90
Receipt Items:
Name: Cappuccino, confidence: 0.96s
Quantity: null, confidence: 0.957s]
Total Price: 2.200000, confidence: 0.95
Name: BACON & EGGS, confidence: 0.94s
Quantity: null, confidence: 0.927s]
Total Price: null, confidence: 0.93
```

## <a name="train-a-custom-model"></a>カスタム モデルをトレーニングする

このセクションでは、独自のデータを使用してモデルをトレーニングする方法を示します。 トレーニング済みのモデルは、元のフォーム ドキュメント内のキー/値の関係を含む構造化データを出力できます。 モデルをトレーニングした後、モデルをテストおよび再トレーニングでき、最終的にはモデルを使用して、ニーズに従ってより多くのフォームから正確にデータを抽出できます。

> [!NOTE]
> また、[Form Recognizer のサンプル ラベル付けツール](../../quickstarts/label-tool.md)などのグラフィカル ユーザー インターフェイスを使用してモデルをトレーニングすることもできます。

### <a name="train-a-model-without-labels"></a>ラベルなしでモデルをトレーニングする

カスタム モデルをトレーニングして、トレーニング ドキュメントに手動でラベルを付けることなく、カスタム フォームにあるすべてのフィールドと値を認識できるようにします。

次のメソッドは、指定された一連のドキュメントでモデルをトレーニングし、モデルの状態をコンソールに出力します。 

```java
private static String TrainModel(
    FormTrainingClient trainingClient, String trainingDataUrl)
{
    SyncPoller<FormRecognizerOperationResult, CustomFormModel> trainingPoller =
        trainingClient.beginTraining(trainingDataUrl, false);
    
    CustomFormModel customFormModel = trainingPoller.getFinalResult();
    
    // Model Info
    System.out.printf("Model Id: %s%n", customFormModel.getModelId());
    System.out.printf("Model Status: %s%n", customFormModel.getModelStatus());
    System.out.printf("Training started on: %s%n", customFormModel.getTrainingStartedOn());
    System.out.printf("Training completed on: %s%n%n", customFormModel.getTrainingCompletedOn());
```
返される **CustomFormModel** オブジェクトには、モデルが認識できるフォームの種類と、それぞれのフォームの種類から抽出できるフィールドに関する情報が含まれています。 次のコード ブロックは、この情報をコンソールに出力します。

```java 
    System.out.println("Recognized Fields:");
    // looping through the subModels, which contains the fields they were trained on
    // Since the given training documents are unlabeled, we still group them but they do not have a label.
    customFormModel.getSubmodels().forEach(customFormSubmodel -> {
        // Since the training data is unlabeled, we are unable to return the accuracy of this model
        System.out.printf("The subModel has form type %s%n", customFormSubmodel.getFormType());
        customFormSubmodel.getFields().forEach((field, customFormModelField) ->
            System.out.printf("The model found field '%s' with label: %s%n",
                field, customFormModelField.getLabel()));
    });
```

最後に、このメソッドはモデルの一意の ID を返します。

```java
    return customFormModel.getModelId();
}
```

### <a name="output"></a>出力

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

### <a name="train-a-model-with-labels"></a>ラベルを使用してモデルをトレーニングする

トレーニング ドキュメントに手動でラベルを付けて、カスタム モデルをトレーニングすることもできます。 ラベルを使用してトレーニングを行うと、一部のシナリオでパフォーマンスの向上につながります。 ラベルを使用してトレーニングするには、トレーニング ドキュメントと共に、BLOB ストレージ コンテナーに特別なラベル情報ファイル ( *\<filename\>.pdf.labels.json*) を用意する必要があります。 [Form Recognizer のサンプル ラベル付けツール](../../quickstarts/label-tool.md)では、これらのラベル ファイルの作成を支援する UI が提供されています。 それらの用意ができたら、*useTrainingLabels* パラメーターを `true` に設定して **beginTraining** メソッドを呼び出すことができます。

```java
private static String TrainModelWithLabels(
    FormTrainingClient trainingClient, String trainingDataUrl)
{
    // Train custom model
    String trainingSetSource = trainingDataUrl;
    SyncPoller<FormRecognizerOperationResult, CustomFormModel> trainingPoller = trainingClient.beginTraining(trainingSetSource, true);

    CustomFormModel customFormModel = trainingPoller.getFinalResult();

    // Model Info
    System.out.printf("Model Id: %s%n", customFormModel.getModelId());
    System.out.printf("Model Status: %s%n", customFormModel.getModelStatus());
    System.out.printf("Training started on: %s%n", customFormModel.getTrainingStartedOn());
    System.out.printf("Training completed on: %s%n%n", customFormModel.getTrainingCompletedOn());
```

返される **CustomFormModel** は、モデルが抽出できるフィールドを、各フィールドの予測精度と共に示します。 次のコード ブロックは、この情報をコンソールに出力します。

```java
    // looping through the subModels, which contains the fields they were trained on
    // The labels are based on the ones you gave the training document.
    System.out.println("Recognized Fields:");
    // Since the data is labeled, we are able to return the accuracy of the model
    customFormModel.getSubmodels().forEach(customFormSubmodel -> {
        System.out.printf("The subModel with form type %s has accuracy: %.2f%n",
            customFormSubmodel.getFormType(), customFormSubmodel.getAccuracy());
        customFormSubmodel.getFields().forEach((label, customFormModelField) ->
            System.out.printf("The model found field '%s' to have name: %s with an accuracy: %.2f%n",
                label, customFormModelField.getName(), customFormModelField.getAccuracy()));
    });
    return customFormModel.getModelId();
}
```

### <a name="output"></a>出力

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

## <a name="analyze-forms-with-a-custom-model"></a>カスタム モデルを使用してフォームを分析する

このセクションでは、独自のフォームでトレーニングしたモデルを使用して、カスタムのフォームの種類からキー/値の情報やその他のコンテンツを抽出する方法について説明します。

> [!IMPORTANT]
> このシナリオを実装するには、モデルのトレーニングが完了している必要があります。それにより、次のメソッドにその ID を渡すことができます。 [モデルのトレーニング](#train-a-model-without-labels)に関するセクションを参照してください。

**beginRecognizeCustomFormsFromUrl** メソッドを使用します。 返される値は **RecognizedForm** オブジェクトのコレクションで、送信されたドキュメント内のページごとに 1 つあります。

```java
// Analyze PDF form data
private static void AnalyzePdfForm(
    FormRecognizerClient formClient, String modelId, String pdfFormUrl)
{    
    SyncPoller<FormRecognizerOperationResult, List<RecognizedForm>> recognizeFormPoller =
    formClient.beginRecognizeCustomFormsFromUrl(modelId, pdfFormUrl);

    List<RecognizedForm> recognizedForms = recognizeFormPoller.getFinalResult();
```

次のコードは、分析結果をコンソールに出力します。 認識された各フィールドと対応する値が、信頼度スコアと共に出力されます。

```java
    for (int i = 0; i < recognizedForms.size(); i++) {
        final RecognizedForm form = recognizedForms.get(i);
        System.out.printf("----------- Recognized custom form info for page %d -----------%n", i);
        System.out.printf("Form type: %s%n", form.getFormType());
        form.getFields().forEach((label, formField) ->
            // label data is populated if you are using a model trained with unlabeled data,
            // since the service needs to make predictions for labels if not explicitly given to it.
            System.out.printf("Field '%s' has label '%s' with a confidence "
                + "score of %.2f.%n", label, formField.getLabelData().getText(), formField.getConfidence()));
    }
}
```

### <a name="output"></a>出力

```console
Analyze PDF form...
----------- Recognized custom form info for page 0 -----------
Form type: form-0
Field 'field-0' has label 'Address:' with a confidence score of 0.91.
Field 'field-1' has label 'Invoice For:' with a confidence score of 1.00.
Field 'field-2' has label 'Invoice Number' with a confidence score of 1.00.
Field 'field-3' has label 'Invoice Date' with a confidence score of 1.00.
Field 'field-4' has label 'Invoice Due Date' with a confidence score of 1.00.
Field 'field-5' has label 'Charges' with a confidence score of 1.00.
Field 'field-6' has label 'VAT ID' with a confidence score of 1.00.
```


## <a name="manage-custom-models"></a>カスタム モデルを管理する

このセクションでは、アカウントに格納されているカスタム モデルを管理する方法について説明します。 次のコードは、例として、1 つのメソッドですべてのモデル管理タスクを実行します。 まず、次のメソッド シグネチャをコピーします。

```java
private static void ManageModels(
    FormTrainingClient trainingClient, String trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>FormRecognizer リソース アカウント内のモデルの数を確認する

次のコード ブロックは、Form Recognizer アカウントに保存したモデルの数を確認し、アカウントの制限と比較します。

```java
    AtomicReference<String> modelId = new AtomicReference<>();

    // First, we see how many custom models we have, and what our limit is
    AccountProperties accountProperties = trainingClient.getAccountProperties();
    System.out.printf("The account has %s custom models, and we can have at most %s custom models",
        accountProperties.getCustomModelCount(), accountProperties.getCustomModelLimit());
```

#### <a name="output"></a>出力 

```console
The account has 12 custom models, and we can have at most 250 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>リソース アカウントに現在格納されているモデルを一覧表示する

次のコード ブロックは、アカウント内の現在のモデルを一覧表示し、その詳細をコンソールに出力します。

```java    
    // Next, we get a paged list of all of our custom models
    PagedIterable<CustomFormModelInfo> customModels = trainingClient.listCustomModels();
    System.out.println("We have following models in the account:");
    customModels.forEach(customFormModelInfo -> {
        System.out.printf("Model Id: %s%n", customFormModelInfo.getModelId());
        // get custom model info
        modelId.set(customFormModelInfo.getModelId());
        CustomFormModel customModel = trainingClient.getCustomModel(customFormModelInfo.getModelId());
        System.out.printf("Model Id: %s%n", customModel.getModelId());
        System.out.printf("Model Status: %s%n", customModel.getModelStatus());
        System.out.printf("Training started on: %s%n", customModel.getTrainingStartedOn());
        System.out.printf("Training completed on: %s%n", customModel.getTrainingCompletedOn());
        customModel.getSubmodels().forEach(customFormSubmodel -> {
            System.out.printf("Custom Model Form type: %s%n", customFormSubmodel.getFormType());
            System.out.printf("Custom Model Accuracy: %.2f%n", customFormSubmodel.getAccuracy());
            if (customFormSubmodel.getFields() != null) {
                customFormSubmodel.getFields().forEach((fieldText, customFormModelField) -> {
                    System.out.printf("Field Text: %s%n", fieldText);
                    System.out.printf("Field Accuracy: %.2f%n", customFormModelField.getAccuracy());
                });
            }
        });
    });
```

#### <a name="output"></a>出力 

この応答は、読みやすくするために一部が省略されています。

```console
We have following models in the account:
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Status: ready
Training started on: 2020-06-04T18:33:08Z
Training completed on: 2020-06-04T18:33:10Z
Custom Model Form type: form-0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Custom Model Accuracy: 1.00
Field Text: invoice date
Field Accuracy: 1.00
Field Text: invoice number
Field Accuracy: 1.00
...
```

### <a name="delete-a-model-from-the-resource-account"></a>リソース アカウントからモデルを削除する

ID を参照することで、アカウントからモデルを削除することもできます。

```java
    // Delete Custom Model
    System.out.printf("Deleted model with model Id: %s, operation completed with status: %s%n", modelId.get(),
    trainingClient.deleteModelWithResponse(modelId.get(), Context.NONE).getStatusCode());
}
```


## <a name="run-the-application"></a>アプリケーションの実行

メイン プロジェクト ディレクトリに戻ります。 次に、次のコマンドを使用してアプリをビルドします。

```console
gradle build
```

`run` ゴールを使用してアプリケーションを実行します。

```console
gradle run
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>トラブルシューティング

Recognizer クライアントで `ErrorResponseException` 例外が発生します。 たとえば、無効なファイル ソースの URL を指定しようとすると、失敗の原因を示すエラーと共に `ErrorResponseException` が発生します。 次のコード スニペットでは、例外をキャッチし、エラーに関する追加情報を表示することで、エラーが適切に処理されます。

```java Snippet:FormRecognizerBadRequest
try {
    formRecognizerClient.beginRecognizeContentFromUrl("invalidSourceUrl");
} catch (ErrorResponseException e) {
    System.out.println(e.getMessage());
}
```

### <a name="enable-client-logging"></a>クライアントのログ記録を有効にする

Java 用の Azure SDK では、アプリケーション エラーをトラブルシューティングしてその解決を促進するために、一貫したログ記録が提供されます。 生成されたログでは、最終状態に達する前のアプリケーションのフローがキャプチャされ、根本原因を特定するのに役立ちます。 ログ記録の有効化に関するガイダンスについては、[ログ記録に関する Wiki](https://github.com/Azure/azure-sdk-for-java/wiki/Logging-with-Azure-SDK) を参照してください。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Form Recognizer Java クライアント ライブラリを使用してモデルをトレーニングし、さまざまな方法でフォームを分析しました。 次に、より適切なトレーニング データ セットを作成し、より正確なモデルを生成するためのヒントについて学習します。

> [!div class="nextstepaction"]
> [トレーニング データ セットの作成](../../build-training-data-set.md)

* [Form Recognizer とは](../../overview.md)
* このガイドのサンプル コード (およびその他の情報) については、[GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples) を参照してください。
