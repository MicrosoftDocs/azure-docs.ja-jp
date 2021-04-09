---
title: Azure IoT Hub のエンドポイントについて | Microsoft Docs
description: 開発者ガイド - IoT Hub デバイス向けおよびサービス向けエンドポイントに関する参照情報。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: System Architecture'
ms.openlocfilehash: a58e141c6232db08b125b265e3d4ad74c784ba24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92152176"
---
# <a name="reference---iot-hub-endpoints"></a>リファレンス - IoT Hub エンドポイント

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>IoT Hub 名

ポータルのハブの **[概要]** ページで、エンドポイントをホストしている IoT ハブのホスト名を検索できます。 既定では、IoT Hub の DNS 名は `{your iot hub name}.azure-devices.net` のようになります。

## <a name="list-of-built-in-iot-hub-endpoints"></a>IoT Hub の組み込みエンドポイントの一覧

Azure IoT Hub はさまざまなアクターに機能を公開するマルチテナント サービスです。 次の図は、IoT Hub が公開しているさまざまなエンドポイントを示します。

![IoT Hub エンドポイント](./media/iot-hub-devguide-endpoints/endpoints.png)

次の一覧では、エンドポイントについて説明します。

* **リソースプロバイダー**。 IoT Hub リソース プロバイダーでは、[Azure Resource Manager](../azure-resource-manager/management/overview.md) インターフェイスが公開されています。 Azure サブスクリプションの所有者は、IoT Hub の作成と削除や IoT Hub プロパティの更新などを、このインターフェイスで行うことができます。 IoT Hub のプロパティでは、[ハブレベルのセキュリティ ポリシー](iot-hub-devguide-security.md#access-control-and-permissions) (デバイスレベルのアクセス制御ではありません) と、Cloud-to-device (クラウドからデバイス) と Device-to-cloud (デバイスからクラウド) のメッセージング機能のオプションを管理します。 また、IoT Hub リソースプロバイダーにより、[デバイス ID をエクスポート](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)することもできます。

* **デバイス ID の管理**。 各 IoT Hub は、デバイス ID の管理 (作成、取得、更新、削除) を行うための、一連の HTTPS REST エンドポイントを公開します。 [デバイス ID](iot-hub-devguide-identity-registry.md)は、デバイスの認証とアクセス制御に使用されます。

* **デバイス ツインの管理**。 各 IoT Hub は、[デバイス ツイン](iot-hub-devguide-device-twins.md)のクエリと更新 (タグとプロパティの更新) を実行するサービス接続 HTTPS REST エンドポイントを公開します。 

* **ジョブの管理**。 各 IoT Hub は、[ジョブ](iot-hub-devguide-jobs.md)のクエリと管理を実行するサービス接続 HTTPS REST エンドポイントを公開します。

* **デバイスのエンドポイント**。 IoT Hub では、ID レジストリ内のデバイスごとに一連のエンドポイントを公開しています。 明記されている場合を除き、これらのエンドポイントは、[MQTT v3.1.1](https://mqtt.org/)、HTTPS 1.1、および [AMQP 1.0](https://www.amqp.org/) の各プロトコルを使用して公開されます。 AMQP および MQTT は、ポート 443 で [WebSockets](https://tools.ietf.org/html/rfc6455) 経由で使用することもできます。

  * *D2C メッセージの送信*。 デバイスは、このエンドポイントを使用して、[デバイスからクラウドへのメッセージを送信します](iot-hub-devguide-messages-d2c.md)。

  * *C2D メッセージの受信*。 デバイスは、このエンドポイントを使用して、そのデバイスが宛先となっている [Cloud-to-device メッセージ](iot-hub-devguide-messages-c2d.md)を受信します。

  * *ファイルのアップロードの開始*。 デバイスでは、[ファイルをアップロード](iot-hub-devguide-file-upload.md)するために、このエンドポイントを使用して、IoT Hub から Azure Storage の SAS URI を受け取ります。

  * *デバイス ツインのプロパティを取得して更新します*。 デバイスは、このエンドポイントを使用して、その[デバイス ツイン](iot-hub-devguide-device-twins.md)のプロパティにアクセスします。 HTTPS はサポートされていません。

  * *ダイレクト メソッド要求の受信*。 デバイスは、このエンドポイントを使用して、[ダイレクト メソッド](iot-hub-devguide-direct-methods.md)の要求をリッスンします。 HTTPS はサポートされていません。

  [!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

* **サービス エンドポイント**。 各 IoT Hub では、ソリューション バックエンドに対して一連のエンドポイントを公開し、デバイスと通信を行います。 唯一の例外は、これらのエンドポイントが、[AMQP](https://www.amqp.org/) および WebSockets プロトコル経由の AMQP を使用して、公開されるのみの場合です。 ダイレクト メソッド呼び出しのエンドポイントは、HTTPS プロトコル経由で公開されます。
  
  * *D2C メッセージの受信*。 このエンドポイントには、[Azure Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/)との互換性があります。 バックエンド サービスはこのエンドポイントを使用して、デバイスによって送信された[デバイスからクラウドへのメッセージ](iot-hub-devguide-messages-d2c.md)を読み取ることができます。 この組み込みのエンドポイントに加え、IoT Hub のカスタム エンドポイントを作成することもできます。
  
  * *C2D メッセージの送信と、配信の確認メッセージの受信*。 これらのエンドポイントにより、ソリューション バックエンドは、信頼性の高い [Cloud-to-device メッセージ](iot-hub-devguide-messages-c2d.md)を送信し、対応する配信または有効期限の確認メッセージを受信できます。
  
  * *ファイル通知の受信*。 このメッセージング エンドポイントにより、デバイスがファイルを正常にアップロードしたときに通知を受信できます。 
  
  * *ダイレクト メソッドの呼び出し*。 このエンドポイントにより、バックエンド サービスがデバイスで[ダイレクト メソッド](iot-hub-devguide-direct-methods.md)を呼び出すことができます。
  
  * *操作監視イベントの受信*。 このエンドポイントでは、IoT hub から出力するように構成されている場合は、操作監視イベントを受信できます。 詳細については、「[IoT Hub 操作の監視](iot-hub-operations-monitoring.md)」を参照してください。

[Azure IoT SDK](iot-hub-devguide-sdks.md) に関する記事で、これらのエンドポイントにアクセスするさまざまな方法が説明されています。

IoT Hub エンドポイントはすべて [TLS](https://tools.ietf.org/html/rfc5246)プロトコルを使用しており、暗号化/セキュリティ保護されていない経路からエンドポイントが公開されることはありません。

## <a name="custom-endpoints"></a>カスタム エンドポイント

サブスクリプションの既存の Azure サービスを、メッセージをルーティングするためのエンドポイントとして機能する IoT Hub にリンクできます。 これらのエンドポイントはサービス エンドポイントとして機能し、メッセージ ルートのシンクとして使用されます。 デバイスは、追加のエンドポイントに直接書き込むことはできません。 [メッセージ ルーティング](../iot-hub/iot-hub-devguide-messages-d2c.md)について確認します。

現在、IoT Hub は、追加のエンドポイントとして、次の Azure サービスをサポートします。

* Azure Storage コンテナー
* Event Hubs
* Service Bus キュー
* Service Bus トピック

追加できるエンドポイントの数の制限については、「[クォータと調整](iot-hub-devguide-quotas-throttling.md)」をご覧ください。

## <a name="endpoint-health"></a>エンドポイントの正常性

[!INCLUDE [iot-hub-endpoint-health](../../includes/iot-hub-include-endpoint-health.md)]

## <a name="field-gateways"></a>フィールド ゲートウェイ

IoT ソリューションでは、*フィールド ゲートウェイ* はデバイスと IoT Hub エンドポイントの間に配置され、 通常はデバイスの近くにあります。 デバイスは、そのデバイスでサポートされているプロトコルを使用してフィールド ゲートウェイと直接通信します。 フィールド ゲートウェイは、IoT Hub でサポートされているプロトコルを使用して IoT Hub エンドポイントに接続します。 フィールド ゲートウェイは、専用のハードウェア デバイス、またはカスタム ゲートウェイ ソフトウェアを実行する低電力コンピューターの場合があります。

[Azure IoT Edge](../iot-edge/index.yml) を使用してフィールド ゲートウェイを実装できます。 IoT Edge には、同じ IoT Hub 接続上で複数のデバイスからの通信を多重化する機能などが用意されています。

## <a name="next-steps"></a>次のステップ

この IoT Hub 開発者ガイド内の他の参照トピックは次のとおりです。

* [デバイス ツイン、ジョブ、およびメッセージ ルーティングの IoT Hub クエリ言語](iot-hub-devguide-query-language.md)
* [クォータとスロットル](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub の MQTT サポート](iot-hub-mqtt-support.md)
* [IoT Hub IP アドレスについて](iot-hub-understand-ip-address.md)