---
title: Azure IoT Hub Device Provisioning Service でカスタム割り当てポリシーを使用する方法 | Microsoft Docs
description: Azure IoT Hub Device Provisioning Service でカスタム割り当てポリシーを使用する方法
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 1e672e7bd43dcd05d048d22205939749c1d96579
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348071"
---
# <a name="how-to-use-custom-allocation-policies"></a>カスタム割り当てポリシーの使用方法


カスタム割り当てポリシーを使用すると、デバイスを IoT ハブに割り当てる方法をより細かく制御できます。 これは、デバイスを IoT ハブに割り当てる際に、[Azure 関数](../azure-functions/functions-overview.md)でカスタム コードを使用することで実現されます。 Device Provisioning Service から、デバイスと登録に関するすべての関連情報を提供する Azure 関数コードを呼び出します。 関数コードが実行され、デバイスのプロビジョニングに使用する IoT ハブ情報が返されます。

Device Provisioning Service で提供されるポリシーがご自身のシナリオの要件を満たしていない場合は、カスタム割り当てポリシーを使用して独自の割り当てポリシーを定義します。

たとえば、プロビジョニングの際にデバイスで使用されている証明書を確認し、証明書のプロパティに基づいて IoT ハブにデバイスを割り当てる必要がある場合です。 また、デバイス用のデータベースに情報を格納していて、どの IoT ハブにデバイスを割り当てるかを決定するためにそのデータベースを照会する必要がある場合です。


この記事では、C# で記述された Azure 関数を使用して、カスタム割り当てポリシーについて説明します。 "*Contoso トースター部門*" と "*Contoso ヒート ポンプ部門*" を表す 2 つの新しい IoT ハブを作成します。 プロビジョニングを必要とするデバイスには、プロビジョニングの要求が受け入れられるようにするために、次のいずれかのサフィックスを持つ登録 ID が必要です。

- **-contoso-tstrsd-007**: Contoso トースター部門
- **-contoso-hpsd-088**: Contoso ヒート ポンプ部門

デバイスは、これらの登録 ID の必須サフィックスのいずれかに基づいてプロビジョニングされます。 これらのデバイスは、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) に含まれるプロビジョニング サンプルを使用してシミュレートされます。 

この記事では、以下の手順を実施します。

