---
title: "Azure IoT Hub ID レジストリについて | Microsoft Docs"
description: "開発者ガイド - IoT Hub ID レジストリおよびこのレジストリを使用してデバイスを管理する方法の説明。 デバイス ID の一括でのインポートとエクスポートに関する情報が含まれています。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0706eccd-e84c-4ae7-bbd4-2b1a22241147
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 75a2fa16a7e33cf85746538e120ca90a389b05c5
ms.lasthandoff: 03/24/2017


---
# <a name="understand-identity-registry-in-your-iot-hub"></a>IoT Hub の ID レジストリの理解

## <a name="overview"></a>概要

すべての IoT Hub には、その IoT Hub への接続が許可されたデバイスに関する情報を保存する ID レジストリがあります。 デバイスを IoT Hub に接続できるようにするには、そのデバイスのエントリが IoT Hub の ID レジストリに存在する必要があります。 さらに、ID レジストリに保存されている資格情報に基づいて、デバイスが IoT Hub で認証される必要があります。

大まかに言うと、ID レジストリは、デバイス ID リソースの REST 対応コレクションです。 この ID レジストリにエントリを追加すると、IoT Hub によって、転送中の C2D メッセージを含むキューなどのデバイス単位のリソースがサービス内に作成されます。

### <a name="when-to-use"></a>使用時の注意

ID レジストリは、IoT Hub に接続するデバイスをプロビジョニングする必要があるとき、およびハブのデバイス向けのエンドポイントに対するアクセスをデバイス単位で制御する必要があるときに使用します。

> [!NOTE]
> ID レジストリには、アプリケーション固有のメタデータは含まれません。

## <a name="identity-registry-operations"></a>ID レジストリの操作

IoT Hub の ID レジストリでは、次の操作が公開されています。

* デバイス ID の作成
* デバイス ID の更新
* ID ごとのデバイス ID の取得
* デバイス ID の削除
* 最大 1000 個の ID の一覧表示
* Azure BLOB ストレージへのすべての ID のエクスポート
* Azure BLOB ストレージからのすべての ID のインポート

これらすべての操作で、[RFC7232][lnk-rfc7232] に指定されているオプティミスティック同時実行制御を使用できます。

> [!IMPORTANT]
> IoT Hub の ID レジストリにあるすべての ID を取得する唯一の方法は、[エクスポート][lnk-export]機能を使用することです。

IoT Hub の ID レジストリの特徴は次のとおりです。

* アプリケーションのメタデータは含まれていません。
* **deviceId** をキーとして使用することで、ディクショナリと同様にアクセスできます。
* 詳細なクエリはサポートされていません。

通常、IoT ソリューションにはソリューション固有のストアがあり、各ストアにはアプリケーション固有のメタデータが含まれています。 たとえば、スマート ビルディング ソリューションのソリューション固有のストアでは、気温センサーをデプロイする部屋を記録します。

> [!IMPORTANT]
> ID レジストリは、デバイスの管理およびプロビジョニング操作でのみ使用します。 実行時の高スループット操作が、ID レジストリに対する操作の影響を受けないようにする必要があります。 たとえば、コマンドを送信する前に、デバイスの接続状態を確認するやり方は、サポートされていないパターンです。 ID レジストリの[スロットル率][lnk-quotas]と[デバイス ハートビート パターン][lnk-guidance-heartbeat]を必ず確認してください。

## <a name="disable-devices"></a>デバイスの無効化

ID レジストリで ID の **status** プロパティを更新することにより、デバイスを無効にすることができます。 通常、このプロパティは次の 2 つのシナリオで使用します。

* オーケストレーション プロセスのプロビジョニング中。 詳細については、[デバイスのプロビジョニング][lnk-guidance-provisioning]に関するページを参照してください。
* 何らかの理由でデバイスが侵害された、または許可されていないと考えられる場合。

## <a name="import-and-export-device-identities"></a>デバイス ID のインポートとエクスポート

[IoT Hub のリソース プロバイダー エンドポイント][lnk-endpoints]に対する非同期操作を使用して、IoT Hub の ID レジストリから一括でデバイス ID をエクスポートすることができます。 エクスポートは、顧客が指定した BLOB コンテナーを使用して、デバイス ID レジストリから読み取ったデバイス ID データを保存する、長時間実行されるジョブです。

