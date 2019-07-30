---
title: チュートリアル - Node.js 向け Azure Batch クライアント ライブラリを使用する | Microsoft Docs
description: Azure Batch の基本的な概念について説明し、Node.js でシンプルなソリューションを作成します。
services: batch
author: shwetams
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: nodejs
ms.topic: conceptual
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: shg
ms.openlocfilehash: a6895773e0109aa0fb643e4fadf8a31ac5b1a33a
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323401"
---
# <a name="get-started-with-batch-sdk-for-nodejs"></a>Node.js 向け Batch SDK の概要

この記事では、[Azure Batch Node.js SDK](/javascript/api/overview/azure/batch) を使って Node.js で Batch クライアントを作成するうえで基本となる事項を説明します。 説明にあたっては、段階的なアプローチを採用しています。具体的には、バッチ アプリケーションを使用するシナリオを紹介したうえで、Node.js クライアントを使ってアプリケーションをセットアップしていきます。  

## <a name="prerequisites"></a>前提条件
この記事は、Node.js の実務知識を持ち、Linux に精通している読者を想定しています。 また、Batch と Storage の 2 つのサービスを作成できるアクセス権を備えた Azure アカウントも必要になります。

この記事を読む前に、[Azure Batch の技術的概要](batch-technical-overview.md)に関する記事を読むことをお勧めします。

## <a name="the-tutorial-scenario"></a>このチュートリアルのシナリオ
まずは、バッチ ワークフローのシナリオを説明しましょう。 このチュートリアルでは、Python で書かれたシンプルなスクリプトを使用します。このスクリプトは、Azure Blob Storage コンテナーから csv ファイルをすべてダウンロードし、JSON に変換するものです。 今回は複数のストレージ アカウントのコンテナーを同時に処理するために、このスクリプトを Azure Batch ジョブとしてデプロイします。

## <a name="azure-batch-architecture"></a>Azure Batch のアーキテクチャ
以下の図は、上で説明した Python スクリプトを Azure Batch と Node.js クライアントを使って拡張する方法を示しています。

![Azure Batch のシナリオ](./media/batch-nodejs-get-started/BatchScenario.png)

Node.js クライアントは、準備タスク (後述) と、ストレージ アカウント内のコンテナーの数に応じた一連のタスクを備えたバッチ ジョブをデプロイします。 スクリプトは、GitHub リポジトリからダウンロードできます。

