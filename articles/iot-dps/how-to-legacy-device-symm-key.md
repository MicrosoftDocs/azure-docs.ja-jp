---
title: 対称キーを使用してデバイスをプロビジョニングする - Azure IoT Hub Device Provisioning Service
description: デバイス プロビジョニング サービス (DPS) インスタンスで対称キーを使用してデバイスをプロビジョニングする方法
author: wesmc7777
ms.author: wesmc
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: lizross
ms.openlocfilehash: a4c16347d1883e1522fda18c2382f2d67b8ace80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99051111"
---
# <a name="how-to-provision-devices-using-symmetric-key-enrollment-groups"></a>対称キー登録グループを使用してデバイスをプロビジョニングする方法

この記事では、登録グループを利用し、1 つの IoT Hub に複数の対称キーを安全にプロビジョニングする方法を実演します。

一部のデバイスには、デバイスを安全に識別するために使用できる証明書、TPM、またはその他のセキュリティ機能がない場合があります。 Device Provisioning Service には、[対称キーの構成証明](concepts-symmetric-key-attestation.md)が含まれています。 対称キーの構成証明は、MAC アドレスやシリアル番号などの固有の情報に基づいてデバイスを識別するために使用できます。

[ハードウェア セキュリティ モジュール (HSM)](concepts-service.md#hardware-security-module) と証明書を簡単にインストールできる場合は、その方法の方が、デバイスを識別およびプロビジョニングするアプローチとして優れている可能性があります。 HSM を使用すると、すべてのデバイスにデプロイされているコードの更新を省略でき、デバイス イメージに秘密キーは埋め込まれません。 この記事では、HSM と証明書のどちらも有効なオプションではないことを前提としています。 ただし、Device Provisioning Service を使用してこれらのデバイスをプロビジョニングするよう、デバイス コードを更新するいくつかの方法があることを前提としています。 

また、この記事では、マスター グループ キーまたは派生デバイス キーへの未承認のアクセスを防ぐために、セキュリティで保護された環境でデバイスの更新が実行されることも想定されています。

この記事は、Windows ベースのワークスペース向けです。 ただし、Linux でもこの手順を実行できます。 Linux の例については、「[How to provision for multitenancy](how-to-provision-multitenant.md)」(マルチテナント方式のプロビジョニング) を参照してください。

> [!NOTE]
> この記事で使用されているサンプルは、C で記述されています。[C# デバイス プロビジョニングの対称キーのサンプル](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample)も使用できます。 このサンプルを使用するには、[azure-iot-samples-csharp](https://github.com/Azure-Samples/azure-iot-samples-csharp) リポジトリをダウンロードまたは複製し、サンプル コードのインラインの手順に従います。 この記事の手順に従って、ポータルを使用して対称キー登録グループを作成し、サンプルの実行に必要な ID スコープと登録グループのプライマリ キーとセカンダリ キーを検索できます。 また、サンプルを使用して個別登録を作成することもできます。

## <a name="overview"></a>概要

一意の登録 ID は、そのデバイスを識別する情報に基づいてデバイスごとに定義されます。 たとえば、MAC アドレスまたはシリアル番号などです。

[対称キーの構成証明](concepts-symmetric-key-attestation.md)を使用する登録グループは、Device Provisioning Service を使用して作成されます。 登録グループには、グループ マスター キーが含まれます。 このマスター キーは、デバイスごとに一意のデバイス キーを生成するために、一意の各登録 ID のハッシュに使用されます。 デバイスは、この派生デバイス キーとその一意の登録 ID を使用して Device Provisioning Service で構成証明され、IoT ハブに割り当てられます。

この記事で示すデバイス コードは、「[クイック スタート: 対称キーを使用してシミュレートされたデバイスをプロビジョニングする](quick-create-simulated-device-symm-key.md)」と同じパターンに従います。 このコードでは、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) からのサンプルを使用してデバイスがシミュレートされます。 シミュレートされたデバイスは、クイック スタートで示されている個々の登録ではなく、登録グループで構成証明されます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>前提条件

* [Azure portal での IoT Hub Device Provisioning Service の設定](./quick-setup-auto-provision.md)に関するクイック スタートが完了していること。

Windows 開発環境の前提条件は次のとおりです。 Linux または macOS については、SDK ドキュメントの「[開発環境を準備する](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)」の該当するセクションを参照してください。

* [C++ によるデスクトップ開発](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development)ワークロードを有効にした [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019。 Visual Studio 2015 と Visual Studio 2017 もサポートされています。

* [Git](https://git-scm.com/download/) の最新バージョンがインストールされている。

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK の開発環境を準備する

このセクションでは、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) のビルドに使用する開発環境を準備します。 

この SDK には、シミュレートされたデバイス用のサンプル コードが含まれています。 このシミュレートされたデバイスでは、デバイスのブート シーケンス中にプロビジョニングを試行します。

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

4. Git リポジトリのルート ディレクトリに `cmake` サブディレクトリを作成し、そのフォルダーに移動します。 `azure-iot-sdk-c` ディレクトリから次のコマンドを実行します。

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. 次のコマンドを実行して、開発クライアント プラットフォームに固有の SDK のバージョンをビルドします。 シミュレートされたデバイスの Visual Studio ソリューションが `cmake` ディレクトリに生成されます。 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```
    
    `cmake` で C++ コンパイラが見つからない場合は、上記のコマンドの実行中にビルド エラーが発生している可能性があります。 これが発生した場合は、[Visual Studio コマンド プロンプト](/dotnet/framework/tools/developer-command-prompt-for-vs)でこのコマンドを実行してください。 

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


## <a name="create-a-symmetric-key-enrollment-group"></a>対称キーの登録グループを作成する

1. [Azure portal](https://portal.azure.com) にサインインし、Device Provisioning Services のインスタンスを開きます。

2. **[登録を管理します]** タブを選択し、ページの上部にある **[登録グループの追加]** ボタンをクリックします。 

3. **[登録グループの追加]** で、次の情報を入力して、**[保存]** ボタンをクリックします。

   - **[グループ名]**: 「**mylegacydevices**」と入力します。

   - **[Attestation Type]\(構成証明の種類\)**: **[対称キー]** を選択します。

   - **[キーの自動生成]**: このボックスをオンにします。

   - **[デバイスをハブに割り当てる方法を選択してください]**:特定のハブに割り当てるために **[静的構成]** を選択します。

   - **[Select the IoT hubs this group can be assigned to]/(このグループを割り当てられる IoT ハブを選択してください/)**: いずれかのハブを選択します。

     ![対称キー構成証明に登録グループを追加する](./media/how-to-legacy-device-symm-key/symm-key-enrollment-group.png)

4. 登録を保存したら、**主キー** と **セカンダリ キー** が生成され、登録エントリに追加されます。 対称キーの登録グループが、*[登録グループ]* タブの *[グループ名]* 列に **mylegacydevices** として対表示されます。 

    登録を開き、生成された **主キー** の値をコピーします。 このキーは、マスター グループ キーです。


## <a name="choose-a-unique-registration-id-for-the-device"></a>デバイスの一意の登録 ID を選択する

各デバイスを識別する一意の登録 ID を定義する必要があります。 デバイスの MAC アドレス、シリアル番号、または何らかの固有の情報を使用できます。 

この例では、MAC アドレスとシリアル番号の組み合わせを使用して、次のような登録 ID の文字列を形成します。

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

デバイスごとに一意の登録 ID を作成します。 有効な文字は、小文字の英字、数字、ダッシュ ('-') です。


## <a name="derive-a-device-key"></a>デバイス キーを派生させる 

デバイス キーを生成するには、登録グループのマスター キーを使用して、各デバイスの登録 ID の [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) を計算します。 結果は、デバイスごとに Base64 形式に変換されます。

> [!WARNING]
> 各デバイスのデバイス コードには、そのデバイスの対応する派生デバイス キーのみ含める必要があります。 デバイス コードにはグループのマスター キーを含めないでください。 マスター キーが盗まれた場合、それで認証されるすべてのデバイスのセキュリティが危険にさらされる可能性があります。


# <a name="windows"></a>[Windows](#tab/windows)

Windows ベースのワークステーションを使用している場合は、次の例に示すように、PowerShell を使用して派生デバイス キーを生成することができます。

**KEY** の値を、前に書き留めた **主キー** で置き換えます。

**REG_ID** の値を登録 ID に置き換えます。

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

# <a name="linux"></a>[Linux](#tab/linux)

Linux ワークステーションを使用している場合は、次の例に示すように、openssl を使用して派生デバイス キーを生成することができます。

**KEY** の値を、前に書き留めた **主キー** で置き換えます。

**REG_ID** の値を登録 ID に置き換えます。

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

---

各デバイスのプロビジョニング時に、派生デバイス キーと一意の登録 ID を使用して、登録グループで対称キーの構成証明が実行されます。



## <a name="create-a-device-image-to-provision"></a>プロビジョニングするデバイス イメージを作成する

このセクションでは、前にセットアップした Azure IoT C SDK にある **prov\_dev\_client\_sample** という名前のプロビジョニング サンプルを更新します。 

このサンプル コードでは、Device Provisioning Service のインスタンスにプロビジョニング要求を送信するデバイス ブート シーケンスがシミュレートされます。 ブート シーケンスにより、デバイスが認識され、登録グループで構成した IoT ハブに割り当てられます。 これは、登録グループを使用してプロビジョニングされるデバイスごとに実行されます。

1. Azure portal で、Device Provisioning Service の **[概要]** タブをクリックし、**[_ID スコープ_]** の値を書き留めます。

    ![ポータルのブレードから Device Provisioning サービスのエンドポイント情報を抽出](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. 前に CMake を実行して生成された **azure_iot_sdks.sln** ソリューション ファイルを Visual Studio で開きます。 ソリューション ファイルは次の場所にあります。

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
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

6. **prov\_dev\_client\_sample.c** で、コメントになっている `prov_dev_set_symmetric_key_info()` の呼び出しを探します。

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    関数呼び出しのコメントを解除し、プレースホルダーの値 (山かっこを含む) を、お使いのデバイスの一意の登録 ID とご自分で生成したデバイス派生キーに置き換えます。

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6", "Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=");
    ```
   
    ファイルを保存します。

7. **prov\_dev\_client\_sample** プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。 

8. Visual Studio のメニューで **[デバッグ]**  >  **[デバッグなしで開始]** の順に選択して、ソリューションを実行します。 プロジェクトをリビルドするよう求められたら、**[はい]** をクリックして、プロジェクトをリビルドしてから実行します。

    次の出力は、シミュレートされたデバイスが正常に起動し、IoT ハブに割り当てられるプロビジョニング サービス インスタンスに接続する例です。

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

    Press enter key to exit:
    ```

9. ポータルで、シミュレートされたデバイスが割り当てられた IoT ハブに移動し、 **[IoT デバイス]** タブをクリックします。シミュレートされたデバイスがハブに正常にプロビジョニングされると、そのデバイス ID は、**有効** な *状態* で **[IoT デバイス]** ブレードに表示されます。 必要に応じて、一番上の **[更新]** ボタンをクリックします。 

    ![IoT ハブに登録されたデバイス](./media/how-to-legacy-device-symm-key/hub-registration.png) 



## <a name="security-concerns"></a>セキュリティに関する考慮事項

派生デバイス キーが各デバイスのイメージの一部として含まれたままになることに注意してください。この状況は、推奨されるセキュリティのベスト プラクティスではありません。 これは、セキュリティと使いやすさが両立しないことが多い理由の 1 つです。 独自の要件に基づいて、デバイスのセキュリティを徹底的に確認する必要があります。


## <a name="next-steps"></a>次のステップ

* 再プロビジョニングの詳細については、「[IoT Hub Device reprovisoning concepts](concepts-device-reprovision.md)」(IoT Hub デバイスの再プロビジョニングの概念) をご覧ください 
* [クイック スタート: 対称キーを使用してシミュレートされたデバイスをプロビジョニングする](quick-create-simulated-device-symm-key.md)
* プロビジョニング解除の詳細については、「[自動プロビジョニングされた以前のデバイスのプロビジョニングを解除する方法](how-to-unprovision-devices.md)」をご覧ください
