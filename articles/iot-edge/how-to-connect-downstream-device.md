---
title: ダウンストリーム デバイスを構成する - Azure IoT Edge | Microsoft Docs
description: ダウンストリーム デバイスまたはリーフ デバイスを Azure IoT Edge ゲートウェイ デバイスに接続するように構成する方法。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: 44fe128658b90d2327f17f22b2a33aaa1d4da1fc
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046127"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>ダウンストリーム デバイスを Azure IoT Edge ゲートウェイに接続する

この記事では、ダウンストリーム デバイスと IoT Edge の透過的ゲートウェイの間で信頼できる接続を確立するための手順について説明します。 透過的ゲートウェイのシナリオでは、1 台以上のデバイスが、IoT Hub への接続を維持する単一のゲートウェイ デバイスを経由してメッセージを渡すことができます。

透過的なゲートウェイ接続を正常にセットアップするための 3 つの一般的な手順があります。 この記事では、3 番目の手順について説明します。

1. ゲートウェイ デバイスをサーバーとして構成し、ダウンストリーム デバイスが安全に接続できるようにします。 ダウンストリーム デバイスからメッセージを受信するようにゲートウェイを設定し、適切な宛先にルーティングします。 その手順については、「[透過的なゲートウェイとして機能するように IoT Edge デバイスを構成する](how-to-create-transparent-gateway.md)」を参照してください。
2. ダウンストリーム デバイスのデバイス ID を作成して、IoT Hub で認証できるようにします。 ゲートウェイ デバイスを介してメッセージを送信するようにダウンストリーム デバイスを構成します。 その手順については、「[Azure IoT Hub に対するダウンストリーム デバイスの認証を行う](how-to-authenticate-downstream-device.md)」を参照してください。
3. **ダウンストリーム デバイスをゲートウェイ デバイスに接続し、メッセージの送信を開始します。**

この記事では、ダウンストリーム デバイス接続の基本概念について説明し、ダウンストリーム デバイスを設定するための次のような手順を説明します。

* トランスポート層セキュリティ (TLS) と証明書の基礎について説明します。
* TLS ライブラリが異なる複数のオペレーティング システム上でどのように機能するか、また、各オペレーティング システムによって証明書がどのように処理されるかについて説明します。
* 作業を開始するときに役立つ、複数の言語で用意された Azure IoT のサンプルを紹介します。

この記事では、"*ゲートウェイ*" および "*IoT Edge ゲートウェイ*" という用語は、透過的ゲートウェイとして構成された IoT Edge デバイスを指します。

## <a name="prerequisites"></a>前提条件

* 「[透過的なゲートウェイとして機能するように IoT Edge デバイスを構成する](how-to-create-transparent-gateway.md)」でデバイス CA 証明書を生成するために使用されたルート CA 証明書ファイルを、ダウンストリーム デバイスで利用できるようにします。 ダウンストリーム デバイスは、この証明書を使用してゲートウェイ デバイスの ID を検証します。 デモ証明書を使用している場合、ルート CA 証明書は **azure-iot-test-only.root.ca.cert.pem** になります。
* 「[Azure IoT Hub に対するダウンストリームデバイスの認証を行う](how-to-authenticate-downstream-device.md)」の説明に従って、ゲートウェイデバイスを指すように変更された接続文字列を取得します。

## <a name="prepare-a-downstream-device"></a>ダウンストリーム デバイスを準備する

Azure IoT Hub クラウド サービスを使って作成された ID を持つ任意のアプリケーションまたはプラットフォームを、ダウンストリーム デバイスにすることもできます。 多くの場合、これらのアプリケーションでは [Azure IoT device SDK](../iot-hub/iot-hub-devguide-sdks.md) が使用されます。 ダウンストリーム デバイスは、IoT Edge ゲートウェイ デバイスそのもので実行されているアプリケーションの場合もあります。 ただし、別の IoT Edge デバイスを IoT Edge ゲートウェイのダウンストリームにすることはできません。

>[!NOTE]
>IoT Hub に登録されている IoT デバイスでは、[モジュール ツイン](../iot-hub/iot-hub-devguide-module-twins.md)を使用して、1 つのデバイス上のさまざまなプロセス、ハードウェア、または機能を分離できます。 IoT Edge ゲートウェイでは、x.509 証明書認証ではなく、対称キー認証を使用したダウンストリーム モジュール接続がサポートされます。

