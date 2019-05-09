---
title: リモート監視に接続されたデバイスを一括管理する - Azure | Microsoft Docs
description: このチュートリアルでは、リモート監視ソリューションに接続されたデバイスを一括管理する方法を学習します。
author: aditidugar
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: adugar
ms.openlocfilehash: 8a5c74c76662a089675fcbdcd8d5a7ea54b58fd1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799042"
---
# <a name="tutorial-manage-your-connected-devices-in-bulk"></a>チュートリアル:接続されたデバイスを一括管理する

このチュートリアルでは、リモート監視ソリューション アクセラレータを使用して、接続されたデバイスの構成を一括管理します。

Contoso のオペレーターは、新しいバージョンのファームウェアでデバイスのグループを構成する必要があります。 個デバイスのファームウェアを個別に更新する必要があるのは好ましくありません。 デバイスのグループでファームウェアを更新するには、リモート監視ソリューション アクセラレータのデバイス グループと自動デバイス管理を使用できます。 デバイス グループに追加したすべてのデバイスでは、デバイスがオンラインになるとすぐに、最新のファームウェアが取得されます。

このチュートリアルでは、次のことを行いました。

>[!div class="checklist"]
> * デバイス グループを作成する
> * ファームウェアを準備してホストする
> * Azure portal でデバイス構成を作成する
> * リモート監視ソリューションにデバイス構成をインポートする
> * デバイス グループのデバイスに構成をデプロイする
> * デプロイの監視

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

<!--
If this is going to be a tutorial - we need to split this include into two so that we can accommodate the additional prerequisites:

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]
-->

## <a name="prerequisites"></a>前提条件

このチュートリアルを実行するには、お使いの Azure サブスクリプションにリモート監視ソリューション アクセラレータのインスタンスをデプロイしておく必要があります。

まだリモート監視ソリューション アクセラレータをデプロイしていない場合は、クイック スタート「[クラウドベースのリモート監視ソリューションのデプロイ](quickstart-remote-monitoring-deploy.md)」を完了する必要があります。

ファームウェアのファイルをホストするには、Azure ストレージ アカウントが必要です。 既存のストレージ アカウントを使うのでも、お使いのサブスクリプションに[新しいストレージ アカウントを作成する](../storage/common/storage-quickstart-create-account.md)のでもかまいません。

チュートリアルでは、サンプル デバイスとして [IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) デバイスを使用します。

ローカル コンピューターに次のソフトウェアがインストールされている必要があります。

