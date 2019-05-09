---
title: Azure - Azure の Device Provisioning Service クライアント SDK で各種構成証明メカニズムを使用する方法
description: Azure - Azure の Device Provisioning Service クライアント SDK で各種構成証明メカニズムを使用する方法
author: yzhong94
ms.author: yizhon
ms.date: 03/30/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: arjmands
ms.custom: mvc
ms.openlocfilehash: af59ccc6d14dce49d06e178aac3ecafc29bd982c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57990737"
---
# <a name="how-to-use-different-attestation-mechanisms-with-device-provisioning-service-client-sdk-for-c"></a>C 言語用 Device Provisioning Service クライアント SDK で各種構成証明メカニズムを使用する方法

この記事では、C 言語用 Device Provisioning Service クライアント SDK で各種の[構成証明メカニズム](concepts-security.md#attestation-mechanism)を使用する方法について説明します。物理デバイスとシミュレーターのどちらを使用してもかまいません。 このプロビジョニング サービスは、次の 2 種類の構成証明メカニズムでの認証をサポートします: X.509 とトラステッド プラットフォーム モジュール (TPM)。

## <a name="prerequisites"></a>前提条件

[シミュレートされたデバイスの作成とプロビジョニング](./quick-create-simulated-device.md)に関するガイドの「開発環境の準備」セクションに従って開発環境を準備してください。

### <a name="choose-an-attestation-mechanism"></a>構成証明メカニズムの選択

デバイスの製造元は、サポートされるいずれかの種類に基づく構成証明メカニズムを最初に選択する必要があります。 現在、[C 言語用 Device Provisioning Service クライアント SDK](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client) では、次の構成証明メカニズムがサポートされています。 

- [トラステッド プラットフォーム モジュール (TPM)](https://en.wikipedia.org/wiki/Trusted_Platform_Module): TPM は、ほとんどの Windows ベースのデバイス プラットフォームと一部の Linux/Ubuntu ベースのデバイス向けの代表的な標準です。 デバイスの製造元は、製造するデバイスでこれらの OS のいずれかが稼働している場合や、代表的な標準を求めている場合に、この構成証明メカニズムを選択できます。 TPM チップを使用すると、各デバイスの Device Provisioning Service への個別登録のみが可能です。 開発向けの場合、Windows または Linux の開発マシンで TPM シミュレーターを使用できます。

- [X.509](https://cryptography.io/en/latest/x509/):X.509 証明書は、[ハードウェア セキュリティ モジュール (HSM)](concepts-security.md#hardware-security-module) と呼ばれる比較的新しいチップに格納することができます。 また、X.509 証明書を実装する RIoT チップまたは DICE チップ上での作業が Microsoft 内で現在進められています。 X.509 チップを使用すると、ポータルでデバイスの一括登録を実行できます。 また、X.509 チップは、embedOS などの Windows 以外の特定の OS もサポートしています。 開発のために、Device Provisioning Service クライアント SDK は、X.509 デバイス シミュレーターをサポートしています。 

詳細については、IoT Hub Device Provisioning Service の[セキュリティの概念](concepts-security.md)と[自動プロビジョニングの概念](/azure/iot-dps/concepts-auto-provisioning)を参照してください。

## <a name="enable-authentication-for-supported-attestation-mechanisms"></a>サポートされている構成証明メカニズムでの認証を有効にする

Azure Portal で物理デバイスまたはシミュレーターを登録するためには、その物理デバイスまたはシミュレーターに使用する SDK 認証モード (X **.** 509 または TPM) を有効にしておく必要があります。 まず、azure-iot-sdk-c のルート フォルダーに移動します。 その後、選択した認証モードに応じて、指定のコマンドを実行してください。

### <a name="use-x509-with-simulator"></a>X **.** 509 をシミュレーターで使用する場合

プロビジョニング サービスには、デバイスの認証用の X **.** 509 証明書を生成する Device Identity Composition Engine (DICE) エミュレーターが付属しています。 X **.** 509 認証を有効にするには、次のコマンドを実行します。 

```
cmake -Ddps_auth_type=x509 ..
```

DICE 対応ハードウェアについては、[こちら](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/)を参照してください。

### <a name="use-x509-with-hardware"></a>X **.** 509 をハードウェアで使用する場合

プロビジョニング サービスは、他のハードウェア上の X **.** 509 と共に使用することができます。 ハードウェアと SDK との間には、接続を確立するためのインターフェイスが必要となります。 このインターフェイスについては、ご利用の HSM の製造元にお問い合わせください。

### <a name="use-tpm"></a>TPM を使用する場合

プロビジョニング サービスは、Windows および Linux のハードウェア TPM チップに対し、SAS トークンを使って接続することができます。 TPM 認証を有効にするには、次のコマンドを実行します。

```
cmake -Ddps_auth_type=tpm ..
```

### <a name="use-tpm-with-simulator"></a>TPM をシミュレーターで使用する場合

TPM チップを搭載したデバイスがない場合は、Windows OS での開発を目的としてシミュレーターを使用できます。 TPM 認証を有効にして TPM シミュレーターを実行するには、次のコマンドを実行します。

```
cmake -Ddps_auth_type=tpm_simulator ..
```

## <a name="build-the-sdk"></a>SDK のビルド
デバイスの登録を行う前に SDK をビルドします。

### <a name="linux"></a>Linux
- Linux で SDK をビルドするには:
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake ..
    cmake --build .  # append '-- -j <n>' to run <n> jobs in parallel
    ```
- デバッグ バイナリをビルドするには、対応する CMake オプションをプロジェクト生成コマンドに追加します。その例を次に示します。
    ```
    cmake -DCMAKE_BUILD_TYPE=Debug ..
    ```

- SDK のビルドに利用できる [CMake 構成オプション](https://cmake.org/cmake/help/v3.6/manual/cmake.1.html)が数多く用意されています。 たとえば、CMake プロジェクト生成コマンドに次の引数を追加して、いずれかの使用可能なプロトコル スタックを無効にできます。
    ```
    cmake -Duse_amqp=OFF ..
    ```
- また、単体テストをビルドして実行することもできます。
    ```
    cmake -Drun_unittests=ON ..
    cmake --build .
    ctest -C "debug" -V
    ```

### <a name="windows"></a> Windows
- Windows で SDK をビルドするには、次の手順に従ってプロジェクト ファイルを生成します。
  - "開発者コマンド プロンプト for VS2015" を開きます。
  - リポジトリのルートで次の CMake コマンドを実行します。
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake -G "Visual Studio 14 2015" ..
    ```
    このコマンドでは x86 ライブラリがビルドされます。 x64 用にビルドするために、cmake ジェネレーターの引数を変更します。 
    ```
    cmake .. -G "Visual Studio 14 2015 Win64"
    ```

- プロジェクトの生成が正常に完了した場合、`cmake` フォルダーに Visual Studio ソリューション ファイル (.sln) が表示されます。 SDK をビルドするには:
    - Visual Studio で **cmake\azure_iot_sdks.sln** を開いてビルドするか、**または**
    - プロジェクト ファイルの生成に使用したコマンド プロンプトで次のコマンドを実行します。
      ```
      cmake --build . -- /m /p:Configuration=Release
      ```
- デバッグ バイナリをビルドするには、対応する MSBuild 引数を使用します。 
    ```
    cmake --build . -- /m /p:Configuration=Debug`
    ```
- SDK のビルドに利用できる CMake 構成オプションが数多く用意されています。 たとえば、CMake プロジェクト生成コマンドに次の引数を追加して、いずれかの使用可能なプロトコル スタックを無効にできます。
    ```
    cmake -G "Visual Studio 14 2015" -Duse_amqp=OFF ..
    ```
- また、単体テストをビルドして実行することもできます。
    ```
    cmake -G "Visual Studio 14 2015" -Drun_unittests=ON ..
    cmake --build . -- /m /p:Configuration=Debug
    ctest -C "debug" -V
    ```
  
### <a name="libraries-to-include"></a>追加するライブラリ
- 次のライブラリを SDK に追加する必要があります。
    - プロビジョニング サービス: dps_http_transport、dps_client、dps_security_client
    - IoTHub セキュリティ: iothub_security_client

## <a name="create-a-device-enrollment-entry-in-device-provisioning-services"></a>Device Provisioning Service でのデバイス登録エントリの作成

### <a name="tpm"></a>TPM
TPM を使用する場合は、「[シミュレートされたデバイスを作成して IoT Hub Device Provisioning Service でプロビジョニングする](./quick-create-simulated-device.md)」の手順に従って、Device Provisioning Service にデバイス登録エントリを作成し、初回ブートをシミュレートします。

### <a name="x509"></a>X **.** 509

1. プロビジョニング サービスにデバイスを登録するためには、各デバイスの保証キーと登録 ID を書き留めておく必要があります。これらの情報は、クライアント SDK に含まれるプロビジョニング ツールに表示されます。 次のコマンドを実行して、ルート CA 証明書 (グループ登録用) とリーフ証明書 (個別登録用) を出力してください。
      ```
      ./azure-iot-sdk-c/dps_client/tools/x509_device_provision/x509_device_provision.exe
      ```
2. Azure Portal にサインインし、左側のメニューの **[すべてのリソース]** をクリックして、Device Provisioning Service を開きます。
   - X **.** 509 個別登録: プロビジョニング サービスの概要ブレードで **[Manage enrollments]\(登録の管理\)** を選択します。 **[Individual Enrollments]\(個々の登録\)** タブの上部にある **[追加]** ボタンをクリックします。 ID 構成証明の "*メカニズム*" として **X**.**509** を選択し、ブレードの指示に従ってリーフ証明書をアップロードします。 作業が完了したら、**[保存]** をクリックします。 
   - X **.** 509 グループ登録: プロビジョニング サービスの概要ブレードで **[Manage enrollments]\(登録の管理\)** を選択します。 **[Group Enrollments]\(グループ登録\)** タブを選択して、上部の **[追加]** ボタンをクリックします。 ID 構成証明の "*メカニズム*" として **X**.**509** を選択し、グループ名と証明書名を入力してから、ブレードの指示に従って CA/中間証明機関の証明書をアップロードします。 作業が完了したら、**[保存]** をクリックします。 

## <a name="enable-authentication-for-devices-using-a-custom-attestation-mechanism-optional"></a>カスタム構成証明メカニズムを使用してデバイスの認証を有効にする (省略可)

> [!NOTE]
> このセクションの内容は、C 言語用 Device Provisioning Service クライアント SDK で現在サポート対象外となっているカスタム プラットフォーム (構成証明メカニズム) のサポートを必要とするデバイスのみを対象としています。また SDK では、"HSM" という用語が、しばしば "構成証明メカニズム" の一般的な代用語として使われていることにも注意してください。

まず、次のようにしてカスタム構成証明メカニズムに使用するリポジトリとライブラリを開発する必要があります。

1. 構成証明メカニズムにアクセスするためのライブラリを開発します。 このプロジェクトでは、Device Provisioning SDK で使用するスタティック ライブラリを生成する必要があります。

2. ライブラリでは、次のヘッダー ファイルで定義されている関数を実装する必要があります。 

    - カスタム TPM の場合: [HSM TPM API](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-tpm-api) に定義されている関数を実装します。  
    - カスタム X.509 の場合: [HSM X.509 API](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-x509-api) に定義されている関数を実装します。 

ライブラリ単独でのビルドに成功したら、それを Device Provisioning Service クライアント SDK に統合するために、ライブラリへのリンクを行う必要があります。 :

1. 次の `cmake` コマンドで、カスタム GitHub リポジトリとライブラリを指定します。
    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=<path_and_name_of_library> <PATH_TO_AZURE_IOT_SDK>
    ```
   
2. CMake でビルドされた Visual Studio ソリューション ファイル (`\azure-iot-sdk-c\cmake\azure_iot_sdks.sln`) を開いてビルドします。 

    - ビルド プロセスにより、SDK ライブラリがコンパイルされます。
    - SDK は、`cmake` コマンドで定義されたカスタム ライブラリへのリンクを試みます。

3. カスタム構成証明メカニズムが正しく実装されているかどうかを確認するには、"Provision_Samples" の "prov_dev_client_ll_sample" サンプル アプリ (`\azure-iot-sdk-c\cmake\provisioning_client\samples\prov_dev_client_ll_sample`) を実行します。

## <a name="connecting-to-iot-hub-after-provisioning"></a>プロビジョニング後の IoT Hub への接続

プロビジョニング サービスを使ってデバイスがプロビジョニングされた後は、この API で IoT Hub に接続する際、指定した認証モード (X **.** 509 または TPM) が使用されます。 
  ```
  IOTHUB_CLIENT_LL_HANDLE handle = IoTHubClient_LL_CreateFromDeviceAuth(iothub_uri, device_id, iothub_transport);
  ```
