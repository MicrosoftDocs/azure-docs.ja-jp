---
title: 'ML Studio (classic): API Management を使用して Web サービスを管理する - Azure'
description: API Management を使用した AzureML Web サービスの管理方法について説明するガイドです。 ユーザー アクセス、使用帯域幅の調整、ダッシュボードの監視を定義することで、REST API エンドポイントを管理します。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/03/2017
ms.openlocfilehash: 293643ab5354c51142baaf281b1845cfc9fa6f23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100518030"
---
# <a name="manage-azure-machine-learning-studio-classic-web-services-using-api-management"></a>API Management を使用して Azure Machine Learning Studio (クラシック) Web サービスを管理する

**適用対象:** ![適用対象: ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (classic)   ![適用対象外: ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


## <a name="overview"></a>概要
このガイドでは、API Management を使用して Azure Machine Learning Studio (クラシック) Web サービスを管理する方法について簡単に説明します。

## <a name="what-is-azure-api-management"></a>Azure API Management とは
Azure API Management は、ユーザー アクセス、使用帯域幅の調整、ダッシュボードの監視を定義することで、REST API エンドポイントを管理できる Azure のサービスです。 詳細については、[API Management のサイト](https://azure.microsoft.com/services/api-management/)をご覧ください。 Azure API Management の使用を開始するには、[インポートと発行のガイド](../../api-management/import-and-publish.md)を参照してください。 このガイドがベースとなる他のガイドでは、通知の構成、価格レベル、応答の処理、ユーザー認証、製品、開発者のサブスクリプション、使用状況のダッシュボードなどのトピックについて説明します。

## <a name="prerequisites"></a>前提条件
このガイドを完了するには、以下が必要です。

* Azure アカウント。
* AzureML アカウント。
* Web サービスとしてデプロイされる AzureML 実験用のワークスペース、サービス、api_key。 AzureML 実験の作成方法の詳細については、[Studio のクイックスタート](create-experiment.md)をご覧ください。 Studio (クラシック) の実験を Web サービスとしてデプロイする方法の詳細については、[Studio のデプロイ方法](deploy-a-machine-learning-web-service.md) で、AzureML 実験を Web サービスとしてデプロイする方法の詳細を参照してください。 また、シンプルな AzureML 実験を作成してテストし、Web サービスとしてデプロイする方法については付録 A をご覧ください。

## <a name="create-an-api-management-instance"></a>API Management インスタンスの作成

Azure Machine Learning Web サービスは、API Management インスタンスを使って管理できます。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. **[+ リソースの作成]** を選択します。
3. 検索ボックスに「API management」と入力して "API Management" リソースを選択します。
4. **Create** をクリックしてください。
5. **[名前]** の値は、一意の URL を作成するために使用されます (この例では "demoazureml" を使用します)。
6. サービス インスタンスの **[サブスクリプション]** 、 **[リソース グループ]** 、 **[場所]** を選択します。
7. **[組織名]** の値を指定します (この例では "demoazureml" を使用します)。
8. **[管理者のメール アドレス]** を入力します。API Management システムからの通知には、このメール アドレスが使用されます。
9. **Create** をクリックしてください。

新しいサービスの作成には最大 30 分かかる場合があります。

![スクリーンショットは、サービスを作成するために必要なオプションを含む [API Management サービス] ダイアログ ボックスを示しています。](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>API の作成
サービス インスタンスが作成されたら、API を作成します。 API は、クライアント アプリケーションから呼び出すことのできる一連の操作で構成されます。 API の操作は、既存の Web サービスに引き渡されます。 このガイドでは、既存の AzureML RRS と BES Web サービスにプロキシする API を作成します。

API を作成するには、次の手順に従います。

1. Azure portal で、先ほど作成したサービス インスタンスを開きます。
2. 左側のナビゲーション ウィンドウで **[API]** を選択します。

   ![api-management-menu](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. **[API の追加]** をクリックします。
2. **[Web API 名]** を入力します (この例では "AzureML Demo API" を使用します)。
3. **[Web サービスの URL]** に「`https://ussouthcentral.services.azureml.net`」と入力します。
4. **Web API URL サフィックス**を入力します。 ユーザーがサービス インスタンスに要求を送信する際に使用する URL 末尾の構成要素になります (この例では "azureml-demo" を使用します)。
5. **[Web API URL scheme]\(Web API の URL スキーム\)** に **[HTTPS]** を選択します。
6. **[製品]** として **[スターター]** を選択します。
7. **[保存]** をクリックします。


## <a name="add-the-operations"></a>操作の追加

操作を API に追加して構成するには、パブリッシャー ポータルを使用します。 発行者ポータルにアクセスするには、API Management サービスの Azure Portal で **[パブリッシャー ポータル]** をクリックし、 **[API]** 、 **[操作]** の順に選択して、 **[操作の追加]** をクリックします。

![add-operation](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

**[新しい操作]** ウィンドウが表示され、 **[署名]** タブが既定で選択されます。

## <a name="add-rrs-operation"></a>RRS 操作の追加
まず、AzureML RRS サービスの操作を作成します。

1. **[HTTP 動詞]** に **[POST]** を選択します。
2. **[URL テンプレート]** に「`/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}`」と入力します。
3. **[表示名]** を入力します (この例では "RRS Execute" を使用します)。

   ![スクリーンショットは、表示名を入力できる [署名] ページを示しています。](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. 左側の **[応答]**  >  **[追加]** をクリックし、 **[200 OK]** を選択します。
5. **[保存]** を選択してこの操作を保存します。

   ![スクリーンショットは、操作の [RRS Execute] ページと [保存] ボタンを示しています。](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>BES 操作の追加

> [!NOTE]
> RRS 操作の追加の場合とよく似ているため、BES 操作のスクリーン ショットは省略します。

### <a name="submit-but-not-start-a-batch-execution-job"></a>バッチ実行ジョブの送信 (開始はしない)

1. **[操作を追加]** をクリックして、BES 操作を API に追加します。
2. **[HTTP 動詞]** に **[POST]** を選択します。
3. **[URL テンプレート]** に「`/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}`」と入力します。
4. **[表示名]** を入力します (この例では "BES Submit" を使用します)。
5. 左側の **[応答]**  >  **[追加]** をクリックし、 **[200 OK]** を選択します。
6. **[保存]** をクリックします。

### <a name="start-a-batch-execution-job"></a>バッチ実行ジョブを送信する

1. **[操作を追加]** をクリックして、BES 操作を API に追加します。
2. **[HTTP 動詞]** に **[POST]** を選択します。
3. **[HTTP 動詞]** に「`/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}`」と入力します。
4. **[表示名]** を入力します (この例では "BES Start" を使用します)。
6. 左側の **[応答]**  >  **[追加]** をクリックし、 **[200 OK]** を選択します。
7. **[保存]** をクリックします。

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>バッチ実行ジョブの状態または結果を取得する

1. **[操作を追加]** をクリックして、BES 操作を API に追加します。
2. **[HTTP 動詞]** に **[GET]** を選択します。
3. **[URL テンプレート]** に「`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`」と入力します。
4. **[表示名]** を入力します (この例では "BES Status" を使用します)。
6. 左側の **[応答]**  >  **[追加]** をクリックし、 **[200 OK]** を選択します。
7. **[保存]** をクリックします。

### <a name="delete-a-batch-execution-job"></a>バッチ実行ジョブの削除

1. **[操作を追加]** をクリックして、BES 操作を API に追加します。
2. **[HTTP 動詞]** に **[削除]** を選択します。
3. **[URL テンプレート]** に「`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`」と入力します。
4. **[表示名]** を入力します (この例では "BES Delete" を使用します)。
5. 左側の **[応答]**  >  **[追加]** をクリックし、 **[200 OK]** を選択します。
6. **[保存]** をクリックします。

## <a name="call-an-operation-from-the-developer-portal"></a>開発者ポータルから操作を呼び出す

開発者ポータルには、API の操作を見てテストするための便利が環境が用意されており、操作を直接呼び出すことができます。 この手順では、**AzureML Demo API** に追加された **RRS Execute** メソッドを呼び出します。 

1. **[開発者ポータル]** をクリックします。

   ![スクリーンショットは、開発者ポータルのリンクを示しています。](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. 上部のメニューで **[API]** をクリックし、 **[AzureML Demo API]** をクリックして、利用できる操作を表示します。

   ![スクリーンショットは、AzureML Demo API のリンクを示しています。](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. 操作の **[RRS Execute]** を選択します。 **[試用版]** をクリックします。

   ![スクリーンショットは、[RRS Execute] が選択されている [AzureML Demo API] ダイアログ ボックスと [試用版] ボタンを示しています。](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. **[要求パラメーター]** の **[ワークスペース]** と **[サービス]** を入力し、 **[apiversion]** に「2.0」を、 **[詳細]** に「true」を入力します。 AzureML Web サービスのダッシュボードに **ワークスペース** と **サービス** が表示されます (付録 A の「**Web サービスをテストする**」をご覧ください)。

   **[要求ヘッダー]** で **[ヘッダーの追加]** をクリックし、「Content-Type」と「application/json」を入力します。 **[ヘッダーの追加]** をもう一度クリックし、「Authorization」と「Bearer *\<your service API-KEY\>* 」を入力します。 AzureML Web サービスのダッシュボードに API キーが表示されます (付録 A の「**Web サービスをテストする**」をご覧ください)。

   **[要求本文]** に「`{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}`」と入力します。

   ![スクリーンショットは、AzureML Demo API の要求パラメーター、要求ヘッダー、要求本文、および承認を示しています。](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. **[送信]** をクリックします。

   ![このスクリーンショットは、[送信] ボタンを示しています。](./media/manage-web-service-endpoints-using-api-management/send.png)

操作を呼び出すと、バックエンド サービスの **要求された URL**、**応答のステータス**、**応答ヘッダー**、**応答内容** が開発者ポータルに表示されます。

![スクリーンショットは、応答のステータス、応答の待機時間、応答ヘッダー、および応答内容が表示された開発者ポータルを示しています。](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>付録 A - シンプルな AzureML Web サービスを作成しテストする
### <a name="creating-the-experiment"></a>実験の作成
シンプルな AzureML 実験を作成し、Web サービスとしてデプロイする手順を次に示します。 Web サービスは、任意のテキストの列を入力として取得し、整数として表される機能のセットを返します。 次に例を示します。

| Text | ハッシュされたテキスト |
| --- | --- |
| This is a good day |1 1 2 2 0 2 0 1 |

まず、任意のブラウザーを使用して、[https://studio.azureml.net/](https://studio.azureml.net/) に移動し、資格情報を入力してログインします。 次に、新しい空白の実験を作成します。

![スクリーンショットは、[実験] が選択された新しいページとテキスト検索を示しています。](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

名前を **SimpleFeatureHashingExperiment** に変更します。 **[保存されたデータセット]** を展開し、 **[Amazon の書評]** を実験にドラッグします。

![スクリーンショットは、左側にサンプル、右側に SimpleFeatureHashingExperiment ウィンドウと「ここに項目をドラッグ」という指示を示しています。](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

**[データ操作]** と **[操作]** を展開し、 **[データセット内の列の選択]** を実験にドラッグします。 **[Amazon の書評]** を **[データセット内の列の選択]** に接続します。

![書評データセット モジュールをプロジェクト列モジュールに接続する](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

**[データセット内の列の選択]** をクリックし、次に **[列セレクターの起動]** をクリックし、**Col2** を選択します。 チェック マークをクリックして、これらの変更を適用します。

![列名を使用して列を選択する](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

**[テキスト分析]** を展開し、 **[特徴ハッシュ]** を実験にドラッグします。 **[データセット内の列の選択]** を **[特徴ハッシュ]** に接続します。

![スクリーンショットは、ワークスペースに追加される特徴ハッシュ項目を示しています。](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

**Hashing bitsize** に「**3**」を入力します。 これにより、8 (23) 列が作成されます。

![スクリーンショットは、[特徴ハッシュ] が選択された [プロパティ] を示しています。また、ハッシュ ビットサイズを入力できることを示しています。](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

この時点で、 **[実行]** をクリックして実験をテストできます。

![このスクリーンショットは、[実行] ボタンを示しています。](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Web サービスの作成
Web サービスを作成します。 **[Web サービス]** を展開し、 **[入力]** を実験にドラッグします。 **[入力]** を **[特徴ハッシュ]** に接続します。 **[出力]** を実験にドラッグします。 **[出力]** を **[特徴ハッシュ]** に接続します。

![スクリーンショットは、指定された変更の後のワークスペースを示しています。](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

**[Web サービスの発行]** をクリックします。

![スクリーンショットは、[Web サービスの発行] ボタンを示しています。](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

**[はい]** をクリックして実験を発行します。

![スクリーンショットは、確認メッセージと、発行するかどうかのオプションを示しています。](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>Web サービスをテストする
AzureML web サービスは、RSS (要求/応答サービス) と BES (バッチ実行サービス) のエンドポイントで構成されます。 RSS は、同期の実行用です。 BES は、同期ジョブの実行用です。 次のサンプルの Python ソースを使用して、Web サービスをテストするには、Azure SDK for Python をダウンロードして、インストールする必要があります (「[Python のインストール方法](/azure/developer/python/azure-sdk-install)」を参照)。

次のサンプルのソースには、実験の **workspace**、**service**、**api_key** が必要です。 Web サービス ダッシュボードの実験の **[要求/応答]** か **[バッチ実行]** をクリックするとワークスペースとサービスが表示されます。

![スクリーンショットは、[要求] ウィンドウを示しています。このウィンドウでは、ワークスペースとサービスの値を確認できます。](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Web サービス ダッシュボードの実験をクリックすると **api_key** が表示されます。

![スクリーンショットは、Web サービス ダッシュボード内の実験を示しています。そこで API キーを見つけることができます。](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>RRS エンドポイントのテスト
##### <a name="test-button"></a>テスト ボタン
RRS エンドポイントを簡単にテストするには、Web サービス ダッシュ ボードで **[テスト]** をクリックします。

![スクリーンショットは、[テスト] ボタンのある Web サービス ダッシュボード内の実験を示しています。](./media/manage-web-service-endpoints-using-api-management/test.png)

**[col2]** に「**This is a good day**」と入力します。 チェック マークをクリックします。

![スクリーンショットは、[予測するデータの入力] ダイアログ ボックスを示しています。そこには、例の「This is a good day」のようなテキストを入力できます。](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

次のような結果が表示されます。

![スクリーンショットは実験の結果を示しています。「This is a good day」と複数の数字が引用符で囲まれています。](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>サンプル コード
クライアント コードから RRS テストする方法もあります。 ダッシュボードで **[要求/応答]** をクリックし、最下部までスクロールすると、C#、Python、R のサンプル コードが表示されます。また、要求 URI、ヘッダー、本文を含む RRS 要求の構文も表示されます。

このガイドでは、Python の機能例について説明します。 実験の **workspace**、**service**、**api_key** を使用して変更する必要があります。

```python
import urllib2
import json
workspace = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE WORKSPACE ID>"
service = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE SERVICE ID>"
api_key = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE API KEY>"
data = {
"Inputs": {
    "input1": {
        "ColumnNames": ["Col2"],
        "Values": [ [ "This is a good day" ] ]
    },
},
"GlobalParameters": { }
}
url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
body = str.encode(json.dumps(data))
try:
    req = urllib2.Request(url, body, headers)
    response = urllib2.urlopen(req)
    result = response.read()
    print "result:" + result
        except urllib2.HTTPError, error:
    print("The request failed with status code: " + str(error.code))
    print(error.info())
    print(json.loads(error.read()))
```

#### <a name="test-bes-endpoint"></a>BES エンドポイントのテスト
ダッシュボードの **[バッチ実行]** をクリックして、最下部までスクロールします。 C# の場合、Python、R のサンプル コードが表示されます。また、ジョブを送信する、ジョブを送信する、ジョブを開始する、ジョブのステータスか結果を取得する、ジョブを削除するなどの BES 要求の構文も表示されます。

このガイドでは、Python の機能例について説明します。 実験の **workspace**、**service**、**api_key** を使用して変更する必要があります。 また、**ストレージ アカウント名**、**ストレージ アカウント キー**、**ストレージ コンテナー名** を変更するがあります。 最後に、 **[入力ファイル]** の場所と **[出力ファイル]** の場所を変更する必要があります。

```python
import urllib2
import json
import time
from azure.storage import *
workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
service = "<REPLACE WITH YOUR SERVICE ID>"
api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
input_blob_name = "mydatablob.csv"
output_blob_name = "myresultsblob.csv"
def printHttpError(httpError):
print("The request failed with status code: " + str(httpError.code))
print(httpError.info())
print(json.loads(httpError.read()))
return
def saveBlobToFile(blobUrl, resultsLabel):
print("Reading the result from " + blobUrl)
try:
    response = urllib2.urlopen(blobUrl)
except urllib2.HTTPError, error:
    printHttpError(error)
    return
with open(output_file, "wb+") as f:
    f.write(response.read())
print(resultsLabel + " have been written to the file " + output_file)
return
def processResults(result):
first = True
results = result["Results"]
for outputName in results:
    result_blob_location = results[outputName]
    sas_token = result_blob_location["SasBlobToken"]
    base_url = result_blob_location["BaseLocation"]
    relative_url = result_blob_location["RelativeLocation"]
    print("The results for " + outputName + " are available at the following Azure Storage location:")
    print("BaseLocation: " + base_url)
    print("RelativeLocation: " + relative_url)
    print("SasBlobToken: " + sas_token)
    if (first):
        first = False
        url3 = base_url + relative_url + sas_token
        saveBlobToFile(url3, "The results for " + outputName)
return

def invokeBatchExecutionService():
url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
print("Uploading the input to blob storage...")
data_to_upload = open(input_file, "r").read()
blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
print "Uploaded the input to blob storage"
input_blob_path = "/" + storage_container_name + "/" + input_blob_name
connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
payload =  {
    "Input": {
        "ConnectionString": connection_string,
        "RelativeLocation": input_blob_path
    },
    "Outputs": {
        "output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
    },
    "GlobalParameters": {
    }
}
    body = str.encode(json.dumps(payload))
headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
print("Submitting the job...")
# submit the job
req = urllib2.Request(url + "?api-version=2.0", body, headers)
try:
    response = urllib2.urlopen(req)
except urllib2.HTTPError, error:
    printHttpError(error)
    return
result = response.read()
job_id = result[1:-1] # remove the enclosing double-quotes
print("Job ID: " + job_id)
# start the job
print("Starting the job...")
req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
try:
    response = urllib2.urlopen(req)
except urllib2.HTTPError, error:
    printHttpError(error)
    return
url2 = url + "/" + job_id + "?api-version=2.0"

while True:
    print("Checking the job status...")
    # If you are using Python 3+, replace urllib2 with urllib.request in the following code
    req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    result = json.loads(response.read())
    status = result["StatusCode"]
    print "status:" + status
    if (status == 0 or status == "NotStarted"):
        print("Job " + job_id + " not yet started...")
    elif (status == 1 or status == "Running"):
        print("Job " + job_id + " running...")
    elif (status == 2 or status == "Failed"):
        print("Job " + job_id + " failed!")
        print("Error details: " + result["Details"])
        break
    elif (status == 3 or status == "Cancelled"):
        print("Job " + job_id + " cancelled!")
        break
    elif (status == 4 or status == "Finished"):
        print("Job " + job_id + " finished!")
        processResults(result)
        break
    time.sleep(1) # wait one second
return
invokeBatchExecutionService()
```