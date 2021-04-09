---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 03/02/2021
ms.openlocfilehash: 57415ec76a3e8d9fc3c160b47668d3419ff6ea5c
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622210"
---
IoT Edge エージェント ランタイムの応答を使用すると、コンピューティング関連のエラーをトラブルシューティングできます。 考えられる応答の一覧を以下に示します。

* 200 - OK
* 400 - デプロイ構成が正しくない形式であるか、無効です。
* 417 - デバイスにデプロイ構成セットがありません。
* 412 - デプロイ構成にあるスキーマ バージョンが無効です。
* 406 - IoT Edge デバイスがオフラインであるか、状態レポートを送信していません。
* 500 - IoT Edge ランタイムでエラーが発生しました。

詳細については、「[IoT Edge エージェント](../articles/iot-edge/iot-edge-runtime.md?preserve-view=true&view=iotedge-2018-06#iot-edge-agent)」を参照してください。

次のエラーは、ご使用の Azure Stack Edge Pro デバイスの IoT Edge サービスに関連しています。

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>Compute modules have Unknown status and can't be used (コンピューティング モジュールの状態が不明であるため、使用できません)

#### <a name="error-description"></a>エラーの説明

デバイス上のすべてのモジュールは状態が不明と示されているため、使用できません。 不明の状態は再起動しても保持されます。<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>推奨されている解決方法

IoT Edge サービスを削除してから、モジュールを再デプロイしてください。 詳細については、「[IoT Edge サービスの削除](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service)」を参照してください。


### <a name="modules-show-as-running-but-are-not-working"></a>モジュールは実行中と表示されているが動作していない

#### <a name="error-description"></a>エラーの説明

モジュールのランタイムの状態は実行中と表示されていますが、予期された結果が表示されません。 

この状態は、機能していないモジュール ルート構成に関する問題、または `edgehub` が予期したとおりにメッセージをルーティングしていないことが原因である可能性があります。 `edgehub` ログを確認できます。 IoT Hub サービスに接続できないなどのエラーが表示される場合、最も一般的な理由は接続の問題です。 接続の問題は、IoT Hub サービスによって通信用に既定のポートとして使用される AMPQ ポートがブロックされているか、Web プロキシ サーバーでこれらのメッセージがブロックされていることが原因である可能性があります。

#### <a name="suggested-solution"></a>推奨されている解決方法

次の手順を実行します。
1. このエラーを解決するには、デバイスの IoT Hub リソースにアクセスし、Edge デバイスを選択します。 
1. **[モジュールの設定] > [ランタイムの設定]** にアクセスします。 
1. `Upstream protocol` 環境変数を追加し、それに値 `AMQPWS` を割り当てます。 この場合に構成されるメッセージは、ポート 443 を介して Websocket 経由で送信されます。

### <a name="modules-show-as-running-but-do-not-have-an-ip-assigned"></a>モジュールは実行中と表示されているが IP が割り当てられていない

#### <a name="error-description"></a>エラーの説明

モジュールのランタイムの状態は実行中と表示されていますが、コンテナー化されたアプリに IP が割り当てられていません。 

この状態の原因は、Kubernetes 外部サービス IP に指定した IP の範囲が不十分であることです。 デプロイした各コンテナーまたは VM が確実に対象として含まれるように、この範囲を拡張する必要があります。

#### <a name="suggested-solution"></a>推奨されている解決方法

ご利用のデバイスのローカル Web UI で、次の手順を実行します。
1. **[コンピューティング]** ページにアクセスします。 コンピューティング ネットワークを有効にしたポートを選択します。 
1. **[Kubernetes external service IPs]\(Kubernetes 外部サービス IP\)** に IP の連続した静的な範囲を入力します。 `edgehub` サービスには 1 つの IP が必要です。 また、IoT Edge モジュールごと、およびデプロイする VM ごとに 1 つの IP が必要です。 
1. **[適用]** を選択します。 変更された IP 範囲は直ちに有効になります。

詳細については、「[コンテナーの外部サービス IP を変更する](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#change-external-service-ips-for-containers)」を参照してください。