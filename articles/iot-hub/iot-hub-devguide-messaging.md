---
title: "Azure IoT Hub メッセージについて | Microsoft Docs"
description: "開発者ガイド - IoT Hub でのデバイスからクラウドおよびクラウドからデバイスへのメッセージ。 メッセージ形式とサポートされる通信プロトコルに関する情報が含まれています。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3fc5f1a3-3711-4611-9897-d4db079b4250
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: e9e1649e4329d10ca8b87c730ad8c6beb3be818f
ms.lasthandoff: 03/16/2017


---
# <a name="send-and-receive-messages-with-iot-hub"></a>IoT Hub でのメッセージの送受信
## <a name="overview"></a>Overview
IoT Hub には、デバイスと通信するための次のメッセージング プリミティブが備わっています。

* [Device-to-cloud][lnk-d2c]: デバイスからバックエンド アプリへの通信。
* [Cloud-to-device][lnk-c2d]: バックエンド アプリ (*service* または *cloud*) からの通信。

IoT Hub のメッセージング機能の中心となる特性は、メッセージの信頼性と持続性です。 これらのプロパティにより、デバイス側では断続的な接続に対する復元性を、クラウド側ではイベント処理の負荷の急増に対する復元性を実現できます。 IoT Hub では、D2C および C2D メッセージングの両方について、 *少なくとも 1 回* の配信保証が実装されます。

IoT Hub では、[デバイス用プロトコル][lnk-protocols] (MQTT、AMQP、HTTP など) が複数サポートされています。 プロトコル間でのシームレスな相互運用性をサポートするために、IoT Hub では、すべてのデバイス用プロトコルでサポートされる[共通のメッセージ形式][lnk-message-format]が定義されています。

IoT Hub では、組み込みの [Event Hubs と互換性のあるエンドポイント][lnk-compatible-endpoint]が公開されるため、Hub から受け取った D2C メッセージをバックエンド アプリで読み取ることができます。 サブスクリプション内の他のサービスをハブにリンクして 、IoT Hub でのカスタム エンドポイントを作成することもできます。

### <a name="when-to-use"></a>使用時の注意
デバイス アプリから時系列テレメトリおよびアラートを送信するには D2C メッセージを、デバイス アプリに一方向の通知を送信するには C2D メッセージを使用してください。

報告プロパティ、Device-to-cloud メッセージ、またはファイルのアップロードのどれを使用するべきか不明な場合は、「[Device-to-cloud communication guidance (デバイスからクラウドへの通信に関するガイダンス)][lnk-d2c-guidance]」を参照してください。
必要とされるプロパティ、ダイレクト メソッド、または C2D メッセージの使用方法の詳細については、「[Cloud-to-device communication guidance][lnk-c2d-guidance]」 (C2D 通信のガイダンス) を参照してください。

IoT Hub と Event Hubs の両サービスの比較については、[IoT Hub と Event Hubs の比較][lnk-compare]に関するページをご覧ください。

## <a name="device-to-cloud-messages"></a>デバイスからクラウドへのメッセージ
デバイスからのクラウドへのメッセージはデバイス向けのエンドポイント (**/devices/{deviceId}/messages/events**) を介して送信されます。 次にルーティング ルールに従って、IoT Hub 上のサービス向けエンドポイントの 1 つにメッセージをルーティングします。 ルーティング ルールはハブを通過する D2C メッセージのプロパティを使用して、メッセージのルーティング先を決定します。 既定では、[Event Hubs][lnk-event-hubs] と互換性のある組み込みサービス向けエンドポイント (messages/events) にルーティングされます。 このため、標準的な [Event Hubs の統合と SDK][lnk-compatible-endpoint] を使用して D2C メッセージを受信できます。

IoT Hub は、ストリーミング メッセージング パターンを使用して、D2C メッセージングを実装しています。 IoT Hub の D2C メッセージは[Service Bus][lnk-servicebus] *メッセージ* というよりはむしろ [Event Hubs][lnk-event-hubs] の *イベント* であり、複数のリーダーで読み取り可能なサービスを経由する、大量のイベントが存在します。

この実装には、次のような意味があります。

* Event Hubs のイベントのように、D2C のメッセージには持続性があり、最長で 7 日間、既定の IoT Hub の **messages/events** エンドポイントに保持されます。
* Event Hubs のイベントと同様、D2C メッセージは最大 256 KB で、バッチとしてグループ化して送信を最適化できます。 バッチは最大で 256 KB です。