* [Node.js クライアント](https://github.com/Azure/azure-batch-samples/blob/master/Node.js/GettingStarted/nodejs_batch_client_sample.js)
* [準備タスクのシェル スクリプト](https://github.com/Azure/azure-batch-samples/blob/master/Node.js/GettingStarted/startup_prereq.sh)
* [csv から JSON への変換処理スクリプト (Python)](https://github.com/Azure/azure-batch-samples/blob/master/Node.js/GettingStarted/processcsv.py)

> [!TIP]
> 上記リンクから入手できる Node.js クライアントには、Azure Function App としてデプロイするための具体的なコードが含まれていません。 Function App を作成するための手順については、以下のリンクを参照してください。
> - [Function App の作成](../azure-functions/functions-create-first-azure-function.md)
> - [タイマー トリガー関数の作成](../azure-functions/functions-bindings-timer.md)
>
>

## <a name="build-the-application"></a>アプリケーションのビルド

それでは、Node.js クライアントを作成するプロセスを 1 つずつ実践していきましょう。

### <a name="step-1-install-azure-batch-sdk"></a>手順 1:Azure Batch SDK をインストールする

Node.js 向け Azure Batch SDK をインストールするには、npm install コマンドを使用します。

`npm install azure-batch`

このコマンドを実行すると、azure-batch ノード SDK の最新バージョンがインストールされます。

>[!Tip]
> Azure Function App では、Azure 関数の [設定] タブにある "Kudu コンソール" から npm install コマンドを実行できます。 この場合、Node.js 向けの Azure Batch SDK をインストールします。
>
>

### <a name="step-2-create-an-azure-batch-account"></a>手順 2:Azure Batch アカウントを作成する

[Azure Portal](batch-account-create-portal.md) またはコマンド ライン ([Powershell](batch-powershell-cmdlets-get-started.md) /[Azure CLI](/cli/azure)) から作成できます。

以下で紹介するのは、Azure CLI からアカウントを 1 つ作成するためのコマンドです。

リソース グループを作成します。Batch アカウントの作成先とするリソース グループが既にある場合には、この手順は省略してください。

`az group create -n "<resource-group-name>" -l "<location>"`

続いて、Azure Batch アカウントを作成します。

`az batch account create -l "<location>"  -g "<resource-group-name>" -n "<batch-account-name>"`

各 Batch アカウントには、対応するアクセス キーが存在します。 Azure Batch アカウントに追加でリソースを作成するには、このアクセス キーが必要になります。 運用環境の場合には、アクセス キーの格納場所として Azure Key Vault を使用すると良いでしょう。 そうすれば、アプリケーション用にサービス プリンシパルを作成できます。 アプリケーションではこのサービス プリンシパルを使用して、キー コンテナーからキーにアクセスするための OAuth トークンを作成します。

`az batch account keys list -g "<resource-group-name>" -n "<batch-account-name>"`

以降の手順で使用するため、キーをコピーして保存します。

### <a name="step-3-create-an-azure-batch-service-client"></a>手順 3:Azure Batch サービス クライアントを作成する
以下のコード スニペットでは、azure-batch Node.js モジュールをインポートしたうえで、Batch サービス クライアントを作成します。 最初に、先ほどの手順でコピーしておいた Batch アカウントのキーを使って SharedKeyCredentials オブジェクトを作成する必要があります。

```nodejs
// Initializing Azure Batch variables

var batch = require('azure-batch');

var accountName = '<azure-batch-account-name>';

var accountKey = '<account-key-downloaded>';

var accountUrl = '<account-url>'

// Create Batch credentials object using account name and account key

var credentials = new batch.SharedKeyCredentials(accountName,accountKey);

// Create Batch service client

var batch_client = new batch.ServiceClient(credentials,accountUrl);

```

Azure Batch の URI は、Azure Portal の [概要] タブにあります。 これは、次のような形式になっています。

`https://accountname.location.batch.azure.com`

スクリーンショットを参照してください。

![Azure Batch の URI](./media/batch-nodejs-get-started/azurebatchuri.png)



### <a name="step-4-create-an-azure-batch-pool"></a>手順 4:Azure Batch プールを作成する
Azure Batch プールは、複数の VM (別名 Batch ノード) で構成されています。 Azure Batch サービスでは、そのノードにタスクをデプロイして管理します。 プールには以下の構成パラメーターを定義できます。

* 仮想マシン イメージの種類
* 仮想マシン ノードのサイズ
* 仮想マシン ノードの数

> [!Tip]
> 仮想マシン ノードのサイズと数は、並行して実行するタスクの数のほか、タスク自体の内容に大きく左右されます。 このため、テストを実施して最適な数とサイズを確認することをお勧めします。
>
>

以下のコード スニペットは、構成パラメーター オブジェクトを作成するものです。

```nodejs
// Creating Image reference configuration for Ubuntu Linux VM
var imgRef = {publisher:"Canonical",offer:"UbuntuServer",sku:"14.04.2-LTS",version:"latest"}

// Creating the VM configuration object with the SKUID
var vmconfig = {imageReference:imgRef,nodeAgentSKUId:"batch.node.ubuntu 14.04"}

// Setting the VM size to Standard F4
var vmSize = "STANDARD_F4"

//Setting number of VMs in the pool to 4
var numVMs = 4
```

> [!Tip]
> Azure Batch で利用できる Linux VM イメージの一覧とその SKU ID については、「[仮想マシン イメージの一覧](batch-linux-nodes.md#list-of-virtual-machine-images)」を参照してください。
>
>

プールの構成を定義したら、Azure Batch プールを作成できます。 Batch プール コマンドを実行すると、Azure 仮想マシン ノードが作成され、ノードで実行対象のタスクを受信する準備が整います。 各プールには、後の手順で参照するための一意の ID が必要です。

以下のコード スニペットでは、Azure Batch プールが 1 つ作成されます。

```nodejs
// Create a unique Azure Batch pool ID
var poolid = "pool" + customerDetails.customerid;
var poolConfig = {id:poolid, displayName:poolid,vmSize:vmSize,virtualMachineConfiguration:vmconfig,targetDedicatedComputeNodes:numVms,enableAutoScale:false };
// Creating the Pool for the specific customer
var pool = batch_client.pool.add(poolConfig,function(error,result){
    if(error!=null){console.log(error.response)};
});
```

プールを作成したら、その状態を確認できます。プールにジョブを送信する前に、状態が "アクティブ" であることを確認しましょう。

```nodejs
var cloudPool = batch_client.pool.get(poolid,function(error,result,request,response){
        if(error == null)
        {

            if(result.state == "active")
            {
                console.log("Pool is active");
            }
        }
        else
        {
            if(error.statusCode==404)
            {
                console.log("Pool not found yet returned 404...");    

            }
            else
            {
                console.log("Error occurred while retrieving pool data");
            }
        }
        });
```

上の pool.get 関数によって返される結果オブジェクトのサンプルは、以下のとおりです。

```
{ id: 'processcsv_201721152',
  displayName: 'processcsv_201721152',
  url: 'https://<batch-account-name>.centralus.batch.azure.com/pools/processcsv_201721152',
  eTag: '<eTag>',
  lastModified: 2017-03-27T10:28:02.398Z,
  creationTime: 2017-03-27T10:28:02.398Z,
  state: 'active',
  stateTransitionTime: 2017-03-27T10:28:02.398Z,
  allocationState: 'resizing',
  allocationStateTransitionTime: 2017-03-27T10:28:02.398Z,
  vmSize: 'standard_a1',
  virtualMachineConfiguration:
   { imageReference:
      { publisher: 'Canonical',
        offer: 'UbuntuServer',
        sku: '14.04.2-LTS',
        version: 'latest' },
     nodeAgentSKUId: 'batch.node.ubuntu 14.04' },
  resizeTimeout:
   { [Number: 900000]
     _milliseconds: 900000,
     _days: 0,
     _months: 0,
     _data:
      { milliseconds: 0,
        seconds: 0,
        minutes: 15,
        hours: 0,
        days: 0,
        months: 0,
        years: 0 },
     _locale:
      Locale {
        _calendar: [Object],
        _longDateFormat: [Object],
        _invalidDate: 'Invalid date',
        ordinal: [Function: ordinal],
        _ordinalParse: /\d{1,2}(th|st|nd|rd)/,
        _relativeTime: [Object],
        _months: [Object],
        _monthsShort: [Object],
        _week: [Object],
        _weekdays: [Object],
        _weekdaysMin: [Object],
        _weekdaysShort: [Object],
        _meridiemParse: /[ap]\.?m?\.?/i,
        _abbr: 'en',
        _config: [Object],
        _ordinalParseLenient: /\d{1,2}(th|st|nd|rd)|\d{1,2}/ } },
  currentDedicated: 0,
  targetDedicated: 4,
  enableAutoScale: false,
  enableInterNodeCommunication: false,
  maxTasksPerNode: 1,
  taskSchedulingPolicy: { nodeFillType: 'Spread' } }
```


### <a name="step-4-submit-an-azure-batch-job"></a>手順 4:Azure Batch ジョブを送信する
Azure Batch ジョブは、よく似たタスクを論理的なグループにまとめたものです。 今回のシナリオの場合、"csv を処理して JSON に変換する" ためのジョブです。 このジョブでは、それぞれのタスクが各 Azure ストレージ コンテナーに存在する csv ファイルを処理します。

タスクはいずれも並行して実行され、多数のノードにデプロイされます。Azure Batch サービスは、そのオーケストレーションを担当します。

> [!Tip]
> 1 つのノードで同時に実行できるタスクの最大数を指定するには、[maxTasksPerNode](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add) プロパティを使用します。
>
>

#### <a name="preparation-task"></a>準備タスク

作成された VM ノードは、空の Ubuntu ノードです。 多くの場合、前提条件としていくつかのプログラムをインストールする必要があります。
Linux ノードの場合には通常、実際のタスクの実行前に前提条件となるプログラムをインストールするシェル スクリプトを用意します。 もっとも、そのようなスクリプトはプログラミング可能な実行可能ファイルであれば何でもかまいません。
この例で使用する[シェル スクリプト](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/startup_prereq.sh)は、Python pip と Python 向けの Azure Storage SDK をインストールします。

このスクリプトを Azure ストレージ アカウントにアップロードすると、スクリプトにアクセスするための SAS の URI を生成できます。 この処理は、Azure Storage Node.js SDK を使って自動化することもできます。

> [!Tip]
> ジョブの準備タスクは、特定のタスクの実行が必要な VM ノードでのみ実行されます。 ノードで実行するタスクに関係なく全ノードに前提条件をインストールする場合には、プールを追加するときに [startTask](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add) プロパティを使用します。 以下で紹介する準備タスクの定義は、参考として使用してください。
>
>

準備タスクは、Azure Batch ジョブの送信中に指定します。 準備タスクの構成パラメーターは以下のとおりです。

* **ID**:準備タスクの一意の識別子
* **commandLine**:タスクの実行可能ファイルを実行するためのコマンド ライン
* **resourceFiles**:このタスクを実行するためにダウンロードが必要なファイルの詳細を指定したオブジェクトの配列。  オプションは以下のとおりです。
    - blobSource:ファイルの SAS の URI
    - filePath:ファイルのダウンロードと保存のためのローカル パス
    - fileMode:0770 を既定値とする 8 進数形式の値 (Linux ノードのみ該当)
* **waitForSuccess**:true に設定すると、準備タスクが失敗した場合にタスクが実行されなくなります
* **runElevated**:タスクの実行に昇格された特権が必要な場合には true に設定します。

以下のコード スニペットでは、準備タスク スクリプトの構成サンプルを示しています。

```nodejs
var job_prep_task_config = {id:"installprereq",commandLine:"sudo sh startup_prereq.sh > startup.log",resourceFiles:[{'blobSource':'Blob SAS URI','filePath':'startup_prereq.sh'}],waitForSuccess:true,runElevated:true}
```

タスクを実行するための前提条件が一切インストールされていない場合には、準備タスクを省略できます。 以下のコードは、表示名が "process csv files" のジョブを作成するものです。

 ```nodejs
 // Setting up Batch pool configuration
 var pool_config = {poolId:poolid}
 // Setting up Job configuration along with preparation task
 var jobId = "processcsvjob"
 var job_config = {id:jobId,displayName:"process csv files",jobPreparationTask:job_prep_task_config,poolInfo:pool_config}
 // Adding Azure batch job to the pool
 var job = batch_client.job.add(job_config,function(error,result){
     if(error != null)
     {
         console.log("Error submitting job : " + error.response);
     }});
```


### <a name="step-5-submit-azure-batch-tasks-for-a-job"></a>手順 5:ジョブの Azure Batch タスクを送信する

csv を処理するジョブが作成できたので、このジョブのためのタスクを作成しましょう。 コンテナーが 4 つあるとすると、コンテナーごとに 1 つずつ、計 4 つのタスクを作成する必要があります。

[Python スクリプト](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/processcsv.py)を見ると、次の 2 つのパラメーターを受け取ることがわかります。

* コンテナー名:ファイルをダウンロードするストレージ コンテナー
* パターン:ファイル名パターンの省略可能なパラメーター

以下のコードは、コンテナーが "con1"、"con2"、"con3"、"con4" の 4 つであると仮定して、先ほど作成した Azure Batch ジョブ "process csv" にタスクを送信する方法を示しています。

```nodejs
// storing container names in an array
var container_list = ["con1","con2","con3","con4"]
    container_list.forEach(function(val,index){           

           var container_name = val;
           var taskID = container_name + "_process";
           var task_config = {id:taskID,displayName:'process csv in ' + container_name,commandLine:'python processcsv.py --container ' + container_name,resourceFiles:[{'blobSource':'<blob SAS URI>','filePath':'processcsv.py'}]}
           var task = batch_client.task.add(poolid,task_config,function(error,result){
                if(error != null)
                {
                    console.log(error.response);     
                }
                else
                {
                    console.log("Task for container : " + container_name + "submitted successfully");
                }



           });

    });
```

このコードによって、プールにタスクが複数追加されます。 各タスクは、作成された VM のプール内のノードで実行されます。 タスクの数がプールまたは maxTasksPerNode プロパティの VM の数を上回った場合には、タスクはノードが利用可能になるまで待機します。 このオーケストレーションは、Azure Batch によって自動的に処理されます。

ポータルでは、タスクとジョブの状態の詳細を確認できます。 このほか、Azure Node SDK の list 関数や get 関数も使用できます。 詳細については、ドキュメント ([リンク](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Job.html)) を参照してください。

## <a name="next-steps"></a>次の手順

- このサービスを初めて扱う場合は、 [Azure Batch 機能の概要](batch-api-basics.md) に関する記事を確認することをお勧めします。
- Batch API の詳細については、[Batch Node.js のリファレンス](/javascript/api/overview/azure/batch)を参照してください。

