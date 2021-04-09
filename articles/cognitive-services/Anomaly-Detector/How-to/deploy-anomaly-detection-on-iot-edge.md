---
title: IoT Edge で Anomaly Detector を実行する
titleSuffix: Azure Cognitive Services
description: Anomaly Detector モジュールを IoT Edge にデプロイします。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: mbullwin
ms.openlocfilehash: b4153b07b153a9ee0b16dc032ab5e7810e236d7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98936266"
---
# <a name="deploy-an-anomaly-detector-module-to-iot-edge"></a>Anomaly Detector モジュールを IoT Edge にデプロイします

Cognitive Services [Anomaly Detector](../anomaly-detector-container-howto.md) モジュールを IoT Edge デバイスにデプロイする方法について説明します。 IoT Edge にデプロイされると、このモジュールは、他のモジュールと共にコンテナー インスタンスとして IoT Edge 内で実行されます。 これにより、標準の Docker コンテナー環境で実行されている Anomaly Detector コンテナー インスタンスとまったく同じ API が公開されます。 

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションを使用してください。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free) を作成してください。
* [Azure CLI](/cli/azure/install-azure-cli) をインストールします。
* [IoT Hub](../../../iot-hub/iot-hub-create-through-portal.md) と [IoT Edge](../../../iot-edge/quickstart-linux.md) デバイス。

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

## <a name="deploy-the-anomaly-detection-module-to-the-edge"></a>異常検出モジュールをエッジにデプロイする

1. Azure portal で、「**Anomaly Detector on IoT Edge**」と検索に入力し、Azure Marketplace の結果を開きます。
2. これにより、Azure portal の [[IoT Edge モジュールのターゲット デバイス] ページ](https://portal.azure.com/#create/azure-cognitive-service.edge-anomaly-detector)に移動します。 次の必要な情報を入力します。

    1. サブスクリプションを選択します。

    1. IoT ハブを選択します。

    1. **[デバイスの検索]** を選択して、IoT Edge デバイスを検索します。

3. **[作成]** ボタンを選択します。

4. **AnomalyDetectoronIoTEdge** モジュールを選択します。

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/iot-edge-modules.png" alt-text="IoT Edge モジュールのユーザー インターフェイスの画像。AnomalyDetectoronIoTEdge リンクが赤色のボックスで強調表示されており、この項目を選択することが示されています。":::

5. **[環境変数]** に移動し、次の情報を指定します。

    1.  Eula の値は **accept** のままにします。

    1. **Billing** にご自分の Cognitive Services エンドポイントを入力します。

    1. **Apikey** にご自分の Cognitive Services API キーを入力します。

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/environment-variables.png" alt-text="エンドポイントと API キーを表す環境変数の値を入力する必要のある領域が赤い枠で囲まれている":::

6. **[Update]\(更新\)** を選択します。

7. **Next:ルート** を選択して、ルートを定義します。 すべてのモジュールから、すべてのメッセージが Azure IoT Hub に送信されるように定義します。

8. **[次へ: 確認および作成]** を選択します。 IoT Edge デバイスにデプロイされるすべてのモジュールを定義した JSON ファイルをプレビューできます。
    
9. **[作成]** を選択してモジュール デプロイを開始します。

10. モジュールのデプロイが完了すると、IoT ハブの [IoT Edge] ページに戻ります。 IoT Edge デバイスの一覧からご自分のデバイスを選択し、その詳細を表示します。

11. 下にスクロールして、一覧表示されているモジュールを表示します。 新しいモジュールについてランタイムの状態が実行中であることを確認します。 

IoT Edge デバイスのランタイムの状態をトラブルシューティングするには、[トラブルシューティング ガイド](../../../iot-edge/troubleshoot.md)をご覧ください

## <a name="test-anomaly-detector-on-an-iot-edge-device"></a>IoT Edge デバイスで Anomaly Detector をテストする

Azure Cognitive Services コンテナーが実行されている Azure IoT Edge デバイスへの HTTP 呼び出しを行います。 コンテナーには、REST ベースのエンドポイント API が用意されています。 モジュール API には、ホスト `http://<your-edge-device-ipaddress>:5000` を使用します。

エッジ デバイスでポート 5000 での受信方向の通信がまだ許可されていない場合は、新しい **受信ポート規則** を作成する必要があります。 

Azure VM の場合、これは **[仮想マシン]**  >  **[設定]**  >  **[ネットワーク]**  >  **[受信ポートの規則]**  >  **[受信ポートの規則を追加する]** で設定できます。

モジュールが実行されていることを検証する方法は複数あります。 問題になっているエッジ デバイスの "*外部 IP*" アドレスと公開ポートを特定し、任意の Web ブラウザーを開きます。 以下に示した各種の要求 URL を使用して、コンテナーが実行中であることを確認します。 以下に示した例の要求 URL は `http://<your-edge-device-ipaddress:5000` ですが、実際のコンテナーは異なる可能性があります。 エッジ デバイスの "*外部 IP*" アドレスを使用する必要があることにご注意ください。

| 要求 URL | 目的 |
|:-------------|:---------|
| `http://<your-edge-device-ipaddress>:5000/` | コンテナーには、ホーム ページが用意されています。 |
| `http://<your-edge-device-ipaddress>:5000/status` | これも GET で要求することで、コンテナーを起動するために使用された API キーが有効であるかどうかを、エンドポイント クエリを発生させずに確認できます。 この要求は Kubernetes の [liveness probe と readiness probe](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) に対して使用できます。 |
| `http://<your-edge-device-ipaddress>:5000/swagger` | コンテナーには、エンドポイントの完全なドキュメント一式と、 **[Try it out]\(試してみる\)** の機能が用意されています。 この機能を使用すると、コードを一切記述することなく、お客様の設定を Web ベースの HTML フォームに入力したりクエリを実行したりできます。 クエリから戻った後、HTTP ヘッダーと HTTP 本文の必要な形式を示すサンプル CURL コマンドが得られます。 |

![コンテナーのホーム ページ](../../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

## <a name="next-steps"></a>次のステップ

* コンテナー イメージのプルおよびコンテナーの実行の詳細については、[コンテナーのインストールおよび実行](../anomaly-detector-container-configuration.md)に関する説明を確認する
* 構成設定について、[コンテナーの構成](../anomaly-detector-container-configuration.md)を確認する
* [Anomaly Detector API サービスの詳細情報](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
