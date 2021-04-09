---
title: Azure IoT Hub Device Provisioning Service (DPS) でカスタム割り当てポリシーを使用するためのチュートリアル
description: Azure IoT Hub Device Provisioning Service (DPS) でカスタム割り当てポリシーを使用するためのチュートリアル
author: wesmc7777
ms.author: wesmc
ms.date: 09/23/2020
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 42098786bb92e98b89b1dbfba01a2e071398f460
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258543"
---
# <a name="tutorial-use-custom-allocation-policies-with-device-provisioning-service-dps"></a>チュートリアル:Device Provisioning Service (DPS) でカスタム割り当てポリシーを使用する

カスタム割り当てポリシーを使用すると、デバイスを IoT ハブに割り当てる方法をより細かく制御できます。 これは、プロビジョニング中に実行される [Azure 関数](../azure-functions/functions-overview.md)でカスタム コードを使用してデバイスを IoT ハブに割り当てることで実現されます。 Device Provisioning Service から、デバイスと登録に関するすべての関連情報を提供する Azure 関数コードを呼び出します。 関数コードが実行され、デバイスのプロビジョニングに使用する IoT ハブ情報が返されます。

Device Provisioning Service で提供されるポリシーがご自身のシナリオの要件を満たしていない場合は、カスタム割り当てポリシーを使用して独自の割り当てポリシーを定義します。

たとえば、プロビジョニングの際にデバイスで使用されている証明書を確認し、証明書のプロパティに基づいて IoT ハブにデバイスを割り当てる必要がある場合です。 または、デバイス用のデータベースに情報を格納していて、どの IoT ハブにデバイスを割り当てるかを決定するためにそのデータベースを照会する必要がある場合です。

この記事では、対称キーを使用してトースター デバイスをプロビジョニングする、C# で記述された Azure 関数を使用するカスタム割り当てポリシーがある登録グループについて説明します。 トースター デバイスとして認識されないデバイスは、IoT ハブにプロビジョニングされません。

デバイスは、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) に含まれるプロビジョニング サンプル コードを使用してプロビジョニングを要求します。


このチュートリアルでは、以下のことを行います。

> [!div class="checklist"]
> * カスタム割り当て関数をサポートする新しい Azure 関数アプリを作成する
> * カスタム割り当てポリシー用に Azure 関数を使用して新しいグループ登録を作成する
> * 2 つのデバイスのデバイス キーを作成する
> * [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) のデバイス コード例の開発環境をセットアップする
> * デバイスを実行して、デバイスがカスタム割り当てポリシーに従ってプロビジョニングされることを確認する


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* この記事は、[Azure portal での IoT Hub Device Provisioning Service の設定](./quick-setup-auto-provision.md)に関するページの手順を完了していることを前提としています。

