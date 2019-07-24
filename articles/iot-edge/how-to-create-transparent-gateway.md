---
title: 透過的なゲートウェイ デバイスを作成する - Azure IoT Edge | Microsoft Docs
description: ダウンストリーム デバイスからの情報を処理できる透過的なゲートウェイとして、Azure IoT Edge デバイスを使用します
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 9d67a87b182758e37c9e379a8f96a6540797ce3e
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58482948"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>透過的なゲートウェイとして機能するように IoT Edge デバイスを構成する

この記事では、他のデバイスが IoT Hub と通信するために、IoT Edge デバイスを透過的なゲートウェイとして機能するように構成するための詳細な手順を提供します。 この記事に記載されている "*IoT Edge ゲートウェイ*" という用語は、透過的なゲートウェイとして使われる IoT Edge デバイスを指します。 詳細については、概念の概要ついて説明している「[IoT Edge デバイスをゲートウェイとして使用する方法](./iot-edge-as-gateway.md)」をご覧ください。

>[!NOTE]
>現時点では:
> * ゲートウェイが IoT Hub から切断された場合、ダウンストリーム デバイスはゲートウェイで認証を行うことができません。
> * また、Edge 対応デバイスは、IoT Edge ゲートウェイに接続できません。 
> * ダウンストリーム デバイスではファイル アップロードを使用できません。

ゲートウェイとして機能するデバイスでは、ダウンストリーム デバイスに安全に接続できる必要があります。 Azure IoT Edge では、公開キー基盤 (PKI) を使用して、これらのデバイス間にセキュリティで保護された接続を設定することができます。 この場合、透過的なゲートウェイとして機能する IoT Edge デバイスにダウンストリーム デバイスが接続できるようにします。 合理的なセキュリティを維持するには、デバイスを自分のゲートウェイにのみ接続し、悪意のある可能性があるゲートウェイには接続しないようにする必要があるため、ダウンストリーム デバイスは Edge デバイスの ID を確認する必要があります。

[Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) クラウド サービスを使って作成された ID を持つ任意のアプリケーションまたはプラットフォームを、ダウンストリーム デバイスにすることもできます。 多くの場合、これらのアプリケーションでは [Azure IoT device SDK](../iot-hub/iot-hub-devguide-sdks.md) が使用されます。 実際には、ダウンストリーム デバイスには IoT Edge ゲートウェイ デバイスそのもので実行されているアプリケーションも指定できます。 

デバイス ゲートウェイ トポロジに必要な信頼を有効にする証明書インフラストラクチャを作成できます。 この記事では、[X.509 CA セキュリティ](../iot-hub/iot-hub-x509ca-overview.md)を IoT Hub で有効にするために使用するのと同じ証明書のセットアップが前提となっています。これには、特定の IoT ハブ (IoT ハブ所有者の CA) に関連付けられている X.509 CA 証明書、およびこの CA と Edge デバイスの CA で署名された一連の証明書が含まれます。

![ゲートウェイ証明書の設定](./media/how-to-create-transparent-gateway/gateway-setup.png)

ゲートウェイは、接続の開始時に、ダウンストリーム デバイスに Edge デバイス CA 証明書を提示します。 ダウンストリーム デバイスは、Edge デバイス CA 証明書が、所有者 CA 証明書によって署名されていることを確認します。 このプロセスにより、ダウンストリーム デバイスは、ゲートウェイが信頼できるソースからのものであることを確認できます。

次の手順では、証明書を作成し、適切な場所にインストールするプロセスについて説明します。

## <a name="prerequisites"></a>前提条件

ゲートウェイとして構成する Azure IoT Edge デバイス。 次のオペレーティング システムの手順に従って、開発マシンまたは仮想マシンを IoT Edge デバイスとして使用できます。
* [Windows](./how-to-install-iot-edge-windows.md)
* [Linux x64](./how-to-install-iot-edge-linux.md)
* [Linux ARM32](./how-to-install-iot-edge-linux-arm.md)

任意のマシンを使用して証明書を生成してから、その証明書をお使いの IoT Edge デバイスにコピーできます。

