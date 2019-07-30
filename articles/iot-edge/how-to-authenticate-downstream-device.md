---
title: ダウンストリーム デバイスを認証する - Azure IoT Edge | Microsoft Docs
description: IoT Hub へのダウンストリーム デバイスまたはリーフ デバイスの認証方法、およびこれらの接続を Azure IoT Edge ゲートウェイ デバイスを経由してルーティングする方法。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 4d6c7665d281ff7c27fd8b61537804b6803b3b43
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360156"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Azure IoT Hub に対するダウンストリーム デバイスの認証を行う

透過的なゲートウェイのシナリオでは、ダウンストリーム デバイス (リーフ デバイスや子デバイスとも呼ばれます) には、他のデバイスと同じように IoT Hub での ID が必要です。 この記事では、IoT Hub にダウンストリーム デバイスを認証するためのオプションについて説明し、ゲートウェイ接続を宣言する方法を示します。

透過的なゲートウェイ接続を正常にセットアップするための 3 つの一般的な手順があります。 この記事では、2 番目の手順について説明します。

1. ゲートウェイ デバイスは、ダウンストリーム デバイスに安全に接続し、ダウンストリーム デバイスからの通信を受信し、正しい宛先にメッセージをルーティングできる必要があります。 詳細については、「[透過的なゲートウェイとして機能するように IoT Edge デバイスを構成する](how-to-create-transparent-gateway.md)」を参照してください。
2. **ダウンストリーム デバイスは、IoT Hub で認証を行うためにデバイス ID を持っており、そのゲートウェイ デバイス経由で通信することを認識している必要があります。**
3. ダウンストリーム デバイスは、そのゲートウェイ デバイスに安全に接続できる必要があります。 詳細については、「[ダウンストリーム デバイスを Azure IoT Edge ゲートウェイに接続する](how-to-connect-downstream-device.md)」のページを参照してください。

ダウンストリーム デバイスは、対称キー (共有アクセス キーとも呼ばれます)、X.509 の自己署名証明書、X.509 証明機関 (CA) の署名済み証明書の 3 つの方法のいずれかを使用して IoT Hub で認証できます。 認証の手順は、IoT Hub を使用して IoT Edge ではない任意のデバイスを設定するために使用される手順に似ていますが、ゲートウェイのリレーションシップを宣言するところが少し違っています。

この記事の手順では、Azure IoT Hub Device Provisioning Service を使用した自動プロビジョニングではなく、手動でのデバイス プロビジョニングを示します。 

## <a name="symmetric-key-authentication"></a>対称キーの認証

対称キーの認証 (共有アクセス キーの認証) は、IoT Hub で認証する最も簡単な方法です。 対称キーの認証では、base64 キーが IoT Hub 内の IoT デバイス ID に関連付けられます。 そのキーを自分の IoT アプリケーションに含めて、お使いのデバイスが IoT Hub に接続するときにそれを渡せるようにします。 

### <a name="create-the-device-identity"></a>デバイス ID を作成する 

Azure portal、Azure CLI、Visual Studio Code の IoT 拡張機能のいずれかを使用して、お使いの IoT ハブに新しい IoT デバイスを追加します。 ダウンストリーム デバイスは、IoT Hub 内で、IoT Edge デバイスではなく通常の IoT デバイスとして識別される必要があることに注意してください。 

新しいデバイス ID を作成するときに、次の情報を提供します。 

* デバイスの ID を作成します。

* 認証の種類として **[対称キー]** を選択します。 