* [Git](https://git-scm.com/download/) の最新バージョンがインストールされている。

* Windows 開発環境の場合、["C++ によるデスクトップ開発"](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) のワークロードが有効になっている [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019が必要です。 Visual Studio 2015 と Visual Studio 2017 もサポートされています。

* Linux または macOS については、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) ドキュメントの「[開発環境を準備する](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)」にある適切なセクションを参照してください。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-custom-allocation-function"></a>カスタム割り当て関数を作成する

このセクションでは、カスタム割り当てポリシーを実装する Azure 関数を作成します。 この関数により、登録 ID に文字列プレフィックス **contoso-toaster** が含まれているかどうかに基づいて、デバイスを IoT Hub に登録する必要があるかどうかが決定されます。

1. [Azure portal](https://portal.azure.com) にサインインします。 ご自分のホーム ページから **[+ リソースの作成]** を選択します。

2. *[Marketplace を検索]* 検索ボックスで、「関数アプリ」と入力します。 ドロップダウン リストから **[関数アプリ]** を選択し、 **[作成]** を選択します。

3. **関数アプリ** の作成ページの **[基本]** タブで、新しい関数アプリに次の設定を入力し、**[確認と作成]** を選択します。

    **サブスクリプション**:複数のサブスクリプションがあり、目的のサブスクリプションが選択されていない場合は、使用するサブスクリプションを選択します。

    **リソース グループ**:このフィールドでは、新しいリソース グループを作成することも、関数アプリを含む既存のリソース グループを選択することもできます。 テストのために先ほど作成した IoT ハブが含まれる、同じリソース グループを選択します (例: **TestResources**)。 関連するすべてのリソースを 1 つのグループ内に配置することで、それらを一緒に管理できます。

    **関数アプリ名**:一意の関数アプリ名を入力します。 この例では、**contoso-function-app** を使用します。

    **発行**: **[コード]** が選択されていることを確認します。

    **ランタイム スタック**:ドロップダウンから **[.NET Core]** を選択します。

    **[リージョン]** :ご自分のリソース グループと同じリージョンを選択します。 この例では **米国西部** を使用します。

    > [!NOTE]
    > 既定では、Application Insights が有効になっています。 この記事では Application Insights は必要ありませんが、カスタム割り当てで発生する問題を理解し、調査するのに役立つ場合があります。 必要に応じて、 **[監視]** タブを選択し、 **[Application Insights を有効にする]** を **[いいえ]** に選択することで、Application Insights を無効にすることができます。

    ![カスタム割り当て関数をホストする Azure 関数アプリを作成する](./media/tutorial-custom-allocation-policies/create-function-app.png)

4. **[概要]** ページで、 **[作成]** を選択して関数アプリを作成します。 デプロイには数分かかることがあります。 完了したら、**[リソースに移動]** を選択します。

5. 左側のウィンドウで、 **[関数]** の下にある **[関数]** をクリックしてから、 **[+ 追加]** をクリックして新しい関数を追加します。

6. テンプレートのページで、 **[HTTP トリガー]** タイルを選択してから、 **[関数の作成]** を選択します。 **HttpTrigger1** という名前の関数が作成され、ポータルに 関数の概要ページが表示されます。

7. 新しい関数に対して、 **[コードとテスト]** をクリックします。 ポータルには、**run.csx** コード ファイルの内容が表示されます。 

8. **HttpTrigger1** 関数のコードを、次のコードに置き換えて、 **[保存]** を選択します。 カスタム割り当てコードを使用する準備ができました。

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);

        log.LogInformation("Request.Body:...");
        log.LogInformation(requestBody);

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.LogInformation("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.LogInformation("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster 
                if (regId.Contains("contoso-toaster"))
                {
                    //Log IoT hubs configured for the enrollment
                    foreach(string hubString in hubs)
                    {
                        log.LogInformation("linkedHub : " + hubString);
                    }

                    obj.iotHubHostName = hubs[0];
                    log.LogInformation("Selected hub : " + obj.iotHubHostName);
                }
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.LogInformation("Unknown device registration");
                }
            }
        }

        log.LogInformation("\nResponse");
        log.LogInformation((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message);

        return (fail)
            ? new BadRequestObjectResult(message) 
            : (ActionResult)new OkObjectResult(obj);
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
    }
    ```

9. **run.csx** コードファイルのすぐ下で、 **[ログ]** をクリックしてカスタム割り当て関数のログ記録を監視します。 


## <a name="create-the-enrollment"></a>登録を作成する

このセクションでは、カスタム割り当てポリシーを使用する新しい登録グループを作成します。 わかりやすくするため、この記事では[対称キーの構成証明](concepts-symmetric-key-attestation.md)を登録で使用します。 ソリューションをさらに安全にするには、信頼チェーンで [X.509 証明書構成証明](concepts-x509-attestation.md)を使用することを検討してください。

1. 引き続き [Azure portal](https://portal.azure.com) でプロビジョニング サービスを開きます。

2. 左側のウィンドウで **[登録を管理します]** を選択してから、ページの上部にある **[登録グループの追加]** ボタンを選択します。

3. **[登録グループの追加]** で、次の表の情報を入力して、 **[保存]** ボタンをクリックします。

    | フィールド | 説明や推奨値 |
    | :---- | :----------------------------- |
    | **グループ名** | 「**contoso-custom-allocated-devices**」と入力します |
    | **構成証明の種類** | **[対称キー]** を選択します |
    | **キーの自動生成** | このチェック ボックスは既にオンになっているはずです。 |
    | **デバイスをハブに割り当てる方法を選択してください** | **[カスタム (Azure 関数を使用)]** を選択します |
    | **このグループを割り当てることができる IoT ハブを選択してください** | 前にクイック スタートを完了したときに作成した IoT hub を選択します。 |
    | **Azure 関数の選択** | 作成した関数アプリを含むサブスクリプションを選択する。 次に、その関数のために **contoso-function-app** と **HttpTrigger1** を選択します。 |

    ![対称キー構成証明用にカスタム割り当て登録グループを追加する](./media/tutorial-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. 登録を保存した後、もう一度開き、**[主キー]** を書き留めておきます。 キーを生成するには、まず登録を保存する必要があります。 このプライマリ対称キーは、後でプロビジョニングを試みるデバイス用に一意のデバイス キーを生成するために使用されます。 

## <a name="derive-unique-device-keys"></a>一意のデバイス キーを派生させる

デバイスでは、プライマリ対称キーは直接使用されません。 代わりに、プライマリ キーを使用して各デバイスのデバイス キーが派生されます。 このセクションでは、一意のデバイス キーを 2 つ作成します。 1 つのキーは、シミュレートされたトースター デバイスに使用します。 もう 1 つのキーは、シミュレートされたヒート ポンプ デバイスに使用します。 生成されたキーを使用することで、両方のデバイスが登録を試みることが可能になります。 カスタム割り当てポリシーのコード例で受け付けられる有効なサフィックスは、1 つのデバイス登録 ID のみが持つことになります。 結果として、1 つは受け付けられ、他方は拒否されます。

デバイス キーを派生させるには、前に書き留めた対象キーを使用して、デバイスごとにデバイス登録 ID の [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) を計算し、その結果を Base64 形式に変換します。 登録グループを使用して派生デバイス キーを作成する方法の詳細については、「[Symmetric key attestation (対称キーの構成証明)](concepts-symmetric-key-attestation.md)」のグループ登録に関するセクションを参照してください。

この記事の例では、下記のコードで次の 2 つのデバイス登録 ID を使用して、両方のデバイスのデバイス キーを計算します。

* **contoso-toaster-007**
* **contoso-heatpump-088**

**KEY** 変数の値は、登録グループの作成後に書き留めた **主キー** に置き換えます。 下のコードで示されているキー値と出力は単なる例です。

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Windows ベースのワークステーションを使用している場合は、次の例に示すように、PowerShell を使用して派生デバイス キーを生成することができます。

```powershell
$KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

