---
title: 'チュートリアル: Azure 関数を使用して格納済みドキュメントを処理する'
titleSuffix: Azure Cognitive Services
description: このガイドでは、Azure 関数を使用して、Azure BLOB ストレージ コンテナーにアップロードされたドキュメントの処理をトリガーする方法について説明します。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 03/19/2021
ms.author: lajanuar
ms.openlocfilehash: 8c72a018f03b5284d3fc53be02d9eb526cdfcf28
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722244"
---
# <a name="tutorial-use-an-azure-function-to-process-stored-documents"></a>チュートリアル: Azure 関数を使用して格納済みドキュメントを処理する

Form Recognizer は、Azure Functions で構築された自動データ処理パイプラインの一部として使用できます。 このガイドでは、Azure 関数を使用して、Azure BLOB ストレージ コンテナーにアップロードされたドキュメントを処理する方法について説明します。 このワークフローでは、Form Recognizer レイアウト サービスを使用して格納済みドキュメントからテーブル データを抽出し、そのテーブル データを Azure で .csv ファイルに保存します。 その後、Microsoft Power BI (ここでは説明しません) を使用してデータを表示できます。

> [!div class="mx-imgBorder"]
> ![Aure サービス ワークフローの図](./media/tutorial-azure-function/workflow-diagram.png)

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Storage アカウントの作成
> * Azure Functions プロジェクトを作成する
> * アップロードされたフォームからレイアウト データを抽出する
> * レイアウト データを Azure Storage にアップロードする

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Form Recognizer リソースを作成"  target="_blank">Form Recognizer リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、自分の Form Recognizer キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
  * 自分のアプリケーションを Form Recognizer API に接続するには、作成したリソースのキーとエンドポイントが必要になります。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
  * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
