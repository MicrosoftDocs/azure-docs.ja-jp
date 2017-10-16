---
title: "Azure IoT Hub Device Provisioning Service 用にデバイスを設定する | Microsoft Docs"
description: "デバイスの製造プロセス中に、IoT Hub Device Provisioning Service を使用してプロビジョニングするデバイスを設定します。"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: c99279413b50e7bf1e6058a4151890e3a8f83892
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Azure IoT Hub Device Provisioning Service を使用してプロビジョニングするデバイスの設定

前のチュートリアルでは、デバイスを IoT ハブに自動的にプロビジョニングするために、Azure IoT Hub Device Provisioning Service を設定する方法を説明しました。 このチュートリアルでは、[ハードウェア セキュリティ モジュール (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security) に基づいてデバイスの Device Provisioning Service を構成し、デバイスが初回起動時に Device Provisioning Service に接続できるように、製造プロセス中にデバイスを設定するためのガイダンスを示します。 このチュートリアルでは、次のプロセスについて説明します。

> [!div class="checklist"]
> * ハードウェア セキュリティ モジュールを選択する
> * 選択した HSM の Device Provisioning クライアント SDK をビルドする
> * セキュリティ アーティファクトを抽出する
> * デバイスで Device Provisioning Service の構成を設定する

## <a name="prerequisites"></a>前提条件

以降の手順に進む前に、[デバイス プロビジョニングのためのクラウドの設定](./tutorial-set-up-cloud.md)に関するチュートリアルに記載されている指示に従って、Device Provisioning Service インスタンスと IoT ハブを作成してください。


## <a name="select-a-hardware-security-module"></a>ハードウェア セキュリティ モジュールを選択する

[Device Provisioning Service クライアント SDK](https://github.com/Azure/azure-iot-sdk-c/tree/master/dps_client) は、次の 2 種類のハードウェア セキュリティ モジュール (HSM) をサポートしています。 

- [トラステッド プラットフォーム モジュール (TPM)](https://en.wikipedia.org/wiki/Trusted_Platform_Module)
    - TPM は、ほとんどの Windows ベースのデバイス プラットフォームと一部の Linux/Ubuntu ベースのデバイス向けの代表的な標準です。 デバイスの製造元は、製造するデバイスでこれらの OS のいずれかが稼働している場合や、代表的な HSM 標準を求めている場合に、この HSM を選択できます。 TPM チップを使用すると、各デバイスの Device Provisioning Service への個別登録のみが可能です。 開発向けの場合、Windows または Linux の開発マシンで TPM シミュレーターを使用できます。

- [X.509](https://cryptography.io/en/latest/x509/) ベースのハードウェア セキュリティ モジュール 
    - X.509 ベースの HSM は比較的新しいチップであり、X.509 証明書を実装する RIoT チップまたは DICE チップ上での作業が Microsoft 内で現在進められています。 X.509 チップを使用すると、ポータルで一括登録を実行できます。 また、X.509 チップは、embedOS などの Windows 以外の特定の OS もサポートしています。 開発のために、Device Provisioning Service クライアント SDK は、X.509 デバイス シミュレーターをサポートしています。 

デバイスの製造元は、上記のいずれかの種類に基づくハードウェア セキュリティ モジュール/チップを選択する必要があります。 他の種類の HSM は、Device Provisioning Service クライアント SDK では現在サポートされていません。   


## <a name="build-device-provisioning-client-sdk-for-the-selected-hsm"></a>選択した HSM の Device Provisioning クライアント SDK をビルドする

Device Provisioning Service クライアント SDK は、選択したセキュリティ メカニズムをソフトウェアに実装する際に役立ちます。 次の手順は、選択した HSM チップで SDK を使用する方法を示しています。

1. [シミュレートされたデバイスを作成するためのクイック スタート](./quick-create-simulated-device.md)の手順に従った場合は、SDK をビルドする準備ができています。 そうでない場合は、「[開発環境の準備](./quick-create-simulated-device.md#setupdevbox)」の最初の 4 つの手順に従います。 これらの手順では、Device Provisioning Service クライアント SDK の GitHub リポジトリを複製し、`cmake` ビルド ツールをインストールします。 

1. コマンド プロンプトで次のいずれかのコマンドを使用して、デバイス用に選択した HSM の種類に対応する SDK をビルドします。
    - TPM デバイスの場合:
        ```cmd/sh
        cmake -Ddps_auth_type=tpm ..
        ```

    - TPM シミュレーターの場合:
        ```cmd/sh
        cmake -Ddps_auth_type=tpm_simulator ..
        ```

    - X.509 デバイスおよびシミュレーターの場合:
        ```cmd/sh
        cmake -Ddps_auth_type=x509 ..
        ```

1. SDK では、Windows または Ubuntu 実装を実行している TPM および X.509 HSM デバイスを既定でサポートしています。 これらのサポートされている HSM の場合は、「[セキュリティ アーティファクトを抽出する](#extractsecurity)」に進んでください。 
 
## <a name="support-custom-tpm-and-x509-devices"></a>カスタムの TPM および X.509 デバイスをサポートする

Device Provisioning Service クライアント SDK では、Windows または Ubuntu を実行していない TPM および X.509 デバイスは既定でサポートされていません。 このようなデバイスの場合、次の手順に示すように、特定の HSM チップのカスタム コードを記述する必要があります。

### <a name="develop-your-custom-repository"></a>カスタム リポジトリを作成する

1. HSM にアクセスするための GitHub リポジトリを作成します。 このプロジェクトでは、Device Provisioning SDK で使用するスタティック ライブラリを生成する必要があります。
1. ライブラリでは、次のヘッダー ファイルで定義されている関数を実装する必要があります。a.  カスタム TPM の場合、`\azure-iot-sdk-c\dps_client\adapters\custom_hsm_tpm_impl.h` で定義されている関数を実装します。
    b. カスタム X.509 の場合、`\azure-iot-sdk-c\dps_client\adapters\custom_hsm_x509_impl.h` で定義されている関数を実装します。 
1. HSM リポジトリには、ビルドするリポジトリのルートに `CMakeLists.txt` も含まれている必要があります。

### <a name="integrate-with-the-device-provisioning-service-client"></a>Device Provisioning Service クライアントと統合する

ライブラリが単独で正常にビルドされたら、IoThub C-SDK に移行し、リポジトリをプルできます。

1. 次の cmake コマンドで、カスタム HSM GitHub リポジトリ、ライブラリのパス、ライブラリの名前を指定します。
    ```cmd/sh
    cmake -Ddps_auth_type=<custom_hsm> -Ddps_hsm_custom_repo=<github_repo_name> -Ddps_hsm_custom_lib=<path_and_name_of library> <PATH_TO_AZURE_IOT_SDK>
    ```
   このコマンドの `<custom_hsm>` を、`tpm` または `x509` に置き換えます。 このコマンドにより、`cmake` ディレクトリ内にカスタム HSM リポジトリのマーカーが作成されます。 カスタム HSM は、引き続き TPM または X.509 セキュリティ メカニズムに基づいている必要があります。

1. Visual Studio で SDK を開いてビルドします。 

    - ビルド プロセスでは、カスタム リポジトリが複製され、ライブラリがビルドされます。
    - SDK は、cmake コマンドで定義されたカスタム HSM へのリンクを試みます。

1. `\azure-iot-sdk-c\dps_client\samples\dps_client_sample\dps_client_sample.c` サンプルを実行して、HSM が正しく実装されているかどうかを確認します。

<a id="extractsecurity"></a>
## <a name="extract-the-security-artifacts"></a>セキュリティ アーティファクトを抽出する

次に、デバイスで HSM のセキュリティ アーティファクトを抽出します。

1. TPM デバイスの場合、デバイスに関連付けられている、TPM チップの製造元の**保証キー**を調べる必要があります。 保証キーをハッシュすることによって、TPM デバイスの一意の**登録 ID** を派生させることができます。 
2. X.509 デバイスの場合、デバイスに発行された証明書 (デバイスの個別登録の場合はエンド エンティティ証明書、デバイスのグループ登録の場合はルート証明書) を取得する必要があります。

これらのセキュリティ アーティファクトは、デバイスを Device Provisioning Service に登録する際に必要となります。 プロビジョニング サービスは、これらのデバイスのいずれかが起動し、後でサービスに接続するまで待機します。 これらのセキュリティ アーティファクトを使用して登録を作成する方法については、[デバイス登録を管理する方法](how-to-manage-enrollments.md)に関する記事をご覧ください。 

デバイスの初回起動時に、クライアント SDK はチップと対話してデバイスからセキュリティ アーティファクトを抽出し、Device Provisioning Service への登録を確認します。 


## <a name="set-up-the-device-provisioning-service-configuration-on-the-device"></a>デバイスで Device Provisioning Service の構成を設定する

デバイス製造プロセスでの最後の手順では、Device Provisioning Service クライアント SDK を使用してデバイスをサービスに登録するアプリケーションを作成します。 この SDK は、アプリケーションが使用する次の API を提供します。

```C
typedef void(*DPS_REGISTER_DEVICE_CALLBACK)(DPS_RESULT register_result, const char* iothub_uri, const char* device_id, void* user_context); // Callback to notify user of device registration results.
DPS_CLIENT_LL_HANDLE DPS_Client_LL_Create (const char* dps_uri, const char* scope_id, DPS_TRANSPORT_PROVIDER_FUNCTION protocol, DPS_CLIENT_ON_ERROR_CALLBACK on_error_callback, void* user_ctx); // Creates the IOTHUB_DPS_LL_HANDLE to be used in subsequent calls.
void DPS_Client_LL_Destroy(DPS_CLIENT_LL_HANDLE handle); // Frees any resources created by the IoTHub Device Provisioning Service module.
DPS_RESULT DPS_LL_Register_Device(DPS_LL_HANDLE handle, DPS_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, DPS_CLIENT_REGISTER_STATUS_CALLBACK status_cb, void* status_ctx); // Registers a device that has been previously registered with Device Provisioning Service
void DPS_Client_LL_DoWork(DPS_LL_HANDLE handle); // Processes the communications with the Device Provisioning Service and calls any user callbacks that are required.
```

[こちらのクイック スタートの「デバイスの初回ブート シーケンスをシミュレートする」](./quick-create-simulated-device.md#firstbootsequence)に記載されているように、変数 `dps_uri` と `dps_scope_id` は使用前に必ず初期化してください。 Device Provisioning Service クライアント登録 API の `DPS_Client_LL_Create` は、グローバルな Device Provisioning Service に接続します。 サービスによって "*ID スコープ*" が生成され、一意性が保証されます。 ID スコープは不変であり、登録 ID を一意に識別するために使用されます。 `iothub_uri` により、IoT Hub クライアント登録 API の `IoTHubClient_LL_CreateFromDeviceAuth` は適切な IoT ハブに接続できます。 


これらの API を使用することで、デバイスは起動時に Device Provisioning Service に接続してサービスに登録し、IoT ハブの情報を取得してハブに接続できます。 `dps_client/samples/dps_client_sample/dps_client_sample.c` ファイルに、これらの API の使用方法が示されています。 一般に、クライアント登録のための次のフレームワークを作成する必要があります。

```C
static const char* dps_uri = "global.azure-devices-provisioning.net";
static const char* dps_scope_id = "[ID scope for your provisioning service]";
...
static void register_callback(DPS_RESULT register_result, const char* iothub_uri, const char* device_id, void* context)
{
    USER_DEFINED_INFO* user_info = (USER_DEFINED_INFO *)user_context;
    ...
    user_info. reg_complete = 1;
}
static void registation_status(DPS_REGISTRATION_STATUS reg_status, void* user_context)
{
}
int main()
{
    ...    
    security_device_init(); // initialize your HSM 

    DPS_CLIENT_LL_HANDLE handle = DPS_Client_LL_Create(dps_uri, dps_scope_id, dps_transport, on_dps_error_callback, &user_info); // Create your DPS client

    if (DPS_Client_LL_Register_Device(handle, register_callback, &user_info, register_status, &user_info) == IOTHUB_DPS_OK) {
        do {
            // The dps_register_callback is called when registration is complete or fails
            DPS_Client_LL_DoWork(handle);
        } while (user_info.reg_complete == 0);
    }
    DPS_Client_LL_Destroy(handle); // Clean up the DPS client
    ...
    iothub_client = IoTHubClient_LL_CreateFromDeviceAuth(user_info.iothub_uri, user_info.device_id, transport); // Create your IoT hub client and connect to your hub
    ...
}
```

最初はシミュレートされたデバイスを使用し、テスト用のサービス セットアップを使用して、Device Provisioning Service クライアント登録アプリケーションを改良できます。 テスト環境でアプリケーションが動作したら、特定のデバイス用にアプリケーションをビルドし、実行可能ファイルをデバイス イメージにコピーします。 デバイスはまだ起動しないでください。デバイスを起動する前に、[デバイスを Device Provisioning Service に登録する](./tutorial-provision-device-to-hub.md#enrolldevice)必要があります。 このプロセスについては、下記の「次のステップ」をご覧ください。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この時点で、ポータルで Device Provisioning Service と IoT Hub サービスの設定が完了していると考えられます。 デバイス プロビジョニングの設定を破棄したり、これらのサービスの使用を遅らせる場合は、不要なコストが発生しないようにサービスをシャットダウンすることをお勧めします。

1. Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックし、Device Provisioning Service を選択します。 **[すべてのリソース]** ブレードの上部にある **[削除]** をクリックします。  
1. Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックし、IoT ハブを選択します。 **[すべてのリソース]** ブレードの上部にある **[削除]** をクリックします。  


## <a name="next-steps"></a>次のステップ
このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * ハードウェア セキュリティ モジュールを選択する
> * 選択した HSM の Device Provisioning クライアント SDK をビルドする
> * セキュリティ アーティファクトを抽出する
> * デバイスで Device Provisioning Service の構成を設定する

次のチュートリアルに進み、自動プロビジョニングのために Azure IoT Hub Device Provisioning Service にデバイスを登録して、IoT ハブにデバイスをプロビジョニングする方法を学習してください。

> [!div class="nextstepaction"]
> [IoT ハブにデバイスをプロビジョニングする](tutorial-provision-device-to-hub.md)

