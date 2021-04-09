---
title: Azure IoT Hub を使用した大規模な自動デバイス管理 (CLI) | Microsoft Docs
description: Azure IoT Hub 自動構成を使用し、複数の IoT デバイスまたはモジュールを管理する
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.openlocfilehash: 0b8b499613f8234f449e6d72f6ed6ec1f2f21287
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92545414"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-cli"></a>Azure CLI を使用した IoT デバイス/モジュールの自動管理

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Azure IoT Hub の自動デバイス管理では、多数のデバイスを管理するという反復的で複雑なタスクの多くを自動化できます。 自動デバイス管理では、対象となる一連のデバイスをプロパティに基づいて設定し、必要な構成を定義しておくと、デバイスがスコープに適合したときに、IoT Hub によってデバイスが更新されます。 この更新は、_自動デバイス構成_ または _自動モジュール構成_ を使用して行われます。これにより、完了やコンプライアンスの概況を把握し、マージや競合に対処し、構成を段階的なアプローチで展開することができます。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

自動デバイス管理では、一連のデバイス ツインまたはモジュール ツインが必要なプロパティで更新され、ツインで報告されたプロパティに基づくサマリーが報告されます。  この機能では、*構成* という新しいクラスと JSON ドキュメントが導入されています。構成には、次の 3 つの要素が含まれています。

* **ターゲットの条件**: 更新されるデバイス ツインまたはモジュール ツインのスコープ (範囲) を定義します。 ターゲットの条件は、デバイス ツインのタグや報告されたプロパティに関するクエリとして指定されます。

* **ターゲット コンテンツ**: ターゲットのデバイス ツインまたはモジュール ツイン内のどのプロパティを追加または更新するのかを定義します。 このコンテンツには、変更するプロパティのセクションへのパスが含まれます。

* **メトリック**: 各種の構成状態 (**成功**、**進行中**、**エラー** など) の集計カウントを定義します。 カスタム メトリックは、ツインから報告されたプロパティに関するクエリとして指定されます。  システム メトリックは、ツインの更新状態を測定する既定のメトリックです (対象となるツインの数や、正常に更新されたツインの数など)。

自動構成は、構成が作成された直後に初めて実行され、その後は 5 分間隔で実行されます。 自動構成が実行されるたびに、メトリックのクエリが実行されます。

## <a name="cli-prerequisites"></a>CLI の前提条件

* Azure サブスクリプション内の [IoT ハブ](../iot-hub/iot-hub-create-using-cli.md)。 

* ご使用の環境内の [Azure CLI](/cli/azure/install-azure-cli)。 Azure CLI のバージョンは、少なくとも 2.0.70 以降である必要があります。 検証するには、`az –-version` を使用します。 このバージョンでは、az 拡張機能のコマンドがサポートされ、Knack コマンド フレームワークが導入されています。 

