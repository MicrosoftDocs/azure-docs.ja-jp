---
title: "Azure - Device Provisioning Service クライアント SDK で各種 HSM を使用する方法 | Microsoft Docs"
description: "Azure - Device Provisioning Service クライアント SDK で各種 HSM を物理デバイスおよびシミュレーターと共に使用する方法"
services: iot-dps
keywords: 
author: yzhong94
ms.author: yizhon
ms.date: 08/28/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: 
ms.devlang: na
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: a5649ab873993d20803cb01a4b0ecc278c3ce16c
ms.contentlocale: ja-jp
ms.lasthandoff: 09/14/2017

---

# <a name="how-to-use-different-hardware-security-modules-with-device-provisioning-service-client-sdk"></a>Device Provisioning Service クライアント SDK で各種ハードウェア セキュリティ モジュールを使用する方法
以下の手順では、C 言語で Device Provisioning Service クライアント SDK を利用し、物理デバイスおよびシミュレーターと共に各種の[ハードウェア セキュリティ モジュール (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) を使用する方法について説明しています。  このプロビジョニング サービスでは、X**.**509 とトラステッド プラットフォーム モジュール (TPM) という 2 つの認証モードがサポートされています。

## <a name="prerequisites"></a>前提条件

[シミュレートされたデバイスの作成とプロビジョニング](./quick-create-simulated-device.md)に関するガイドの「開発環境の準備」セクションに従って開発環境を準備してください。

## <a name="enable-authentication-with-different-hsms"></a>各種 HSM を使った認証の有効化

Azure Portal で物理デバイスまたはシミュレーターを登録するためには、その物理デバイスまたはシミュレーターに使用する認証モード (X**.**509 または TPM) を有効にしておく必要があります。  azure-iot-sdk-c のルート フォルダーに移動します。  選択した認証モードに応じて、指定のコマンドを実行してください。

### <a name="use-x509-with-simulator"></a>X**.**509 をシミュレーターで使用する場合

プロビジョニング サービスには、デバイスの認証用の X**.**509 証明書を生成する Device Identity Composition Engine (DICE) エミュレーターが付属しています。  X**.**509 認証を有効にするには、次のコマンドを実行します。

```
cmake -Ddps_auth_type=x509 ..
```

DICE 対応ハードウェアについては、[こちら](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/)を参照してください。

### <a name="use-x509-with-hardware"></a>X**.**509 をハードウェアで使用する場合

プロビジョニング サービスは、他のハードウェア上の X**.**509 と共に使用することができます。  ハードウェアと SDK との間には、接続を確立するためのインターフェイスが必要となります。  このインターフェイスについては、ご利用の HSM の製造元にお問い合わせください。

### <a name="use-tpm"></a>TPM を使用する場合

プロビジョニング サービスは、Windows および Linux のハードウェア TPM チップに対し、SAS トークンを使って接続することができます。  TPM 認証を有効にするには、次のコマンドを実行します。

```
cmake -Ddps_auth_type=tpm ..
```

### <a name="use-tpm-with-simulator"></a>TPM をシミュレーターで使用する場合

TPM チップを搭載したデバイスがない場合は、Windows OS での開発を目的としてシミュレーターを使用できます。  TPM 認証を有効にして TPM シミュレーターを実行するには、次のコマンドを実行します。

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

### <a name="windows"></a>Windows
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

## <a name="create-a-device-enrollment-entry-in-dps"></a>DPS におけるデバイス登録エントリの作成

### <a name="tpm"></a>TPM
TPM を使用する場合は、「[シミュレートされたデバイスを作成して IoT Hub Device Provisioning Service でプロビジョニングする](./quick-create-simulated-device.md)」の手順に従って、DPS にデバイス登録エントリを作成し、初回ブートをシミュレートします。

### <a name="x509"></a>X**.**509
1. プロビジョニング サービスにデバイスを登録するためには、各デバイスの保証キーと登録 ID を書き留めておく必要があります。これらの情報は、クライアント SDK に含まれるプロビジョニング ツールに表示されます。 次のコマンドを実行して、ルート CA 証明書 (グループ登録用) と署名者証明書 (個別登録用) を出力してください。
      ```
      ./azure-iot-sdk-c/dps_client/tools/x509_device_provision/x509_device_provision.exe
      ```
2. Azure Portal にログインし、左側のメニューの **[すべてのリソース]** ボタンをクリックして、DPS サービスを開きます。
   - X**.**509 個別登録: プロビジョニング サービスの概要ブレードで **[Manage enrollments]\(登録の管理\)** を選択します。 **[Individual Enrollments]\(個々の登録\)** タブの上部にある **[追加]** ボタンをクリックします。 ID 構成証明の "*メカニズム*" として **X**.**509** を選択し、ブレードの指示に従って署名者証明書をアップロードします。 作業が完了したら、**[保存]** をクリックします。 
   - X**.**509 グループ登録: プロビジョニング サービスの概要ブレードで **[Manage enrollments]\(登録の管理\)** を選択します。 **[Group Enrollments]\(グループ登録\)** タブを選択して、上部の **[追加]** ボタンをクリックします。 ID 構成証明の "*メカニズム*" として **X**.**509** を選択し、グループ名と証明書名を入力してから、ブレードの指示に従ってルート CA 証明書をアップロードします。 作業が完了したら、**[保存]** をクリックします。 

## <a name="connecting-to-iot-hub-after-provisioning"></a>プロビジョニング後の IoT Hub への接続

プロビジョニング サービスを使ってデバイスがプロビジョニングされた後は、IoT Hub に接続する際にこの API で HSM 認証モードが使用されます。 
  ```
  IOTHUB_CLIENT_LL_HANDLE handle = IoTHubClient_LL_CreateFromDeviceAuth(iothub_uri, device_id, iothub_transport);
  ```

