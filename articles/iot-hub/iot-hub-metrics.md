---
title: "メトリックを使用して Azure IoT Hub を監視する | Microsoft Docs"
description: "Azure IoT Hub メトリックを使用して、IoT Hub の全体的な正常性を評価および監視する方法。"
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a47108fd-f994-4105-b21d-5b8f697b699c
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2017
ms.author: nberdy
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 37b2a82d7f6043224e68219fde753eef73078ffd
ms.openlocfilehash: a736be397e07a1ea98819f6e016df8c0c7c637eb
ms.lasthandoff: 03/02/2017


---
# <a name="understand-iot-hub-metrics"></a>IoT Hub メトリックの理解
IoT Hub メトリックは、Azure サブスクリプション内の Azure IoT リソースの状態に関する有用なデータを提供します。 IoT Hub メトリックにより、ユーザーは IoT Hub サービスとそれに接続されたデバイスの全体的な正常性を評価することができます。 ユーザー向けの統計情報は重要です。この情報は、IoT Hub で起こっていることを確認するのに役立ち、また、Azure サポートに連絡することなく問題の根本原因を特定するための有用な情報となります。

メトリックは既定で有効になっています。 IoT Hub メトリックは Azure Portal で表示できます。

## <a name="how-to-view-iot-hub-metrics"></a>IoT Hub メトリックを表示する方法
1. IoT Hub を作成します。 IoT ハブの作成方法の手順については、[使用開始][lnk-get-started]に関するガイドを参照してください。
2. IoT Hub のブレードを開きます。 そこで **[メトリック]** をクリックします。
   
    ![][1]
3. メトリック ブレードから、IoT Hub のメトリックを表示し、メトリックのカスタム ビューを作成できます。 メトリック データを Event Hubs エンドポイントまたは Azure ストレージ アカウントに送信するように選択するには、**[診断設定]** をクリックします。
   
    ![][2]

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT Hub メトリックとその使用方法
IoT Hub には、ハブの正常性の概要と、ハブに接続されているデバイスの総数を示すいくつかのメトリックが用意されています。 複数のメトリックから得た情報を組み合わせることで、IoT Hub の状態をより大局的に把握することができます。 次の表では、各 IoT Hub で追跡されるメトリックを示すと共に、各メトリックが IoT Hub の全体的な状態にどのように関連するかを説明します。

