---
title: デプロイ マニフェストを使ってモジュールとルートを宣言する Azure IoT Edge |Microsoft Docs
description: デプロイ マニフェストを使ってデプロイするモジュールを宣言する方法、モジュールをデプロイする方法、モジュール間のメッセージ ルートを作成する方法について説明します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/28/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f4a562cab445398986c1b8f379f6cb90ca843342
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758084"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>IoT Edge にモジュールをデプロイしてルートを確立する方法について説明します。

各 IoT Edge デバイスでは、$edgeAgent と $edgeHub という少なくとも 2 つのモジュールが実行されます。これらは IoT Edge ランタイムに含まれています。 加えて、IoT Edge デバイスでは複数のモジュールを実行して、任意の数のプロセスを実行できます。 デバイスには、これらすべてのモジュールを一度にデプロイすることになります。そのため IoT Edge には、どのモジュールをインストールし、それらをどのように構成して連携させるかを宣言するための手段が用意されています。 

*デプロイ マニフェスト*は、次の内容が記述された JSON ドキュメントです。

* **IoT Edge エージェント** モジュール ツイン。各モジュールのコンテナー イメージ、プライベート コンテナー レジストリにアクセスするための資格情報、および各モジュールを作成し、管理する方法についての手順が含まれます。
* **IoT Edge ハブ** モジュール ツイン。モジュール間および最終的には IoT Hub へのメッセージ フローの方法が含まれます。
* さらに別のモジュール ツインがある場合は、オプションとして、その必要なプロパティ。

配置マニフェストを使用してすべての IoT Edge デバイスを構成する必要があります。 新しくインストールされた IoT Edge ランタイムは、有効なマニフェストで構成されるまでエラー コードを報告します。 

Azure IoT Edge チュートリアルでは、Azure IoT Edge ポータルでウィザードを使用することによってデプロイ マニフェストを作成します。 また、REST または IoT Hub サービス SDK を使用して、プログラムでデプロイ マニフェストを適用することもできます。 詳細については、[IoT Edge のデプロイ](module-deployment-monitoring.md)に関する記事を参照してください。

## <a name="create-a-deployment-manifest"></a>配置マニフェストの作成

大まかに言えば、配置マニフェストとは、必要なプロパティを使用して構成されたモジュール ツインのリストです。 インストールするモジュールとその構成方法は、配置マニフェストから IoT Edge デバイス (1 つまたは複数のデバイス) に伝えられます。 配置マニフェストには、"*desired プロパティ*" (必要なプロパティ) がモジュール ツインごとに記述されています。 IoT Edge デバイスは、モジュールごとに "*reported プロパティ*" (レポートされるプロパティ) を返します。 

すべての配置マニフェストに、`$edgeAgent` と `$edgeHub` という 2 つのモジュールが必要です。 これらのモジュールは、IoT Edge デバイスとそこで実行されるモジュールを管理する IoT Edge ランタイムの構成要素です。 これらのモジュールの詳細については、[IoT Edge ランタイムとそのアーキテクチャの概要](iot-edge-runtime.md)に関するページを参照してください。

この 2 つのランタイム モジュールに加え、独自のモジュールを 20 個まで追加して、IoT Edge デバイス上で動作させることができます。 

IoT Edge ランタイム (edgeAgent と edgeHub) さえ含まれていれば配置マニフェストは有効です。

配置マニフェストの構造は次のとおりです。

```json
{
    "modulesContent": {
        "$edgeAgent": { // required
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
                // includes container registry credentials
            }
        },
        "$edgeHub": { //required
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "module1": {  // optional
            "properties.desired": {
                // desired properties of module1
            }
        },
        "module2": {  // optional
            "properties.desired": {
                // desired properties of module2
            }
        },
        ...
    }
}
```

## <a name="configure-modules"></a>モジュールの構成

デプロイに含まれるモジュールを IoT Edge ランタイムでどのようにインストールするかを定義します。 IoT Edge エージェントは、IoT Edge デバイスのインストール、更新、状態レポートを管理するランタイム コンポーネントです。 そのため、$edgeAgent モジュール ツインには、すべてのモジュールの構成情報と管理情報が必要です。 この情報には、IoT Edge エージェント自体の構成パラメーターが含まれます。 

含めることができるプロパティおよび必須プロパティの完全な一覧については、[IoT Edge エージェントおよび IoT Edge ハブのプロパティ](module-edgeagent-edgehub.md)に関するページをご覧ください。

