---
title: テスト証明書を作成する - Azure IoT Edge | Microsoft Docs
description: テスト証明書を作成し、それらを Azure IoT Edge デバイスにインストールして運用環境のデプロイの準備をする方法について説明します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d8cf3dbe9d1dc2ad329a0b5ab8fa9554c85ae55c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199092"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>IoT Edge デバイスの機能をテストするためのデモ用の証明書を作成する

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

ランタイム、モジュール、およびダウンストリーム デバイスとの間の通信をセキュリティで保護するため、IoT Edge デバイスには証明書が必要です。
必要な証明書を作成するための証明機関がない場合は、デモ用の証明書を使用し、テスト環境で IoT Edge の機能を試すことができます。
この記事では、IoT Edge でテスト用に提供されている証明書生成スクリプトの機能について説明します。

これらの証明書の有効期限は 30 日です。運用環境のシナリオでは使用しないでください。

任意のマシンで証明書を生成してから、お使いの IoT Edge デバイスに証明書をコピーできます。
IoT Edge デバイス自体で証明書を生成するのではなく、プライマリ マシンを使用して証明書を作成する方が容易です。
プライマリ マシンを使用することで、スクリプトを 1 回設定すれば、その後は複数のデバイスに対して証明書を作成するのに使用できます。

IoT Edge シナリオをテストするためのデモ証明書を作成するには、次の手順に従います。

