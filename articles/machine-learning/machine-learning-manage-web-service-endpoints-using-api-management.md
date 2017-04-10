---
title: "API Management を使用した AzureML Web サービスの管理方法について説明します | Microsoft Docs"
description: "API Management を使用した AzureML Web サービスの管理方法について説明するガイドです。"
keywords: "Machine Learning、api 管理"
services: machine-learning
documentationcenter: 
author: roalexan
manager: jhubbard
editor: 
ms.assetid: 05150ae1-5b6a-4d25-ac67-fb2f24a68e8d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2017
ms.author: roalexan
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 444e352f2f3fc7ec9258b16272936c8ed8d4fbf6


---
# <a name="learn-how-to-manage-azureml-web-services-using-api-management"></a>API Management を使用した AzureML Web サービスの管理方法
## <a name="overview"></a>Overview
このガイドでは、API Management を使用して AzureML Web サービスを管理する方法について簡単に説明します。

## <a name="what-is-azure-api-management"></a>Azure API Management とは
Azure API Management は、ユーザー アクセス、使用帯域幅の調整、ダッシュボードの監視を定義することで、REST API エンドポイントを管理できる Azure のサービスです。 [ここ](https://azure.microsoft.com/services/api-management/) をクリックして、Azure API Management の詳細についてご覧ください。 [ここ](../api-management/api-management-get-started.md) をクリックして、Azure API Management を使用する方法についてのガイドをご覧ください。 このガイドがベースとなる他のガイドでは、通知の構成、価格レベル、応答の処理、ユーザー認証、製品、開発者のサブスクリプション、使用状況のダッシュボードなどのトピックについて説明します。

## <a name="what-is-azureml"></a>Azure ML とは
AzureML は、高度な分析ソリューションを簡単に構築、デプロイ、共有できる、機械学習用の Azure サービスです。 [ここ](https://azure.microsoft.com/services/machine-learning/) をクリックして、AzureML の詳細についてご覧ください。

## <a name="prerequisites"></a>前提条件
このガイドを完了するには、以下が必要です。

* Azure アカウント。 Azure アカウントを持っていない場合は、 [ここ](https://azure.microsoft.com/pricing/free-trial/) をクリックして、無料試用版用アカウントの作成方法の詳細についてご覧ください。
* AzureML アカウント。 AzureML アカウントを持っていない場合は、 [ここ](https://studio.azureml.net/) をクリックして、無料試用版用アカウントの作成方法の詳細についてご覧ください。
* Web サービスとしてデプロイされる AzureML 実験用のワークスペース、サービス、api_key。 [ここ](machine-learning-create-experiment.md) をクリックして、AzureML 実験の作成方法の詳細についてご覧ください。 [ここ](machine-learning-publish-a-machine-learning-web-service.md) をクリックして、Web サービスとして AzureML 実験をデプロイする方法の詳細についてご覧ください。 また、シンプルな AzureML 実験を作成してテストし、Web サービスとしてデプロイする方法については付録 A をご覧ください。

## <a name="create-an-api-management-instance"></a>API Management インスタンスの作成
API Management を使用して、AzureML Web サービスを管理する手順を次に示します。 まず、サービス インスタンスを作成します。 [クラシック ポータル](https://manage.windowsazure.com/)にログインし、**[新規]** > **[App Services]** > **[API Management]** > **[作成]**をクリックします。

![create-instance](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-instance.png)

一意の **URL**を指定します。 このガイドでは **demoazureml** を使用しますが、実際には別のものを選択する必要があります。 サービス インスタンスの **[サブスクリプション]** と **[リージョン]** を選択します。 それらを選択したら、次に進むボタンをクリックします。

![create-service-1](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-service-1.png)

**[組織名]**の値を指定します。 このガイドでは **demoazureml** を使用しますが、実際には別のものを選択する必要があります。 **[管理者の電子メール]** フィールドに電子メール アドレスを入力します。 API Management システムからの通知には、この電子メール アドレスが使用されます。

![create-service-2](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-service-2.png)

チェック ボックスをクリックすると、サービス インスタンスが作成されます。 *新しいサービスを作成するまでに最大で 30 分かかります*。

## <a name="create-the-api"></a>API の作成
サービス インスタンスが作成されたら、API を作成します。 API は、クライアント アプリケーションから呼び出すことのできる一連の操作で構成されます。 API の操作は、既存の Web サービスに引き渡されます。 このガイドでは、既存の AzureML RRS と BES Web サービスにプロキシする API を作成します。

API は API パブリッシャー ポータルから作成され、構成されます。このポータルには、Azure クラシック ポータルからアクセスします。 パブリッシャー ポータルに到達するには、サービス インスタンスを選択します。

![select-service-instance](./media/machine-learning-manage-web-service-endpoints-using-api-management/select-service-instance.png)

ご利用の API Management サービスの Azure クラシック ポータルで **[管理]** をクリックします。

![manage-service](./media/machine-learning-manage-web-service-endpoints-using-api-management/manage-service.png)

左側の **[API Management]** メニューの **[API]** をクリックし、**[API の追加]** をクリックします。

![api-management-menu](./media/machine-learning-manage-web-service-endpoints-using-api-management/api-management-menu.png)

**[Web API 名]** として「**AzureML Demo API**」と入力します。 **[Web サービス URL]** として「**https://ussouthcentral.services.azureml.net**」と入力します。 **[Web API URL サフィックス]** として「**azureml-demo**」と入力します。 **[Web API URL]** として **[HTTPS]** を選択します。 **[製品]** として **[スターター]** を選択します。 完了したら、**[保存]** をクリックして、API を作成します。

![add-new-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-new-api.png)

## <a name="add-the-operations"></a>操作の追加
**[操作の追加]** をクリックして新しい操作を この API に追加します。

![add-operation](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-operation.png)

**[新しい操作]** ウィンドウが表示され、**[署名]** タブが既定で選択されます。

## <a name="add-rrs-operation"></a>RRS 操作の追加
まず、AzureML RRS サービスの操作を作成します。 **[HTTP 動詞]** として **[POST]** を選択します。 **[URL template]** として「**/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}**」と入力します。 **[表示名]** として「**RRS Execute**」と入力します。

![add-rrs-operation-signature](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

左側の **[応答]** > **[追加]** をクリックし、**[200 OK]** を選択します。 **[保存]** を選択してこの操作を保存します。

![add-rrs-operation-response](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>BES 操作の追加
RRS 操作の追加の場合とよく似ているため、BES 操作のスクリーン ショットは用意されていません。

### <a name="submit-but-not-start-a-batch-execution-job"></a>バッチ実行ジョブの送信 (開始はしない)
**[操作を追加]** をクリックして、AzureML BES 操作を API に追加します。 **[HTTP 動詞]** に **[POST]** を選択します。 **[URL template]** に「**/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}**」と入力します。 **[表示名]** に「**BES Submit**」と入力します。 左側の **[応答]** > **[追加]** をクリックし、**[200 OK]** を選択します。 **[保存]** を選択してこの操作を保存します。

### <a name="start-a-batch-execution-job"></a>バッチ実行ジョブを送信する
**[操作を追加]** をクリックして、AzureML BES 操作を API に追加します。 **[HTTP 動詞]** に **[POST]** を選択します。 **[URL template]** に「**/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}**」と入力します。 **[表示名]** に「**BES Start**」と入力します。 左側の **[応答]** > **[追加]** をクリックし、**[200 OK]** を選択します。 **[保存]** を選択してこの操作を保存します。

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>バッチ実行ジョブの状態または結果を取得する
**[操作を追加]** をクリックして、AzureML BES 操作を API に追加します。 **[HTTP 動詞]** に **[GET]** を選択します。 **[URL template]** に「**/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}**」と入力します。 **[表示名]** に「**BES Status**」と入力します。 左側の **[応答]** > **[追加]** をクリックし、**[200 OK]** を選択します。 **[保存]** を選択してこの操作を保存します。

### <a name="delete-a-batch-execution-job"></a>バッチ実行ジョブの削除
**[操作を追加]** をクリックして、AzureML BES 操作を API に追加します。 **[HTTP 動詞]** に **[削除]** を選択します。 **[URL template]** に「**/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}**」と入力します。 **[表示名]** に **[BES Delete]** と入力します。 左側の **[応答]** > **[追加]** をクリックし、**[200 OK]** を選択します。 **[保存]** を選択してこの操作を保存します。

## <a name="call-an-operation-from-the-developer-portal"></a>開発者ポータルから操作を呼び出す
開発者ポータルには、API の操作を見てテストするための便利が環境が用意されており、操作を直接呼び出すことができます。 このガイドの手順では、**AzureML Demo API** に追加された **RRS Execute** メソッドを呼び出します。 クラシック ポータルの右上のメニューから **[開発者ポータル]** をクリックします。

![developer-portal](./media/machine-learning-manage-web-service-endpoints-using-api-management/developer-portal.png)

上部のメニューで **[API]** をクリックし、**[AzureML Demo API]** をクリックして、利用できる操作を表示します。

![demoazureml-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/demoazureml-api.png)

操作の **[RRS Execute]** を選択します。 **[試用版]**をクリックします。

![try-it](./media/machine-learning-manage-web-service-endpoints-using-api-management/try-it.png)

要求パラメーターで、**[apiversion]** に「**workspace**」、「**service**」、「**2.0**」と入力し、**[詳細]** に「**true**」と入力します。 AzureML Web サービスのダッシュボードに**ワークスペース**と**サービス**が表示されます (付録 A の「**Web サービスをテストする**」をご覧ください)。

要求ヘッダーで、**[ヘッダーの追加]** をクリックして「**Content-Type**」と「**application/json**」を入力し、**[ヘッダーの追加]** をクリックして「**Authorization**」と「**Bearer <YOUR AZUREML SERVICE API-KEY>**」を入力します。 AzureML Web サービスのダッシュボードに **API キー**が表示されます (付録 A の「**Web サービスをテストする**」をご覧ください)。

要求本文に「**{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}**」と入力します。

![azureml-demo-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

**[Send]**をクリックします。

![[Send]](./media/machine-learning-manage-web-service-endpoints-using-api-management/send.png)

操作を呼び出すと、バックエンド サービスの**要求された URL**、**応答のステータス**、**応答ヘッダー**、**応答内容**が開発者ポータルに表示されます。

![response-status](./media/machine-learning-manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>付録 A - シンプルな AzureML Web サービスを作成しテストする
### <a name="creating-the-experiment"></a>実験の作成
シンプルな AzureML 実験を作成し、Web サービスとしてデプロイする手順を次に示します。 Web サービスは、任意のテキストの列を入力として取得し、整数として表される機能のセットを返します。 For example:

| テキスト | ハッシュされたテキスト |
| --- | --- |
| This is a good day |1 1 2 2 0 2 0 1 |

最初に、任意のブラウザーを使用して、 [https://studio.azureml.net/](https://studio.azureml.net/) に移動し、資格情報を入力してログインします。 次に、新しい空白の実験を作成します。

![search-experiment-templates](./media/machine-learning-manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

名前を **SimpleFeatureHashingExperiment**に変更します。 **[保存されたデータセット]** を展開し、**[Amazon の書評]** を実験にドラッグします。

![simple-feature-hashing-experiment](./media/machine-learning-manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

**[データ操作]** と **[操作]** を展開し、**[データセット内の列の選択]** を実験にドラッグします。 **[Amazon の書評]** を **[データセット内の列の選択]** に接続します。

![select-columns](./media/machine-learning-manage-web-service-endpoints-using-api-management/project-columns.png)

**[データセット内の列の選択]** をクリックし、次に **[列セレクターの起動]** をクリックし、**Col2** を選択します。 チェック マークをクリックして、これらの変更を適用します。

![select-columns](./media/machine-learning-manage-web-service-endpoints-using-api-management/select-columns.png)

**[テキスト分析]** を展開し、**[特徴ハッシュ]** を実験にドラッグします。 **[データセット内の列の選択]** を **[特徴ハッシュ]** に接続します。

![connect-project-columns](./media/machine-learning-manage-web-service-endpoints-using-api-management/connect-project-columns.png)

**Hashing bitsize** に「**3**」を入力します。 これにより、8 (23) 列が作成されます。

![hashing-bitsize](./media/machine-learning-manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

この時点で、 **[実行]** をクリックして実験をテストできます。

![[実行]](./media/machine-learning-manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Web サービスの作成
Web サービスを作成します。 **[Web サービス]** を展開し、**[入力]** を実験にドラッグします。 **[入力]** を **[特徴ハッシュ]** に接続します。 **[出力]** を実験にドラッグします。 **[出力]** を **[特徴ハッシュ]** に接続します。

![output-to-feature-hashing](./media/machine-learning-manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

**[Web サービスの発行]**をクリックします。

![publish-web-service](./media/machine-learning-manage-web-service-endpoints-using-api-management/publish-web-service.png)

**[はい]** をクリックして実験を発行します。

![yes-to-publish](./media/machine-learning-manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>Web サービスをテストする
AzureML web サービスは、RSS (要求/応答サービス) と BES (バッチ実行サービス) のエンドポイントで構成されます。 RSS は、同期の実行用です。 BES は、同期ジョブの実行用です。 次のサンプルの Python ソースを使用して、Web サービスをテストするには、Azure SDK for Python をダウンロードして、インストールする必要があります (「 [Python をインストールする方法](../python-how-to-install.md)」をご覧ください)。

次のサンプルのソースには、実験の**workspace**、**service**、**api_key** が必要です。 Web サービス ダッシュボードの実験の **[要求/応答]** か **[バッチ実行]** をクリックするとワークスペースとサービスが表示されます。

![find-workspace-and-service](./media/machine-learning-manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Web サービス ダッシュボードの実験をクリックすると **api_key** が表示されます。

![find-api-key](./media/machine-learning-manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>RRS エンドポイントのテスト
##### <a name="test-button"></a>テスト ボタン
RRS エンドポイントを簡単にテストするには、Web サービス ダッシュ ボードで **[テスト]** をクリックします。

![test](./media/machine-learning-manage-web-service-endpoints-using-api-management/test.png)

**[col2]** に「**This is a good day**」と入力します。 チェック マークをクリックします。

![enter-data](./media/machine-learning-manage-web-service-endpoints-using-api-management/enter-data.png)

次のような結果が表示されます。

![sample-output](./media/machine-learning-manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>サンプル コード
クライアント コードから RRS テストする方法もあります。 ダッシュボードで **[要求/応答]** をクリックし、最下部までスクロールすると、C#、Python、R のサンプル コードが表示されます。また、要求 URI、ヘッダー、本文を含む RRS 要求の構文も表示されます。

このガイドでは、Python の機能例について説明します。 実験の **workspace**、**service**、**api_key** を使用して変更する必要があります。

    import urllib2
    import json
    workspace = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE WORKSPACE ID>"
    service = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE SERVICE ID>"
    api_key = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE API KEY>"
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

#### <a name="test-bes-endpoint"></a>BES エンドポイントのテスト
ダッシュボードの **[バッチ実行]** をクリックして、最下部までスクロールします。 C# の場合、Python、R のサンプル コードが表示されます。また、ジョブを送信する、ジョブを送信する、ジョブを開始する、ジョブのステータスか結果を取得する、ジョブを削除するなどの BES 要求の構文も表示されます。

このガイドでは、Python の機能例について説明します。 実験の **workspace**、**service**、**api_key** を使用して変更する必要があります。 また、**ストレージ アカウント名**、**ストレージ アカウント キー**、**ストレージ コンテナー名**を変更するがあります。 最後に、**[入力ファイル]** の場所と **[出力ファイル]** の場所を変更する必要があります。

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
    with open(output_file, "w+") as f:
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
        # If you are using Python 3+, replace urllib2 with urllib.request in the follwing code
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



<!--HONumber=Nov16_HO3-->