* [Azure CLI 向け IoT 拡張機能](https://github.com/Azure/azure-cli)。

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="implement-twins"></a>ツインを実装する

自動デバイス構成では、クラウドとデバイスとの間で状態を同期するために、デバイス ツインを使う必要があります。  詳細については、「[IoT Hub のデバイス ツインの理解と使用](iot-hub-devguide-device-twins.md)」を参照してください。

自動モジュール構成では、クラウドとモジュール間で状態を同期するために、モジュール ツインを使う必要があります。 詳細については、「[IoT Hub のモジュール ツインの理解と使用](iot-hub-devguide-module-twins.md)」を参照してください。

## <a name="use-tags-to-target-twins"></a>タグを使用してツインをターゲットにする

構成を作成する前に、影響の範囲内に含めるデバイスまたはモジュールを指定する必要があります。 Azure IoT Hub では、デバイス ツインのタグを利用してデバイスが識別され、モジュール ツインのタグを利用してモジュールが識別されます。 各デバイスまたはモジュールには複数のタグを設定することができ、ソリューションに適した任意の方法で定義できます。 たとえば、複数の場所のデバイスを管理する場合には、デバイス ツインに次のタグを追加します。

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>ターゲット コンテンツとメトリックを定義する

ターゲット コンテンツとメトリック クエリは、デバイス ツインまたはモジュール ツインの設定される目的のプロパティと、測定対象の報告されるプロパティを記述する JSON ドキュメントとして指定されます。  Azure CLI を使用して自動構成を作成するには、対象のコンテンツとメトリックを .txt ファイルとしてローカルに保存します。 コマンドを実行して構成をデバイスに適用するときには、後のセクションに示すファイル パスを使用します。

自動デバイス構成の基本的なターゲット コンテンツ サンプルは次のようになります。

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

自動モジュール構成の動作は非常に似ていますが、`deviceContent` ではなく `moduleContent` がターゲットになります。

```json
{
  "content": {
    "moduleContent": {
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
    "Compliant": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

モジュールのメトリック クエリはデバイスのクエリにも似ていますが、`devices.modules` から `moduleId` に対して選択します。 次に例を示します。 

```json
{
  "queries": {
    "Compliant": "select deviceId, moduleId from devices.module where configurations.[[chillermodulesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'"
  }
}
```

## <a name="create-a-configuration"></a>構成を作成する

ターゲットコンテンツとメトリックから成る構成を作成して、ターゲット デバイスを構成します。 

次のコマンドを使用して、構成を作成します。

```azurecli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-id** - IoT ハブに作成される構成の名前です。 構成に一意の名前を付けます。名前は最大 128 文字の英小文字で指定します。 スペースや、無効な文字は使用しないでください。`& ^ [ ] { } \ | " < > /`

* --**labels** - 構成を追跡するためのラベルを追加します。 ラベルは、デプロイを説明する、[名前] と [値] で一組になっています。 たとえば、`HostPlatform, Linux` や `Version, 3.0.1` のようにします。

* --**content** - インライン JSON 、またはツインの必要なプロパティとして設定されるターゲット コンテンツへのファイル パス。 

* --**hub-name** - 構成が作成される IoT ハブの名前です。 ハブは現在のサブスクリプションにある必要があります。 コマンド `az account set -s [subscription name]` を使用して目的のサブスクリプションに切り替えます。

* --**target-condition** - どのデバイスまたはモジュールがこの構成の対象となるかを指定する対象の条件を入力します。  自動デバイス構成の場合、条件は、デバイス ツイン タグか、デバイス ツインで必要なプロパティに基づいて指定し、式の形式に一致させる必要があります。  たとえば、`tags.environment='test'` または `properties.desired.devicemodel='4000x'` です。  自動モジュール構成の場合、条件は、モジュール ツイン タグまたはモジュール ツインの目的のプロパティに基づきます。 たとえば、`from devices.modules where tags.environment='test'` または `from devices.modules where properties.reported.chillerProperties.model='4000x'` です。

* --**priority** - 正の整数にする必要があります。 同じデバイスまたはモジュールで複数の構成がターゲットとなっている場合は、優先度の数値が最も大きい構成が適用されます。

* --**metrics** - メトリック クエリへのファイルパスです。 メトリックは、構成のコンテンツを適用した後にデバイスまたはモジュールから報告される、各種の状態の集計カウントを示すものです。 たとえば、保留設定の変更に対するメトリック、エラーのメトリック、正常設定の変更に対するメトリックなどを作成できます。 

## <a name="monitor-a-configuration"></a>構成の監視

次のコマンドを使用して、構成の内容を表示します。

```azurecli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id** - IoT ハブに存在する構成の名前です。

* --**hub-name** - 構成が存在する IoT ハブの名前です。 ハブは現在のサブスクリプションにある必要があります。 コマンド `az account set -s [subscription name]` を使用して目的のサブスクリプションに切り替えます。

コマンド ウィンドウで構成を調べます。  **メトリック** プロパティは、各ハブによって評価される各メトリックの数を表示します。

* **targetedCount** - ターゲット条件と一致する IoT Hub 内のデバイス ツインまたはモジュール ツインの数を指定するシステム メトリックです。

* **appliedCount** - ターゲット コンテンツが適用されているデバイスまたはモジュールの数を指定するシステム メトリックです。

* **カスタム メトリック** - メトリックを定義した場合、そのメトリックは、ユーザー メトリックとなります。

次のコマンドを使用して、メトリック別のデバイス ID、モジュール ID、オブジェクトの一覧を表示できます。

```azurecli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id** - IoT ハブに存在するデプロイの名前です。

* --**metric-id** - デバイス ID またはモジュール ID (`appliedCount` など) の一覧を表示するメトリックの名前です。

* --**hub-name** - デプロイが存在する IoT ハブの名前です。 ハブは現在のサブスクリプションにある必要があります。 コマンド `az account set -s [subscription name]` を使用して目的のサブスクリプションに切り替えます。

* --**metric-type** - メトリックの種類として `system` または `user` にできます。  システム メトリックは `targetedCount` および `appliedCount` です。 その他のすべてのメトリックはユーザー メトリックです。

## <a name="modify-a-configuration"></a>構成の変更

構成を変更すると、変更はすべての対象デバイスにただちにレプリケートされます。 

対象の条件を更新すると、次の更新が実行されます。

* 古いターゲット条件を満たしていなかったツインが、新しいターゲット条件を満たしていて、かつその構成の優先度がそのツインに対して最も高い場合、その構成が適用されます。 

* その構成を現在実行しているツインがターゲット条件を満たさなくなった場合は、構成から設定が削除され、次に高い優先度の構成でツインが変更されます。 

* その構成を現在実行しているツインがターゲット条件を満たさなくなり、その他の構成のターゲット条件も満たさない場合には、構成の設定が削除され、ツインに対するその他の変更は加えられません。 

次のコマンドを使用して、構成を更新します。

```azurecli
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

構成を削除すると、対象となっていたすべてのデバイス ツインまたはモジュール ツインが、次に高い優先度の構成に移行されます。 ツインが他のいずれの構成のターゲット条件も満たさない場合は、他の設定は適用されません。 

次のコマンドを使用して、構成を削除します。

```azurecli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```

* --**config-id** - IoT ハブに存在する構成の名前です。

* --**hub-name** - 構成が存在する IoT ハブの名前です。 ハブは現在のサブスクリプションにある必要があります。 コマンド `az account set -s [subscription name]` を使用して目的のサブスクリプションに切り替えます。

## <a name="next-steps"></a>次のステップ

この記事では、多数の IoT デバイスを構成および監視する方法について説明しました。 Azure IoT Hub の管理についてさらに学習するには、次のリンクを使用してください。

* [IoT Hub デバイス ID を一括で管理する](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub の監視](monitor-iot-hub.md)

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Hub 開発者ガイド](iot-hub-devguide.md)
* [Azure IoT Edge でエッジ デバイスに AI をデプロイする](../iot-edge/quickstart-linux.md)

IoT Hub Device Provisioning サービスを使用してノータッチの Just-In-Time プロビジョニングを実現する方法については、次を参照してください。 

* [Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml)