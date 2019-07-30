---
title: Azure IoT Hub を使用した大規模な自動デバイス管理 (CLI) | Microsoft Docs
description: 複数の IoT デバイスに構成を割り当てるには、Azure IoT Hub の自動デバイス管理を使用します。
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: chrisgre
ms.openlocfilehash: d57dbbdd7614d09d52fef0f613c43d4ca1d08136
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485861"
---
# <a name="automatic-iot-device-management-at-scale-using-the-azure-cli"></a>Azure CLI を使用した大規模な自動 IoT デバイス管理

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Azure IoT Hub の自動デバイス管理では、多数のデバイスを管理するという反復的かつ複雑なタスクの多くを自動化できます。 自動デバイス管理では、対象となる一連のデバイスをプロパティに基づいて設定し、必要な構成を定義しておくと、デバイスがスコープに適合したときに、IoT Hub によってデバイスが更新されます。 この更新は、_自動デバイス構成_を使用して実行されます。これにより、操作やコンプライアンスの概況を把握し、マージや競合に対処し、構成を段階的なアプローチで展開することができます。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

自動デバイス管理では、一連のデバイス ツインが目的のプロパティで更新され、デバイス ツインで報告されたプロパティに基づくサマリーが報告されます。  この機能では、*構成*という新しいクラスと JSON ドキュメントが導入されています。構成には、次の 3 つの要素が含まれています。

* **ターゲットの条件**: 更新されるデバイス ツインのスコープ (範囲) を定義します。 ターゲットの条件は、デバイス ツインのタグや報告されたプロパティに関するクエリとして指定されます。

* **ターゲット コンテンツ**: ターゲットのデバイス ツイン内のどのプロパティを追加または更新するのかを定義します。 このコンテンツには、変更するプロパティのセクションへのパスが含まれます。

* **メトリック**: 各種の構成状態 (**成功**、**進行中**、**エラー**など) の集計カウントを定義します。 カスタム メトリックは、デバイス ツインから報告されたプロパティに関するクエリとして指定されます。  システム メトリックは、ツインの更新状態を測定する既定のメトリックです (対象となるデバイス ツインの数や、正常に更新されたツインの数など)。

自動デバイス構成は、構成が作成された直後に初めて実行され、その後は 5 分間隔で実行されます。 自動デバイス構成が実行されるたびに、メトリックのクエリが実行されます。

## <a name="cli-prerequisites"></a>CLI の前提条件

