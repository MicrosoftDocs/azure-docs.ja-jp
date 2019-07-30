---
title: 透過的なゲートウェイ デバイスを作成する - Azure IoT Edge | Microsoft Docs
description: ダウンストリーム デバイスからの情報を処理できる透過的なゲートウェイとして、Azure IoT Edge デバイスを使用します
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 50d91c03ac33c32577ddf3838ed6b10024133333
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414542"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>透過的なゲートウェイとして機能するように IoT Edge デバイスを構成する

この記事では、他のデバイスが IoT Hub と通信するための透過的なゲートウェイとして機能するように IoT Edge デバイスを構成するための詳細な手順を提供します。 この記事に記載されている "*IoT Edge ゲートウェイ*" という用語は、透過的なゲートウェイとして使われる IoT Edge デバイスを指します。 詳細は、[「IoT Edge デバイスをゲートウェイとして使用する方法」](./iot-edge-as-gateway.md)を参照してください。

>[!NOTE]
>現時点では:
> * また、Edge 対応デバイスは、IoT Edge ゲートウェイに接続できません。 
> * ダウンストリーム デバイスではファイル アップロードを使用できません。

透過的なゲートウェイ接続を正常にセットアップするための 3 つの一般的な手順があります。 この記事では、最初の手順について説明します。

1. **ゲートウェイ デバイスは、ダウンストリーム デバイスに安全に接続し、ダウンストリーム デバイスからの通信を受信し、正しい宛先にメッセージをルーティングできる必要があります。**
2. ダウンストリーム デバイスは、IoT Hub で認証を行うためにデバイス ID が必要であり、そのゲートウェイ デバイス経由で通信することを認識している必要があります。 詳細については、「[Azure IoT Hub に対するダウンストリーム デバイスの認証を行う](how-to-authenticate-downstream-device.md)」を参照してください。
3. ダウンストリーム デバイスは、そのゲートウェイ デバイスに安全に接続できる必要があります。 詳細については、「[ダウンストリーム デバイスを Azure IoT Edge ゲートウェイに接続する](how-to-connect-downstream-device.md)」のページを参照してください。


デバイスがゲートウェイとして機能するためには、そのダウンストリーム デバイスに安全に接続できる必要があります。 Azure IoT Edge では、公開キー基盤 (PKI) を使用して、これらのデバイス間にセキュリティで保護された接続を設定することができます。 この場合、透過的なゲートウェイとして機能する IoT Edge デバイスにダウンストリーム デバイスが接続できるようにします。 妥当なセキュリティを維持するために、ダウン ストリーム デバイスはゲートウェイ デバイスの ID を確認する必要があります。 この ID 検査により、お使いのデバイスが悪意のある可能性のあるゲートウェイに接続することを防止できます。

[Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) クラウド サービスを使って作成された ID を持つ任意のアプリケーションまたはプラットフォームを、ダウンストリーム デバイスにすることもできます。 多くの場合、これらのアプリケーションでは [Azure IoT device SDK](../iot-hub/iot-hub-devguide-sdks.md) が使用されます。 実際には、ダウンストリーム デバイスには IoT Edge ゲートウェイ デバイスそのもので実行されているアプリケーションも指定できます。 

デバイス ゲートウェイ トポロジに必要な信頼を有効にする証明書インフラストラクチャを作成できます。 この記事では、IoT Hub で [X.509 CA セキュリティ](../iot-hub/iot-hub-x509ca-overview.md)を 有効にするために使用するのと同じ証明書のセットアップを前提としています。これには、特定の IoT ハブ (IoT ハブのルート CA) に関連付けられた X.509 CA 証明書、およびこの CA と IoT Edge デバイスの CA によって署名された一連の証明書が使用されます。

