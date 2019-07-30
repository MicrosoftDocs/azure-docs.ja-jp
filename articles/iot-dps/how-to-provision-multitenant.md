---
title: Azure IoT Hub Device Provisioning Service でマルチテナント用にデバイスをプロビジョニングする方法 | Microsoft Docs
description: デバイス プロビジョニング サービス インスタンスでマルチテナント用にデバイスをプロビジョニングする方法です
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 84e1f57175d772ad281c18b67fa1be484c0cac69
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66116058"
---
# <a name="how-to-provision-for-multitenancy"></a>マルチテナント用にプロビジョニングする方法 

プロビジョニング サービスによって定義されている割り当てポリシーでは、さまざまな割り当てシナリオがサポートされています。 よく使用されるシナリオは次の 2 つです。

* **位置情報/geo 待機時間**: デバイスが異なる場所の間を移動するときは、各場所に最も近い IoT ハブに対してデバイスをプロビジョニングすることにより、ネットワーク待ち時間が改善されます。 このシナリオでは、異なるリージョンに広がる IoT ハブのグループを、登録対象に選択します。 これらの登録に対して、**最短待機時間**割り当てポリシーを選択します。 このポリシーを指定すると、Device Provisioning Service はデバイスの待機時間を評価して、IoT ハブのグループから最も近い IoT ハブを決定します。 

* **マルチテナント**: IoT ソリューション内で使用されるデバイスは、特定の IoT ハブまたは IoT ハブのグループに割り当てることが必要な場合があります。 ソリューションでは、特定のテナントのすべてのデバイスが、IoT ハブの特定のグループと通信することが必要な場合があります。 場合によっては、テナントが IoT ハブを所有しており、デバイスを IoT ハブに割り当てることが必要なことがあります。

これら 2 つのシナリオを結合するのは一般的なことです。 たとえば、マルチテナントの IoT ソリューションでは、複数のリージョンに分散している IoT ハブのグループを使用して、テナントのデバイスを割り当てることがよくあります。 これらのテナント デバイスは、地理的な場所に基づいて、そのグループ内で待機時間が最短の IoT ハブに割り当てることができます。

この記事では、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) のシミュレートされたデバイスのサンプルを使用して、複数リージョンのマルチテナント シナリオでデバイスをプロビジョニングする方法を示します。 この記事では、以下の手順を実施します。

* Azure CLI を使用して、2 つのリージョン (**米国西部**と**米国東部**) に IoT ハブを作成します
* マルチテナントの登録を作成します
* Azure CLI を使用して、同じ 2 つのリージョン (**米国西部**と**米国東部**) にデバイスとして機能する Linux VM を作成します
* 両方の Linux VM 上に Azure IoT C SDK 用の開発環境をセットアップします
* デバイスをシミュレートして、最も近いリージョンの同じテナントに対してプロビジョニングされることを確認します


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>前提条件

* [Azure portal での IoT Hub Device Provisioning Service の設定](./quick-setup-auto-provision.md)に関するクイック スタートが完了していること。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-two-regional-iot-hubs"></a>2 つのリージョンに IoT ハブを作成する

このセクションでは、Azure Cloud Shell を使用して、**米国西部**と**米国東部**の 2 つのリージョンにテナント用の IoT ハブを作成します。


