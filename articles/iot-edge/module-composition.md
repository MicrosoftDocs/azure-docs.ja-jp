---
title: デプロイ マニフェストを使ってモジュールとルートをデプロイする - Azure IoT Edge
description: デプロイ マニフェストを使ってデプロイするモジュールを宣言する方法、モジュールをデプロイする方法、モジュール間のメッセージ ルートを作成する方法について説明します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2e3f1891a786751365a0bea58097e03bd41f85bb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489921"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>IoT Edge にモジュールをデプロイしてルートを確立する方法について説明します。

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

各 IoT Edge デバイスでは、$edgeAgent と $edgeHub という少なくとも 2 つのモジュールが実行されます。これらは IoT Edge ランタイムに含まれています。 IoT Edge デバイスは、任意の数のプロセスに対して複数の追加モジュールを実行できます。 インストールするモジュールとそれらを連携させるための構成方法をデバイスに伝えるには、配置マニフェストを使用します。

*デプロイ マニフェスト* は、次の内容が記述された JSON ドキュメントです。

* 3 つのコンポーネントを含む **IoT Edge エージェント** モジュール ツイン。
  * デバイスで実行される各モジュールのコンテナー イメージ。
  * モジュール イメージを含むプライベート コンテナー レジストリにアクセスするための資格情報。
  * 各モジュールの作成と管理の方法に関する指示。
* **IoT Edge ハブ** モジュール ツイン。モジュール間および最終的には IoT Hub へのメッセージ フローの方法が含まれます。
* さらに別のモジュール ツインがある場合は、その必要なプロパティ (省略可能)。

配置マニフェストを使用してすべての IoT Edge デバイスを構成する必要があります。 新しくインストールされた IoT Edge ランタイムは、有効なマニフェストで構成されるまでエラー コードを報告します。

Azure IoT Edge チュートリアルでは、Azure IoT Edge ポータルでウィザードを使用することによってデプロイ マニフェストを作成します。 また、REST または IoT Hub サービス SDK を使用して、プログラムでデプロイ マニフェストを適用することもできます。 詳細については、[IoT Edge のデプロイ](module-deployment-monitoring.md)に関する記事を参照してください。

## <a name="create-a-deployment-manifest"></a>配置マニフェストの作成

大まかに言えば、配置マニフェストとは、必要なプロパティを使用して構成されたモジュール ツインのリストです。 インストールするモジュールとその構成方法は、配置マニフェストから IoT Edge デバイス (1 つまたは複数のデバイス) に伝えられます。 配置マニフェストには、"*desired プロパティ*" (必要なプロパティ) がモジュール ツインごとに記述されています。 IoT Edge デバイスは、モジュールごとに "*reported プロパティ*" (レポートされるプロパティ) を返します。

すべての配置マニフェストに、`$edgeAgent` と `$edgeHub` という 2 つのモジュールが必要です。 これらのモジュールは、IoT Edge デバイスとそこで実行されるモジュールを管理する IoT Edge ランタイムの構成要素です。 これらのモジュールの詳細については、[IoT Edge ランタイムとそのアーキテクチャの概要](iot-edge-runtime.md)に関するページを参照してください。

この 2 つのランタイム モジュールに加え、独自のモジュールを 50 個まで追加して、IoT Edge デバイス上で動作させることができます。

IoT Edge ランタイム (edgeAgent と edgeHub) さえ含まれていれば配置マニフェストは有効です。

配置マニフェストの構造は次のとおりです。

```json
{
  "modulesContent": {
    "$edgeAgent": { // required
      "properties.desired": {
        // desired properties of the IoT Edge agent
        // includes the image URIs of all deployed modules
        // includes container registry credentials
      }
    },
    "$edgeHub": { //required
      "properties.desired": {
        // desired properties of the IoT Edge hub
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
    }
  }
}
```

## <a name="configure-modules"></a>モジュールの構成

デプロイに含まれるモジュールを IoT Edge ランタイムでどのようにインストールするかを定義します。 IoT Edge エージェントは、IoT Edge デバイスのインストール、更新、状態レポートを管理するランタイム コンポーネントです。 そのため、$edgeAgent モジュール ツインには、すべてのモジュールの構成情報と管理情報が含まれます。 この情報には、IoT Edge エージェント自体の構成パラメーターが含まれます。