ただし、IoT Hub の D2C のメッセージングと Event Hubs には、いくつかの重要な違いがあります。

* 「[IoT Hub へのアクセスの制御][lnk-devguide-security]」セクションで説明されているように、IoT Hub ではデバイスごとに認証とアクセス制御を行うことができます。
* IoT Hub では、最大 10 個のカスタム エンドポイントを作成できます。 IoT Hub で構成されているルートに基づいて、エンドポイントにメッセージが配信されます。
* IoT Hub では、何百万ものデバイスを同時に接続できますが ([クォータと調整][lnk-quotas]に関するページを参照してください)、Event Hubs では、名前空間ごとの AMQP 接続が 5000 に制限されています。
* IoT Hub では、 **PartitionKey**を使用した任意のパーティション分割は許可されていません。 D2C メッセージは、発信元の **deviceId**に基づいてパーティション分割されます。
* IoT Hub のスケーリングは Event Hubs と若干異なります。 詳細については、「[IoT Hub のスケーリング][lnk-guidance-scale]」を参照してください。

D2C メッセージングの使用方法の詳細については、「[Azure IoT SDK][lnk-sdks]」を参照してください。

メッセージのルーティングを設定する方法については、「[ルーティング ルール](#routing-rules)」を参照してください。

> [!NOTE]
> HTTP を使用して D2C メッセージを送信する場合、プロパティ名と値に含めることができるのは、ASCII 英数字と ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` のみです。
> 
> 

### <a name="non-telemetry-traffic"></a>非テレメトリ トラフィック
多くの場合、デバイスからは、テレメトリ データ ポイントが送信されるだけでなく、アプリケーションのビジネス ロジック層での個別の実行と処理を必要とするメッセージと要求が送信されます。 たとえば、バック エンドで特定のアクションをトリガーする必要がある重大なアラートです。 これらの処理を専用に行うエンドポイントに対して、このようなメッセージを送信するルーティング ルールを簡単に記述できます。

この種類のメッセージを処理する最適な方法の詳細については、[IoT Hub のデバイスからクラウドへのメッセージを処理する方法][lnk-d2c-tutorial]に関するチュートリアルをご覧ください。

### <a name="routing-rules"></a>ルーティング ルール

IoT Hub では、メッセージ プロパティに基づいて IoT Hub エンドポイントにメッセージをルーティングできます。 ルーティング ルールによってメッセージ送信を柔軟に実行できるため、メッセージを処理するために追加のサービスを起動したり、追加のコードを記述したりする必要がありません。 各ルーティング ルールには、次のプロパティがあります。

* **名前**。 ルールを識別する一意の名前。
* **ソース**。 処理するデータ ストリームの元データ。 たとえば、デバイス テレメトリです。
* **条件**。 メッセージのプロパティに対して実行され、エンドポイントと一致するかどうかの確認のために使用されるルーティング ルールのクエリ式。 ルート条件の構築の詳細については、[リファレンス - デバイス ツインとジョブのクエリ言語][lnk-devguide-query-language]に関する記事を参照してください。
* **エンドポイント**。 IoT Hub が条件に一致するメッセージを送信するエンドポイントの名前。 エンドポイントは、IoT Hub と同じリージョン内に存在する必要があり、そうでない場合は、リージョン間の書き込みに対して課金されるおそれがあります。

1 つのメッセージが複数のルーティング ルールの条件と一致することがあり、その場合、IoT Hub は一致する各ルールに関連するエンドポイントにメッセージを送信します。 IoT Hub はメッセージ配信の重複を自動的に排除するため、あるメッセージが複数のルールに一致し、それらのルールの配信先がすべて同じであった場合は、配信先には 1 度のみメッセージが配信されます。

IoT Hub でのカスタム エンドポイントの作成の詳細については、[IoT Hub エンドポイント][lnk-devguide-endpoints]に関するページを参照してください。

### <a name="built-in-endpoint-messagesevents"></a>組み込みエンドポイント: messages/events

IoT Hub は、Event Hub と互換性のある組み込みのメッセージング エンドポイント **messages/events** を制御するための以下のプロパティを公開しています。

* **パーティション数**。 このプロパティは作成時に設定し、D2C イベントを取り込む場合の[パーティション][lnk-event-hub-partitions]数を定義します。
* **保存期間**。 このプロパティは、IoT Hub によってメッセージが保持される期間を日数で指定します。 既定は 1 日ですが、7 日間に増やすことができます。

IoT Hub では、組み込みの D2C 受信エンドポイントでコンシューマー グループを管理ができます。

既定では、メッセージのルーティング ルールと明示的に一致しないメッセージはすべて、組み込みエンドポイントに書き込まれます。 このフォールバック ルートを無効にした場合は、どのメッセージのルーティング ルールにも明示的に一致しないメッセージは破棄されます。

リテンション期間は、[IoT Hub のリソース プロバイダー REST API][lnk-resource-provider-apis] を使用してプログラムにより変更するか、[Azure Portal][lnk-management-portal] を使用して変更できます。

### <a name="anti-spoofing-properties"></a>なりすまし対策のプロパティ
D2C メッセージでのデバイスのなりすましを回避するために、IoT Hub では、すべてのメッセージに次のプロパティを持つスタンプが使用されます。

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

最初の 2 つには、「[デバイス ID プロパティ][lnk-device-properties]」で説明されている発信元デバイスの **deviceId** と **generationId** が含まれています。

**ConnectionAuthMethod** プロパティには、次のプロパティを使用してシリアル化された JSON オブジェクトが含まれています。

```
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="cloud-to-device-messages"></a>クラウドからデバイスへのメッセージ
C2D メッセージはサービス向けエンドポイント (**/messages/devicebound**) を介して送信できます。 デバイスはそのメッセージをデバイス固有のエンドポイント (**/devices/{deviceId}/messages/devicebound**) を介して受信します。

個々の C2D メッセージの宛先は単一のデバイスであり、**to** プロパティが **/devices/{deviceId}/messages/devicebound** に設定されます。

> [!IMPORTANT]
> 各デバイスのキューで保持できる C2D メッセージは最大 50 個です。 同じデバイスにそれ以上のメッセージを送信しようとすると、エラーが発生します。
> 
> [!NOTE]
> C2D メッセージを送信するとき、プロパティ名と値に含めることができるのは、ASCII 英数字と ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` のみです。
> 
> 

### <a name="message-lifecycle"></a>メッセージのライフサイクル
少なくとも 1 回のメッセージ配信を保証するために、IoT Hub は C2D デバイス メッセージをデバイスごとのキューに保持します。 IoT Hub がキューからメッセージを削除できるように、デバイスで明示的に *完了* を確認する必要があります。 そうすることで、接続とデバイスの障害に対する復元性が保証されます。

次の図に、C2D メッセージのライフサイクルの状態に関するグラフを示します。

![クラウドとデバイス間のメッセージのライフ サイクル][img-lifecycle]

サービスからメッセージが送信されると、そのメッセージは *エンキューされた*と見なされます。 デバイスでメッセージを*受信*する場合、同じデバイス上の他のスレッドが他のメッセージの受信を開始できるようにするために、IoT Hub によりメッセージが*ロック*されます (状態は**非表示**に設定されます)。 デバイスのスレッドがメッセージの処理を完了すると、メッセージを *完了* して IoT Hub にその旨を通知します。

デバイスは次の動作をすることもあります。

* メッセージの*拒否*。この場合、IoT Hub によってメッセージの状態が**配信不能**に設定されます。 注: MQTT に接続するデバイスは、C2D メッセージを拒否することはできません。
* メッセージの*破棄*。この場合、IoT Hub によってメッセージがキューに戻され、状態が**エンキュー**に設定されます。

スレッドが IoT Hub に通知することなく、メッセージの処理に失敗することもあります。 その場合、*表示 (またはロック) がタイムアウト*になった後で、メッセージの状態が自動的に**非表示**から**エンキュー**に切り替わります。 このタイムアウトの既定値は 1 分です。

メッセージの状態は**エンキュー**と**非表示**の間で切り替わりますが、この回数が IoT Hub の**最大配信数**のプロパティで指定された上限に達すると、 IoT Hub によってメッセージの状態が**配信不能**に設定されます。 同様に、有効期限 ([有効期限][lnk-ttl]に関するセクションを参照) が切れた後も、IoT Hub によってメッセージの状態が**配信不能**に設定されます。

クラウドからデバイスへのメッセージのチュートリアルは、[IoT Hub でクラウドからデバイスへのメッセージを送信する方法のチュートリアル][lnk-c2d-tutorial]に関するページをご覧ください。 各種 API と SDK での C2D 機能の公開方法に関するリファレンス トピックについては、「[Azure IoT SDK][lnk-sdks]」を参照してください。

> [!NOTE]
> 通常、メッセージの損失がアプリケーション ロジックに影響しない場合、C2D メッセージは必ず完了されます。 たとえば、メッセージの内容が正常にローカル ストレージで保持されている場合、操作が正常に実行された場合などがあります。 また、メッセージには、メッセージの損失がアプリケーションの機能に影響しないことを示す一時的な情報が含まれている場合もあります。 実行時間の長いタスクの場合、タスクの説明をローカル ストレージに保持した後で C2D メッセージを完了できます。 その後、タスクの進行状況のさまざまな段階で、1 つ以上の D2C メッセージによりソリューション バックエンドに通知できます。
> 
> 

### <a name="message-expiration-time-to-live"></a>メッセージの有効期限
すべての C2D メッセージに有効期限があります。 これはサービス (**ExpiryTimeUtc** プロパティ) で設定するか、IoT Hub のプロパティとして指定した既定の *time to live* を使用して IoT Hub で設定できます。 「[C2D の構成オプション][lnk-c2d-configuration]」を参照してください。

> [!NOTE]
> メッセージの有効期限を利用し、接続されていないデバイスにメッセージが送信されないようにするには、一般的には、有効期限に短い値を設定します。 この方法を使用すると、デバイスの接続状態を維持するのと同じ結果が得られますが、より効率化されます。 メッセージの受信確認を要求すると、メッセージを受信できるデバイスや、オンラインではないデバイスまたは障害が発生しているデバイスについて IoT Hub から通知を受け取ります。
> 
> 

### <a name="message-feedback"></a>メッセージのフィードバック
C2D メッセージを送信するときに、サービスは、そのメッセージの最終状態に関するメッセージごとのフィードバックの配信を要求できます。

* **Ack** プロパティを **positive** に設定すると、C2D メッセージの状態が**完了**に達した場合に限り、IoT Hub によりフィードバック メッセージが生成されます。
* **Ack** プロパティを **negative** に設定すると、C2D メッセージの状態が**配信不能**に達した場合に限り、IoT Hub によりフィードバック メッセージが生成されます。
* **Ack** プロパティを **full** に設定すると、上記のどちらの場合にも IoT Hub によりフィードバック メッセージが生成されます。

> [!NOTE]
> **Ack** が **full** の場合で、フィードバック メッセージを受信しない場合は、フィードバック メッセージの有効期限が切れたことを意味します。 このとき、元のメッセージがどうなったかをサービスが認識することはできません。 実際には、有効期限切れになる前にフィードバックをサービスが確実に処理できることが必要です。 有効期限は最長 2 日間であるため、障害が発生した場合も、サービスを再び稼働させる時間は十分にあります。
> 
> 

[エンドポイント][lnk-endpoints]に関するページで説明されているように、IoT Hub はサービス向けエンドポイント (**/messages/servicebound/feedback**) 経由でメッセージとしてフィードバックを配信します。 フィードバックを受信するためのセマンティクスは C2D メッセージの場合と同様であり、[メッセージのライフサイクル][lnk-lifecycle]も同じです。 可能な限り、メッセージのフィードバックは、次の形式で 1 つのメッセージのバッチとして処理します。

| プロパティ | Description |
| --- | --- |
| EnqueuedTime |メッセージが作成された日時を示すタイムスタンプ。 |
| UserId |`{iot hub name}` |
| ContentType |`application/vnd.microsoft.iothub.feedback.json` |

本文はシリアル化された JSON レコードの配列で、それぞれ次のプロパティを持っています。

| プロパティ | Description |
| --- | --- |
| EnqueuedTimeUtc |メッセージの結果が発生した日時を示すタイムスタンプ。 たとえば、デバイスの完了やメッセージの期限切れなどです。 |
| OriginalMessageId |**MessageId** です。 |
| StatusCode |必須の整数です。 IoT Hub によって生成されたフィードバック メッセージで使用されます。 <br/> 0 = 成功 <br/> 1 = メッセージの有効期限が切れました <br/> 2 = 最大配信数を超えました <br/> 3 = メッセージは拒否されました |
| 説明 |**StatusCode**の文字列値。 |
| deviceId |**DeviceId** 。 |
| DeviceGenerationId |**DeviceGenerationId** 。 |

> [!IMPORTANT]
> メッセージのフィードバックを元のメッセージと関連付けることができるように、サービスは C2D メッセージの **MessageId** を指定する必要があります。
> 
> 

次の例は、フィードバック メッセージの本文を示しています。

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

### <a name="cloud-to-device-configuration-options"></a>C2D の構成オプション
各 IoT Hub では、C2D メッセージング用に次の構成オプションを公開しています。

| プロパティ | Description | 範囲と既定値 |
| --- | --- | --- |
| defaultTtlAsIso8601 |C2D メッセージの TTL の既定値。 |最大 2D の ISO_8601 書式による間隔 (最小 1 分)。 既定値: 1 時間。 |
| maxDeliveryCount |デバイスごとの C2D キューの最大配信数。 |1 ～ 100。 既定値: 10。 |
| feedback.ttlAsIso8601 |サービス宛てのフィードバックのメッセージの保有期間。 |最大 2D の ISO_8601 書式による間隔 (最小 1 分)。 既定値: 1 時間。 |
| feedback.maxDeliveryCount |フィードバック キューの最大配信数。 |1 ～ 100。 既定値: 100。 |

詳細については、[IoT Hub の作成][lnk-portal] に関するページをご覧ください。

## <a name="read-device-to-cloud-messages"></a>デバイスからクラウドへのメッセージの読み取り
IoT Hub は、**messages/events** 組み込みエンドポイントをバックエンド サービスに公開して、ハブで受信した D2C メッセージを読み取ります。 このエンドポイントには Event Hubs との互換性があり、Event Hubs のサービスがメッセージを読み取るためにサポートするすべてのメカニズムを使用できます。

IoT Hub でカスタム エンドポイントを作成することもできます。 IoT Hub は現在、カスタム エンドポイントとして Event Hubs、Service Bus キュー、Service Bus トピックをサポートします。 これらのサービスからの読み取りの詳細については、[Event Hubs][lnk-getstarted-eh]からの読み取り、 [Service Bus キュー][lnk-getstarted-queue]からの読み取り、 [Service Bus トピック][lnk-getstarted-topic]からの読み取りに関する記事を参照してください。

### <a name="reading-from-the-built-in-endpoint"></a>組み込みエンドポイントからの読み取り

[Azure Service Bus SDK for .NET][lnk-servicebus-sdk] または [Event Hubs - イベント プロセッサ ホスト][lnk-eventprocessorhost]を使用する場合、適切なアクセス許可があれば任意の IoT Hub の接続文字列を使用することができます。 これにより、 **メッセージやイベント** を Event Hub の名前として使用できます。

IoT Hub を認識しない SDK (または製品の統合) を使用する場合は、[Azure Portal][lnk-management-portal] の IoT Hub 設定から、Event Hub と互換性があるエンドポイントと Event Hub と互換性がある名前を取得する必要があります。

1. IoT Hub ブレードで、**[エンドポイント]** をクリックします。
2. **[Built-in endpoints]** (組み込みエンドポイント) セクションで、**[イベント]** をクリックします。 ブレードには、**[Event Hub-compatible endpoint]** (Event Hub と互換性のあるエンドポイント)、**[Event Hub-compatible name]** (Event Hub と互換性のある名前)、**[パーティション]**、**[保持期間]**、**[コンシューマー グループ]** が表示されます。
   
    ![Device-to-cloud settings][img-eventhubcompatible]

> [!NOTE]
> IoT Hub SDK には IoT Hub エンドポイント名が必要であり、**エンドポイント** ブレードに表示されているように、**messages/events** となります。
>
>

> [!NOTE]
> SDK で **[Hostname]** (ホスト名) または **[Namespace]** (名前空間) の値が必要な場合は、**[Event Hub-compatible endpoint]** (イベント ハブと互換性のあるエンドポイント) からスキームを削除します。 たとえば、Event Hub 互換のエンドポイントが **sb://iothub-ns-myiothub-1234.servicebus.windows.net/** の場合、**Hostname** は **iothub-ns-myiothub-1234.servicebus.windows.net**、**Namespace** は **iothub-ns-myiothub-1234** です。
> 
>

この場合、指定したイベント ハブに接続するための **ServiceConnect** のアクセス許可を持つ、共有アクセス ポリシーを使用できます。

前の情報を使用してイベント ハブの接続文字列を作成する必要がある場合は、次のパターンを使用できます。

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

次に、IoT Hub から公開されている、Event Hub 互換のエンドポイントを使用できる SDK と統合の一覧を示します。

* [Event Hubs の Java クライアント](https://github.com/hdinsight/eventhubs-client)
* [Apache Storm のスパウト](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md)。 [スパウトのソース](https://github.com/apache/storm/tree/master/external/storm-eventhubs) は GitHub で確認できます。
* [Apache Spark の統合](../hdinsight/hdinsight-apache-spark-eventhub-streaming.md)

## <a name="reference-topics"></a>参照トピック:
以下の参照トピックは、IoT Hub とのメッセージの交換に関する詳細情報を提供しています。

## <a name="message-format"></a>メッセージの形式
IoT Hub のメッセージは、次の要素で構成されています。

* 一連の *システム プロパティ*。 IoT Hub が解釈または設定するプロパティです。 この設定は、事前に決定されています。
* 一連の *アプリケーション プロパティ*。 メッセージ本文を逆シリアル化しなくてもアプリケーションが定義してアクセスできる、文字列プロパティのディクショナリです。 IoT Hub によってこれらのプロパティが変更されることはありません。
* 非透過的なバイナリ本文。

各種プロトコルでメッセージをエンコードする方法の詳細については、「[Azure IoT SDK][lnk-sdks]」を参照してください。

次のテーブルは、IoT Hub メッセージ内の一連のシステム プロパティです。

| プロパティ | Description |
| --- | --- |
| MessageId |要求/応答パターンに使用する、メッセージのユーザー設定 ID。 形式: ASCII 7 ビット英数字の大文字と小文字が区別される文字列 (最大 128 文字) + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`。 |
| Sequence number |IoT Hub によって各 C2D メッセージに割り当てられる数値 (デバイスとキューごとに一意)。 |
| To |[C2D][lnk-c2d] メッセージで指定される宛先。 |
| ExpiryTimeUtc |メッセージの有効期限の日時。 |
| EnqueuedTime |IoT Hub が[クラウドからデバイス][lnk-c2d]へのメッセージを受信した日時。 |
| CorrelationId |通常、要求/応答パターンで要求の MessageId を格納する、応答メッセージの文字列プロパティ。 |
| UserId |メッセージの送信元を指定するために使用される ID。 IoT Hub でメッセージが生成されると、 `{iot hub name}`に設定されます。 |
| Ack |フィードバック メッセージのジェネレーター。 このプロパティは、デバイスがメッセージを使用した結果としてのフィードバック メッセージの生成を IoT Hub に要求するために、C2D メッセージで使用されます。 使用可能な値: **none** (既定値): フィードバック メッセージは生成されません。**positive**: メッセージが完了した場合にフィードバック メッセージを受信します。**negative**: デバイスでメッセージが完了しないまま、メッセージの有効期限が切れた場合 (または最大配信数に達した場合) にフィードバック メッセージを受信します。**full**: positive と negative の両方の値を意味します。 詳細については、「[メッセージのフィードバック][lnk-feedback]を参照してください。 |
| ConnectionDeviceId |IoT Hub で D2C メッセージに対して設定される ID。 メッセージを送信したデバイスの **deviceId** が含まれます。 |
| ConnectionDeviceGenerationId |IoT Hub で D2C メッセージに対して設定される ID。 メッセージを送信したデバイスの **generationId** (「[デバイス ID のプロパティ][lnk-device-properties]」を参照) が含まれています。 |
| ConnectionAuthMethod |IoT Hub で D2C メッセージに対して設定される認証方法。 このプロパティには、メッセージを送信するデバイスの認証に使用する認証方法に関する情報が含まれます。 詳細については、[D2C のなりすまし対策][lnk-antispoofing]に関するセクションを参照してください。 |

## <a name="message-size"></a>メッセージ サイズ

IoT Hub では、メッセージのサイズは、プロトコルに関係なく実際のペイロードのみを考慮して測定されます。 バイト単位のサイズは次の和として計算されます。

* 本文のサイズ (バイト単位)
* メッセージ システム プロパティの全値のサイズ (バイト単位)
* すべてのユーザー プロパティの名前と値のサイズ (バイト単位)

プロパティの名前と値は ASCII 文字に制限されているため、文字列の長さがバイト単位のサイズと等しくなります。

## <a name="communication-protocols"></a>通信プロトコル
IoT Hub では、デバイスはデバイス側の通信で [MQTT][lnk-mqtt]、WebSocket 経由の MQTT、[AMQP][lnk-amqp]、WebSocket 経由の AMQP、および HTTP プロトコルを使用できます。 プロトコル選択に関する高度な推奨事項には次のようなものがあります。

| プロトコル | そのプロトコルを選択しなければならない場合 |
| --- | --- |
| MQTT <br> WebSocket 経由の MQTT |同じ TLS 接続で (それぞれがデバイス独自の資格情報を有する) 複数のデバイスに接続する必要がないすべてのデバイスで使用します。 |
| AMQP <br> WebSocket 経由の AMQP |デバイスを多重化する接続を活用するために、フィールド ゲートウェイとクラウド ゲートウェイで使用します。 |
| HTTP |その他のプロトコルをサポートできないデバイスに使用します。 |

デバイス側の通信用プロトコルを選択する場合、次の点を考慮してください。

* **C2D のパターン**。 HTTP には、サーバー プッシュを実装する効率的な方法がありません。 そのため、HTTP を使用する場合、デバイスは IoT Hub に対して C2D メッセージのポーリングを行います。 この方法は、デバイスと IoT Hub の両方で非効率的です。 現在の HTTP ガイドラインでは、各デバイスによるメッセージのポーリングの間隔は 25 分以上となっています。 一方、MQTT と AMQP では、C2D メッセージを受信する場合のサーバー プッシュがサポートされています。 IoT Hub からデバイスへのメッセージも即座にプッシュできます。 配信の待機時間が問題となる場合は、MQTT または AMQP が使用に最適なプロトコルです。 頻繁に接続されないデバイスの場合は、HTTP でも対応できます。
* **フィールド ゲートウェイ**。 MQTT と HTTP を使用する場合は、同じ TLS 接続で (それぞれがデバイス独自の資格情報を有する) 複数のデバイスに接続することはできません。 したがって、[フィールド ゲートウェイ シナリオ][lnk-azure-gateway-guidance]の場合、これらのプロトコルでは、フィールド ゲートウェイに接続するデバイスごとにフィールド ゲートウェイと IoT Hub の間に TLS 接続が 1 つ必要となるため、次善のプロトコルになります。
* **リソースの少ないデバイス**。 MQTT および HTTP ライブラリのフットプリントは、AMQP ライブラリの場合より小さくなります。 そのため、リソースが限られたデバイス (RAM が 1 MB 未満など) の場合、MQTT と HTTP/1 が実装可能な唯一のプロトコルとなります。
* **ネットワーク トラバーサル**。 標準的な AMQP プロトコルはポート 5671 を使用しますが、MQTT はポート 8883 でリッスンします。このため、ネットワークが HTTP 以外のプロトコルに限定されている場合、問題が発生する可能性があります。 このようなシナリオでは、WebSocket 経由の HTTP、WebSocket 経由の AMQP、および HTTP を使用できます。
* **ペイロードのサイズ**。 MQTT と AMQP はバイナリ プロトコルであり、HTTP よりもペイロードが小さくなります。

> [!NOTE]
> HTTP を使用する場合、各デバイスでは 25 分以上の間隔で C2D メッセージをポーリングする必要があります。 ただし、開発段階では、ポーリングの頻度を 25 分より短く設定することができます。
> 
> 

## <a name="port-numbers"></a>ポート番号
デバイスは、さまざまなプロトコルを使用して Azure の IoT Hub と通信できます。 通常、プロトコルの選択は、ソリューションの特定の要件によって左右されます。 次の表に、デバイスが特定のプロトコルを使用できるようにするために開く必要がある送信ポートを示します。

| プロトコル | Port |
| --- | --- |
| MQTT |8883 |
| WebSocket 経由の MQTT |443 |
| AMQP |5671 |
| AMQP over WebSocket |443 |
| HTTP |443 |
| LWM2M (デバイス管理) |5684 |

Azure リージョンに IoT Hub を作成すると、その IoT Hub は IoT Hub の存続期間中同じ IP アドレスを保持します。 ただし、サービス品質を維持するため、Microsoft によって IoT Hub が別のスケール ユニットに移動されると、新しい IP アドレスが割り当てられます。

## <a name="notes-on-mqtt-support"></a>MQTT サポートに関する留意事項
IoT Hub では、次の制限事項と特定の動作で MQTT v3.1.1 プロトコルを実装します。

* **QoS 2 はサポートされていません**。 デバイス アプリが **QoS 2** によってメッセージを発行すると、IoT Hub はネットワーク接続を閉じます。 デバイス アプリが **QoS 2** を使用してトピックをサブスクライブしている場合、IoT Hub は **SUBACK** パケットに最大の QoS レベル 1 を許可します。
* **メッセージの保持は維持されません**。 デバイス アプリが RETAIN フラグを 1 に設定してメッセージを発行すると、IoT Hub はそのメッセージに **x-opt-retain** アプリケーション プロパティを追加します。 この場合は、IoT Hub は保持メッセージを保持するのでなく、バックエンド アプリに渡します。

詳細については、「[IoT Hub の MQTT サポート][lnk-devguide-mqtt]」を参照してください。

最後の考慮事項として、[Azure IoT Hub プロトコル ゲートウェイ][lnk-azure-protocol-gateway]に関するページを確認してください。このゲートウェイでは、IoT Hub と直接やり取りする高性能のカスタム プロトコル ゲートウェイをデプロイできます。 Azure IoT プロトコル ゲートウェイでは、ブラウンフィールド MQTT デプロイメントまたは他のカスタム プロトコルに応じてデバイス プロトコルをカスタマイズすることができます。 ただし、このアプローチでは、カスタム プロトコル ゲートウェイを実行して運用する必要があります。

## <a name="additional-reference-material"></a>参考資料
IoT Hub 開発者ガイド内の他の参照トピックは次のとおりです。

* [IoT Hub エンドポイント][lnk-endpoints]: 各 IoT Hub でランタイムと管理の操作のために公開される、さまざまなエンドポイントについて説明します。
* [調整とクォータ][lnk-quotas]: IoT Hub サービスに適用されるクォータと、サービスを使用するときに想定される調整の動作について説明します。
* [Azure IoT device SDK とサービス SDK][lnk-sdks]: IoT Hub とやりとりするデバイスとサービス アプリの両方を開発する際に使用できるさまざまな言語の SDK を紹介します。
* [IoT Hub のツインおよびジョブ向けのクエリ言語][lnk-query]: IoT Hub からデバイス ツインおよびジョブに関する情報を取得する際に使用できる IoT Hub のクエリ言語について説明します。
* [IoT Hub の MQTT サポート][lnk-devguide-mqtt]: IoT Hub での MQTT プロトコルのサポートについて詳しく説明します。

## <a name="next-steps"></a>次のステップ
IoT Hub でメッセージを送受信する方法を理解できたら、次の IoT Hub 開発者ガイドのトピックも参考にしてください。

* [デバイスからファイルをアップロードする][lnk-devguide-upload]
* [IoT Hub でデバイス ID を管理する][lnk-devguide-identities]
* [IoT Hub へのアクセスの制御][lnk-devguide-security]
* [デバイス ツインを使って状態と構成を同期する][lnk-devguide-device-twins]
* [デバイスでダイレクト メソッドを呼び出す][lnk-devguide-directmethods]
* [複数デバイスで実行されるジョブのスケジュール設定][lnk-devguide-jobs]

この記事で説明した概念を試す場合は、次の IoT Hub のチュートリアルをご利用ください。

* [Azure IoT Hub を使ってみる][lnk-getstarted-tutorial]
* [IoT Hub を使用した C2D メッセージの送信方法][lnk-c2d-tutorial]
* [IoT Hub の D2C メッセージの処理方法][lnk-d2c-tutorial]

[img-lifecycle]: ./media/iot-hub-devguide-messaging/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide-messaging/eventhubcompatible.png

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-amqp]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf
[lnk-mqtt]: http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-management-portal]: https://portal.azure.com
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-portal]: iot-hub-create-through-portal.md
[lnk-getstarted-eh]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-getstarted-queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[lnk-getstarted-topic]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md

[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-compatible-endpoint]: iot-hub-devguide-messaging.md#read-device-to-cloud-messages
[lnk-protocols]: iot-hub-devguide-messaging.md#communication-protocols
[lnk-message-format]: iot-hub-devguide-messaging.md#message-format
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-ttl]: iot-hub-devguide-messaging.md#message-expiration-time-to-live
[lnk-c2d-configuration]: iot-hub-devguide-messaging.md#cloud-to-device-configuration-options
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle
[lnk-feedback]: iot-hub-devguide-messaging.md#message-feedback
[lnk-antispoofing]: iot-hub-devguide-messaging.md#anti-spoofing-properties
[lnk-compare]: iot-hub-compare-event-hubs.md

[lnk-devguide-upload]: iot-hub-devguide-file-upload.md
[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx
[lnk-devguide-query-language]: iot-hub-devguide-query-language.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-event-hub-partitions]: ../event-hubs/event-hubs-what-is-event-hubs.md#partitions