[IoT Hub のリソース プロバイダー エンドポイント][lnk-endpoints]に対する非同期操作を使用して、IoT Hub の ID レジストリから一括でデバイス ID をインポートすることができます。 インポートは、顧客が指定した BLOB コンテナー内のデータを使用してデバイス ID データを ID レジストリに書き込む、長時間実行ジョブです。

* インポート API とエクスポート API の詳細については、[IoT Hub のリソース プロバイダー REST API][lnk-resource-provider-apis] に関する記事を参照してください。
* インポートおよびエクスポート ジョブの実行方法の詳細については、[IoT Hub デバイス ID の一括管理][lnk-bulk-identity]に関するページを参照してください。

## <a name="device-provisioning"></a>デバイス プロビジョニング

特定の IoT ソリューションに格納されるデバイス データは、そのソリューションの具体的な要件によって異なりますが、 ソリューションには少なくともデバイスの ID と認証キーを格納する必要があります。 Azure IoT Hub には、各デバイスの ID、認証キー、状態コードなどの値を保存できる ID レジストリがあります。 ソリューションでは、Azure テーブル ストレージ、Azure BLOB ストレージ、Azure DocumentDB などの Azure サービスを使用して、さらにデバイス データを保存できます。

*デバイス プロビジョニング* とは、最初のデバイス データをソリューション内のストアに追加するプロセスです。 新しいデバイスをハブに接続できるようにするには、デバイスの ID とキーを IoT Hub ID レジストリに追加する必要があります。 プロビジョニング プロセスの一環として、他のソリューション ストアにあるデバイス固有データの初期化が必要になる場合があります。

## <a name="device-heartbeat"></a>デバイスのハートビート

IoT Hub ID レジストリには、**connectionState** というフィールドが含まれています。 **connectionState**フィールドは、開発とデバッグ中にのみ使用します。 IoT ソリューションでは実行時にこのフィールドを照会しないでください (たとえば、C2D メッセージまたは SMS を送信するかどうかを決定するためにデバイスが接続されているかどうかを確認する目的で)。

デバイスが接続されているかどうかを (実行時に、または **connectionState** プロパティを使用するよりも正確に) IoT ソリューションで把握する必要がある場合は、*ハートビート パターン*を実装してください。

ハートビート パターンでは、デバイスは一定の時間ごとに 1 回以上、D2C メッセージを送信します。(たとえば、1 時間ごとに 1 回以上)。 したがって、デバイスは送信するデータを保持していなくても、空の D2C メッセージを送信します (通常は、それをハートビートとして識別するプロパティが伴います)。 サービス側では、ソリューションは、各デバイスから受信した最後のハートビートのマップを保持します。 所定の時間内にハートビート メッセージを受信しなかった場合、ソリューションは、デバイスに問題があるとみなします。

より複雑な実装になると、[操作の監視][lnk-devguide-opmon]からの情報を取り込むことで、接続または通信を試みているが失敗しているデバイスを識別することが可能です。 ハートビート パターンを実装する場合は、[IoT Hub のクォータとスロットル][lnk-quotas]を必ず確認してください。

> [!NOTE]
> C2D メッセージを送信するかどうかを判断するためだけに IoT ソリューションでデバイス接続状態が必要であり、多数のデバイスにメッセージをブロードキャストしない場合は、簡単なパターンとして短い有効期間の使用を検討する必要があります。 この方法を使用すると、ハートビート パターンを使用してデバイス接続状態レジストリを維持するのと同じ結果を得られますが、効率が向上します。 また、メッセージの受信確認を要求することにより、メッセージを受信できるデバイスおよびオンラインではないデバイスや障害が発生しているデバイスについて IoT Hub から通知を受け取ることもできます。

## <a name="reference-topics"></a>参照トピック:

以下の参照トピックに、ID レジストリに関する詳細情報を示します。

## <a name="device-identity-properties"></a>デバイス ID のプロパティ

デバイス ID は、次のプロパティを持つ JSON ドキュメントとして表されます。

