---
title: X.509 証明書を使用して DPS でデバイスを自動プロビジョニングする - Azure IoT Edge | Microsoft Docs
description: X.509 証明書を使用して、デバイス プロビジョニング サービスで Azure IoT Edge の自動デバイス プロビジョニングをテストします
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
ms.openlocfilehash: 44ea6546eb2099165071fd493ec8f890820c0688
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199829"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>X.509 証明書を使用して IoT Edge デバイスを作成およびプロビジョニングする

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

[Azure IoT Hub Device Provisioning Service (DPS)](../iot-dps/index.yml) では、X.509 証明書を使用して IoT Edge デバイスを自動的にプロビジョニングできます。 自動プロビジョニングの処理に慣れていない場合は、[プロビジョニング](../iot-dps/about-iot-dps.md#provisioning-process)の概要を読んでから先に進んでください。

この記事では、次の手順に従って、IoT Edge デバイスで X.509 証明書を使用して Device Provisioning Service への登録を作成する方法について説明します。

* 証明書とキーを生成する。
* デバイスの個別登録を作成するか、一連のデバイスに対してグループ登録を作成する。
* IoT Edge ランタイムをインストールし、デバイスを IoT Hub に登録する。

X.509 証明書を構成証明メカニズムとして使用することは、実稼働環境を拡張し、デバイスのプロビジョニングを簡素化するための優れた方法です。 通常、X.509 証明書は信頼する証明書チェーンに配置されます。 自己署名証明書または信頼されたルート証明書から開始して、チェーン内の各証明書によって、それよりも 1 つ下位にある証明書が署名されます。 このパターンにより、ルート証明書から各中間証明書を経由して、デバイスにインストールされた最終的な "リーフ" 証明書に至る、信頼する委任チェーンが作成されます。

## <a name="prerequisites"></a>前提条件

* アクティブな IoT Hub。
* IoT Edge デバイスとなる物理デバイスまたは仮想デバイス。
* [Git](https://git-scm.com/download/) の最新バージョンがインストールされている。
* 自分の IoT ハブにリンクした、Azure 内の IoT Hub Device Provisioning Service のインスタンス。
  * Device Provisioning Service インスタンスがない場合は、[IoT Hub DPS を設定する](../iot-dps/quick-setup-auto-provision.md)手順に従ってください。
  * Device Provisioning Service を実行した後、概要ページから **[ID スコープ]** の値をコピーします。 この値は、IoT Edge ランタイムを構成するときに使用します。

## <a name="generate-device-identity-certificates"></a>デバイス ID 証明書を生成する

デバイス ID 証明書は、信頼する証明書チェーンを介して最上位の X.509 証明機関 (CA) 証明書につながるリーフ証明書です。 デバイス ID 証明書の共通名 (CN) は、IoT ハブ内のデバイスに付けるデバイス ID に設定されている必要があります。

デバイス ID 証明書は、IoT Edge デバイスのプロビジョニングと、Azure IoT Hub でのデバイス認証を行うためにのみ使用されます。 証明書に署名するためのものではありません (IoT Edge デバイスから確認のためにモジュールまたはリーフ デバイスに提示される CA 証明書とは異なります)。 詳細については、[Azure IoT Edge 証明書の使用方法に関する詳細](iot-edge-certs.md)のページを参照してください。

デバイス ID 証明書を作成すると、2 つのファイルができます。証明書の公開部分を含む .cer または pem ファイルと、証明書の秘密キーを含む .cer または pem ファイルです。 DPS でグループ登録を使用する予定がある場合は、同じ信頼する証明書チェーン内にある中間証明書またはルート CA 証明書の公開部分も必要です。

X.509 を使用して自動プロビジョニングを設定するには、次のファイルが必要です。

* デバイス ID 証明書とその秘密キー証明書。 個別登録を作成すると、デバイス ID 証明書が DPS にアップロードされます。 秘密キーは IoT Edge ランタイムに渡されます。
* 完全なチェーン証明書。少なくともデバイス ID と中間証明書が含まれている必要があります。 完全なチェーン証明書は IoT Edge ランタイムに渡されます。
* 信頼する証明書チェーンからの中間証明書またはルート CA 証明書。 グループ登録を作成すると、この証明書は DPS にアップロードされます。

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
> [!NOTE]
> 現時点では、libiothsm の制限により、2038 年 1 月 1 日以降に有効期限が切れる証明書は使用できません。

:::moniker-end

### <a name="use-test-certificates-optional"></a>テスト証明書を使用する (省略可能)

新しい ID 証明書を作成するために使用できる証明機関がない場合、このシナリオを試すには、Azure IoT Edge の Git リポジトリにあるスクリプトを使用してテスト証明書を生成できます。 それらの証明書は開発テスト専用に設計されているため、運用環境では使用できません。

テスト証明書を作成するには、「[IoT Edge デバイスの機能をテストするためのデモ用の証明書を作成する](how-to-create-test-certificates.md)」の手順に従います。 証明書生成スクリプトを設定してルート CA 証明書を作成するために必要とされる、2 つのセクションを完了します。 次に、手順に従ってデバイス ID 証明書を作成します。 完了すると、次の証明書チェーンとキーの組が作成されます。

Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

これらの証明書の両方が IoT Edge デバイス上に必要です。 DPS で個別登録を使用する場合は、.cert.pem ファイルをアップロードします。 DPS でグループ登録を使用する場合は、同じ信頼する証明書チェーン内に、アップロードする中間証明書またはルート CA 証明書も必要です。 デモ用の証明書を使用している場合は、グループ登録用に `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` 証明書を使用します。

## <a name="create-a-dps-individual-enrollment"></a>DPS 個別登録を作成する

生成された証明書とキーを使用して、単一の IoT Edge デバイスに対して DPS で個別登録を作成します。 個別登録では、デバイスの ID 証明書の公開部分が渡され、デバイス上の証明書と照合されます。

複数の IoT Edge デバイスをプロビジョニングする場合は、次のセクション「[DPS グループ登録を作成する](#create-a-dps-group-enrollment)」の手順に従います。

DPS 内に登録を作成するときに、**デバイス ツインの初期状態** を宣言する機会があります。 デバイス ツインでは、ソリューションで必要な任意のメトリック (リージョン、環境、場所、デバイスの種類など) によってデバイスをグループ化するためのタグを設定できます。 これらのタグは、[自動展開](how-to-deploy-at-scale.md)を作成するために使用されます。

Device Provisioning Service での登録の詳細については、[デバイス登録の管理方法](../iot-dps/how-to-manage-enrollments.md)に関するページを参照してください。

   > [!TIP]
   > Azure CLI では、[登録](/cli/azure/ext/azure-iot/iot/dps/enrollment)または [登録グループ](/cli/azure/ext/azure-iot/iot/dps/enrollment-group)を作成し、**Edge 対応** フラグを使用して、デバイスまたはデバイスのグループが IoT Edge デバイスになるよう指定できます。

1. [Azure portal](https://portal.azure.com) で、IoT Hub Device Provisioning Service のインスタンスに移動します。

1. **[設定]** の下の **[登録の管理]** を選択します。

1. **[Add individual enrollment]\(個別登録の追加\)** を選択し、登録を構成する次の手順を完了します。  

   * **メカニズム**: **[X.509]** を選択します。

   * **[プライマリ証明書の .pem ファイルまたは .cer ファイル]** : デバイス ID 証明書からパブリック ファイルをアップロードします。 スクリプトを使用してテスト証明書を生成した場合は、次のファイルを選択します。

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **[IoT Hub のデバイス ID]** : 必要に応じて、デバイス ID を指定します。 デバイス ID を使用して、個々のデバイスをモジュール展開のターゲットにすることができます。 デバイス ID を指定しない場合は、X.509 証明書内の共通名 (CN) が使用されます。

   * **[IoT Edge デバイス]** : **[True]** を選択して、その登録が IoT Edge デバイス用のものであることを宣言します。

   * **[このデバイスを割り当てることができる IoT ハブを選択する]** : デバイスの接続先になるリンクされた IoT ハブを選択します。 複数のハブを選択でき、デバイスは、選択した割り当てポリシーに従ってそれらのハブの 1 つに割り当てられます。

   * **[デバイス ツインの初期状態]** : 必要に応じて、デバイス ツインに追加するタグ値を追加します。 タグを使用して、デバイス グループを自動展開のターゲットにすることができます。 次に例を示します。

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. **[保存]** を選択します。

これで、このデバイスの登録が存在しているので、IoT Edge ランタイムによってインストール時にデバイスを自動的にプロビジョニングできます。 「[IoT Edge ランタイムをインストールする](#install-the-iot-edge-runtime)」セクションに進み、IoT Edge デバイスを設定します。

## <a name="create-a-dps-group-enrollment"></a>DPS グループ登録を作成する

生成された証明書とキーを使用して、複数の IoT Edge デバイスに対して DPS でグループ登録を作成します。 グループ登録では、個々のデバイス ID 証明書の生成に使用された信頼する証明書チェーンから、中間証明書またはルート CA 証明書を使用します。

または、単一の IoT Edge デバイスをプロビジョニングする場合は、前のセクション「[DPS 個別登録を作成する](#create-a-dps-individual-enrollment)」の手順に従います。

DPS 内に登録を作成するときに、**デバイス ツインの初期状態** を宣言する機会があります。 デバイス ツインでは、ソリューションで必要な任意のメトリック (リージョン、環境、場所、デバイスの種類など) によってデバイスをグループ化するためのタグを設定できます。 これらのタグは、[自動展開](how-to-deploy-at-scale.md)を作成するために使用されます。

### <a name="verify-your-root-certificate"></a>ルート証明書を確認する

登録グループを作成するときに、確認済みの証明書を使用することもできます。 ルート証明書の所有権があることを示すことによって、DPS で証明書を確認できます。 詳細については、[X.509 CA 証明書の所有証明を行う方法](../iot-dps/how-to-verify-certificates.md)に関するページを参照してください。

1. [Azure portal](https://portal.azure.com) で、IoT Hub Device Provisioning Service のインスタンスに移動します。

1. 左側のメニューから **[証明書]** を選択します。

1. **[追加]** を選択して新しい証明書を追加します。

1. 証明書のフレンドリ名を入力し、X.509 証明書の公開部分を表す .cer または .pem ファイルを参照します。

   デモ用の証明書を使用している場合は、`<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` 証明書をアップロードします。

1. **[保存]** を選択します。

1. これで、その証明書が **[証明書]** ページに表示されます。 それを選択して証明書の詳細を開きます。

1. **[確認コードを生成します]** を選択した後、生成されたコードをコピーします。

1. 独自の CA 証明書を入手したか、デモ用の証明書を使用しているかにかかわらず、IoT Edge リポジトリに用意されている検証ツールを使用して、所有証明を確認することができます。 検証ツールでは、CA 証明書を使用して、指定された確認コードをサブジェクト名として持つ新しい証明書に署名します。

   * Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. Azure portal の同じ証明書詳細ページで、新しく生成された検証証明書をアップロードします。

1. **[認証]** を選択します。

### <a name="create-enrollment-group"></a>登録グループを作成する

Device Provisioning Service での登録の詳細については、[デバイス登録の管理方法](../iot-dps/how-to-manage-enrollments.md)に関するページを参照してください。

1. [Azure portal](https://portal.azure.com) で、IoT Hub Device Provisioning Service のインスタンスに移動します。

1. **[設定]** の下の **[登録の管理]** を選択します。

1. **[登録グループの追加]** を選択し、登録を構成する以下の手順を完了します。

   * **[グループ名]** : このグループ登録の覚えやすい名前を入力します。

   * **[構成証明の種類]** : **[Certificate]** を選択します。

   * **[IoT Edge デバイス]** : **[True]** を選択します。 グループ登録の場合、すべてのデバイスを IoT Edge デバイスにする必要があります。そうしない場合、いずれも IoT Edge デバイスにすることはできません。

   * **[証明書の種類]** : DPS に格納された確認済みの CA 証明書がある場合は、 **[CA 証明書]** を選択します。または、この登録だけのために新しいファイルをアップロードする場合は、 **[中間証明書]** を選択します。

   * **[プライマリ証明書]** : 直前のセクションで CA 証明書を選択した場合は、ドロップダウン リストから該当する証明書を選択します。 中間証明書を選択した場合は、デバイス ID 証明書の生成に使用された信頼する証明書チェーン内の CA 証明書から、パブリック ファイルをアップロードします。

   * **[このデバイスを割り当てることができる IoT ハブを選択する]** : デバイスの接続先になるリンクされた IoT ハブを選択します。 複数のハブを選択でき、デバイスは、選択した割り当てポリシーに従ってそれらのハブの 1 つに割り当てられます。

   * **[デバイス ツインの初期状態]** : 必要に応じて、デバイス ツインに追加するタグ値を追加します。 タグを使用して、デバイス グループを自動展開のターゲットにすることができます。 次に例を示します。

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. **[保存]** を選択します。

これで、このデバイスの登録が存在しているので、IoT Edge ランタイムによってインストール時にデバイスを自動的にプロビジョニングできます。 次のセクションに進み、IoT Edge デバイスをセットアップします。

## <a name="install-the-iot-edge-runtime"></a>IoT Edge ランタイムをインストールする

IoT Edge ランタイムはすべての IoT Edge デバイスに展開されます。 そのコンポーネントはコンテナー内で実行されるため、デバイスに追加のコンテナーを展開して、Edge でコードを実行できるようにすることができます。

[Azure IoT Edge ランタイムのインストール](how-to-install-iot-edge.md)に関するページにある手順に従い、その後、この記事に戻ってデバイスをプロビジョニングします。

DPS による x.509 のプロビジョニングは、IoT Edge バージョン 1.0.9 以降でのみサポートされています。

## <a name="configure-the-device-with-provisioning-information"></a>プロビジョニング情報を使用してデバイスを構成する

ランタイムがデバイスにインストールされたら、デバイス プロビジョニング サービスと IoT Hub に接続するために使用される情報でデバイスを構成します。

次の情報を用意しておきます。

* DPS の **ID スコープ** 値。 Azure portal で、使用している DPS インスタンスの概要ページから、この値を取得できます。
* デバイス上のデバイス ID 証明書チェーン ファイル。
* デバイス上のデバイス ID キー ファイル。
* 登録 ID (省略可能)。 指定しない場合、ID はデバイス ID 証明書内の共通名から取得されます。

### <a name="linux-device"></a>Linux デバイス

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. IoT Edge デバイスで構成ファイルを開きます。

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. ファイルのプロビジョニング構成セクションを見つけます。 DPS X.509 証明書のプロビジョニング行をコメント解除し、他にもプロビジョニング行があれば、それらもコメント アウトするようにします。

   `provisioning:` の行の先頭には空白文字を入れず、入れ子の項目には 2 つの空白でインデントする必要があります。

   ```yml
   # DPS X.509 provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "x509"
   #   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. `scope_id`、`identity_cert`、`identity_pk` の値を DPS およびデバイス情報で更新します。

   X.509 証明書とキー情報を config.yaml ファイルに追加する場合、パスはファイル URI として指定する必要があります。 次に例を示します。

   `file:///<path>/identity_certificate_chain.pem`
   `file:///<path>/identity_key.pem`

1. 必要に応じて、デバイスに `registration_id` を指定します。 それ以外の場合は、その行をコメント アウトしたままにして、デバイスを ID 証明書の CN 名で登録します。

1. 必要に応じて、`always_reprovision_on_startup` または `dynamic_reprovisioning` の行を使用して、デバイスの再プロビジョニング動作を構成します。 起動時に再プロビジョニングするようにデバイスが設定されている場合、常に最初に DPS を使用してプロビジョニングが試行され、失敗した場合はプロビジョニングのバックアップにフォールバックします。 動的に再プロビジョニングするようにデバイスが設定されている場合、再プロビジョニング イベントが検出されると、IoT Edge が再起動し、再プロビジョニングされます。 詳細については、「[IoT Hub デバイスの再プロビジョニングの概念](../iot-dps/concepts-device-reprovision.md)」を参照してください。

1. config.yaml ファイルを保存して閉じます。

1. デバイスで行ったすべての構成の変更が反映されるように、IoT Edge ランタイムを再起動します。

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1. -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. IoT Edge のインストールの一部として提供されるテンプレート ファイルに基づいて、デバイスの構成ファイルを作成します。

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. IoT Edge デバイスで構成ファイルを開きます。

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. ファイルの **プロビジョニング** セクションを見つけます。 X.509 証明書を使用した DPS のプロビジョニング行をコメント解除し、他にもプロビジョニング行があれば、それらもコメント アウトするようにします。

   ```toml
   # DPS provisioning with X.509 certificate
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "x509"
   # registration_id = "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"

   identity_cert = "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

1. `id_scope`、`identity_cert`、`identity_pk` の値を DPS およびデバイス情報で更新します。

   ID 証明書の値は、ファイルの URI として指定することも、EST またはローカルの証明機関を使用して動的に発行することもできます。 使用するように選択した形式に基づいて、1 行だけコメント解除します。

   ID 秘密キーの値は、ファイル URI または PKCS#11 URI として指定できます。 使用するように選択した形式に基づいて、1 行だけコメント解除します。

   任意の PKCS#11 URI を使用する場合は、構成ファイルで **PKCS#11** セクションを見つけ、PKCS#11 構成に関する情報を提供します。

1. 必要に応じて、デバイスに `registration_id` を指定します。 それ以外の場合は、その行をコメント アウトしたままにして、デバイスを ID 証明書の共通名で登録します。

1. ファイルを保存して閉じます。

1. 加えた構成の変更を IoT Edge に適用します。

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

### <a name="windows-device"></a>Windows デバイス

1. 管理者モードで PowerShell ウィンドウを開きます。 IoT Edge をインストールするときは、PowerShell (x86) ではなく、PowerShell の AMD64 セッションを必ず使用してください。

1. **Initialize-IoTEdge** コマンドを使用して、お使いのマシンに IoT Edge ランタイムを構成します。 このコマンドでは、Windows コンテナーを使用した手動プロビジョニングが既定で設定されます。そのため、`-DpsX509` フラグを使用し、X.509 証明書認証と共に自動プロビジョニングを使用します。

   `{scope_id}`、`{identity cert chain path}`、`{identity key path}` のプレースホルダー値を、使用している DPS インスタンスの適切な値およびデバイス上のファイル パスに置き換えます。

   ID 証明書の CN 名以外としてデバイス ID を設定する場合、`-RegistrationId {registration_id}` を追加します。

   Windows で Linux コンテナーを使用している場合、`-ContainerOs Linux` パラメーターを追加します。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsX509 -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >構成ファイルには、証明書とキー情報がファイル URI として格納されます。 ただし、Initialize-IoTEdge コマンドを使用すると、この書式設定の手順が処理されるため、デバイス上の証明書ファイルとキー ファイルの絶対パスを指定できます。

## <a name="verify-successful-installation"></a>インストールの成功を確認する

ランタイムが正常に起動されたら、IoT Hub にアクセスし、デバイスに IoT Edge モジュールを展開できます。

Device Provisioning Service で作成した個々の登録が使用されたことを確認できます。 Azure portal で Device Provisioning Service インスタンスに移動します。 作成した個々の登録の詳細を開きます。 登録の状態が **割り当て** られており、デバイス ID が表示されています。

ランタイムが正常にインストールされ、起動されたことを確認するには、デバイスで次のコマンドを使用します。

### <a name="linux-device"></a>Linux デバイス

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

IoT Edge サービスの状態を確認します。

```cmd/sh
systemctl status iotedge
```

サービス ログを調べます。

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

実行中のモジュールを一覧表示します。

```cmd/sh
iotedge list
```
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

IoT Edge サービスの状態を確認します。

```cmd/sh
sudo iotedge system status
```

サービス ログを調べます。

```cmd/sh
sudo iotedge system logs
```

実行中のモジュールを一覧表示します。

```cmd/sh
sudo iotedge list
```
:::moniker-end

### <a name="windows-device"></a>Windows デバイス

IoT Edge サービスの状態を確認します。

```powershell
Get-Service iotedge
```

サービス ログを調べます。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

実行中のモジュールを一覧表示します。

```powershell
iotedge list
```

## <a name="next-steps"></a>次のステップ

Device Provisioning Service の登録プロセスでは、新しいデバイスをプロビジョニングするときに、デバイス ID とデバイス ツイン タグを同時に設定できます。 これらの値を使用して、個々のデバイスまたはデバイス グループをデバイスの自動管理で使用できます。 使用方法については、「[Azure Portal を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-at-scale.md)」または「[Azure CLI を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-cli-at-scale.md)」を参照してください。
