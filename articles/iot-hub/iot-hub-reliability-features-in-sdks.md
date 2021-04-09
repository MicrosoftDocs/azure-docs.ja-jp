---
title: デバイス SDK を使用して IoT Hub 接続と信頼できるメッセージングを管理する
description: Azure IoT Hub device SDK を利用するとき、デバイスの接続とメッセージ処理を改善する方法について説明します。
services: iot-hub
author: robinsh
ms.author: robinsh
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
ms.custom:
- amqp
- mqtt
- devx-track-csharp
ms.openlocfilehash: efd16f0e8dd6ba952b647fbfbd6f35fc05ffd4be
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102615841"
---
# <a name="manage-connectivity-and-reliable-messaging-by-using-azure-iot-hub-device-sdks"></a>Azure IoT Hub device SDK を使用して、接続と信頼できるメッセージングを管理する

この記事では、回復性の高いデバイス アプリケーションの設計に役立つ概要ガイダンスを提供します。 Azure IoT device SDK の接続機能と信頼性の高いメッセージング機能を利用する方法を示します。 このガイドの目的は、次のシナリオを管理することです。

* 切断されたネットワーク接続を修正する

* 異なるネットワーク接続間を切り替える

* サービスの一時的な接続エラーのために再接続する

実装の詳細は、言語によって異なる場合があります。 詳しくは、API のドキュメントまたは特定の SDK をご覧ください。

