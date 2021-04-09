---
title: 組み込みのメトリックにアクセスする - Azure IoT Edge
description: IoT Edge ランタイム コンポーネントから組み込みメトリックへのリモート アクセス
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 10/05/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1a78db821c0fab01ad5d6752216a8f7682fb2c46
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200499"
---
# <a name="access-built-in-metrics"></a>組み込みのメトリックにアクセスする

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge ランタイム コンポーネント (IoT Edge ハブと IoT Edge エージェント) を使用すると、[Prometheus の出力フォーマット](https://prometheus.io/docs/instrumenting/exposition_formats/)で組み込みのメトリックが生成されます。 これらのメトリックにリモートでアクセスして、IoT Edge デバイスの正常性を監視および把握します。

リリース 1.0.10 からは、メトリックは既定で、**edgeHub** および **edgeAgent** のモジュール (`http://edgeHub:9600/metrics` および `http://edgeAgent:9600/metrics`) の **ポート 9600** で自動的に公開されます。 既定では、これらのポートはホストにマップされません。

モジュールの `createOptions` からメトリック ポートを公開およびマップすることによって、ホストからメトリックにアクセスします。 次の例では、既定のメトリック ポートをホスト上のポート 9601 にマップしています。

```
{
  "ExposedPorts": {
    "9600/tcp": {}
  },
  "HostConfig": {
    "PortBindings": {
      "9600/tcp": [
        {
          "HostPort": "9601"
        }
      ]
    }
  }
}
```

edgeHub と edgeAgent の両方のメトリック エンドポイントをマップする場合は、別の一意のホスト ポート番号を選択してください。

> [!NOTE]
> 組み込みメトリックをコレクションに使用できるようにするには、環境変数 `httpSettings__enabled` を `false` に設定しないでください。
>
> メトリックを無効にするために使用できる環境変数の一覧は、[azure/iotedge リポジトリ ドキュメント](https://github.com/Azure/iotedge/blob/master/doc/EnvironmentVariables.md)にあります。

## <a name="available-metrics"></a>使用可能なメトリック

メトリックには、収集されるメトリックの性質を特定するのに役立つタグが含まれています。 すべてのメトリックには、次のタグが含まれています。

| タグ | 説明 |
|-|-|
| iothub | デバイスが通信しているハブ |
| edge_device | 現在のデバイスの ID |
| instance_number | 現在のランタイムを表す GUID。 再起動時に、すべてのメトリックがリセットされます。 この GUID を使用すると、再起動の調整が容易になります。 |

Prometheus の出力フォーマットには、カウンター、ゲージ、ヒストグラム、概要という 4 種類のコア メトリックがあります。 さまざまなメトリックの種類の詳細については、[Prometheus のメトリックの種類に関するドキュメント](https://prometheus.io/docs/concepts/metric_types/)を参照してください。

組み込みのヒストグラムと概要のメトリックに対して提供される分位点は、0.1、0.5、0.9、0.99 です。

**edgeHub** モジュールにより、次のメトリックが生成されます。

| 名前 | Dimensions | 説明 |
|-|-|-|
| `edgehub_gettwin_total` | `source` (操作元)<br> `id` (モジュール ID) | 種類: カウンター<br> GetTwin 呼び出しの合計数 |
| `edgehub_messages_received_total` | `route_output` (メッセージを送信した出力)<br> `id` | 種類: カウンター<br> クライアントから受信したメッセージの合計数 |
| `edgehub_messages_sent_total` | `from` (メッセージの送信元)<br> `to` (メッセージの送信先)<br>`from_route_output`<br> `to_route_input` (メッセージの送信先の入力)<br> `priority` (送信先へのメッセージの優先度) | 種類: カウンター<br> クライアントまたはアップストリームに送信されたメッセージの合計数<br> `to` が $upstream 場合、`to_route_input` は空になります |
| `edgehub_reported_properties_total` | `target` (ターゲットの更新)<br> `id` | 種類: カウンター<br> 報告されたプロパティ更新呼び出しの合計数 |
| `edgehub_message_size_bytes` | `id`<br> | 種類: 概要<br> クライアントからのメッセージ サイズ<br> 一定期間 (現時点では 10 分)、新しい測定値が報告されない場合、値は `NaN` として報告されることがあります。`summary` 型の場合、対応する `_count` と `_sum` のカウンターが生成されます。 |
| `edgehub_gettwin_duration_seconds` | `source` <br> `id` | 種類: 概要<br> ツイン操作の取得にかかった時間 |
| `edgehub_message_send_duration_seconds` | `from`<br> `to`<br> `from_route_output`<br> `to_route_input` | 種類: 概要<br> メッセージの送信に要した時間 |
| `edgehub_message_process_duration_seconds` | `from` <br> `to` <br> `priority` | 種類: 概要<br> キューからのメッセージの処理にかかった時間 |
| `edgehub_reported_properties_update_duration_seconds` | `target`<br> `id` | 種類: 概要<br> 報告されたプロパティの更新にかかった時間 |
| `edgehub_direct_method_duration_seconds` | `from` (呼び出し元)<br> `to` (受信者) | 種類: 概要<br> ダイレクト メッセージの解決にかかった時間 |
| `edgehub_direct_methods_total` | `from`<br> `to` | 種類: カウンター<br> 送信されたダイレクト メッセージの合計数 |
| `edgehub_queue_length` | `endpoint` (メッセージの送信元)<br> `priority` (キューの優先順位) | 種類: ゲージ<br> 指定された優先度に対する edgeHub のキューの現在の長さ |
| `edgehub_messages_dropped_total` | `reason` (no_route、ttl_expiry)<br> `from` <br> `from_route_output` | 種類: カウンター<br> 理由により削除されたメッセージの合計数 |
| `edgehub_messages_unack_total` | `reason` (storage_failure)<br> `from`<br> `from_route_output` | 種類: カウンター<br> ストレージの障害による未確認のメッセージの合計数 |
| `edgehub_offline_count_total` | `id` | 種類: カウンター<br> edgeHub がオフラインになった合計回数 |
| `edgehub_offline_duration_seconds`| `id` | 種類: 概要<br> edgeHub がオフラインだった時間 |
| `edgehub_operation_retry_total` | `id`<br> `operation` (操作名) | 種類: カウンター<br> edgeHub 操作が再試行された合計回数 |
| `edgehub_client_connect_failed_total` | `id` <br> `reason` (未認証)<br> | 種類: カウンター<br> クライアントが edgeHub に接続できなかった合計回数 |

**edgeAgent** モジュールにより、次のメトリックが生成されます。

| 名前 | Dimensions | 説明 |
|-|-|-|
| `edgeAgent_total_time_running_correctly_seconds` | `module_name` | 種類: ゲージ<br> デプロイでモジュールが指定され、実行中の状態だった時間 |
| `edgeAgent_total_time_expected_running_seconds` | `module_name` | 種類: ゲージ<br> デプロイでモジュールが指定された時間 |
| `edgeAgent_module_start_total` | `module_name`, `module_version` | 種類: カウンター<br> モジュールを開始するように edgeAgent から docker に要求された回数 |
| `edgeAgent_module_stop_total` | `module_name`, `module_version` | 種類: カウンター<br> モジュールを停止するように edgeAgent から docker に要求された回数 |
| `edgeAgent_command_latency_seconds` | `command` | 種類: ゲージ<br> docker が特定のコマンドを実行するのにかかった時間。 使用可能なコマンド: 作成、更新、削除、開始、停止、再起動 |
| `edgeAgent_iothub_syncs_total` | | 種類: カウンター<br> edgeAgent がそのツインと iotHub との同期を試行した回数 (成功と失敗の両方)。 この数には、ツインを要求するエージェントとツインの更新を通知するハブの両方が含まれます |
| `edgeAgent_unsuccessful_iothub_syncs_total` | | 種類: カウンター<br> edgeAgent がそのツインと iotHub との同期に失敗した回数。 |
| `edgeAgent_deployment_time_seconds` | | 種類: カウンター<br> 変更を受け取った後に新しいデプロイを完了するために要した時間。 |
| `edgeagent_direct_method_invocations_count` | `method_name` | 種類: カウンター<br> Ping や Restart など、組み込みの edgeAgent ダイレクト メソッドが呼び出された回数。 |
| `edgeAgent_host_uptime_seconds` | | 種類: ゲージ<br> ホストがオンになっている期間 |
| `edgeAgent_iotedged_uptime_seconds` | | 種類: ゲージ<br> iotedged が実行されている期間 |
| `edgeAgent_available_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype` | 種類: ゲージ<br> ディスクの空き領域のサイズ |
| `edgeAgent_total_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype`| 種類: ゲージ<br> ディスクのサイズ |
| `edgeAgent_used_memory_bytes` | `module_name` | 種類: ゲージ<br> すべてのプロセスで使用されている RAM の容量 |
| `edgeAgent_total_memory_bytes` | `module_name` | 種類: ゲージ<br> 使用可能な RAM |
| `edgeAgent_used_cpu_percent` | `module_name` | 種類: ヒストグラム<br> すべてのプロセスで使用されている CPU の割合 |
| `edgeAgent_created_pids_total` | `module_name` | 種類: ゲージ<br> コンテナーによって作成されたプロセスまたはスレッドの数 |
| `edgeAgent_total_network_in_bytes` | `module_name` | 種類: ゲージ<br> ネットワークから受信したバイト数 |
| `edgeAgent_total_network_out_bytes` | `module_name` | 種類: ゲージ<br> ネットワークに送信されたバイト数 |
| `edgeAgent_total_disk_read_bytes` | `module_name` | 種類: ゲージ<br> ディスクから読み取られたバイト数 |
| `edgeAgent_total_disk_write_bytes` | `module_name` | 種類: ゲージ<br> ディスクに書き込まれたバイト数 |
| `edgeAgent_metadata` | `edge_agent_version`, `experimental_features`, `host_information` | 種類: ゲージ<br> デバイスに関する一般的なメタデータ。 値は常に 0 です。情報はタグでエンコードされます。 `experimental_features` と `host_information` が json オブジェクトであることに注意してください。 `host_information` は ```{"OperatingSystemType": "linux", "Architecture": "x86_64", "Version": "1.0.10~dev20200803.4", "Provisioning": {"Type": "dps.tpm", "DynamicReprovisioning": false, "AlwaysReprovisionOnStartup": true}, "ServerVersion": "19.03.6", "KernelVersion": "5.0.0-25-generic", "OperatingSystem": "Ubuntu 18.04.4 LTS", "NumCpus": 6, "Virtualized": "yes"}``` のように表示されます。 `ServerVersion` は Docker バージョンで、`Version` は IoT Edge セキュリティ デーモンのバージョンであることに注意してください。 |

## <a name="next-steps"></a>次のステップ

* [Azure IoT Edge ランタイムとそのアーキテクチャの概要](iot-edge-runtime.md)
* [IoT Edge エージェントと IoT Edge ハブのモジュール ツインのプロパティ](module-edgeagent-edgehub.md)