>[!NOTE]
>この手順で証明書の作成に使用されている "gateway name" は、IoT Edge config.yaml ファイル内のホスト名やダウンストリーム デバイスの接続文字列内にある GatewayHostName として使用されているのと同じ名前にする必要があります。 "gateway name" は、DNS または host ファイル エントリを使用して IP アドレスに解決できるようにする必要があります。 使用されるプロトコル (MQTTS:8883/AMQPS:5671/HTTPS:433) に基づく通信は、ダウンストリーム デバイスと透過的な IoT Edge の間で可能にする必要があります。 ファイアウォールが間にある場合は、それぞれのポートを開く必要があります。

## <a name="generate-certificates-with-windows"></a>Windows での証明書の生成

Windows デバイスでテスト証明書を生成するには、このセクションの手順を使用します。 ご使用の IoT Edge デバイスそのものに証明書を生成することも、別のマシンを使用して、サポートされる任意のオペレーティング システムを実行している任意の IoT Edge デバイスに最終的な証明書をコピーすることもできます。 

このセクションで生成される証明書は、テスト目的でのみ使用することを意図しています。 

### <a name="install-openssl"></a>OpenSSL のインストール

証明書を生成するために使用するマシンに OpenSSL for Windows をインストールします。 OpenSSL をインストールするには、いくつかの方法があります。

   >[!NOTE]
   >ご使用の Windows デバイスに既に OpenSSL がインストールされている場合は、この手順をスキップできますが、openssl.exe がご自分の PATH 環境変数で使用できることを確認してください。

