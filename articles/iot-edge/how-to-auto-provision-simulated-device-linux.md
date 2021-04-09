---
title: Linux VM で仮想 TPM を使用してデバイスをプロビジョニングする - Azure IoT Edge
description: Linux VM 上のシミュレートされた TPM を使用して Azure IoT Edge 用の Azure Device Provisioning Service をテストします
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9356da9516ea17243be8e0e5519ec4b19e06db8a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200874"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-tpm-on-linux"></a>Linux で TPM を使用して IoT Edge デバイスを作成およびプロビジョニングする

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

この記事では、トラステッド プラットフォーム モジュール (TPM) を使用して Linux IoT Edge デバイスに対して自動プロビジョニングをテストする方法について説明します。 Azure IoT Edge デバイスは、[Device Provisioning Service](../iot-dps/index.yml) を使用して自動プロビジョニングできます。 自動プロビジョニングの処理に慣れていない場合は、[プロビジョニング](../iot-dps/about-iot-dps.md#provisioning-process)の概要を読んでから先に進んでください。

:::moniker range=">=iotedge-2020-11"
> [!NOTE]
> 現時点では、TPM 認証を使用した自動プロビジョニングは IoT Edge バージョン 1.2 ではサポートされていません。
:::moniker-end

タスクは次のとおりです。

1. ハードウェアのセキュリティ用のシミュレートされたトラステッド プラットフォーム モジュール (TPM) がある Hyper-V 内に Linux 仮想マシン (VM) を作成する。
1. IoT Hub Device Provisioning Service (DPS) のインスタンスを作成する。
1. デバイスの個別登録を作成する。
1. IoT Edge ランタイムをインストールし、デバイスを IoT Hub に接続する。

> [!TIP]
> この記事では、TPM シミュレーターを使用して DPS プロビジョニングをテストする方法について説明しますが、その多くは、Azure Certified for IoT デバイスである [Infineon OPTIGA&trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board) などの物理 TPM ハードウェアに適用されます。
>
> 物理デバイスを使用している場合は、この記事の「[物理デバイスからプロビジョニング情報を取得する](#retrieve-provisioning-information-from-a-physical-device)」セクションに進むことができます。

## <a name="prerequisites"></a>前提条件

* [Hyper-V 対応の](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) Windows 開発マシン。 この記事では、Ubuntu Server VM を実行中の Windows 10 を使用します。
* アクティブな IoT Hub。

> [!NOTE]
> DPS で TPM 構成証明を使用する場合は、TPM 2.0 が必要です。これは、グループではなく個別の登録を作成する場合にのみ使用できます。

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>仮想 TPM がある Linux 仮想マシンを作成する

このセクションでは、Hyper-V で新しい Linux 仮想マシンを作成します。 IoT Edge で自動プロビジョニングがどのように機能するかをテストするために、シミュレートされた TPM を使用してこの仮想マシンを構成します。

### <a name="create-a-virtual-switch"></a>仮想スイッチを作成する

仮想スイッチを使用して、仮想マシンを物理ネットワークに接続できます。

1. Windows マシンで Hyper-V Manager を開きます。

2. **[アクション]** メニューの **[仮想スイッチ マネージャー]** を選択します。

3. **[外部]** 仮想スイッチを選択し、 **[仮想スイッチの作成]** を選択します。

4. 新しい仮想スイッチに名前を付けます (たとえば **EdgeSwitch**)。 接続の種類が **[外部ネットワーク]** に設定されていることを確認し、 **[OK]** を選択します。

5. ネットワーク接続が中断される可能性があることを警告するポップアップが表示されます。 **[はい]** を選択して続行します。

新しい仮想スイッチの作成中にエラーが発生した場合は、イーサネット アダプターを使用している他のスイッチがないこと、および同じ名前を使用している他のスイッチがないことを確認します。

### <a name="create-virtual-machine"></a>仮想マシンの作成

1. 仮想マシンで使用するディスク イメージ ファイルをダウンロードし、ローカルに保存します。 たとえば、[Ubuntu Server 18.04](http://releases.ubuntu.com/18.04/) をダウンロードします。 IoT Edge デバイスでサポートされているオペレーティング システムの詳細については、「[Azure IoT Edge のサポートされるシステム](support.md)」を参照してください。

2. 再度 Hyper-V マネージャーの **[操作]** メニューで、 **[操作]**  >  **[新規]**  >  **[仮想マシン]** の順に選択します。

3. **新しい仮想マシン ウィザード** を、次の構成で完了します。

   1. **世代の指定**: **[第 2 世代]** を選択します。 第 2 世代の仮想マシンでは、入れ子になった仮想化が有効になっており、これは仮想マシンで IoT Edge を実行するのに必要です。
   2. **ネットワークの構成**: **[接続]** の値を、前のセクションで作成した仮想スイッチに設定します。
   3. **インストール オプション**: **[ブート イメージ ファイルからオペレーティング システムをインストールする]** を選択し、ローカルに保存したディスク イメージ ファイルを参照します。

4. 仮想マシンを作成するウィザードで、 **[完了]** を選択します。

新しい VM の作成には数分かかることがあります。

### <a name="enable-virtual-tpm"></a>仮想 TPM を有効にする

VM が作成されたら、その設定を開いて、デバイスの自動プロビジョニングに使用する仮想トラステッド プラットフォーム モジュール (TPM) を有効化します。

1. Hyper-V マネージャーで、VM を右クリックし、 **[設定]** を選択します。

2. **[セキュリティ]** に移動します。

3. **[セキュア ブートを有効にする]** をオフにします。

4. **[トラステッド プラットフォーム モジュールを有効にする]** をオンにします。

5. **[OK]** をクリックします。  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>仮想マシンを起動し、TPM データを収集する

仮想マシンで、デバイスの **登録 ID** と **保証キー** を取得するために使用できるツールをビルドします。

1. Hyper-V マネージャーで、VM を起動して接続します。

1. 仮想マシン内のプロンプトに従ってインストール プロセスを実行し、コンピューターを再起動します。

1. VM にサインインし、[Linux 開発環境の設定](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux)に関するセクションの手順に従って、Azure IoT device SDK for C をインストールしてビルドします。

   >[!TIP]
   >この記事の過程では、仮想マシン上でコピーと貼り付けを行います。Hyper-V マネージャー接続アプリケーション経由でこの操作を行うことは容易ではありません。 Hyper-V マネージャー経由でいったん仮想マシンに接続して、その IP アドレスを取得することをお勧めします。 まず `sudo apt install net-tools` を実行し、次に `hostname -I` を実行します。 その後、この IP アドレスを使用して、SSH 経由で接続できます: `ssh <username>@<ipaddress>`。

1. 次のコマンドを実行して、TPM からデバイスのプロビジョニング情報を取得する SDK ツールをビルドします。

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. 出力ウィンドウに、デバイスの **登録 ID** と **保障キー** が表示されます。 後でデバイスの個別登録を作成する際に使用できるように、これらの値をコピーします。

登録 ID と保証キーを取得したら、「[Azure IoT Hub Device Provisioning Service を設定する](#set-up-the-iot-hub-device-provisioning-service)」セクションに進みます

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>物理デバイスからプロビジョニング情報を取得する

VM ではなく物理 IoT Edge デバイスを使用している場合は、デバイスのプロビジョニング情報を取得するために使用できるツールを構築します。

1. [Linux 開発環境の設定](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux)に関するセクションの手順に従って、Azure IoT device SDK for C をインストールしてビルドします。

1. 次のコマンドを実行して、TPM デバイスからデバイスのプロビジョニング情報を取得する SDK ツールをビルドします。

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. **登録 ID** と **保証キー** の値をコピーします。 これらの値を使用して、DPS でのデバイスの個別登録を作成します。

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Azure IoT Hub Device Provisioning Service を設定する

Azure 内に IoT Hub Device Provisioning Service の新しいインスタンスを作成し、それを自分の IoT Hub にリンクします。 手順については、[IoT Hub DPS の設定](../iot-dps/quick-setup-auto-provision.md)に関する記事を参照してください。

Device Provisioning Service を実行した後、概要ページから **[ID スコープ]** の値をコピーします。 この値は、IoT Edge ランタイムを構成するときに使用します。

## <a name="create-a-dps-enrollment"></a>DPS の登録を作成する

仮想マシンからプロビジョニング情報を取得し、それを使用して Device Provisioning Service 内に個別登録を作成します。

DPS 内に登録を作成するときに、**デバイス ツインの初期状態** を宣言する機会があります。 デバイス ツインでは、ソリューションで必要な任意のメトリック (リージョン、環境、場所、デバイスの種類など) によってデバイスをグループ化するためのタグを設定できます。 これらのタグは、[自動展開](how-to-deploy-at-scale.md)を作成するために使用されます。

> [!TIP]
> Azure CLI では、[登録](/cli/azure/ext/azure-iot/iot/dps/enrollment)を作成し、**Edge 対応** フラグを使用して、デバイスが IoT Edge デバイスであることを指定できます。

1. [Azure portal](https://portal.azure.com) で、IoT Hub Device Provisioning Service のインスタンスに移動します。

2. **[設定]** の下の **[登録の管理]** を選択します。

3. **[Add individual enrollment]\(個別登録の追加\)** を選択し、登録を構成する次の手順を完了します。  

   1. **[メカニズム]** で **[TPM]** を選択します。

   2. 仮想マシンからコピーした **保証キー** と **登録 ID** を指定します。

      > [!TIP]
      > 物理 TPM デバイスを使用している場合は、各 TPM チップに固有であり、それに関連付けられている TPM チップの製造元から取得される **保証キー** を判断する必要があります。 たとえば保証キーの SHA-256 ハッシュを作成することによって、TPM デバイスの一意の **登録 ID** を派生させることができます。

   3. 必要に応じて、デバイス ID を指定します。 デバイス ID を指定しなかった場合は、登録 ID が使用されます。

   4. **[True]** を選択して、この仮想マシンが IoT Edge デバイスであることを宣言します。

   5. デバイスの接続先になるリンクされた IoT ハブを選択するか、 **[Link to new IoT Hub]\(新しい IoT ハブへのリンク\)** を選択します。 複数のハブを選択でき、デバイスは、選択した割り当てポリシーに従ってそれらのハブの 1 つに割り当てられます。

   6. 必要に応じて、 **[デバイス ツインの初期状態]** にタグ値を追加します。 タグを使用して、デバイス グループをモジュール展開のターゲットにすることができます。 詳細については、[大規模な IoT Edge モジュールの展開](how-to-deploy-at-scale.md)に関する記事を参照してください。

   7. **[保存]** を選択します。

これで、このデバイスの登録が存在しているので、IoT Edge ランタイムによってインストール時にデバイスを自動的にプロビジョニングできます。

## <a name="install-the-iot-edge-runtime"></a>IoT Edge ランタイムをインストールする

IoT Edge ランタイムはすべての IoT Edge デバイスに展開されます。 そのコンポーネントはコンテナー内で実行されるため、デバイスに追加のコンテナーを展開して、Edge でコードを実行できるようにすることができます。 IoT Edge ランタイムを仮想マシンにインストールします。

[Azure IoT Edge ランタイムのインストール](how-to-install-iot-edge.md)に関するページにある手順に従い、その後、この記事に戻ってデバイスをプロビジョニングします。

## <a name="configure-the-device-with-provisioning-information"></a>プロビジョニング情報を使用してデバイスを構成する

ランタイムがデバイスにインストールされたら、デバイス プロビジョニング サービスと IoT Hub に接続するために使用される情報でデバイスを構成します。

1. 前のセクションで集めた DPS **ID スコープ** とデバイスの **登録 ID** を把握しておきます。

1. IoT Edge デバイスで構成ファイルを開きます。

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. ファイルのプロビジョニング構成セクションを見つけます。 TPM プロビジョニング行のコメントを解除し、他にもプロビジョニング行があれば、それらのコメントが解除されていることを確認します。

   `provisioning:` の行の先頭には空白文字を入れず、入れ子の項目には 2 つの空白でインデントする必要があります。

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "tpm"
       registration_id: "<REGISTRATION_ID>"
   # always_reprovision_on_startup: true
   # dynamic_reprovisioning: false
   ```

   必要に応じて、`always_reprovision_on_startup` または `dynamic_reprovisioning` の行を使用して、デバイスの再プロビジョニング動作を構成します。 起動時に再プロビジョニングするようにデバイスが設定されている場合、常に最初に DPS を使用してプロビジョニングが試行され、失敗した場合はプロビジョニングのバックアップにフォールバックします。 動的に再プロビジョニングするようにデバイスが設定されている場合、再プロビジョニング イベントが検出されると、IoT Edge が再起動し、再プロビジョニングされます。 詳細については、「[IoT Hub デバイスの再プロビジョニングの概念](../iot-dps/concepts-device-reprovision.md)」を参照してください。

1. `scope_id` と `registration_id` の値を DPS およびデバイス情報で更新します。

## <a name="give-iot-edge-access-to-the-tpm"></a>IoT Edge に TPM へのアクセス権を付与する

デバイスを自動的にプロビジョニングするには、IoT Edge ランタイムが TPM にアクセスする必要があります。

IoT Edge ランタイムに TPM へのアクセス権を付与するには、systemd 設定をオーバーライドして、`iotedge` サービスに root 特権を付与します。 サービス権限を昇格したくない場合は、次の手順を使用して、TPM へのアクセス権を手動で付与することもできます。

1. デバイスで、TPM ハードウェア モジュールへのファイル パスを探してローカル変数として保存します。

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. IoT Edge ランタイムに tpm0 へのアクセス権を付与する新しいルールを作成します。

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. 規則ファイルを開きます。

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. 次のアクセス情報を規則ファイルにコピーします。

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
   ```

5. ファイルを保存して終了します。

6. udev system をトリガーして、新しい規則を評価します。

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. 規則が正常に適用されたことを確認します。

   ```bash
   ls -l /dev/tpm0
   ```

   成功した場合の出力は、次のようになります。

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   適切なアクセス許可が適用されない場合は、コンピューターを再起動して udev を更新してみてください。

## <a name="restart-the-iot-edge-runtime"></a>IoT Edge ランタイムを再起動する

デバイスで行ったすべての構成の変更が反映されるように、IoT Edge ランタイムを再起動します。

   ```bash
   sudo systemctl restart iotedge
   ```

IoT Edge ランタイムが実行されていることを確認します。

   ```bash
   sudo systemctl status iotedge
   ```

プロビジョニング エラーが発生した場合は、構成の変更がまだ有効になっていない可能性があります。 IoT Edge を再起動をもう一度試してください。

   ```bash
   sudo systemctl daemon-reload
   ```

または、変更が新たな開始時に有効になる場合は、仮想マシンの再起動を試してください。

## <a name="verify-successful-installation"></a>インストールの成功を確認する

ランタイムが正常に起動した場合は、IoT Hub に移動して、新しいデバイスが自動的にプロビジョニングされたことを確認できます。 これで、デバイスで IoT Edge モジュールを実行する準備ができました。

IoT Edge デーモンの状態を確認します。

```cmd/sh
systemctl status iotedge
```

デーモンのログを調べます。

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

実行中のモジュールを一覧表示します。

```cmd/sh
iotedge list
```

Device Provisioning Service で作成した個々の登録が使用されたことを確認できます。 Azure portal で Device Provisioning Service インスタンスに移動します。 作成した個々の登録の詳細を開きます。 登録の状態が **割り当て** られており、デバイス ID が表示されています。

## <a name="next-steps"></a>次のステップ

DPS の登録プロセスを使用すると、新しいデバイスをプロビジョニングするときに、デバイス ID とデバイス ツイン タグを同時に設定できます。 これらの値を使用して、個々のデバイスまたはデバイス グループをデバイスの自動管理で使用できます。 使用方法については、「[Azure Portal を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-at-scale.md)」または「[Azure CLI を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-cli-at-scale.md)」を参照してください。
