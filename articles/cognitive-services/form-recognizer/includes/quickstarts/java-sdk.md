---
title: クイック スタート:Java 用 Form Recognizer クライアント ライブラリ
description: Java 用の Form Recognizer クライアント ライブラリを使用して、カスタム ドキュメントからキーと値のペアとテーブル データを抽出するフォーム処理アプリを作成します。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 02/12/2021
ms.custom: devx-track-java
ms.author: lajanuar
ms.openlocfilehash: 2e52bc8e6e41165da2f274d3613337fe603ccc39
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445634"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!IMPORTANT]
> この記事のコードでは、単純化するために、同期メソッドと、セキュリティで保護されていない資格情報の格納を使用しています。

[リファレンスのドキュメント](/java/api/overview/azure/ai-formrecognizer-readme) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src) | [パッケージ (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) | [サンプル](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* 最新バージョンの [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK)
* [Gradle ビルド ツール](https://gradle.org/install/)、または別の依存関係マネージャー。
* Azure サブスクリプションを用意できたら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Form Recognizer リソースを作成"  target="_blank">Form Recognizer リソースを作成</a>し、自分のキーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
  * 自分のアプリケーションを Form Recognizer API に接続するには、作成したリソースのキーとエンドポイントが必要になります。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
  * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
* トレーニング データのセットを含む Azure Storage Blob。 トレーニング データ セットをまとめるためのヒントとオプションについては、「[カスタム モデルのトレーニング データ セットを作成する](../../build-training-data-set.md)」を参照してください。 このクイックスタートでは、[サンプル データ セット](https://go.microsoft.com/fwlink/?linkid=2090451)の **Train** フォルダーにあるファイルを使用できます (*sample_data.zip* をダウンロードして展開します)。

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

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

このクイックスタートでは、Gradle 依存関係マネージャーを使用します。 クライアント ライブラリとその他の依存関係マネージャーの情報については、[Maven Central Repository](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) を参照してください。

プロジェクトの *build.gradle.kts* ファイルに、必要なプラグインと設定と共に、クライアント ライブラリを `implementation` ステートメントとして含めます。

#### <a name="v21-preview"></a>[v2.1 プレビュー](#tab/preview)

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
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.1.0-beta.1")
}
```

> [!NOTE]
> Form Recognizer 3.1.0 SDK は、API バージョン 2.1 プレビューを反映しています

#### <a name="v20"></a>[v2.0](#tab/ga)

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

> [!NOTE]
> Form Recognizer 3.0.0 SDK は、API v2.0 を反映しています

---

### <a name="create-a-java-file"></a>Java ファイルを作成する


作業ディレクトリから、次のコマンドを実行します。

```console
mkdir -p src/main/java
```

新しいフォルダーに移動し、*FormRecognizer.java* という名前のファイルを作成します。 それを任意のエディターまたは IDE で開き、以下の `import` ステートメントを追加します。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_imports)]

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/FormRecognizer/FormRecognizer.java) にあり、このクイックスタートのコード例が含まれています。


アプリケーションの **FormRecognizer** クラスで、対象のリソースのキーとエンドポイントの変数を作成します。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_creds)]

> [!IMPORTANT]
> Azure Portal にアクセスします。 「**前提条件**」セクションで作成した Form Recognizer リソースが正常にデプロイされた場合、 **[次の手順]** の下にある **[リソースに移動]** ボタンをクリックします。 キーとエンドポイントは、リソースの **[key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。 
>
> 終わったらコードからキーを削除し、公開しないよう注意してください。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 詳細については、Cognitive Services の[セキュリティ](../../../cognitive-services-security.md)に関するページを参照してください。

アプリケーションの **main** メソッドで、このクイックスタートで使用するメソッドの呼び出しを追加します。 その定義は後から行います。 また、トレーニング データとテスト データの URL への参照を追加する必要もあります。

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]
  
   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL の取得":::
* テストするフォームの URL を取得するには、上記の手順を使用して、BLOB ストレージ内の個々のドキュメントの SAS URL を取得できます。 または、別の場所にあるドキュメントの URL を取得します。
* 上記のメソッドを使用して、領収書の画像の URL も取得します。
<!-- markdownlint-disable MD024 -->
#### <a name="v21-preview"></a>[v2.1 プレビュー](#tab/preview)

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_maincalls)]

#### <a name="v20"></a>[v2.0](#tab/ga)

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_maincalls)]

---

## <a name="object-model"></a>オブジェクト モデル

Form Recognizer で作成できるクライアントは 2 種類あります。 1 つは、`FormRecognizerClient` です。認識されたフォームのフィールドやコンテンツをサービスに照会するときに使用します。 もう 1 つは `FormTrainingClient` です。認識精度を高めるために使用できるカスタム モデルを作成したり管理したりするときに使用します。

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` には、以下を目的とした操作が用意されています。

* 対象のカスタム フォームを分析するようトレーニングされたカスタム モデルを使用して、フォームのフィールドやコンテンツを認識する。  これらの値は、`RecognizedForm` オブジェクトのコレクションとして返されます。 [カスタム フォームを分析する](#analyze-forms-with-a-custom-model)例を参照してください。
* モデルをトレーニングせずにフォームのコンテンツ (表、行、単語など) を認識する。  フォームのコンテンツは、`FormPage` オブジェクトのコレクションとして返されます。 [レイアウトを分析する](#analyze-layout)例を参照してください。
* Form Recognizer サービスの事前トレーニング済みの領収書モデルを使用して、米国の領収書から一般的なフィールドを認識する。  これらのフィールドとメタデータは、`RecognizedForm` オブジェクトのコレクションとして返されます。 [領収書を分析する](#analyze-receipts)例を参照してください。

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` には、以下を目的とした操作が用意されています。

* カスタム モデルをトレーニングして、対象のカスタム フォームにあるすべてのフィールドと値を分析する。  モデルによって分析されるフォームの種類とそれぞれのフォームの種類で抽出されるフィールドを示す `CustomFormModel` が返されます。
* 対象のカスタム フォームにラベル付けすることによって指定した特定のフィールドと値を分析するように、カスタム モデルをトレーニングする。  モデルによって抽出されるフィールドと各フィールドの推定精度を示す `CustomFormModel` が返されます。
* アカウントに作成されたモデルを管理する。
* Form Recognizer リソース間でカスタム モデルをコピーする。

> [!NOTE]
> モデルのトレーニングは、[Form Recognizer のラベル付けツール](../../quickstarts/label-tool.md)など、グラフィカル ユーザー インターフェイスを使用して行うこともできます。

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Java 用 Form Recognizer クライアント ライブラリを使用して次のタスクを実行する方法を示します。
<!-- markdownlint-disable MD001 -->
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

**main** メソッドの先頭に、次のコードを追加します。 ここでは、上で定義したサブスクリプション変数を使用して 2 つのクライアント オブジェクトを認証します。 必要に応じて、新しいクライアント オブジェクトを作成せずに API キーを更新できるように、**AzureKeyCredential** オブジェクトを使用します。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_auth)]

## <a name="analyze-layout"></a>レイアウトを分析する

Form Recognizer を使用すると、ドキュメント内の表、行、および単語を分析できます。モデルをトレーニングする必要はありません。 レイアウトの抽出の詳細については、[レイアウトの概念ガイド](../../concept-layout.md)を参照してください。

指定された URL にあるファイルの内容を分析するには、**beginRecognizeContentFromUrl** メソッドを使用します。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_call)]

> [!TIP]
> また、ローカルのファイルから内容を取得することもできます。 [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) のメソッドを参照してください (**beginRecognizeContent** など)。 また、ローカルの画像に関連したシナリオについては、[GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) 上のサンプル コードを参照してください。

返される値は **FormPage** オブジェクトのコレクションで、送信されたドキュメント内のページごとに 1 つあります。 次のコードでは、これらのオブジェクトを反復処理し、抽出されたキー/値のペアとテーブル データを出力します。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_print)]
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

## <a name="analyze-invoices"></a>請求書を分析する

#### <a name="v21-preview"></a>[v2.1 プレビュー](#tab/preview)

このセクションでは、事前トレーニング済みのモデルを使用して、売上請求書から共通フィールドを分析、抽出する方法を示します。 請求書の分析の詳細については、[請求書の概念ガイド](../../concept-invoices.md)を参照してください。

URL から請求書を分析するには、`beginRecognizeInvoicesFromUrl` メソッドを使用します。 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_invoice_call)]

> [!TIP]
> ローカルの請求書を分析することもできます。 [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) のメソッドを参照してください (**beginRecognizeInvoices** など)。 また、ローカルの画像に関連したシナリオについては、[GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) 上のサンプル コードを参照してください。

返される値は **RecognizedForm** オブジェクトのコレクションで、ドキュメント内の請求書ごとに 1 つです。 次のコードでは、指定された URI にある請求書を処理し、主要なフィールドと値をコンソールに出力します。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_invoice_print)]

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

次のメソッドは、指定された一連のドキュメントでモデルをトレーニングし、モデルの状態をコンソールに出力します。 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_call)]

返される **CustomFormModel** オブジェクトには、モデルが分析できるフォームの種類と、それぞれのフォームの種類から抽出できるフィールドに関する情報が含まれています。 次のコード ブロックは、この情報をコンソールに出力します。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_print)]

最後に、このメソッドはモデルの一意の ID を返します。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_return)]


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

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_call)]


返される **CustomFormModel** は、モデルが抽出できるフィールドを、各フィールドの予測精度と共に示します。 次のコード ブロックは、この情報をコンソールに出力します。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_print)]


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

**beginRecognizeCustomFormsFromUrl** メソッドを使用します。 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_call)]

> [!TIP]
> ローカルのファイルを分析することもできます。 [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) のメソッドを参照してください (**beginRecognizeCustomForms** など)。 また、ローカルの画像に関連したシナリオについては、[GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) 上のサンプル コードを参照してください。

返される値は **RecognizedForm** オブジェクトのコレクションで、送信されたドキュメント内のページごとに 1 つです。次のコードは、分析結果をコンソールに出力します。 認識された各フィールドと対応する値が、信頼度スコアと共に出力されます。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_print)]


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

## <a name="analyze-receipts"></a>領収書を分析する

このセクションでは、事前トレーニング済みの領収書モデルを使用して、米国のレシートから共通フィールドを分析、抽出する方法を示します。 レシートの分析の詳細については、[レシートの概念ガイド](../../concept-receipts.md)を参照してください。

URI からレシートを分析するには、**beginRecognizeReceiptsFromUrl** メソッドを使用します。 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_call)]

> [!TIP]
> ローカルにあるレシートの画像を分析することもできます。 [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) のメソッドを参照してください (**beginRecognizeReceipts** など)。 また、ローカルの画像に関連したシナリオについては、[GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) 上のサンプル コードを参照してください。

返される値は **RecognizedReceipt** オブジェクトのコレクションで、送信されたドキュメント内のページごとに 1 つあります。 次のコード ブロックは、領収書を反復処理し、その詳細をコンソールに出力します。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print)]

次のコード ブロックは、領収書で検出された個々の項目を反復処理し、その詳細をコンソールに出力します。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print_items)]

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

## <a name="analyze-business-cards"></a>名刺を分析する

#### <a name="v21-preview"></a>[v2.1 プレビュー](#tab/preview)

このセクションでは、事前トレーニング済みのモデルを使用して、英語の名刺から共通フィールドを分析、抽出する方法を示します。 名刺の分析の詳細については、[名刺の概念ガイド](../../concept-business-cards.md)を参照してください。

URL から名刺を分析するには、`beginRecognizeBusinessCardsFromUrl` メソッドを使用します。 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_bc_call)]

> [!TIP]
> ローカルにある名刺の画像を分析することもできます。 [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) のメソッドを参照してください (**beginRecognizeBusinessCards** など)。 また、ローカルの画像に関連したシナリオについては、[GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) 上のサンプル コードを参照してください。

返される値は **RecognizedForm** オブジェクトのコレクションで、ドキュメント内の名刺ごとに 1 つです。 次のコードでは、指定された URI にある名刺を処理し、主要なフィールドと値をコンソールに出力します。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_bc_print)]

#### <a name="v20"></a>[v2.0](#tab/ga)

> [!IMPORTANT]
> この機能は、選択した API バージョンでは使用できません。

---

## <a name="manage-custom-models"></a>カスタム モデルを管理する

このセクションでは、アカウントに格納されているカスタム モデルを管理する方法について説明します。 次のコードは、例として、1 つのメソッドですべてのモデル管理タスクを実行します。 まず、次のメソッド シグネチャをコピーします。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage)]


### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>FormRecognizer リソース アカウント内のモデルの数を確認する

次のコード ブロックは、Form Recognizer アカウントに保存したモデルの数を確認し、アカウントの制限と比較します。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_count)]


#### <a name="output"></a>出力 

```console
The account has 12 custom models, and we can have at most 250 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>リソース アカウントに現在格納されているモデルを一覧表示する

次のコード ブロックは、アカウント内の現在のモデルを一覧表示し、その詳細をコンソールに出力します。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_list)]


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

ID を参照して、アカウントからモデルを削除することもできます。

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_delete)]

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