$REG_ID1='contoso-toaster-007'
$REG_ID2='contoso-heatpump-088'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($key)
$sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
$sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
$derivedkey1 = [Convert]::ToBase64String($sig1)
$derivedkey2 = [Convert]::ToBase64String($sig2)

echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
```

```powershell
contoso-toaster-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
contoso-heatpump-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

#### <a name="bash"></a>[Bash](#tab/bash)

Linux ワークステーションを使用している場合は、次の Bash の例に示すように、openssl を使用して派生デバイス キーを生成できます。

```bash
KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

REG_ID1=contoso-toaster-007
REG_ID2=contoso-heatpump-088

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
```

```bash
contoso-toaster-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
contoso-heatpump-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

---


## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK の開発環境を準備する

デバイスは、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) に含まれるプロビジョニング サンプル コードを使用してプロビジョニングを要求します。

このセクションでは、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) のビルドに使用する開発環境を準備します。 この SDK には、シミュレートされたデバイス用のサンプル コードが含まれています。 このシミュレートされたデバイスでは、デバイスのブート シーケンス中にプロビジョニングを試行します。

このセクションは、Windows ベースのワークステーション向けです。 Linux の例については、「[How to provision for multitenancy (マルチテナント用にプロビジョニングする方法)](how-to-provision-multitenant.md)」の VM のセットアップに関するセクションを参照してください。

1. [CMake ビルド システム](https://cmake.org/download/)をダウンロードします。

    `CMake` のインストールを開始する **前に**、Visual Studio の前提条件 (Visual Studio と "C++ によるデスクトップ開発" ワークロード) が マシンにインストールされていることが重要です。 前提条件を満たし、ダウンロードを検証したら、CMake ビルド システムをインストールします。

2. SDK の[最新リリース](https://github.com/Azure/azure-iot-sdk-c/releases/latest)のタグ名を見つけます。

3. コマンド プロンプトまたは Git Bash シェルを開きます。 次のコマンドを実行して、最新リリースの [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub リポジトリを複製します。 `-b` パラメーターの値として、前の手順で見つけたタグを使用します。

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    この操作は、完了するまでに数分かかります。

4. git リポジトリのルート ディレクトリに `cmake` サブディレクトリを作成し、そのフォルダーに移動します。 `azure-iot-sdk-c` ディレクトリから次のコマンドを実行します。

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. 次のコマンドを実行して、開発クライアント プラットフォームに固有の SDK のバージョンをビルドします。 シミュレートされたデバイスの Visual Studio ソリューションが `cmake` ディレクトリに生成されます。 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    `cmake` で C++ コンパイラが見つからない場合は、コマンドの実行中にビルド エラーが発生している可能性があります。 これが発生した場合は、[Visual Studio コマンド プロンプト](/dotnet/framework/tools/developer-command-prompt-for-vs)でコマンドを実行してください。

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
    azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. Visual Studio の "*ソリューション エクスプローラー*" ウィンドウで、**Provision\_Samples** フォルダーに移動します。 **prov\_dev\_client\_sample** という名前のサンプル プロジェクトを展開します。 **Source Files** を展開し、**prov\_dev\_client\_sample.c** を開きます。

4. 定数 `id_scope` を探し、以前にコピーした **ID スコープ** の値で置き換えます。 

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

6. `main()` 関数で、`Prov_Device_Register_Device()` の呼び出しを見つけます。 その呼び出しの直前に、[`Prov_Device_Set_Provisioning_Payload()`](/azure/iot-hub/iot-c-sdk-ref/prov-device-client-h/prov-device-set-provisioning-payload) を使用する次のコード行を追加し、プロビジョニング中にカスタム JSON ペイロードを渡します。 カスタム割り当て関数に与える情報を増やすためにこれを利用できます。 登録 ID を調べる代わりに、デバイスの種類を渡す目的でこれを使用することもできます。 DPS を使用したカスタム データ ペイロードの送受信の詳細については、「[デバイスと DPS の間でペイロードを転送する方法](how-to-send-additional-data.md)」を参照してください。

    ```c
    // An example custom payload
    const char* custom_json_payload = "{\"MyDeviceFirmwareVersion\":\"12.0.2.5\",\"MyDeviceProvisioningVersion\":\"1.0.0.0\"}";

    prov_device_result = Prov_Device_Set_Provisioning_Payload(prov_device_handle, custom_json_payload);
    if (prov_device_result != PROV_DEVICE_RESULT_OK)
    {
        (void)printf("\r\nFailure setting provisioning payload: %s\r\n", MU_ENUM_TO_STRING(PROV_DEVICE_RESULT, prov_device_result));
    }
    ```

7. **prov\_dev\_client\_sample** プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。

### <a name="simulate-the-contoso-toaster-device"></a>Contoso トースター デバイスをシミュレートする

1. トースター デバイスをシミュレートするには、**prov\_dev\_client\_sample.c** で、コメントになっている `prov_dev_set_symmetric_key_info()` の呼び出しを探します。

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    関数呼び出しのコメントを解除し、プレースホルダーの値 (山かっこを含む) を、前に生成したトースター登録 ID と派生デバイスキーに置き換えます。 以下に示したキーの値 **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** は、あくまで一例です。

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-toaster-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    ファイルを保存します。

2. Visual Studio のメニューで **[デバッグ]**  >  **[デバッグなしで開始]** の順に選択して、ソリューションを実行します。 プロジェクトをリビルドするよう求められたら、 **[はい]** を選択して、プロジェクトをリビルドしてから実行します。

    次のテキストに示すのは、トースター デバイスのために実行されているカスタム割り当て関数コードからのログ記録の出力例です。 トースター デバイスに対してハブが適切に選択されていることに注目してください。 コードに追加したカスタム JSON コンテンツが含まれる `payload` メンバーにも注意してください。 これは `deviceRuntimeContext` 内でコードで使用する目的でも利用できます。

    このログ記録は、ポータルの関数コードの下にある **[ログ]** をクリックすると使用できます。

    ```cmd
    2020-09-23T11:44:37.505 [Information] Executing 'Functions.HttpTrigger1' (Reason='This function was programmatically called via the host APIs.', Id=4596d45e-086f-4e86-929b-4a02814eee40)
    2020-09-23T11:44:41.380 [Information] C# HTTP trigger function processed a request.
    2020-09-23T11:44:41.381 [Information] Request.Body:...
    2020-09-23T11:44:41.381 [Information] {"enrollmentGroup":{"enrollmentGroupId":"contoso-custom-allocated-devices","attestation":{"type":"symmetricKey"},"capabilities":{"iotEdge":false},"etag":"\"e8002126-0000-0100-0000-5f6b2a570000\"","provisioningStatus":"enabled","reprovisionPolicy":{"updateHubAssignment":true,"migrateDeviceData":true},"createdDateTimeUtc":"2020-09-23T10:58:31.62286Z","lastUpdatedDateTimeUtc":"2020-09-23T10:58:31.62286Z","allocationPolicy":"custom","iotHubs":["contoso-toasters-hub-1098.azure-devices.net"],"customAllocationDefinition":{"webhookUrl":"https://contoso-function-app.azurewebsites.net/api/HttpTrigger1?****","apiVersion":"2019-03-31"}},"deviceRuntimeContext":{"registrationId":"contoso-toaster-007","symmetricKey":{},"payload":{"MyDeviceFirmwareVersion":"12.0.2.5","MyDeviceProvisioningVersion":"1.0.0.0"}},"linkedHubs":["contoso-toasters-hub-1098.azure-devices.net"]}
    2020-09-23T11:44:41.687 [Information] linkedHub : contoso-toasters-hub-1098.azure-devices.net
    2020-09-23T11:44:41.688 [Information] Selected hub : contoso-toasters-hub-1098.azure-devices.net
    2020-09-23T11:44:41.688 [Information] Response
    2020-09-23T11:44:41.688 [Information] {"iotHubHostName":"contoso-toasters-hub-1098.azure-devices.net"}
    2020-09-23T11:44:41.689 [Information] Executed 'Functions.HttpTrigger1' (Succeeded, Id=4596d45e-086f-4e86-929b-4a02814eee40, Duration=4347ms)    
    ```

    次のデバイス出力例は、シミュレートされたトースター デバイスが正常に起動し、カスタム割り当てポリシーによってトースター IoT ハブに割り当てられるプロビジョニング サービス インスタンスに接続することを示しています。

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: contoso-toaster-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Contoso ヒート ポンプ デバイスをシミュレートする

1. ヒート ポンプ デバイスをシミュレートするには、**prov\_dev\_client\_sample.c** の `prov_dev_set_symmetric_key_info()` の呼び出しを、同じようにヒート ポンプの登録 ID とデバイス派生キー (先ほど生成したもの) で更新します。 以下に示したキーの値 **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** も、あくまで一例です。

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-heatpump-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    ファイルを保存します。

2. Visual Studio のメニューで **[デバッグ]**  >  **[デバッグなしで開始]** の順に選択して、ソリューションを実行します。 プロジェクトをリビルドするよう求められたら、 **[はい]** を選択して、プロジェクトをリビルドしてから実行します。

    次のテキストに示すのは、ヒート ポンプ デバイスのために実行されているカスタム割り当て関数コードからのログ記録の出力例です。 カスタム割り当てポリシーではこの登録が拒否されて、HTTP エラー 400 の無効な要求が発生します。 コードに追加したカスタム JSON コンテンツが含まれる `payload` メンバーに注意してください。 これは `deviceRuntimeContext` 内でコードで使用する目的でも利用できます。

    このログ記録は、ポータルの関数コードの下にある **[ログ]** をクリックすると使用できます。

    ```cmd
    2020-09-23T11:50:23.652 [Information] Executing 'Functions.HttpTrigger1' (Reason='This function was programmatically called via the host APIs.', Id=2fa77f10-42f8-43fe-88d9-a8c01d4d3f68)
    2020-09-23T11:50:23.653 [Information] C# HTTP trigger function processed a request.
    2020-09-23T11:50:23.654 [Information] Request.Body:...
    2020-09-23T11:50:23.654 [Information] {"enrollmentGroup":{"enrollmentGroupId":"contoso-custom-allocated-devices","attestation":{"type":"symmetricKey"},"capabilities":{"iotEdge":false},"etag":"\"e8002126-0000-0100-0000-5f6b2a570000\"","provisioningStatus":"enabled","reprovisionPolicy":{"updateHubAssignment":true,"migrateDeviceData":true},"createdDateTimeUtc":"2020-09-23T10:58:31.62286Z","lastUpdatedDateTimeUtc":"2020-09-23T10:58:31.62286Z","allocationPolicy":"custom","iotHubs":["contoso-toasters-hub-1098.azure-devices.net"],"customAllocationDefinition":{"webhookUrl":"https://contoso-function-app.azurewebsites.net/api/HttpTrigger1?****","apiVersion":"2019-03-31"}},"deviceRuntimeContext":{"registrationId":"contoso-heatpump-088","symmetricKey":{},"payload":{"MyDeviceFirmwareVersion":"12.0.2.5","MyDeviceProvisioningVersion":"1.0.0.0"}},"linkedHubs":["contoso-toasters-hub-1098.azure-devices.net"]}
    2020-09-23T11:50:23.654 [Information] Unknown device registration
    2020-09-23T11:50:23.654 [Information] Response
    2020-09-23T11:50:23.654 [Information] Unrecognized device registration.
    2020-09-23T11:50:23.655 [Information] Executed 'Functions.HttpTrigger1' (Succeeded, Id=2fa77f10-42f8-43fe-88d9-a8c01d4d3f68, Duration=11ms)
    ```

    次のデバイス出力例は、シミュレートされたヒート ポンプ デバイスが起動し、カスタム割り当てポリシーを使用して IoT ハブへの登録を試みるプロビジョニング サービス インスタンスに接続することを示しています。 カスタム割り当てポリシーはトースター デバイスのみを許可するように設計されたため、これはエラー (`Custom allocation failed with status code: 400`) で失敗します。


    ```cmd
    Provisioning API Version: 1.3.7
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_device_ll_client.c Func:_prov_transport_process_json_reply Line:658 Provisioning Failure: OperationId: 4.eb89f3e8407a3711.2525bd34-02e9-4e91-a9c0-4dbc4ad5de66 - Date: 2020-09-23T17:05:58.2363145Z - Msg: Custom allocation failed with status code: 400
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_transport_mqtt_common.c Func:_prov_transport_common_mqtt_dowork Line:1014 Unable to process registration reply.
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_device_ll_client.c Func:_on_transport_registration_data Line:770 Failure retrieving data from the provisioning service
    
    Failure registering device: PROV_DEVICE_RESULT_DEV_AUTH_ERROR
    Press enter key to exit:    
    ```
    
## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事で作成したリソースを引き続き使用する場合は、そのままにしてかまいません。 これ以上リソースを使用しない場合は、不要な課金を避けるために、次の手順に従って、この記事で作成したすべてのリソースを削除してください。

以下の手順では、この記事の説明に従って **contoso-us-resource-group** という名前の同じリソース グループにすべてのリソースが作成されていることを前提にしています。

> [!IMPORTANT]
> リソース グループを削除すると、元に戻すことができません。 リソース グループとそこに含まれるすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 IoT ハブを、保持したいリソースが含まれている既存のリソース グループ内に作成した場合は、リソース グループを削除するのではなく、IoT Hub リソースだけを削除してください。
>

名前でリソース グループを削除するには

1. [Azure portal](https://portal.azure.com) にサインインし、 **[リソース グループ]** を選択します。

2. **[名前でフィルター]** テキスト ボックスに、リソースが含まれているリソース グループの名前 **contoso-us-resource-group** を入力します。 

3. 結果一覧のリソース グループの右側で、 **[...]** 、 **[リソース グループの削除]** の順に選択します。

4. リソース グループの削除の確認を求めるメッセージが表示されます。 確認のためにもう一度リソース グループの名前を入力し、 **[削除]** を選択します。 しばらくすると、リソース グループとそこに含まれているすべてのリソースが削除されます。

## <a name="next-steps"></a>次のステップ

* より詳細なカスタム割り当てポリシーの例については、「[カスタム割り当てポリシーの使用方法](how-to-use-custom-allocation-policies.md)」を参照してください。
* 再プロビジョニングの詳細については、「[IoT Hub デバイスの再プロビジョニングの概念](concepts-device-reprovision.md)」を参照してください。
* プロビジョニング解除の詳細については、「[以前に自動プロビジョニングされたデバイスのプロビジョニングを解除する方法](how-to-unprovision-devices.md)」を参照してください。