1. Azure Cloud Shell を使用して、[az group create](/cli/azure/group#az-group-create) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

    次の例では、*contoso-us-resource-group* という名前のリソース グループを *eastus* リージョンに作成します。 この記事で作成するすべてのリソースについて、このグループを使用することをお勧めします。 そうすれば、終わった後のクリーンアップが簡単になります。

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Azure Cloud Shell で [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) コマンドを使用して、**eastus** リージョンに IoT ハブを作成します。 その IoT ハブは、*contoso-us-resource-group* に追加されます。

    次の例では、*contoso-east-hub* という名前の IoT ハブを場所 *eastus* に作成します。 **contoso-east-hub** の代わりに、独自の一意のハブ名を使用する必要があります。

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    このコマンドが完了するまでに数分かかる場合があります。

3. Azure Cloud Shell で [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) コマンドを使用して、**westus** リージョンに IoT ハブを作成します。 この IoT ハブも、*contoso-us-resource-group* に追加されます。

    次の例では、*contoso-west-hub* という名前の IoT ハブを場所 *westus* に作成します。 **contoso-west-hub** の代わりに、独自の一意のハブ名を使用する必要があります。

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    このコマンドが完了するまでに数分かかる場合があります。



## <a name="create-the-multitenant-enrollment"></a>マルチテナントの登録を作成する

このセクションでは、テナント デバイス用の新しい登録グループを作成します。  

わかりやすくするため、この記事では[対称キーの構成証明](concepts-symmetric-key-attestation.md)を登録で使用します。 ソリューションをさらに安全にするには、信頼チェーンで [X.509 証明書構成証明](concepts-security.md#x509-certificates)を使用することを検討してください。

1. [Azure portal](https://portal.azure.com) にサインインし、Device Provisioning Services のインスタンスを開きます。

2. **[登録を管理します]** タブを選択し、ページの上部にある **[登録グループの追加]** ボタンをクリックします。 

3. **[登録グループの追加]** で、次の情報を入力して、 **[保存]** ボタンをクリックします。

    **[グループ名]** : 「**contoso-us-devices**」と入力します。

    **[構成証明の種類]** : **[対称キー]** を選択します。

    **[キーの自動生成]** : このチェック ボックスは既にオンになっているはずです。

    **[デバイスをハブに割り当てる方法を選択してください]** : **[最短待機時間]** を選択します。

    ![対称キー構成証明にマルチテナントの登録グループを追加する](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. **[登録グループの追加]** で、 **[Link a new IoT hub]\(新しい IoT ハブにリンクする\)** をクリックして両方のリージョンのハブをリンクします。

    **サブスクリプション**:複数のサブスクリプションがある場合は、リージョンの IoT ハブを作成したサブスクリプションを選択します。

    **[IoT ハブ]** : 作成したリージョン ハブのいずれかを選択します。

    **[アクセス ポリシー]:** **[iothubowner]** を選択します。

    ![リージョンの IoT ハブをプロビジョニング サービスとリンクする](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. 両方のリージョンの IoT ハブをリンクした後、それらを登録グループに対して選択し、 **[保存]** をクリックして、登録用のリージョン IoT ハブ グループを作成する必要があります。

    ![登録用のリージョン ハブ グループを作成する](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. 登録を保存した後、もう一度開き、 **[プライマリ キー]** を書き留めておきます。 キーを生成するには、最初に登録を保存する必要があります。 このキーは、後で両方のシミュレートされたデバイスに対する一意のデバイス キーを生成するために使用されます。


## <a name="create-regional-linux-vms"></a>リージョンの Linux VM を作成する

このセクションでは、2 つのリージョン Linux 仮想マシン (VM) を作成します。 これらの VM は、各リージョンからデバイス シミュレーション サンプルを実行して、両方のリージョンからのテナント デバイスのデバイス プロビジョニングをデモンストレーションします。

クリーンアップを容易にするため、これらの VM は、作成された IoT ハブを格納しているのと同じリソース グループ *contoso-us-resource-group* に追加されます。 ただし、VM は別のリージョン (**米国西部**と**米国東部**) で実行されます。

1. Azure Cloud Shell で次のコマンドを実行して、**米国東部**リージョンの VM を作成します。実行前に、コマンドの次のパラメーターを変更します。

    **--name**: 自分の**米国東部**リージョンのデバイスの VM に対する一意名を入力します。 

    **--admin-username**: 自分の管理者ユーザー名を使用します。

    **--admin-password**: 自分の管理者パスワードを使用します。

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceEast \
    --location eastus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    このコマンドが完了するまでに数分かかります。 コマンドが完了したら、米国東部リージョンの VM の **publicIpAddress** の値を書き留めておきます。

1. Azure Cloud Shell で次のコマンドを実行して、**米国西部**リージョンの VM を作成します。実行前に、コマンドの次のパラメーターを変更します。

    **--name**: 自分の**米国西部**リージョンのデバイスの VM に対する一意名を入力します。 

    **--admin-username**: 自分の管理者ユーザー名を使用します。

    **--admin-password**: 自分の管理者パスワードを使用します。

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceWest \
    --location westus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    このコマンドが完了するまでに数分かかります。 コマンドが完了したら、米国西部リージョンの VM の **publicIpAddress** の値を書き留めておきます。

1. コマンド ライン シェルを 2 つ開きます。 SSH を使用して各シェルでリージョン VM の 1 つに接続します。 

    管理者のユーザー名と、VM について書き留めたパブリック IP アドレスを、SSH へのパラメーターとして渡します。 メッセージが表示されたら、管理者のパスワードを入力します。

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK の開発環境を準備する

このセクションでは、各 VM で Azure IoT C SDK を複製します。 SDK には、各リージョンからのテナントのデバイスのプロビジョニングをシミュレートするサンプルが含まれています。


1. VM ごとに、次のコマンドを使用して **Cmake**、**g++** 、**gcc**、[Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) をインストールします。

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```


1. 両方の VM で、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) を複製します。

    ```bash
    cd ~/
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    この操作は、完了するまでに数分かかります。

1. 両方の VM で、リポジトリの内部に新しい **cmake** フォルダーを作成して、そのフォルダーに移動します。

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. 両方の VM で、次のコマンドを実行して、開発クライアント プラットフォームに固有の SDK のバージョンをビルドします。 

    ```bash
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    ビルドが成功すると、最後のいくつかの出力行は次のようになります。

    ```bash
    -- IoT Client SDK Version = 1.2.9
    -- Provisioning client ON
    -- Provisioning SDK Version = 1.2.9
    -- target architecture: x86_64
    -- Checking for module 'libcurl'
    --   Found libcurl, version 7.58.0
    -- Found CURL: curl
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- iothub architecture: x86_64
    -- target architecture: x86_64
    -- Configuring done
    -- Generating done
    -- Build files have been written to: /home/contosoadmin/azure-iot-sdk-c/cmake
    ```    


## <a name="derive-unique-device-keys"></a>一意のデバイス キーを派生させる

グループ登録で対称キーの構成証明を使用する場合、登録グループのキーを直接は使用しません。 代わりに、[Group Enrollments with symmetric keys](concepts-symmetric-key-attestation.md#group-enrollments) (対称キーでのグループ登録) に関するページで説明されているように、デバイスごとに一意の派生キーを作成します。

デバイス キーを生成するには、グループのマスター キーを使用してデバイスに対する一意の登録 ID の [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) を計算し、結果を Base64 形式に変換します。

デバイス コードにはグループのマスター キーを含めないでください。

Bash シェルの例を使用し、**openssl** を使用して、各デバイスのデバイス派生キーを作成します。

- **KEY** の値を、前に書き留めた登録の**プライマリ キー**に置き換えます。

- **REG_ID** の値を、各デバイスの独自の一意登録 ID に置き換えます。 両方の ID を定義するには、小文字の英字、数字、ダッシュ ('-') 文字を使用します。

*contoso-simdevice-east* のデバイス キー生成の例:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

*contoso-simdevice-west* のデバイス キー生成の例:

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


各テナント デバイスは、テナントの IoT ハブに対するプロビジョニングの間に、派生デバイス キーと一意の登録 ID を使用して、登録グループで対称キー構成証明を実行します。




## <a name="simulate-the-devices-from-each-region"></a>各リージョンからデバイスをシミュレートする


このセクションでは、両方のリージョン VM について、Azure IoT C SDK のプロビジョニング サンプルを更新します。 

サンプル コードでは、Device Provisioning Services のインスタンスにプロビジョニング要求を送信するデバイスのブート シーケンスがシミュレートされます。 ブート シーケンスにより、デバイスが認識され、待機時間に基づいて最も近い IoT ハブに割り当てられます。

1. Azure portal で、Device Provisioning Service の **[概要]** タブをクリックし、**[_ID スコープ_]** の値を書き留めます。

    ![ポータルのブレードから Device Provisioning サービスのエンドポイント情報を抽出](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. 両方の VM で **~/azure-iot-sdk-c/provisioning\_client/samples/prov\_dev\_client\_sample/prov\_dev\_client\_sample.c** を開いて編集します。

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. 定数 `id_scope` を探し、以前にコピーした **ID スコープ**の値で置き換えます。 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. 同じファイル内で `main()` 関数の定義を探します。 次に示すように、登録グループの構成証明の方法に合わせて `hsm_type` 変数が `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` に設定されていることを確認します。 

    両方の VM でファイルに変更を保存します。

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. **prov\_dev\_client\_sample.c** で、コメントになっている `prov_dev_set_symmetric_key_info()` の呼び出しを両方の VM で探します。

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    関数呼び出しのコメントを解除し、各デバイスについて、プレースホルダーの値 (山かっこを含む) を一意の登録 ID とデバイス派生キーに置き換えます。 以下に示したキーはあくまでも例です。 先ほど生成したキーを使用してください。

    米国東部:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    米国西部:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

    ファイルを保存します。

1. 両方の VM で、次に示すサンプル フォルダーに移動し、サンプルをビルドします。

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. ビルドが成功したら、両方の VM で **prov\_dev\_client\_sample.exe** を実行して、各リージョンからテナント デバイスをシミュレートします。 シミュレートされたデバイスのリージョンに最も近いテナント IoT ハブに、各デバイスが割り当てられることに注意してください。

    シミュレーションを実行する:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    米国東部の VM からの出力例:

    ```bash
    contosoadmin@ContosoSimDeviceEast:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-east-hub.azure-devices.net, deviceId: contoso-simdevice-east
    Press enter key to exit:

    ```

    米国西部の VM からの出力例:
    ```bash
    contosoadmin@ContosoSimDeviceWest:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-west-hub.azure-devices.net, deviceId: contoso-simdevice-west
    Press enter key to exit:
    ```



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











