---
title: REST API を使用して Azure Stream Analytics on IoT Edge の CI/CD を行う
description: REST API を使用して Azure Stream Analytics のための継続的インテグレーションと継続的なデプロイのパイプラインを実装する方法について説明します。
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 3c3f776ad0996fa0b7422f0fca2d899a35e853d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016135"
---
# <a name="implement-cicd-for-stream-analytics-on-iot-edge-using-apis"></a>API を使用して IoT Edge 上に Stream Analytics 向けの CI/CD を実装する

REST API を使用して Azure Stream Analytics ジョブのための継続的インテグレーションと継続的なデプロイを有効にすることができます。 この記事では、使用する API とその使用方法の例を示します。 REST API は、Azure Cloud Shell ではサポートされていません。

## <a name="call-apis-from-different-environments"></a>異なる環境から API を呼び出す

REST API は、Linux と Windows の両方から呼び出すことができます。 次のコマンドでは、API 呼び出しの正しい構文を示します。 API の具体的な使用方法は、この記事の後のセクションで説明します。

### <a name="linux"></a>Linux

Linux では、`Curl` または `Wget` コマンドを使用できます。

```bash
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" { <url> }   
```

```bash
wget -q -O- --{ <method> } -data="<request body>" --header=Content-Type:application/json --auth-no-challenge --http-user="<Admin>" --http-password="<password>" <url>
```
 
### <a name="windows"></a>Windows

Windows では、Powershell を使用します。 

```powershell 
$user = "<username>" 
$pass = "<password>" 
$encodedCreds = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $user,$pass))) 
$basicAuthValue = "Basic $encodedCreds" 
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("Content-Type", 'application/json') 
$headers.Add("Authorization", $basicAuthValue) 
$content = "<request body>" 
$response = Invoke-RestMethod <url> -Method <method> -Body $content -Headers $Headers 
echo $response 
```
 
## <a name="create-an-asa-job-on-edge"></a>Edge で ASA ジョブを作成する 
 
Stream Analytics ジョブを作成するには、Stream Analytics API を使用して PUT メソッドを呼び出します。

|Method|要求 URL|
|------|-----------|
|PUT|`https://management.azure.com/subscriptions/{\**subscription-id**}/resourcegroups/{**resource-group-name**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**job-name**}?api-version=2017-04-01-preview`|
 
**curl** を使用するコマンドの例:

```curl
curl -u { <username:password> } -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}?api-version=2017-04-01-preview  
``` 
 
JSON での要求本文の例:

```json
{ 
  "location": "West US", 
  "tags": { "key": "value", "ms-suppressjobstatusmetrics": "true" }, 
  "sku": {  
      "name": "Standard" 
    }, 
  "properties": { 
    "sku": {  
      "name": "standard" 
    }, 
       "eventsLateArrivalMaxDelayInSeconds": 1, 
       "jobType": "edge", 
    "inputs": [ 
      { 
        "name": "{inputname}", 
        "properties": { 
         "type": "stream", 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ], 
    "transformation": { 
      "name": "{queryName}", 
      "properties": { 
        "query": "{query}" 
      } 
    }, 
    "package": { 
      "storageAccount" : { 
        "accountName": "{blobstorageaccountname}", 
        "accountKey": "{blobstorageaccountkey}" 
      }, 
      "container": "{blobcontaine}]" 
    }, 
    "outputs": [ 
      { 
        "name": "{outputname}", 
        "properties": { 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ] 
  } 
} 
```
 
詳しくは、[API のドキュメント](/rest/api/streamanalytics/)をご覧ください。  
 
## <a name="publish-edge-package"></a>Edge パッケージを発行する 
 
IoT Edge で Stream Analytics ジョブを発行するには、Edge Package Publish API を使用して POST メソッドを呼び出します。

|Method|要求 URL|
|------|-----------|
|POST|`https://management.azure.com/subscriptions/{\**subscriptionid**}/resourceGroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**jobname**}/publishedgepackage?api-version=2017-04-01-preview`|