$EdgeAgent プロパティは次の構造に従います。

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": {
          "settings":{
            "registryCredentials":{
              // give the IoT Edge agent access to container images that aren't public
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
          "module1": {
            // configuration and management details
          },
          "module2": {
            // configuration and management details
          }
        }
      }
    },
    "$edgeHub": { ... },
    "module1": { ... },
    "module2": { ... }
  }
}
```

IoT Edge エージェント スキーマ バージョン 1.1 は IoT Edge バージョン 1.0.10 と共にリリースされ、モジュールの起動順序機能を使用可能にします。 バージョン 1.0.10 以降を実行している IoT Edge デプロイでは、スキーマ バージョン 1.1 の使用をお勧めします。

### <a name="module-configuration-and-management"></a>モジュールの構成と管理

IoT Edge エージェントの必要なプロパティの一覧では、IoT Edge デバイスにデプロイするモジュールと、その構成と管理の方法を定義します。

含めることが可能または必須のプロパティの完全な一覧については、[IoT Edge エージェントおよび IoT Edge ハブのプロパティ](module-edgeagent-edgehub.md)に関するページをご覧ください。

次に例を示します。

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "runtime": { ... },
        "systemModules": {
          "edgeAgent": { ... },
          "edgeHub": { ... }
        },
        "modules": {
          "module1": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 2,
            "settings": {
              "image": "myacr.azurecr.io/module1:latest",
              "createOptions": "{}"
            }
          },
          "module2": { ... }
        }
      }
    },
    "$edgeHub": { ... },
    "module1": { ... },
    "module2": { ... }
  }
}
```

すべてのモジュールには、**settings** プロパティがあり、これにはモジュールの **image** (コンテナー レジストリ内のコンテナー イメージのアドレス)、および起動時にイメージを構成する任意の **createOptions** が含まれます。 詳細については、「[IoT Edge モジュールのコンテナー作成オプションを構成する方法](how-to-use-create-options.md)」を参照してください。

edgeHub モジュールとカスタム モジュールには、IoT Edge エージェントに管理方法を指示する 3 つのプロパティもあります。

* **状態**: 最初のデプロイ時にモジュールを実行中にするか、停止するか。 必須です。
* **restartPolicy**:モジュールが停止する場合は、IoT Edge エージェントがモジュールを再起動する必要があるか、およびそのタイミング。 必須です。
* **startupOrder**:*IoT Edge バージョン 1.0.10 で導入されました。* IoT Edge エージェントが最初にデプロイされたときにモジュールを起動する順序。 順序は整数で宣言され、スタートアップ値が 0 であるモジュールが最初に起動し、より大きい数値のものが続きます。 edgeAgent モジュールは、常に最初に起動するため、スタートアップ値がありません。 任意。

  IoT Edge エージェントはスタートアップ値の順にモジュールを起動しますが、各モジュールの起動が終了するのを待ってから次に移ります。

  スタートアップ順序は、一部のモジュールが他のモジュールに依存している場合に便利です。 たとえば、edgeHub モジュールを最初に起動して、他のモジュールが開始されたときにメッセージがルーティングされるよう準備を整えることができます。 または、ストレージ モジュールにデータを送信するモジュールより前に、ストレージ モジュールを開始することもできます。 ただし、他のモジュールの障害を処理するようにモジュールを設計する必要があります。 これは、いつでも停止したり再起動したりする可能性が任意の回数あるというコンテナーの性質によるものです。

## <a name="declare-routes"></a>ルートの宣言

IoT Edge ハブは、モジュール、IoT ハブ、リーフ デバイス間の通信を管理します。 そのため、$edgeHub モジュール ツインには、デプロイ内でのメッセージの受け渡し方法を宣言する、*routes* と呼ばれる必要なプロパティが含まれています。 同じデプロイ内にルートを複数持たせることができます。

ルートは、 **$edgeHub** の必要なプロパティで、次の構文を使用して宣言します。

```json
{
  "modulesContent": {
    "$edgeAgent": { ... },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.1",
        "routes": {
          "route1": "FROM <source> WHERE <condition> INTO <sink>",
          "route2": {
            "route": "FROM <source> WHERE <condition> INTO <sink>",
            "priority": 0,
            "timeToLiveSecs": 86400
          }
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    },
    "module1": { ... },
    "module2": { ... }
  }
}
```

IoT Edge ハブ スキーマ バージョン 1.1 は IoT Edge バージョン 1.0.10 と共にリリースされ、ルートの優先順位付け機能と有効期限を使用可能にします。 バージョン 1.0.10 以降を実行している IoT Edge デプロイでは、スキーマ バージョン 1.1 の使用をお勧めします。

各ルートには、メッセージの送信元である *ソース* と、メッセージの送信先である *シンク* が必要です。 *条件* は、メッセージをフィルター処理するために使用できる省略可能な部分です。

メッセージが最初に処理されるようにする *優先順位* をルートに割り当てることができます。 この機能は、アップストリーム接続が脆弱または制限付きであり、標準のテレメトリ メッセージより優先される重要なデータがある場合に役立ちます。

### <a name="source"></a>source

ソースでは、メッセージがどこから送信されるのかを指定します。 IoT Edge は、モジュールまたはリーフ デバイスからメッセージをルーティングすることができます。

