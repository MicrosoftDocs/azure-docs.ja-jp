---
title: "Azure IoT Hub のエンドポイントについて | Microsoft Docs"
description: "開発者ガイド - IoT Hub デバイス向けおよびサービス向けエンドポイントに関する参照情報。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 57ba52ae-19c6-43e4-bc6c-d8a5c2476e95
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: 9b7316a5bffbd689bdb26e9524129ceed06606d5
ms.openlocfilehash: a5753df2ff6874d9574e268953792cac9765cc54
ms.contentlocale: ja-jp
ms.lasthandoff: 09/08/2017

---
# <a name="reference---iot-hub-endpoints"></a>リファレンス - IoT Hub エンドポイント

## <a name="iot-hub-names"></a>IoT Hub 名

ポータルの **[概要]** ブレードで、エンドポイントをホストする IoT Hub の名前を検索できます。 既定では、IoT Hub の DNS 名は `{your iot hub name}.azure-devices.net` のようになります。

Azure DNS を使用して、IoT Hub のカスタム DNS 名を作成できます。 詳細については、「[Azure DNS を使用して Azure サービス用のカスタム ドメイン設定を提供する](../dns/dns-custom-domain.md#azure-iot)」をご覧ください。

## <a name="list-of-built-in-iot-hub-endpoints"></a>IoT Hub の組み込みエンドポイントの一覧

Azure IoT Hub はさまざまなアクターに機能を公開するマルチテナント サービスです。 次の図は、IoT Hub が公開しているさまざまなエンドポイントを示します。

![IoT Hub エンドポイント][img-endpoints]

次の一覧では、エンドポイントについて説明します。

* **リソースプロバイダー**。 IoT Hub リソースプロバイダーでは、[Azure Resource Manager][lnk-arm] インターフェイスが公開されています。 Azure サブスクリプションの所有者は、IoT Hub の作成と削除や IoT Hub プロパティの更新などを、このインターフェイスで行うことができます。 IoT Hub のプロパティでは、[ハブレベルのセキュリティ ポリシー][lnk-accesscontrol] (デバイスレベルのアクセス制御ではありません) と、Cloud-to-device (クラウドからデバイス) と Device-to-cloud (デバイスからクラウド) のメッセージング機能のオプションを管理します。 また、IoT Hub リソースプロバイダーにより、[デバイス ID をエクスポート][lnk-importexport]することもできます。
* **デバイス ID の管理**。 各 IoT Hub は、デバイス ID の管理 (作成、取得、更新、削除) を行うための、一連の HTTP REST エンドポイントを公開します。 [デバイス ID][lnk-device-identities]は、デバイスの認証とアクセス制御に使用されます。
* **デバイス ツインの管理**。 各 IoT Hub は、[デバイス ツイン][lnk-twins]のクエリと更新 (タグとプロパティの更新) を実行するサービス接続 HTTP REST エンドポイントを公開します。
* **ジョブの管理**。 各 IoT Hub は、[ジョブ][lnk-jobs]のクエリと管理を実行するサービス接続 HTTP REST エンドポイントを公開します。
* **デバイスのエンドポイント**。 IoT Hub では、ID レジストリ内のデバイスごとに、以下の一連のエンドポイントを公開しています。

  * *D2C メッセージの送信*。 デバイスは、このエンドポイントを使用して、[デバイスからクラウドへのメッセージを送信します][lnk-d2c]。
  * *C2D メッセージの受信*。 デバイスは、このエンドポイントを使用して、そのデバイスが宛先となっている [Cloud-to-device メッセージ][lnk-c2d]を受信します。
  * *ファイルのアップロードの開始*。 デバイスでは、[ファイルをアップロード][lnk-upload]するために、このエンドポイントを使用して、IoT Hub から Azure Storage の SAS URI を受け取ります。
  * *デバイス ツインのプロパティを取得して更新します*。 デバイスは、このエンドポイントを使用して、その[デバイス ツイン][lnk-twins]のプロパティにアクセスします。
  * *ダイレクト メソッド要求の受信*。 デバイスは、このエンドポイントを使用して、[ダイレクト メソッド][lnk-methods]の要求をリッスンします。

    これらのエンドポイントは、[MQTT v3.1.1][lnk-mqtt]、HTTP 1.1、および [AMQP 1.0][lnk-amqp] の各プロトコルを使用して公開されます。 AMQP は、ポート 443 で [WebSockets][lnk-websockets] 経由で使用することもできます。

* **サービス エンドポイント**。 各 IoT Hub では、ソリューション バックエンドに対して一連のエンドポイントを公開し、デバイスと通信を行います。 唯一の例外は、これらのエンドポイントが [AMQP][lnk-amqp] プロトコルを使用して公開のみが行われる場合です。 メソッド呼び出しのエンドポイントは、HTTP プロトコルを介して公開されます。
  
  * *D2C メッセージの受信*。 このエンドポイントには、[Azure Event Hubs][lnk-event-hubs] との互換性があります。 バックエンド サービスはこのエンドポイントを使用して、デバイスによって送信された[デバイスからクラウドへのメッセージ][lnk-d2c]を読み取ることができます。 この組み込みのエンドポイントに加え、IoT Hub のカスタム エンドポイントを作成することもできます。
  * *C2D メッセージの送信と、配信の確認メッセージの受信*。 これらのエンドポイントにより、ソリューション バックエンドは、信頼性の高い [Cloud-to-device メッセージ][lnk-c2d]を送信し、対応する配信または有効期限の確認メッセージを受信できます。
  * *ファイル通知の受信*。 このメッセージング エンドポイントにより、デバイスがファイルを正常にアップロードしたときに通知を受信できます。 
  * *ダイレクト メソッドの呼び出し*。 このエンドポイントにより、バックエンド サービスがデバイスで[ダイレクト メソッド][lnk-methods]を呼び出すことができます。
  * *操作監視イベントの受信*。 このエンドポイントでは、IoT hub から出力するように構成されている場合は、操作監視イベントを受信できます。 詳細については、「[IoT Hub 操作の監視][lnk-operations-mon]」を参照してください。

[Azure IoT SDK][lnk-sdks] に関する記事で、これらのエンドポイントにアクセスするさまざまな方法が説明されています。

IoT Hub エンドポイントはすべて [TLS][lnk-tls] プロトコルを使用しており、暗号化/セキュリティ保護されていない経路からエンドポイントが公開されることはありません。

## <a name="custom-endpoints"></a>カスタム エンドポイント

サブスクリプションの既存の Azure サービスを、メッセージをルーティングするためのエンドポイントとして機能する IoT Hub にリンクできます。 これらのエンドポイントはサービス エンドポイントとして機能し、メッセージ ルートのシンクとして使用されます。 デバイスは、追加のエンドポイントに直接書き込むことはできません。 メッセージ ルートの詳細については、開発者ガイドの [IoT Hub でのメッセージの送受信][lnk-devguide-messaging]に関する説明を参照してください。

現在、IoT Hub は、追加のエンドポイントとして、次の Azure サービスをサポートします。

* Event Hubs
* Service Bus キュー
* Service Bus トピック

IoT Hub でメッセージのルーティングを機能させるには、これらのサービス エンドポイントへの書き込みアクセス許可が必要です。 Azure Portal を使用してエンドポイントを構成する場合、必要なアクセス許可は自動的に追加されます。 必要なスループットをサポートするようにサービスを確実に構成してください。 IoT ソリューションを初めて構成する場合は、追加したエンドポイントを監視し、実際の負荷の調整を行う必要があります。

メッセージが、同じエンドポイントを指している複数のルートと一致する場合、IoT Hub はそのエンドポイントにメッセージを 1 回だけ送信します。 そのため、Service Bus キューまたはトピックで重複除去を構成する必要はありません。 パーティション分割されたキューでは、パーティションのアフィニティによってメッセージの順序が保証されます。

> [!NOTE]
> IoT Hub エンドポイントとして使用される Service Bus のキューおよびトピックでは、**セッション**も**重複データ検出**も有効にしないでください。 これらのオプションのいずれかが有効になっている場合、エンドポイントは Azure Portal に**到達不能**として表示されます。

追加できるエンドポイントの数の制限については、「[クォータと調整][lnk-devguide-quotas]」をご覧ください。

## <a name="field-gateways"></a>フィールド ゲートウェイ

IoT ソリューションでは、*フィールド ゲートウェイ*はデバイスと IoT Hub エンドポイントの間に配置され、 通常はデバイスの近くにあります。 デバイスは、そのデバイスでサポートされているプロトコルを使用してフィールド ゲートウェイと直接通信します。 フィールド ゲートウェイは、IoT Hub でサポートされているプロトコルを使用して IoT Hub エンドポイントに接続します。 フィールド ゲートウェイは、専用のハードウェア デバイス、またはカスタム ゲートウェイ ソフトウェアを実行する低電力コンピューターの場合があります。

[Azure IoT Edge][lnk-iot-edge] を使用してフィールド ゲートウェイを実装できます。 IoT Edge には、同じ IoT Hub 接続上で複数のデバイスからの通信を多重化する機能などが用意されています。

## <a name="next-steps"></a>次のステップ

この IoT Hub 開発者ガイド内の他の参照トピックは次のとおりです。

* [デバイス ツイン、ジョブ、およびメッセージ ルーティングの IoT Hub クエリ言語][lnk-devguide-query]
* [クォータと調整][lnk-devguide-quotas]
* [IoT Hub の MQTT サポート][lnk-devguide-mqtt]

[lnk-iot-edge]: https://github.com/Azure/iot-edge

[img-endpoints]: ./media/iot-hub-devguide-endpoints/endpoints.png
[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../azure-resource-manager/resource-group-overview.md
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/

[lnk-tls]: https://tools.ietf.org/html/rfc5246


[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-accesscontrol]: iot-hub-devguide-security.md#access-control-and-permissions
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-device-identities]: iot-hub-devguide-identity-registry.md
[lnk-upload]: iot-hub-devguide-file-upload.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md

[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[lnk-operations-mon]: iot-hub-operations-monitoring.md