* Azure サブスクリプション内の [IoT ハブ](../iot-hub/iot-hub-create-using-cli.md)。 
* ご使用の環境内の [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 Azure CLI のバージョンは、少なくとも 2.0.24 以降である必要があります。 検証するには、`az –-version` を使用します。 このバージョンでは、az 拡張機能のコマンドがサポートされ、Knack コマンド フレームワークが導入されています。 
* [Azure CLI 向け IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)。

## <a name="implement-device-twins-to-configure-devices"></a>デバイスを構成するためのデバイス ツインを実装する

自動デバイス構成では、クラウドとデバイスとの間で状態を同期するために、デバイス ツインを使う必要があります。  デバイス ツインの使用方法については、「[IoT Hub のデバイス ツインの理解と使用](iot-hub-devguide-device-twins.md)」をご覧ください。

## <a name="identify-devices-using-tags"></a>タグを使用したデバイスの識別

構成を作成する前に、影響の範囲内に含めるデバイスを指定する必要があります。 Azure IoT Hub では、デバイス ツイン内のタグを使用してデバイスを識別します。 各デバイスには複数のタグを設定することができ、ソリューションに適した任意の方法で定義できます。 たとえば、複数の場所のデバイスを管理する場合には、デバイス ツインに次のタグを追加します。

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>ターゲット コンテンツとメトリックを定義する

ターゲット コンテンツとメトリック クエリは、デバイス ツインの設定される目的のプロパティと、測定対象の報告されるプロパティを記述する JSON ドキュメントとして指定されます。  Azure CLI を使用して自動デバイス構成を作成するには、対象のコンテンツとメトリックを .txt ファイルとしてローカルに保存します。 コマンドを実行して構成をデバイスに適用するときには、後のセクションに示すファイル パスを使用します。

基本的なターゲット コンテンツ サンプルを次に示します。

```json
{
  "content": {
    "deviceContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

メトリック クエリの例を次に示します。

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

## <a name="create-a-configuration"></a>構成を作成する

ターゲットコンテンツとメトリックから成る構成を作成して、ターゲット デバイスを構成します。 

次のコマンドを使用して、構成を作成します。

```cli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-id** - IoT ハブに作成される構成の名前です。 構成に一意の名前を付けます。名前は最大 128 文字の英小文字で指定します。 スペースや、無効な文字は使用しないでください。`& ^ [ ] { } \ | " < > /`

* --**labels** - 構成を追跡するためのラベルを追加します。 ラベルは、デプロイを説明する、[名前] と [値] で一組になっています。 たとえば、`HostPlatform, Linux` や `Version, 3.0.1` のようにします。

* --**content** - インライン JSON 、またはツインの必要なプロパティとして設定されるターゲット コンテンツへのファイル パス。 

* --**hub-name** - 構成が作成される IoT ハブの名前です。 ハブは現在のサブスクリプションにある必要があります。 コマンド `az account set -s [subscription name]` を使用して目的のサブスクリプションに切り替えます。

* --**target-condition** - どのデバイスがこの構成の対象となるかを指定する対象の条件を入力します。 条件は、デバイス ツイン タグか、デバイス ツインで必要なプロパティに基づいて指定し、式の形式に一致させる必要があります。 たとえば、`tags.environment='test'` または `properties.desired.devicemodel='4000x'` です。 

* --**priority** - 正の整数にする必要があります。 同じデバイスで複数の構成がターゲットとなっている場合は、優先度の数値が最も大きい構成が適用されます。

* --**metrics** - メトリック クエリへのファイルパスです。 メトリックは、構成のコンテンツを適用した後にデバイスから報告される、各種の状態の集計カウントを示すものです。 たとえば、保留設定の変更に対するメトリック、エラーのメトリック、正常設定の変更に対するメトリックなどを作成できます。 

## <a name="monitor-a-configuration"></a>構成の監視

次のコマンドを使用して、構成の内容を表示します。

```cli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id** - IoT ハブに存在する構成の名前です。

* --**hub-name** - 構成が存在する IoT ハブの名前です。 ハブは現在のサブスクリプションにある必要があります。 コマンド `az account set -s [subscription name]` を使用して目的のサブスクリプションに切り替えます。

コマンド ウィンドウで構成を調べます。  **メトリック** プロパティは、各ハブによって評価される各メトリックの数を表示します。

* **targetedCount** - ターゲット条件と一致する IoT Hub 内のデバイス ツインの数を指定するシステム メトリックです。

* **appliedCount** - ターゲット コンテンツが適用されているデバイスの数を指定するシステム メトリックです。

* **カスタム メトリック** - メトリックを定義した場合、そのメトリックは、ユーザー メトリックとなります。

次のコマンドを使用して、デバイス ID または各メトリックのオブジェクトの一覧を表示できます。

```cli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id** - IoT ハブに存在するデプロイの名前です。

* --**metric-id** - デバイス ID (`appliedCount` など) の一覧を表示するメトリックの名前です。

* --**hub-name** - デプロイが存在する IoT ハブの名前です。 ハブは現在のサブスクリプションにある必要があります。 コマンド `az account set -s [subscription name]` を使用して目的のサブスクリプションに切り替えます。

* --**metric-type** - メトリックの種類として `system` または `user` にできます。  システム メトリックは `targetedCount` および `appliedCount` です。 その他のすべてのメトリックはユーザー メトリックです。

## <a name="modify-a-configuration"></a>構成の変更

構成を変更すると、変更はすべての対象デバイスにただちにレプリケートされます。 

対象の条件を更新すると、次の更新が実行されます。

* 古いターゲット条件を満たしていなかったデバイス ツインが、新しいターゲット条件を満たしていて、かつその構成の優先度がそのデバイスに対して最も高い場合には、その構成がデバイス ツインに適用されます。 

* デバイス ツインがターゲット条件を満たさなくなった場合は、構成から設定が削除され、次に高い優先度の構成で、デバイス ツインが変更されます。 

* その構成を現在実行しているデバイス ツインがターゲット条件を満たさなくなり、その他の構成のターゲット条件も満たさない場合には、構成の設定が削除され、ツインに対するその他の変更は加えられません。 

次のコマンドを使用して、構成を更新します。

```cli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-id** - IoT ハブに存在する構成の名前です。

* --**hub-name** - 構成が存在する IoT ハブの名前です。 ハブは現在のサブスクリプションにある必要があります。 コマンド `az account set -s [subscription name]` を使用して目的のサブスクリプションに切り替えます。

* --**set** - 構成のプロパティを更新します。 次のプロパティを更新することができます。

    * targetCondition - `targetCondition=tags.location.state='Oregon'` など

    * labels 

    * priority

## <a name="delete-a-configuration"></a>構成を削除する

構成を削除すると、対象となっていたすべてのデバイス ツインが、次に高い優先度の構成に移行されます。 デバイス ツインが他のいずれの構成のターゲット条件も満たさない場合は、他の設定は適用されません。 

次のコマンドを使用して、構成を削除します。

```cli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```
* --**config-id** - IoT ハブに存在する構成の名前です。

* --**hub-name** - 構成が存在する IoT ハブの名前です。 ハブは現在のサブスクリプションにある必要があります。 コマンド `az account set -s [subscription name]` を使用して目的のサブスクリプションに切り替えます。

## <a name="next-steps"></a>次の手順

この記事では、多数の IoT デバイスを構成および監視する方法について説明しました。 Azure IoT Hub の管理についてさらに学習するには、次のリンクを使用してください。

* [IoT Hub デバイス ID を一括で管理する](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub メトリック](iot-hub-metrics.md)
* [操作の監視](iot-hub-operations-monitoring.md)

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Hub 開発者ガイド](iot-hub-devguide.md)
* [Azure IoT Edge でエッジ デバイスに AI をデプロイする](../iot-edge/tutorial-simulate-device-linux.md)

IoT Hub Device Provisioning サービスを使用してノータッチの Just-In-Time プロビジョニングを実現する方法については、次を参照してください。 

* [Azure IoT Hub Device Provisioning Service](/azure/iot-dps)
