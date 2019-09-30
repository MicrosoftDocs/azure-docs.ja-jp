---
title: ランタイムでデバイスを管理する方法について - Azure IoT Edge | Microsoft Docs
description: デバイス上のモジュール、セキュリティ、通信、およびレポートを Azure IoT Edge ランタイムで管理する方法について説明します
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/06/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 677ff7ffab22eebdace67151d703ba83c2146602
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998604"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Azure IoT Edge ランタイムとそのアーキテクチャの概要

IoT Edge ランタイムは、デバイスを IoT Edge デバイスに変えるプログラムのコレクションです。 これらの IoT Edge ランタイム コンポーネントを使用することにより、IoT Edge デバイスは、エッジで実行するコードを受信し、結果を通信できます。 

IoT Edge ランタイムは、IoT Edge デバイスで次の機能を実行します。

* デバイスにワークロードをインストールし、更新する。
* デバイス上の Azure IoT Edge のセキュリティ標準を維持する。
* [IoT Edge モジュール](iot-edge-modules.md)の実行状態を絶えず確保する。
* モジュールの正常性をクラウドにレポートしてリモート監視を可能にする。
* ダウンストリームのリーフ デバイスと IoT Edge デバイス間の通信を円滑化する。
* IoT Edge デバイス上のモジュール間の通信を円滑化する。
* IoT Edge デバイスとクラウド間の通信を円滑化する。

![ランタイムによる分析情報とモジュールの正常性の IoT Hub への通信](./media/iot-edge-runtime/Pipeline.png)

IoT Edge ランタイムには、通信とモジュール管理の 2 つのカテゴリの役割があります。 これら 2 つの役割は、IoT Edge ランタイムの部品である 2 つのコンポーネントによって実行されます。 *IoT Edge ハブ*は通信を担当し、*IoT Edge エージェント*は、モジュールを展開し、監視します。 

IoT Edge ハブと IoT Edge エージェントはどちらも、IoT Edge デバイスで実行される他のモジュールと同様のモジュールです。 

## <a name="iot-edge-hub"></a>IoT Edge ハブ

IoT Edge ハブは、Azure IoT Edge ランタイムを構成する 2 つのモジュールの 1 つです。 Edge ハブは、IoT Hub と同じプロトコル エンドポイントを公開することで IoT Hub のためのローカル プロキシとして動作します。 この整合性により、クライアント (デバイスまたはモジュール) は、IoT Hub と同じように IoT Edge ランタイムに接続できます。 

>[!NOTE]
> IoT Edge ハブでは、MQTT または AMQP を使用して接続するクライアントがサポートされます。 HTTP を使用するクライアントはサポートされません。 

IoT Edge ハブは、ローカルで実行される完全バージョンの IoT Hub ではありません。 IoT Edge ハブは、いくつかの機能を IoT Hub を自動的に委任します。 たとえば、IoT Edge ハブは、デバイスが初めて接続を試みたときに認証要求を IoT Hub に送信します。 初回の接続確立後、セキュリティ情報は IoT Edge ハブによってローカルにキャッシュされます。 そのデバイスからのその後の接続は、クラウドへの認証なしに許可されます。 

IoT Edge ソリューションが使用する帯域幅を減らすために、IoT Edge ハブは、クラウドに対して作成される実際の接続数を最適化します。 IoT Edge ハブは、モジュールやリーフ デバイスなどのクライアントからの論理接続を取得し、それらをクラウドへの 1 つの物理接続に統合します。 ソリューションの他の部分は、このプロセスの詳細を認識する必要がありません。 クライアントは、すべて、共通の接続を使って接続しているにもかかわらず、クラウドにそれぞれ独自に接続していると認識します。 

![IoT Edge ハブは物理デバイスと IoT Hub との間のゲートウェイです](./media/iot-edge-runtime/Gateway.png)

IoT Edge ハブでは、IoT Hub に接続されているかどうかを判断できます。 接続が切断された場合は、IoT Edge ハブがメッセージを保存するか、ツインがローカルで更新します。 接続が再確立されると、すべてのデータが同期されます。 この一時キャッシュに使用される場所は、IoT Edge ハブのモジュール ツインのプロパティによって規定されます。 キャッシュのサイズには上限がなく、デバイスにストレージ容量がある限り拡張されます。 詳細については、[オフライン機能](offline-capabilities.md)に関するページを参照してください。

### <a name="module-communication"></a>モジュール通信

IoT Edge ハブを使用することで、モジュール間の通信が容易になっています。 メッセージ ブローカーとして IoT Edge ハブを使用することで、モジュールの相互独立性を維持できます。 モジュールは、メッセージを受信する入力と、メッセージを書き出す出力を指定するだけです。 ソリューション開発者は、そのソリューションに固有の順序でデータを処理できるよう、これらの入力と出力を統合します。 

![IoT Edge ハブを使用することで、モジュール間の通信が容易になっています](./media/iot-edge-runtime/module-endpoints.png)

