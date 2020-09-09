---
title: ダウンストリーム デバイスを認証する - Azure IoT Edge | Microsoft Docs
description: IoT Hub へのダウンストリーム デバイスまたはリーフ デバイスの認証方法、およびこれらの接続を Azure IoT Edge ゲートウェイ デバイスを経由してルーティングする方法。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3ccb8d29d0ec52c31913a43358c7daa1c0693df7
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2020
ms.locfileid: "84308848"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Azure IoT Hub に対するダウンストリーム デバイスの認証を行う

透過的なゲートウェイのシナリオでは、ダウンストリーム デバイス (リーフ デバイスや子デバイスとも呼ばれます) には、他のデバイスと同じように IoT Hub での ID が必要です。 この記事では、IoT Hub にダウンストリーム デバイスを認証するためのオプションについて説明し、ゲートウェイ接続を宣言する方法を示します。

透過的なゲートウェイ接続を正常にセットアップするための 3 つの一般的な手順があります。 この記事では、2 番目の手順について説明します。

1. ゲートウェイ デバイスをサーバーとして構成し、ダウンストリーム デバイスが安全に接続できるようにします。 ダウンストリーム デバイスからメッセージを受信するようにゲートウェイを設定し、適切な宛先にルーティングします。 詳細については、「[透過的なゲートウェイとして機能するように IoT Edge デバイスを構成する](how-to-create-transparent-gateway.md)」を参照してください。
2. **ダウンストリーム デバイスのデバイス ID を作成して、IoT Hub で認証できるようにします。ゲートウェイ デバイスを介してメッセージを送信するようにダウンストリーム デバイスを構成します。**
3. ダウンストリーム デバイスをゲートウェイ デバイスに接続し、メッセージの送信を開始します。 詳細については、「[ダウンストリーム デバイスを Azure IoT Edge ゲートウェイに接続する](how-to-connect-downstream-device.md)」のページを参照してください。

ダウンストリーム デバイスは、対称キー (共有アクセス キーとも呼ばれます)、X.509 の自己署名証明書、X.509 証明機関 (CA) の署名済み証明書の 3 つの方法のいずれかを使用して IoT Hub で認証できます。 認証の手順は、IoT Hub を使用して IoT Edge ではない任意のデバイスを設定するために使用される手順に似ていますが、ゲートウェイのリレーションシップを宣言するところが少し違っています。

この記事の手順では、手動でのデバイス プロビジョニングについて説明します。 Azure IoT Hub Device Provisioning Service (DPS) を使用したダウンストリーム デバイスの自動プロビジョニングはサポートされていません。

## <a name="prerequisites"></a>前提条件

「[透過的なゲートウェイとして機能するように IoT Edge デバイスを構成する](how-to-create-transparent-gateway.md)」の手順を完了してください。

X.509 認証を使用している場合は、ダウンストリーム デバイス用の証明書を生成します。 同じルート CA 証明書と、透過的なゲートウェイの記事に使用した証明書生成スクリプトを再度使用できるようにします。

この記事では、いくつかの時点で*ゲートウェイ ホスト名*を参照します。 ゲートウェイ ホスト名は、IoT Edge ゲートウェイ デバイス上の config.yaml ファイルの **hostname** パラメーターで宣言されます。 これはダウンストリーム デバイスの接続文字列で参照されます。 ゲートウェイ ホスト名は、DNS またはダウンストリーム デバイス上の host ファイル エントリのどちらかを使用して IP アドレスに解決できる必要があります。

## <a name="register-device-with-iot-hub"></a>IoT Hub でデバイスを登録する

IoT Hub でダウンストリーム デバイスを認証する方法を選択します。

