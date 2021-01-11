---
ms.openlocfilehash: be983a643b45847d2a44db018b1383aa56ab2302
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691192"
---
1. 次の場所からリポジトリをクローンします: https://github.com/Azure-Samples/live-video-analytics-iot-edge-python 。
1. Visual Studio Code で、リポジトリをダウンロードしたフォルダーを開きます。
1. Visual Studio Code で、*src/cloud-to-device-console-app* フォルダーに移動します。 そこにファイルを作成し、*appsettings.json* という名前を付けます。 このファイルには、プログラムを実行するために必要な設定が格納されます。
1. このクイックスタートで前に生成した *~/clouddrive/lva-sample/appsettings.json* ファイルの内容をコピーします。

    テキストは次の出力のようになります。

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. *src/edge* フォルダーに移動し、 *.env* という名前のファイルを作成します。
1. */clouddrive/lva-sample/edge-deployment/.env* ファイルの内容をコピーします。 テキストは次のコードのようになります。

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"      
    ```
    