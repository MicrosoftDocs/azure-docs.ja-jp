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
ms.openlocfilehash: aa5e5fba3758fa3983924660b9b5f714d02613c6
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2019
ms.locfileid: "54158603"
---
# <a name="create-and-provision-a-simulated-tpm-edge-device-on-windows"></a>Windows 上のシミュレートされた TPM Edge デバイスの作成とプロビジョニング

Azure IoT Edge デバイスは、[Device Provisioning Service](../iot-dps/index.yml) を使用して、Edge に対応していないデバイスと同じように自動プロビジョニングできます。 自動プロビジョニングの処理に慣れていない場合は、「[自動プロビジョニングの概念](../iot-dps/concepts-auto-provisioning.md)」を読んでから先に進んでください。

この記事では、シミュレートされた Edge デバイスの自動プロビジョニングを次の手順でテストする方法を示します。

* IoT Hub Device Provisioning Service (DPS) のインスタンスを作成する。
* ハードウェアのセキュリティ用のシミュレートされたトラステッド プラットフォーム モジュール (TPM) を使用して、Windows マシン上にシミュレートされたデバイスを作成する。
* デバイスの個別登録を作成する。
* IoT Edge ランタイムをインストールし、デバイスを IoT Hub に接続する。

## <a name="prerequisites"></a>前提条件

* Windows 開発マシン。 この記事では、Windows 10 を使用します。
* アクティブな IoT Hub。

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Azure IoT Hub Device Provisioning Service を設定する

Azure 内に IoT Hub Device Provisioning Service の新しいインスタンスを作成し、それを自分の IoT Hub にリンクします。 手順については、[IoT Hub DPS の設定](../iot-dps/quick-setup-auto-provision.md)に関する記事を参照してください。

Device Provisioning Service を実行した後、概要ページから **[ID スコープ]** の値をコピーします。 この値は、IoT Edge ランタイムを構成するときに使用します。

## <a name="simulate-a-tpm-device"></a>TPM デバイスのシミュレート

Windows 開発マシン上でシミュレートされた TPM デバイスを作成します。 デバイスの**登録 ID** と**保証キー**を取得し、それらを使用して DPS に個別の登録エントリを作成します。

DPS 内に登録を作成するときに、**デバイス ツインの初期状態**を宣言する機会があります。 デバイス ツインでは、ソリューションで必要な任意のメトリック (リージョン、環境、場所、デバイスの種類など) によってデバイスをグループ化するためのタグを設定できます。 これらのタグは、[自動展開](how-to-deploy-monitor.md)を作成するために使用されます。

シミュレートされたデバイスの作成に使用する SDK の言語を選択し、個々の登録を作成するまでの手順に従います。

個々の登録を作成するときに、**[有効にする]** を選択して、Windows 開発用マシンでシミュレートされている TPM デバイスが **IoT Edge デバイス**であることを宣言します。

シミュレートされたデバイスと個々の登録ガイド:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

個別登録を作成したら、**登録 ID** の値を保存します。 この値は、IoT Edge ランタイムを構成するときに使用します。

## <a name="install-the-iot-edge-runtime"></a>IoT Edge ランタイムをインストールする

前のセクションを完了すると、新しいデバイスが IoT Edge デバイスとして IoT Hub に一覧表示されます。 次は、IoT Edge ランタイムをデバイスにインストールする必要があります。

IoT Edge ランタイムはすべての IoT Edge デバイスに展開されます。 そのコンポーネントはコンテナー内で実行されるため、デバイスに追加のコンテナーを展開して、Edge でコードを実行できるようにすることができます。  

手順に従って、前のセクションのシミュレートされた TPM を実行しているデバイスに IoT Edge ランタイムをインストールします。 IoT Edge ランタイムの構成が、手動プロビジョニングではなく、自動プロビジョニングになっていることを確認してください。

IoT Edge をデバイスにインストールする前に、DPS の **ID スコープ**とデバイスの**登録 ID** を確認します。

[IoT Edge をインストールして自動的にプロビジョニングする](how-to-install-iot-edge-windows.md#option-2-install-and-automatically-provision)

## <a name="verify-successful-installation"></a>インストールの成功を確認する

ランタイムが正常に起動されたら、IoT Hub にアクセスし、デバイスに IoT Edge モジュールを展開できます。 ランタイムが正常にインストールされ、起動されたことを確認するには、デバイスで次のコマンドを使用します。  

IoT Edge サービスの状態を確認します。

```powershell
Get-Service iotedge
```

過去 5 分のサービス ログを調べます。

```powershell
# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

実行中のモジュールを一覧表示します。

```powershell
iotedge list
```

## <a name="next-steps"></a>次の手順

Device Provisioning Service の登録プロセスでは、新しいデバイスをプロビジョニングするときに、デバイス ID とデバイス ツイン タグを同時に設定できます。 これらの値を使用して、個々のデバイスまたはデバイス グループをデバイスの自動管理で使用できます。 使用方法については、「[Azure Portal を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-monitor.md)」または「[Azure CLI を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-monitor-cli.md)」を参照してください。
