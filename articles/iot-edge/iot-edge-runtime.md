---
title: ランタイムでデバイスを管理する方法について - Azure IoT Edge | Microsoft Docs
description: デバイス上のモジュール、セキュリティ、通信、およびレポートを IoT Edge ランタイムで管理する方法について説明します
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, mqtt, devx-track-csharp
ms.openlocfilehash: 74cfe4ba3c92d8d96dd196ef6f612b9ed7c0da9d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103496254"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Azure IoT Edge ランタイムとそのアーキテクチャの概要

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge ランタイムは、デバイスを IoT Edge デバイスに変えるプログラムのコレクションです。 これらの IoT Edge ランタイム コンポーネントを使用することにより、IoT Edge デバイスは、エッジで実行するコードを受信し、結果を通信できます。

IoT Edge ランタイムは、IoT Edge デバイスで次の機能の実行を担当します。

* デバイスにワークロードをインストールし、更新する。

* デバイス上の Azure IoT Edge のセキュリティ標準を維持する。

* [IoT Edge モジュール](iot-edge-modules.md)の実行状態を絶えず確保する。

* モジュールの正常性をクラウドにレポートしてリモート監視を可能にする。

* ダウンストリーム デバイスと IoT Edge デバイス間の通信を管理する。

* IoT Edge デバイス上のモジュール間の通信を管理する。

* IoT Edge デバイスとクラウド間の通信を管理する。
<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
* IoT Edge デバイス間の通信を管理する。
::: moniker-end

![ランタイムによる分析情報とモジュールの正常性の IoT Hub への通信](./media/iot-edge-runtime/Pipeline.png)

IoT Edge ランタイムには、通信とモジュール管理の 2 つのカテゴリの役割があります。 これら 2 つの役割は、IoT Edge ランタイムの部品である 2 つのコンポーネントによって実行されます。 *IoT Edge エージェント* は、モジュールを展開し、監視しますが、*IoT Edge ハブ* は通信を担当します。

IoT Edge エージェントと IoT Edge ハブはどちらも、IoT Edge デバイスで実行される他のモジュールと同様のモジュールです。 これらは、*ランタイム モジュール* と呼ばれる場合もあります。

## <a name="iot-edge-agent"></a>IoT Edge エージェント

IoT Edge エージェントは、Azure IoT Edge ランタイムを構成する 2 つのモジュールの 1 つです。 このモジュールは、モジュールをインスタンス化し、モジュールの実行を継続し、IoT Hub にモジュールのステータスを報告します。 この構成データは、IoT Edge エージェント モジュール ツインのプロパティとして書き込まれます。

デバイスの起動時に、[IoT Edge セキュリティ デーモン](iot-edge-security-manager.md)が IoT Edge エージェントを開始します。 エージェントは IoT Hub からそのモジュール ツインを取得し、配置マニフェストを検査します。 配置マニフェストとは、開始する必要があるモジュールを宣言する JSON ファイルです。

配置マニフェスト内の各項目にはモジュールに関する特定の情報が含まれており、モジュールのライフ サイクルを制御するために IoT Edge エージェントによって使用されます。 IoT Edge エージェントによってモジュールの制御に使用されるすべてのプロパティの詳細については、「[IoT Edge エージェントと IoT Edge ハブのモジュール ツインのプロパティ](module-edgeagent-edgehub.md)」を参照してください。

IoT Edge エージェントは、ランタイム応答を IoT ハブに送信します。 考えられる応答の一覧を以下に示します。
  
* 200 - OK
* 400 - デプロイ構成が正しくない形式であるか、無効です。
* 417 - デバイスにデプロイ構成セットがありません。
* 412 - デプロイ構成にあるスキーマ バージョンが無効です。
* 406 - IoT Edge デバイスがオフラインであるか、状態レポートを送信していません。
* 500 - IoT Edge ランタイムでエラーが発生しました。

デプロイ マニフェストの作成の詳細については、「[IoT Edge にモジュールをデプロイしてルートを確立する方法について説明します。](module-composition.md)」を参照してください。

### <a name="security"></a>Security

IoT Edge エージェントは、IoT Edge デバイスのセキュリティ上、重要な役割を果たします。 たとえば、起動前のモジュール イメージの検証などの操作を実行します。

