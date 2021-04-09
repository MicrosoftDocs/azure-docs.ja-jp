---
title: 対称キーの構成証明を使用してデバイスをプロビジョニングする - Azure IoT Edge
description: 対称キーの構成証明を使用して、デバイス プロビジョニング サービスで Azure IoT Edge の自動デバイス プロビジョニングをテストします
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: eb5cbc2f2db0ba9f92a637c7e9a905d2f746880a
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200834"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>対称キーの構成証明を使用して IoT Edge デバイスを作成およびプロビジョニングする

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Azure IoT Edge デバイスは、[Device Provisioning Service](../iot-dps/index.yml) を使用して、Edge に対応していないデバイスと同じように自動プロビジョニングできます。 自動プロビジョニングの処理に慣れていない場合は、[プロビジョニング](../iot-dps/about-iot-dps.md#provisioning-process)の概要を読んでから先に進んでください。

この記事では、次の手順に従って、IoT Edge デバイスで対称キーの構成証明を使用して Device Provisioning Service の個別またはグループ登録を作成する方法について説明します。

* IoT Hub Device Provisioning Service (DPS) のインスタンスを作成する。
* デバイスの登録を作成する。
* IoT Edge ランタイムをインストールし、IoT Hub に接続する。

対称キーの構成証明は、Device Provisioning Service インスタンスを使用してデバイスを認証する簡単なアプローチです。 この構成証明の方法では、初めてデバイスのプロビジョニングを行う開発者または厳密なセキュリティ要件がない開発者に対して、"Hello world" エクスペリエンスを提示します。 [TPM](../iot-dps/concepts-tpm-attestation.md) または [X.509 証明書](../iot-dps/concepts-x509-attestation.md)を使用するデバイス構成証明は、より安全であり、さらに厳格なセキュリティ要件に対して使用する必要があります。

## <a name="prerequisites"></a>前提条件

* アクティブな IoT Hub
* 物理デバイスまたは仮想デバイス

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Azure IoT Hub Device Provisioning Service を設定する

Azure 内に IoT Hub Device Provisioning Service の新しいインスタンスを作成し、それを自分の IoT Hub にリンクします。 手順については、[IoT Hub DPS の設定](../iot-dps/quick-setup-auto-provision.md)に関する記事を参照してください。

Device Provisioning Service を実行した後、概要ページから **[ID スコープ]** の値をコピーします。 この値は、IoT Edge ランタイムを構成するときに使用します。

## <a name="choose-a-unique-registration-id-for-the-device"></a>デバイスの一意の登録 ID を選択する

各デバイスを識別する一意の登録 ID を定義する必要があります。 デバイスの MAC アドレス、シリアル番号、または何らかの固有の情報を使用できます。

この例では、MAC アドレスとシリアル番号の組み合わせを使用して、次のような登録 ID の文字列を形成します: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`。

デバイスの一意の登録 ID を作成します。 有効な文字は、小文字の英字、数字、ダッシュ ('-') です。

## <a name="create-a-dps-enrollment"></a>DPS の登録を作成する

デバイスの登録 ID を使用して、DPS で個別登録を作成します。

DPS 内に登録を作成するときに、**デバイス ツインの初期状態** を宣言する機会があります。 デバイス ツインでは、ソリューションで必要な任意のメトリック (リージョン、環境、場所、デバイスの種類など) によってデバイスをグループ化するためのタグを設定できます。 これらのタグは、[自動展開](how-to-deploy-at-scale.md)を作成するために使用されます。

> [!TIP]
> 対称キーの構成証明を使用する場合、グループの登録も可能です。その場合は、個別の登録と同じ決定を行います。

1. [Azure portal](https://portal.azure.com) で、IoT Hub Device Provisioning Service のインスタンスに移動します。

1. **[設定]** の下の **[登録の管理]** を選択します。

1. **[Add individual enrollment]\(個別登録の追加\)** を選択し、登録を構成する次の手順を完了します。  

   1. **[メカニズム]** で **[対称キー]** を選択します。

   1. **[キーの自動生成]** チェック ボックスをオンにします。

   1. デバイス用に作成した **登録 ID** を指定します。

   1. 必要に応じて、**IoT Hub のデバイス ID** を指定します。 デバイス ID を使用して、個々のデバイスをモジュール展開のターゲットにすることができます。 デバイス ID を指定しなかった場合は、登録 ID が使用されます。

   1. **[True]** を選択して、その登録が IoT Edge デバイス用のものであることを宣言します。 グループ登録の場合、すべてのデバイスを IoT Edge デバイスにする必要があります。そうしない場合、いずれも IoT Edge デバイスにすることはできません。

      > [!TIP]
      > Azure CLI では、[登録](/cli/azure/ext/azure-iot/iot/dps/enrollment)または [登録グループ](/cli/azure/ext/azure-iot/iot/dps/enrollment-group)を作成し、**Edge 対応** フラグを使用して、デバイスまたはデバイスのグループが IoT Edge デバイスであることを指定できます。

   1. **デバイスをハブに割り当てる方法** について、Device Provisioning Service の割り当てポリシーの既定値をそのまま使用するか、その登録に固有の別の値を選択します。

   1. デバイスの接続先になるリンクされた **IoT Hub** を選択します。 複数のハブを選択でき、デバイスは、選択した割り当てポリシーに従ってそれらのハブの 1 つに割り当てられます。

   1. デバイスが初回以降にプロビジョニングを要求したときのために、**再プロビジョニング時のデバイス データの処理方法** を選択します。

   1. 必要に応じて、 **[デバイス ツインの初期状態]** にタグ値を追加します。 タグを使用して、デバイス グループをモジュール展開のターゲットにすることができます。 次に例を示します。

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

   1. **[エントリの有効化]** が **[有効]** に設定されていることを確認します。

   1. **[保存]** を選択します。

これで、このデバイスの登録が存在しているので、IoT Edge ランタイムによってインストール時にデバイスを自動的にプロビジョニングできます。 IoT Edge ランタイムのインストール時や、グループ登録に使用するデバイス キーを作成する場合に使用できるよう、登録の **プライマリ キー** の値をコピーしておいてください。

## <a name="derive-a-device-key"></a>デバイス キーを派生させる

> [!NOTE]
> このセクションは、グループ登録を使用する場合にのみ必須となります。

各デバイスでは、プロビジョニングの間に、派生デバイス キーと一意の登録 ID を使用して、登録で対称キーの構成証明が実行されます。 デバイス キーを生成するには、DPS 登録からコピーしたキーを使用して、デバイスに対する一意の登録 ID の [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) を計算し、結果を Base64 形式に変換します。

デバイス コードには、登録の主キーやセカンダリ キーを含めないでください。

### <a name="linux-workstations"></a>Linux ワークステーション

Linux ワークステーションを使用している場合は、次の例に示すように、openssl を使用して派生デバイス キーを生成することができます。

**KEY** の値を、前に書き留めた **主キー** で置き換えます。

**REG_ID** の値をデバイスの登録 ID に置き換えます。

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

### <a name="windows-based-workstations"></a>Windows ベースのワークステーション

Windows ベースのワークステーションを使用している場合は、次の例に示すように、PowerShell を使用して派生デバイス キーを生成することができます。

**KEY** の値を、前に書き留めた **主キー** で置き換えます。

**REG_ID** の値をデバイスの登録 ID に置き換えます。

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

## <a name="install-the-iot-edge-runtime"></a>IoT Edge ランタイムをインストールする

IoT Edge ランタイムはすべての IoT Edge デバイスに展開されます。 そのコンポーネントはコンテナー内で実行されるため、デバイスに追加のコンテナーを展開して、Edge でコードを実行できるようにすることができます。

[Azure IoT Edge ランタイムのインストール](how-to-install-iot-edge.md)に関するページにある手順に従い、その後、この記事に戻ってデバイスをプロビジョニングします。

## <a name="configure-the-device-with-provisioning-information"></a>プロビジョニング情報を使用してデバイスを構成する

ランタイムがデバイスにインストールされたら、デバイス プロビジョニング サービスと IoT Hub に接続するために使用される情報でデバイスを構成します。

次の情報を用意しておきます。

* DPS の **ID スコープ** 値
* 作成したデバイス **登録 ID**
* DPS の登録からコピーした **プライマリ キー**

> [!TIP]
> グループ登録の場合、DPS 登録の主キーではなく、各デバイスの[派生キー](#derive-a-device-key)が必要となります。

### <a name="linux-device"></a>Linux デバイス

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
1. IoT Edge デバイスで構成ファイルを開きます。

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. ファイルのプロビジョニング構成セクションを見つけます。 DPS 対称キーのプロビジョニング行のコメントを解除し、他にもプロビジョニング行があれば、それらのコメントが解除されていることを確認します。

   `provisioning:` の行の先頭には空白文字を入れず、入れ子の項目には 2 つの空白でインデントする必要があります。

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "symmetric_key"
       registration_id: "<REGISTRATION_ID>"
       symmetric_key: "<SYMMETRIC_KEY>"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. `scope_id`、`registration_id`、`symmetric_key` の値を DPS およびデバイス情報で更新します。

1. 必要に応じて、`always_reprovision_on_startup` または `dynamic_reprovisioning` の行を使用して、デバイスの再プロビジョニング動作を構成します。 起動時に再プロビジョニングするようにデバイスが設定されている場合、常に最初に DPS を使用してプロビジョニングが試行され、失敗した場合はプロビジョニングのバックアップにフォールバックします。 動的に再プロビジョニングするようにデバイスが設定されている場合、再プロビジョニング イベントが検出されると、IoT Edge が再起動し、再プロビジョニングされます。 詳細については、「[IoT Hub デバイスの再プロビジョニングの概念](../iot-dps/concepts-device-reprovision.md)」を参照してください。

1. デバイスで行ったすべての構成の変更が反映されるように、IoT Edge ランタイムを再起動します。

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

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

1. ファイルの **プロビジョニング** セクションを見つけます。 対称キーによる DPS のプロビジョニング行をコメント解除し、他にもプロビジョニング行があれば、それらもコメント アウトするようにします。

   ```toml
   # DPS provisioning with symmetric key
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "symmetric_key"
   registration_id = "<REGISTRATION_ID>"

   symmetric_key = "<PRIMARY_KEY OR DERIVED_KEY>"
   ```

1. `id_scope`、`registration_id`、`symmetric_key` の値を DPS およびデバイス情報で更新します。

   対称キー パラメーターは、インライン キー、ファイル URI、または PKCS#11 URI の値を受け入れることができます。 使用している形式に基づいて、1 つの対称キー行だけをコメント解除します。

   任意の PKCS#11 URI を使用する場合は、構成ファイルで **PKCS#11** セクションを見つけ、PKCS#11 構成に関する情報を提供します。

1. config.toml ファイルを保存して閉じます。

1. 加えた構成の変更を IoT Edge に適用します。

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

### <a name="windows-device"></a>Windows デバイス

1. 管理者モードで PowerShell ウィンドウを開きます。 IoT Edge をインストールするときは、PowerShell (x86) ではなく、PowerShell の AMD64 セッションを必ず使用してください。

1. **Initialize-IoTEdge** コマンドを使用して、お使いのマシンに IoT Edge ランタイムを構成します。 このコマンドでは、Windows コンテナーを使用した手動プロビジョニングが既定で設定されます。そのため、`-DpsSymmetricKey` フラグを使用し、対称キー認証と共に自動プロビジョニングを使用します。

   `{scope_id}`、`{registration_id}`、および `{symmetric_key}` のプレースホルダー値を、前に収集したデータに置き換えます。

   Windows で Linux コンテナーを使用している場合、`-ContainerOs Linux` パラメーターを追加します。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsSymmetricKey -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>インストールの成功を確認する

ランタイムが正常に起動されたら、IoT Hub にアクセスし、デバイスに IoT Edge モジュールを展開できます。 ランタイムが正常にインストールされ、起動されたことを確認するには、デバイスで次のコマンドを使用します。

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

Device Provisioning Service で作成した個々の登録が使用されたことを確認できます。 Azure portal で Device Provisioning Service インスタンスに移動します。 作成した個々の登録の詳細を開きます。 登録の状態が **割り当て** られており、デバイス ID が表示されています。

## <a name="next-steps"></a>次のステップ

Device Provisioning Service の登録プロセスでは、新しいデバイスをプロビジョニングするときに、デバイス ID とデバイス ツイン タグを同時に設定できます。 これらの値を使用して、個々のデバイスまたはデバイス グループをデバイスの自動管理で使用できます。 使用方法については、「[Azure Portal を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-at-scale.md)」または「[Azure CLI を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-cli-at-scale.md)」を参照してください。
