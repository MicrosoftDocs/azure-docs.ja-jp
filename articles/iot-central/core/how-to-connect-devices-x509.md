---
title: Azure IoT Central アプリケーションで X.509 証明書を使用するデバイスを接続する
description: IoT Central アプリケーション用の Node.js デバイス SDK を使用して、x.509 証明書を使用するデバイスを接続する方法
author: dominicbetts
ms.author: dobett
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: cf0db71600c9350b4d70e6375f509a6e88709f70
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100378334"
---
# <a name="how-to-connect-devices-with-x509-certificates-using-nodejs-device-sdk-for-iot-central-application"></a>IoT Central アプリケーション用の Node.js デバイス SDK を使用して、x.509 証明書を使用するデバイスを接続する方法

IoT Central では、デバイスとアプリケーションの間の通信をセキュリティで保護するために、共有アクセス署名 (SAS) と X.509 証明書の両方がサポートされています。 SAS は、「[クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する](./tutorial-connect-device.md)」チュートリアルで使用されます。 この記事では、X.509 を使用するようにコード サンプルを変更する方法について説明します。  運用環境では、X.509 証明書はお勧めしません。 詳細については、「[Azure IoT Central に接続する](./concepts-get-connected.md)」を参照してください。

この記事では、X.509 の 2 つの使用方法を示します。通常は運用環境で使用される[グループ登録](how-to-connect-devices-x509.md#use-a-group-enrollment)と、テストに役立つ[個別登録](how-to-connect-devices-x509.md#use-an-individual-enrollment)です。

この記事のコード スニペットでは、JavaScript を使用します。 他の言語のコード サンプルについては、次を参照してください。

- [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_ll_client_x509_sample)
- [C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/X509DeviceCertWithChainSample)
- [Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
- [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples)

## <a name="prerequisites"></a>前提条件

- 「[クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する (JavaScript)](./tutorial-connect-device.md)」チュートリアルの完了。
- [Git](https://git-scm.com/download/).
- [OpenSSL](https://www.openssl.org/) のダウンロードとインストール。 Windows を使用している場合は、[SourceForge の OpenSSL ページ](https://sourceforge.net/projects/openssl/)からバイナリを使用できます。

## <a name="use-a-group-enrollment"></a>グループ登録を使用する

運用環境のグループ登録では X.509 証明書を使用します。 グループ登録では、IoT Central アプリケーションにルートまたは中間の X.509 証明書が追加されます。 ルートまたは中間の証明書から派生したリーフ証明書を使用するデバイスをアプリケーションに接続できます。

## <a name="generate-root-and-device-cert"></a>ルートとデバイスの証明書を生成する

このセクションでは、X.509 証明書を使用して、登録グループの証明書から派生した証明書を使用してデバイスを接続します。これにより、IoT Central アプリケーションに接続できます。

> [!WARNING]
> この X.509 証明書の生成方法は、テストのみを目的としています。 運用環境では、証明書を生成するためのセキュリティで保護された正式なメカニズムを使用する必要があります。

1. コマンド プロンプトを開きます。 GitHub リポジトリの証明書生成スクリプトを複製します。

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

1. 証明書ジェネレーター スクリプトに移動し、必要なパッケージをインストールします。

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

1. ルート証明書を作成してから、スクリプトを実行してデバイス証明書を取得します。

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device sample-device-01 mytestrootcert
    ```

    > [!TIP]
    > デバイス ID には、文字、数字、および `-` 文字を含めることができます。

これらのコマンドによって、ルートとデバイス証明書用のファイルがそれぞれ 3 つ生成されます。

filename | 目次
-------- | --------
\<name\>_cert.pem | X509 証明書の公開部分
\<name\>_key.pem | X509 証明書の秘密キー
\<name\>_fullchain.pem | X509 証明書のキーチェーン全体

## <a name="create-a-group-enrollment"></a>グループ登録を作成する

1. IoT Central アプリケーションを開き、左側のペインで **[管理]** に移動し、 **[デバイスの接続]** を選択します。

1. **[+ 登録グループの作成]** を選択し、構成証明書の種類として **証明書 (X.509)** を使用する、_MyX509Group_ という名前の新しい登録グループを作成します。

1. 作成した登録グループを開き、 **[プライマリの管理]** を選択します。

1. ファイル オプションを選択し、先ほど生成した _mytestrootcert_cert.pem_ という名前のルート証明書ファイルをアップロードします。

    ![証明書のアップロード](./media/how-to-connect-devices-x509/certificate-upload.png)

1. 検証を完了するには、確認コードを生成し、それをコピーした後、それを使用してコマンド プロンプトで X.509 検証証明書を作成します。

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

1. 署名された検証証明書 _verification_cert.pem_ をアップロードして検証を完了します。

    ![検証済みの証明書](./media/how-to-connect-devices-x509/verified.png)

これで、このプライマリ ルート証明書から派生した X.509 証明書があるデバイスを接続できます。

登録グループを保存した後、ID スコープをメモしておきます。

## <a name="run-sample-device-code"></a>サンプル デバイス コードを実行する

1. **sampleDevice01_key.pem** と **sampleDevice01_cert.pem** ファイルを **simple_thermostat.js** アプリケーションが格納されている _azure-iot-sdk-node/device/samples/pnp_ フォルダーにコピーします。 このアプリケーションは、[デバイスの接続 (JavaScript) に関するチュートリアル](./tutorial-connect-device.md)の完了時に使用しました。

1. **simple_thermostat.js** アプリケーションが格納されている _azure-iot-sdk-node/device/samples/pnp_ フォルダーに移動し、次のコマンドを実行して X.509 パッケージをインストールします。

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

1. テキスト エディターで **simple_thermostat.js** ファイルを開きます。

1. `require` ステートメントを編集して次の内容を含めます。

    ```javascript
    const fs = require('fs');
    const X509Security = require('azure-iot-security-x509').X509Security;
    ```

1. 次の 4 行を "DPS 接続情報" セクションに追加して、`deviceCert` 変数が初期化されるようにします。

    ```javascript
    const deviceCert = {
      cert: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_CERT).toString(),
      key: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_KEY).toString()
    };
    ```

1. クライアントを作成する `provisionDevice` 関数を編集し、最初の行を次の内容に置き換えます。

    ```javascript
    var provSecurityClient = new X509Security(registrationId, deviceCert);
    ```

1. 同じ関数で、`deviceConnectionString` 変数を設定する行を次のように変更します。

    ```javascript
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    ```

1. `main` 関数で、`Client.fromConnectionString` を呼び出す行の後に次の行を追加します。

    ```javascript
    client.setOptions(deviceCert);
    ```

1. シェル環境で、次の 2 つの環境変数を設定します。

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=sampleDevice01_key.pem
    ```

    > [!TIP]
    > その他の必要な環境変数は、[クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続](./tutorial-connect-device.md)に関するチュートリアルを完了したときに設定しています。

1. スクリプトを実行し、デバイスが正常にプロビジョニングされたことを確認します。

    ```cmd/sh
    node simple_thermostat.js
    ```

    テレメトリがダッシュボードに表示されることを確認することもできます。

    ![デバイス テレメトリを確認する](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-an-individual-enrollment"></a>個別登録を使用する

個別登録の X.509 証明書を使用して、デバイスとソリューションをテストします。 個別登録では、IoT Central アプリケーション内にルートまたは中間の X.509 証明書は存在しません。 デバイスでは、自己署名された X.509 証明書を使用してアプリケーションに接続します。

## <a name="generate-self-signed-device-cert"></a>自己署名されたデバイス証明書を生成する

このセクションでは、自己署名された X.509 証明書を使用して、個別登録用のデバイスを接続します。これらは、単一のデバイスを登録するために使用されます。 自己署名証明書はテスト目的専用です。

スクリプトを実行して、自己署名された X.509 デバイス証明書を作成します。 証明書の名前には、小文字の英数字とハイフンのみを使用します。

  ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    node create_test_cert.js device mytestselfcertprimary
    node create_test_cert.js device mytestselfcertsecondary 
  ```

## <a name="create-individual-enrollment"></a>個別登録を作成する

1. Azure IoT Central アプリケーションで、 **[デバイス]** を選択し、サーモスタット デバイス テンプレートから **デバイス ID** として _mytestselfcertprimary_ を使用する新しいデバイスを作成します。 **ID スコープ** の値をメモしておきます。後で使用します。

1. 作成したデバイスを開き、 **[接続]** を選択します。

1. 次のように、 **[接続方法]** として **[個別登録]** を、[メカニズム] として **[証明書 (X.509)]** を選択します。

    ![個別加入](./media/how-to-connect-devices-x509/individual-device-connect.png)

1. プライマリ用のファイル オプションを選択し、先ほど生成した _mytestselfcertprimary_cert.pem_ という名前の証明書ファイルをアップロードします。

1. セカンダリ証明書用のファイル オプションを選択し、_mytestselfcertsecondary_cert.pem_ という名前の証明書ファイルをアップロードします。 次に、 **[保存]** を選択します。

    ![個別登録証明書のアップロード](./media/how-to-connect-devices-x509/individual-enrollment.png)

これで、デバイスに X.509 証明書がプロビジョニングされました。

## <a name="run-a-sample-individual-enrollment-device"></a>サンプルの個別登録デバイス実行する

1. _mytestselfcertprimary_key.pem_ と _mytestselfcertprimary_cert.pem_ ファイルを **simple_thermostat.js** アプリケーションが格納されている _azure-iot-sdk-node/device/samples/pnp_ フォルダーにコピーします。 このアプリケーションは、[デバイスの接続 (JavaScript) に関するチュートリアル](./tutorial-connect-device.md)の完了時に使用しました。

1. 上記のサンプルで使用した環境変数を次のように変更します。

    ```cmd/sh
    set IOTHUB_DEVICE_DPS_DEVICE_ID=mytestselfcertprimary
    set IOTHUB_DEVICE_X509_CERT=mytestselfcertprimary_cert.pem
    set IOTHUB_DEVICE_X509_KEY=mytestselfcertprimary_key.pem
    ```

1. スクリプトを実行し、デバイスが正常にプロビジョニングされたことを確認します。

    ```cmd/sh
    node environmentalSensor.js
    ```

    テレメトリがダッシュボードに表示されることを確認することもできます。

    ![テレメトリの個別登録](./media/how-to-connect-devices-x509/telemetry-primary.png)

_mytestselfcertsecondary_ 証明書に対して、上記の手順を繰り返すことができます。

## <a name="next-steps"></a>次のステップ

これで、X.509 証明書を使用するデバイスを接続する方法を学習できました。推奨される次の手順は、[Azure CLI を使用してデバイスの接続を監視する](howto-monitor-devices-azure-cli.md)方法を確認することです。