* [対称キー認証](#symmetric-key-authentication):IoT Hub で、ダウンストリーム デバイスに配置するキーが作成されます。 デバイスが認証されると、IoT Hub で 2 つのキーが一致することが確認されます。 対称キー認証を使用するために追加の証明書を作成する必要はありません。
* [X.509 自己署名認証](#x509-self-signed-authentication):デバイスの X.509 証明書からの拇印を IoT Hub と共有するため、拇印認証とも呼ばれます。
* [X.509 CA 署名認証](#x509-ca-signed-authentication):ルート CA 証明書を IoT Hub にアップロードします。 デバイスが認証用に X.509 証明書を提示すると、IoT Hub では、同じルート CA 証明書によって署名された信頼チェーンに属しているかどうかが確認されます。

これら 3 つの方法のいずれかを使用してデバイスを登録したら、次のセクションに進み、ダウンストリーム デバイス用に[接続文字列を取得して変更](#retrieve-and-modify-connection-string)します。

### <a name="symmetric-key-authentication"></a>対称キーの認証

対称キーの認証 (共有アクセス キーの認証) は、IoT Hub で認証する最も簡単な方法です。 対称キーの認証では、base64 キーが IoT Hub 内の IoT デバイス ID に関連付けられます。 そのキーを自分の IoT アプリケーションに含めて、お使いのデバイスが IoT Hub に接続するときにそれを渡せるようにします。

Azure portal、Azure CLI、Visual Studio Code の IoT 拡張機能のいずれかを使用して、お使いの IoT ハブに新しい IoT デバイスを追加します。 ダウンストリーム デバイスは、IoT Hub 内で、IoT Edge デバイスではなく通常の IoT デバイスとして識別される必要があることに注意してください。

新しいデバイス ID を作成するときに、次の情報を提供します。

* デバイスの ID を作成します。

* 認証の種類として **[対称キー]** を選択します。

* 必要に応じて、 **[親デバイスの設定]** を選択し、このダウンストリーム デバイスが経由して接続する IoT Edge ゲートウェイ デバイスを選択します。 この手順は、対称キーの認証では省略可能ですが、親デバイスを設定するとお使いのダウンストリーム デバイスに対して[オフライン機能](offline-capabilities.md)が有効になるため、実行することをお勧めします。 デバイスの詳細は、後からいつでも更新して、親を追加したり変更することができます。

   ![ポータルで対称キー認証を使用したデバイス ID の作成](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

[Azure CLI の IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)を使用しても同じ操作を完了できます。 次の例では、対称キーの認証で新しい IoT デバイスを作成し、親デバイスを割り当てます。

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

デバイスの作成と親/子の管理のための Azure CLI コマンドの詳細については、[az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest) コマンドのリファレンス コンテンツを参照してください。

次に、[接続文字列 を取得および変更](#retrieve-and-modify-connection-string)して、デバイスがゲートウェイ経由で接続するように設定 します。

### <a name="x509-self-signed-authentication"></a>X.509 自己署名認証

X.509 自己署名認証 (拇印認証とも呼ばれます) の場合、お使いのダウンストリーム デバイス上に配置する証明書を作成する必要があります。 これらの証明書には、認証のために IoT Hub と共有する拇印が含まれています。

1. CA 証明書を使用して、ダウンストリーム デバイス用に 2 つのデバイス証明書 (プライマリとセカンダリ) を作成します。

   X.509 証明書を作成するための証明機関がない場合は、IoT Edge のデモ証明書スクリプトを使用して、[ダウンストリーム デバイスの証明書を作成](how-to-create-test-certificates.md#create-downstream-device-certificates)できます。 自己署名証明書を作成する手順に従います。 ゲートウェイ デバイスの証明書を生成した同じルート CA 証明書を使用します。

   独自の証明書を作成する場合は、そのデバイス証明書のサブジェクト名が、Azure IoT Hub で IoT デバイスを登録するときに使用するデバイス ID に設定されていることを確認してください。 この設定は認証に必要です。

2. 各証明書から、SHA1 フィンガープリント (IoT Hub のインターフェイスでは拇印と呼ばれます) を取得します。これは 16 進数の 40 文字の文字列です。 次の openssl コマンドを使用して、証明書を表示し、フィンガープリントを見つけます。

   * Windows:

     ```PowerShell
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint
     ```

   * Linux:

     ```Bash
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
     ```

   このコマンドは 2 回実行します。1 回目はプライマリ証明書、2 回目はセカンダリ証明書に対して実行します。 自己署名 x.509 証明書を使用して新しい IoT デバイスを登録する場合は、両方の証明書のフィンガープリントを指定します。

3. Azure portal で自分の IoT ハブに移動し、次の値で新しい IoT デバイス ID を作成します。

   * デバイス証明書のサブジェクト名と一致する **[デバイス ID]** を指定します。
   * 認証の種類として **[X.509 自己署名済み]** を選択します。
   * デバイスのプライマリとセカンダリの証明書からコピーした 16 進数の文字列を貼り付けます。
   * **[親デバイスの設定]** を選択し、このダウンストリーム デバイスが経由して接続する IoT Edge ゲートウェイ デバイスを選択します。 親デバイスは、ダウンストリーム デバイスの X.509 認証に必要です。

   ![ポータルで X.509 自己署名認証を使用したデバイス ID の作成](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. プライマリとセカンダリの両方のデバイス証明書とそのキーを、ダウンストリーム デバイス上の任意の場所にコピーします。 また、ゲートウェイ デバイス証明書とダウンストリーム デバイス証明書の両方を生成した共有ルート CA 証明書のコピーを移動します。

   これらの証明書ファイルは、IoT Hub に接続されるダウンストリーム デバイス上のすべてのアプリケーションで参照します。 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) のようなサービスや、[Secure copy protocol](https://www.ssh.com/ssh/scp/) のような関数を使用して、証明書ファイルを削除することができます。

5. 使用する言語に応じて、IoT アプリケーションで x.509 証明書が参照される方法を示すサンプルを確認してください。

   * C#:[Azure IoT Hub での X.509 セキュリティの設定](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java:[SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

[Azure CLI の IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)を使用しても同じデバイス作成操作を完了できます。 次の例では、X.509 自己署名認証で新しい IoT デバイスを作成し、親デバイスを割り当てます。

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

デバイスの作成、証明書の生成、および親子の管理を行うための Azure CLI コマンドの詳細については、[az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest) コマンドのリファレンス コンテンツを参照してください。

次に、[接続文字列 を取得および変更](#retrieve-and-modify-connection-string)して、デバイスがゲートウェイ経由で接続するように設定 します。

### <a name="x509-ca-signed-authentication"></a>X.509 CA 署名認証

X.509 証明機関 (CA) 署名認証の場合、お使いのダウンストリーム デバイスの証明書の署名に使用する IoT Hub に登録されているルート CA 証明書が必要です。 ルート CA 証明書または中間証明書のいずれかで発行された証明書を使用しているデバイスはすべて、認証が許可されます。

このセクションは、IoT Hub の記事「[Azure IoT Hub での X.509 セキュリティの設定](../iot-hub/iot-hub-security-x509-get-started.md)」に詳しく記載されている手順に基づいています。

1. CA 証明書を使用して、ダウンストリーム デバイス用に 2 つのデバイス証明書 (プライマリとセカンダリ) を作成します。

   X.509 証明書を作成するための証明機関がない場合は、IoT Edge のデモ証明書スクリプトを使用して、[ダウンストリーム デバイスの証明書を作成](how-to-create-test-certificates.md#create-downstream-device-certificates)できます。 CA 署名証明書を作成する手順に従います。 ゲートウェイ デバイスの証明書を生成した同じルート CA 証明書を使用します。

2. 「*Azure IoT Hub での X.509 セキュリティの設定*」の「[IoT ハブに X.509 CA 証明書を登録する](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)」セクションの指示に従います。 そのセクションでは、次の手順を実行します。

   1. ルート CA 証明書をアップロードします。 デモ証明書を使用している場合、ルート CA は **\<path>/certs/azure-iot-test-only.root.ca.cert.pem** になります。

   2. そのルート CA 証明書を所有していることを確認します。

3. 「*Azure IoT Hub での X.509 セキュリティの設定*」の「[IoT ハブの X.509 デバイスを作成する](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub)」セクションの指示に従います。 そのセクションでは、次の手順を実行します。

   1. 新しいデバイスを追加します。 **デバイス ID** に小文字の名前を指定し、認証の種類に **X.509 CA 署名済み**を選択します。

   2. 親デバイスを設定します。 ダウンストリーム デバイスの場合は、 **[親デバイスの設定]** を選択し、IoT Hub への接続を提供する IoT Edge ゲートウェイ デバイスを選択します。

4. お使いのダウンストリーム デバイスに証明書チェーンを作成します。 このチェーンを作成するために IoT Hub にアップロードしたのと同じルート CA 証明書を使用します。 ポータルでお使いのデバイス ID に指定したのと同じ小文字のデバイス ID を使用します。

5. デバイス証明書とキーをダウンストリーム デバイス上の任意の場所にコピーします。 また、ゲートウェイ デバイス証明書とダウンストリーム デバイス証明書の両方を生成した共有ルート CA 証明書のコピーを移動します。

   これらのファイルは、IoT Hub に接続されるダウンストリーム デバイス上のすべてのアプリケーションで参照します。 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) のようなサービスや、[Secure copy protocol](https://www.ssh.com/ssh/scp/) のような関数を使用して、証明書ファイルを削除することができます。

6. 使用する言語に応じて、IoT アプリケーションで x.509 証明書が参照される方法を示すサンプルを確認してください。

   * C#:[Azure IoT Hub での X.509 セキュリティの設定](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java:[SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

[Azure CLI の IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)を使用しても同じデバイス作成操作を完了できます。 次の例では、X.509 CA 署名済み認証で新しい IoT デバイスを作成し、親デバイスを割り当てます。

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

詳細については、[az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest) コマンドについての Azure CLI リファレンス コンテンツを参照してください。

次に、[接続文字列 を取得および変更](#retrieve-and-modify-connection-string)して、デバイスがゲートウェイ経由で接続するように設定 します。

## <a name="retrieve-and-modify-connection-string"></a>接続文字列を取得および変更する

ポータルで IoT デバイス ID を作成すると、そのプライマリ キーまたはセカンダリ キーを取得できます。 これらのキーのいずれかが、アプリケーションが IoT Hub との通信に使用する接続文字列に含まれている必要があります。 対称キーの認証では、利便性のため、デバイスの詳細に完全な形式の接続文字列が IoT Hub によって提供されます。 ゲートウェイ デバイスに関する追加情報を接続文字列に追加する必要があります。

ダウンストリーム デバイスの接続文字列には、次のコンポーネントが必要です。

* デバイスが接続する IoT ハブ: `Hostname={iothub name}.azure-devices.net`
* ハブに登録されたデバイス ID: `DeviceID={device ID}`
* プライマリ キーまたはセカンダリ キーのいずれか: `SharedAccessKey={key}`
* デバイスが経由して接続するゲートウェイ デバイス。 IoT Edge ゲートウェイ デバイスの config.yaml ファイルからの **hostname** 値を指定します: `GatewayHostName={gateway hostname}`

すべてがそろった完全な接続文字列は、次のようになります。

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

このダウンストリーム デバイスに対して親/子のリレーションシップを確立すると、ゲートウェイを接続ホストとして直接呼び出すことによって、接続文字列を簡素化することができます。 X.509 認証には親子関係が必要ですが、対称キー認証では省略可能です。 次に例を示します。

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

この変更された接続文字列は、透過的なゲートウェイ シリーズの次の記事で使用します。

## <a name="next-steps"></a>次のステップ

この時点で、ご自身の IoT ハブに IoT Edge デバイスが登録され、透過的なゲートウェイとして構成されています。 また、ご自身の IoT ハブにダウンストリーム デバイスも登録され、そのゲートウェイ デバイスを指しています。

この記事の手順では、IoT Hub に対して認証を行うようにダウンストリーム デバイスを設定しました。 次は、ゲートウェイ デバイスを信頼し、それに安全に接続するようにダウンストリーム デバイスを構成する必要があります。 透過的なゲートウェイ シリーズの次の記事に進み、[ダウンストリーム デバイスを Azure IoT Edge ゲートウェイに接続](how-to-connect-downstream-device.md)します。
