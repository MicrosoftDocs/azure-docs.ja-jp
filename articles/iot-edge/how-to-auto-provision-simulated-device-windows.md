---
title: DPS を使用した Windows デバイスの自動プロビジョニング - Azure IoT Edge | Microsoft Docs
description: Windows マシン上でシミュレートされたデバイスを使用して、デバイス プロビジョニング サービスで Azure IoT Edge の自動デバイス プロビジョニングをテストします
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 16ac8ef9e0fb876103b57b1cc463bdae5b2362b7
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828104"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Windows 上で、仮想 TPM を使用するシミュレートされた IoT Edge デバイスを作成し、プロビジョニングする

Azure IoT Edge デバイスは、[Device Provisioning Service](../iot-dps/index.yml) を使用して、Edge に対応していないデバイスと同じように自動プロビジョニングできます。 自動プロビジョニングの処理に慣れていない場合は、「[自動プロビジョニングの概念](../iot-dps/concepts-auto-provisioning.md)」を読んでから先に進んでください。

この記事では、シミュレートされた IoT Edge デバイスの自動プロビジョニングを次の手順でテストする方法を示します。

* IoT Hub Device Provisioning Service (DPS) のインスタンスを作成する。
* ハードウェアのセキュリティ用のシミュレートされたトラステッド プラットフォーム モジュール (TPM) を使用して、Windows マシン上にシミュレートされたデバイスを作成する。
* デバイスの個別登録を作成する。
* IoT Edge ランタイムをインストールし、デバイスを IoT Hub に接続する。

> [!NOTE]
> DPS で TPM 構成証明を使用する場合は、TPM 2.0 が必要です。これは、グループではなく個別の登録を作成する場合にのみ使用できます。

> [!TIP]
> この記事では、仮想デバイスでの TPM 構成証明を使用した自動プロビジョニングのテストについて説明しますが、その多くは、物理 TPM ハードウェアを使用する場合にも適用されます。

## <a name="prerequisites"></a>前提条件

* Windows 開発マシン。 この記事では、Windows 10 を使用します。
* アクティブな IoT Hub。

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Azure IoT Hub Device Provisioning Service を設定する

Azure 内に IoT Hub Device Provisioning Service の新しいインスタンスを作成し、それを自分の IoT Hub にリンクします。 手順については、[IoT Hub DPS の設定](../iot-dps/quick-setup-auto-provision.md)に関する記事を参照してください。

Device Provisioning Service を実行した後、概要ページから **[ID スコープ]** の値をコピーします。 この値は、IoT Edge ランタイムを構成するときに使用します。

> [!TIP]
> 物理 TPM デバイスを使用している場合は、各 TPM チップに固有であり、それに関連付けられている TPM チップの製造元から取得される**保証キー**を判断する必要があります。 たとえば保証キーの SHA-256 ハッシュを作成することによって、TPM デバイスの一意の**登録 ID** を派生させることができます。
>
> 記事「[Azure Portal でデバイス登録を管理する方法](../iot-dps/how-to-manage-enrollments.md)」の手順にしたがって DPS に登録を作成し、その後、この記事内の「[IoT Edge ランタイムをインストールする](#install-the-iot-edge-runtime)」セクションに進んで処理を続行します。

## <a name="simulate-a-tpm-device"></a>TPM デバイスのシミュレート

Windows 開発マシン上でシミュレートされた TPM デバイスを作成します。 デバイスの**登録 ID** と**保証キー**を取得し、それらを使用して DPS に個別の登録エントリを作成します。

DPS 内に登録を作成するときに、**デバイス ツインの初期状態**を宣言する機会があります。 デバイス ツインでは、ソリューションで必要な任意のメトリック (リージョン、環境、場所、デバイスの種類など) によってデバイスをグループ化するためのタグを設定できます。 これらのタグは、[自動展開](how-to-deploy-monitor.md)を作成するために使用されます。

シミュレートされたデバイスの作成に使用する SDK の言語を選択し、個々の登録を作成するまでの手順に従います。

個々の登録を作成するときに、 **[True]** を選択して、Windows 開発用マシンでシミュレートされている TPM デバイスが **IoT Edge デバイス**であることを宣言します。

シミュレートされたデバイスと個々の登録ガイド:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.JS](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

個別登録を作成したら、**登録 ID** の値を保存します。 この値は、IoT Edge ランタイムを構成するときに使用します。

## <a name="install-the-iot-edge-runtime"></a>IoT Edge ランタイムをインストールする

IoT Edge ランタイムはすべての IoT Edge デバイスに展開されます。 そのコンポーネントはコンテナー内で実行されるため、デバイスに追加のコンテナーを展開して、Edge でコードを実行できるようにすることができます。

デバイスをプロビジョニングする際には、次の情報が必要になります。

* DPS の **ID スコープ**値
* 作成したデバイス**登録 ID**

シミュレートされた TPM を実行しているデバイスに IoT Edge ランタイムをインストールします。 IoT Edge ランタイムを、手動プロビジョニングではなく、自動プロビジョニング用に構成します。

> [!TIP]
> インストールとテストを行っている間は、TPM シミュレーターを実行しているウィンドウを開いたままにしてください。

Windows での IoT Edge のインストールの詳細については、コンテナーの管理や IoT Edge の更新などのタスクに関する前提条件や手順を含めて、「[Windows に Azure IoT Edge ランタイムをインストールする](how-to-install-iot-edge-windows.md)」を参照してください。

1. 管理者モードで PowerShell ウィンドウを開きます。 IoT Edge をインストールするときは、PowerShell (x86) ではなく、PowerShell の AMD64 セッションを必ず使用してください。

1. **Deploy-IoTEdge** コマンドを使用して、ご使用の Windows コンピューターがサポートされているバージョンであることを確認し、コンテナー機能をオンに設定した後、moby ランタイムと IoT Edge ランタイムをダウンロードします。 コマンドの既定値では Windows コンテナーが使用されます。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. この時点で、IoT Core デバイスが自動的に再起動することがあります。 その他の Windows 10 または Windows Server デバイスでは、再起動が求められることがあります。 その場合、デバイスをすぐに再起動してください。 デバイスが起動されたら、管理者として PowerShell を再実行します。

1. **Initialize-IoTEdge** コマンドを使用して、お使いのマシンに IoT Edge ランタイムを構成します。 このコマンドでは、Windows コンテナーを使用した手動プロビジョニングが既定で設定されます。 手動プロビジョニングではなく Device Provisioning Service を使用するには、`-Dps` フラグを使用します。

   `{scope_id}` および `{registration_id}` のプレースホルダー値を、前に収集したデータに置き換えます。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>インストールの成功を確認する

ランタイムが正常に起動されたら、IoT Hub にアクセスし、デバイスに IoT Edge モジュールを展開できます。 ランタイムが正常にインストールされ、起動されたことを確認するには、デバイスで次のコマンドを使用します。  

IoT Edge サービスの状態を確認します。

```powershell
Get-Service iotedge
```

過去 5 分のサービス ログを調べます。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

実行中のモジュールを一覧表示します。

```powershell
iotedge list
```

## <a name="next-steps"></a>次の手順

Device Provisioning Service の登録プロセスでは、新しいデバイスをプロビジョニングするときに、デバイス ID とデバイス ツイン タグを同時に設定できます。 これらの値を使用して、個々のデバイスまたはデバイス グループをデバイスの自動管理で使用できます。 使用方法については、「[Azure Portal を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-monitor.md)」または「[Azure CLI を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-monitor-cli.md)」を参照してください。
