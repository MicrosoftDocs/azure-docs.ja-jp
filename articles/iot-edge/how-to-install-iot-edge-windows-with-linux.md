---
title: Azure IoT Edge for Linux を Windows にインストールする | Microsoft Docs
description: Windows 10、Windows Server、Windows IoT Core に Azure IoT Edge をインストールし、Linux コンテナーを実行する手順
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: 9e9028d0c9aeff19dc221b81defa5e2057927fa6
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69034201"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Windows で IoT Edge を使用し、Linux コンテナーを実行する

Windows コンピューターを使用し、Linux デバイス向けの IoT Edge モジュールをテストします。 

運用シナリオでは、Windows デバイスでは Windows コンテナーのみを実行してください。 ただし、一般的な開発シナリオでは、Windows コンピューターを使用して Linux 向けの IoT Edge モジュールを構築することがあります。 Windows 向けの IoT Edge ランタイムを利用することで、**テストと開発**目的で Linux コンテナーを実行できます。 

この記事では、Linux コンテナーと共に Windows x64 (AMD/Intel) システムに Azure IoT Edge ランタイムをインストールする手順を示します。 全インストール パラメーターの詳細など、IoT Edge ランタイム インストーラーの詳細については、「[Windows に Azure IoT Edge ランタイムをインストールする](how-to-install-iot-edge-windows.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このセクションを使用して、使用する Windows デバイスが IoT Edge をサポートできるかどうかを確認し、インストールする前に Windows デバイスをコンテナー エンジン用に準備します。 

### <a name="supported-windows-versions"></a>サポートされている Windows バージョン

Linux コンテナーを使用する Azure IoT Edge は、[Docker Desktop の要件](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)を満たす任意のバージョンの Windows で実行できます。

IoT Edge の最新バージョンの内容の詳細については、 [Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)に関する記事を参照してください。

IoT Edge を仮想マシンに インストールする場合は、入れ子になった仮想化を有効にし、2 GB 以上のメモリを割り当てます。 入れ子になった仮想化を有効にする方法は、使用しているハイパーバイザーによって異なります。 Hyper-V の場合、第 2 世代仮想マシンでは入れ子になった仮想化が既定で有効になっています。 VMWare の場合、仮想マシンでこの機能を有効に切り替えます。 

### <a name="prepare-the-container-engine"></a>コンテナー エンジンを準備する 

Azure IoT Edge は、[OCI と互換性のある](https://www.opencontainers.org/)コンテナー エンジンに依存します。 Windows コンピューターで Windows コンテナーを実行することと、Linux コンテナーを実行することの構成上の最大の違いは、IoT Edge インストールには Windows コンテナー ランタイムが含まれるが、IoT Edge をインストールする前に、Linux コンテナーに独自のライタイムを提供する必要があるということです。 

Linux デバイス用のコンテナーを開発およびテストするために Windows コンピューターを設定するには、コンテナー エンジンとして [Docker Desktop](https://www.docker.com/docker-windows) を使用できます。 IoT Edge をインストールする前に、Docker をインストールし、[Linux コンテナー](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)を使用するように構成する必要があります。  

使用する IoT Edge デバイスが Windows コンピューターの場合は、Hyper-V の[システム要件](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements)を満たしていることを確認します。

## <a name="install-iot-edge-on-a-new-device"></a>新しいデバイスに IoT Edge をインストールする

>[!NOTE]
>Azure IoT Edge ソフトウェア パッケージには、(LICENSE ディレクトリ内の) パッケージ内にあるライセンス条項が適用されます。 パッケージを使用する前に、ライセンス条項をお読みください。 インストールし、パッケージを使用すると、これらの条項に同意したものと見なされます。 ライセンス条項に同意しない場合は、パッケージを使用しないでください。

PowerShell スクリプトは、Azure IoT Edge セキュリティ デーモンをダウンロードしてインストールします。 セキュリティ デーモンは、2 つのランタイム モジュールのうちの 1 つである IoT Edge エージェントを開始し、これによってもう 1 つモジュールのリモート デプロイが可能になります。 

IoT Edge ランタイムをデバイスに初めてインストールするときは、IoT ハブからの ID を使用してデバイスをプロビジョニングする必要があります。 IoT Hub によって提供されるデバイス接続文字列を使用して、1 つの IoT Edge デバイスを手動でプロビジョニングすることもできますし、 Device Provisioning Service を使用して、複数のデバイスを自動的にプロビジョニングすることもできます。これは、セットアップするデバイスが多数ある場合に便利です。 

「[Windows に Azure IoT Edge ランタイムをインストールする](how-to-install-iot-edge-windows.md)」という記事に、インストールのさまざまなオプションとパラメーターに関する詳細があります。 Docker Desktop をインストールし、Linux コンテナーを使用するように構成したら、インストール時の主な違いは **-ContainerOs** パラメーターで Linux を宣言することです。 例: 

1. 新しい IoT Edge デバイスを登録していない場合は登録し、接続文字列を取得します。 このセクションの後半で使用するため、接続文字列をコピーします。 この手順は次のツールで完了できます。

   * [Azure Portal](how-to-register-device-portal.md)
   * [Azure CLI](how-to-register-device-cli.md)
   * [Visual Studio Code](how-to-register-device-vscode.md)

2. PowerShell を管理者として実行します。

   >[!NOTE]
   >IoT Edge をインストールするには、PowerShell (x86) ではなく PowerShell の AMD64 セッションを使用します。 どのセッションの種類を使用しているかがわからない場合は、次のコマンドを実行します。
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. **Deploy-IoTEdge** コマンドを使用し、ご使用の Windows コンピューターがサポートされているバージョンであることを確認し、コンテナー機能をオンに設定し、moby ランタイム (Linux コンテナーには使用されません) と IoT Edge ランタイムをダウンロードします。 何も指定しない場合、このコマンドは Windows コンテナーを選択します。そのため、目的のコンテナー オペレーティング システムとして Linux を宣言してください。 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. この時点で、IoT Core デバイスが自動的に再起動することがあります。 その他の Windows 10 または Windows Server デバイスでは、再起動が求められることがあります。 その場合、デバイスをすぐに再起動してください。 デバイスが起動されたら、管理者として PowerShell を再実行します。

5. **Initialize-IoTEdge** コマンドを使用して、お使いのマシンに IoT Edge ランタイムを構成します。 何も指定しない場合、このコマンドはデバイス接続文字列を使用した手動プロビジョニングを選択します。 もう一度、目的のコンテナー オペレーティング システムとして Linux を宣言します。 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. 入力を求められたら、手順 1 で取得したデバイス接続文字列を指定します。 このデバイス接続文字列により、物理デバイスと IoT Hub のデバイス ID が関連付けられます。 

   デバイス接続文字列は次の形式をとります。疑問符は入れないでください。`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>インストールの成功を確認する

IoT Edge サービスの状態を確認します。 実行中として一覧表示されるはずです。  

```powershell
Get-Service iotedge
```

過去 5 分のサービス ログを調べます。 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

実行中のモジュールを一覧表示します。 新規インストール後、実行されているモジュールは **edgeAgent** だけです。 初めて [IoT Edge モジュールをデプロイ](how-to-deploy-modules-portal.md)した後、別のシステム モジュール **edgeHub** もデバイスで起動します。 


```powershell
iotedge list
```

## <a name="next-steps"></a>次の手順

ランタイムがインストールされた IoT Edge デバイスがプロビジョニングされたら、次は [IoT Edge モジュールをデプロイ](how-to-deploy-modules-portal.md)できます。

IoT Edge を正常にインストールできない場合は、[トラブルシューティング](troubleshoot.md)のページを調べてください。

IoT Edge の既存のインストールを最新バージョンに更新するには、「[IoT Edge セキュリティ デーモンおよびランタイムの更新](how-to-update-iot-edge.md)」を参照してください。
