---
title: チュートリアル - Azure IoT Hub Device Provisioning Service 用にデバイスをセットアップする
description: このチュートリアルでは、デバイスの製造プロセス中に、IoT Hub Device Provisioning Service (DPS) を使用してプロビジョニングするデバイスを設定する方法について説明します。
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 337ac2f60809370e6a07b2b0403d21ef7230b034
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976708"
---
# <a name="tutorial-set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>チュートリアル:Azure IoT Hub Device Provisioning Service を使用してプロビジョニングするデバイスの設定

前のチュートリアルでは、デバイスを IoT ハブに自動的にプロビジョニングするために、Azure IoT Hub Device Provisioning Service を設定する方法を説明しました。 このチュートリアルでは、製造プロセス中にデバイスをセットアップし、IoT Hub で自動プロビジョニングされるようにする方法について説明します。 デバイスは、初めて起動してプロビジョニング サービスに接続するときに、[構成証明メカニズム](concepts-device.md#attestation-mechanism)に基づいてプロビジョニングされます。 このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * プラットフォーム固有の Device Provisioning Services Client SDK を構築する
> * セキュリティ アーティファクトを抽出する
> * デバイス登録ソフトウェアを作成する

このチュートリアルは、前の「[クラウド リソースを設定する](tutorial-set-up-cloud.md)」チュートリアルの指示に従って、Device Provisioning Service インスタンスと IoT ハブが既に作成されていることを前提としています。

このチュートリアルでは、[Azure IoT SDKs and libraries for C repository (C 用の Azure IoT SDK とライブラリのリポジトリ)](https://github.com/Azure/azure-iot-sdk-c) を使用します。これには、Device Provisioning Service Client SDK for C が含まれています。この SDK は、現在、Windows または Ubuntu 実装上で実行されているデバイスに対して TPM および X.509 サポートを提供しています。 このチュートリアルは、Windows 開発クライアントの使用をベースとしています。また、Visual Studio に関する基本的な知識を前提としています。 

自動プロビジョニングの処理に慣れていない場合は、「[自動プロビジョニングの概念](concepts-auto-provisioning.md)」を読んでから先に進んでください。 


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* ["C++ によるデスクトップ開発"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) ワークロードが有効になった [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 以降。
* [Git](https://git-scm.com/download/) の最新バージョンがインストールされている。



## <a name="build-a-platform-specific-version-of-the-sdk"></a>SDK のプラットフォーム固有のバージョンを構築する

Device Provisioning Service Client SDK は、デバイス登録ソフトウェアを実装するために役立ちます。 しかし、使用する前に、開発クライアント プラットフォームと構成証明メカニズムに固有の SDK のバージョンを構築する必要があります。 このチュートリアルでは、Windows 開発プラットフォーム上で Visual Studio を使用する、以下のサポートされている種類の構成証明用の SDK を構築します。

1. [CMake ビルド システム](https://cmake.org/download/)をダウンロードします。

    `CMake` のインストールを開始する**前に**、Visual Studio の前提条件 (Visual Studio と "C++ によるデスクトップ開発" ワークロード) が マシンにインストールされていることが重要です。 前提条件を満たし、ダウンロードを検証したら、CMake ビルド システムをインストールします。

1. コマンド プロンプトまたは Git Bash シェルを開きます。 次のコマンドを実行して、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) の GitHub リポジトリを複製します。
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    この操作は、完了するまでに数分かかります。


1. git リポジトリのルート ディレクトリに `cmake` サブディレクトリを作成し、そのフォルダーに移動します。 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. 使用する構成証明メカニズムに基づいて、開発プラットフォーム用 SDK を構築します。 次のいずれかのコマンドを使用します (各コマンドの末尾に 2 つのピリオドがあることにも注意してください)。 完了すると、CMake は次のようなデバイス固有の内容を持つ `/cmake` サブディレクトリを作成します。
 
    - 構成証明のために TPM シミュレーターを使用するデバイスの場合:

        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
        ```

    - 他のすべてのデバイスの場合 (物理 TPM/HSM/X.509、またはシミュレートされた X.509 証明書):

        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```


これで、SDK を使用して、デバイス登録コードをビルドする準備ができました。 
 
<a id="extractsecurity"></a> 

## <a name="extract-the-security-artifacts"></a>セキュリティ アーティファクトを抽出する 

次に、デバイスで使用される構成証明メカニズムのために、セキュリティ アーティファクトを抽出します。 

### <a name="physical-devices"></a>物理デバイス 

物理 TPM/HSM に構成証明を使用する SDK を構築したのか X.509 証明書を使用するのかに応じて、セキュリティ アーチファクトを収集する方法は次のとおりです。

- TPM デバイスの場合、デバイスに関連付けられている、TPM チップの製造元の**保証キー**を判断する必要があります。 保証キーをハッシュすることによって、TPM デバイスの一意の**登録 ID** を派生させることができます。  

- X.509 デバイスの場合、デバイスに発行された証明書を取得する必要があります。 プロビジョニング サービスでは、X.509 構成証明メカニズムを使用するデバイスのアクセスを制御する、次の 2 種類の登録エントリが公開されています。 必要な証明書は、使用する登録の種類によって異なります。

    1. 個々の登録: 特定の 1 つのデバイスの登録。 この種類の登録エントリには、[エンド エンティティ、"リーフ"、証明書](concepts-security.md#end-entity-leaf-certificate)が必要です。
    1. 登録グループ: この種類の登録エントリには、中間証明書またはルート証明書が必要です。 詳細については、「[X.509 証明書を使用してプロビジョニング サービスへのデバイスのアクセスを制御する](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates)」を参照してください。

### <a name="simulated-devices"></a>シミュレートされたデバイス

TPM を使用するシミュレートされたデバイスのために構成証明を使用する SDK を構築したのか、X.509 かに応じて、セキュリティ アーチファクトを収集する方法は次のとおりです。

- シミュレートされた TPM デバイスの場合:

   1. Windows コマンド プロンプトを開き、`azure-iot-sdk-c` サブディレクトリに移動して、TPM シミュレーターを実行します。 これは、ソケットでポート 2321 とポート 2322 をリッスンします。 このコマンド ウィンドウを閉じないでください。この次のクイック スタートの終了まで、このシミュレーターを実行状態にしておく必要があります。 

      `azure-iot-sdk-c` サブディレクトリで次のコマンドを実行して、シミュレーターを起動します。

      ```cmd/sh
      .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
      ```

      > [!NOTE]
      > この手順に Git Bash コマンド プロンプトを使用する場合は、バックスラッシュをスラッシュに変更する必要があります。たとえば、`./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe` のようにします。

   1. Visual Studio を使用して、*cmake* フォルダーに生成された `azure_iot_sdks.sln` という名前のソリューションを開き、[ビルド] メニューの [ソリューションのビルド] コマンドを使用してビルドします。

   1. Visual Studio の "*ソリューション エクスプローラー*" ウィンドウで、**Provision\_Tools** フォルダーに移動します。 **tpm_device_provision** プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。 

   1. [デバッグ] メニューのいずれかの [開始] コマンドを使用してソリューションを実行します。 出力ウィンドウに、TPM シミュレーターの " **_登録 ID_** " と " **_保証キー_** " が表示されます。これらは、デバイスの加入と登録に必要です。 後で使用するために、これらの値をコピーしておきます。 このウィンドウ (登録 ID と保証キーのウィンドウ) は閉じてもかまいませんが、手順 1. で開始した TPM シミュレーター ウィンドウは実行したままにしておきます。

- シミュレートされた X.509 デバイスの場合:

  1. Visual Studio を使用して、*cmake* フォルダーに生成された `azure_iot_sdks.sln` という名前のソリューションを開き、[ビルド] メニューの [ソリューションのビルド] コマンドを使用してビルドします。

  1. Visual Studio の "*ソリューション エクスプローラー*" ウィンドウで、**Provision\_Tools** フォルダーに移動します。 **[dice\_device\_enrollment]** プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。 
  
  1. [デバッグ] メニューのいずれかの [開始] コマンドを使用してソリューションを実行します。 出力ウィンドウで、確認を求められたら個々の登録を行うための「**i**」を入力します。 シミュレートされたデバイスについて、ローカルで生成された X.509 証明書が出力ウィンドウに表示されます。 出力内容の *-----BEGIN CERTIFICATE-----* から最初の *-----END CERTIFICATE-----* までをクリップボードにコピーします。この両方の行を確実に含めるようにしてください。 必要なのは出力ウィンドウの最初の証明書のみです。
 
  1. **_X509testcert.pem_** という名前のファイルを作成して任意のテキスト エディターで開き、クリップボードの内容をこのファイルにコピーします。 後でデバイスの加入に使用するため、ファイルを保存しておきます。 登録ソフトウェアを実行すると、自動プロビジョニング中に、同じ証明書が使用されます。    

これらのセキュリティ アーティファクトは、デバイスを Device Provisioning Service に加入させる際に必要となります。 プロビジョニング サービスは、デバイスが起動し、後でこのサービスに接続してくるまで待機します。 デバイスの初回起動時に、クライアント SDK ロジックはチップ (またはシミュレーター) と対話してデバイスからセキュリティ アーティファクトを抽出し、Device Provisioning Service への登録を確認します。 

## <a name="create-the-device-registration-software"></a>デバイス登録ソフトウェアを作成する

手順の最後に、Device Provisioning Service Client SDK を使用してデバイスを IoT Hub サービスに登録する登録アプリケーションを記述します。 

> [!NOTE]
> この手順では、シミュレートされたデバイスの使用を想定して、ワークステーションから SDK サンプル登録アプリケーションを実行します。 ただし、物理デバイスにデプロイするための登録アプリケーションを構築する場合も、同じ概念が適用されます。 

1. Azure Portal で、Device Provisioning サービスの **[概要]** ブレードを選択し、**[_ID スコープ_]** の値をコピーします。 サービスによって "*ID スコープ*" が生成され、一意性が保証されます。 ID スコープは不変であり、登録 ID を一意に識別するために使用されます。

    ![ポータルのブレードから Device Provisioning サービスのエンドポイント情報を抽出](./media/tutorial-set-up-device/extract-dps-endpoints.png) 

1. お使いのコンピューターの Visual Studio の "*ソリューション エクスプローラー*" で、**Provision\_Samples** フォルダーに移動します。 **prov\_dev\_client\_sample** という名前のサンプル プロジェクトを選択し、**prov\_dev\_client\_sample.c** ソース ファイルを開きます。

1. 手順 1. で取得した "_ID スコープ_" 値を `id_scope` 変数に割り当てます (`[` かっこと `]` かっこは削除します)。 

    ```c
    static const char* global_prov_uri = "global.azure-devices-provisioning.net";
    static const char* id_scope = "[ID Scope]";
    ```

    なお、`global_prov_uri` 変数は、IoT Hub クライアント登録 API `IoTHubClient_LL_CreateFromDeviceAuth` が、指定された Device Provisioning Service インスタンスに接続できるようにします。

1. 同じファイルの **main()** 関数で、デバイスの登録ソフトウェアによって使用されている構成証明メカニズム (TPM または X.509) に対応する `hsm_type` 変数をコメント/コメント解除します。 

    ```c
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

1. 変更を保存し、[ビルド] メニューの [ソリューションのビルド] を選択して、**prov\_dev\_client\_sample** サンプルを再ビルドします。 

1. **Provision\_Samples** フォルダーの **prov\_dev\_client\_sample** プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。 まだサンプル アプリケーションを実行しないでください。

> [!IMPORTANT]
> まだデバイスを実行/起動しないでください。 デバイスを起動する前に、デバイスを Device Provisioning Service に加入させて、プロセスを完了する必要があります。 後の「次の手順」セクションで、次の記事に移動することができます。

### <a name="sdk-apis-used-during-registration-for-reference-only"></a>登録中に使用される SDK API (参考情報)

SDK では、登録中にアプリケーションで使用できるように、以下の API が用意されています。 これらの API を使用することで、デバイスは起動時に Device Provisioning Service に接続して登録することができます。 代わりに、デバイスは IoT Hub インスタンスへの接続を確立するために必要な情報を受け取ります。

```C
// Creates a Provisioning Client for communications with the Device Provisioning Client Service.  
PROV_DEVICE_LL_HANDLE Prov_Device_LL_Create(const char* uri, const char* scope_id, PROV_DEVICE_TRANSPORT_PROVIDER_FUNCTION protocol)

// Disposes of resources allocated by the provisioning Client.
void Prov_Device_LL_Destroy(PROV_DEVICE_LL_HANDLE handle)

// Asynchronous call initiates the registration of a device.
PROV_DEVICE_RESULT Prov_Device_LL_Register_Device(PROV_DEVICE_LL_HANDLE handle, PROV_DEVICE_CLIENT_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, PROV_DEVICE_CLIENT_REGISTER_STATUS_CALLBACK reg_status_cb, void* status_user_ctext)

// Api to be called by user when work (registering device) can be done
void Prov_Device_LL_DoWork(PROV_DEVICE_LL_HANDLE handle)

// API sets a runtime option identified by parameter optionName to a value pointed to by value
PROV_DEVICE_RESULT Prov_Device_LL_SetOption(PROV_DEVICE_LL_HANDLE handle, const char* optionName, const void* value)
```

まずはシミュレートされたデバイスと、テスト用のサービス セットアップを使用することで、Device Provisioning Service クライアント登録アプリケーションの改良が必要であることがわかる場合もあります。 テスト環境でアプリケーションが動作したら、特定のデバイス用にアプリケーションをビルドし、実行可能ファイルをデバイス イメージにコピーします。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この時点で、Device Provisioning Service と IoT Hub サービスがポータルで実行されているでしょう。 デバイス プロビジョニングのセットアップを破棄したり、このチュートリアル シリーズの完了を遅らせる場合は、不要なコストが発生しないようにサービスをシャットダウンすることをお勧めします。

1. Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックし、Device Provisioning サービスを選択します。 **[すべてのリソース]** ブレードの上部にある **[削除]** をクリックします。  
1. Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックし、IoT ハブを選択します。 **[すべてのリソース]** ブレードの上部にある **[削除]** をクリックします。  

## <a name="next-steps"></a>次の手順
このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * プラットフォーム固有の Device Provisioning Service Client SDK を構築する
> * セキュリティ アーティファクトを抽出する
> * デバイス登録ソフトウェアを作成する

次のチュートリアルに進み、自動プロビジョニングのために Azure IoT Hub Device Provisioning Service にデバイスを登録して、IoT ハブにデバイスをプロビジョニングする方法を学習してください。

> [!div class="nextstepaction"]
> [IoT ハブにデバイスをプロビジョニングする](tutorial-provision-device-to-hub.md)
