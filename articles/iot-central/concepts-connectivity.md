---
title: Azure IoT Central のデバイス接続機能 | Microsoft Docs
description: この記事では、Azure IoT Central のデバイス接続機能に関連する主な概念を紹介します。
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 9e1e85d1ab1c5e7ce0cbd96c64137309c2e2916a
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59425969"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Azure IoT Central のデバイス接続機能

この記事では、Microsoft Azure IoT Central のデバイス接続機能に関連する主な概念を紹介します。

Azure IoT Central では、すべてのデバイスの登録と接続の管理に [Azure IoT Hub Device Provisioning Service (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) が使用されます。

DPS を使用すると、次のことが可能になります。

- IoT Central で、大量のデバイスのオンボーディングと接続をサポートできます。
- デバイスの資格情報を生成し、デバイスをオフラインで構成できます。IoT Central UI を通じてデバイスを登録する必要はありません。
- Shared Access Signature (SAS) を使用してデバイスを接続できます。
- 業界標準の X.509 証明書を使用してデバイス接続できます。
- 独自のデバイス ID を使用して、IoT Central にデバイスを登録できます。 独自のデバイス ID を使用して、既存のバックオフィス システムとの統合を簡略化できます。
- 1 つの一貫した方法で IoT Central にデバイスを接続できます。

この記事では、次の 4 つのユース ケースについて説明します。

1. [SAS を使用して 1 台のデバイスをすばやく接続する](#connect-a-single-device)
1. [SAS を使用して大量のデバイスを接続する](#connect-devices-at-scale-using-sas)
1. [X.509 証明書を使用して大量のデバイスを接続する](#connect-devices-using-x509-certificates)。これは運用環境に対して推奨されるアプローチです。
1. [最初にデバイスを登録しないで接続する](#connect-without-registering-devices)

## <a name="connect-a-single-device"></a>1 つのデバイスを接続する

このアプローチは、IoT Central を使用した実験やデバイスのテストを行う場合に便利です。 IoT Central アプリケーションからのデバイス接続情報を使用して、デバイスの接続文字列を生成できます。 詳細については、[Azure IoT Central アプリケーションに接続するためにデバイス接続文字列を生成する方法](howto-generate-connection-string.md)に関するページを参照してください。

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

      ![接続の設定](media/concepts-connectivity/connection-settings.png)

      セキュリティ侵害があるか、プライマリ証明書の期限が設定されている場合は、セカンダリ証明書を使用してダウンタイムを短縮します。 プライマリ証明書の更新中に、セカンダリ証明書を使用してデバイスのプロビジョニングに進むことができます。

    - 証明書の所有権を検証して、証明書のアップロード者が証明書の秘密キーを持っていることを確認します。 証明書を検証するには:
        - **[確認コード]** の横のボタンを選択してコードを生成します。
        - 前の手順で生成した確認コードを使用して X.509 検証証明書を作成します。 証明書を .cer ファイルとして保存します。
        - 署名された検証証明書をアップロードし、**[確認]** を選択します。

          ![接続の設定](media/concepts-connectivity/verify-cert.png)

1. IoT Central アプリケーション内で、CSV ファイルを使用して "_デバイスをインポートおよび登録_" します。

1. _デバイスを設定します。_ アップロードしたルート証明書を使用してリーフ証明書を生成します。 **デバイス ID** をリーフ証明書の CNAME 値として使用します。 デバイス ID はすべて小文字である必要があります。 次に、プロビジョニング サービスの情報を使用してデバイスをプログラムします。 初めてデバイスの電源を入れたときに、デバイスは IoT Central アプリケーションに関する接続情報を DPS から取得します。

### <a name="further-reference"></a>他の参考資料

- [RaspberryPi](https://aka.ms/iotcentral-docs-Raspi-releases) のサンプル実装。

- [C のサンプル デバイス クライアント。](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md)

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

![接続の設定](media/concepts-connectivity/device-connection-flow.png)

次の手順では、このプロセスについて詳しく説明します。 手順は、デバイス認証に SAS と X.509 のどちらの証明書を使用しているかによって若干異なります。

1. 接続設定を構成します。

    - **X.509 証明書:**[ルート/中間証明書を追加および検証](#connect-devices-using-x509-certificates)し、次の手順でそれを使用してデバイス証明書を生成します。
    - **SAS:** 主キーをコピーします。 このキーは、IoT Central アプリケーションのグループ SAS キーです。 キーを使用して、次の手順でデバイス SAS キーを生成します。
    ![接続設定 SAS](media/concepts-connectivity/connection-settings-sas.png)

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

1. 接続されているデバイスは、最初は **[デバイス エクスプローラー]** ページに**関連付けられていないデバイス**として表示されます。 デバイスのプロビジョニング状態は **[登録済み]** です。 デバイスを適切なデバイス テンプレートに**関連付け**て、デバイスが IoT Central アプリケーションに接続するのを承認します。 その後、デバイスでは、IoT Hub から接続文字列を取得し、データの送信を開始できます。 デバイス プロビジョニングが完了し、プロビジョニング状態が **[プロビジョニング済み]** になります。

## <a name="provisioning-status"></a>プロビジョニング状態

実際のデバイスが IoT Central アプリケーションに接続すると、そのプロビジョニング状態は次のように変わります。

1. 最初のデバイス プロビジョニング状態は **[登録済み]** です。 この状態は、デバイスが IoT Central 内で作成され、デバイス ID を持つことを意味します。 デバイスは、以下の場合に登録されます。
    - 1 台の新しい実際のデバイスが **[デバイス エクスプローラー]** ページで追加された。
    - 一連のデバイスが **[デバイス エクスプローラー]** ページの **[インポート]** を使用して追加された。
    - デバイスは **[デバイス エクスプローラー]** ページで手動で登録されなかったが、有効な資格情報を使用して接続され、**[デバイス エクスプローラー]** ページに**関連付けられていない**デバイスとして表示されている。

1. 有効な資格情報を使用して IoT Central アプリケーションに接続されているデバイスのプロビジョニング手順が完了すると、デバイス プロビジョニング状態が **[プロビジョニング済み]** に変わります。 この手順では、デバイスは IoT Hub から接続文字列を取得します。 デバイスは、IoT Hub に接続し、データの送信を開始できるようになりました。

1. オペレーターはデバイスをブロックできます。 デバイスがブロックされると、そのデバイスは IoT Central アプリケーションにデータを送信できません。 ブロックされたデバイスのプロビジョニング状態は **[ブロック]** です。 オペレーターは、データの送信を再開する前にデバイスをリセットする必要があります。 オペレーターがデバイスのブロックを解除すると、プロビジョニング状態は前の値 (**[登録済み]** または **[プロビジョニング済み]**) に戻ります。

## <a name="sdk-support"></a>SDK のサポート

Azure Device SDK では、デバイス コードを最も簡単に実装する方法が提供されます。 次のデバイス SDK が使用できます。

- [Azure IoT SDK for C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK for Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK for Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK for Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK for .NET](https://github.com/azure/azure-iot-sdk-csharp)

各デバイスは、デバイスを識別する一意の接続文字列を使用して接続します。 デバイスは、それが登録されている IoT ハブにのみ接続できます。 Azure IoT Central アプリケーションで実際のデバイスを作成すると、`dps-keygen` を使用して接続文字列を構築するために必要な情報がアプリケーションによって生成されます。

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

デバイス SDK の使用の詳細については、次に関連する記事のコード例を参照してください。

- [汎用の Node.js クライアント を Azure IoT Central アプリケーションに接続する](howto-connect-nodejs.md)
- [Raspberry Pi デバイスを Azure IoT Central アプリケーションに接続する](howto-connect-raspberry-pi-python.md)
- [DevDiv キット デバイスを Azure IoT Central アプリケーションに接続する ](howto-connect-devkit.md)

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

- [DevKit デバイスを準備して接続する](howto-connect-devkit.md)
- [Raspberry Pi を準備して接続する](howto-connect-raspberry-pi-python.md)
- [汎用の Node.js クライアント を Azure IoT Central アプリケーションに接続する](howto-connect-nodejs.md)
- [C SDK:Provisioning Device Client SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