ダウンストリーム デバイスを IoT Edge ゲートウェイに接続するには、次の 2 つのものが必要です。

* ゲートウェイへの接続用に情報を追加した IoT Hub デバイス接続文字列を使って、構成されたデバイスまたはアプリケーション。

    この手順は、前の記事「[Azure IoT Hub に対するダウンストリーム デバイスの認証を行う](how-to-authenticate-downstream-device.md#retrieve-and-modify-connection-string)」で実行されました。

* デバイスまたはアプリケーションは、ゲートウェイ デバイスへのトランスポート層セキュリティ (TLS) 接続を検証するために、ゲートウェイの **ルート CA** 証明書を信頼する必要があります。

    この手順については、この記事の残りの部分で詳しく説明します。 この手順は、次の 2 つのうちいずれかの方法で実行できます。つまり、CA 証明書をオペレーティング システムの証明書ストアにインストールする方法と、(特定の言語について) Azure IoT SDK を使用してアプリケーション内で証明書を参照する方法です。

## <a name="tls-and-certificate-fundamentals"></a>TLS と証明書の基礎

ダウンストリーム デバイスを IoT Edge に安全に接続することに関する課題は、インターネット上で発生する他の安全なクライアント/サーバー通信の課題と同様です。 クライアントとサーバーの間では、[トランスポート層セキュリティ (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) を使用してインターネット上での安全な通信が行われます。 TLS は、証明書と呼ばれる標準の[公開キー基盤 (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) 構造体を使用して構築されています。 TLS はかなり複雑な仕様であり、2 つのエンドポイントのセキュリティ保護に関連した幅広いトピックに対応します。 このセクションでは、デバイスを IoT Edge ゲートウェイに安全に接続することに関連した概念をまとめています。

クライアントからサーバーに接続するとき、*サーバー証明書チェーン* と呼ばれる証明書のチェーンがサーバーから提供されます。 証明書チェーンは一般的に、ルート証明機関 (CA) 証明書、1 つ以上の中間 CA 証明書、最後にサーバーの証明書そのものから構成されます。 クライアントは、サーバー証明書チェーン全体を暗号学的に検証し、サーバーとの信頼関係を確立します。 このようなクライアントによるサーバー証明書チェーンの検証は、*サーバー チェーン検証* と呼ばれます。 クライアントは、サーバーに対して、*所有権証明* と呼ばれるプロセスで、サーバー証明書に関連付けられた秘密キーを所持していることを証明するためのチャレンジを実行します。 サーバー チェーン検証と所有権証明書の組み合わせは、*サーバー認証* と呼ばれます。 サーバー証明書チェーンを検証するには、サーバーの証明書を作成 (または発行) するために使われたルート CA 証明書のコピーがクライアントに必要です。 通常、Web サイトに接続する時点で、ブラウザーにはよく使われる CA 証明書が事前に構成されているので、クライアントのプロセスはシームレスです。

デバイスが Azure IoT Hub に接続するとき、デバイスがクライアントであり、IoT Hub クラウド サービスがサーバーです。 IoT Hub クラウド サービスは、**Baltimore CyberTrust Root** と呼ばれるルート CA 証明書によってサポートされています。この証明書は公開されていて入手可能であり、広く使われています。 IoT Hub CA 証明書は、ほとんどのデバイスに既にインストールされているため、多くの TLS 実装 (OpenSSL、Schannel、LibreSSL など) において、サーバー証明書の検証時にその証明書が自動的に使用されます。 ただし、IoT Hub に正常に接続したデバイスから IoT Edge ゲートウェイに接続しようとして、問題が発生する場合があります。

デバイスが IoT Edge ゲートウェイに接続するときは、ダウンストリーム デバイスがクライアントで、ゲートウェイ デバイスがサーバーです。 Azure IoT Edge を使用すると、適合するゲートウェイ証明書チェーンを構築できます。 Baltimore のような公開 CA 証明書を使用することも、自己署名 (または社内) ルート CA 証明書を使用することもできます。 多くの場合、公開 CA 証明書には、関連するコストが生じるため、一般的には運用環境シナリオで使用されます。 開発とテストに適しているのは自己署名 CA 証明書です。 デモ証明書を使用している場合、自己署名ルート CA 証明書になります。

IoT Edge ゲートウェイ用に自己署名のルート CA 証明書を使用するときは、ゲートウェイに接続しようとするダウンストリーム デバイスすべてにその証明書をインストールまたは提供する必要があります。

![ゲートウェイ証明書の設定](./media/how-to-create-transparent-gateway/gateway-setup.png)

IoT Edge 証明書の詳細と、運用環境での実装の詳細については、「[IoT Edge 証明書の使用方法の詳細](iot-edge-certs.md)」を参照してください。

## <a name="provide-the-root-ca-certificate"></a>ルート CA 証明書を指定する

ゲートウェイ デバイスの証明書を検証するために、ダウンストリーム デバイスには、ルート CA 証明書の独自のコピーが必要です。 IoT Edge Git リポジトリで提供されているスクリプトを使用してテスト証明書を作成した場合、ルート CA 証明書は **azure-iot-test-only.root.ca.cert.pem** と呼ばれます。 他のダウンストリーム デバイス準備手順の一部としてまだ行っていない場合は、この証明書ファイルをダウンロード デバイス上の任意のディレクトリに移動します。 [Azure Key Vault](../key-vault/index.yml) のようなサービスや、[Secure copy protocol](https://www.ssh.com/ssh/scp/) のような関数を使用して、証明書ファイルを削除することができます。

## <a name="install-certificates-in-the-os"></a>OS に証明書をインストールする

ルート CA 証明書がダウンストリーム デバイスに置かれたら、ゲートウェイに接続しているアプリケーションで証明書にアクセスできることを確認する必要があります。

オペレーティング システムの証明書ストアにルート CA 証明書をインストールすると、一般的に、ほとんどのアプリケーションでルート CA 証明書を使用できるようになります。 いくつか例外はあり、NodeJS アプリケーションのように、OS 証明書ストアを使用せずにノード ランタイムの内部証明書ストアを使用するものがあります。 オペレーティング システム レベルで証明書をインストールできない場合は、スキップして、「[Azure IoT SDK で証明書を使用する](#use-certificates-with-azure-iot-sdks)」に進んでください。

### <a name="ubuntu"></a>Ubuntu

次のコマンドは、Ubuntu ホストに CA 証明書をインストールする方法を示す例です。 この例は、前提条件の記事の **azure-iot-test-only.root.ca.cert.pem** 証明書を使用していること、およびダウンストリーム デバイス上の場所に証明書をコピー済みであることを前提にしています。

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

"/etc/ssl/certs の証明書を更新中..." という内容のメッセージが表示されます。1 added, 0 removed; done." (/etc/ssl/certs の証明書を更新しています... 1 個追加、0 個削除されました。完了しました) というメッセージが表示されます。

### <a name="windows"></a>Windows

次の手順は、Windows ホストに CA 証明書をインストールする方法の例です。 この例は、前提条件の記事の **azure-iot-test-only.root.ca.cert.pem** 証明書を使用していること、およびダウンストリーム デバイス上の場所に証明書をコピー済みであることを前提にしています。

証明書は、PowerShell の [Import-Certificate](/powershell/module/pkiclient/import-certificate) を使用して管理者としてインストールできます。

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

**certlm** ユーティリティを使用して証明書をインストールすることもできます。

1. スタート メニューで、**コンピューター証明書の管理** を検索して選択します。 **certlm** というユーティリティが開きます。
2. **[証明書 - ローカル コンピューター]**  >  **[信頼されたルート証明機関]** に移動します。
3. **[証明書]** を右クリックし、 **[すべてのタスク]**  >  **[インポート]** を選択します。 証明書のインポート ウィザードが起動します。
4. 表示される手順に従って証明書ファイル `<path>/azure-iot-test-only.root.ca.cert.pem` をインポートします。 完了すると、「正常にインポートされました」メッセージが表示されます。

この記事の後半にある .NET のサンプルで示すように、.NET API を使用してプログラムによって証明書をインストールすることもできます。

一般にアプリケーションでは、TLS 経由で安全に接続するために、Windows で提供される [Schannel](/windows/desktop/com/schannel) という TLS スタックを使用します。 Schannel で TLS 接続の確立を試みるには、すべての証明書が Windows 証明書ストアにインストールされている *必要があります*。

## <a name="use-certificates-with-azure-iot-sdks"></a>証明書で Azure IoT SDK を使用する

このセクションでは、Azure IoT SDK から IoT Edge デバイスに接続する方法について、簡単なサンプル アプリケーションを使用して説明します。 すべてのサンプルの目的は、デバイス クライアントを接続し、テレメトリ メッセージをゲートウェイに送信した後、接続を閉じて終了することです。

アプリケーションレベルのサンプルを使用する前に、次の 2 つのものを準備してください。

* ゲートウェイ デバイスを指すように変更されたダウンストリーム デバイスの IoT Hub 接続文字列、およびダウンストリーム デバイスを IoT Hub に対して認証するために必要なすべての証明書。 詳細については、「[Azure IoT Hub に対するダウンストリーム デバイスの認証を行う](how-to-authenticate-downstream-device.md)」を参照してください。

* コピーしてダウンストリーム デバイス上の任意の場所に保存したルート CA 証明書への完全なパス。

    たとえば、「 `<path>/azure-iot-test-only.root.ca.cert.pem` 」のように入力します。

### <a name="nodejs"></a>NodeJS

このセクションでは、Azure IoT NodeJS デバイス クライアントを IoT Edge ゲートウェイに接続するサンプル アプリケーションを示します。 NodeJS アプリケーションの場合は、こちらに示すように、アプリケーション レベルでルート CA 証明書をインストールする必要があります。 NodeJS アプリケーションでは、システムの証明書ストアは使用されません。

1. **edge_downstream_device.js** のサンプルを [Node.js 用 Azure IoT device SDK サンプル リポジトリ](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)から入手します。
2. **readme.md** ファイルを読み、サンプルを実行するための前提として必要なものがすべて揃っていることを確認します。
3. edge_downstream_device.js ファイル内の **connectionString** 変数と **edge_ca_cert_path** 変数を更新します。
4. お使いのデバイス上でサンプルを実行する方法を示す手順については、SDK ドキュメントを参照してください。

実行するサンプルについて理解するために、次のコード スニペットを参照してください。このコードは、クライアント SDK によって証明書ファイルを読み取り、読み取った証明書ファイルを使用してセキュリティで保護された TLS 接続を確立する方法を示しています。

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

このセクションでは、Azure IoT .NET デバイス クライアントを IoT Edge ゲートウェイに接続するサンプル アプリケーションを示します。 ただし、Linux ホストと Windows ホストの両方で、.NET アプリケーションは、システムの証明書ストアにインストールされている任意の証明書を自動的に使用できます。

1. **EdgeDownstreamDevice** のサンプルは、[IoT Edge .NET サンプル フォルダー](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice)にあります。
2. **readme.md** ファイルを読み、サンプルを実行するための前提として必要なものがすべて揃っていることを確認します。
3. **Properties / launchSettings.json** ファイル内の **DEVICE_CONNECTION_STRING** 変数と **CA_CERTIFICATE_PATH** 変数を更新します。 ホスト システム上の信頼された証明書ストアにインストールされている証明書を使用する場合は、この変数を空白のままにします。
4. お使いのデバイス上でサンプルを実行する方法を示す手順については、SDK ドキュメントを参照してください。

.NET アプリケーションを使用して、信頼された証明書を証明書ストアにプログラムによってインストールするには、**EdgeDownstreamDevice / Program.cs** ファイル内の **InstallCACert()** 関数を参照してください。 この操作は、べき等であるため、その他の効果を与えずに同じ値を使用して複数回実行できます。

### <a name="c"></a>C

このセクションでは、Azure IoT C デバイス クライアントを IoT Edge ゲートウェイに接続するサンプル アプリケーションを示します。 C SDK は、OpenSSL、WolfSSL、Schannel など、多くの TLS ライブラリと共に動作します。 詳細については、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) のページを参照してください。

1. **iotedge_downstream_device_sample** アプリケーションを [C 用 Azure IoT device SDK サンプル](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)のページから入手します。
2. **readme.md** ファイルを読み、サンプルを実行するための前提として必要なものがすべて揃っていることを確認します。
3. iotedge_downstream_device_sample.c ファイル内の **connectionString** 変数と **edge_ca_cert_path** 変数を更新します。
4. お使いのデバイス上でサンプルを実行する方法を示す手順については、SDK ドキュメントを参照してください。


C 用 Azure IoT device SDK には、クライアントを設定するときに CA 証明書を登録するためのオプションが用意されています。 この操作では、証明書をどこにもインストールせず、代わりに、メモリ内にある証明書の文字列形式を使用します。 接続を確立するときに、保存されている証明書が、基になる TLS スタックに提供されます。

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

>[!NOTE]
> [マネージド](https://github.com/Azure/azure-iot-sdk-c#packages-and-libraries) パッケージまたはライブラリを使用している場合は、クライアントのセットアップ時に、CA 証明書を登録する方法が変更される可能性があります。 たとえば、[Arduino IDE ベースのライブラリ](https://github.com/azure/azure-iot-arduino)では、`IoTHubDeviceClient_LL_SetOption` 操作を使用するのではなく、グローバルな [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) ファイルで定義されている証明書配列に CA 証明書を追加する必要があります。  

Windows ホストで OpenSSL または別の TLS ライブラリを使用していない場合、SDK の既定では Schannel が使用されます。 Schannel が機能するためには、IoT Edge ルート CA 証明書が、(`IoTHubDeviceClient_SetOption` 操作を使用して設定されていない) Windows 証明書ストアにインストールされている必要があります。

### <a name="java"></a>Java

このセクションでは、Azure IoT Java デバイス クライアントを IoT Edge ゲートウェイに接続するサンプル アプリケーションを示します。

1. **Send-event** のサンプルを [Java 用 Azure IoT device SDK サンプル](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)のページから入手します。
2. **readme.md** ファイルを読み、サンプルを実行するための前提として必要なものがすべて揃っていることを確認します。
3. お使いのデバイス上でサンプルを実行する方法を示す手順については、SDK ドキュメントを参照してください。

### <a name="python"></a>Python

このセクションでは、Azure IoT Python デバイス クライアントを IoT Edge ゲートウェイに接続するサンプル アプリケーションを示します。

1. **send_message_downstream** のサンプルを [Python 用 Azure IoT device SDK のサンプル](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios)から入手します。
2. Python スクリプトのコメントに指定されているように、`IOTHUB_DEVICE_CONNECTION_STRING` および `IOTEDGE_ROOT_CA_CERT_PATH` 環境変数を設定します。
3. お使いのデバイス上でサンプルを実行する方法に関する追加の手順については、SDK ドキュメントを参照してください。

## <a name="test-the-gateway-connection"></a>ゲートウェイ接続をテストする

ダウンストリーム デバイスでこのサンプル コマンドを使用し、ゲートウェイ デバイスに接続できることをテストします。

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

このコマンドは、MQTTS (ポート 8883) 経由の接続をテストします。 別のプロトコルを使用している場合は、AMQPS (5671) または HTTPS (433) の必要に応じてコマンドを調整する

このコマンドの出力は、チェーン内のすべての証明書に関する情報を含む、長い出力になる場合があります。 接続に成功すると、`Verification: OK` または `Verify return code: 0 (ok)` のような行が表示されます。

![ゲートウェイ接続を確認する](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>ゲートウェイ接続をトラブルシューティングする

リーフ デバイスにゲートウェイ デバイスへの断続的な接続がある場合は、解決のために次の手順を試してください。

1. 接続文字列のゲートウェイ ホスト名は、ゲートウェイ デバイスの IoT Edge 構成ファイル内のホスト名値と同じですか?
2. そのゲートウェイ ホスト名は IP アドレスに解決できますか? DNS を使用するか、またはリーフ デバイスの host ファイル エントリを追加することによって、断続的な接続を解決できます。
3. ファイアウォールの通信ポートは開いていますか? 使用されるプロトコル (MQTTS:8883/AMQPS:5671/HTTPS:433) に基づく通信を、ダウンストリーム デバイスと透過的な IoT Edge の間で可能にする必要があります。

## <a name="next-steps"></a>次のステップ

IoT Edge を使用して、どのような方法でダウンストリーム デバイスに[オフライン機能](offline-capabilities.md)を拡張できるかについて学びます。