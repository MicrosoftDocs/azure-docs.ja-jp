---
title: Azure IoT ハブを複製する方法
description: Azure IoT ハブを複製する方法
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: robinsh
ms.openlocfilehash: 6e4d110221c7f360e8177505de2a7789f9616d51
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976147"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>Azure IoT ハブを別のリージョンに複製する方法

この記事では、IoT Hub を複製する方法について説明し、始める前に答えるべきいくつかの質問を示します。 IoT ハブを複製する理由の一部は次のとおりです。
 
* ヨーロッパから北米 (またはその逆) など、地域間で会社を移動するが、リソースとデータを新しい場所に地理的に近い場所に配置するため、ハブを移動する必要がある。

* 開発環境と運用環境の両方に対応するハブを設定する。

* マルチハブの高可用性のカスタム実装を行う必要がある。 詳細については、[IoT Hub 高可用性とディザスター リカバリーでリージョン間 HA を実現する方法に関するセクション](iot-hub-ha-dr.md#achieve-cross-region-ha)を参照してください。

* ハブで構成されている[パーティション](iot-hub-scaling.md#partitions)数を増やす必要があるとします。 これは初めてハブを作成するときに設定され、変更することはできません。 この記事の情報を使用してハブを複製し、複製を作成するときにパーティションの数を増やします。

ハブを複製するには、元のハブへの管理アクセス権を持つサブスクリプションが必要です。 新しいリソース グループとリージョン、元のハブと同じサブスクリプション、または新しいサブスクリプションに新しいハブを配置することができます。 ハブ名はグローバルに一意である必要があるため、同じ名前を使用することはできません。

> [!NOTE]
> 現時点では、IoT ハブを複製するためのファーストクラスの機能はありません。 複製は主に手動のプロセスであるため、非常にエラーが発生しやすくなっています。 ハブの複製の複雑さは、ハブの複雑さに直接比例します。 たとえば、メッセージ ルーティンなしで IoT ハブを複製するのは非常に簡単です。 メッセージ ルーティングを複雑さの 1 つとして追加すると、ハブの複製は少なくとも桁違いに複雑になります。 また、ルーティング エンドポイントに使用されるリソースも移動する場合、もう一段階複雑になります。 

## <a name="things-to-consider"></a>考慮事項

IoT ハブを複製する前に、いくつかの点を考慮する必要があります。

* 元の場所で使用できるすべての機能を新しい場所でも使用できることを確認します。 一部のサービスはプレビュー段階であり、すべての機能をどこでも利用できるわけではありません。

* 複製されたバージョンを作成して確認する前に、元のリソースを削除しないでください。 ハブを削除すると、そのハブは永久に失われ、設定やデータを復旧してハブが正しく複製されていることを確認する方法はありません。

* 多くのリソースには、グローバルに一意の名前を使用する必要があるため、複製されたバージョンには別の名前を使用する必要があります。 また、複製されたハブが属しているリソース グループには別の名前を使用する必要があります。 

* 元の IoT ハブのデータは移行されません。 これには、すべてのテレメトリ メッセージ、cloud-to-device (C2D) コマンド、およびジョブに関連する情報 (スケジュールや履歴) が含まれます。 メトリクスとログ記録も移行されません。 

* Azure Storage にルーティングされたデータまたはメッセージについては、元のストレージ アカウントにデータを残しておき、そのデータを新しいリージョンの新しいストレージ アカウントに転送するか、古いデータをそのままにして新しい場所に新しいデータを保存する新しいストレージ アカウントを作成することができます。 Blob ストレージでデータを移行する方法について詳しくは、「[AzCopy を使ってみる](../storage/common/storage-use-azcopy-v10.md)」を参照してください。

* Event Hubs および Service Bus のトピックとキューのデータを移行することはできません。 これは、特定の時点のデータであり、メッセージが処理された後は保存されません。

* 移行によるダウンタイムをスケジュールする必要があります。 デバイスを新しいハブに複製するには時間がかかります。 Import/Export メソッドを使用している場合は、50 万デバイスを移動するのに 2 時間、100 万台のデバイスを移動するのに 4 時間かかることがベンチマーク テストによって明らかになりました。 

* デバイスをシャットダウンしたり変更したりすることなく、デバイスを新しいハブにコピーできます。 

    * デバイスが元々 DPS を使用してプロビジョニングされていた場合、再プロビジョニングすると、各デバイスに格納されている接続情報が更新されます。 
    
    * それ以外の場合は、Import/Export メソッドを使用してデバイスを移動し、その後、新しいハブを使用するにはデバイスを変更する必要があります。 たとえば、ツインの必要なプロパティから IoT Hub ホスト名を使用するようにデバイスを設定できます。 デバイスは IoT Hub ホスト名を取得し、古いハブからデバイスを切断し、新しいデバイスに再接続します。
    
* 新しいリソースで使用できるように、使用しているすべての証明書を更新する必要があります。 また、ハブがどこかの DNS テーブルで定義されている場合は、その DNS 情報を更新する必要があります。

## <a name="methodology"></a>手法

これは、IoT ハブをあるリージョンから別のリージョンに移動する場合に推奨される一般的な方法です。 メッセージのルーティングでは、リソースが新しいリージョンに移動されていないことを前提としています。 詳しくは、[メッセージのルーティングに関するセクション](#how-to-handle-message-routing)をご覧ください。

   1. ハブとその設定を Resource Manager テンプレートにエクスポートします。 
   
   1. テンプレートに必要な変更を加えます。たとえば、複製されたハブの名前と場所をすべて更新します。 メッセージ ルーティング エンドポイントに使用されるテンプレート内のリソースについては、そのリソースのテンプレート内のキーを更新します。
   
   1. 新しい場所の新しいリソース グループにテンプレートをインポートします。 これで、複製が作成されます。

   1. 必要に応じてデバッグします。 
   
   1. テンプレートにエクスポートされていないものを追加します。 
   
       たとえば、コンシューマー グループはテンプレートにエクスポートされません。 コンシューマー グループを手動でテンプレートに追加するか、ハブの作成後に [Azure portal](https://portal.azure.com) を使用する必要があります。 テンプレートにコンシューマー グループを 1 つ追加する例については、「[Azure Resource Manager テンプレートを使用して IoT Hub のメッセージ ルーティングを構成する](tutorial-routing-config-message-routing-rm-template.md)」記事を参照してください。
       
   1. 元のハブから複製にデバイスをコピーします。 これについては、[IoT ハブに登録されているデバイスを管理](#managing-the-devices-registered-to-the-iot-hub)する方法に関するセクションを参照してください。

## <a name="how-to-handle-message-routing"></a>メッセージ ルーティングを処理する方法

ハブで[カスタム ルーティング](iot-hub-devguide-messages-read-custom.md)が使用されている場合、ハブのテンプレートのエクスポートにはルーティング構成が含まれますが、リソース自体は含まれません。 ルーティング リソースを新しい場所に移動するか、そのままにしておき、引き続き "そのまま" 使用するかを選択する必要があります。 

たとえば、米国西部にあるストレージ アカウントにメッセージをルーティングするハブを、米国西部から米国東部に移動するとします。 ハブを移動しても、米国西部のストレージ アカウントにメッセージをルーティングすることも、ハブとストレージ アカウントを両方移動することもできます。 別のリージョンのエンドポイント リソースにメッセージをルーティングすることで、パフォーマンスが低下する場合があります。

ルーティング エンドポイントに使用されているリソースを移動しない場合は、メッセージ ルーティングを使用するハブを簡単に移動できます。 

ハブでメッセージ ルーティングを使用している場合は、2 つの選択肢があります。 

1. ルーティング エンドポイントに使用されているリソースを新しい場所に移動します。

    * 新しいリソースは、[Azure portal](https://portal.azure.com) で手動で作成するか、Resource Manager テンプレートを使用して作成する必要があります。 

    * 新しい場所にリソースを作成する際は、グローバルに一意の名前が付けられているため、すべてのリソースの名前を変更する必要があります。 
     
    * 新しいハブを作成する前に、新しいハブのテンプレートでリソース名とリソース キーを更新する必要があります。 リソースは、新しいハブの作成時には存在している必要があります。

1. ルーティング エンドポイントに使用されるリソースは移動しないでください。 "元の位置で" 使用してください。

   * テンプレートを編集する手順では、新しいハブを作成する前に、各ルーティング リソースのキーを取得し、それらをテンプレートに含める必要があります。 

   * ハブは引き続き元のルーティング リソースを参照し、構成されたとおりにメッセージをルーティングします。

   * ハブとルーティング エンドポイント リソースが同じ場所にないため、パフォーマンスがわずかに低下します。

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>ハブを別のリージョンに移行するための準備

このセクションでは、ハブを移行するための具体的な手順について説明します。

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>元のハブを見つけて、リソース テンプレートにエクスポートします。

1. [Azure Portal](https://portal.azure.com) にサインインします。 

1. **[リソース グループ]** に移動し、移動するハブが含まれているリソース グループを選択します。 **[リソース]** にアクセスすることでもハブを見つけることができます。 ハブを選択します。

1. ハブのプロパティと設定の一覧から **[テンプレートのエクスポート]** を選択します。 

   ![IoT Hub のテンプレートをエクスポートするためのコマンドを示すスクリーンショット。](./media/iot-hub-how-to-clone/iot-hub-export-template.png)

1. **[ダウンロード]** を選択してテンプレートをダウンロードします。 ファイルを、後で見つけられる場所に保存します。 

   ![IoT Hub のテンプレートをダウンロードするためのコマンドを示すスクリーンショット。](./media/iot-hub-how-to-clone/iot-hub-download-template.png)

### <a name="view-the-template"></a>テンプレートを見る 

1. 「ダウンロード」フォルダー (または、テンプレートをエクスポートしたときに使用したフォルダー) にアクセスし、zip ファイルを見つけます。 Zip ファイルを開き、`template.json` という名前のファイルを見つけます。 ファイルを選択し、Ctrl + C キーを押してテンプレートをコピーします。 Zip ファイルにない別のフォルダーにアクセスし、ファイルを貼り付けます (Ctrl + V)。 これで編集できるようになりました。
 
    次の例は、ルーティング構成のない汎用ハブを対象としています。 これは、**westus** リージョンの **ContosoTestHub29358** と呼ばれる S1 レベルのハブ (1 ユニット) です。 エクスポートされたテンプレートがこちらです。

    ``` json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Devices/IotHubs",
                "apiVersion": "2018-04-01",
                "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
                "location": "westus",
                "sku": {
                    "name": "S1",
                    "tier": "Standard",
                    "capacity": 1
                },
                "properties": {
                    "operationsMonitoringProperties": {
                        "events": {
                            "None": "None",
                            "Connections": "None",
                            "DeviceTelemetry": "None",
                            "C2DCommands": "None",
                            "DeviceIdentityOperations": "None",
                            "FileUploadOperations": "None",
                            "Routes": "None"
                        }
                    },
                    "ipFilterRules": [],
                    "eventHubEndpoints": {
                        "events": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        },
                        "operationsMonitoringEvents": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358-operationmonitoring",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        }
                    },
                    "routing": {
                        "endpoints": {
                            "serviceBusQueues": [],
                            "serviceBusTopics": [],
                            "eventHubs": [],
                            "storageContainers": []
                        },
                        "routes": [],
                        "fallbackRoute": {
                            "name": "$fallback",
                            "source": "DeviceMessages",
                            "condition": "true",
                            "endpointNames": [
                                "events"
                            ],
                            "isEnabled": true
                        }
                    },
                    "storageEndpoints": {
                        "$default": {
                            "sasTtlAsIso8601": "PT1H",
                            "connectionString": "",
                            "containerName": ""
                        }
                    },
                    "messagingEndpoints": {
                        "fileNotifications": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "enableFileUploadNotifications": false,
                    "cloudToDevice": {
                        "maxDeliveryCount": 10,
                        "defaultTtlAsIso8601": "PT1H",
                        "feedback": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "features": "None"
                }
            }
        ]
    }
    ```

### <a name="edit-the-template"></a>テンプレートの編集 

テンプレートを使用して新しいリージョンに新しいハブを作成する前に、いくつかの変更を行う必要があります。 テンプレートを編集するには、[VS Code](https://code.visualstudio.com) またはテキスト エディターを使用します。

#### <a name="edit-the-hub-name-and-location"></a>ハブの名前と保存先を編集する

1. 上部の [パラメーター] セクションを削除します。ここでは複数のパラメーターを指定しないので、ハブ名を使用する方がはるかに簡単です。 

    ``` json
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
    ```

1. 前の手順で削除したパラメーターから取得するのではなく、実際の (新しい) 名前を使用するように名前を変更します。 

    新しいハブの場合は、元のハブの名前に "*clone*" という文字列を追加して新しい名前を作成します。 ハブの名前と場所をクリーンアップすることから始めます。
    
    前のバージョン:

    ``` json 
    "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
    "location": "westus",
    ```
    
    新しいバージョン: 

    ``` json 
    "name": "ContosoTestHub29358clone",
    "location": "eastus",
    ```

    次に、**パス**の値に古いハブ名が含まれていることがわかります。 新しいものを使用するように変更します。 これらのパス値は **eventHubEndpoints** の下にあり、**events** および **OperationsMonitoringEvents** と呼ばれます。

    完了すると、イベント ハブのエンドポイントのセクションは次のようになります。

    ``` json
    "eventHubEndpoints": {
        "events": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        },
        "operationsMonitoringEvents": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone-operationmonitoring",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        }
    ```

#### <a name="update-the-keys-for-the-routing-resources-that-are-not-being-moved"></a>移動されないルーティング リソースのキーを更新する

ルーティングが構成されているハブの Resource Manager テンプレートをエクスポートすると、リソースの配置はアスタリスクで示され、リソースのキーがエクスポートされたテンプレートで提供されていないことがわかります。 新しいハブのテンプレートをインポートしてハブを作成する**前**に、ポータルでこれらのリソースに移動してキー を取得し、記入する必要があります。 

1. 任意のルーティング リソースに必要なキーを取得し、それらをテンプレートに配置します。 リソースからキーを取得するには、[Azure portal](https://portal.azure.com) を使用します。 

   たとえば、メッセージをストレージ コンテナーにルーティングする場合は、ポータルでストレージ アカウントを検索します。 [設定] セクションで、 **[アクセス キー]** を選択し、いずれかのキーをコピーします。 テンプレートを始めてエクスポートする場合、次のようなキーが表示されます。

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. ストレージ アカウントのアカウント キーを取得した後、アスタリスクの代わりに `AccountKey=****` 句のテンプレートに配置します。 

1. Service Bus キューの場合は、SharedAccessKeyName に一致する共有アクセス キーを取得します。 json のキーと `SharedAccessKeyName` は次のとおりです。

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. Service Bus トピックとイベント ハブ接続についても同じことが当てはまります。

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>新しい場所に新しいルーティング リソースを作成する

このセクションは、ルーティング エンドポイントでハブが使用するリソースを移動する場合にのみ適用されます。

ルーティング リソースを移動する場合は、新しい場所にリソースを手動で設定する必要があります。 ルーティング リソースを作成するには、[Azure portal](https://portal.azure.com) を使用するか、メッセージのルーティングに使用される各リソースの Resource Manager テンプレートをエクスポートして編集し、インポートします。 リソースの設定が完了したら、ハブのテンプレート (ルーティング構成を含む) をインポートできます。

1. ルーティングで使用される各リソースを作成します。 これは、[Azure portal](https://portal.azure.com) を使用して手動で行うことも、Resource Manager テンプレートを使用してリソースを作成することもできます。 テンプレートを使用する場合は、次の手順を実行します。

    1. ルーティングで使用される各リソースを、Resource Manager テンプレートにエクスポートします。
    
    1. リソースの名前と場所を更新します。 

    1. リソース間の相互参照を更新します。 たとえば、新しいストレージ アカウント用のテンプレートを作成する場合は、そのテンプレート内のストレージ アカウント名と、それを参照するその他のテンプレートを更新する必要があります。 ほとんどの場合、そのリソースを参照する唯一のテンプレートはハブのテンプレートのルーティング セクションです。 

    1. 各テンプレートをインポートして、各リソースをデプロイします。

    ルーティングで使用されるリソースの設定を完了して実行すると、続行できます。

1. IoT ハブのテンプレートで、各ルーティング リソースの名前を新しい名前に変更し、必要に応じて場所を更新します。 

これで、ルーティングの処理方法に応じて、以前のハブとほぼ同じように見える新しいハブが作成されます。

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>移動 -- テンプレートを読み込んで、新しいリージョンに新しいハブを作成する

テンプレートを使用して、新しい場所に新しいハブを作成します。 ルーティング リソースを移動する場合は、新しい場所でリソースを設定し、テンプレート内の参照が一致するように更新する必要があります。 ルーティング リソースを移動しない場合は、更新されたキーを含むテンプレート内に存在する必要があります。

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. **[リソースの作成]** を選択します。 

1. 検索ボックスに「template deployment」と入力し、Enter キーを押します。

1. **[Template deployment (deploy using custom templates)]\(テンプレートのデプロイ (カスタム テンプレートを使用してデプロイ)\)** を選択します。 これにより、[Template deployment] \(テンプレートのデプロイ\)の画面が表示されます。 **作成** を選択します。 次の画面が表示されます。

   ![独自のテンプレートを作成するためのコマンドを示すスクリーンショット](./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png)

1. **[Build your own template in the editor] \(エディターで独自のテンプレートをビルド\)** を選択します。これにより、ファイルからテンプレートをアップロードできます。 

1. **[ファイルの読み込み]** を選択します。 

   ![テンプレート ファイルをアップロードするためのコマンドを示すスクリーンショット](./media/iot-hub-how-to-clone/iot-hub-upload-file.png)

1. 編集した新しいテンプレートを参照して選択し、 **[開く]** を選択します。 編集ウィンドウにテンプレートが読み込まれます。 **[保存]** を選択します。 

   ![テンプレートの読み込みを示すスクリーンショット](./media/iot-hub-how-to-clone/iot-hub-loading-template.png)

1. 次のフィールドに入力します。

   **[サブスクリプション]** : 使用するサブスクリプションを選択します。

   **[リソース グループ]** : 新しい場所で新しいリソース グループを作成します。 新しく設定したものが既にある場合は、新しい設定を作成する代わりに選択できます。

   **[場所]** :既存のリソース グループを選択した場合は、リソース グループの場所と一致するように入力されます。 新しいリソース グループを作成した場合は、その場所になります。

   **[同意チェック ボックス]** : これは、作成中のリソースの支払いに同意することを意味します。

1. **[購入]** をクリックします。

これで、ポータルでテンプレートが検証され、複製されたハブがデプロイされます。 ルーティング構成データがある場合は新しいハブに追加されますが、以前の場所のリソースを参照します。

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>IoT ハブに登録されたデバイスを管理する

複製を作成して実行したので、元のハブから複製にすべてのデバイスをコピーする必要があります。 

これを行うには複数の方法があります。 デバイスを始めて作成した際に[デバイス プロビジョニング サービス (DPS)](/azure/iot-dps/about-iot-dps) 使用したかどうかによって異なります。 使用した場合、デバイスのコピーは簡単です。 使用していない場合、非常に複雑になる可能性があります。 

デバイスを DPS をしてプロビジョニングしなかった場合は、次のセクションをスキップし、[Import/Export を使用してデバイスを新しいハブに移動](#using-import-export-to-move-the-devices-to-the-new-hub)することから始めます。

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>DPS を使用して新しいハブでデバイスを再プロビジョニングする

DPS を使用してデバイスを新しい場所に移動する方法については、[「デバイスを再プロビジョニングする方法」](../iot-dps/how-to-reprovision.md)を参照してください。 完了すると [Azure portal](https://portal.azure.com) でデバイスを表示できるため、新しい場所にデバイスがあることを確認できます。

[Azure portal](https://portal.azure.com) を使用して新しいハブにアクセスします。 ハブを選択し、 **[IoT デバイス]** を選択します。 複製されたハブに再プロビジョニングされたデバイスが表示されます。 また、複製されたハブのプロパティを表示することもできます。 

ルーティングを実装している場合は、メッセージがリソースに正しくルーティングされていることをテストして確認します。

### <a name="committing-the-changes-after-using-dps"></a>DPS を使用した後に変更をコミットする

この変更は DPS サービスによってコミットされました。

### <a name="rolling-back-the-changes-after-using-dps"></a>DPS を使用した後に変更をロールバックします。 

変更をロール バックする場合は、デバイスを新しいハブから古いものに再プロビジョニングします。

これで、ハブとデバイスの移行が完了しました。 [クリーンアップ](#clean-up)までスキップできます。

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>インポート/エクスポートを使用して新しいハブにデバイスを移動する

アプリケーションは .NET Core を対象としているので、Windows または Linux で実行できます。 サンプルをダウンロードし、接続文字列を取得し、実行するビットのフラグを設定して実行できます。 これを行うには、コードを開く必要はありません。

### <a name="downloading-the-sample"></a>サンプルのダウンロード

1. 次のページの C# IoT サンプルを使用します:[C# 用 Azure IoT サンプル](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/)。 Zip ファイルをダウンロードし、コンピューターに解凍します。 

1. 関連するコードは、./iot-hub/Samples/service/ImportExportDevicesSample にあります。 アプリケーションを実行するためにコードを表示または編集する必要はありません。

1. アプリケーションを実行するには、3 つの接続文字列と 5 つのオプションを指定します。 このデータをコマンド ライン引数として渡すか、環境変数を使用するか、または 2 つを組み合わせて使用します。 ここでは、オプションをコマンド ライン引数として、接続文字列を環境変数として渡します。 

   これは、接続文字列は長くて扱いづらく、変更される可能性はほとんどありませんが、オプションを変更してアプリケーションを複数回実行する必要があるためです。 環境変数の値を変更するには、コマンド ウィンドウと使用している Visual Studio または VS Code を閉じる必要があります。 

### <a name="options"></a>オプション

アプリケーションの実行時に指定する 5 つのオプションを次に示します。 これらは、1 分間にコマンド ラインに配置されます。

*   **addDevices** (引数 1) -- ユーザー向けに生成された仮想デバイスを追加する場合は、これを true に設定します。 これらは、ソース ハブに追加されます。 また、**numToAdd** (引数 2) を設定して、追加するデバイスの数を指定します。 ハブに登録できるデバイスの最大数は 100 万です。このオプションはテスト用で、特定の数のデバイスを生成して別のハブにコピーできます。

*   **copyDevices** (引数 3) -- あるハブから別のハブにデバイスをコピーするには、この設定を true に設定します。 

*   **deleteSourceDevices** (引数 4) -- ソース ハブに登録されているすべてのデバイスを削除する場合は true に設定します。 これを実行する前に、すべてのデバイスが転送されていることを確認するまで待つことをお勧めします。 デバイスを削除すると、デバイスを復元することはできません。

*   **deleteDestDevices** (引数 5) -- 宛先ハブ (複製) に登録されているすべてのデバイスを削除するには、これを true に設定します。 デバイスを複数回コピーする場合は、この操作を実行することをお勧めします。 

基本的なコマンドは *dotnet run* です -- これは、ローカルの csproj ファイルをビルドして実行するよう .NET に指示します。 コマンド ライン引数は、実行する前に最後に追加します。 

コマンド ラインは次の例のようになります。

``` console 
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub, or delete them. 
    // The first argument is true, numToAdd is 50, and the other arguments are false.
    dotnet run true 1000 false false false 

    // Copy the devices you just added to the other hub; don't delete anything.
    // The first argument is false, numToAdd is 0, copy-devices is true, and the delete arguments are both false
    dotnet run false 0 true false false 
```

### <a name="using-environment-variables-for-the-connection-strings"></a>接続文字列用の環境変数を使用する

1. サンプルを実行するには、古い IoT ハブと新しい IoT ハブへの接続文字列、および一時作業ファイル用のストレージ アカウントが必要です。 これらの値は、環境変数に格納されます。

1. 接続文字列の値を取得するには、[Azure portal](https://portal.azure.com) にサインインします。 

1. 接続文字列を、取得できる場所 (メモ帳など) に置いておきます。 以下の内容をコピーして、接続文字列を直接貼り付けることができます。 等号の前後にスペースを追加したり、変数名を変更したりしないでください。 また、接続文字列を二重引用符で囲む必要はありません。 ストレージ アカウントの接続文字列を引用符で囲むと、機能しなくなります。

   Windows の場合、次のように環境変数を設定します。

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   Linux の場合、次のように環境変数を定義します。

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. IoT ハブの接続文字列については、ポータルの各ハブにアクセスしてください。 ハブは、 **[リソース]** で検索できます。 リソース グループがわかっている場合は、 **[リソース グループ]** にアクセスしてリソース グループを選択し、そのリソース グループのアセットの一覧からハブを選択します。 

1. ハブの設定から **[共有アクセス ポリシー]** を選択し、 **[iothubowner]** を選択していずれかの接続文字列をコピーします。 宛先ハブでも同じ操作を行います。 ハブを適切な SET コマンドに追加します。

1. ストレージ アカウントの接続文字列については、 **[リソース]** またはその **[リソース グループ]** の下からストレージ アカウントを見つけて開きます。 
   
1. [設定] セクションの下にある **[アクセス キー]** を選択し、いずれかの接続文字列をコピーします。 適切な SET コマンドの接続文字列をテキスト ファイルに配置します。 

これで、SET コマンドを使用して環境変数がファイル内に作成され、コマンド ライン引数がわかるようになりました。 ここで、サンプルを実行してみましょう。

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>サンプル アプリケーションの実行とコマンド ライン引数の使用

1. コマンド プロンプト ウィンドウを開きます。 [Windows] を選択し、「`command prompt`」と入力して、コマンド プロンプト ウィンドウを表示します。

1. 環境変数を設定するコマンドを一度に 1 つずつコピーし、コマンド プロンプト ウィンドウに貼り付けて Enter キーを押します。 操作が完了したら、コマンド プロンプト ウィンドウに「`SET`」と入力して、環境変数とその値を確認します。 これらをコマンド プロンプト ウィンドウにコピーした後は、新しいコマンド プロンプト ウィンドウを開かない限りもう一度コピーする必要はありません。

1. コマンド プロンプト ウィンドウで、./ ImportExportDevicesSample.csproj ファイルがある ImportExportDevicesSample にたどり着くまでディレクトリを変更します。 次に、以下の内容を入力し、コマンド ライン引数を含めます。

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    dotnet コマンドは、アプリケーションをビルドして実行します。 アプリケーションを実行するときにオプションを渡すため、アプリケーションを実行するたびにこれらの値を変更できます。 たとえば、一度実行して新しいデバイスを作成し、再度実行して、それらのデバイスを新しいハブにコピーするなどが可能です。 また、1 回の実行ですべての手順を実行することもできます。ただし、複製が完了するまでデバイスを削除しないことをお勧めします。 次に、1000 デバイスを作成し、そのデバイスを他のハブにコピーする例を示します。

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    デバイスが正常にコピーされたことを確認したら、次のように、ソース ハブからデバイスを削除できます。

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>Visual Studio を使用してサンプル アプリケーションを実行する

1. Visual Studio でアプリケーションを実行する場合は、現在のディレクトリを IoTHubServiceSamples.sln ファイルが格納されているフォルダーに変更します。 次に、コマンド プロンプト ウィンドウでこのコマンドを実行して、Visual Studio でソリューションを開きます。 この操作は、変数が認識されるよう、環境変数を設定したコマンド ウィンドウで行う必要があります。

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. プロジェクト "*ImportExportDevicesSample"* を右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。    
    
1. Program.cs の先頭にある変数を、5 つのオプションの ImportExportDevicesSample フォルダーに設定します。

   ``` csharp
   // Add randomly created devices to the source hub.
   private static bool addDevices = true;
   //If you ask to add devices, this will be the number added.
   private static int numToAdd = 0; 
   // Copy the devices from the source hub to the destination hub.
   private static bool copyDevices = false;
   // Delete all of the devices from the source hub. (It uses the IoTHubConnectionString).
   private static bool deleteSourceDevices = false;
   // Delete all of the devices from the destination hub. (Uses the DestIotHubConnectionString).
   private static bool deleteDestDevices = false;
   ```

1. F5 キーを選択してアプリケーションを実行します。 実行が完了したら、結果を表示できます。

### <a name="view-the-results"></a>結果の確認 

[Azure portal](https://portal.azure.com) でデバイスを表示できるため、新しい場所にデバイスがあることを確認できます。

1. [Azure portal](https://portal.azure.com) を使用して新しいハブにアクセスします。 ハブを選択し、 **[IoT デバイス]** を選択します。 古いハブからコピーしたデバイスが、複製されたハブに表示されます。 また、複製されたハブのプロパティを表示することもできます。 

1. インポート/エクスポート エラーを確認するには、[Azure portal](https://portal.azure.com) で Azure ストレージ アカウントに移動し、`ImportErrors.log` の `devicefiles` コンテナーを探します。 このファイルが空の場合 (サイズが 0 の場合)、エラーが発生しなかったことを意味します。 同じデバイスを 1 回以上インポートしようとすると、デバイスは 2 回目以降拒否され、エラー メッセージがログ ファイルに追加されます。

### <a name="committing-the-changes"></a>変更をコミットする 

この時点で、ハブを新しい場所にコピーし、デバイスを新しい複製に移行しました。 次に、デバイスが複製されたハブで動作するように変更を行う必要があります。

変更をコミットするには、次の手順を実行する必要があります。 

* 各デバイスを更新して、IoT Hub ホスト名が新しいハブを指すように IoT Hub ホスト名を変更します。 この操作は、最初にデバイスをプロビジョニングしたときに使用した方法を使用して行う必要があります。

* 古いハブを参照しているすべてのアプリケーションを、新しいハブを指すように変更します。

* 完了すると、新しいハブが起動して実行されます。 古いハブにはアクティブなデバイスがなく、切断状態になっている必要があります。 

### <a name="rolling-back-the-changes"></a>変更をロール バックする

変更をロール バックする場合は、次の手順を実行します。

* 各デバイスを更新して、IoT Hub ホスト名が古いハブを指すように IoT Hub ホスト名を変更します。 この操作は、最初にデバイスをプロビジョニングしたときに使用した方法を使用して行う必要があります。

* 新しいハブを参照しているすべてのアプリケーションを、古いハブを指すように変更します。 たとえば、Azure Analytics を使用している場合は、[Azure Stream Analytics の入力](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub)の再構成が必要になることがあります。

* 新しい Hub を削除します。 

* ルーティング リソースがある場合は、古いハブの構成が正しいルーティング構成を参照している必要があり、ハブの再起動後もそれらのリソースを使用する必要があります。

### <a name="checking-the-results"></a>結果を確認する 

結果を確認するには、新しい場所にあるハブをポイントするよう IoT ソリューションを変更して実行します。 言い換えると、前のハブで実行していた同じ操作を新しいハブで実行し、正常に動作することを確認してください。 

ルーティングを実装している場合は、メッセージがリソースに正しくルーティングされていることをテストして確認します。

## <a name="clean-up"></a>クリーンアップ

新しいハブが稼働していて、デバイスが正常に動作していることを確実に確認するまで、クリーンアップしないでください。 また、機能を使用している場合は、必ずルーティングをテストしてください。 準備ができたら、次の手順を実行して、古いリソースをクリーンアップします。

* まだの場合は、古いハブを削除します。 これにより、ハブからすべてのアクティブなデバイスが削除されます。

* 新しい場所に移動したルーティング リソースがある場合は、古いルーティング リソースを削除できます。

## <a name="next-steps"></a>次の手順

IoT ハブを新しいリージョンにある新しいハブに複製し、デバイスで完了しました。 IoT Hub で ID レジストリに対して一括操作を実行する方法の詳細については、[「IoT Hub デバイス ID の一括でのインポートおよびエクスポート」](iot-hub-bulk-identity-mgmt.md)を参照してください。

IoT Hub とハブの開発の詳細については、以下の記事を参照してください。

* [IoT Hub 開発者ガイド](iot-hub-devguide.md)

* [IoT Hub のルーティングのチュートリアル](tutorial-routing.md)

* [IoT Hub のデバイス管理概要](iot-hub-device-management-overview.md)

* サンプル アプリケーションをデプロイする場合は、「[.NET Core アプリケーションの展開](https://docs.microsoft.com/dotnet/core/deploying/index)」を参照してください。