* [C/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python SDK](https://github.com/Azure/azure-iot-sdk-python) (信頼性はまだ実装されていません)

## <a name="designing-for-resiliency"></a>回復性の設計

IoT デバイスでは、断続的で不安定なネットワーク接続に依存することがよくあります (GSM や衛星など)。 サービスの可用性やインフラストラクチャ レベルの断続的な障害または一時的な障害のため、デバイスがクラウド ベースのサービスとやり取りしているときに、エラーが発生することがあります。 デバイス上で実行されるアプリケーションでは、接続、再接続、メッセージの送受信の再試行ロジックのメカニズムを管理する必要があります。 また、再試行戦略の要件は、デバイスの IoT シナリオ、コンテキスト、機能に大きく依存します。

Azure IoT Hub device SDK の目的は、接続と、cloud-to-device および device-to-cloud からの通信を、簡素化することです。 これらの SDK では、Azure IoT Hub に接続する堅牢な方法と、メッセージを送受信するための包括的なオプション セットが提供されています。 開発者は、既存の実装を変更し、特定のシナリオに合わせて再試行戦略をよりよくカスタマイズすることもできます。

以降のセクションでは、接続と信頼できるメッセージ処理をサポートする SDK 機能について取り上げます。

## <a name="connection-and-retry"></a>接続と再試行

このセクションでは、接続を管理するときに使用できる再接続と再試行のパターンの概要を示します。 デバイス アプリケーションでさまざまな再試行ポリシーを使用するための実装のガイダンスについて詳しく説明し、device SDK の関連する API の一覧を示します。

### <a name="error-patterns"></a>エラー パターン

接続エラーはさまざまなレベルで発生します。

* ネットワークのエラー: ソケットの切断や名前解決エラー

* HTTP、AMQP、MQTT トランスポートのプロトコルレベル エラー: リンクのデタッチやセッションの期限切れ

* ローカルな誤りに起因するアプリケーションレベル エラー: 無効な資格情報やサービスの動作 (クォータの超過やスロットリングなど)

device SDK は 3 つのレベルすべてでエラーを検出します。 OS 関連のエラーとハードウェア エラーは、デバイス SDK によって検出され、処理されます。 SDK の設計は、Azure アーキテクチャ センターの[一時的な障害の処理に関するガイダンス](/azure/architecture/best-practices/transient-faults#general-guidelines)に基づいています。

### <a name="retry-patterns"></a>再試行パターン

以下の手順では、接続エラーが検出されたときの再試行処理について説明します。

1. SDK がネットワーク、プロトコル、またはアプリケーションでエラーと関連エラーを検出します。

2. SDK はエラー フィルターを使用してエラーの種類を特定し、再試行が必要かどうかを判断します。

3. SDK が **回復不能なエラー** を識別すると、接続、送信、受信などの操作は停止されます。 SDK は、ユーザーに通知します。 回復不能なエラーの例としては、認証エラーや不正エンドポイント エラーなどがあります。

4. SDK は、**回復可能なエラー** を識別すると、ユーザーが指定した再試行ポリシーに従って、定義されているタイムアウト時間が経過するまで、再試行します。  SDK では、 **[指数関数的後退とゆらぎ]** の再試行ポリシーが既定で使用されることに注意してください。
5. 定義されているタイムアウト時間が経過すると、SDK は接続や送信の再試行を停止します。 ユーザーに通知します。

6. SDK によって、ユーザーはコールバックをアタッチし、接続ステータス変更を受信できます。

SDK では、3 つの再試行ポリシーが提供されています。

* **指数関数的後退とゆらぎ**:この既定の再試行ポリシーでは、最初は積極的に、その後は最大遅延に達するまで時間経過と共に頻度を減らしながら、再試行が行われます。 このデザインは、[Azure アーキテクチャ センターの再試行に関するガイダンス](/azure/architecture/best-practices/retry-service-specific)に基づいています。 

* **カスタム再試行**:SDK の一部の言語では、シナリオに適したカスタム再試行ポリシーを設計して、RetryPolicy に挿入することができます。 C SDK ではカスタム再試行を利用できず、Python SDK でも現在サポートされていません。 Python SDK は必要に応じて再接続します。

* **再試行なし**:再試行ポリシーを "再試行なし" に設定できます。再試行ロジックは無効になります。 接続が確立されていれば、SDK は一回の接続と一回のメッセージ送信を試行します。 このポリシーは通常、帯域幅またはコストが重要なシナリオで使用されます。 このオプションを選択すると、送信できなかったメッセージは失われ、回復できません。

### <a name="retry-policy-apis"></a>再試行ポリシー API

   | SDK | SetRetryPolicy メソッド | ポリシー実装 | 実装ガイダンス |
   |-----|----------------------|--|--|
   |  C/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **既定**:[IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**カスタム:** 利用可能な [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies) を利用<BR>**再試行なし:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [C/iOS の実装](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](/java/api/com.microsoft.azure.sdk.iot.device.deviceclientconfig.setretrypolicy)        | **既定**:[ExponentialBackoffWithJitter class](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**カスタム:** [RetryPolicy インターフェイス](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)を実装<BR>**再試行なし:** [NoRetry クラス](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Java 実装](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy) | **既定**:[ExponentialBackoff クラス](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff)<BR>**カスタム:** [IRetryPolicy インターフェイス](/dotnet/api/microsoft.azure.devices.client.iretrypolicy)を実装<BR>**再試行なし:** [NoRetry クラス](/dotnet/api/microsoft.azure.devices.client.noretry) | [C# 実装](https://github.com/Azure/azure-iot-sdk-csharp) | |
   | Node| [setRetryPolicy](/javascript/api/azure-iot-device/client) | **既定**:[ExponentialBackoffWithJitter class](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java) | [ノード実装](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   | Python| 現在、サポートされていません | 現在、サポートされていません | 現在、サポートされていません |

以下は、このフローを示すコード サンプルです。

#### <a name="net-implementation-guidance"></a>.NET 実装ガイダンス

次のコード サンプルでは、既定の再試行ポリシーを定義し、設定する方法を示しています。

   ```csharp
   // define/set default retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

CPU の使用率が高くなるのを避けるため、コードがすぐに失敗する場合、再試行は制限されます。 たとえば、ターゲットへのネットワークやルートがない場合などです。 次の再試行を実行するまでの最小時間は、1 秒です。

サービスが調整エラーで応答する場合は、再試行ポリシーが違っており、パブリック API からは変更できません。

   ```csharp
   // throttled retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), 
     TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5)); SetRetryPolicy(retryPolicy);
   ```

再試行メカニズムは `DefaultOperationTimeoutInMilliseconds` 後に停止しますが、これは現在、4 分に設定されています。

#### <a name="other-languages-implementation-guidance"></a>他の言語の実装ガイダンス

他の言語でのコード サンプルについては、次の実装ドキュメントを確認してください。 リポジトリには、再試行ポリシー API の使用方法を示すサンプルが含まれています。

* [C/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python SDK](https://github.com/Azure/azure-iot-sdk-python) (信頼性はまだ実装されていません)

## <a name="next-steps"></a>次のステップ

* [デバイス SDK とサービス SDK の使用](./iot-hub-devguide-sdks.md)

* [C 用 Azure IoT device SDK の使用](./iot-hub-device-sdk-c-intro.md)

* [制約のあるデバイスの場合の開発](./iot-hub-devguide-develop-for-constrained-devices.md)

* [モバイル デバイスの場合の開発](./iot-hub-how-to-develop-for-mobile-devices.md)

* [デバイスの切断のトラブルシューティング](iot-hub-troubleshoot-connectivity.md)