* 分析対象のローカル PDF ドキュメント。 この[サンプル ドキュメント](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout.pdf)をダウンロードして使用できます。
* [Python 3.8.x](https://www.python.org/downloads/) がインストールされていること。
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) がインストールされていること。
* [Azure Functions Core Tools](https://docs.microsoft.com/azure/azure-functions/functions-run-local?tabs=windows%2Ccsharp%2Cbash#install-the-azure-functions-core-tools) がインストールされていること。
* Visual Studio Code と次の拡張機能がインストールされていること。
  * [Azure Functions 拡張機能](https://docs.microsoft.com/azure/developer/python/tutorial-vs-code-serverless-python-01#visual-studio-code-python-and-the-azure-functions-extension)
  * [Python の拡張機能](https://code.visualstudio.com/docs/python/python-tutorial#_install-visual-studio-code-and-the-python-extension)

## <a name="create-an-azure-storage-account"></a>Azure Storage アカウントを作成する

Azure portal で [Azure ストレージ アカウントを作成](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)します。 [アカウントの種類] には **[StorageV2]** を選択します。

左側のペインで **[CORS]** タブを選択し、既存の CORS ポリシーが存在する場合は削除します。

これがデプロイされたら、**test** と **output** という名前の 2 つの空の BLOB ストレージ コンテナーを作成します。

## <a name="create-an-azure-functions-project"></a>Azure Functions プロジェクトを作成する

Visual Studio Code を開きます。 Azure Functions 拡張機能をインストールしている場合は、左側のナビゲーション ウィンドウに Azure のロゴが表示されます。 それを選択します。 新しいプロジェクトを作成し、プロンプトが表示されたら、プロジェクトを格納するための **coa_new** ローカル フォルダーを作成します。

![VSCode の関数の作成ボタン](./media/tutorial-azure-function/vs-code-create-function.png)


いくつかの設定を構成するように求められます。
* **[言語の選択]** プロンプトで、[Python] を選択します。
* **[テンプレートの選択]** プロンプトで、[Azure Blob Storage trigger]\(Azure Blob Storage トリガー\) を選択します。 次に、既定のトリガーに名前を付けます。
* **[設定の選択]** プロンプトで、新しいローカル アプリ設定を作成することを選択します。
* 作成したストレージ アカウントを持つ Azure サブスクリプションを選択します。 次に、ストレージ コンテナーの名前を入力する必要があります  (この場合は `test/{name}`)。
* 現在のウィンドウでプロジェクトを開くことを選択します。 

![VSCode の作成プロンプトの例](./media/tutorial-azure-function/vs-code-prompt.png)

これらの手順を完了すると、VSCode によって、 *\_\_init\_\_.py* Python スクリプトを持つ新しい Azure 関数プロジェクトが追加されます。 このスクリプトは、ファイルが **test** ストレージ コンテナーにアップロードされたときにトリガーされますが、何も実行しません。

## <a name="test-the-function"></a>関数をテストする

F5 キーを押して、基本的な関数を実行します。 VSCode によって、インターフェイスとして使用するストレージ アカウントを選択するよう求められます。 作成したストレージ アカウントを選択し、続行します。

Azure Storage Explorer を開き、サンプル PDF ドキュメントを **Test** コンテナーにアップロードします。 次に、VSCode ターミナルを確認します。 このスクリプトは、PDF のアップロードによってトリガーされたことをログしているはずです。

![VSCode ターミナル テスト](./media/tutorial-azure-function/vs-code-terminal-test.png)


続行する前に、スクリプトを停止してください。

## <a name="add-form-processing-code"></a>フォーム処理コードを追加する

次に、独自のコードを Python スクリプトに追加して、Form Recognizer サービスを呼び出し、アップロードしたドキュメントを Form Recognizer [Layout API](concept-layout.md) を使用して解析します。

VSCode で、この関数の *requirements.txt* ファイルに移動します。 これにより、お使いのスクリプトに対する依存関係が定義されます。 次の Python パッケージをこのファイルに追加します。

```
cryptography
azure-functions
azure-storage-blob
azure-identity
requests
pandas
numpy
```

次に、 *\_\_init\_\_.py* スクリプトを開きます。 次の `import` ステートメントを追加します。

```Python
import logging
from azure.storage.blob import BlobServiceClient
import azure.functions as func
import json
import time
from requests import get, post
import os
from collections import OrderedDict
import numpy as np
import pandas as pd
```

生成された `main` 関数はそのまま使用できます。 この関数内にカスタム コードを追加します。

```python
# This part is automatically generated
def main(myblob: func.InputStream):
    logging.info(f"Python blob trigger function processed blob \n"
    f"Name: {myblob.name}\n"
    f"Blob Size: {myblob.length} bytes")
```

次のコード ブロックは、アップロードされたドキュメントに対して  Form Recognizer [Analyze Layout](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync) API を呼び出します。 エンドポイントとキーの値を入力します。 


# <a name="version-20"></a>[バージョン 2.0](#tab/2-0)

```Python
# This is the call to the Form Recognizer endpoint
    endpoint = r"Your Form Recognizer Endpoint"
    apim_key = "Your Form Recognizer Key"
    post_url = endpoint + "/formrecognizer/v2.0/Layout/analyze"
    source = myblob.read()

    headers = {
    # Request headers
    'Content-Type': 'application/pdf',
    'Ocp-Apim-Subscription-Key': apim_key,
        }

    text1=os.path.basename(myblob.name)
```

# <a name="version-21-preview"></a>[バージョン 2.1 プレビュー](#tab/2-1)

```Python
# This is the call to the Form Recognizer endpoint
    endpoint = r"Your Form Recognizer Endpoint"
    apim_key = "Your Form Recognizer Key"
    post_url = endpoint + "/formrecognizer/v2.1-preview.3/Layout/analyze"
    source = myblob.read()

    headers = {
    # Request headers
    'Content-Type': 'application/pdf',
    'Ocp-Apim-Subscription-Key': apim_key,
        }

    text1=os.path.basename(myblob.name)
```
---

> [!IMPORTANT]
> Azure Portal にアクセスします。 「**前提条件**」セクションで作成した Form Recognizer リソースが正常にデプロイされた場合、 **[次の手順]** の下にある **[リソースに移動]** ボタンをクリックします。 キーとエンドポイントは、リソースの **[key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。 
>
> 終わったらコードからキーを削除し、公開しないよう注意してください。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 詳細については、[Cognitive Services のセキュリティ](../cognitive-services-security.md)に関する記事を参照してください。

次に、サービスに対してクエリを実行してから返されたデータを取得するコードを追加します。 


```Python
resp = requests.post(url = post_url, data = source, headers = headers)
    if resp.status_code != 202:
        print("POST analyze failed:\n%s" % resp.text)
        quit()
    print("POST analyze succeeded:\n%s" % resp.headers)
    get_url = resp.headers["operation-location"]

    wait_sec = 25
    
    time.sleep(wait_sec)
    # The layout API is async therefore the wait statement
    
    resp =requests.get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
    
    resp_json = json.loads(resp.text)
    
    
    status = resp_json["status"]
    
    
    if status == "succeeded":
        print("Layout Analysis succeeded:\n%s")
        results=resp_json
    else:
        print("GET Layout results failed:\n%s")
        quit()

    results=resp_json
```

その後、次のコードを追加して、Azure Storage の **output** コンテナーに接続します。 ストレージ アカウントの名前とキーには、独自の値を入力します。 このキーは、Azure portal のストレージ リソースの **[アクセス キー]** タブで取得できます。

```Python
# This is the connection to the blob storage, with the Azure Python SDK
    blob_service_client = BlobServiceClient.from_connection_string("DefaultEndpointsProtocol=https;AccountName="Storage Account Name";AccountKey="storage account key";EndpointSuffix=core.windows.net")
    container_client=blob_service_client.get_container_client("output")
```

次のコードは、返された Form Recognizer 応答を解析し、.csv ファイルを作成して、**output** コンテナーにアップロードします。 


> [!IMPORTANT]
> 多くの場合、独自のフォーム ドキュメントの構造に合わせて、このコードを編集する必要があります。

```Python
# The code below is how I extract the json format into tabular data 
    # Please note that you need to adjust the code below to your form structure
    # It probably won't work out-of-box for your specific form
    pages = results["analyzeResult"]["pageResults"]

    def make_page(p):
        res=[]
        res_table=[]
        y=0
        page = pages[p]
        for tab in page["tables"]:
            for cell in tab["cells"]:
                res.append(cell)
                res_table.append(y)
            y=y+1
    
        res_table=pd.DataFrame(res_table)
        res=pd.DataFrame(res)
        res["table_num"]=res_table[0]
        h=res.drop(columns=["boundingBox","elements"])
        h.loc[:,"rownum"]=range(0,len(h))
        num_table=max(h["table_num"])
        return h, num_table, p

    h, num_table, p= make_page(0)   

    for k in range(num_table+1):
        new_table=h[h.table_num==k]
        new_table.loc[:,"rownum"]=range(0,len(new_table))
        row_table=pages[p]["tables"][k]["rows"]
        col_table=pages[p]["tables"][k]["columns"]
        b=np.zeros((row_table,col_table))
        b=pd.DataFrame(b)
        s=0
        for i,j in zip(new_table["rowIndex"],new_table["columnIndex"]):
            b.loc[i,j]=new_table.loc[new_table.loc[s,"rownum"],"text"]
            s=s+1

```

最後に、コードの最後のブロックによって、抽出されたテーブルとテキスト データが BLOB ストレージ要素にアップロードされます。

```Python
    # Here is the upload to the blob storage
    tab1_csv=b.to_csv(header=False,index=False,mode='w')
    name1=(os.path.splitext(text1)[0]) +'.csv'
    container_client.upload_blob(name=name1,data=tab1_csv)
```

## <a name="run-the-function"></a>関数を実行する

F5 キーを押して、関数を再度実行します。 Azure Storage Explorer を使用して、サンプルの PDF フォームを **Test** ストレージ コンテナーにアップロードします。 この操作により、スクリプトの実行がトリガーされ、結果として得られる .csv ファイル (テーブルとして表示されます) が **output** コンテナーに表示されます。

このコンテナーを Power BI に接続すると、それに含まれるデータを詳細に視覚化できます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Python で記述された Azure 関数を使用して、アップロードされた PDF ドキュメントを自動的に処理し、その内容をよりデータに適した形式で出力する方法について学習しました。 次に、Power BI を使用してデータを表示する方法について学習します。

> [!div class="nextstepaction"]
> [Microsoft Power BI](https://powerbi.microsoft.com/integrations/azure-table-storage/)

* [Form Recognizer とは](overview.md)
* [Layout API](concept-layout.md) に関する詳細情報