|メトリック|メトリックの表示名|単位|集計の種類|説明|
|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|テレメトリ メッセージ送信試行|カウント|合計|IoT Hub への送信が試行された Device to Cloud テレメトリ メッセージの数|
|d2c.telemetry.ingress.success|送信済みテレメトリ メッセージ|カウント|合計|IoT Hub に正常に送信された Device to Cloud テレメトリ メッセージの数|
|c2d.commands.egress.complete.success|完了したコマンド|カウント|合計|デバイスで正常に完了した Cloud to Device コマンドの数|
|c2d.commands.egress.abandon.success|中止したコマンド|カウント|合計|デバイスで中止された Cloud to Device コマンドの数|
|c2d.commands.egress.reject.success|拒否されたコマンド|カウント|合計|デバイスで拒否された Cloud to Device コマンドの数|
|devices.totalDevices|合計デバイス|カウント|合計|IoT Hub に登録されたデバイスの数|
|devices.connectedDevices.allProtocol|接続されているデバイス|カウント|合計|IoT Hub に接続されているデバイスの数|
|d2c.telemetry.egress.success|配信されたテレメトリ メッセージ|カウント|合計|メッセージがエンドポイントに正常に書き込まれた回数 (合計)|
|d2c.telemetry.egress.dropped|破棄されたメッセージ|カウント|合計|ルートが一致せず、フォールバック ルートが無効になったために破棄されたメッセージの数|
|d2c.telemetry.egress.orphaned|孤立したメッセージ|カウント|合計|フォールバック ルートを含め、すべてのルートと一致しないメッセージの数|
|d2c.telemetry.egress.invalid|無効なメッセージ|カウント|合計|エンドポイントとの互換性がないために配信されなかったメッセージの数|
|d2c.telemetry.egress.fallback|フォールバック条件に一致するメッセージ|カウント|合計|フォールバック エンドポイントに書き込まれたメッセージの数|
|d2c.endpoints.egress.eventHubs|Event Hub エンドポイントに配信されたメッセージ|カウント|合計|メッセージが Event Hub エンドポイントに正常に書き込まれた回数|
|d2c.endpoints.latency.eventHubs|イベント ハブ エンドポイントのメッセージの待機時間|ミリ秒|平均|IoT ハブの受信メッセージとイベント ハブ エンドポイントの受信メッセージの間の平均待機時間 (ミリ秒)|
|d2c.endpoints.egress.serviceBusQueues|Service Bus キュー エンドポイントに配信されたメッセージ|カウント|合計|メッセージが Service Bus キュー エンドポイントに正常に書き込まれた回数|
|d2c.endpoints.latency.serviceBusQueues|Service Bus キュー エンドポイントのメッセージの待機時間|ミリ秒|平均|IoT ハブの受信メッセージと Service Bus キュー エンドポイントの受信メッセージの間の平均待機時間 (ミリ秒)|
|d2c.endpoints.egress.serviceBusTopics|Service Bus トピック エンドポイントに配信されたメッセージ|カウント|合計|メッセージが Service Bus トピック エンドポイントに正常に書き込まれた回数|
|d2c.endpoints.latency.serviceBusTopics|Service Bus トピック エンドポイントのメッセージの待機時間|ミリ秒|平均|IoT ハブの受信メッセージと Service Bus トピック エンドポイントの受信メッセージの間の平均待機時間 (ミリ秒)|
|d2c.endpoints.egress.builtIn.events|組み込みエンドポイント (イベント/メッセージ) に配信されたメッセージ|カウント|合計|メッセージが組み込みエンドポイント (イベント/メッセージ) に正常に書き込まれた回数|
|d2c.endpoints.latency.builtIn.events|組み込みエンドポイント (イベント/メッセージ) のメッセージ待機時間|ミリ秒|平均|IoT ハブの受信メッセージと組み込みエンドポイント (メッセージ/イベント) の受信メッセージの間の平均待機時間 (ミリ秒) |
|d2c.twin.read.success|成功したデバイスからのツイン読み取り|カウント|合計|デバイスが開始して成功したツイン読み取りの数。|
|d2c.twin.read.failure|失敗したデバイスからのツイン読み取り|カウント|合計|デバイスが開始したツイン読み取りの失敗数。|
|d2c.twin.read.size|デバイスからのツイン読み取りの応答サイズ|バイト|平均|デバイスが開始して成功したツイン読み取りの平均、最小、および最大サイズ。|
|d2c.twin.update.success|成功したデバイスからのツイン更新|カウント|合計|デバイスが開始して成功したツイン更新の数。|
|d2c.twin.update.failure|失敗したデバイスからのツイン更新|カウント|合計|デバイスが開始して失敗したツイン更新の数。|
|d2c.twin.update.size|デバイスからのツイン更新のサイズ|バイト|平均|デバイスが開始して成功したツイン更新の平均、最小、および最大サイズ。|
|c2d.methods.success|成功したダイレクト メソッドの呼び出し|カウント|合計|成功したダイレクト メソッドの呼び出しの数。|
|c2d.methods.failure|失敗したダイレクト メソッドの呼び出し|カウント|合計|失敗したダイレクト メソッドの呼び出しの数。|
|c2d.methods.requestSize|ダイレクト メソッドの呼び出しの要求サイズ|バイト|平均|成功したダイレクト メソッド要求の平均、最小、および最大サイズ。|
|c2d.methods.responseSize|ダイレクト メソッドの呼び出しの応答サイズ|バイト|平均|成功したダイレクト メソッド応答の平均、最小、および最大サイズ。|
|c2d.twin.read.success|成功したバック エンドからのツイン読み取り|カウント|合計|バックエンドが開始して成功したツイン読み取りの数。|
|c2d.twin.read.failure|失敗したバックエンドからのツイン読み取り|カウント|合計|バックエンドが開始して失敗したツイン読み取りの数。|
|c2d.twin.read.size|バック エンドからのツイン読み取りの応答サイズ|バイト|平均|バックエンドが開始して成功したツイン読み取りの平均、最小、および最大サイズ。|
|c2d.twin.update.success|成功したバックエンドからのツイン更新|カウント|合計|バックエンドが開始して成功したツイン更新の数。|
|c2d.twin.update.failure|失敗したバック エンドからのツイン更新|カウント|合計|バックエンドが開始して失敗したツイン更新の数。|
|c2d.twin.update.size|バックエンドからのツイン更新のサイズ|バイト|平均|バックエンドが開始して成功したツイン更新の平均、最小、および最大サイズ。|
|twinQueries.success|成功したツイン クエリ|カウント|合計|成功したツイン クエリの数。|
|twinQueries.failure|失敗したツイン クエリ|カウント|合計|失敗したツイン クエリの数。|
|twinQueries.resultSize|ツイン クエリの結果のサイズ|バイト|平均|成功したツイン クエリの結果の平均、最小、および最大サイズ。|
|jobs.createTwinUpdateJob.success|成功したツイン更新ジョブの作成|カウント|合計|正常に作成されたツイン更新ジョブの数。|
|jobs.createTwinUpdateJob.failure|失敗したツイン更新ジョブの作成|カウント|合計|作成に失敗したツイン更新ジョブの数。|
|jobs.createDirectMethodJob.success|成功したメソッド呼び出しジョブの作成|カウント|合計|正常に作成されたダイレクト メソッド呼び出しジョブの数。|
|jobs.createDirectMethodJob.failure|失敗したメソッド呼び出しジョブの作成|カウント|合計|作成に失敗したダイレクト メソッド呼び出しジョブの数。|
|jobs.listJobs.success|成功したジョブ一覧の呼び出し|カウント|合計|正常に実行されたジョブ一覧の呼び出しの数。|
|jobs.listJobs.failure|失敗したジョブ一覧の呼び出し|カウント|合計|失敗したジョブ一覧の呼び出しの数。|
|jobs.cancelJob.success|成功したジョブの取り消し|カウント|合計|正常に実行されたジョブ取り消しの呼び出し。|
|jobs.cancelJob.failure|失敗したジョブの取り消し|カウント|合計|失敗したジョブ取り消しの呼び出し。|
|jobs.queryJobs.success|成功したジョブ クエリ|カウント|合計|正常に実行されたクエリ ジョブの呼び出しの数。|
|jobs.queryJobs.failure|失敗したジョブ クエリ|カウント|合計|失敗したジョブ クエリの呼び出しの数。|
|jobs.completed|完了したジョブ|カウント|合計|完了したジョブの数。|
|jobs.failed|失敗したジョブ|カウント|合計|失敗したジョブの数。|

## <a name="next-steps"></a>次のステップ
IoT Hub メトリックの概要については以上です。Azure IoT Hub の管理に関する詳細については、次のリンク先にアクセスしてください。

* [操作の監視][lnk-monitor]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Hub 開発者ガイド][lnk-devguide]
* [IoT Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

