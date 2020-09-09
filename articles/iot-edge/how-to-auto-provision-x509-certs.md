---
title: X.509 証明書を使用して DPS でデバイスを自動プロビジョニングする - Azure IoT Edge | Microsoft Docs
description: X.509 証明書を使用して、デバイス プロビジョニング サービスで Azure IoT Edge の自動デバイス プロビジョニングをテストします
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 04/09/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c54690645286a4fceb3fd786d85652b1cf77d7aa
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86260028"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>X.509 証明書を使用して IoT Edge デバイスを作成およびプロビジョニングする

[Azure IoT Hub Device Provisioning Service (DPS)](../iot-dps/index.yml) では、X.509 証明書を使用して IoT Edge デバイスを自動的にプロビジョニングできます。 自動プロビジョニングの処理に慣れていない場合は、「[自動プロビジョニングの概念](../iot-dps/concepts-auto-provisioning.md)」を読んでから先に進んでください。

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

> [!NOTE]
> 現時点では、libiothsm の制限により、2050 年 1 月 1 日以降に有効期限が切れた証明書は使用できません。

### <a name="use-test-certificates"></a>テスト証明書を使用する

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

DPS 内に登録を作成するときに、**デバイス ツインの初期状態**を宣言する機会があります。 デバイス ツインでは、ソリューションで必要な任意のメトリック (リージョン、環境、場所、デバイスの種類など) によってデバイスをグループ化するためのタグを設定できます。 これらのタグは、[自動展開](how-to-deploy-at-scale.md)を作成するために使用されます。

Device Provisioning Service での登録の詳細については、[デバイス登録の管理方法](../iot-dps/how-to-manage-enrollments.md)に関するページを参照してください。

   > [!TIP]
   > Azure CLI では、[登録](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment)または[登録グループ](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group)を作成し、**Edge 対応**フラグを使用して、デバイスまたはデバイスのグループが IoT Edge デバイスになるよう指定できます。

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

DPS 内に登録を作成するときに、**デバイス ツインの初期状態**を宣言する機会があります。 デバイス ツインでは、ソリューションで必要な任意のメトリック (リージョン、環境、場所、デバイスの種類など) によってデバイスをグループ化するためのタグを設定できます。 これらのタグは、[自動展開](how-to-deploy-at-scale.md)を作成するために使用されます。

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

DPS による x.509 のプロビジョニングは、IoT Edge バージョン 1.0.9 以降でのみサポートされています。

デバイスをプロビジョニングする際には、次の情報が必要になります。

* DPS の **ID スコープ**値。 Azure portal で、使用している DPS インスタンスの概要ページから、この値を取得できます。
* デバイス上のデバイス ID 証明書チェーン ファイル。
* デバイス上のデバイス ID キー ファイル。
* オプションの登録 ID (指定しない場合、デバイス ID 証明書内の共通名から取得されます)。

### <a name="linux-device"></a>Linux デバイス

以下のリンクを使用して、デバイスのアーキテクチャに適したコマンドを使ってデバイスに Azure IoT Edge ランタイムをインストールします。 セキュリティ デーモンの構成に関するセクションまで進んだら、X.509 の手動プロビジョニングではなく自動プロビジョニング用に IoT Edge ランタイムを構成します。 この記事にあるここまでのセクションを完了すると、必要なすべての情報と証明書ファイルが用意できます。

[Linux に Azure IoT Edge ランタイムをインストールする](how-to-install-iot-edge-linux.md)

X.509 証明書とキー情報を config.yaml ファイルに追加する場合、パスはファイル URI として指定する必要があります。 次に例を示します。

* `file:///<path>/identity_certificate_chain.pem`
* `file:///<path>/identity_key.pem`

構成ファイル内の X.509 自動プロビジョニングに関するセクションは、次のようになります。

```yaml
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
```

`scope_id`、`identity_cert`、`identity_pk` のプレースホルダー値を、使用している DPS インスタンスのスコープ ID と、デバイス上の証明書チェーンおよびキー ファイルの場所を表す URI に置き換えます。 必要に応じてデバイスの `registration_id` を指定するか、この行をコメントアウトして、デバイスを ID 証明書の CN 名で登録します。

config.yaml ファイルを更新した後は、必ずセキュリティ デーモンを再起動します。

```bash
sudo systemctl restart iotedge
```

### <a name="windows-device"></a>Windows デバイス

ID 証明書チェーンと ID キーを生成したデバイスに IoT Edge ランタイムをインストールします。 IoT Edge ランタイムを、手動プロビジョニングではなく、自動プロビジョニング用に構成します。

Windows での IoT Edge のインストールの詳細については、コンテナーの管理や IoT Edge の更新などのタスクに関する前提条件や手順を含めて、「[Windows に Azure IoT Edge ランタイムをインストールする](how-to-install-iot-edge-windows.md)」を参照してください。

1. 管理者モードで PowerShell ウィンドウを開きます。 IoT Edge をインストールするときは、PowerShell (x86) ではなく、PowerShell の AMD64 セッションを必ず使用してください。

1. **Deploy-IoTEdge** コマンドを使用して、ご使用の Windows コンピューターがサポートされているバージョンであることを確認し、コンテナー機能をオンに設定した後、moby ランタイムと IoT Edge ランタイムをダウンロードします。 コマンドの既定値では Windows コンテナーが使用されます。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. この時点で、IoT Core デバイスが自動的に再起動することがあります。 その他の Windows 10 または Windows Server デバイスでは、再起動が求められることがあります。 その場合、デバイスをすぐに再起動してください。 デバイスが起動されたら、管理者として PowerShell を再実行します。

1. **Initialize-IoTEdge** コマンドを使用して、お使いのマシンに IoT Edge ランタイムを構成します。 自動プロビジョニングを使用するために `-Dps` フラグを使用する場合を除き、このコマンドでは、手動プロビジョニングが既定で設定されます。

   `{scope_id}`、`{identity cert chain path}`、`{identity key path}` のプレースホルダー値を、使用している DPS インスタンスの適切な値およびデバイス上のファイル パスに置き換えます。 登録 ID を指定する場合は、`-RegistrationId {registration_id}` も含めて、プレースホルダーを適宜置き換えます。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >config.yaml ファイルには、証明書とキー情報がファイル URI として格納されます。 ただし、Initialize-IoTEdge コマンドを使用すると、この書式設定の手順が処理されるため、デバイス上の証明書ファイルとキー ファイルの絶対パスを指定できます。

## <a name="verify-successful-installation"></a>インストールの成功を確認する

ランタイムが正常に起動されたら、IoT Hub にアクセスし、デバイスに IoT Edge モジュールを展開できます。

Device Provisioning Service で作成した個々の登録が使用されたことを確認できます。 Azure portal で Device Provisioning Service インスタンスに移動します。 作成した個々の登録の詳細を開きます。 登録の状態が**割り当て**られており、デバイス ID が表示されています。

ランタイムが正常にインストールされ、起動されたことを確認するには、デバイスで次のコマンドを使用します。

### <a name="linux-device"></a>Linux デバイス

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
