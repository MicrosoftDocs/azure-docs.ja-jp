---
title: 気象パートナーから気象データを取得する
description: この記事では、パートナーから気象データを取得する方法について説明します。
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: bb28c517e353af6b8c1ee0cad788ff41b971918c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96460871"
---
# <a name="get-weather-data-from-weather-partners"></a>気象パートナーから気象データを取得する

Azure FarmBeats を利用すると、Docker ベースのコネクタ フレームワークを使用して、気象データ プロバイダーから気象データを取り込むことができます。 気象データ プロバイダーでは、このフレームワークを使用して、FarmBeats と統合できる Docker を実装します。 現在、次の気象データ プロバイダーがサポートされています。

  ![FarmBeats パートナー](./media/get-sensor-data-from-sensor-partner/dtn-logo.png)
  
   [DTN](https://www.dtn.com/dtn-content-integration/)

気象データを使用して、実用的な分析情報を生成し、FarmBeats で AI または ML のモデルを構築することができます。

## <a name="before-you-start"></a>開始する前に

気象データを取得するには、確実に [FarmBeats をインストール](./install-azure-farmbeats.md)してください。 気象の統合は、バージョン 1.2.11 以降でサポートされています。 

## <a name="enable-weather-integration-with-farmbeats"></a>FarmBeats との気象の統合を有効にする

FarmBeats Datahub で気象データの取得を開始するには、次の手順を行います。

1. FarmBeats Datahub Swagger `https://farmbeatswebsite-api.azurewebsites.net/swagger` にアクセスします。

2. /Partner API にアクセスして、POST 要求を行います。 次の入力ペイロードを使用します。

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name",
       "imageTag" : "<docker image tag, default:latest>",
       "azureBatchVMDetails": {  
         "batchVMSKU" : "<VM SKU. Default is standard_d2_v2>",  
         "dedicatedComputerNodes" : 1,
         "nodeAgentSKUID": "<Node SKU. Default is batch.node.ubuntu 18.04>"  
       }
     },
     "partnerCredentials": {  
       "key1": "value1",  
       "key2": "value2"  
     },  
     "partnerType": "Weather",  
     "name": "<Name of the partner>",  
     "description": "<Description>",
     "properties": {  }  
   }  
   ```

   たとえば、DTN から気象データを取得するには、次のペイロードを使用します。 好みに応じて、名前と説明を変更できます。

   > [!NOTE]
   > 次の手順では、API キーが必要です。 DTN サブスクリプションのキーを取得するには、DTN にお問い合わせください。

   ```json
   {
 
     "dockerDetails": {
       "imageName": "dtnweather/dtn-farm-beats",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerCredentials": {
      "apikey": "<API key from DTN>"
      },
     "partnerType": "Weather",
     "name": "dtn-weather",
     "description": "DTN registered as a Weather Partner in FarmBeats"
   }  
   ```

   > [!NOTE]
   > パートナー オブジェクトの詳細については、この記事の「[付録](get-weather-data-from-weather-partner.md#appendix)」を参照してください。

   前述の手順により、お客様の FarmBeats 環境で Docker を実行できるようにリソースがプロビジョニングされます。  

   これらのリソースをプロビジョニングするには、約 10 分から 15 分かかります。

3. 前述の手順で作成した /Partner オブジェクトの状態を確認します。 状態を確認するには、/Partner API で GET 要求を行い、パートナー オブジェクトの状態を確認します。 FarmBeats でパートナーが正常にプロビジョニングされると、状態が **Active** に設定されます。

4. /JobType API で GET 要求を行います。 前述のパートナー追加プロセスで作成した気象ジョブを確認します。 気象ジョブでは、**pipelineName** フィールドの形式は、**partner-name_partner-type_job-name** です。

      以上で、FarmBeats インスタンスにアクティブなデータ パートナーが含まれます。 ジョブを実行して、特定の場所 (緯度と経度) およびデータ範囲の気象データを要求できます。 ジョブの種類には、気象ジョブの実行に必要なパラメーターの詳細が含まれます。

      たとえば、DTN の場合、次のジョブの種類が作成されます。
   
      - **get_dtn_daily_observations**: ある場所と期間の毎日の観測結果を取得します。
      - **get_dtn_daily_forecasts**: ある場所と期間の毎日の予測を取得します。
      - **get_dtn_hourly_observations**: ある場所と期間の 1 時間単位の観測結果を取得します。
      - **get_dtn_hourly_forecasts**: ある場所と期間の 1 時間単位の予測を取得します。

6. ID と、ジョブの種類のパラメーターをメモしておきます。

7. /Jobs API に移動し、/Jobs で POST 要求を行います。 次の入力ペイロードを使用します。

   ```json
    {
         "typeId": "<id of the JobType>",
         "arguments": {
           "additionalProp1": {},
           "additionalProp2": {},
           "additionalProp3": {}
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
       }
   ```

   たとえば、**get_dtn_daily_observations** を実行するには、次のペイロードを使用します。

   ```json
   { 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "days": 5
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. 前述の手順により、パートナー Docker で定義されている気象ジョブが実行され、気象データが FarmBeats に取り込まれます。 ジョブの状態を確認するには、/Jobs で GET 要求を行います。 応答で、**currentState** を見つけます。 完了すると、**currentState** が **Succeeded** に設定されます。

## <a name="query-ingested-weather-data"></a>取り込まれた気象データに対してクエリを実行する

気象ジョブが完了したら、FarmBeats Datahub REST API を使用して、取り込まれた気象データに対してクエリを実行し、モデルや実用的な分析情報を作成できます。

FarmBeats REST API を使用して気象データに対してクエリを実行するには、次の手順を行います。

1. FarmBeats Datahub [Swagger](https://yourdatahub.azurewebsites.net/swagger) で、/WeatherDataLocation API に移動し、GET 要求を行います。 応答には、ジョブの実行で指定された場所 (緯度と経度) に対して作成された /WeatherDataLocation オブジェクトが含まれます。 オブジェクトの **ID** と **weatherDataModelId** をメモしておきます。

2. 先ほどと同様、/WeatherDataModel API で、**weatherDataModelId** に対して GET/{id} 要求を行います。 気象データ モデルには、取り込まれた気象データに関するすべてのメタデータと詳細が示されます。 たとえば、気象データ モデル オブジェクト内の気象メジャーには、サポートされている気象情報とその種類および単位に関する詳細が含まれます。 次に例を示します。

   ```json
   {
       "name": "Temperature <name of the weather measure - this is what we will receive as part of the queried weather data>",
       "dataType": "Double <Data Type - eg. Double, Enum>",
       "type": "AmbientTemperature <Type of measure eg. AmbientTemperature, Wind speed etc.>",
       "unit": "Celsius <Unit of measure eg. Celsius, Percentage etc.>",
       "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation, Sum, Total>",
       "description": "<Description of the measure>"
   }
   ```

   気象データ モデルの GET/{id} 呼び出しからの応答をメモしておきます。

3. Telemetry API に移動して、POST 要求を行います。 次の入力ペイロードを使用します。

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

    応答には、指定した時間範囲で使用できる気象データが示されます。

   ```json
   {
     "timestamps": [
       "2020-XX-XXT07:30:00Z",
       "2020-XX-XXT07:45:00Z"
     ],
     "properties": [
       {
         "values": [
           "<id of the weatherDataLocation>",
           "<id of the weatherDataLocation>"
         ],
         "name": "Id",
         "type": "String"
       },
       {
         "values": [
           29.1,
           30.2
         ],
         "name": "Temperature <name of the WeatherMeasure as defined in the WeatherDataModel object>",
         "type": "Double <Data Type of the value - eg. Double>"
       }
     ]
   }
   ```

前の例の応答には、2 つのタイムスタンプのデータが示されています。 また、メジャー名 (Temperature)、および 2 つのタイムスタンプ内の報告された気象データの値も示されています。 報告された値の種類と単位を解釈するには、関連する気象データ モデルを参照してください。

## <a name="troubleshoot-job-failures"></a>ジョブの失敗のトラブルシューティング

ジョブの失敗をトラブルシューティングするには、[ジョブのログを確認](troubleshoot-azure-farmbeats.md#weather-data-job-failures)します。


## <a name="appendix"></a>付録

|        Partner   |  詳細   |
| ------- | -------             |
|     DockerDetails - imageName         |          Docker イメージの名前。 たとえば、docker.io/mydockerimage (hub.docker.com 内のイメージ) または myazureacr.azurecr.io/mydockerimage (Azure Container Registry 内のイメージ) などです。 レジストリが指定されていない場合、既定値は hub.docker.com です。      |
|          DockerDetails - imageTag             |         Docker イメージのタグ名。 既定値は "latest" です。     |
|  DockerDetails - credentials      |  プライベート Docker にアクセスするための資格情報。 資格情報は、パートナーから提供されます。   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Azure Batch VM の SKU。 詳細については、[使用可能なすべての Linux 仮想マシン](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページをご覧ください。 <BR> <BR> 有効な値は、次のとおりです。'Small'、'ExtraLarge'、'Large'、'A8'、'A9'、'Medium'、'A5'、'A6'、'A7'、'STANDARD_D1'、'STANDARD_D2'、'STANDARD_D3'、'STANDARD_D4'、'STANDARD_D11'、'STANDARD_D12'、'STANDARD_D13'、'STANDARD_D14'、'A10'、'A11'、'STANDARD_D1_V2'、'STANDARD_D2_V2'、'STANDARD_D3_V2'、'STANDARD_D4_V2'、'STANDARD_D11_V2'、'STANDARD_D12_V2'、'STANDARD_D13_V2'、'STANDARD_D14_V2'、'STANDARD_G1'、'STANDARD_G2'、'STANDARD_G3'、'STANDARD_G4'、'STANDARD_G5'、'STANDARD_D5_V2'、'BASIC_A1'、'BASIC_A2'、'BASIC_A3'、'BASIC_A4'、'STANDARD_A1'、'STANDARD_A2'、'STANDARD_A3'、'STANDARD_A4'、'STANDARD_A5'、'STANDARD_A6'、'STANDARD_A7'、'STANDARD_A8'、'STANDARD_A9'、'STANDARD_A10'、'STANDARD_A11'、'STANDARD_D15_V2'、'STANDARD_F1'、'STANDARD_F2'、'STANDARD_F4'、'STANDARD_F8'、'STANDARD_F16'、'STANDARD_NV6'、'STANDARD_NV12'、'STANDARD_NV24'、'STANDARD_NC6'、'STANDARD_NC12'、'STANDARD_NC24'、'STANDARD_NC24r'、'STANDARD_H8'、'STANDARD_H8m'、'STANDARD_H16'、'STANDARD_H16m'、'STANDARD_H16mr'、'STANDARD_H16r'、'STANDARD_A1_V2'、'STANDARD_A2_V2'、'STANDARD_A4_V2'、'STANDARD_A8_V2'、'STANDARD_A2m_V2'、'STANDARD_A4m_V2'、'STANDARD_A8m_V2'、'STANDARD_M64ms'、'STANDARD_M128s'、'STANDARD_D2_V3'。 "*既定値は 'STANDARD_D2_V2' です。* "  |
|    DockerDetails - azureBatchVMDetails - dedicatedComputerNodes   |  バッチ プールあたりの専用コンピューター ノードの数。 既定値は 1 です。 |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    Azure Batch ノード エージェント SKU ID。 現在サポートされているバッチ ノード エージェントは、"batch.node.ubuntu 18.04" のみです。    |
| DockerDetails - partnerCredentials | Docker でのパートナー API を呼び出すための資格情報。 パートナーは、サポートされている認証メカニズム (たとえば、ユーザー名とパスワード、API キーなど) に基づいて、この情報を提供します。 |
| partnerType | "Weather"。 FarmBeats での、その他のパートナーの種類として "Sensor" と "Imagery" があります。  |
|  name   |   FarmBeats システムでのパートナーの任意の名前。   |
|  description |  説明   |

## <a name="next-steps"></a>次のステップ

Azure FarmBeats インスタンスからセンサー データのクエリを実行したので、ファームの[マップを生成する](generate-maps-in-azure-farmbeats.md#generate-maps)方法を説明します。