* [Visual Studio Code (VS Code)](https://code.visualstudio.com/)。
* [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) VS Code 拡張機能。

開始する前に次の操作を実行してください。

* [IoT DevKit デバイスのブートローダーがバージョン 1.4.0 以降である](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/)ことを確認します。
* IoT DevKit SDK がブートローダーと同じバージョンであることを確認します。 VS Code で Azure IoT Workbench を使用して、IoT DevKit SDK を更新できます。 コマンド パレットを開き、次のように入力します: **Arduino:**  を入力して選択します。 詳しくは、「[開発環境の準備](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md#prepare-the-development-environment)」をご覧ください。

少なくとも 1 台の IoT DevKit デバイスをリモート監視ソリューション アクセラレータに接続する必要もあります。 IoT DevKit デバイスを接続していない場合は、「[MXChip IoT DevKit AZ3166 を IoT リモート監視ソリューション アクセラレータに接続する](iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md)」をご覧ください。

## <a name="navigate-to-the-dashboard"></a>ダッシュボードに移動します。

リモート監視ソリューション ダッシュボードをブラウザーに表示するには、最初に [[Microsoft Azure IoT Solution Accelerators]\(Microsoft Azure IoT ソリューション アクセラレーター\)](https://www.azureiotsolutions.com/Accelerators#dashboard) に移動します。 Azure サブスクリプションの資格情報を使用してサインインするように求められる場合があります。

次に、[クイック スタート](quickstart-remote-monitoring-deploy.md)でデプロイしたリモート監視ソリューション アクセラレーターのタイル上で **[起動]** をクリックします。

## <a name="create-a-device-group"></a>デバイス グループを作成する

デバイスのグループでファームウェアを自動的に更新するには、デバイスがリモート監視ソリューションのデバイス グループのメンバーになっている必要があります。

1. **[デバイス]** ページで、ソリューション アクセラレータに接続したすべての **IoT DevKit** デバイスを選択します。 次に、**[ジョブ]** をクリックします。

1. **[ジョブ]** パネルで **[タグ]** を選択し、ジョブ名を **AddDevKitTag** に設定します。**IsDevKitDevice** という名前のテキスト タグを追加し、値を **Y** に設定します。次に、**[適用]** をクリックします。

1. タグ値を使用してデバイス グループを作成できるようになります。 **[デバイス]** ページで、**[Manage device groups]\(デバイス グループの管理\)** をクリックします。

1. 条件にタグ名 **IsDevKitDevice** と値 **Y** を使用するテキスト フィルターを作成します。 デバイス グループを **IoT DevKit デバイス**として保存します。

後でこのデバイス グループを使用して、すべてのメンバーのファームウェアを更新するデバイス構成を適用します。

## <a name="prepare-and-host-the-firmware"></a>ファームウェアを準備してホストする

[Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) VS Code 拡張機能には、ファームウェア更新のサンプル デバイス コードが含まれています。

### <a name="open-the-firmware-ota-sample-in-vs-code"></a>VS Code で Firmware OTA サンプルを開く

1. IoT DevKit がお使いのコンピューターに接続されていないことを確認します。 VS Code を起動し、DevKit をコンピューターに接続します。

1. **F1** キーを押してコマンド パレットを開き、次のように入力して選択します: **IoT Workbench: Examples**。 次に、ボードとして **IoT DevKit** を選択します。

1. **Firmware OTA** を探し、**[Open Sample]\(サンプルを開く\)** をクリックします。 新しい VS Code ウィンドウが開き、**firmware_ota** プロジェクト フォルダーが表示されます。

    ![IoT Workbench、Firmware OTA サンプルの選択](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-firmware-example.png)

### <a name="build-the-new-firmware"></a>新しいファームウェアをビルドする

デバイスのファームウェアの初期バージョンは 1.0.0 です。 新しいファームウェアは、それより高いバージョン番号にする必要があります。

1. VS Code で **FirmwareOTA.ino** ファイルを開き、`currentFirmwareVersion` を `1.0.0` から `1.0.1` に変更します。

    ![ファームウェアのバージョンを変更する](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. コマンド パレットを開き、次のように入力して選択します: **IoT Workbench: Device**。 次に、**[Device Compile]\(デバイスのコンパイル\)** を選択してコードをコンパイルします。

    ![デバイスのコンパイル](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-compile.png)

    VS Code では、コンパイルされたファイルはプロジェクト内の `.build` フォルダーに保存されます。 設定によっては、VS Code のエクスプローラー ビューで `.build` フォルダーが非表示になっている場合があります。

### <a name="generate-the-crc-value-and-calculate-the-firmware-file-size"></a>CRC 値を生成し、ファームウェアのファイル サイズを計算する

1. コマンド パレットを開き、次のように入力して選択します: **IoT Workbench: Device**。 次に、**[Generate CRC]\(CRC の生成\)** を選択します。

    ![CRC を生成する](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-crc.png)

1. VS Code で CRC が生成されて、CRC の値、ファームウェアのファイル名とパス、ファイルのサイズが出力ウィンドウに表示されます。 後のためにこれらの値を書き留めておきます。

    ![CRC 情報](media/iot-accelerators-remote-monitoring-bulk-configuration-update/crc-info.png)

### <a name="upload-the-firmware-to-the-cloud"></a>ファームウェアをクラウドにアップロードする

お使いの Azure ストレージ アカウントを使用して、クラウドで新しいファームウェア ファイルをホストします。

1. Azure Portal のストレージ アカウントに移動します。 [サービス] セクションで、**[BLOB]** を選択します。 ファームウェア ファイルを保存するために、**firmware** という名前のパブリック コンテナーを作成します。

    ![フォルダーを作成する](media/iot-accelerators-remote-monitoring-bulk-configuration-update/blob-folder.png)

1. ファームウェア ファイルをコンテナーにアップロードするには、**firmware** コンテナーを選択して、**[Upload]\(アップロード\)** をクリックします。

1. **FirmwareOTA.ino.bin** を選択します。 このファイルの完全なパスは、前のセクションで書き留めたものです。

1. ファームウェア ファイルのアップロードが完了した後、ファイルの URL を書き留めておきます。

### <a name="build-and-upload-the-original-firmware-to-the-iot-devkit-device"></a>元のファームウェアをビルドして IoT DevKit デバイスにアップロードする

1. VS Code で **FirmwareOTA.ino** ファイルを開き、`currentFirmwareVersion` を `1.0.0` に戻します。

    ![バージョン 1.0.0](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. コマンド パレットを開き、次のように入力して選択します: **IoT Workbench: Device**。 **[Device Upload]\(デバイスのアップロード\)** を選択します。

    ![デバイスのアップロード](media/iot-accelerators-remote-monitoring-bulk-configuration-update/device-upload.png)

1. VS Code によってコードが検証されて、IoT DevKit デバイスにアップロードされます。

1. アップロードが完了したら、IoT DevKit デバイスを再起動します。 再起動が完了すると、IoT DevKit の画面に、**FW version: 1.0.0** と、新しいファームウェアをチェックしていることが表示されます。

    ![ota-1](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-1.jpg)

## <a name="create-a-device-configuration"></a>デバイス構成を作成する

デバイス構成では、デバイスの望ましい状態を指定します。 通常、開発者は、Azure portal の **[IoT デバイスの構成]** ページで[構成を作成](../iot-hub/iot-hub-automatic-device-management.md#create-a-configuration)します。 デバイス構成は、デバイスの望ましい状態とメトリックのセットが指定されている JSON ドキュメントです。

次の構成を **firmware-update.json** という名前のファイルとしてローカル コンピューターに保存します。 `YOURSTRORAGEACCOUNTNAME`、`YOURCHECKSUM`、`YOURPACKAGESIZE` の各プレースホルダーを、前に書き留めた値に置き換えます。

```json
{
  "id": "firmware-update",
  "schemaVersion": null,
  "labels": {
    "Version": "1.0.1",
    "Devices": "IoT DevKit"
  },
  "content": {
    "deviceContent": {
      "properties.desired.firmware": {
        "fwVersion": "1.0.1",
        "fwPackageURI": "https://YOURSTRORAGEACCOUNTNAME.blob.core.windows.net/firmware/FirmwareOTA.ino.bin",
        "fwPackageCheckValue": "YOURCHECKSUM",
        "fwSize": YOURPACKAGESIZE
      }
    }
  },
  "targetCondition": "",
  "priority": 10,
  "systemMetrics": {
    "results": {
      "targetedCount": 0,
      "appliedCount": 0
    },
    "queries": {
      "targetedCount": "",
      "appliedCount": "select deviceId from devices where configurations.[[firmware-update]].status = 'Applied'"
    }
  },
  "metrics": {
    "results": {
      "Current": 1
    },
    "queries": {
      "Current": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Current' AND properties.reported.firmware.type='IoTDevKit'",
      "Downloading": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Downloading' AND properties.reported.firmware.type='IoTDevKit'",
      "Verifying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Verifying' AND properties.reported.firmware.type='IoTDevKit'",
      "Applying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Applying' AND properties.reported.firmware.type='IoTDevKit'",
      "Error": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Error' AND properties.reported.firmware.type='IoTDevKit'"
    }
  }
}
```

次のセクションではこの構成ファイルを使用します。

## <a name="import-a-configuration"></a>構成をインポートする

このセクションでは、リモート監視ソリューション アクセラレータにパッケージとしてデバイス構成をインポートします。 通常は、オペレーターがこのタスクを行います。

1. リモート監視の Web UI で **[パッケージ]** ページに移動し、**[+ 新しいパッケージ]** をクリックします。

    ![新しいパッケージ](media/iot-accelerators-remote-monitoring-bulk-configuration-update/packagepage.png)

1. **[New Package]\(新しいパッケージ\)** パネルで、パッケージの種類として **[Device configuration]\(デバイス構成\)** を選択し、構成の種類として **[Firmware]\(ファームウェア\)** を選択します。 **[Browse]\(参照\)** をクリックし、ローカル コンピューター上で **firmware-update.json** ファイルを探して、**[Upload]\(アップロード\)** をクリックします。

    ![パッケージのアップロード](media/iot-accelerators-remote-monitoring-bulk-configuration-update/uploadpackage.png)

1. パッケージの一覧に **firmware-update** パッケージが含まれるようになります。

## <a name="deploy-the-configuration-to-your-devices"></a>構成をデバイスにデプロイする

このセクションでは、デバイス構成を IoT DevKit デバイスに適用するデプロイを作成して実行します。

1. リモート監視の Web UI で **[デプロイ]** ページに移動し、**[+ 新しいデプロイ]** をクリックします。

    ![新しいデプロイ](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentpage.png)

1. **[新しいデプロイ]** パネルで、次の設定を使用してデプロイを作成します。

    |オプション|値|
    |---|---|
    |Name|ファームウェア更新のデプロイ|
    |パッケージの種類|Device Configuration (デバイス構成)|
    |Configuration type (構成の種類)|Firmware|
    |Package|firmware-update.json|
    |デバイス グループ|IoT DevKit デバイス|
    |優先順位|10|

    ![Create deployment](media/iot-accelerators-remote-monitoring-bulk-configuration-update/newdeployment.png)

    **[Apply]** をクリックします。 **[Deployments]\(デプロイ\)** ページに新しいデプロイが表示され、次のメトリックが示されます。

    * **[Targeted]\(対象設定済み\)** には、デバイス グループ内のデバイスの数が表示されます。
    * **[Applied]\(適用済み\)** には、構成の内容で更新されたデバイスの数が表示されます。
    * **[Succeeded]\(成功\)** には、成功を報告しているデプロイ内のデバイスの数が表示されます。
    * **[Failed]\(失敗\)** には、失敗を報告しているデプロイ内のデバイスの数が表示されます。

## <a name="monitor-the-deployment"></a>デプロイの監視

数分経つと、IoT DevKit で新しいファームウェア情報が取得されて、デバイスへのダウンロードが開始されます。

![ota-2](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-2.jpg)

ネットワークの速度によっては、ダウンロードに数分かかる場合があります。 ファームウェアのダウンロード後、デバイスでファイルのサイズと CRC 値が検証されます。 検証が成功した場合、MXChip の画面に **[passed]** と表示されます。

![ota-3](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-3.jpg)

確認が成功した場合、デバイスは再起動します。 再起動が行われる前に、**5** から **0** までカウントダウンされます。

![ota-4](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-4.jpg)

再起動の後、IoT DevKit のブートローダーによってファームウェアが新しいバージョンにアップグレードされます。 アップグレードには数秒かかる場合があります。 このステージの間、デバイスの RGB LED は赤に、画面は空白になります。

![ota-5](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-5.jpg)

再起動が完了すると、IoT DevKit デバイスではファームウェアのバージョン 1.0.1 が実行されています。

![ota-6](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-6.jpg)

**[Deployments]\(デプロイ\)** ページで、デプロイをクリックして、更新されたデバイスの状態を確認します。 デバイス グループ内の各デバイスの状態と、定義したカスタム メトリックを確認できます。

![デプロイ詳細](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentstatus.png)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、ソリューションに接続されているデバイスのグループのファームウェアを更新する方法を示しました。 デバイスを更新するため、ソリューションではデバイスの自動管理が使用されます。 ソリューションの基になっている IoT ハブでの自動デバイス管理機能について詳しくは、「[Azure portal を使って多数の IoT デバイスを構成および監視する](../iot-hub/iot-hub-auto-device-config.md)」をご覧ください。