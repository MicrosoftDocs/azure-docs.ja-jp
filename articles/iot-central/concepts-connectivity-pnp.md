---
title: Azure IoT Central のデバイス接続機能 | Microsoft Docs
description: この記事では、Azure IoT Central のデバイス接続機能に関連する主な概念を紹介します。
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 81e386be98f9c5684402c376372f43e90fefcb42
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066744"
---
# <a name="device-connectivity-in-azure-iot-central-preview-features"></a>Azure IoT Central のデバイス接続機能 (プレビュー機能)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

この記事では、Microsoft Azure IoT Central のデバイス接続機能に関連する主な概念を紹介します。

Azure IoT Central では、すべてのデバイスの登録と接続の管理に [Azure IoT Hub Device Provisioning Service (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) が使用されます。

DPS を使用すると、次のことが可能になります。

- IoT Central で、大量のデバイスのオンボーディングと接続をサポートできます。
- デバイスの資格情報を生成し、デバイスをオフラインで構成できます。IoT Central UI を通じてデバイスを登録する必要はありません。
- Shared Access Signature (SAS) を使用してデバイスを接続できます。
- 業界標準の X.509 証明書を使用してデバイス接続できます。
- 独自のデバイス ID を使用して、IoT Central にデバイスを登録できます。 独自のデバイス ID を使用して、既存のバックオフィス システムとの統合を簡略化できます。
- 1 つの一貫した方法で IoT Central にデバイスを接続できます。

この記事では、次のユース ケースについて説明します。

- [SAS を使用して 1 台のデバイスをすばやく接続する](#connect-a-single-device)
- [SAS を使用して大量のデバイスを接続する](#connect-devices-at-scale-using-sas)
- [X.509 証明書を使用して大量のデバイスを接続する](#connect-devices-using-x509-certificates)。これは運用環境に対して推奨されるアプローチです。
- [最初にデバイスを登録しないで接続する](#connect-without-registering-devices)
- [IoT プラグ アンド プレイ機能を使用してデバイスを接続する](howto-connect-pnp-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

## <a name="connect-a-single-device"></a>1 つのデバイスを接続する

このアプローチは、IoT Central を使用した実験やデバイスのテストを行う場合に便利です。 Device Provisioning Service (DPS) を使ってデバイスを IoT Central アプリケーションに接続するには、IoT Central アプリケーションからのデバイス接続情報を使用できます。 次の言語のサンプル DPS デバイス クライアント コードがあります。

- [C\#](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device)
- [Node.JS](https://github.com/Azure-Samples/azure-iot-samples-node/tree/master/provisioning/Samples/device)

## <a name="connect-devices-at-scale-using-sas"></a>SAS を使用して大量のデバイスを接続する

SAS を使用して大量のデバイスを IoT Central に接続するには、デバイスを登録してから設定する必要があります。

### <a name="register-devices-in-bulk"></a>デバイスの一括登録

IoT Central アプリケーションに大量のデバイスを登録するには、CSV ファイルを使用して[デバイス ID とデバイス名をインポート](howto-manage-devices.md#import-devices)します。

インポートしたデバイスの接続情報を取得するには、[IoT Central アプリケーションから CSV ファイルをエクスポート](howto-manage-devices.md#export-devices)します。

> [!NOTE]
> 最初に IoT Central に登録しないでデバイスを接続する方法については、「[デバイスを登録しないで接続する](#connect-without-registering-devices)」をご覧ください。

### <a name="set-up-your-devices"></a>デバイスの設定

デバイス コード内のエクスポート ファイルからの接続情報を使用して、デバイスが IoT 経由で IoT Central アプリケーションに接続してデータを送信できるようにします。 デバイスの接続について詳しくは、「[次の手順](#next-steps)」をご覧ください。

## <a name="connect-devices-using-x509-certificates"></a>X.509 証明書を使用してデバイスを接続する

運用環境では、X.509 証明書を使用することが、IoT Central に対して推奨されるデバイス認証メカニズムです。 詳細については、「[X.509 CA 証明書を使用したデバイス認証](../iot-hub/iot-hub-x509ca-overview.md)」をご覧ください。

次の手順では、X.509 証明書を使用して IoT Central にデバイスを接続する方法について説明します。

1. IoT Central アプリケーションで、デバイス証明書の生成に使用している "_中間またはルート X.509 証明書を追加し、検証_" します。

    - **[管理] > [デバイスの接続] > [証明書 (X.509)]** に移動し、リーフ デバイス証明書の生成に使用している X.509 ルートまたは中間証明書を追加します。

      ![接続の設定](media/concepts-connectivity-pnp/connection-settings.png)

      セキュリティ侵害があるか、プライマリ証明書の期限が設定されている場合は、セカンダリ証明書を使用してダウンタイムを短縮します。 プライマリ証明書の更新中に、セカンダリ証明書を使用してデバイスのプロビジョニングに進むことができます。

    - 証明書の所有権を検証して、証明書のアップロード者が証明書の秘密キーを持っていることを確認します。 証明書を検証するには:
        - **[確認コード]** の横のボタンを選択してコードを生成します。
        - 前の手順で生成した確認コードを使用して X.509 検証証明書を作成します。 証明書を .cer ファイルとして保存します。
        - 署名された検証証明書をアップロードし、 **[確認]** を選択します。

          ![接続の設定](media/concepts-connectivity-pnp/verify-cert.png)

1. IoT Central アプリケーション内で、CSV ファイルを使用して "_デバイスをインポートおよび登録_" します。

1. "_デバイスを設定します。_ " アップロードしたルート証明書を使用してリーフ証明書を生成します。 **デバイス ID** をリーフ証明書の CNAME 値として使用します。 デバイス ID はすべて小文字である必要があります。 次に、プロビジョニング サービスの情報を使用してデバイスをプログラムします。 初めてデバイスの電源を入れたときに、デバイスは IoT Central アプリケーションに関する接続情報を DPS から取得します。

### <a name="further-reference"></a>他の参考資料

- [RaspberryPi](https://aka.ms/iotcentral-docs-Raspi-releases) のサンプル実装。

- [C のサンプル デバイス クライアント](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)。

### <a name="for-testing-purposes-only"></a>テスト目的のみ

テストの場合にのみ、これらのユーティリティを使用して CA 証明書とデバイス証明書を生成できます。

- DevKit デバイスを使用している場合、この[コマンドライン ツール](https://aka.ms/iotcentral-docs-dicetool)では、証明書を検証するために IoT Central アプリケーションに追加できる CA 証明書が生成されます。

- この[コマンドライン ツール](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md )を使用して、以下の操作を行います。
  - 証明書チェーンを作成します。 GitHub の記事の手順 2 に従ってください。
  - IoT Central アプリケーションにアップロードする .cer ファイルとして証明書を保存します。
  - IoT Central アプリケーションからの確認コードを使用して検証証明書を生成します。 GitHub の記事の手順 3 に従ってください。
  - デバイス ID をツールへのパラメーターとして使用して、デバイスのリーフ証明書を作成します。 GitHub の記事の手順 4 に従ってください。

## <a name="connect-without-registering-devices"></a>デバイスを登録しないで接続する

IoT Central によって有効になる主要なシナリオは、最初に登録しなくても IoT Central アプリケーションに接続できるデバイスを OEM が大量に製造する場合です。 製造元は、適切な資格情報を生成し、デバイスを工場で構成する必要があります。 初めてデバイスの電源をオンにしたときに、デバイスは IoT Central アプリケーションに自動的に接続します。 IoT Central のオペレーターは、データの送信を開始する前にデバイスを承認する必要があります。

次の図で、このフローについて概説します。

![接続の設定](media/concepts-connectivity-pnp/device-connection-flow1.png)

次の手順では、このプロセスについて詳しく説明します。 手順は、デバイス認証に SAS と X.509 のどちらの証明書を使用しているかによって若干異なります。

1. 接続設定を構成します。

    - **X.509 証明書:** [ルート/中間証明書を追加および検証](#connect-devices-using-x509-certificates)し、次の手順でそれを使用してデバイス証明書を生成します。
    - **SAS:** 主キーをコピーします。 このキーは、IoT Central アプリケーションのグループ SAS キーです。 キーを使用して、次の手順でデバイス SAS キーを生成します。
    ![接続設定 SAS](media/concepts-connectivity-pnp/connection-settings-sas.png)

1. デバイスの資格情報を生成します
    - **証明書 X.509:** IoT Central アプリケーションに追加したルートまたは中間証明書を使用して、デバイスのリーフ証明書を生成します。 リーフ証明書の CNAME として小文字の**デバイス ID** を使用していることを確認します。 テストの目的でのみ、こちらの[コマンドライン ツール](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md )を使用してデバイス証明書を生成します。
    - **SAS:** こちらの[コマンド ライン ツール](https://www.npmjs.com/package/dps-keygen)を使用して、デバイス SAS キーを生成します。 前の手順のグループ**主キー**を使用します。 デバイス ID は小文字である必要があります。

      [キー ジェネレーター ユーティリティ](https://github.com/Azure/dps-keygen)をインストールするには、次のコマンドを実行します。

      ```cmd/sh
      npm i -g dps-keygen
      ```

      グループ SAS 主キーからデバイス キーを生成するには、次のコマンドを実行します。

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. デバイスを設定するには、**スコープ ID**、**デバイス ID**、および **X.509 デバイス証明書**または **SAS キー**を使用して各デバイスをフラッシュします。

1. 次に、そのデバイスの電源をオンにして IoT Central アプリケーションに接続します。 デバイスの電源をオンにすると、そのデバイスはまず DPS に接続して、IoT Central の登録情報を取得します。

1. 接続されているデバイスは、最初は **[Devices]\(デバイス\)** ページに **[関連付けなし]** として表示されます。 デバイスのプロビジョニング状態は **[登録済み]** です。 デバイスを適切なデバイス テンプレートに**移行**して、デバイスが IoT Central アプリケーションに接続するのを承認します。 その後、デバイスでは、IoT Hub から接続文字列を取得し、データの送信を開始できます。 デバイス プロビジョニングが完了し、プロビジョニング状態が **[プロビジョニング済み]** になります。

## <a name="connect-devices-with-iot-plug-and-play"></a>IoT プラグ アンド プレイを使用してデバイスを接続する

IoT Central の IoT プラグ アンド プレイの主な機能の 1 つとして、デバイスの接続時にデバイス テンプレートを自動的に関連付ける機能があります。 デバイスは、デバイスの資格情報と共に、デバイス登録呼び出しの一部として **CapabilityModelId** を送信できるようになりました。IoT Central は、デバイス テンプレートを検出して関連付けます。 検出プロセスは、次の順序で実行されます。

1. デバイス テンプレートに関連付けます (IoT Central アプリケーションで既に発行されている場合)。
1. 発行および認定済みの機能モデルのパブリック リポジトリからフェッチします。

DPS 登録呼び出し中にデバイスが送信する追加のペイロードの形式を次に示します

```javascript
'__iot:interfaces': {
              CapabilityModelId: <this is the URN for the capability model>
          }
```

IoT プラグ アンド プレイ デバイスの接続の詳細については、[IoT プラグ アンド プレイ デバイスの接続](howto-connect-pnp-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)方法に関する記事を参照してください。

> [!NOTE]
> デバイスが自動的に接続し、モデルを検出して、データの送信を開始するためには、自動承認オプションが有効になっている必要がある点に注意してください。

## <a name="device-status"></a>デバイスの状態

実際のデバイスが IoT Central アプリケーションに接続すると、そのデバイスの状態は次のように変わります。

1. デバイスの状態は、最初は **[登録済み]** です。 この状態は、デバイスが IoT Central 内で作成され、デバイス ID を持つことを意味します。 デバイスは、以下の場合に登録されます。
    - 1 台の新しい実際のデバイスが **[Devices]\(デバイス\)** ページで追加された。
    - 一連のデバイスが **[Devices]\(デバイス\)** ページで **[インポート]** を使用して追加された。

1. 有効な資格情報を使用して IoT Central アプリケーションに接続されているデバイスのプロビジョニング手順が完了すると、デバイスの状態が **[プロビジョニング済み]** に変わります。 この手順では、デバイスは IoT Hub から接続文字列を取得します。 デバイスは、IoT Hub に接続し、データの送信を開始できるようになりました。

1. オペレーターはデバイスをブロックできます。 デバイスがブロックされると、そのデバイスは IoT Central アプリケーションにデータを送信できません。 ブロックされたデバイスの状態は **[ブロック]** です。 オペレーターは、データの送信を再開する前にデバイスをリセットする必要があります。 オペレーターがデバイスのブロックを解除すると、状態は前の値 ( **[登録済み]** または **[プロビジョニング済み]** ) に戻ります。

1. デバイスの状態が **[Waiting for Approval]\(承認待ち\)** の場合は、 **[自動承認]** オプションが無効になっており、IoT Central に接続しているすべてのデバイスがオペレーターによって明示的に承認される必要があることを意味します。 **[Devices]\(デバイス\)** ページで手動で登録されていないが、有効な資格情報を使って接続されているデバイスの場合、デバイスの状態が **[Waiting for Approval]\(承認待ち\)** になります。 オペレーターは、 **[Devices]\(デバイス\)** ページから **[承認]** ボタンを使用してこれらのデバイスを承認できます。

1. デバイスの状態が **[関連付けなし]** の場合は、IoT Central に接続しているデバイスにデバイス テンプレートが関連付けられていないことを意味します。 これは通常、次のシナリオで発生します。
    - デバイス テンプレートを指定せずに、 **[Devices]\(デバイス\)** ページで **[インポート]** を使用して一連のデバイスが追加された
    - **[Devices]\(デバイス\)** ページで手動で登録されていないデバイスが、有効な資格情報を使用して接続されたが、登録中にテンプレート ID が指定されなかった。  
オペレーターは、 **[Devices]\(デバイス\)** ページから **[移行]** ボタンを使用して、デバイスをテンプレートに関連付けることができます。

## <a name="sdk-support"></a>SDK のサポート

Azure Device SDK では、デバイス コードを最も簡単に実装する方法が提供されます。 次のデバイス SDK が使用できます。

- [Azure IoT SDK for C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK for Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK for Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK for Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK for .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK の機能と IoT Hub の接続機能

IoT Hub を使用するすべてのデバイス通信では、次の IoT Hub 接続オプションを使用します。

- [デバイスからクラウドへのメッセージ](../iot-hub/iot-hub-devguide-messages-d2c.md)
- [デバイス ツイン](../iot-hub/iot-hub-devguide-device-twins.md)

次の表では、Azure IoT Central デバイスの機能が IoT Hub の機能にどのように対応しているかをまとめたものです。

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| 測定:テレメトリ | デバイスからクラウドへのメッセージ |
| デバイスのプロパティ | デバイス ツインの報告されるプロパティ |
| 設定 | デバイス ツインの目的および報告されるプロパティ |

デバイス SDK の使用方法の詳細については、「[DevDiv キット デバイスを Azure IoT Central アプリケーションに接続する](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)」のコード例を参照してください。

### <a name="protocols"></a>プロトコル

デバイス SDK は、次のネットワーク プロトコルを使った IoT Hub への接続をサポートしています。

- MQTT
- AMQP
- HTTPS

これらプロトコルの相違点や、プロトコルを選択する上でのガイダンスについては、「[通信プロトコルの選択](../iot-hub/iot-hub-devguide-protocols.md)」を参照してください。

デバイスでサポートされているプロトコルのいずれも使用できない場合は、Azure IoT Edge を使用して、プロトコルを変換します。 IoT Edge は、Azure IoT Central アプリケーションの処理負荷を軽減するための他のエッジ上のインテリジェンス シナリオをサポートします。

## <a name="security"></a>セキュリティ

デバイスと、Azure IoT Central 間で交換されるすべてのデータは暗号化されます。 IoT Hub は IoT Hub エンドポイントに接続するデバイスからのすべての要求を認証します。 有線上で資格情報を交換することを避けるため、デバイスでは、認証に署名入りトークンが使用されます。 詳細については、「[IoT Hub へのアクセスの制御](../iot-hub/iot-hub-devguide-security.md)」を参照してください。

## <a name="next-steps"></a>次の手順

ここでは、Azure IoT Central のデバイス接続機能について説明しました。推奨される次の手順は次のとおりです。

- [IoT プラグ アンド プレイ デバイスを接続する](howto-connect-pnp-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
- [DevKit デバイスを準備して接続する](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
- [C SDK:Provisioning Device Client SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