IoT SDK を使用することにより、モジュールは、ModuleClient クラスを使用してメッセージの特定の出力キューを宣言することができます。 出力キューは必要ではありませんが、複数のルートを管理するのに役立ちます。 リーフ デバイスは、IoT SDK の DeviceClient クラスを使用して、IoT Hub にメッセージを送信するのと同じ方法で IoT Edge ゲートウェイ デバイスにメッセージを送信することができます。 詳細については、「[Azure IoT Hub SDK の概要と使用方法](../iot-hub/iot-hub-devguide-sdks.md)」を参照してください。

ソース プロパティは、次のいずれかの値にすることができます。

| source | 説明 |
| ------ | ----------- |
| `/*` | 任意のモジュールまたはリーフ デバイスからのすべての device-to-cloud メッセージまたはツイン変更通知 |
| `/twinChangeNotifications` | 任意のモジュールまたはリーフ デバイスから送信されるツイン変更 (reported プロパティ) |
| `/messages/*` | モジュールによって (なんらかの出力を通じてまたは出力なしで) 送信される、またはリーフ デバイスによって送信される device-to-cloud メッセージ |
| `/messages/modules/*` | 何らかの出力と共に、または出力なしでモジュールによって送信された、デバイスからクラウドへの任意のメッセージ |
| `/messages/modules/<moduleId>/*` | なんらかの出力を通じて、または出力なしで特定のモジュールによって送信される任意の device-to-cloud メッセージ |
| `/messages/modules/<moduleId>/outputs/*` | なんらかの出力を通じて特定のモジュールによって送信される任意の device-to-cloud メッセージ |
| `/messages/modules/<moduleId>/outputs/<output>` | 特定の出力を通じて特定のモジュールによって送信される任意の device-to-cloud メッセージ |

### <a name="condition"></a>条件

条件は、ルートの宣言では省略可能です。 ソースからシンクへのメッセージをすべて渡す場合は、**WHERE** 句全体をそのまま削除します。 または、[IoT Hub クエリ言語](../iot-hub/iot-hub-devguide-routing-query-syntax.md)を使用して、条件を満たす特定のメッセージまたはメッセージの種類をフィルター処理することができます。 IoT Edge のルートは、ツインのタグやプロパティに基づくメッセージのフィルタリングをサポートしません。

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

### <a name="priority-and-time-to-live"></a>優先順位と有効期限

ルートは、ルートを定義する文字列として、またはルート文字列、優先順位の整数、および有効期限の整数を使用するオブジェクトとして宣言できます。

オプション 1: 

   ```json
   "route1": "FROM <source> WHERE <condition> INTO <sink>",
   ```

オプション 2、IoT Edge バージョン 1.0.10 と IoT Edge ハブ スキーマ バージョン 1.1 で導入:

   ```json
   "route2": {
     "route": "FROM <source> WHERE <condition> INTO <sink>",
     "priority": 0,
     "timeToLiveSecs": 86400
   }
   ```

**priority** の値は 0 ～ 9 にすることができます (0 と 9 を含む)。ここで、0 が最も高い優先順位です。 メッセージは、エンドポイントに基づいてキューに登録されます。 特定のエンドポイントを対象とするすべての優先度 0 メッセージは、同じエンドポイントを対象とする優先度 1 メッセージが処理される前にすべて処理されます。 同じエンドポイントに対して複数のルートが同じ優先順位を持つ場合、そのメッセージは先着順で処理されます。 優先順位が指定されていない場合、そのルートは最も低い優先順位に割り当てられます。

**timeToLiveSecs** プロパティは、明示的に設定されていない限り、IoT Edge ハブの **storeAndForwardConfiguration** からその値を継承します。 値には正の整数を指定できます。

優先キューを管理する方法の詳細については、[ルートの優先順位と有効期限](https://github.com/Azure/iotedge/blob/master/doc/Route_priority_and_TTL.md)に関するリファレンス ページをご覧ください。

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
        "schemaVersion": "1.1",
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
              "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 0,
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
              "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "startupOrder": 2,
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
            "startupOrder": 1,
            "env": {
              "tempLimit": {"value": "100"}
            },
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
        "schemaVersion": "1.1",
        "routes": {
          "sensorToFilter": {
            "route": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
            "priority": 0,
            "timeToLiveSecs": 1800
          },
          "filterToIoTHub": {
            "route": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream",
            "priority": 1,
            "timeToLiveSecs": 1800
          }
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 100
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>次のステップ

* $edgeAgent および $edgeHub に含めることができるプロパティおよび含める必要があるプロパティの完全な一覧については、[IoT Edge エージェントおよび IoT Edge ハブのプロパティ](module-edgeagent-edgehub.md)に関するページをご覧ください。

* これで IoT Edge モジュールがどのように使用されるかがわかったので、「[IoT Edge モジュールを開発するための要件とツールについて理解する](module-development.md)」に進みます。
