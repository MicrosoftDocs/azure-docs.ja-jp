---
title: IoT Edge デバイスに Live Video Analytics をデプロイする - Azure
description: この記事では、IoT Edge デバイスに Live Video Analytics をデプロイするときに役立つ手順を示します。 たとえば、ローカル Linux コンピューターにアクセスできる場合や、以前に Azure Media Services アカウントを作成してある場合などに、これを行います。
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: 01b98c7a1f4073adcd8dea7cbfbfc57abc3787c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101718932"
---
# <a name="deploy-live-video-analytics-on-an-iot-edge-device"></a>IoT Edge デバイスに Live Video Analytics をデプロイする

この記事では、IoT Edge デバイスに Live Video Analytics をデプロイするときに役立つ手順を示します。 たとえば、ローカル Linux コンピューターにアクセスできる場合や、以前に Azure Media Services アカウントを作成してある場合などに、これを行います。

> [!NOTE]
> ARM64 デバイスのサポートは、Live Video Analytics on IoT Edge ビルド `1.0.4` 以降で利用できます。
> ARM64 デバイスでの Azure IoT Edge ランタイムの実行のサポートは、[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)中です。

## <a name="prerequisites"></a>前提条件

* [サポートされている Linux オペレーティング システム](../../iot-edge/support.md#operating-systems)のいずれかを実行している x86-64 または ARM64 デバイス
* [所有者特権](../../role-based-access-control/built-in-roles.md#owner)がある Azure サブスクリプション
* [IoT Hub を作成してセットアップします](../../iot-hub/iot-hub-create-through-portal.md)
* [IoT Edge デバイスを登録します](../../iot-edge/how-to-register-device.md)
* [Debian ベースの Linux システムに Azure IoT Edge ランタイムをインストールする](../../iot-edge/how-to-install-iot-edge.md)
* [Azure Media Services アカウントを作成します](../latest/create-account-howto.md)

    * 次のいずれかのリージョンを使用します: 米国東部 2、米国東部、米国中部、米国中北部、東日本、米国西部、米国西部 2、米国中西部、カナダ東部、英国南部、フランス中部、フランス南部、スイス北部、スイス西部、西日本。
    * General Purpose v2 (GPv2) ストレージ アカウントを使用することをお勧めします

## <a name="configuring-azure-resources-for-using-live-video-analytics"></a>Live Video Analytics を使用するための Azure リソースの構成

### <a name="create-custom-azure-resource-manager-role"></a>カスタム Azure Resource Manager ロールの作成

[カスタム Azure Resource Manager ロールの作成](create-custom-azure-resource-manager-role-how-to.md)に関するページを参照して、Live Video Analytics を使用するためのサービス プリンシパルに割り当てます。

### <a name="set-up-a-premium-streaming-endpoint"></a>Premium ストリーミング エンドポイントを設定する

Live Video Analytics を使用して、継続的にビデオをクラウドに記録し、後でそれを再生する前に、[クエリ API](playback-recordings-how-to.md#query-api) を使用する場合、[Premium ストリーミング エンドポイント](../latest/streaming-endpoint-concept.md#types)を使用するように Media Service を更新することをお勧めします。  

これは省略可能な手順です。 次の Azure CLI コマンドを使用して、それを行うことができます。

```azurecli
az ams streaming-endpoint scale --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --scale-units 1
```

このコマンドを使用して、ストリーミング エンドポイントを開始できます 

> [!IMPORTANT]
> この時点から、サブスクリプションに対する請求が開始されます。

```azurecli
az ams streaming-endpoint start --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --no-wait
```

[Media Service API へのアクセス](../latest/access-api-howto.md?tabs=portal)に関する記事の手順に従って Media Service API にアクセスするための資格情報を取得し、[ポータル] タブを選択します。

## <a name="create-and-use-local-user-account-for-deployment"></a>デプロイ用のローカル ユーザー アカウントを作成して使用する
Live Video Analytics on IoT Edge モジュールを実行するには、可能な限り少ない権限でローカル ユーザー アカウントを作成します。 たとえば、Linux コンピューターで次のコマンドを実行します。

```
sudo groupadd -g 1010 localusergroup
sudo useradd --home-dir /home/edgeuser --uid 1010 --gid 1010 lvaedgeuser
```

## <a name="granting-permissions-to-device-storage"></a>デバイスのストレージへのアクセス許可を付与する

これでローカル ユーザー アカウントが作成されました。 

* アプリケーション構成データを格納するためのローカル フォルダーが必要です。 次のコマンドを使用して、フォルダーを作成し、ローカル ユーザー アカウントにそのフォルダーへの書き込みアクセス許可を付与します。

```
sudo mkdir -p /var/lib/azuremediaservices
sudo chown -R lvaedgeuser /var/lib/azuremediaservices
```

* また、[ビデオをローカル ファイルに記録する](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources)ためのフォルダーも必要です。 次のコマンドを使用して、同じようにローカル フォルダーを作成します。

```
sudo mkdir -p /var/media
sudo chown -R lvaedgeuser /var/media
```

## <a name="deploy-live-video-analytics-edge-module"></a>Live Video Analytics Edge モジュールをデプロイする

Live Video Analytics on IoT Edge では、「[モジュール ツインの構成スキーマ](module-twin-configuration-schema.md)」に記載されているモジュール ツイン プロパティが公開されています。 

### <a name="deploy-using-the-azure-portal"></a>Azure Portal を使用したデプロイ

Azure portal では、配置マニフェストの作成から、IoT Edge デバイスへのデプロイのプッシュまでをガイドします。  
#### <a name="select-your-device-and-set-modules"></a>デバイスを選択してモジュールを設定する

1. [Azure Portal](https://ms.portal.azure.com/) にサインインし、IoT Hub に移動します。
1. メニューから **[IoT Edge]** を選択します。
1. デバイスの一覧でターゲット デバイスの ID をクリックします。
1. **[Set Modules] \(モジュールの設定)** を選択します。

#### <a name="configure-a-deployment-manifest"></a>配置マニフェストを構成する

配置マニフェストは、デプロイするモジュール、モジュール間でのデータ フロー、およびモジュール ツインの目的のプロパティを記述した JSON ドキュメントです。 Azure portal には、配置マニフェストを作成する手順を示すウィザードがあります。 タブには次の3つの手順が構成されています:**モジュール**、**ルート**、および **レ表示と作成** を行います。

#### <a name="add-modules"></a>モジュールを追加する

1. ページの **[IoT Edge モジュール]** セクションで、 **[追加]** ドロップダウンをクリックし、**IoT Edge モジュール** を選択して、 **[IoT Edge モジュールの追加]** ページを表示します。
1. **モジュールの設定** タブで、モジュール名を入力し、コンテナー イメージ URI を指定します:   
    例 :
    
    * **IoT Edge モジュールの名前**: lvaEdge
    * **イメージ URI**: mcr.microsoft.com/media/live-video-analytics:2.0    
    
    ![[モジュール設定] タブを示しているスクリーンショット。](./media/deploy-iot-edge-device/add.png)
    
    > [!TIP]
    > この手順の説明に従って、 **[モジュール設定]** タブ、 **[コンテナ作成オプション]** タブ、および **[モジュールツイン設定]** タブで値を指定するまで、 **[追加]** を選択しないでください。
    
    > [!WARNING]
    > Azure IoT Edge モジュールの呼び出しでは、大文字と小文字が区別されます。 モジュール名として使用する文字列を正確に記録しておきます。

1. **[環境変数]** タブを開きます。
   
   ![環境変数](./media/deploy-iot-edge-device/environment-variables.png)に表示される入力ボックスに、次の値を追加します。 

1. **[コンテナーの作成オプション]** タブを開きます。

    ![コンテナーの作成オプション](./media/deploy-iot-edge-device/container-create-options.png)
 
    次の JSON をコピーしてボックスに貼り付け、モジュールによって生成されるログ ファイルのサイズを制限します。
    
    ```    
    {
        "HostConfig": {
            "LogConfig": {
                "Type": "",
                "Config": {
                    "max-size": "10m",
                    "max-file": "10"
                }
            },
            "Binds": [
               "/var/lib/azuremediaservices:/var/lib/azuremediaservices",
               "/var/media:/var/media"
            ]
        }
    }
    ````
   
   JSON の "Binds" セクションには、次の 2 つのエントリがあります。
   1. "/var/lib/azuremediaservices:/var/lib/azuremediaservices": これは、永続的なアプリケーション構成データをコンテナーからバインドし、エッジ デバイスに格納するために使用されます。
   1. "/var/media:/var/media": これにより、エッジ デバイスとコンテナーの間でメディア フォルダーがバインドされます。 これは、エッジ デバイスへのビデオ クリップの格納をサポートするメディア グラフ トポロジを実行するときに、ビデオ記録を格納するために使用されます。
   
1. **[モジュール ツインの設定]** タブで、以降の JSON をコピーし、ボックスに貼り付けます。
 
    ![ツインの設定](./media/deploy-iot-edge-device/twin-settings.png)

    [モジュール ツインの構成スキーマ](module-twin-configuration-schema.md)に関するページで説明されているように、Live Video Analytics on IoT Edge では、実行するために一連の必須ツイン プロパティが必要です。  

    [モジュール ツインの設定] 編集ボックスには、次の JSON を入力する必要があります。    
    ```
    {
        "applicationDataDirectory": "/var/lib/azuremediaservices",
        "azureMediaServicesArmId": "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{AMS-account-name}",
        "aadTenantId": "{the-ID-of-your-tenant}",
        "aadServicePrincipalAppId": "{the-ID-of-the-service-principal-app-for-ams-account}",
        "aadServicePrincipalSecret": "{secret}"
    }
    ```
    これらは、上記の JSON に対して **必須** のプロパティです。  
    * {subscriptionID} - これは、自分の Azure サブスクリプション ID です
    * {resourceGroupName} - これは、Media Services アカウントが属しているリソース グループです
    * {AMS-account-name} - これは、Media Services アカウントの名前です
    
    他の値については、[Azure Media Services API へのアクセス](../latest/access-api-howto.md?tabs=portal)に関する記事を参照し、[ポータル] タブを選択します。  
    * aadTenantId - これはテナントの ID であり、上記のリンクの "AadTenantId" と同じです。
    * aadServicePrincipalAppId - これは、Media Services アカウントのサービス プリンシパルのアプリ ID であり、上記のリンクの "AadClientId" と同じです。
    * aadServicePrincipalSecret - これはサービス プリンシパルのパスワードであり、上記のリンクの "AadSecret" と同じです。

    次に示すのは、JSON に追加してモジュールを監視するのに役立つ、**推奨される** いくつかの追加プロパティです。 詳細については、「[監視とログ記録](monitoring-logging.md)」を参照してください。
    
    ```
    "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
    "OperationalEventsOutputName": "lvaEdgeOperational",
    "logLevel": "Information",
    "logCategories": "Application,Events"
    ```
    
    次に示すのは、JSON で追加できる **オプションの** プロパティです。
    
    ```
    "aadEndpoint": "https://login.microsoftonline.com",
    "aadResourceId": "https://management.core.windows.net/",
    "armEndpoint": "https://management.azure.com/",
    "allowUnsecuredEndpoints": true
    ```

   > [!Note]
   > ツイン プロパティ **allowUnsecuredEndpoints** を true に設定してあるのは、チュートリアルとクイックスタートのためです。   
   運用環境で実行する場合は、このプロパティを **false** に設定する必要があります。 これにより、セキュリティ保護されていないすべてのエンドポイントがアプリケーションでブロックされ、グラフのトポロジを実行するには、有効な接続資格情報が必要になります。  
   
    [追加] を選択して、モジュール ツインのプロパティを追加します。
1. **Next:ルート** 　でルート のセクションに進みます。
    ルートを指定します。

既定のルートをそのまま保持して、[**次へ] を選択します:[確認と作成]** で、[レビュー] セクションに進みます。

#### <a name="review-deployment"></a>デプロイを確認する

確認のセクションには、前述の 2 つのセクションの選択項目に基づいて作成された JSON 配置マニフェストが表示されます。 追加しなかった 2 つのモジュール ($edgeAgent および $edgeHub) も宣言されています。 これらの 2 つのモジュールは、IoT Edge ランタイムを構成し、すべてのデプロイの既定値として使用されます。

デプロイ情報を確認してから、[作成] を選択します。

### <a name="verify-your-deployment"></a>デプロイを確認する

デプロイを作成すると、IoT ハブの IoT Edge ページに戻ります。

1. デプロイで対象にした Azure IoT Edge デバイスを選択して、その詳細を開きます。
2. デバイスの詳細で、Blob Storage モジュールが **[デプロイで指定] と [デバイス別に報告]** の両方として一覧に表示されていることを確認します。

モジュールがデバイス上で開始してから IoT Hub にレポートされるまでしばらく時間がかかる場合があります。 ページを更新して、最新の状態を表示します。
状態コード:200 –OK は、[IoT Edge ランタイム](../../iot-edge/iot-edge-runtime.md)が正常であり、正常に動作していることを意味します。

![IoT Edge ランタイムの状態値を示しているスクリーンショット。](./media/deploy-iot-edge-device/status.png)

#### <a name="invoke-a-direct-method"></a>ダイレクト メソッドを呼び出す

次に、ダイレクト メソッドを呼び出してサンプルをテストします。 lvaEdge モジュールによって提供されているすべてのダイレクト メソッドについては、[Live Video Analytics on IoT Edge 用のダイレクト メソッド](direct-methods.md)に関するページを参照してください。

1. 作成したエッジ モジュールをクリックすると、その構成ページが表示されます。  

    ![エッジ モジュールの構成ページを示しているスクリーンショット。](./media/deploy-iot-edge-device/modules.png)
1. [ダイレクト メソッド] メニューオプションをクリックします。

    > [!NOTE] 
    > 現在のページで確認できるように、[接続文字列] セクションに値を追加する必要があります。 **[設定名]** セクションの値を非表示にしたり変更したりする必要はありません。 パブリックにしておいてかまいません。

    ![ダイレクト メソッド](./media/deploy-iot-edge-device/module-details.png)
1. 次に、[メソッド名] ボックスに「GraphTopologyList」と入力します。
1. さらに、次の JSON ペイロードをコピーしてペイロード ボックスに貼り付けます。
    
    ```
    {
        "@apiVersion" : "2.0"
    }
    ```
1. ページの上部にある [メソッドの呼び出し] オプションをクリックします

    ![ダイレクト メソッド](./media/deploy-iot-edge-device/direct-method.png)
1. [結果] ボックスに、状態が 200 であることを示すメッセージが表示されます

    ![200 状態メッセージ](./media/deploy-iot-edge-device/connection-timeout.png) 

## <a name="next-steps"></a>次のステップ

次を試します。「[クイックスタート: はじめに - Live Video Analytics on IoT Edge](get-started-detect-motion-emit-events-quickstart.md#deploy-modules-on-your-edge-device)

> [!TIP]
> 上記のクイックスタートに進む場合は、Visual Studio Code を使用してダイレクト メソッドを呼び出すときに、既定の `lva-sample-device` ではなく、この記事で IoT Hub に追加したデバイスを使用します。