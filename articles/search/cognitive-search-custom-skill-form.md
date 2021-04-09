---
title: Form Recognizer カスタム スキル (C#)
titleSuffix: Azure Cognitive Search
description: C# および Visual Studio を使用して Form Recognizer カスタム スキルを作成する方法について説明します。
manager: nitinme
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: a3b073cdb90e0c427bfbca15c1440b9122672610
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98880137"
---
# <a name="example-create-a-form-recognizer-custom-skill"></a>例:Form Recognizer カスタム スキルを作成する

この Azure Cognitive Search スキルセットの例では、C# と Visual Studio を使用して Form Recognizer カスタム スキルを作成する方法を学習します。 Form Recognizer はドキュメントを分析し、キー/値ペアとテーブル データを抽出します。 Form Recognizer を[カスタム スキル インターフェイス](cognitive-search-custom-skill-interface.md)にラップすると、エンドツーエンドの強化パイプラインの手順としてこの機能を追加できます。 このパイプラインは、ドキュメントを読み込み、他の変換を実行できます。

## <a name="prerequisites"></a>前提条件

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (任意のエディション)。
- 同じ種類の 5 つ以上のフォーム。 このガイドで提供されているサンプル データを使用できます。

## <a name="create-a-form-recognizer-resource"></a>Form Recognizer リソースを作成する

[!INCLUDE [create resource](../cognitive-services/form-recognizer/includes/create-resource.md)]

## <a name="train-your-model"></a>モデルをトレーニングする

このスキルを使用する前に、入力フォームで Form Recognizer モデルのトレーニングを行う必要があります。 モデルのトレーニング方法については、[cURL クイックスタート](../cognitive-services/form-recognizer/quickstarts/client-library.md?pivots=programming-language-rest-api)に従ってください。 このクイックスタートに記載されているサンプル フォームを使用するか、独自のデータを使用できます。 モデルのトレーニング後、その ID 値を安全な場所にコピーします。

## <a name="set-up-the-custom-skill"></a>カスタム スキルを設定する

このチュートリアルでは、[Azure Search Power Skills](https://github.com/Azure-Samples/azure-search-power-skills) GitHub リポジトリで [AnalyzeForm](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Vision/AnalyzeForm) プロジェクトを使用します。 このリポジトリをローカル コンピューターに複製し、**Vision/AnalyzeForm/** に移動して、プロジェクトにアクセスします。 次に、Visual Studio で _AnalyzeForm.csproj_ を開きます。 このプロジェクトでは、[カスタム スキル インターフェイス](cognitive-search-custom-skill-interface.md)を満たし、Azure Cognitive Search の強化に使用できる Azure 関数リソースを作成します。 フォーム ドキュメントを入力として受け取り、指定したキー/値ペアを (テキストとして) 出力します。

まず、プロジェクト レベルの環境変数を追加します。 左側のウィンドウで **AnalyzeForm** プロジェクトを見つけ、右クリックして **[プロパティ]** を選択します。 **[プロパティ]** ウィンドウで **[デバッグ]** タブをクリックし、 **[環境変数]** フィールドを見つけます。 **[追加]** をクリックして以下の変数を追加します。
* `FORMS_RECOGNIZER_ENDPOINT_URL`、エンドポイント URL に設定されている値を含む。
* `FORMS_RECOGNIZER_API_KEY`、サブスクリプション キーに設定されている値を含む。
* `FORMS_RECOGNIZER_MODEL_ID`、トレーニングを行ったモデルの ID に設定されている値を含む。
* `FORMS_RECOGNIZER_RETRY_DELAY`、1000 に設定されている値を含む。 この値は、プログラムがクエリを再試行する前に待機する時間 (ミリ秒単位) です。
* `FORMS_RECOGNIZER_MAX_ATTEMPTS`、100 に設定されている値を含む。 この値は、プログラムが正常な応答を取得しようとしているときに、サービスに対してクエリを実行する回数です。

次に、_AnalyzeForm.cs_ を開き、*field-mappings.json* ファイルを参照する `fieldMappings` 変数を見つけます。 このファイル (およびそれを参照する変数) は、フォームから抽出するキーのリストと、各キーのカスタム ラベルを定義します。 たとえば、`{ "Address:", "address" }, { "Invoice For:", "recipient" }` の値は、検出された `Address:` フィールドと `Invoice For:` フィールドの値のみを保存することを意味します。これらの値には、それぞれ `"address"` と `"recipient"` を使用してラベルが付けられます。

最後に、`contentType` 変数に注意してください。 このスクリプトは、URL によって参照されるリモート ドキュメントに対して、指定された Form Recognizer モデルを実行するので、コンテンツの種類は `application/json` になります。 HTTP 要求にバイト ストリームを含めることによってローカル ファイルを分析する場合は、ファイルの適切な [MIME の種類](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types)に `contentType` を変更する必要があります。

## <a name="test-the-function-from-visual-studio"></a>Visual Studio から関数をテストする

プロジェクトを編集したら、それを保存し、**AnalyzeForm** プロジェクトを Visual Studio のスタートアップ プロジェクトとして設定します (まだ設定されていない場合)。 次に、**F5** を押し、ローカル環境で関数を実行します。 [Postman](https://www.postman.com/) などの REST サービスを使用して関数を呼び出します。

### <a name="http-request"></a>HTTP 要求

関数を呼び出すために次の要求を行います。

```HTTP
POST https://localhost:7071/api/analyze-form
```

### <a name="request-body"></a>要求本文

以下の要求本文テンプレートを使用して開始します。

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": { 
                "formUrl": "<your-form-url>",
                "formSasToken": "<your-sas-token>"
            }
        }
    ]
}
```

ここでは、トレーニングを行ったフォームと同じ種類のフォームの URL を指定する必要があります。 テスト目的で、トレーニング フォームのいずれかを使用できます。 cURL クイックスタートに従っている場合、フォームは Azure BLOB ストレージ アカウントに配置されます。 Azure Storage Explorer を開き、フォーム ファイルを見つけて右クリックし、 **[Shared Access Signature の取得]** を選択します。 次のダイアログ ウィンドウには、URL と SAS トークンが表示されます。 これらの文字列は、要求本文の `"formUrl"` と `"formSasToken"` の各フィールドにそれぞれ入力します。

> [!div class="mx-imgBorder"]
> ![Azure Storage Explorer で pdf ドキュメントが選択されています](media/cognitive-search-skill-form/form-sas.png)

Azure BLOB ストレージにないリモート ドキュメントを分析する場合は、`"formUrl"` フィールドに URL を貼り付け、`"formSasToken"` フィールドは空白のままにします。

> [!NOTE]
> スキルがスキルセットに統合されると、 Cognitive Search によって URL とトークンが提供されます。

### <a name="response"></a>Response

次の例のような応答が表示されます。

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": {
                "address": "1111 8th st. Bellevue, WA 99501 ",
                "recipient": "Southridge Video 1060 Main St. Atlanta, GA 65024 "
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Azure に関数を発行する

関数の動作に満足したら、発行できます。

1. Visual Studio の **ソリューション エクスプローラー** でプロジェクトを右クリックし、 **[発行]** を選択します。 **[新規作成]**  >  **[発行]** の順に選択します。
1. まだ Visual Studio を Azure アカウントに接続していない場合は、 **[アカウントの追加]** を選択します。
1. 画面の指示に従います。 使用するアプリ サービス、Azure サブスクリプション、リソース グループ、ホスティング プラン、ストレージ アカウントに対して一意の名前を指定します。 リソース グループ、ホスティング プラン、ストレージ アカウントをまだ作成していない場合は新しく作成できます。 完了したら、 **[作成]** をクリックします。
1. デプロイが完了したら、サイトの URL を書き留めておきます。 この URL は Azure の関数アプリのアドレスになります。 一時的な場所にこれを保存します。
1. [Azure portal](https://portal.azure.com) でリソース グループに移動し、発行した `AnalyzeForm` 関数を探します。 **[管理]** セクションに [ホスト キー] が表示されます。 *既定* のホスト キーをコピーし、一時的な場所に保存します。

## <a name="connect-to-your-pipeline"></a>パイプラインに接続する

このスキルを Cognitive Search パイプラインで使用するには、スキルの定義をスキルセットに追加する必要があります。 次の JSON ブロックは、サンプルのスキル定義です (特定のシナリオとスキルセット環境を反映するように入力と出力を更新する必要があります)。 `AzureFunctionEndpointUrl` を関数の URL に置き換え、`AzureFunctionDefaultHostKey` をホスト キーに置き換えます。

```json
{ 
  "description":"Skillset that invokes the Form Recognizer custom skill",
  "skills":[ 
    "[... your existing skills go here]",
    { 
      "@odata.type":"#Microsoft.Skills.Custom.WebApiSkill",
      "name":"formrecognizer",
      "description":"Extracts fields from a form using a pre-trained form recognition model",
      "uri":"[AzureFunctionEndpointUrl]/api/analyze-form?code=[AzureFunctionDefaultHostKey]",
      "httpMethod":"POST",
      "timeout":"PT30S",
      "context":"/document",
      "batchSize":1,
      "inputs":[ 
        { 
          "name":"formUrl",
          "source":"/document/metadata_storage_path"
        },
        { 
          "name":"formSasToken",
          "source":"/document/metadata_storage_sas_token"
        }
      ],
      "outputs":[ 
        { 
          "name":"address",
          "targetName":"address"
        },
        { 
          "name":"recipient",
          "targetName":"recipient"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>次のステップ

このガイドでは、Azure Form Recognizer サービスからカスタム スキルを作成しました。 カスタム スキルの詳細については、次のリソースを参照してください。 

* [Azure Search Power Skills: カスタム スキルのリポジトリ](https://github.com/Azure-Samples/azure-search-power-skills)
* [AI エンリッチメント パイプラインにカスタム スキルを追加する方法](cognitive-search-custom-skill-interface.md)
* [スキルセットを定義する](cognitive-search-defining-skillset.md)
* [スキルセットを作成する (REST)](/rest/api/searchservice/create-skillset)
* [強化されたフィールドをマッピングする](cognitive-search-output-field-mapping.md)