データを IoT Edge ハブに送信するために、モジュールは、SendEventAsync メソッドを呼び出します。 最初の引数は、どの出力にメッセージを送信するかを指定します。 次の疑似コードは、**output1** にメッセージを送信します。

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync(“output1”, message); 
   ```

メッセージを受信するには、特定の入力で受け取ったメッセージを処理するコールバックを登録します。 次の疑似コードは、**input1** で受信したすべてのメッセージを処理する関数 messageProcessor を登録します。

   ```csharp
   await client.SetInputMessageHandlerAsync(“input1”, messageProcessor, userContext);
   ```

ModuleClient クラスとその通信方法に関する詳細については、優先する SDK 言語 ([C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)、[C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)、[Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python)、[Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)、または [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)) の API リファレンスを参照してください。

IoT Edge ハブがモジュール間でメッセージを渡す方法を決定するルールを指定するのはソリューション開発者です。 ルーティング規則はクラウドで定義され、そのデバイス ツインの中で IoT Edge ハブにプッシュ ダウンされます。 IoT Hub ルートと同じ構文を使用して、Azure IoT Edge のモジュール間のルートが定義されます。 詳細については、[モジュールのデプロイと IoT Edge へのルートの確立の方法の学習](module-composition.md)に関する記事をご覧ください。   

![モジュール間のルートは IoT Edge ハブを経由します](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>IoT Edge エージェント

IoT Edge エージェントは、Azure IoT Edge ランタイムを構成するもう 1 つのモジュールです。 このモジュールは、モジュールをインスタンス化し、モジュールの実行を継続し、IoT Hub にモジュールのステータスを報告します。 IoT Edge エージェントは、他のモジュールと同様に、そのモジュール ツインを使用してこの構成データを格納します。 

デバイスの起動時に、[IoT Edge セキュリティ デーモン](iot-edge-security-manager.md)が IoT Edge エージェントを開始します。 エージェントは IoT Hub からそのモジュール ツインを取得し、配置マニフェストを検査します。 配置マニフェストとは、開始する必要があるモジュールを宣言する JSON ファイルです。 

配置マニフェスト内の各項目にはモジュールに関する特定の情報が含まれており、モジュールのライフ サイクルを制御するために IoT Edge エージェントによって使用されます。 重要なプロパティを次に示します。 

* **settings.image** – IoT Edge エージェントがモジュールを起動するために使用するコンテナー イメージ。 イメージがパスワードで保護されている場合、IoT Edge エージェントは、コンテナー レジストリの資格情報を使用して構成されている必要があります。 コンテナー レジストリの資格情報は、配置マニフェストを使用してリモートで構成するか、IoT Edge のプログラム フォルダー内の `config.yaml` ファイルを更新することで、IoT Edge デバイス自体で構成できます。
* **settings.createOptions** – モジュールのコンテナーを起動したときに、Moby コンテナー デーモンに直接渡される文字列。 このプロパティでオプションを追加することにより、ポート転送またはモジュール コンテナーへのボリュームのマウントなどの詳細な構成を設定できます。  
* **status** – IoT Edge エージェントがモジュールを設定する状態。 IoT Edge によってデバイス上のすべてのモジュールを直ちに起動する必要があるユーザーが大半のため、この値は通常、*実行中*に設定されます。 ただし、モジュールの初期状態を "停止済み" に指定しておき、IoT Edge エージェントにモジュールを起動するよう指示するタイミングまで待機することもできます。 IoT Edge エージェントは、各モジュールの状態を、報告されるプロパティに格納してクラウドに報告します。 期待されるプロパティと報告されたプロパティの間に差がある場合は、デバイスの動作が不適切であることを示しています。 次の状態がサポートされています。
   * ダウンロード中
   * 実行中
   * 異常
   * 失敗
   * 停止済み
* **restartPolicy** – IoT Edge エージェントがモジュールを再起動する方法。 指定できる値は、次のとおりです。
   * `never` – IoT Edge エージェントがモジュールを再起動することはありません。
   * `on-failure` - モジュールがクラッシュした場合に IoT Edge エージェントがモジュールを再起動します。 モジュールがクリーンにシャット ダウンした場合、IoT Edge エージェントはモジュールを再起動しません。
   * `on-unhealthy` - モジュールがクラッシュしたか、異常と判断された場合は、IoT Edge エージェントがモジュールを再起動します。
   * `always` - モジュールがクラッシュした場合、異常と判断された場合、または方法を問わずシャットダウンされた場合、IoT Edge エージェントがモジュールを再起動します。 

IoT Edge エージェントは、ランタイム応答を IoT ハブに送信します。 考えられる応答の一覧を以下に示します。
  * 200 - OK
  * 400 - デプロイ構成が正しくない形式であるか、無効です。
  * 417 - デバイスにデプロイ構成セットがありません。
  * 412 - デプロイ構成にあるスキーマ バージョンが無効です。
  * 406 - IoT Edge デバイスがオフラインであるか、状態レポートを送信していません。
  * 500 - IoT Edge ランタイムでエラーが発生しました。

詳細については、[モジュールのデプロイと IoT Edge へのルートの確立の方法の学習](module-composition.md)に関する記事をご覧ください。   

### <a name="security"></a>セキュリティ

IoT Edge エージェントは、IoT Edge デバイスのセキュリティ上、重要な役割を果たします。 たとえば、起動前のモジュール イメージの検証などの操作を実行します。 

Azure IoT Edge セキュリティ フレームワークについて詳しくは、[IoT Edge セキュリティ マネージャー](iot-edge-security-manager.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順

[Azure IoT Edge モジュールについて](iot-edge-modules.md)