$EdgeAgent プロパティは次の構造に従います。

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
            "settings":{
                "registryCredentials":{ // give the edge agent access to container images that aren't public
                    }
                }
            }
        },
        "systemModules": {
            "edgeAgent": {
                // configuration and management details
            },
            "edgeHub": {
                // configuration and management details
            }
        },
        "modules": {
            "module1": { // optional
                // configuration and management details
            },
            "module2": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>ルートの宣言

IoT Edge ハブは、モジュール、IoT ハブ、リーフ デバイス間の通信を管理します。 そのため、$edgeHub モジュール ツインには、デプロイ内でのメッセージの受け渡し方法を宣言する、*routes* と呼ばれる必要なプロパティが含まれています。 同じデプロイ内にルートを複数持たせることができます。

ルートは、**$edgeHub** の必要なプロパティで、次の構文を使用して宣言します。

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "route1": "FROM <source> WHERE <condition> INTO <sink>",
            "route2": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

ソースとシンクはすべてのルートに必要ですが、条件は、メッセージをフィルター処理するために使用できる省略可能な部分です。 


### <a name="source"></a>ソース

ソースでは、メッセージがどこから送信されるのかを指定します。 IoT Edge は、リーフ デバイスまたはモジュールからのメッセージをルーティングすることができます。

ソース プロパティは、次のいずれかの値にすることができます。

| ソース | 説明 |
| ------ | ----------- |
| `/*` | 任意のモジュールまたはリーフ デバイスからのすべての device-to-cloud メッセージまたはツイン変更通知 |
| `/twinChangeNotifications` | 任意のモジュールまたはリーフ デバイスから送信されるツイン変更 (reported プロパティ) |
| `/messages/*` | なんらかの出力を通じて、または出力なしでモジュールまたはリーフ デバイスによって送信される任意の device-to-cloud メッセージ |
| `/messages/modules/*` | 何らかの出力と共に、または出力なしでモジュールによって送信された、デバイスからクラウドへの任意のメッセージ |
| `/messages/modules/<moduleId>/*` | なんらかの出力を通じて、または出力なしで特定のモジュールによって送信される任意の device-to-cloud メッセージ |
| `/messages/modules/<moduleId>/outputs/*` | なんらかの出力を通じて特定のモジュールによって送信される任意の device-to-cloud メッセージ |
| `/messages/modules/<moduleId>/outputs/<output>` | 特定の出力を通じて特定のモジュールによって送信される任意の device-to-cloud メッセージ |

### <a name="condition"></a>条件
条件は、ルートの宣言では省略可能です。 シンクからソースへのメッセージをすべて渡す場合は、**WHERE** 句全体をそのまま削除します。 または、[IoT Hub クエリ言語](../iot-hub/iot-hub-devguide-routing-query-syntax.md)を使用して、条件を満たす特定のメッセージまたはメッセージの種類をフィルター処理することができます。 IoT Edge のルートは、ツインのタグやプロパティに基づくメッセージのフィルタリングをサポートしません。 

IoT Edge のモジュール間を通過するメッセージは、デバイスと Azure IoT Hub の間を通過するメッセージと同じ形式になります。 すべてのメッセージは JSON で書式設定され、パラメーターとして **systemProperties**、**appProperties**、**body** が与えられます。 

次の構文を利用して、この 3 つのパラメーターを中心にクエリを構築できます。 

* システム プロパティ: `$<propertyName>` または `{$<propertyName>}`
* アプリケーション プロパティ: `<propertyName>`
* 本文プロパティ: `$body.<propertyName>` 

メッセージ プロパティのクエリを作成する方法の例は、「[デバイスからクラウドへのメッセージ ルートのクエリ式](../iot-hub/iot-hub-devguide-routing-query-syntax.md)」を参照してください。

IoT Edge に固有の例としては、たとえば、リーフ デバイスからゲートウェイ デバイスに到着したメッセージにフィルターを適用します。 モジュールから送信されるメッセージには、**connectionModuleId** と呼ばれるシステム プロパティが含まれます。 したがって、リーフ デバイスからメッセージを直接 IoT Hub にルーティングする場合は、次のルートを使用してモジュールのメッセージを除外します。

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>シンク
シンクでは、メッセージの送信先が定義されます。 メッセージを受信できるのは、モジュールと IoT Hub だけです。 他のデバイスにメッセージをルーティングすることはできません。 シンク プロパティにワイルドカードのオプションはありません。 

シンク プロパティは、次のいずれかの値にすることができます。

| シンク | 説明 |
| ---- | ----------- |
| `$upstream` | IoT Hub にメッセージを送信する |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | 特定のモジュールの特定の入力にメッセージを送信する |

IoT Edge は、At-Least-Once 保証を提供します。 IoT Edge ハブは、ルートでそのシンクにメッセージを配信できなかった場合のために、ローカルにメッセージを保存します。 たとえば、IoT Edge ハブが IoT Hub に接続できない場合や、ターゲット モジュールが接続されていない場合です。

IoT Edge ハブでは、[IoT Edge ハブの必要なプロパティ](module-edgeagent-edgehub.md)の `storeAndForwardConfiguration.timeToLiveSecs` プロパティで指定した時間まで、メッセージが格納されます。

## <a name="define-or-update-desired-properties"></a>必要なプロパティの定義または更新 

配置マニフェストでは、IoT Edge デバイスにデプロイされるモジュールごとに、必要なプロパティを指定します。 現時点でモジュール ツインにある必要なプロパティは、配置マニフェストにある必要なプロパティによってすべて上書きされます。

モジュール ツインの必要なプロパティを配置マニフェストで指定していない場合、IoT Hub はどのような方法であれ、モジュール ツインを変更することはありません。 必要なプロパティを自分でプログラムから設定することはできます。

デバイス ツインを変更できるのと同じメカニズムを使用してモジュール ツインを変更できます。 詳細については、[モジュール ツイン開発者ガイド](../iot-hub/iot-hub-devguide-module-twins.md)をご覧ください。   

## <a name="deployment-manifest-example"></a>デプロイ マニフェストの例

有効な配置マニフェスト ドキュメントの例を次に示します。

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
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "<password>",
                "address": "myacr.azurecr.io"
              }
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": ""
            }
          }
        },
        "modules": {
          "tempSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>次の手順

* $edgeAgent および $edgeHub に含めることができるプロパティおよび含める必要があるプロパティの完全な一覧については、[IoT Edge エージェントおよび IoT Edge ハブのプロパティ](module-edgeagent-edgehub.md)に関するページをご覧ください。

* これで IoT Edge モジュールがどのように使用されるかがわかったので、「[IoT Edge モジュールを開発するための要件とツールについて理解する](module-development.md)」に進みます。