* 必要に応じて、 **[親デバイスの設定]** を選択し、このダウンストリーム デバイスが経由して接続する IoT Edge ゲートウェイ デバイスを選択します。 この手順は、対称キーの認証では省略可能ですが、親デバイスを設定するとお使いのダウンストリーム デバイスに対して[オフライン機能](offline-capabilities.md)が有効になるため、実行することをお勧めします。 デバイスの詳細は、後からいつでも更新して、親を追加したり変更することができます。 

   ![ポータルで対称キー認証を使用したデバイス ID の作成](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

[Azure CLI の IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)を使用しても同じ操作を完了できます。 次の例では、対称キーの認証で新しい IoT デバイスを作成し、親デバイスを割り当てます。 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID}
```

デバイスの作成と親/子の管理のための Azure CLI コマンドの詳細については、[az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) コマンドのリファレンス コンテンツを参照してください。

### <a name="connect-to-iot-hub-through-a-gateway"></a>ゲートウェイを経由して IoT Hub に接続する

対称キーを使用して IoT Hub に対して通常の IoT デバイスを認証するために使用されるのと同じプロセスが、ダウンストリーム デバイスにも適用されます。 唯一の違いは、接続をルーティングするためのゲートウェイ デバイスへのポインターを追加するか、またはオフライン シナリオでは IoT Hub に代わって認証を処理する必要があることです。 

対称キーの認証では、IoT Hub で認証するためにデバイスで行う必要がある追加の手順はありません。 「[ダウンストリーム デバイスを Azure IoT Edge ゲートウェイに接続する](how-to-connect-downstream-device.md)」で説明されているように、ダウンストリーム デバイスをそのゲートウェイ デバイスに接続できるようにするには、引き続き証明書がある必要があります。

ポータルで IoT デバイス ID を作成すると、そのプライマリ キーまたはセカンダリ キーを取得できます。 これらのキーのいずれかが、IoT Hub と通信するアプリケーションに含める接続文字列に含まれている必要があります。 対称キーの認証では、利便性のため、デバイスの詳細に完全な形式の接続文字列が IoT Hub によって提供されます。 ゲートウェイ デバイスに関する追加情報を接続文字列に追加する必要があります。 

ダウンストリーム デバイスの対称キーの接続文字列には、次のコンポーネントが必要です。 

* デバイスが接続する IoT ハブ: `Hostname={iothub name}.azure-devices.net`
* ハブに登録されたデバイス ID: `DeviceID={device ID}`
* プライマリ キーまたはセカンダリ キーのいずれか: `SharedAccessKey={key}`
* デバイスが経由して接続するゲートウェイ デバイス。 IoT Edge ゲートウェイ デバイスの config.yaml ファイルからの **hostname** 値を指定します: `GatewayHostName={gateway hostname}`

すべてがそろった完全な接続文字列は、次のようになります。

``` 
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

このダウンストリーム デバイスに対して親/子のリレーションシップを確立すると、ゲートウェイを接続ホストとして直接呼び出すことによって、接続文字列を簡素化することができます。 例: 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

## <a name="x509-authentication"></a>X.509 認証 

X.509 証明書を使用して IoT デバイスを認証するには、2 通りの方法があります。 どちらの方法を選択して認証する場合も、デバイスを IoT Hub に接続する手順は同じです。 認証のために自己署名証明書または CA 署名済み証明書を選択し、IoT Hub に接続する方法について引き続き学習します。 

IoT Hub で X.509 認証がどのように使用されているかに関する詳細は、次の記事を参照してください。 
* [X.509 CA 証明書を使用したデバイス認証](../iot-hub/iot-hub-x509ca-overview.md)
* [IoT 業界における X.509 CA 証明書の概念理解](../iot-hub/iot-hub-x509ca-concept.md)

### <a name="create-the-device-identity-with-x509-self-signed-certificates"></a>X.509 自己署名証明書でデバイス ID を作成する

X.509 自己署名認証 (拇印認証とも呼ばれます) の場合、お使いの IoT デバイス上に配置する新しい証明書を作成する必要があります。 これらの証明書には、認証のために IoT Hub と共有する拇印が含まれています。 