1. デバイスで証明書を生成するための[スクリプトを設定します](#set-up-scripts)。
2. シナリオに応じて、他のすべての証明書の署名に使用する[ルート CA 証明書を作成します。](#create-root-ca-certificate)
3. テストするシナリオに必要な証明書を生成します。
   * 手動または IoT Hub Device Provisioning Service を使用して、X.509 証明書認証でデバイスをプロビジョニングするための [IoT Edge デバイス ID 証明書を作成](#create-iot-edge-device-identity-certificates)します。
   * ゲートウェイ シナリオの IoT Edge デバイス向けに [IoT Edge デバイスの CA 証明書を作成します](#create-iot-edge-device-ca-certificates)。
   * ゲートウェイ シナリオでダウンストリーム デバイスを認証するために、[ダウンストリーム デバイス証明書を作成します](#create-downstream-device-certificates)。

## <a name="prerequisites"></a>前提条件

Git がインストールされた開発用マシン。

## <a name="set-up-scripts"></a>スクリプトの設定

GitHub の IoT Edge リポジトリには、デモ用の証明書を作成するのに使用できる証明書生成スクリプトが含まれています。
このセクションでは、Windows または Linux のいずれかのコンピューターで実行するスクリプトを準備する手順について説明します。
Linux マシンを使用する場合は、「[Linux での設定](#set-up-on-linux)」まで進んでください。

### <a name="set-up-on-windows"></a>Windows での設定

Windows デバイスでデモ用の証明書を作成するには、OpenSSL をインストールしてから生成スクリプトを複製し、ローカルで実行されるように PowerShell でスクリプトを設定する必要があります。

#### <a name="install-openssl"></a>OpenSSL のインストール

証明書を生成するために使用するマシンに OpenSSL for Windows をインストールします。
ご使用の Windows デバイスに既に OpenSSL がインストールされている場合は、openssl.exe がご自分の PATH 環境変数で使用可能であることを確認してください。

OpenSSL のインストール方法はいくつかあり、以下のような選択肢があります。

* **簡単:** [サードパーティの OpenSSL バイナリ](https://wiki.openssl.org/index.php/Binaries)を (たとえば、[SourceForge の OpenSSL から](https://sourceforge.net/projects/openssl/)) ダウンロードしてインストールします。 openssl.exe への完全なパスを PATH 環境変数に追加します。

* **推奨:** OpenSSL ソース コードをダウンロードし、お使いのコンピューター上または [vcpkg](https://github.com/Microsoft/vcpkg) 経由でバイナリをビルドします。 以下に示す指示では、vcpkg を使用してソース コードのダウンロード、コンパイル、および Windows コンピューターへの OpenSSL のインストールを、簡単な手順で実行します。

   1. vcpkg をインストールするディレクトリに移動します。 指示に従って [vcpkg](https://github.com/Microsoft/vcpkg) インストーラーをダウンロードし、実行します。

   2. vcpkg がインストールされたら、PowerShell プロンプトから次のコマンドを実行して、Windows x64 用の OpenSSL パッケージをインストールします。 インストールが完了するまで、通常は約 5 分かかります。

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. ご自分の PATH 環境変数に `<vcpkg path>\installed\x64-windows\tools\openssl` を追加して、openssl.exe ファイルを呼び出せるようにします。

#### <a name="prepare-scripts-in-powershell"></a>PowerShell でのスクリプトの準備

Azure IoT Edge の Git リポジトリには、テスト証明書の生成に使用できるスクリプトが含まれています。
このセクションでは、IoT Edge リポジトリを複製して、スクリプトを実行します。

1. 管理者モードで PowerShell ウィンドウを開きます。

2. デモ用の証明書を生成するスクリプトが含まれている IoT Edge git リポジトリを複製します。 `git clone` コマンドを使用するか、[ZIP をダウンロードします](https://github.com/Azure/iotedge/archive/master.zip)。

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

5. PowerShell でスクリプトの実行を有効にします。

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. スクリプトで使用される関数を PowerShell のグローバル名前空間に取り込みます。

   ```powershell
   . .\ca-certs.ps1
   ```

   このスクリプトによって生成された証明書はテスト目的専用であり、運用シナリオでは使用してはならないという警告が PowerShell ウィンドウに表示されます。

7. OpenSSL が正しくインストールされていることを確認し、既存証明書と名前の競合がないことを確認します。 問題がある場合は、スクリプト出力に、システムで問題を修正する方法が記述されているはずです。

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>Linux での設定

Windows デバイスでデモ用の証明書を作成するには、生成スクリプトを複製し、ローカルで実行されるように bash でスクリプトを設定する必要があります。

1. デモ用の証明書を生成するスクリプトが含まれている IoT Edge git リポジトリを複製します。

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

## <a name="create-root-ca-certificate"></a>ルート CA 証明書の作成

ルート CA 証明書は、IoT Edge シナリオをテストするための、その他すべてのデモ用証明書を作成するために使用されます。
同じルート CA 証明書を使用し続けて、複数の IoT Edge やダウンストリーム デバイスのためにデモ用の証明書を作成できます。

作業フォルダーにルート CA 証明書が既に 1 つある場合は、新しいものを作成しないでください。
新しいルート CA 証明書によって古い証明書が上書きされるので、古い証明書から作成されたダウンストリームの証明書がすべて機能しなくなります。
複数のルート CA 証明書が必要な場合は、それらを別個のフォルダーで管理してください。

このセクションの手順を続ける前に、「[スクリプトの設定](#set-up-scripts)」セクションの手順に従って、デモ用の証明書生成スクリプトがある作業ディレクトリを準備してください。

### <a name="windows"></a>Windows

1. 証明書生成スクリプトを配置した作業ディレクトリに移動します。

1. ルート CA 証明書を作成し、1 つの中間証明書に署名させます。 証明書はすべて作業ディレクトリに配置されます。

   ```powershell
   New-CACertsCertChain rsa
   ```

   このスクリプト コマンドでは、証明書とキーのファイルが複数作成されますが、記事で **ルート CA 証明書** が求められた場合は次のファイルを使用します。

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

### <a name="linux"></a>Linux

1. 証明書生成スクリプトを配置した作業ディレクトリに移動します。

1. ルート CA 証明書と 1 つの中間証明書を作成します。

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   このスクリプト コマンドでは、証明書とキーのファイルが複数作成されますが、記事で **ルート CA 証明書** が求められた場合は次のファイルを使用します。

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-identity-certificates"></a>IoT Edge デバイス ID 証明書の作成

X.509 証明書認証を使用することを選択した場合、デバイス ID 証明書が IoT Edge デバイスのプロビジョニングに使用されます。 これらの証明書は、手動プロビジョニングを使用する場合でも、Azure IoT Hub Device Provisioning Service (DPS) による自動プロビジョニングを使用する場合でも機能します。

デバイス ID 証明書は、IoT Edge デバイス上の構成ファイルの **プロビジョニング** セクションにあります。

このセクションの手順を続ける前に、「[スクリプトの設定](#set-up-scripts)」セクションと「[ルート CA 証明書の作成](#create-root-ca-certificate)」セクションの手順に従います。

### <a name="windows"></a>Windows

次のコマンドを使用して、IoT Edge デバイス ID 証明書と秘密キーを作成します。

```powershell
New-CACertsEdgeDeviceIdentity "<name>"
```

このコマンドに渡す名前が、IoT Hub の IoT Edge デバイス用のデバイス ID になります。

新しいデバイス ID コマンドでは、複数の証明書とキー ファイルが作成されます。それには、DPS で個別登録を作成して IoT Edge ランタイムをインストールするときに使用する 3 つが含まれます。

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

### <a name="linux"></a>Linux

次のコマンドを使用して、IoT Edge デバイス ID 証明書と秘密キーを作成します。

```bash
./certGen.sh create_edge_device_identity_certificate "<name>"
```

このコマンドに渡す名前が、IoT Hub の IoT Edge デバイス用のデバイス ID になります。

スクリプトでは、複数の証明書とキー ファイルが作成されます。それには、DPS で個別登録を作成して IoT Edge ランタイムをインストールするときに使用する 2 つが含まれます。

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

## <a name="create-iot-edge-device-ca-certificates"></a>IoT Edge デバイス CA 証明書の作成

運用環境に移行するすべての IoT Edge デバイスには、構成ファイルから参照されるデバイス CA 証明書が必要です。
デバイス CA 証明書により、デバイスで実行されるモジュールのために証明書の作成が行われます。
また、デバイス CA 証明書は、IoT Edge デバイスがダウンストリーム デバイスに対して ID を検証する手段であるため、ゲートウェイのシナリオにも必要です。

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
デバイス CA の証明書は、IoT Edge デバイス上の config.yaml ファイルの **証明書** セクションにあります。
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
デバイス CA の証明書は、IoT Edge デバイス上の config.toml ファイルの **Edge CA** セクションにあります。
:::moniker-end

このセクションの手順を続ける前に、「[スクリプトの設定](#set-up-scripts)」セクションと「[ルート CA 証明書の作成](#create-root-ca-certificate)」セクションの手順に従います。

### <a name="windows"></a>Windows

1. 証明書生成スクリプトとルート CA 証明書がある作業ディレクトリに移動します。

2. 次のコマンドを使用して、IoT Edge デバイス CA 証明書と秘密キーを作成します。 CA 証明書の名前を入力します。

   ```powershell
   New-CACertsEdgeDevice "<CA cert name>"
   ```

   このコマンドでは、証明書とキーのファイルがいくつか作成されます。 次の証明書とキーのペアを IoT Edge デバイスにコピーし、構成ファイルでそれらを参照する必要があります。

   * `<WRKDIR>\certs\iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-<CA cert name>.key.pem`

**New-CACertsEdgeDevice** コマンドに渡される名前は、構成ファイルの hostname パラメーターや IoT Hub のデバイス ID と同じにしないでください。

### <a name="linux"></a>Linux

1. 証明書生成スクリプトとルート CA 証明書がある作業ディレクトリに移動します。

2. 次のコマンドを使用して、IoT Edge デバイス CA 証明書と秘密キーを作成します。 CA 証明書の名前を入力します。

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "<CA cert name>"
   ```

   このスクリプト コマンドでは、証明書とキーのファイルがいくつか作成されます。 次の証明書とキーのペアを IoT Edge デバイスにコピーし、構成ファイルでそれらを参照する必要があります。

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

**create_edge_device_ca_certificate** コマンドに渡される名前は、構成ファイルの hostname パラメーターや IoT Hub のデバイス ID と同じにしないでください。

## <a name="create-downstream-device-certificates"></a>ダウンストリーム デバイス証明書の作成

ゲートウェイ シナリオのためにダウンストリーム IoT デバイスを設定しており、X.509 認証を使用する場合は、ダウンストリーム デバイスのためのデモ用証明書を生成できます。
対称キー認証を使用する場合は、ダウンストリーム デバイスの追加の証明書を作成する必要はありません。
X.509 証明書を使用して IoT デバイスを認証する方法は 2 つあります。自己署名証明書を使用する方法、または証明機関 (CA) の署名付き証明書を使用する方法です。
X.509 自己署名認証 (拇印認証とも呼ばれます) の場合、お使いの IoT デバイス上に配置する新しい証明書を作成する必要があります。
これらの証明書には、認証のために IoT Hub と共有する拇印が含まれています。
X.509 証明機関 (CA) の署名済みの認証の場合、お使いの IoT デバイスの証明書の署名に使用する IoT Hub に登録されているルート CA 証明書が必要です。
ルート CA 証明書または中間証明書のいずれかで発行された証明書を使用しているデバイスはすべて、認証が許可されます。

証明書生成スクリプトは、これらの認証シナリオのいずれかをテストするデモ用の証明書を作成するのに役立ちます。

このセクションの手順を続ける前に、「[スクリプトの設定](#set-up-scripts)」セクションと「[ルート CA 証明書の作成](#create-root-ca-certificate)」セクションの手順に従います。

### <a name="self-signed-certificates"></a>自己署名証明書

自己署名証明書を使用して IoT デバイスを認証する場合は、お使いのソリューションのルート CA 証明書に基づくデバイス証明書を作成する必要があります。
次に、その証明書から 16 進数の "フィンガープリント" を取得し、IoT Hub に提供します。
IoT Hub で認証できるように、IoT デバイスにはデバイス証明書のコピーも必要です。

#### <a name="windows"></a>Windows

1. 証明書生成スクリプトとルート CA 証明書がある作業ディレクトリに移動します。

2. ダウンストリーム デバイス用に 2 つの証明書 (プライマリとセカンダリ) を作成します。 簡単に使える名前付け規則として、IoT デバイスの名前の後に primary または secondary のラベルを付けて証明書を作成します。 次に例を示します。

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   このスクリプト コマンドでは、証明書とキーのファイルがいくつか作成されます。 以下の証明書とキーのペアをダウンストリーム IoT デバイスにコピーし、IoT Hub に接続するアプリケーション内で、それらのペアを参照する必要があります。

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. 各証明書から SHA1 フィンガープリント (IoT Hub のコンテキストではサムプリントと呼ばれます) を取得します。 フィンガープリントは、40 文字の 16 進数文字列です。 次の openssl コマンドを使用して、証明書を表示し、フィンガープリントを見つけます。

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint
   ```

   このコマンドは 2 回実行します。1 回目はプライマリ証明書、2 回目はセカンダリ証明書に対して実行します。 自己署名 X.509 証明書を使用して新しい IoT デバイスを登録する場合は、両方の証明書のフィンガープリントを指定します。

#### <a name="linux"></a>Linux

1. 証明書生成スクリプトとルート CA 証明書がある作業ディレクトリに移動します。

2. ダウンストリーム デバイス用に 2 つの証明書 (プライマリとセカンダリ) を作成します。 簡単に使える名前付け規則として、IoT デバイスの名前の後に primary または secondary のラベルを付けて証明書を作成します。 次に例を示します。

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   このスクリプト コマンドでは、証明書とキーのファイルがいくつか作成されます。 以下の証明書とキーのペアをダウンストリーム IoT デバイスにコピーし、IoT Hub に接続するアプリケーション内で、それらのペアを参照する必要があります。

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. 各証明書から SHA1 フィンガープリント (IoT Hub のコンテキストではサムプリントと呼ばれます) を取得します。 フィンガープリントは、40 文字の 16 進数文字列です。 次の openssl コマンドを使用して、証明書を表示し、フィンガープリントを見つけます。

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   自己署名 X.509 証明書を使用して新しい IoT デバイスを登録する場合は、プライマリとセカンダリの両方のフィンガープリントを指定します。

### <a name="ca-signed-certificates"></a>CA 署名証明書

自己署名証明書を使用して IoT デバイスを認証する場合は、お使いのソリューションのルート CA 証明書を IoT Hub にアップロードする必要があります。
次に、ルート CA 証明書を所有している IoT Hub を証明するための検証を実行します。
最後に、同じルート CA 証明書を使用して、IoT デバイスが IoT Hub で認証できるように、そのデバイスに配置するデバイス証明書を作成します。

このセクションの証明書は、「[Azure IoT Hub での X.509 セキュリティの設定](../iot-hub/iot-hub-security-x509-get-started.md)」の手順に向けたものです。

#### <a name="windows"></a>Windows

1. 作業ディレクトリからルート CA 証明書ファイル `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` をお使いの IoT ハブにアップロードします。

2. Azure portal で提供されているコードを使用して、そのルート CA 証明書を所有していることを確認します。

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. お使いのダウンストリーム デバイスに証明書チェーンを作成します。 IoT Hub でデバイスの登録に使われたのと同じデバイス ID を使用します。

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   このスクリプト コマンドでは、証明書とキーのファイルがいくつか作成されます。 以下の証明書とキーのペアをダウンストリーム IoT デバイスにコピーし、IoT Hub に接続するアプリケーション内で、それらのペアを参照する必要があります。

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. 作業ディレクトリからルート CA 証明書ファイル `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` をお使いの IoT ハブにアップロードします。

2. Azure portal で提供されているコードを使用して、そのルート CA 証明書を所有していることを確認します。

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. お使いのダウンストリーム デバイスに証明書チェーンを作成します。 IoT Hub でデバイスの登録に使われたのと同じデバイス ID を使用します。

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   このスクリプト コマンドでは、証明書とキーのファイルがいくつか作成されます。 以下の証明書とキーのペアをダウンストリーム IoT デバイスにコピーし、IoT Hub に接続するアプリケーション内で、それらのペアを参照する必要があります。

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
