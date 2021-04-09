---
title: カスタム スキルの例 (Python)
titleSuffix: Azure Cognitive Search
description: Python 開発者向けに、Azure Functions と Visual Studio を使用してカスタム スキルを構築するためのツールと手法について説明します。 カスタム スキルには、Azure Cognitive Search で AI によって強化されたインデックス作成パイプラインに追加できるユーザー定義モデルまたはロジックが含まれています。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 40e20ad4bab0275b44cd868521c7dc70dec52567
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88936131"
---
# <a name="example-create-a-custom-skill-using-python"></a>例:Python を使用してカスタム スキルを作成する

この Azure Cognitive Search スキルセットの例では、Python と Visual Studio Code を使用して Web API のカスタム スキルを作成する方法を学習します。 この例では、[カスタム スキル インターフェイス](cognitive-search-custom-skill-interface.md)を実装する [Azure 関数](https://azure.microsoft.com/services/functions/)を使用します。

Python でのカスタム スキルの開発に使用されるツールと手法に集中できるように、カスタム スキルは、意図的に単純にしてあります (2 つの文字列の連結)。 単純なスキルを正常に実行できたら、より複雑なシナリオを手がけることができます。

## <a name="prerequisites"></a>前提条件

+ カスタム スキルで実装する必要がある入出力インターフェイスの概要については、[カスタム スキル インターフェイス](cognitive-search-custom-skill-interface.md)に関する記事を参照してください。

+ 環境を設定します。 [このチュートリアルを最初から最後まで](/azure/python/tutorial-vs-code-serverless-python-01)実行し、Visual Studio Code と Python の拡張機能を使用してサーバーレス Azure Functions を設定しました。 このチュートリアルでは、次のツールとコンポーネントのインストールについて説明しています。 

  + [Python 3.75](https://www.python.org/downloads/release/python-375/)
  + [Visual Studio Code](https://code.visualstudio.com/)
  + [Visual Studio Code 用の Python 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  + [Azure Functions Core Tools](../azure-functions/functions-run-local.md#v2)
  + [Visual Studio Code 用 Azure Functions 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)

## <a name="create-an-azure-function"></a>Azure Function の作成

この例では、Azure 関数を使用して、Web API をホストするという概念を示していますが、その他の方法も可能です。 [コグニティブ スキルのインターフェイス要件](cognitive-search-custom-skill-interface.md)を満たしていれば、どのような方法を使用してもかまいません。 ただし、Azure Functions を使用すると、カスタム スキルを簡単に作成できます。

### <a name="create-a-function-app"></a>関数アプリの作成

Visual Studio Code の Azure Functions プロジェクト テンプレートでは、Azure の関数アプリに発行できるプロジェクトを作成します。 関数アプリを使用すると、リソースを管理、デプロイ、および共有するための論理ユニットとして関数をグループ化できます。

1. Visual Studio Code で、F1 キーを押してコマンド パレットを開きます。 コマンド パレットで、`Azure Functions: Create new project...` を検索して選択します。

1. プロジェクト ワークスペースのディレクトリの場所を選択し、 **[選択]** をクリックします。

    > [!NOTE]
    > これらの手順は、ワークスペースの外部で実行するように設計されています。 この理由により、ワークスペースの一部であるプロジェクト フォルダーは選択しないでください。

1. 関数アプリ プロジェクトの言語を選択します。 このチュートリアルでは、**[Python]** を選択します。
1. Python のバージョンを選択します (Azure Functions では、バージョン 3.7.5 がサポートされています)。
1. プロジェクトの最初の関数のテンプレートを選択します。 **[HTTP トリガー]** を選択して、HTTP によってトリガーされる関数を新しい関数アプリに作成します。
1. 関数名を指定します。 このケースでは、「**Concatenator**」を使用しましょう。 
1. 認証レベルとして **[関数]** を選択します。 これは、関数の HTTP エンドポイントを呼び出すために[関数キー](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)を指定することを意味します。 
1. プロジェクトを開く方法を選択します。 この手順では、**[ワークスペースに追加]** を選択して、現在のワークスペースに関数アプリを作成します。

Visual Studio Code によって、新しいワークスペースに関数アプリ プロジェクトが作成されます。 このプロジェクトには、[host.json](../azure-functions/functions-host-json.md) および [local.settings.json](../azure-functions/functions-run-local.md#local-settings-file) 構成ファイルと、言語固有のプロジェクト ファイルが含まれています。 

新しい HTTP によってトリガーされる関数は、関数アプリ プロジェクトの **Concatenator** フォルダーにも作成されます。 その中には、次の内容の "\_\_init__.py" というファイルがあります。

```py
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )

```

次に、そのコードを変更して、[カスタム スキル インターフェイス](cognitive-search-custom-skill-interface.md)に従います。 次の内容でコードを変更します。

```py
import logging
import azure.functions as func
import json

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    try:
        body = json.dumps(req.get_json())
    except ValueError:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )
    
    if body:
        result = compose_response(body)
        return func.HttpResponse(result, mimetype="application/json")
    else:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )


def compose_response(json_data):
    values = json.loads(json_data)['values']
    
    # Prepare the Output before the loop
    results = {}
    results["values"] = []
    
    for value in values:
        output_record = transform_value(value)
        if output_record != None:
            results["values"].append(output_record)
    return json.dumps(results, ensure_ascii=False)

## Perform an operation on a record
def transform_value(value):
    try:
        recordId = value['recordId']
    except AssertionError  as error:
        return None

    # Validate the inputs
    try:         
        assert ('data' in value), "'data' field is required."
        data = value['data']        
        assert ('text1' in data), "'text1' field is required in 'data' object."
        assert ('text2' in data), "'text2' field is required in 'data' object."
    except AssertionError  as error:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Error:" + error.args[0] }   ]       
            })

    try:                
        concatenated_string = value['data']['text1'] + " " + value['data']['text2']  
        # Here you could do something more interesting with the inputs

    except:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Could not complete operation for record." }   ]       
            })

    return ({
            "recordId": recordId,
            "data": {
                "text": concatenated_string
                    }
            })
```

**transform_value** メソッドは、1 つのレコードに対して操作を実行します。 このメソッドは、特定のニーズに合うように変更できます。 必ず、必要な入力検証を行い、そのレコードに対して操作を完了できなかった場合に生成されるエラーと警告が返されるようにしてください。

### <a name="debug-your-code-locally"></a>コードをローカルでデバッグする

Visual Studio Code では、コードを簡単にデバッグすることができます。 F5 キーを押すか、**[デバッグ]** メニューにアクセスして、**[デバッグの開始]** を選択します。

目的の行で F9 キーを押すと、コードにブレークポイントを設定できます。

デバッグを開始すると、関数がローカルで実行されます。 Postman や Fiddler などのツールを使用して、localhost に要求を発行できます。 ターミナル ウィンドウでローカル エンドポイントの場所を確認します。 

## <a name="publish-your-function"></a>関数を発行する

関数の動作に満足したら、発行できます。

1. Visual Studio Code で、F1 キーを押してコマンド パレットを開きます。 コマンド パレットで、**[Deploy to Function App...]\(関数アプリにデプロイする...\)** を検索して選択します。 

1. アプリケーションをデプロイする Azure サブスクリプションを選択します。

1. **[+ Create New Function App in Azure]\(+ Azure で新しい関数アプリを作成する\)** を選択します。

1. 関数アプリのグローバルに一意の名前を入力します。

1. Python のバージョンを選択します (この関数では Python 3.7.x が機能します)。

1. 新しいリソースの場所を選択します (たとえば、[米国西部 2])。

この時点で、Azure で新しい Azure 関数をホストするために必要なリソースが Azure サブスクリプションに作成されます。 デプロイが完了するまで待ちます。 出力ウィンドウに、デプロイ プロセスの状態が表示されます。

1. [Azure portal](https://portal.azure.com) で **[すべてのリソース]** に移動し、発行した関数を名前で探します。 **Concatenator** という名前を付けた場合は、そのリソースを選択します。

1. **[</> Get Function URL]\(関数 URL の取得\)** ボタンをクリックします。 これにより、関数を呼び出す URL をコピーできます。

## <a name="test-the-function-in-azure"></a>Azure で関数をテストする

既定のホスト キーが用意できたので、次のように関数をテストします。

```http
POST [Function URL you copied above]
```

### <a name="request-body"></a>要求本文
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "text1":  "Hello",
                "text2":  "World"
            }
        },
        {
            "recordId": "e2",
            "data": "This is an invalid input"
        }
    ]
}
```

この例では、以前にローカル環境で関数を実行したときと同じ結果が得られるはずです。

## <a name="connect-to-your-pipeline"></a>パイプラインに接続する

新しいカスタム スキルが作成できたので、スキルセットに追加できます。 次の例では、スキルを呼び出して、ドキュメントのタイトルと作成者を連結して、merged_title_author という 1 つのフィールドを作成する方法を示します。 `[your-function-url-here]` は、新しい Azure 関数の URL に置き換えます。

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new search custom skill",
        "uri": "https://[your-function-url-here]",        
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "text1",
              "source": "/document/metadata_title"
            },
            {
              "name": "text2",
              "source": "/document/metadata_author"
            },
          ],
          "outputs": [
            {
              "name": "text",
              "targetName": "merged_title_author"
            }
          ]
      }
  ]
}
```

## <a name="next-steps"></a>次のステップ
お疲れさまでした。 最初のカスタム スキルが作成されました。 これで同じパターンに従って、独自のカスタム機能を追加できます。 詳細については、以下のリンクをクリックしてください。

+ [Power Skills: カスタム スキルのリポジトリ](https://github.com/Azure-Samples/azure-search-power-skills)
+ [AI エンリッチメント パイプラインにカスタム スキルを追加する方法](cognitive-search-custom-skill-interface.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [スキルセットを作成する (REST)](/rest/api/searchservice/create-skillset)
+ [エンリッチされたフィールドをマップする方法](cognitive-search-output-field-mapping.md)