このシナリオをテストする最も簡単な方法は、「[透過的なゲートウェイとして機能するように IoT Edge デバイスを構成する](how-to-create-transparent-gateway.md)」で証明書を作成するために使用したのと同じマシンを使用することです。 そのマシンは既に、IoT デバイスの証明書を作成するのに適切なツール、ルート CA 証明書、および中間 CA 証明書を使用して設定されています。 最後の証明書とその秘密キーは、後でダウンストリーム デバイスにコピーできます。 ゲートウェイの記事の手順に従って、お使いのマシンに openssl を設定し、証明書作成スクリプトにアクセスするために IoT Edge リポジトリを複製しました。 次に、証明書を保持する **\<WRKDIR>** という作業ディレクトリを作成しました。 既定の証明書は、開発とテスト目的であるため、30 日間しか保持されません。 ルート CA 証明書と中間証明書を作成するべきでした。 

1. Bash または PowerShell ウィンドウで、作業ディレクトリに移動します。 

2. ダウンストリーム デバイス用に 2 つの証明書 (プライマリとセカンダリ) を作成します。 お使いのデバイス名とプライマリ ラベルまたはセカンダリ ラベルを指定します。 この情報は、複数のデバイスの証明書を追跡できるように、ファイルに名前を付けるのに使用されます。 

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