* Azure CLI を使用して 2 つの Contoso 部門 IoT ハブ (**Contoso トースター部門**と**Contoso ヒート ポンプ部門**) を作成する
* カスタム割り当てポリシー用に Azure 関数を使用して新しいグループ登録を作成する
* 2 つのデバイス シミュレーションのためのデバイス キーを作成する
* Azure IoT C SDK の開発環境をセットアップする
* デバイスをシミュレートして、デバイスがカスタム割り当てポリシーのコード例に従ってプロビジョニングされているかどうかを確認する


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* [Azure portal での IoT Hub Device Provisioning Service の設定](./quick-setup-auto-provision.md)に関するクイック スタートが完了していること。
* ["C++ によるデスクトップ開発"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) ワークロードが有効になった [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 以降。
* [Git](https://git-scm.com/download/) の最新バージョンがインストールされている。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-two-divisional-iot-hubs"></a>2 つの部門 IoT ハブを作成する

このセクションでは、Azure Cloud Shell を使用して、**Contoso トースター部門**と **Contoso ヒート ポンプ部門**を表す 2 つの新しい IoT ハブを作成します。

1. Azure Cloud Shell を使用して、[az group create](/cli/azure/group#az-group-create) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

    次の例では、*contoso-us-resource-group* という名前のリソース グループを *eastus* リージョンに作成します。 この記事で作成するすべてのリソースには、このグループを使用することをお勧めします。 こうすることで、終わった後のクリーンアップが簡単になります。

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Azure Cloud Shell を使用して、[az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) コマンドで **Contoso トースター部門** IoT ハブを作成します。 この IoT ハブは、*contoso-us-resource-group* に追加されます。

    次の例では、*contoso-toasters-hub-1098* という名前の IoT ハブを場所 *eastus* に作成します。 独自の一意のハブ名を使用する必要があります。 **1098** の代わりに、ハブ名内で独自のサフィックスを構成してください。 カスタム割り当てポリシーのコード例では、ハブ名内に `-toasters-` が必要です。

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    このコマンドが完了するまでに数分かかる場合があります。

3. Azure Cloud Shell を使用して、[az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) コマンドで **Contoso ヒート ポンプ部門** IoT ハブを作成します。 この IoT ハブも、*contoso-us-resource-group* に追加されます。

    次の例では、*contoso-heatpumps-hub-1098* という名前の IoT ハブを場所 *eastus* に作成します。 独自の一意のハブ名を使用する必要があります。 **1098** の代わりに、ハブ名内で独自のサフィックスを構成してください。 カスタム割り当てポリシーのコード例では、ハブ名に `-heatpumps-` が必要です。

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    このコマンドも、完了するまでに数分かかる場合があります。




## <a name="create-the-enrollment"></a>登録を作成する

このセクションでは、カスタム割り当てポリシーを使用する新しい登録グループを作成します。 わかりやすくするため、この記事では[対称キーの構成証明](concepts-symmetric-key-attestation.md)を登録で使用します。 ソリューションをさらに安全にするには、信頼チェーンで [X.509 証明書構成証明](concepts-security.md#x509-certificates)を使用することを検討してください。

1. [Azure portal](https://portal.azure.com) にサインインし、Device Provisioning Services のインスタンスを開きます。

2. **[登録を管理します]** タブを選択し、ページの上部にある **[登録グループの追加]** ボタンをクリックします。 

3. **[登録グループの追加]** で、次の情報を入力して、 **[保存]** ボタンをクリックします。

    **[グループ名]** : 「**contoso-custom-allocated-devices**」を入力します。

    **[構成証明の種類]** : **[対称キー]** を選択します。

    **[キーの自動生成]** : このチェック ボックスは既にオンになっているはずです。

    **[デバイスをハブに割り当てる方法を選択してください]** : **[カスタム (Azure 関数を使用)]** を選択します。

    ![対称キー構成証明用にカスタム割り当て登録グループを追加する](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)


4. **[登録グループの追加]** で、 **[Link a new IoT hub]\(新しい IoT ハブにリンクする\)** をクリックして新しい両方の部門 IoT ハブをリンクします。 

    両方の部門 IoT ハブに対してこの手順を実行する必要があります。

    **サブスクリプション**:複数のサブスクリプションがある場合は、部門 IoT ハブを作成したサブスクリプションを選択します。

    **[IoT ハブ]** : 作成した部門ハブのいずれかを選択します。

    **[アクセス ポリシー]:** **[iothubowner]** を選択します。

    ![部門 IoT ハブをプロビジョニング サービスとリンクする](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)


5. 両方の部門 IoT ハブをリンクした後、 **[登録グループの追加]** で、以下に示すように登録グループの IoT ハブ グループとしてこれらを選択する必要があります。

    ![登録用の部門ハブ グループを作成する](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)


6. **[登録グループの追加]** で、 **[Select Azure Function]\(Azure Function を選択\)** セクションまで下にスクロールし、 **[Create a new function app]\(新しい関数アプリを作成する\)** をクリックします。

7. 開いた **[Function App]** の作成ページで、新しい関数用に次の設定を入力して **[作成]** をクリックします。

    **[アプリ名]** : 一意の関数アプリ名を入力します。 例として **contoso-function-app-1098** が表示されています。

    **[リソース グループ]** : この記事で作成するすべてのリソースをまとめるため、 **[既存のものを使用]** と **[contoso-us-resource-group]** を選択します。

    **[Application Insights]** : この演習ではオフにできます。

    ![Function App の作成](./media/how-to-use-custom-allocation-policies/function-app-create.png)


8. **[登録グループの追加]** ページに戻り、新しい関数アプリが選択されていることを確認します。 関数アプリの一覧を更新するには、サブスクリプションを再度選択することが必要になる場合があります。

    新しい関数アプリが選択されていたら、 **[Create a new function]\(新しい関数を作成する\)** をクリックします。

    ![Function App の作成](./media/how-to-use-custom-allocation-policies/click-create-new-function.png)

    新しい関数アプリが開きます。

9. その関数アプリでクリックして、新しい関数を作成します。

    ![Function App の作成](./media/how-to-use-custom-allocation-policies/new-function.png)

    この新しい関数には、既定の設定を使用し、**C#** 言語を使って新しく **Webhook + API** を作成します。 **[この関数を作成する]** をクリックします。

    これによって、**HttpTriggerCSharp1** という名前の新しい C# 関数が作成されます。

10. 新しい C# 関数のコードを次のコードで置き換え、 **[保存]** をクリックします。    

    ```csharp
    #r "Newtonsoft.Json"
    using System.Net;
    using System.Text;
    using Newtonsoft.Json;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        // Just some diagnostic logging
        log.Info("C# HTTP trigger function processed a request.");
        log.Info("Request.Content:...");    
        log.Info(req.Content.ReadAsStringAsync().Result);

        // Get request body
        dynamic data = await req.Content.ReadAsAsync<object>();

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.Info("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.Info("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster Model 007
                if (regId.Contains("-contoso-tstrsd-007"))
                {
                    //Find the "-toasters-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-toasters-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No toasters hub found for the enrollment.";
                        log.Info(message);
                        fail = true;
                    }
                }
                // This is a Contoso Heat pump Model 008
                else if (regId.Contains("-contoso-hpsd-088"))
                {
                    //Find the "-heatpumps-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-heatpumps-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No heat pumps hub found for the enrollment.";
                        log.Info(message);
                        fail = true;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.Info("Unknown device registration");
                }
            }
        }

        return (fail)
            ? req.CreateResponse(HttpStatusCode.BadRequest, message)
            : new HttpResponseMessage(HttpStatusCode.OK)
            {
                Content = new StringContent(JsonConvert.SerializeObject(obj, Formatting.Indented), Encoding.UTF8, "application/json")
            };
    }    

    public class DeviceTwinObj
    {
        public string deviceId {get; set;}
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public string IoTHub {get; set;}
        public DeviceTwinObj initialTwin {get; set;}
        public string[] linkedHubs {get; set;}
        public string enrollment {get; set;}
    }
    ```


11. **[登録グループの追加]** ページに戻り、新しい関数が選択されていることを確認します。 関数の一覧を更新するには、関数アプリを再度選択することが必要になる場合があります。

    新しい関数が選択されていたら、 **[保存]** をクリックして登録グループを保存します。

    ![最後に登録グループを保存する](./media/how-to-use-custom-allocation-policies/save-enrollment.png)


12. 登録を保存した後、もう一度開き、 **[主キー]** を書き留めておきます。 キーを生成するには、まず登録を保存する必要があります。 このキーは、シミュレートされたデバイスに対する一意のデバイス キーを生成するために後で使用します。


## <a name="derive-unique-device-keys"></a>一意のデバイス キーを派生させる

このセクションでは、一意のデバイス キーを 2 つ作成します。 1 つのキーは、シミュレートされたトースター デバイスに使用します。 もう 1 つのキーは、シミュレートされたヒート ポンプ デバイスに使用します。

デバイス キーを生成するには、前にメモした**主キー**を使用して、デバイスごとにデバイス登録 ID の [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) を計算し、結果を Base64 形式に変換します。 登録グループを使用して派生デバイス キーを作成する方法の詳細については、「[Symmetric key attestation (対称キーの構成証明)](concepts-symmetric-key-attestation.md)」のグループ登録に関するセクションを参照してください。

この記事の例では、次の 2 つのデバイス登録 ID を使用して、両方のデバイスのデバイス キーを計算します。 両方の登録 ID に、カスタム割り当てポリシーのサンプル コードを操作するために有効なサフィックスが付いています。

- **breakroom499-contoso-tstrsd-007**
- **mainbuilding167-contoso-hpsd-088**

#### <a name="linux-workstations"></a>Linux ワークステーション

Linux ワークステーションを使用している場合は、次の例に示すように、openssl を使用して派生デバイス キーを生成することができます。

1. **KEY** の値を、前に書き留めた**主キー**で置き換えます。

    ```bash
    KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

    REG_ID1=breakroom499-contoso-tstrsd-007
    REG_ID2=mainbuilding167-contoso-hpsd-088

    keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
    devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
    devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

    echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
    ```

    ```bash
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```


#### <a name="windows-based-workstations"></a>Windows ベースのワークステーション

Windows ベースのワークステーションを使用している場合は、次の例に示すように、PowerShell を使用して派生デバイス キーを生成することができます。

1. **KEY** の値を、前に書き留めた**主キー**で置き換えます。

    ```powershell
    $KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

    $REG_ID1='breakroom499-contoso-tstrsd-007'
    $REG_ID2='mainbuilding167-contoso-hpsd-088'

    $hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
    $hmacsha256.key = [Convert]::FromBase64String($key)
    $sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
    $sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
    $derivedkey1 = [Convert]::ToBase64String($sig1)
    $derivedkey2 = [Convert]::ToBase64String($sig2)

    echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
    ```

    ```powershell
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```


シミュレートされたデバイスは、派生デバイス キーと各登録 ID を使用して、対称キーの構成証明を実行します。




## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK の開発環境を準備する

このセクションでは、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) のビルドに使用する開発環境を準備します。 この SDK には、シミュレートされたデバイスのサンプル コードが含まれています。 このシミュレートされたデバイスでは、デバイスのブート シーケンス中にプロビジョニングを試行します。

このセクションは、Windows ベースのワークステーション向けです。 Linux の例については、「[How to provision for multitenancy (マルチテナント用にプロビジョニングする方法)](how-to-provision-multitenant.md)」の VM のセットアップに関するセクションを参照してください。

1. [CMake ビルド システム](https://cmake.org/download/)をダウンロードします。

    `CMake` のインストールを開始する**前に**、Visual Studio の前提条件 (Visual Studio と "C++ によるデスクトップ開発" ワークロード) が マシンにインストールされていることが重要です。 前提条件を満たし、ダウンロードを検証したら、CMake ビルド システムをインストールします。

2. コマンド プロンプトまたは Git Bash シェルを開きます。 次のコマンドを実行して、Azure IoT C SDK の GitHub リポジトリを複製します。
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    この操作は、完了するまでに数分かかります。


3. git リポジトリのルート ディレクトリに `cmake` サブディレクトリを作成し、そのフォルダーに移動します。 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. 次のコマンドを実行して、開発クライアント プラットフォームに固有の SDK のバージョンをビルドします。 シミュレートされたデバイスの Visual Studio ソリューションが `cmake` ディレクトリに生成されます。 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```
    
    `cmake` で C++ コンパイラが見つからない場合は、上記のコマンドの実行中にビルド エラーが発生している可能性があります。 これが発生した場合は、[Visual Studio コマンド プロンプト](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)でこのコマンドを実行してください。 

    ビルドが成功すると、最後のいくつかの出力行は次のようになります。

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```




## <a name="simulate-the-devices"></a>デバイスをシミュレートする

このセクションでは、前にセットアップした Azure IoT C SDK にある **prov\_dev\_client\_sample** という名前のプロビジョニング サンプルを更新します。 

このサンプル コードでは、Device Provisioning Service のインスタンスにプロビジョニング要求を送信するデバイス ブート シーケンスがシミュレートされます。 ブート シーケンスにより、トースター デバイスが認識され、カスタム割り当てポリシーを使用して IoT ハブに割り当てられます。

1. Azure portal で、Device Provisioning Service の **[概要]** タブをクリックし、**[_ID スコープ_]** の値を書き留めます。

    ![ポータルのブレードから Device Provisioning サービスのエンドポイント情報を抽出](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. 前に CMake を実行して生成された **azure_iot_sdks.sln** ソリューション ファイルを Visual Studio で開きます。 ソリューション ファイルは次の場所にあります。

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. Visual Studio の "*ソリューション エクスプローラー*" ウィンドウで、**Provision\_Samples** フォルダーに移動します。 **prov\_dev\_client\_sample** という名前のサンプル プロジェクトを展開します。 **Source Files** を展開し、**prov\_dev\_client\_sample.c** を開きます。

4. 定数 `id_scope` を探し、以前にコピーした **ID スコープ**の値で置き換えます。 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. 同じファイル内で `main()` 関数の定義を探します。 以下に示すように `hsm_type` 変数が `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` に設定されていることを確認します。

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. **prov\_dev\_client\_sample** プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。 


#### <a name="simulate-the-contoso-toaster-device"></a>Contoso トースター デバイスをシミュレートする

1. トースター デバイスをシミュレートするには、**prov\_dev\_client\_sample.c** で、コメントになっている `prov_dev_set_symmetric_key_info()` の呼び出しを探します。

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    関数呼び出しのコメントを解除し、プレースホルダーの値 (山かっこを含む) を、トースターの登録 ID とデバイス派生キー (先ほど生成したもの) に置き換えます。 以下に示したキーの値 **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** は、あくまで一例です。

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```
   
    ファイルを保存します。

2. Visual Studio のメニューで **[デバッグ]**  >  **[デバッグなしで開始]** の順に選択して、ソリューションを実行します。 プロジェクトをリビルドするよう求められたら、 **[はい]** をクリックして、プロジェクトをリビルドしてから実行します。

    次の出力は、シミュレートされたトースター デバイスが正常に起動し、プロビジョニング サービス インスタンスに接続して、カスタム割り当てポリシーによってトースター IoT ハブに割り当てられる例です。

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```


#### <a name="simulate-the-contoso-heat-pump-device"></a>Contoso ヒート ポンプ デバイスをシミュレートする

1. ヒート ポンプ デバイスをシミュレートするには、**prov\_dev\_client\_sample.c** の `prov_dev_set_symmetric_key_info()` の呼び出しを、同じようにヒート ポンプの登録 ID とデバイス派生キー (先ほど生成したもの) で更新します。 以下に示したキーの値 **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** も、あくまで一例です。

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```
   
    ファイルを保存します。

2. Visual Studio のメニューで **[デバッグ]**  >  **[デバッグなしで開始]** の順に選択して、ソリューションを実行します。 プロジェクトをリビルドするよう求められたら、 **[はい]** をクリックして、プロジェクトをリビルドしてから実行します。

    次の出力は、シミュレートされたヒート ポンプ デバイスが正常に起動し、プロビジョニング サービス インスタンスに接続して、カスタム割り当てポリシーによって Contoso ヒート ポンプ IoT ハブに割り当てられる例です。

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```


## <a name="troubleshooting-custom-allocation-policies"></a>カスタム割り当てポリシーのトラブルシューティング

次の表に、想定されるシナリオと、表示される可能性のある結果のエラー コードを示します。 この表は、Azure Functions でのカスタム割り当てポリシーのエラーのトラブルシューティングに利用してください。


| シナリオ | プロビジョニング サービスでの登録結果 | プロビジョニング SDK の結果 |
| -------- | --------------------------------------------- | ------------------------ |
| Webhook から "200 OK" が返され、"iotHubHostName" が有効な IoT ハブ ホスト名に設定されている | 結果の状態: 割り当て済み  | SDK からハブの情報と共に PROV_DEVICE_RESULT_OK が返される |
| Webhook から "200 OK" が返され、応答に "iotHubHostName" が存在するが、空の文字列または null が設定されている | 結果の状態: 失敗<br><br> エラー コード: CustomAllocationIotHubNotSpecified (400208) | SDK から PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED が返される |
| Webhook から "401 権限がありません" が返される | 結果の状態: 失敗<br><br>エラー コード: CustomAllocationUnauthorizedAccess (400209) | SDK から PROV_DEVICE_RESULT_UNAUTHORIZED が返される |
| デバイスを無効にする個々の登録が作成された | 結果の状態: Disabled | SDK から PROV_DEVICE_RESULT_DISABLED が返される |
| Webhook からエラー コード 429 以上が返される | DPS のオーケストレーションが何回も再試行される。 再試行ポリシーは現在以下の通り。<br><br>&nbsp;&nbsp;- 再試行回数: 10<br>&nbsp;&nbsp;- 初期間隔: 1 秒<br>&nbsp;&nbsp;- 増分: 9 秒 | SDK では、エラーが無視され、特定の期間内に別の状態の取得メッセージが送信される |
| Webhook からその他の状態コードが返される | 結果の状態: 失敗<br><br>エラー コード: CustomAllocationFailed (400207) | SDK から PROV_DEVICE_RESULT_DEV_AUTH_ERROR が返される |


## <a name="clean-up-resources"></a>リソースのクリーンアップ

この記事で作成したリソースを引き続き使用する場合は、そのままにしてかまいません。 これ以上リソースを使用しない場合は、不要な課金を避けるために、次の手順に従って、この記事で作成したすべてのリソースを削除してください。

以下の手順では、この記事の説明に従って **contoso-us-resource-group** という名前の同じリソース グループにすべてのリソースが作成されていることを前提にしています。

> [!IMPORTANT]
> リソース グループを削除すると、元に戻すことができません。 リソース グループとそこに含まれるすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 IoT ハブを、保持したいリソースが含まれている既存のリソース グループ内に作成した場合は、リソース グループを削除するのではなく、IoT Hub リソースだけを削除してください。
>

名前でリソース グループを削除するには

1. [Azure ポータル](https://portal.azure.com) にサインインし、 **[リソース グループ]** をクリックします。

2. **[名前でフィルター]** テキスト ボックスに、リソースが含まれているリソース グループの名前 **contoso-us-resource-group** を入力します。 

3. 結果一覧のでリソース グループの **[...]** をクリックし、 **[リソース グループの削除]** をクリックします。

4. リソース グループの削除の確認を求めるメッセージが表示されます。 確認のためにリソース グループの名前を再度入力し、 **[削除]** をクリックします。 しばらくすると、リソース グループとそこに含まれているすべてのリソースが削除されます。

## <a name="next-steps"></a>次の手順

- 再プロビジョニングの詳細については、「[IoT Hub Device reprovisoning concepts](concepts-device-reprovision.md)」(IoT Hub デバイスの再プロビジョニングの概念) をご覧ください 
- プロビジョニング解除の詳細については、「[自動プロビジョニングされた以前のデバイスのプロビジョニングを解除する方法](how-to-unprovision-devices.md)」をご覧ください 