| プロパティ | オプション | 説明 |
| --- | --- | --- |
| deviceId |必須、読み取り専用 (更新時) |ASCII 7 ビット英数字の大文字と小文字が区別される文字列 (最大 128 文字) + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`。 |
| generationId |必須、読み取り専用 |IoT Hub によって生成された、大文字と小文字が区別される文字列 (最大 128 文字)。 この値は、デバイスが削除されて再作成された場合に、同じ **deviceId** を持つデバイスを区別するために使用します。 |
| etag |必須、読み取り専用 |[RFC7232][lnk-rfc7232] に準拠した、デバイス ID の弱い ETag を表す文字列。 |
| auth |省略可能 |認証情報とセキュリティのマテリアルを含む複合オブジェクト。 |
| auth.symkey |省略可能 |base64 形式でプライマリ キーとセカンダリ キーを格納する複合オブジェクト。 |
| status |必須 |アクセス インジケーター。 **[有効]** または **[無効]** のいずれか。 **[有効]**の場合、デバイスからの接続が許可されます。 **[無効]**の場合、このデバイスからデバイス向けのエンドポイントにアクセスできません。 |
| statusReason |省略可能 |デバイス ID の状態の理由を格納する、長さが 128 文字の文字列。 すべての UTF-8 文字を使用できます。 |
| statusUpdateTime |読み取り専用 |状態が最後に更新された日時を示す時間のインジケーター。 |
| connectionState |読み取り専用 |接続状態を示すフィールド。**Connected** または **Disconnected** のいずれか。 このフィールドは、デバイスの接続状態に関する IoT Hub ビューを表します。 **重要**: このフィールドは、開発およびデバッグ専用として使用してください。 接続状態は、MQTT または AMQP を使用するデバイスについてのみ更新されます。 また、この更新はプロトコル レベルの ping (MQTT ping または AMQP ping) に基づいており、遅延は最大でもわずか 5 分です。 このため、接続状態にあると報告されているが切断状態にあるデバイスのように、偽陽性を示す可能性があります。 |
| connectionStateUpdatedTime |読み取り専用 |前回接続状態が更新された日時を示す時間のインジケーター。 |
| lastActivityTime |読み取り専用 |前回デバイスが接続された日時またはメッセージを送受信した日時を示す時間のインジケーター。 |

> [!NOTE]
> 接続状態が表すのは、その接続状態を示す IoT Hub ビューのみです。 ネットワークの状態と構成によっては、この状態の更新が遅延することがあります。

## <a name="additional-reference-material"></a>参考資料

IoT Hub 開発者ガイド内の他の参照トピックは次のとおりです。

* [IoT Hub エンドポイント][lnk-endpoints]: 各 IoT Hub でランタイムと管理の操作のために公開される、さまざまなエンドポイントについて説明します。
* [調整とクォータ][lnk-quotas]: IoT Hub サービスに適用されるクォータと、サービスを使用するときに想定される調整の動作について説明します。
* [Azure IoT device SDK とサービス SDK][lnk-sdks]: IoT Hub とやりとりするデバイスとサービス アプリの両方を開発する際に使用できるさまざまな言語の SDK を紹介します。
* [IoT Hub のツインおよびジョブ向けのクエリ言語][lnk-query]: IoT Hub からデバイス ツインおよびジョブに関する情報を取得する際に使用できる IoT Hub のクエリ言語について説明します。
* [IoT Hub の MQTT サポート][lnk-devguide-mqtt]: IoT Hub での MQTT プロトコルのサポートについて詳しく説明します。

## <a name="next-steps"></a>次のステップ

IoT Hub の ID レジストリの使用方法を理解できたら、次の IoT Hub 開発者ガイドのトピックも参考にしてください。

* [IoT Hub へのアクセスの制御][lnk-devguide-security]
* [デバイス ツインを使って状態と構成を同期する][lnk-devguide-device-twins]
* [デバイスでダイレクト メソッドを呼び出す][lnk-devguide-directmethods]
* [複数デバイスでのジョブをスケジュール設定する][lnk-devguide-jobs]

この記事で説明した概念を試す場合は、次の IoT Hub のチュートリアルをご利用ください。

* [Azure IoT Hub を使ってみる][lnk-getstarted-tutorial]

<!-- Links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-guidance-provisioning]: iot-hub-devguide-identity-registry.md#device-provisioning
[lnk-guidance-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-export]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md