Azure IoT Edge セキュリティ フレームワークについて詳しくは、[IoT Edge セキュリティ マネージャー](iot-edge-security-manager.md)に関する記事をご覧ください。

## <a name="iot-edge-hub"></a>IoT Edge ハブ

IoT Edge ハブは、Azure IoT Edge ランタイムを構成するもう 1 つのモジュールです。 Edge ハブは、IoT Hub と同じプロトコル エンドポイントを公開することで IoT Hub のためのローカル プロキシとして動作します。 この整合性により、クライアントは、IoT Hub と同じように IoT Edge ランタイムに接続できます。

IoT Edge ハブは、ローカルで実行される完全バージョンの IoT Hub ではありません。 IoT Edge ハブを使うと、一部のタスクを自動的に IoT Hub に委任できます。 たとえば、IoT Edge ハブにより、デバイスが接続できるように、初回の接続で IoT Hub から認証情報が自動的にダウンロードされます。 初回の接続確立後、認証情報が IoT Edge ハブによってローカルにキャッシュされます。 そのデバイスからのその後の接続は、再度クラウドから認証情報をダウンロードする必要なく認可されます。

### <a name="cloud-communication"></a>クラウド通信

IoT Edge ソリューションが使用する帯域幅を減らすために、IoT Edge ハブは、クラウドに対して作成される実際の接続数を最適化します。 IoT Edge ハブは、モジュールやダウンストリーム デバイスからの論理接続を取得し、それらをクラウドへの 1 つの物理接続に統合します。 ソリューションの他の部分は、このプロセスの詳細を認識する必要がありません。 クライアントは、すべて、共通の接続を使って接続しているにもかかわらず、クラウドにそれぞれ独自に接続していると認識します。 IoT Edge ハブは、ダウンストリーム デバイスで使用されているプロトコルと関係なく、AMQP または MQTT プロトコルを使用して、クラウドとアップストリーム通信を行うことができます。 ただし、IoT Edge ハブでは、現在アップストリーム プロトコルとして AMQP を使用し、その多重化機能を使用して、論理接続を 1 つの物理接続に結合することのみがサポートされています。 AMQP は、既定のアップストリーム プロトコルです。

![IoT Edge ハブは物理デバイスと IoT Hub との間のゲートウェイです](./media/iot-edge-runtime/gateway-communication.png)

IoT Edge ハブでは、IoT Hub に接続されているかどうかを判断できます。 接続が切断された場合は、IoT Edge ハブがメッセージを保存するか、ツインがローカルで更新します。 接続が再確立されると、すべてのデータが同期されます。 この一時キャッシュに使用される場所は、IoT Edge ハブのモジュール ツインのプロパティによって規定されます。 キャッシュのサイズには上限がなく、デバイスにストレージ容量がある限り拡張されます。  詳細については、[オフライン機能](offline-capabilities.md)に関するページを参照してください。

<!-- <1.1> -->
::: moniker range="iotedge-2018-06"

### <a name="module-communication"></a>モジュール通信

IoT Edge ハブを使用することで、モジュール間の通信が容易になっています。 メッセージ ブローカーとして IoT Edge ハブを使用することで、モジュールの相互独立性を維持できます。 モジュールは、メッセージを受信する入力と、メッセージを書き出す出力を指定するだけです。 ソリューション開発者は、そのソリューションに固有の順序でデータを処理できるよう、これらの入力と出力を統合します。

![IoT Edge ハブを使用することで、モジュール間の通信が容易になっています](./media/iot-edge-runtime/module-endpoints.png)

データを IoT Edge ハブに送信するために、モジュールは、SendEventAsync メソッドを呼び出します。 最初の引数は、どの出力にメッセージを送信するかを指定します。 次の疑似コードは、**output1** にメッセージを送信します。

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

メッセージを受信するには、特定の入力で受け取ったメッセージを処理するコールバックを登録します。 次の疑似コードは、**input1** で受信したすべてのメッセージを処理する関数 messageProcessor を登録します。

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