ジョブが正常に発行されるまで、この非同期操作からは状態 202 が返されます。 場所応答ヘッダーには、プロセスの状態を取得するために使用する URI が含まれます。 プロセスが実行されている間、場所ヘッダーの URI を呼び出すと 202 の状態が返されます。 プロセスが完了すると、場所ヘッダーの URI の呼び出しからは状態 200 が返されます。 

**curl** を使用した Edge パッケージ発行呼び出しの例: 

```bash
curl -d -X POST https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}/publishedgepackage?api-version=2017-04-01-preview
```
 
POST 呼び出しを行った後は、本文が空の応答を予期する必要があります。 応答の HEAD にある URL を検索し、後で使用するために記録します。
 
応答の HEAD からの URL の例:

```
https://management.azure.com/subscriptions/{**subscriptionid**}/resourcegroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/StreamingJobs/{**resourcename**}/OperationResults/023a4d68-ffaf-4e16-8414-cb6f2e14fe23?api-version=2017-04-01-preview 
```
1 ～ 2 分間待ってから、次のコマンドを実行して、応答の HEAD で見つかった URL を使用して API 呼び出しを行います。 200 の応答が得られない場合は、コマンドを再試行します。
 
**curl** を使用した返された URL での API 呼び出しの例:

```bash
curl -d –X GET https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{resourcename}/publishedgepackage?api-version=2017-04-01-preview 
```

応答には、Edge のデプロイ スクリプトに追加する必要がある情報が含まれます。 次の例では、収集する必要がある情報と、配置マニフェストでそれを追加する場所を示します。
 
正常に発行した後の応答本文のサンプル:

```json
{ 
  edgePackageUrl : null 
  error : null 
  manifest : "{"supportedPlatforms":[{"os":"linux","arch":"amd64","features":[]},{"os":"linux","arch":"arm","features":[]},{"os":"windows","arch":"amd64","features":[]}],"schemaVersion":"2","name":"{jobname}","version":"1.0.0.0","type":"docker","settings":{"image":"{imageurl}","createOptions":null},"endpoints":{"inputs":["\],"outputs":["{outputnames}"]},"twin":{"contentType":"assignments","content":{"properties.desired":{"ASAJobInfo":"{asajobsasurl}","ASAJobResourceId":"{asajobresourceid}","ASAJobEtag":"{etag}","PublishTimeStamp":"{publishtimestamp}"}}}}" 
  status : "Succeeded" 
} 
```

配置マニフェストの例: 

```json
{ 
  "modulesContent": { 
    "$edgeAgent": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "runtime": { 
          "type": "docker", 
          "settings": { 
            "minDockerVersion": "v1.25", 
            "loggingOptions": "", 
            "registryCredentials": {} 
          } 
        }, 
        "systemModules": { 
          "edgeAgent": { 
            "type": "docker", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0", 
              "createOptions": "{}" 
            } 
          }, 
          "edgeHub": { 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0", 
              "createOptions": "{}" 
            } 
          } 
        }, 
        "modules": { 
          "<asajobname>": { 
            "version": "1.0", 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "<settings.image>", 
              "createOptions": "<settings.createOptions>" 
            } 
            "version": "<version>", 
             "env": { 
              "PlanId": { 
               "value": "stream-analytics-on-iot-edge" 
          } 
        } 
      } 
    }, 
    "$edgeHub": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "routes": { 
            "route": "FROM /* INTO $upstream" 
        }, 
        "storeAndForwardConfiguration": { 
          "timeToLiveSecs": 7200 
        } 
      } 
    }, 
    "<asajobname>": { 
      "properties.desired": {<twin.content.properties.desired>} 
    } 
  } 
} 
```

配置マニフェストを構成した後は、デプロイに関する「[Azure CLI を使用して Azure IoT Edge モジュールをデプロイする](../iot-edge/how-to-deploy-modules-cli.md)」をご覧ください。


## <a name="next-steps"></a>次のステップ 
 
* [Azure Stream Analytics on IoT Edge](stream-analytics-edge.md)
* [ASA on IoT Edge チュートリアル](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Visual Studio Tools を使用して Stream Analytics Edge ジョブを作成する](stream-analytics-tools-for-visual-studio-edge-jobs.md)