![ゲートウェイ証明書の設定](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>この記事全体で使用されている "ルート CA" という用語は、PKI 証明書チェーンの最上位機関の公開証明書を指し、必ずしもシンジケート化された認証局の証明書ルートではありません。 多くの場合、実際は中間 CA の公開証明書です。 

ゲートウェイは、接続の開始時に、ダウンストリーム デバイスに IoT Edge デバイス CA 証明書を提示します。 ダウンストリーム デバイスは、IoT Edge デバイスの CA 証明書がルート CA 証明書によって署名されていることを確認します。 このプロセスにより、ダウンストリーム デバイスは、ゲートウェイが信頼できるソースからのものであることを確認できます。

次の手順では、証明書を作成し、ゲートウェイ上の適切な場所にインストールするプロセスについて説明します。 任意のマシンを使用して証明書を生成してから、その証明書をお使いの IoT Edge デバイスにコピーできます。 

## <a name="prerequisites"></a>前提条件

ゲートウェイとして構成する Azure IoT Edge デバイス。 次のいずれかのオペレーティング システム用の IoT Edge のインストール手順を使用します。
  * [Windows](/how-to-install-iot-edge-windows.md)
  * [Linux](/how-to-install-iot-edge-linux.md)

この記事では、いくつかの時点で*ゲートウェイ ホスト名*を参照します。 ゲートウェイ ホスト名は、IoT Edge ゲートウェイ デバイス上の config.yaml ファイルの **hostname** パラメーターで宣言されます。 これは、この記事で証明書を作成するために使用され、ダウンストリーム デバイスの接続文字列で参照されます。 ゲートウェイ ホスト名は、DNS または host ファイル エントリのどちらかを使用して IP アドレスに解決できる必要があります。

## <a name="generate-certificates-with-windows"></a>Windows での証明書の生成

Windows でテスト証明書を生成するには、このセクションの手順を使用します。 Windows マシンを使って証明書を生成して、サポートされているオペレーティング システムで実行されている任意の IoT Edge デバイスにそれらをコピーすることができます。 

このセクションで生成される証明書は、テスト目的でのみ使用することを意図しています。 

### <a name="install-openssl"></a>OpenSSL のインストール

証明書を生成するために使用するマシンに OpenSSL for Windows をインストールします。 ご使用の Windows デバイスに既に OpenSSL がインストールされている場合は、この手順をスキップできますが、openssl.exe がご自分の PATH 環境変数で使用可能であることを確認してください。 

OpenSSL をインストールするには、いくつかの方法があります。

* **簡単:** [サードパーティの OpenSSL バイナリ](https://wiki.openssl.org/index.php/Binaries)を (たとえば、[SourceForge の OpenSSL から](https://sourceforge.net/projects/openssl/)) ダウンロードしてインストールします。 openssl.exe への完全なパスを PATH 環境変数に追加します。 
   
* **推奨されるもの:** OpenSSL ソース コードをダウンロードし、お使いのコンピューター上または [vcpkg](https://github.com/Microsoft/vcpkg) 経由でバイナリをビルドします。 以下に示す指示では、vcpkg を使用してソース コードのダウンロード、コンパイル、および Windows コンピューターへの OpenSSL のインストールを、簡単な手順で実行します。

   1. vcpkg をインストールするディレクトリに移動します。 以降では、このディレクトリを *\<VCPKGDIR>* と呼びます。 指示に従って [vcpkg](https://github.com/Microsoft/vcpkg) インストーラーをダウンロードし、実行します。
   
   2. vcpkg がインストールされたら、Powershell プロンプトから次のコマンドを実行して、Windows x64 用の OpenSSL パッケージをインストールします。 インストールには通常、完了まで約 5 分かかります。

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. ご自分の PATH 環境変数に `<VCPKGDIR>\installed\x64-windows\tools\openssl` を追加して、openssl.exe ファイルを呼び出せるようにします。

### <a name="prepare-creation-scripts"></a>作成スクリプトの準備

Azure IoT Edge の Git リポジトリには、テスト証明書の生成に使用できるスクリプトが含まれています。 このセクションでは、IoT Edge リポジトリを複製して、スクリプトを実行します。 

1. 管理者モードで PowerShell ウィンドウを開きます。 

2. 非運用証明書を生成するスクリプトが含まれている git リポジトリを複製します。 これらのスクリプトでは、透過的なゲートウェイの設定に必要な証明書を作成できます。 `git clone` コマンドを使用するか、[ZIP をダウンロードします](https://github.com/Azure/iotedge/archive/master.zip)。 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. 作業するディレクトリに移動します。 この記事では、このディレクトリを *\<WRKDIR>* と呼びます。 すべての証明書とキーは、この作業ディレクトリに作成されます。

4. 構成ファイルとスクリプト ファイルを、複製したリポジトリから作業ディレクトリにコピーします。 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   ZIP ファイルとしてリポジトリをダウンロードした場合、フォルダー名は `iotedge-master` で、パスの残りの部分は同じです。 
<!--
5. Set environment variable OPENSSL_CONF to use the openssl_root_ca.cnf configuration file.

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```
-->
5. PowerShell でスクリプトの実行を有効にします。

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. スクリプトで使用される関数を PowerShell のグローバル名前空間に取り込みます。
   
   ```powershell
   . .\ca-certs.ps1
   ```

   このスクリプトによって生成された証明書はテスト目的専用であり、運用シナリオでは使用してはならないという警告が PowerShell ウィンドウに表示されます。

8. OpenSSL が正しくインストールされていることを確認し、既存証明書と名前の競合がないことを確認します。 問題がある場合、スクリプトは、システム上で問題を修正する方法を示す必要があります。

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>証明書の作成

このセクションでは、3 つの証明書を作成し、それらをチェーンで接続します。 チェーン ファイル内に証明書を配置すると、それらを IoT Edge ゲートウェイ デバイスおよび、ダウンストリーム デバイスに簡単にインストールすることができます。  

1. ルート CA 証明書を作成し、1 つの中間証明書に署名させます。 証明書はすべて作業ディレクトリに配置されます。

   ```powershell
   New-CACertsCertChain rsa
   ```

   このスクリプト コマンドで、いくつかの証明書ファイルとキー ファイルが作成されますが、この記事の後の方で、特にそのうちの 1 つを参照します。
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. 次のコマンドを使用して、IoT Edge デバイス CA 証明書と秘密キーを作成します。 ゲートウェイ ホスト名を指定します。これは、ゲートウェイ デバイス上の iotedge\config.yaml ファイルにあります。 ゲートウェイ ホスト名は、証明書の生成中にファイルを指定するために使用されます。 

   ```powershell
   New-CACertsEdgeDevice "<gateway hostname>"
   ```

   このスクリプト コマンドで、いくつかの証明書ファイルとキー ファイルが作成されます。この記事の後の方で、そのうちの 2 つを参照します。
   * `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`

証明書が作成されたので、スキップして「[ゲートウェイに証明書をインストール](#install-certificates-on-the-gateway)」に進んでください。

## <a name="generate-certificates-with-linux"></a>Linux での証明書の生成

Linux でテスト証明書を生成するには、このセクションの手順を使用します。 Linux マシンを使って証明書を生成して、サポートされているオペレーティング システムで実行されている任意の IoT Edge デバイスにそれらをコピーすることができます。 

このセクションで生成される証明書は、テスト目的でのみ使用することを意図しています。 

### <a name="prepare-creation-scripts"></a>作成スクリプトの準備

Azure IoT Edge の Git リポジトリには、テスト証明書の生成に使用できるスクリプトが含まれています。 このセクションでは、IoT Edge リポジトリを複製して、スクリプトを実行します。 

1. 非運用証明書を生成するスクリプトが含まれている git リポジトリを複製します。 これらのスクリプトでは、透過的なゲートウェイの設定に必要な証明書を作成できます。 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. 作業するディレクトリに移動します。 この記事では、このディレクトリを *\<WRKDIR>* と呼びます。 すべての証明書ファイルとキー ファイルがこのディレクトリに作成されます。
  
3. 構成ファイルとスクリプト ファイルを、複製した IoT Edge リポジトリから作業ディレクトリにコピーします。

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

<!--
4. Configure OpenSSL to generate certificates using the provided script. 

   ```bash
   chmod 700 certGen.sh 
   ```
-->

### <a name="create-certificates"></a>証明書の作成

このセクションでは、3 つの証明書を作成し、それらをチェーンで接続します。 チェーン ファイル内に証明書を配置すると、それらを IoT Edge ゲートウェイ デバイスおよび、ダウンストリーム デバイスに簡単にインストールすることができます。  

1. ルート CA 証明書と 1 つの中間証明書を作成します。 これらの証明書はすべて、 *\<WRKDIR>* に配置されています。

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   このスクリプトで、いくつかの証明書とキーが作成されます。 1 つをメモしてください。次のセクションでそれを参照します。
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. 次のコマンドを使用して、IoT Edge デバイス CA 証明書と秘密キーを作成します。 ゲートウェイ ホスト名を指定します。これは、ゲートウェイ デバイス上の iotedge/config.yaml ファイルにあります。 ゲートウェイ ホスト名は、証明書の生成中にファイルを指定するために使用されます。 

   ```bash
   ./certGen.sh create_edge_device_certificate "<gateway hostname>"
   ```

   このスクリプトで、いくつかの証明書とキーが作成されます。 2 つをメモしてください。次のセクションでそれらを参照します。 
   * `<WRKDIR>/certs/iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<gateway hostname>.key.pem`

## <a name="install-certificates-on-the-gateway"></a>ゲートウェイに証明書をインストール

証明書チェーンを作成したら、それを IoT Edge ゲートウェイ デバイスにインストールして、新しい証明書を参照するように IoT Edge ランタイムを構成する必要があります。 

1. *\<WRKDIR>* から次のファイルをコピーします。 それらを IoT Edge デバイスの任意の場所に保存します。 以降では、IoT Edge デバイスでのコピー先ディレクトリを *\<CERTDIR>* と呼びます。 

   * デバイス CA 証明書 - `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * デバイス CA 秘密キー - `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`
   * ルート CA - `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) のようなサービスや、[Secure copy protocol](https://www.ssh.com/ssh/scp/) のような関数を使用して、証明書ファイルを削除することができます。  IoT Edge デバイス自体で証明書を生成した場合は、この手順をスキップして、作業ディレクトリへのパスを使用することができます。

2. IoT Edge セキュリティ デーモン構成ファイルを開きます。 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. config.yaml ファイル内の **certificate** プロパティを、IoT Edge デバイス上の証明書ファイルとキー ファイルの完全パスに設定します。 証明書プロパティの前の `#` 文字を削除して、4 行をコメント解除します。 yaml 内のインデントは 2 つのスペースであることに注意してください。

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\iot-edge-device-<gateway hostname>-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\iot-edge-device-<gateway hostname>.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux: 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/iot-edge-device-<gateway hostname>-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/iot-edge-device-<gateway hostname>.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. Linux デバイスでは、証明書を保持しているディレクトリの読み取り権限をユーザー **iotedge**が必ず保持しているようにします。 

## <a name="deploy-edgehub-to-the-gateway"></a>ゲートウェイへの Edge ハブのデプロイ

デバイスに IoT Edge を初めてインストールするときには、1 つのシステム モジュール (IoT Edge エージェント) のみが自動的に起動します。 デバイスをゲートウェイとして機能させるには、両方のシステム モジュールが必要です。 これまでゲートウェイ デバイスにどのモジュールもデプロイしていない場合は、2 つ目のシステム モジュール (IoT Edge ハブ) を起動するためにデバイスの最初のデプロイを作成します。 ウィザードでどのモジュールも追加していないため、デプロイは空のように見えますが、これにより、両方のシステム モジュールが実行されていることが確認されます。 

コマンド `iotedge list` を使用して、デバイスで実行されているモジュールをチェックできます。 一覧で、**edgeHub** なしでモジュール **edgeAgent** のみが返される場合は、次の手順を使用します。

1. Azure Portal で、お使いの IoT ハブに移動します。

2. **IoT Edge** に移動し、ゲートウェイとして使用する IoT Edge デバイスを選択します。

3. **[Set Modules] \(モジュールの設定)** を選択します。

4. **[次へ]** を選択します。

5. **[ルートの指定]** ページで、すべてのモジュールから IoT Hub にすべてのメッセージを送信する既定のルートを用意しておく必要があります。 そうしていない場合は、次のコードを追加し、 **[次へ]** を選択します。

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. **[Review template]\(テンプレートのレビュー\)** ページで、 **[送信]** を選びます。

## <a name="open-ports-on-gateway-device"></a>ゲートウェイ デバイスで開いているポート

IoT Hub とのすべての通信は送信接続を介して行われるため、Standard IoT Edge デバイスが機能するために受信接続は必要ありません。 ゲートウェイ デバイスは、ダウンストリーム デバイスからメッセージを受信する必要があるため、異なります。 ダウンストリーム デバイスとゲートウェイ デバイスの間にファイアウォールがある場合は、ファイアウォール経由でも通信が可能である必要があります。

ゲートウェイ シナリオが機能するには、IoT Edge ハブでサポートされているプロトコルの少なくとも 1 つが、ダウンストリーム デバイスからの受信トラフィック用に開かれている必要があります。 サポートされているプロトコルは、MQTT、AMQP、HTTPS です。 

| Port | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>ダウンストリーム デバイスからのメッセージのルーティング
IoT Edge ランタイムでは、モジュールによって送信されたメッセージと同じように、ダウンストリーム デバイスから送信されたメッセージをルーティングできます。 この機能により、クラウドにデータを送信する前に、ゲートウェイで実行されているモジュールの分析を実行することができます。 

現時点では、ダウンストリーム デバイスによって送信されたメッセージをルーティングするには、モジュールによって送信されたメッセージからそれらを区別します。 モジュールによって送信されたすべてのメッセージには **connectionModuleId** という名前のシステム プロパティが含まれますが、ダウンストリーム デバイスによって送信されたメッセージには含まれません。 ルートの WHERE 句を使用して、そのシステム プロパティが含まれるメッセージを除外することができます。 

以下のルートは、任意のダウンストリーム デバイスから `ai_insights` という名前のモジュールにメッセージを送信し、次に `ai_insights` から IoT Hub へと送信する例です。

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

メッセージのルーティングについて詳しくは、[モジュールのデプロイとルートの確立](./module-composition.md#declare-routes)に関する記事をご覧ください。


## <a name="enable-extended-offline-operation"></a>拡張オフライン操作の有効化

IoT Edge ランタイムの [v1.0.4 リリース](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4)以降、ゲートウェイ デバイスとそれに接続しているダウンストリーム デバイスを、拡張オフライン操作用に構成できるようになりました。 

この機能により、ローカル モジュールまたはダウンストリーム デバイスは、必要に応じて IoT Edge デバイスで再認証を行うことができ、IoT Hub から切断されていても、メッセージとメソッドを使用して相互に通信することができます。 詳細については、「[IoT Edge デバイス、モジュール、子デバイスの拡張オフライン機能について](offline-capabilities.md)」を参照してください。

拡張オフライン機能を有効にするには、IoT Edge ゲートウェイ デバイスとそれに接続するダウンストリーム デバイスとの間で親子関係を確立します。 これらの手順については、「[Azure IoT Hub に対するダウンストリーム デバイスの認証を行う](how-to-authenticate-downstream-device.md)」で詳しく説明されています。

## <a name="next-steps"></a>次の手順

これで IoT Edge デバイスが透過的なゲートウェイとして機能するようになったので、ゲートウェイを信頼してメッセージを送信するようにダウンストリーム デバイスを構成する必要があります。 詳細については、「[ダウンストリーム デバイスを Azure IoT Edge ゲートウェイに接続する](how-to-connect-downstream-device.md)」と「[Azure IoT Hub に対するダウンストリーム デバイスの認証を行う](how-to-authenticate-downstream-device.md)」を参照してください。