ModuleClient クラスとその通信方法に関する詳細については、優先する SDK 言語 ([C#](/dotnet/api/microsoft.azure.devices.client.moduleclient)、[C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)、[Python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient)、[Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)、または [Node.js](/javascript/api/azure-iot-device/moduleclient)) の API リファレンスを参照してください。

IoT Edge ハブがモジュール間でメッセージを渡す方法を決定するルールを指定するのはソリューション開発者です。 ルーティング規則はクラウドで定義され、そのモジュール ツインの中で IoT Edge ハブにプッシュ ダウンされます。 IoT Hub ルートと同じ構文を使用して、Azure IoT Edge のモジュール間のルートが定義されます。 詳細については、[モジュールのデプロイと IoT Edge へのルートの確立の方法の学習](module-composition.md)に関する記事をご覧ください。

![モジュール間のルートは IoT Edge ハブを経由します](./media/iot-edge-runtime/module-endpoints-routing.png)
::: moniker-end

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

### <a name="local-communication"></a>ローカル通信

IoT Edge ハブにより、ローカル通信が容易になります。 これにより、メッセージを仲介して、デバイスからモジュール、モジュールからデバイス、デバイス間の通信を可能にし、デバイスとモジュールの相互に独立した状態を維持します。

>[!NOTE]
> MQTT ブローカー機能は、IoT Edge バージョン 1.2 でパブリック プレビュー段階にあります。 それは明示的に有効にする必要があります。

IoT Edge ハブでは、2 つのブローカー メカニズムがサポートされています。

1. [IoT Hub によってサポートされているメッセージ ルーティング機能](../iot-hub/iot-hub-devguide-messages-d2c.md)および、
2. [MQTT 標準 v 3.1.1](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html) を満たす汎用 MQTT ブローカー

#### <a name="using-routing"></a>ルーティングの使用

最初のブローカー メカニズムでは、IoT Hub と同じルーティング機能を利用して、デバイスまたはモジュール間でメッセージを渡す方法を指定します。 最初のデバイスまたはモジュールでは、メッセージを受信する入力と、メッセージを書き出す出力を指定します。 それにより、ソリューション開発者は、ソース (出力など) と宛先 (入力など) の間で、潜在的なフィルターを使用してメッセージをルーティングできます。

![モジュール間のルートは IoT Edge ハブを経由します](./media/iot-edge-runtime/module-endpoints-routing.png)

ルーティングは、AMQP または MQTT プロトコル経由で、Azure IoT Device SDK で構築されたデバイスまたはモジュールによって使用できます。 すべてのメッセージング IoT Hub プリミティブ (テレメトリ、ダイレクト メソッド、C2D、ツインなど) がサポートされていますが、ユーザー定義のトピックを介した通信はサポートされていません。

ルートの詳細については、「[IoT Edge にモジュールをデプロイしてルートを確立する方法について説明します。](module-composition.md)」を参照してください

#### <a name="using-the-mqtt-broker"></a>MQTT ブローカーの使用

2 つ目のブローカー メカニズムは、標準 MQTT ブローカーに基づいています。 MQTT は、リソースの制約付きデバイスで最適なパフォーマンスを確保する軽量のメッセージ転送プロトコルであり、一般的な発行およびサブスクライブ標準です。 デバイスまたはモジュールでは、トピックをサブスクライブして、他のデバイスまたはモジュールによって発行されたメッセージを受信します。 IoT Edge ハブでは、[MQTT バージョン 3.1.1 の仕様](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html)に従うその独自の MQTT ブローカーを実装します。

MQTT ブローカーにより、ルーティングと比較して、ローカル ブロードキャストとポイントツーポイント通信という 2 つの追加の通信パターンが可能になります。 ローカル ブロードキャストは、1 つのデバイスまたはモジュールが、他の複数のデバイスまたはモジュールをローカルで警告する必要がある場合に便利です。 ポイントツーポイント通信により、2 つの IoT Edge デバイスまたは 2 つの IoT デバイスが、クラウドへのラウンドトリップなしでローカルに通信できます。

![IoT Edge ハブによるローカルでの発行とサブスクライブ](./media/iot-edge-runtime/local-communnication-mqtt-broker.png)

MQTT ブローカーは、MQTT プロトコルまたは任意の汎用 MQTT クライアントを介して通信する Azure IoT Device SDK によって構築されたデバイスまたはモジュールで使用できます。 C2D を除き、すべてのメッセージング IoT Hub プリミティブ (テレメトリ、ダイレクト メソッド、ツインなど) がサポートされています。 IoT Hub プリミティブによって使用される IoT Hub の特別なトピックがサポートされており、ユーザー定義のトピックもサポートされています。
このトピックには、IoT Hub の特別なトピックやユーザー定義のトピックなどがあります。

ルーティング メカニズムと異なり、メッセージの順序付けはベストエフォートにすぎず、保証されていません。またメッセージのフィルター処理は、ブローカーでサポートされていません。 ただし、これらの機能がないことで、MQTT ブローカーはルーティングよりも高速になります。

MQTT ブローカーの詳細については、[IoT Edge による発行とサブスクライブ](how-to-publish-subscribe.md)に関するページを参照してください

#### <a name="comparison-between-brokering-mechanisms"></a>ブローカー メカニズムの比較

各ブローカー メカニズムで使用できる機能を次に示します。

|特徴  | ルーティング  | MQTT ブローカー  |
|---------|---------|---------|
|D2C テレメトリ    |     &#10004;    |         |
|ローカル テレメトリ     |     &#10004;    |    &#10004;     |
|DirectMethods     |    &#10004;     |    &#10004;     |
|ツイン     |    &#10004;     |    &#10004;     |
|デバイスの C2D     |   &#10004;      |         |
|順序     |    &#10004;     |         |
|フィルター処理     |     &#10004;    |         |
|ユーザー定義トピック     |         |    &#10004;     |
|デバイス対デバイス     |         |    &#10004;     |
|ローカル ブロードキャスト     |         |    &#10004;     |
|パフォーマンス     |         |    &#10004;     |

### <a name="connecting-to-the-iot-edge-hub"></a>IoT Edge ハブへの接続

IoT Edge ハブでは、MQTT プロトコルまたは AMQP プロトコルのいずれかを介した、デバイスまたはモジュール クライアントからの接続が受け入れられます。

>[!NOTE]
> IoT Edge ハブでは、MQTT または AMQP を使用して接続するクライアントがサポートされます。 HTTP を使用するクライアントはサポートされません。

クライアントが IoT Edge ハブに接続すると、次の処理が行われます。

1. TLS (トランスポート層セキュリティ) が使用されている場合 (推奨)、クライアントと IoT Edge ハブ間の暗号化された通信を確立するために TLS チャネルが構築されます。
2. クライアントから IoT Edge ハブに、自身を識別するために認証情報が送信されます。
3. IoT Edge ハブでは、認可ポリシーに基づいて接続が認可または拒否されます。

#### <a name="secure-connections-tls"></a>セキュリティで保護された接続 (TLS)

既定で、IoT Edge ハブではトランスポート層セキュリティ (TLS) でセキュリティ保護された接続 (サード パーティが暗号化を解除できない暗号化接続など) のみが受け入れられます。

クライアントがポート 8883 (MQTTS) または 5671 (AMQPS) で IoT Edge ハブに接続する場合、TLS チャネルを構築する必要があります。 TLS ハンドシェイク時に、IoT Edge ハブによって、クライアントで検証する必要がある証明書チェーンが送信されます。 証明書チェーンを検証するには、IoT Edge ハブのルート証明書が、信頼された証明書としてクライアントにインストールされている必要があります。 ルート証明書が信頼されていない場合、クライアントライブラリは、証明書検証エラーで IoT Edge ハブによって拒否されます。

デバイス クライアントにブローカーのこのルート証明書をインストールするために従う手順については、[透過的ゲートウェイ](how-to-create-transparent-gateway.md)と[ダウンストリーム デバイスの準備](how-to-connect-downstream-device.md#prepare-a-downstream-device)に関するドキュメントを参照してください。 モジュールでは、IoT Edge デーモン API を利用することで、IoT Edge ハブと同じルート証明書を使用できます。

#### <a name="authentication"></a>認証

IoT Edge ハブでは、IoT Hub ID を持つデバイスやモジュールからの接続のみを受け入れます。たとえば、IoT Hub に登録されていて、自身の ID を証明するために、IoT Hub でサポートされている次の 3 つのクライアント認証方法のいずれかを使用するデバイスやモジュールなどです。[対称キー認証](how-to-authenticate-downstream-device.md#symmetric-key-authentication)、[x.509 自己署名認証](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)、[x.509 CA 署名認証](how-to-authenticate-downstream-device.md#x509-ca-signed-authentication)。  これらの IoT Hub ID は、IoT Edge ハブでローカルに確認できるため、オフライン中でも接続を行うことができます。

メモ:

* 現在、IoT Edge モジュールでは対称キー認証のみがサポートされています。
* ローカルのユーザー名とパスワードのみを使用する MQTT クライアントは、IoT Edge ハブ MQTT ブローカーで受け入れられません。IoT Hub ID を使用する必要があります。

#### <a name="authorization"></a>承認

認証されたら、IoT Edge ハブではクライアント接続を認可する 2 つの方法があります。

* IoT Hub に定義されている信頼されたクライアントのセットにクライアントが属していることを確認することによって。 信頼されたクライアントのセットは、IoT Hub で親/子またはデバイス/モジュールのリレーションシップを設定することによって指定します。 IoT Edge でモジュールが作成されると、このモジュールとその IoT Edge デバイスの間にリレーションシップが自動的に確立されます。 これは、ルーティング ブローカー メカニズムによってサポートされている唯一の認可モデルです。

* 認可ポリシーを設定することによって。 この認可ポリシーは、IoT Edge ハブ上のリソースにアクセスできるすべての認可されたクライアント ID を一覧表示するドキュメントです。 これは IoT Edge ハブ MQTT ブローカーによって使用される主要認可モデルですが、親/子およびデバイス/モジュールのリレーションシップも IoT Hub トピックの MQTT ブローカーに認識させることができます。

### <a name="remote-configuration"></a>リモート構成

IoT Edge ハブはクラウドによって完全に制御されます。 [モジュール ツイン](iot-edge-modules.md#module-twins)を介して IoT Hub からその構成が取得されます。 次の情報が含まれます。

* ルート構成
* 承認ポリシー
* MQTT ブリッジ構成

さらに、[IoT Edge ハブの環境変数](https://github.com/Azure/iotedge/blob/master/doc/EnvironmentVariables.md)を設定することによって、いくつかの構成を行うことができます。
<!-- </1.2> -->
::: moniker-end

## <a name="runtime-quality-telemetry"></a>ランタイム品質テレメトリ

IoT Edge を使用すると、ホスト ランタイムおよびシステム モジュールから匿名テレメトリを収集し、製品の品質を向上させることができます。 この情報はランタイム品質テレメトリと呼ばれます。 収集されたテレメトリは、デバイスからクラウドへのメッセージとして IoT Edge エージェントから IoT Hub に定期的に送信されます。 これらのメッセージは、お客様の通常のテレメトリには表示されず、メッセージ クォータは使用されません。

IoT Edge エージェントとハブからは、デバイスのパフォーマンスを理解するために収集できるメトリックが生成されます。 これらのメトリックのサブセットは、ランタイム品質テレメトリの一部として IoT Edge Agent によって収集されます。 ランタイム品質テレメトリ用に収集されたメトリックには、タグ `ms_telemetry` のラベルが付けられます。 使用できるすべてのメトリックについては、「[組み込みメトリックへのアクセス](how-to-access-built-in-metrics.md)」を参照してください。

デバイス名やモジュール名など、個人または組織について特定可能な情報は、ランタイム品質のテレメトリの匿名性を確保するために、アップロード前に削除されます。

IoT Edge エージェントによって 1 時間ごとにテレメトリが収集され、24 時間ごとに 1 つのメッセージが IoT Hub に送信されます。

デバイスからのランタイム テレメトリの送信をオプトアウトする場合は、次の 2 つの方法があります。

* **edgeAgent** の `SendRuntimeQualityTelemetry` 環境変数を `false` に設定します。または、
* デプロイ時に Azure portal のオプションをオフにします。

## <a name="next-steps"></a>次のステップ

* [Azure IoT Edge モジュールについて](iot-edge-modules.md)
* [Learn how to deploy modules and establish routes in IoT Edge](module-composition.md) (IoT Edge にモジュールをデプロイしてルートを確立する方法)
* [IoT Edge を使用した発行とサブスクライブの方法を確認する](how-to-publish-subscribe.md)
* [IoT Edge ランタイム メトリックの概要](how-to-access-built-in-metrics.md)
