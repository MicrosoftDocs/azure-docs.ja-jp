---
title: IoT Edge デプロイの監視 - Azure IoT Edge
description: edgeHub および edgeAgent に報告されたプロパティと自動デプロイ メトリックを含む高レベルの監視。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/21/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: f4f2f8522f6d3d1039673803d946323deb485db9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200264"
---
# <a name="monitor-iot-edge-deployments"></a>IoT Edge デプロイの監視

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge には、IoT Edge デバイスにデプロイされているモジュールに関するリアルタイムの情報を監視するためのレポート機能が用意されています。 IoT Hub サービスでは、デバイスからステータスを取得し、オペレーターが利用できるようにします。 また、自動デプロイや多層デプロイなどの[大規模なデプロイ](module-deployment-monitoring.md)でも監視は重要です。

デバイスとモジュールには、接続性などの類似したデータがあるため、値はデバイス ID またはモジュール ID に応じて取得されます。

IoT Hub サービスは、デバイスとモジュール ツインによって報告されたデータを収集し、デバイスに存在する可能性があるさまざまな状態の数を提供します。 IoT Hub サービスは、このデータを 4 つのメトリック グループに編成します。

| Type | 説明 |
| --- | ---|
| ターゲット | IoT Edge デバイスがデプロイのターゲット条件に一致していることを示します。 |
| 適用済み | ターゲット IoT Edge デバイスが、より優先順位の高い別のデプロイのターゲットになっていないことを示します。 |
| 成功の報告 | モジュールが正常にデプロイされたことを報告した IoT Edge デバイスを示します。 |
| 失敗の報告 | 1 つ以上のモジュールが正常にデプロイされなかったことを報告した IoT Edge デバイスを示します。 エラーについて詳しく調べるには、それらのデバイスにリモートで接続し、ログ ファイルを参照します。 |

IoT Hub サービスでは、このデータを Azure portal と Azure CLI で監視できるようになります。

## <a name="monitor-a-deployment-in-the-azure-portal"></a>Azure portal でデプロイを監視する

デプロイの詳細を確認し、それを実行するデバイスを監視するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインし、IoT Hub に移動します。
1. 左ペインのメニューで、 **[IoT Edge]** を選択します。
1. **[IoT Edge デプロイ]** タブを選択します。
1. デプロイの一覧を確認します。  デプロイごとに、次の詳細を表示できます。

    | 列 | 説明 |
    | --- | --- |
    | id | デプロイの名前。 |
    | Type | デプロイの種類。 **[デプロイ]** または **[Layered Deployment]\(多層デプロイ\)** のどちらかです。 |
    | 対象の条件 | ターゲット デバイスを定義するために使用されるタグ。 |
    | Priority | デプロイに割り当てられた優先順位番号。 |
    | システム メトリック | ターゲット条件に一致する IoT Hub 内のデバイス ツインの数を示します。 **[適用済み]** は、IoT Hub 内でモジュール ツインにデプロイ コンテンツが適用されているデバイスの数を指定します。 |
    | デバイス メトリック | IoT Edge クライアント ランタイムから成功またはエラーを報告している IoT Edge デバイスの数。 |
    | カスタム メトリック | デプロイに対して定義したすべてのメトリックのデータを報告している IoT Edge デバイスの数。 |
    | 作成時刻 | デプロイが作成された時刻のタイムスタンプ。 2 つのデプロイの優先順位が同じ場合に、このタイムスタンプを使用して優先するデプロイを決定します。 |

1. 監視するデプロイを選択します。  
1. **[デプロイの詳細]** ページで、下部のセクションにスクロールし、 **[ターゲットの条件]** タブを選択します。ターゲットの条件に一致するデバイスを一覧表示するには、 **[View]\(表示\)** を選択します。 条件のほかに **優先順位** も変更できます。 変更した場合は、 **[保存]** を選択します。

   ![デプロイのターゲット デバイスを表示する](./media/how-to-monitor-iot-edge-deployments/target-devices.png)

1. **[メトリック]** タブを選択します。 **[メトリックの選択]** ドロップダウンからメトリックを選択すると、結果を表示するための **[View]\(表示\)** ボタンが表示されます。 また、 **[メトリックの編集]** を選択して、定義したカスタム メトリックの条件を調整することもできます。 変更した場合は、 **[保存]** を選択します。

   ![デプロイのメトリックを表示する](./media/how-to-monitor-iot-edge-deployments/deployment-metrics-tab.png)

デプロイを変更するには、「[デプロイの変更](how-to-deploy-at-scale.md#modify-a-deployment)」を参照してください。

## <a name="monitor-a-deployment-with-azure-cli"></a>Azure CLI でデプロイを監視する

単一のデプロイの詳細を表示するには、[az IoT edge deployment show](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-show) コマンドを使用します。

```azurecli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

deployment show コマンドは、次のパラメーターを受け取ります。

* **--deployment-id** - IoT ハブに存在するデプロイの名前です。 必須のパラメーター。
* **--hub-name** - デプロイが存在する IoT ハブの名前です。 ハブは現在のサブスクリプションにある必要があります。 コマンド `az account set -s [subscription name]` を使用して目的のサブスクリプションに切り替えます。

コマンド ウィンドウで、デプロイを検査します。  **メトリック** プロパティは、各ハブによって評価される各メトリックの数を表示します。

* **targetedCount** - ターゲット条件と一致する IoT Hub 内のデバイス ツインの数を指定するシステム メトリックです。
* **appliedCount** - IoT Hub 内でモジュール ツインにデプロイ コンテンツが適用されているデバイスの数を指定するシステム メトリックです。
* **reportedSuccessfulCount** - IoT Edge クライアント ランタイムから成功をレポートしているデプロイ内の IoT Edge デバイスの数を指定するデバイス メトリックです。
* **reportedFailedCount** - IoT Edge クライアント ランタイムから失敗をレポートしているデプロイ内の IoT Edge デバイスの数を指定するデバイス メトリックです。

[az IoT Edge deployment show-metric](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-show-metric) コマンドを使用して、各メトリックのデバイス ID またはオブジェクトの一覧を表示できます。

```azurecli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

deployment show-metric コマンドは、次のパラメーターを受け取ります。

* **--deployment-id** - IoT ハブに存在するデプロイの名前です。
* **--metric-id** - デバイス ID (`reportedFailedCount` など) の一覧を表示するメトリックの名前です。
* **--hub-name** - デプロイが存在する IoT ハブの名前です。 ハブは現在のサブスクリプションにある必要があります。 コマンド `az account set -s [subscription name]` を使用して目的のサブスクリプションに切り替えます。

デプロイを変更するには、「[デプロイの変更](how-to-deploy-cli-at-scale.md#modify-a-deployment)」を参照してください。

## <a name="next-steps"></a>次のステップ

IoT Edge のデプロイの接続と正常性のために、[モジュール ツインを監視する](how-to-monitor-module-twins.md)方法を確認します (監視対象は主に IoT Edge Agent と IoT Edge Hub のランタイム モジュールです)。