3. 各証明書から、SHA1 フィンガープリント (IoT Hub のインターフェイスでは拇印と呼ばれます) を取得します。これは 16 進数の 40 文字の文字列です。 次の openssl コマンドを使用して、証明書を表示し、フィンガープリントを見つけます。

   ```PowerShell/bash
   openssl x509 -in <WORKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

4. Azure portal で自分の IoT ハブに移動し、次の値で新しい IoT デバイス ID を作成します。 

   * 認証の種類として **[X.509 自己署名済み]** を選択します。
   * デバイスのプライマリとセカンダリの証明書からコピーした 16 進数の文字列を貼り付けます。
   * **[親デバイスの設定]** を選択し、このダウンストリーム デバイスが経由して接続する IoT Edge ゲートウェイ デバイスを選択します。 親デバイスは、ダウンストリーム デバイスの X.509 認証に必要です。 

   ![ポータルで X.509 自己署名認証を使用したデバイス ID の作成](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

5. 次のファイルをお使いのダウンストリーム デバイス上の任意のディレクトリにコピーします。

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device name>*.key.pem`

   IoT Hub に接続するリーフ デバイス アプリケーションでこれらのファイルを参照します。 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) のようなサービスや、[Secure copy protoco](https://www.ssh.com/ssh/scp/) のような関数を使用して、証明書ファイルを削除することができます。

[Azure CLI の IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)を使用しても同じデバイス作成操作を完了できます。 次の例では、X.509 自己署名認証で新しい IoT デバイスを作成し、親デバイスを割り当てます。 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

デバイスの作成、証明書の生成、および親子の管理を行うための Azure CLI コマンドの詳細については、[az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) コマンドのリファレンス コンテンツを参照してください。

### <a name="create-the-device-identity-with-x509-ca-signed-certificates"></a>X.509 CA 署名済み証明書でデバイス ID を作成する

X.509 証明機関 (CA) の署名済みの認証の場合、お使いの IoT デバイスの証明書の署名に使用する IoT Hub に登録されているルート CA 証明書が必要です。 ルート CA 証明書または中間証明書のいずれかで発行された証明書を使用しているデバイスはすべて、認証が許可されます。 

このセクションは、IoT Hub の記事「[Azure IoT Hub での X.509 セキュリティの設定](../iot-hub/iot-hub-security-x509-get-started.md)」に詳しく記載されている手順に基づいています。 このセクションの手順に従って、ゲートウェイを経由して接続するダウンストリーム デバイスを設定するために使用する値を把握します。 

このシナリオをテストする最も簡単な方法は、「[透過的なゲートウェイとして機能するように IoT Edge デバイスを構成する](how-to-create-transparent-gateway.md)」で証明書を作成するために使用したのと同じマシンを使用することです。 そのマシンは既に、IoT デバイスの証明書を作成するのに適切なツール、ルート CA 証明書、および中間 CA 証明書を使用して設定されています。 最後の証明書とその秘密キーは、後でダウンストリーム デバイスにコピーできます。 ゲートウェイの記事の手順に従って、お使いのマシンに openssl を設定し、証明書作成スクリプトにアクセスするために IoT Edge リポジトリを複製しました。 次に、証明書を保持する **\<WRKDIR>** という作業ディレクトリを作成しました。 既定の証明書は、開発とテスト目的であるため、30 日間しか保持されません。 ルート CA 証明書と中間証明書を作成するべきでした。 

1. 「*Azure IoT Hub での X.509 セキュリティの設定*」の「[IoT ハブに X.509 CA 証明書を登録する](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)」セクションの指示に従います。 そのセクションでは、次の手順を実行します。 

   1. ルート CA 証明書をアップロードします。 透過的なゲートウェイの記事で作成した証明書を使用している場合は、 **\<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem** をルート証明書ファイルとしてアップロードします。 
   2. そのルート CA 証明書を所有していることを確認します。 \<WRKDIR> で証明書ツールを使用して、所有していることを確認できます。 

      ```powershell
      New-CACertsVerificationCert "<verification code from Azure portal>"
      ```

      ```bash
      ./certGen.sh create_verification_certificate <verification code from Azure portal>"
      ```

2. 「*Azure IoT Hub での X.509 セキュリティの設定*」の「[IoT ハブの X.509 デバイスを作成する](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub)」セクションの指示に従います。 そのセクションでは、次の手順を実行します。 

   1. 新しいデバイスを追加します。 **デバイス ID** に小文字の名前を指定し、認証の種類に **X.509 CA 署名済み**を選択します。 
   2. 親デバイスを設定します。 ダウンストリーム デバイスの場合は、 **[親デバイスの設定]** を選択し、IoT Hub への接続を提供する IoT Edge ゲートウェイ デバイスを選択します。 

3. お使いのダウンストリーム デバイスに証明書チェーンを作成します。 このチェーンを作成するために IoT Hub にアップロードしたのと同じルート CA 証明書を使用します。 ポータルでお使いのデバイス ID に指定したのと同じ小文字のデバイス ID を使用します。

   ```powershell
   New-CACertsDevice "<device id>"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

4. 次のファイルをお使いのダウンストリーム デバイス上の任意のディレクトリにコピーします。 

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device id>*.key.pem`

   IoT Hub に接続するリーフ デバイス アプリケーションでこれらのファイルを参照します。 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) のようなサービスや、[Secure copy protoco](https://www.ssh.com/ssh/scp/) のような関数を使用して、証明書ファイルを削除することができます。

[Azure CLI の IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)を使用しても同じデバイス作成操作を完了できます。 次の例では、X.509 CA 署名済み認証で新しい IoT デバイスを作成し、親デバイスを割り当てます。 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

デバイスの作成と親/子の管理のための Azure CLI コマンドの詳細については、[az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) コマンドのリファレンス コンテンツを参照してください。


### <a name="connect-to-iot-hub-through-a-gateway"></a>ゲートウェイを経由して IoT Hub に接続する

各 Azure IoT SDK では、少し異なる方法で X.509 認証が処理されます。 ただし、X.509 証明書を使用して IoT Hub に対して通常の IoT デバイスを認証するために使用されるのと同じプロセスが、ダウンストリーム デバイスにも適用されます。 唯一の違いは、接続をルーティングするためのゲートウェイ デバイスへのポインターを追加するか、またはオフライン シナリオでは IoT Hub に代わって認証を処理する必要があることです。 通常は、すべての IoT Hub デバイスに対して同じ X.509 認証手順に従い、接続文字列内の **Hostname** の値をお使いのゲートウェイ デバイスのホスト名に置き換えるだけです。 

次のセクションでは、さまざまな言語の SDK の例をいくつか示します。 

>[!IMPORTANT]
>次のサンプルでは、IoT Hub SDK が証明書を使用してデバイスを認証する方法を示します。 運用環境のデプロイでは、秘密キーや SAS キーのようなすべてのシークレットをハードウェア セキュリティ モジュール (HSM) に格納する必要があります。 

#### <a name="net"></a>.NET

X.509 証明書で IoT Hub への認証を行う C# プログラムの例については、「[Azure IoT Hub での X.509 セキュリティの設定](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)」を参照してください。 認証プロセスを示すため、そのサンプルの主要な行の一部がここに含まれています。

お使いの DeviceClient インスタンスのホスト名を宣言するときは、IoT Edge ゲートウェイ デバイスのホスト名を使用します。 ホスト名は、ゲートウェイ デバイスの config.yaml ファイルで確認できます。 

IoT Edge の git リポジトリで提供されるテスト証明書を使用している場合、証明書へのキーは **1234** です。

```csharp
try
{
    var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
    var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
    var deviceClient = DeviceClient.Create("<gateway hostname>", auth, TransportType.Amqp_Tcp_Only);

    if (deviceClient == null)
    {
        Console.WriteLine("Failed to create DeviceClient!");
    }
    else
    {
        Console.WriteLine("Successfully created DeviceClient!");
        SendEvent(deviceClient).Wait();
    }

    Console.WriteLine("Exiting...\n");
}
catch (Exception ex)
{
    Console.WriteLine("Error in sample: {0}", ex.Message);
}
```

#### <a name="c"></a>C

X.509 証明書で IoT Hub への認証を行う C プログラムの例については、C IoT SDK の [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample) サンプルを参照してください。 認証プロセスを示すため、そのサンプルの主要な行の一部がここに含まれています。

お使いのダウンストリーム デバイスに接続文字列を定義するときは、**HostName** パラメーターに IoT Edge ゲートウェイ デバイスのホスト名を使用します。 ホスト名は、ゲートウェイ デバイスの config.yaml ファイルで確認できます。 

```C
// If your downstream device uses X.509 authentication (self signed or X.509 CA) then
// resulting connection string should look like the following:
// "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
static const char* connectionString = "[Downstream device IoT Edge connection string]";

// Path to the Edge "owner" root CA certificate
static const char* edge_ca_cert_path = "[Path to root CA certificate]";

// When the downstream device uses X.509 authentication, a certificate and key 
// in PRM format must be provided.
static const char * x509_device_cert_path = "[Path to primary or secondary device cert]";
static const char * x509_device_key_path = "[Path to primary or secondary device key]";

int main(void)
{
    // Create the iothub handle here
    device_handle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, protocol);

    // Provide the Azure IoT device client with the same root
    // X509 CA certificate that was used to set up the IoT Edge gateway runtime
    if (edge_ca_cert_path != NULL)
    {
        cert_string = obtain_edge_ca_certificate();
        (void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
    }

    if ((x509_device_cert_path != NULL) && (x509_device_key_path != NULL))
    {
        const char *x509certificate = obtain_file_contents(x509_device_cert_path);
        const char *x509privatekey = obtain_file_contents(x509_device_key_path);
        if ((IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_CERT, x509certificate) != IOTHUB_CLIENT_OK) ||
            (IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_PRIVATE_KEY, x509privatekey) != IOTHUB_CLIENT_OK)
            )
        {
            printf("failure to set options for x509, aborting\r\n");
            exit(1);
        }
    }
}
```

#### <a name="nodejs"></a>Node.js

X.509 証明書で IoT Hub への認証を行う Node.js プログラムの例については、Node.js IoT SDK の [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js) サンプルを参照してください。 認証プロセスを示すため、そのサンプルの主要な行の一部がここに含まれています。

お使いのダウンストリーム デバイスに接続文字列を定義するときは、**HostName** パラメーターに IoT Edge ゲートウェイ デバイスのホスト名を使用します。 ホスト名は、ゲートウェイ デバイスの config.yaml ファイルで確認できます。 

IoT Edge の git リポジトリで提供されるテスト証明書を使用している場合、証明書へのキーは **1234** です。

```node
// String containing Hostname and Device Id in the following format:
//  "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
var connectionString = '<DEVICE CONNECTION STRING WITH x509=true>';
var certFile = '<PATH-TO-CERTIFICATE-FILE>';
var keyFile = '<PATH-TO-KEY-FILE>';
var passphrase = '<KEY PASSPHRASE IF ANY>';

// fromConnectionString must specify a transport constructor, coming from any transport package.
var client = Client.fromConnectionString(connectionString, Protocol);

var options = {
   cert : fs.readFileSync(certFile, 'utf-8').toString(),
   key : fs.readFileSync(keyFile, 'utf-8').toString(),
   passphrase: passphrase
 };

// Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client transport to use x509 when connecting to IoT Hub
client.setOptions(options);
```

#### <a name="python"></a>Python

X.509 証明書で IoT Hub への認証を行う Python プログラムの例については、Java IoT SDK の [iothub_client_sample_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) サンプルを参照してください。 認証プロセスを示すため、そのサンプルの主要な行の一部がここに含まれています。

お使いのダウンストリーム デバイスに接続文字列を定義するときは、**HostName** パラメーターに IoT Edge ゲートウェイ デバイスのホスト名を使用します。 ホスト名は、ゲートウェイ デバイスの config.yaml ファイルで確認できます。 

```python
# String containing Hostname, Device Id in the format:
# "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
CONNECTION_STRING = "[Device Connection String]"

X509_CERTIFICATE = (
    "-----BEGIN CERTIFICATE-----""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "...""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXX""\n"
    "-----END CERTIFICATE-----"
)

X509_PRIVATEKEY = (
    "-----BEGIN RSA PRIVATE KEY-----""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "...""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    "-----END RSA PRIVATE KEY-----"
)


def iothub_client_init():
    # prepare iothub client
    client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    # this brings in x509 privateKey and certificate
    client.set_option("x509certificate", X509_CERTIFICATE)
    client.set_option("x509privatekey", X509_PRIVATEKEY)

    return client
```

#### <a name="java"></a>Java

X.509 証明書で IoT Hub への認証を行う Java プログラムの例については、Java IoT SDK の [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) サンプルを参照してください。 認証プロセスを示すため、そのサンプルの主要な行の一部がここに含まれています。

お使いのダウンストリーム デバイスに接続文字列を定義するときは、**HostName** パラメーターに IoT Edge ゲートウェイ デバイスのホスト名を使用します。 ホスト名は、ゲートウェイ デバイスの config.yaml ファイルで確認できます。 

```java
//PEM encoded representation of the public key certificate
private static String publicKeyCertificateString =
    "-----BEGIN CERTIFICATE-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END CERTIFICATE-----\n";

//PEM encoded representation of the private key
private static String privateKeyString =
    "-----BEGIN EC PRIVATE KEY-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END EC PRIVATE KEY-----\n";

DeviceClient client = new DeviceClient(connectionString, protocol, publicKeyCertificateString, false, privateKeyString, false);
```

## <a name="next-steps"></a>次の手順

この記事を完了すると、透過的なゲートウェイとして機能する IoT Edge デバイスと、IoT ハブに登録されたダウンストリーム デバイスができているはずです。 次は、ゲートウェイ デバイスを信頼し、それにメッセージを送信するようにダウンストリーム デバイスを構成する必要があります。 詳細については、「[ダウンストリーム デバイスを Azure IoT Edge ゲートウェイに接続する](how-to-connect-downstream-device.md)」のページを参照してください。