* **簡単:**[サードパーティの OpenSSL バイナリ](https://wiki.openssl.org/index.php/Binaries) (たとえば、[SourceForge のこちらのプロジェクトのバイナリ](https://sourceforge.net/projects/openssl/)) をダウンロードしてインストールします。 openssl.exe への完全なパスを PATH 環境変数に追加します。 
   
* **推奨されるもの:** OpenSSL ソース コードをダウンロードし、お使いのコンピューター上または [vcpkg](https://github.com/Microsoft/vcpkg) 経由でバイナリをビルドします。 以下に示す指示では、vcpkg を使用してソース コードのダウンロード、コンパイル、および Windows コンピューターへの OpenSSL のインストールを、簡単な手順で実行します。

   1. vcpkg をインストールするディレクトリに移動します。 以降では、このディレクトリを *\<VCPKGDIR>* と呼びます。 指示に従って [vcpkg](https://github.com/Microsoft/vcpkg) インストーラーをダウンロードし、実行します。
   
   2. vcpkg がインストールされたら、Powershell プロンプトから次のコマンドを実行して、Windows x64 用の OpenSSL パッケージをインストールします。 インストールには通常、完了まで約 5 分かかります。

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. ご自分の PATH 環境変数に `<VCPKGDIR>\installed\x64-windows\tools\openssl` を追加して、openssl.exe ファイルを呼び出せるようにします。

### <a name="prepare-creation-scripts"></a>作成スクリプトの準備

C 用 Azure IoT device SDK には、テスト証明書の生成に使用できるスクリプトが含まれています。 このセクションでは、SDK を複製し、PowerShell を構成します。

1. 管理者モードで PowerShell ウィンドウを開きます。 

2. 非運用証明書を生成するスクリプトが含まれている git リポジトリを複製します。 これらのスクリプトでは、透過的なゲートウェイの設定に必要な証明書を作成できます。 `git clone` コマンドを使用するか、[ZIP をダウンロードします](https://github.com/Azure/azure-iot-sdk-c/archive/master.zip)。 

   ```powershell
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

3. 作業するディレクトリに移動します。 以降では、このディレクトリを *\<WRKDIR>* と呼びます。  すべてのファイルがこのディレクトリに作成されます。

4. 構成ファイルとスクリプト ファイルを作業ディレクトリにコピーします。 

   ```powershell
   copy <path>\azure-iot-sdk-c\tools\CACertificates\*.cnf .
   copy <path>\azure-iot-sdk-c\tools\CACertificates\ca-certs.ps1 .
   ```

   ZIP ファイルとして SDK をダウンロードした場合は、フォルダー名は `azure-iot-sdk-c-master` で、パスの残りの部分は同じです。 

5. openssl_root_ca.cnf 構成ファイルを使用するように OPENSSL_CONF 環境変数を設定します。

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```

6. PowerShell でスクリプトの実行を有効にします。

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. スクリプトで使用される関数を PowerShell のグローバル名前空間に取り込みます。
   
   ```powershell
   . .\ca-certs.ps1
   ```

8. OpenSSL が正しくインストールされていることを確認し、既存の証明書の名前の競合がないことを確認します。 問題がある場合、スクリプトは、システム上で問題を修正する方法を示す必要があります。

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>証明書の作成

このセクションでは、3 つの証明書を作成し、それらをチェーンで接続します。 チェーン ファイル内に証明書を配置すると、それらを IoT Edge ゲートウェイ デバイスおよび、ダウンストリーム デバイスに簡単にインストールすることができます。  

1. 所有者 CA 証明書を作成し、それに 1 つの中間証明書を署名します。 証明書はすべて、*\<WRKDIR>* に配置されます。

      ```powershell
      New-CACertsCertChain rsa
      ```

2. 次のコマンドを使用して、Edge デバイス CA 証明書と秘密キーを作成します。 証明書の生成中にファイルに名前を付けるために使用されるゲートウェイ デバイスの名前を指定します。 

   ```powershell
   New-CACertsEdgeDevice "<gateway name>"
   ```

3. 次のコマンドを使用して、所有者 CA 証明書、中間証明書、Edge デバイス CA 証明書から証明書チェーンを作成します。 

   ```powershell
   Write-CACertsCertificatesForEdgeDevice "<gateway name>"
   ```

   スクリプトでは、次の証明書とキーを作成します。
   * `<WRKDIR>\certs\new-edge-device.*`
   * `<WRKDIR>\private\new-edge-device.key.pem`
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

## <a name="generate-certificates-with-linux"></a>Linux での証明書の生成

Linux デバイスでテスト証明書を生成するには、このセクションの手順を使用します。 ご使用の IoT Edge デバイスそのものに証明書を生成することも、別のマシンを使用して、サポートされる任意のオペレーティング システムを実行している任意の IoT Edge デバイスに最終的な証明書をコピーすることもできます。 

### <a name="prepare-creation-scripts"></a>作成スクリプトの準備

1. 非運用証明書を生成するスクリプトが含まれている git リポジトリを複製します。 これらのスクリプトでは、透過的なゲートウェイの設定に必要な証明書を作成できます。 

   ```bash
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

2. 作業するディレクトリに移動します。 以降では、このディレクトリを *\<WRKDIR>* と呼びます。  すべてのファイルがこのディレクトリに作成されます。
  
3. 構成ファイルとスクリプト ファイルを作業ディレクトリにコピーします。

   ```bash
   cp <path>/azure-iot-sdk-c/tools/CACertificates/*.cnf .
   cp <path>/azure-iot-sdk-c/tools/CACertificates/certGen.sh .
   ```

4. 指定されたスクリプトを使用して証明書を生成するように OpenSSL を構成します。 

   ```bash
   chmod 700 certGen.sh 
   ```

### <a name="create-certificates"></a>証明書の作成

このセクションでは、3 つの証明書を作成し、それらをチェーンで接続します。 チェーン ファイル内に証明書を配置すると、それらを IoT Edge ゲートウェイ デバイスおよび、ダウンストリーム デバイスに簡単にインストールすることができます。  

1. 所有者 CA 証明書と 1 つの中間証明書を作成します。 これらの証明書はすべて、*\<WRKDIR>* に配置されています。

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   スクリプトにより、次の証明書とキーが作成されます。
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>/certs/azure-iot-test-only.intermediate.cert.pem`
   * `<WRKDIR>/private/azure-iot-test-only.root.ca.key.pem`
   * `<WRKDIR>/private/azure-iot-test-only.intermediate.key.pem`

2. 次のコマンドを使用して、Edge デバイス CA 証明書と秘密キーを作成します。 証明書の生成中にファイルに名前を付けるために使用されるゲートウェイ デバイスの名前を指定します。 

   ```bash
   ./certGen.sh create_edge_device_certificate "<gateway name>"
   ```

   スクリプトでは、次の証明書とキーを作成します。
   * `<WRKDIR>/certs/new-edge-device.*`
   * `<WRKDIR>/private/new-edge-device.key.pem`

3. 所有者 CA 証明書、中間証明書、Edge デバイス CA 証明書から、**new-edge-device-full-chain.cert.pem** と呼ばれる証明書チェーンを作成します。

   ```bash
   cat ./certs/new-edge-device.cert.pem ./certs/azure-iot-test-only.intermediate.cert.pem ./certs/azure-iot-test-only.root.ca.cert.pem > ./certs/new-edge-device-full-chain.cert.pem
   ```

## <a name="install-certificates-on-the-gateway"></a>ゲートウェイに証明書をインストール

証明書チェーンを作成したら、それを IoT Edge ゲートウェイ デバイスにインストールして、新しい証明書を参照するように IoT Edge ランタイムを構成する必要があります。 

1. *\<WRKDIR>* から次のファイルをコピーします。 それらを IoT Edge デバイスの任意の場所に保存します。 以降では、IoT Edge デバイスでのコピー先ディレクトリを *\<CERTDIR>* と呼びます。 

   Edge デバイス自体で証明書を生成した場合は、この手順をスキップして、作業ディレクトリへのパスを使用することができます。

   * デバイス CA 証明書 - `<WRKDIR>\certs\new-edge-device-full-chain.cert.pem`
   * デバイス CA 秘密キー - `<WRKDIR>\private\new-edge-device.key.pem`
   * 所有者 CA - `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. IoT Edge セキュリティ デーモン構成ファイルを開きます。 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. config.yaml ファイル内の **certificate** プロパティを、証明書とキー ファイルを配置した IoT Edge デバイス上の場所へのパスに設定します。

```yaml
certificates:
  device_ca_cert: "<CERTDIR>\\certs\\new-edge-device-full-chain.cert.pem"
  device_ca_pk: "<CERTDIR>\\private\\new-edge-device.key.pem"
  trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
```

## <a name="deploy-edgehub-to-the-gateway"></a>ゲートウェイへの Edge ハブのデプロイ

デバイスに IoT Edge を初めてインストールするときには、1 つのシステム モジュール (Edge エージェント) のみが自動的に起動します。 デバイスをゲートウェイとして機能させるには、両方のシステム モジュールが必要です。 これまでゲートウェイ デバイスにどのモジュールもデプロイしていない場合は、2 つ目のシステム モジュール (Edge ハブ) を起動するデバイス用にデプロイを作成します。 ウィザードでどのモジュールも追加していないため、デプロイが空のように見えますが、両方のシステム モジュールがデプロイされます。 

コマンド `iotedge list` を使用して、デバイスで実行されているモジュールをチェックできます。

1. Azure Portal で、お使いの IoT ハブに移動します。

2. **IoT Edge** に移動し、ゲートウェイとして使用する IoT Edge デバイスを選択します。

3. **[Set Modules] \(モジュールの設定)** を選択します。

4. **[次へ]** を選択します。

5. **[ルートの指定]** ページで、すべてのモジュールから IoT Hub にすべてのメッセージを送信する既定のルートを用意しておく必要があります。 そうしていない場合は、次のコードを追加し、**[次へ]** を選択します。

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. **[Review template]\(テンプレートのレビュー\)** ページで、**[送信]** を選びます。

## <a name="route-messages-from-downstream-devices"></a>ダウンストリーム デバイスからのメッセージのルーティング
IoT Edge ランタイムでは、モジュールによって送信されたメッセージと同じように、ダウンストリーム デバイスから送信されたメッセージをルーティングできます。 これにより、クラウドにデータを送信する前に、ゲートウェイで実行されているモジュールの分析を実行することができます。 

現時点では、ダウンストリーム デバイスによって送信されたメッセージをルーティングするには、モジュールによって送信されたメッセージからそれらを区別します。 モジュールによって送信されたすべてのメッセージには **connectionModuleId** という名前のシステム プロパティが含まれますが、ダウンストリーム デバイスによって送信されたメッセージには含まれません。 ルートの WHERE 句を使用して、そのシステム プロパティが含まれるメッセージを除外することができます。 

次のルートは、ダウンストリーム デバイスから `ai_insights` という名前のモジュールにメッセージを送信するために使用されます。

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

メッセージのルーティングについて詳しくは、[モジュールのデプロイとルートの確立](./module-composition.md#declare-routes)に関する記事をご覧ください。

[!INCLUDE [iot-edge-extended-ofline-preview](../../includes/iot-edge-extended-offline-preview.md)]

## <a name="next-steps"></a>次の手順

これで IoT Edge デバイスが透過的なゲートウェイとして機能するようになったので、ゲートウェイを信頼してメッセージを送信するようにダウンストリーム デバイスを構成する必要があります。 詳細については、「[ダウンストリーム デバイスを Azure IoT Edge ゲートウェイに接続する](how-to-connect-downstream-device.md)」のページを参照してください。
