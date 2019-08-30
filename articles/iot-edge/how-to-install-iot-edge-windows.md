---
title: Azure IoT Edge on Windows のインストール | Microsoft Docs
description: Windows 10、Windows Server、Windows IoT Core への Azure IoT Edge のインストール手順
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: e5b99bba3c3b21ea9662845928c523c329695bf8
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877236"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Windows に Azure IoT Edge ランタイムをインストールする

Azure IoT Edge ランタイムを使用すると、デバイスを IoT Edge デバイスに変えることができます。 このランタイムは、Raspberry Pi のような小型デバイスにも、産業用サーバーのような大型デバイスにもデプロイすることができます。 IoT Edge ランタイムを使用してデバイスを構成すると、クラウドからデバイスへのビジネス ロジックのデプロイを開始できます。 

IoT Edge ランタイムの動作については、「[Azure IoT Edge ランタイムとそのアーキテクチャの概要](iot-edge-runtime.md)」を参照してください。

この記事では、Windows コンテナーを使用して Windows x64 (AMD/Intel) システムに Azure IoT Edge ランタイムをインストールする手順を示します。

> [!NOTE]
> Windows オペレーティング システムの既知の問題では、IoT Edge モジュール (プロセスから分離された Windows Nano Server コンテナー) の実行時に、電源状態がスリープおよび休止状態に移行できなくなります。 この問題は、デバイスのバッテリ寿命に影響を与えます。
>
> この問題を回避するには、これらの電源状態を使用する前に、コマンド `Stop-Service iotedge` を使用して実行中のすべての IoT Edge モジュールを停止します。 

Windows システム上での Linux コンテナーの使用は、Azure IoT Edge に対して推奨またはサポートされている実稼働構成ではありません。 ただし、開発とテストの目的には使用できます。 詳しくは、「[Windows で IoT Edge を使用し、Linux コンテナーを実行する](how-to-install-iot-edge-windows-with-linux.md)」をご覧ください。

IoT Edge の最新バージョンの内容については、[Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

このセクションを使用して、使用する Windows デバイスが IoT Edge をサポートできるかどうかを確認し、インストールする前に Windows デバイスをコンテナー エンジン用に準備します。 

### <a name="supported-windows-versions"></a>サポートされている Windows バージョン

開発およびテストのシナリオでは、コンテナー機能をサポートする任意のバージョンの Windows 10 または Windows Server 2019 (ビルド 17763) に、Azure IoT Edge と Windows コンテナーをインストールできます。 運用シナリオ向けに現在サポートされているオペレーティング システムについては、「[Azure IoT Edge のサポートされるシステム](support.md#operating-systems)」を参照してください。 

### <a name="prepare-for-a-container-engine"></a>コンテナー エンジンを準備する 

Azure IoT Edge は、[OCI と互換性のある](https://www.opencontainers.org/)コンテナー エンジンに依存します。 運用シナリオの場合は、インストール スクリプトに含める Moby エンジンを使用して、Windows デバイスで Windows コンテナーを実行します。 

## <a name="install-iot-edge-on-a-new-device"></a>新しいデバイスに IoT Edge をインストールする

>[!NOTE]
>Azure IoT Edge ソフトウェア パッケージには、(LICENSE ディレクトリ内の) パッケージ内にあるライセンス条項が適用されます。 パッケージを使用する前に、ライセンス条項をお読みください。 インストールし、パッケージを使用すると、これらの条項に同意したものと見なされます。 ライセンス条項に同意しない場合は、パッケージを使用しないでください。

PowerShell スクリプトは、Azure IoT Edge セキュリティ デーモンをダウンロードしてインストールします。 セキュリティ デーモンは、2 つのランタイム モジュールのうちの 1 つである IoT Edge エージェントを開始し、これによってもう 1 つモジュールのリモート デプロイが可能になります。 

>[!TIP]
>IoT Core デバイスの場合、RemotePowerShell セッションを使用してインストール コマンドを実行することをお勧めします。 詳細については、「[Windows IoT 用 PowerShell の使用](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell)」を参照してください。

IoT Edge ランタイムをデバイスに初めてインストールするときは、IoT ハブからの ID を使用してデバイスをプロビジョニングする必要があります。 IoT Hub によって提供されるデバイス接続文字列を使用して、1 つの IoT Edge デバイスを手動でプロビジョニングできます。 Device Provisioning Service (DPS) を使用して、複数のデバイスを自動的にプロビジョニングすることもできます。これは、セットアップするデバイスが多数ある場合に便利です。 目的にプロビジョニング方法に応じて、適切なインストール スクリプトを選択してください。 

次のセクションでは、新しいデバイス上で IoT Edge インストール スクリプトを使用する場合の一般的なユース ケースとパラメーターについて説明します。 

### <a name="option-1-install-and-manually-provision"></a>オプション 1:インストールして手動でプロビジョニングする

この最初のオプションでは、IoT Hub で生成される**デバイス接続文字列**を指定して、デバイスをプロビジョニングします。 

この例は、Windows コンテナーでの手動インストールを示します。

1. 新しい IoT Edge デバイスを登録していない場合は登録し、**デバイス接続文字列**を取得します。 このセクションの後半で使用するため、接続文字列をコピーします。 この手順は次のツールで完了できます。

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

3. **Deploy-IoTEdge** コマンドを使用して、ご使用の Windows コンピューターがサポートされているバージョンであることを確認し、コンテナー機能をオンに設定した後、moby ランタイムと IoT Edge ランタイムをダウンロードします。 コマンドの既定値では Windows コンテナーが使用されます。 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. この時点で、IoT Core デバイスが自動的に再起動することがあります。 その他の Windows 10 または Windows Server デバイスでは、再起動が求められることがあります。 その場合、デバイスをすぐに再起動してください。 デバイスが起動されたら、管理者として PowerShell を再実行します。

5. **Initialize-IoTEdge** コマンドを使用して、お使いのマシンに IoT Edge ランタイムを構成します。 このコマンドでは、Windows コンテナーを使用した手動プロビジョニングが既定で設定されます。 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. 入力を求められたら、手順 1 で取得したデバイス接続文字列を指定します。 このデバイス接続文字列により、物理デバイスと IoT Hub のデバイス ID が関連付けられます。 

   デバイス接続文字列は次の形式をとります。疑問符は入れないでください。`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. 「[インストールの成功を確認する](#verify-successful-installation)」の手順に従って、デバイス上の IoT Edge の状態を確認します。 

デバイスを手動でインストールおよびプロビジョニングするとき、追加のパラメーターを使用して、インストールを次のように変更できます。

* プロキシ サーバーを経由するようトラフィックを誘導する
* インストーラーでオフライン ディレクトリを指定する
* 特定のエージェント コンテナー イメージを宣言し、それがプライベート レジストリに存在する場合は資格情報を提供する

これらのインストール オプションの詳細については、「[すべてのインストール パラメーター](#all-installation-parameters)」に進んでください。

### <a name="option-2-install-and-automatically-provision"></a>オプション 2:インストールして自動的にプロビジョニングする

この 2 番目のオプションでは、IoT Hub Device Provisioning Service を使用してデバイスをプロビジョニングします。 Device Provisioning Service インスタンスから**スコープ ID** を指定し デバイスから**登録 ID** を指定します。 [対称キー](how-to-auto-provision-symmetric-keys.md)を使用するときなど、DPS を使用してプロビジョニングを行う場合、使用する構成証明メカニズムに応じた追加の値が必要になることがあります。

次の例は、Windows コンテナーと TPM 構成証明での自動インストールを示しています。

1. 「[Windows 上のシミュレートされた TPM IoT Edge デバイスの作成とプロビジョニング](how-to-auto-provision-simulated-device-windows.md)」の手順に従って、Device Provisioning Service を設定し、その**スコープ ID** を取得し、TPM デバイスをシミュレートし、その**登録 ID** を取得した後、個別の登録を作成します。 デバイスが IoT ハブに登録されたら、インストール手順をけます。  

   >[!TIP]
   >インストールとテストを行っている間は、TPM シミュレーターを実行しているウィンドウを開いたままにしてください。 

1. PowerShell を管理者として実行します。

   >[!NOTE]
   >IoT Edge をインストールするには、PowerShell (x86) ではなく PowerShell の AMD64 セッションを使用します。 どのセッションの種類を使用しているかがわからない場合は、次のコマンドを実行します。
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

1. **Deploy-IoTEdge** コマンドを使用して、ご使用の Windows コンピューターがサポートされているバージョンであることを確認し、コンテナー機能をオンに設定した後、moby ランタイムと IoT Edge ランタイムをダウンロードします。 コマンドの既定値では Windows コンテナーが使用されます。 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. この時点で、IoT Core デバイスが自動的に再起動することがあります。 その他の Windows 10 または Windows Server デバイスでは、再起動が求められることがあります。 その場合、デバイスをすぐに再起動してください。 デバイスが起動されたら、管理者として PowerShell を再実行します。

1. **Initialize-IoTEdge** コマンドを使用して、お使いのマシンに IoT Edge ランタイムを構成します。 このコマンドでは、Windows コンテナーを使用した手動プロビジョニングが既定で設定されます。 手動プロビジョニングではなく Device Provisioning Service を使用するには、`-Dps` フラグを使用します。 `{scope ID}` を Device Provisioning Service からのスコープ ID に置き換え、`{registration ID}` をデバイスからの登録 ID に置き換えます。どちらも手順 1 で取得済みのはずです。

   TPM 構成証明で DPS を使用するために **Initialize-IoTEdge** コマンドを使用します。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

   対称キー構成証明で DPS を使用するために **Initialize-IoTEdge** コマンドを使用します。 `{symmetric key}` をデバイス キーで置き換えます。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

1. 「[インストールの成功を確認する](#verify-successful-installation)」の手順に従って、デバイス上の IoT Edge の状態を確認します。 

デバイスを手動でインストールおよびプロビジョニングするとき、追加のパラメーターを使用して、インストールを次のように変更できます。

* プロキシ サーバーを経由するようトラフィックを誘導する
* インストーラーでオフライン ディレクトリを指定する
* 特定のエージェント コンテナー イメージを宣言し、それがプライベート レジストリに存在する場合は資格情報を提供する

これらのインストール オプションの詳細については、この記事を読み進めるか、[すべてのインストール パラメーター](#all-installation-parameters)にスキップしてください。

## <a name="offline-installation"></a>オフライン インストール

インストール中に、次の 2 つのファイルがダウンロードされます。

* Microsoft Azure IoT Edge cab には、IoT Edge セキュリティ デーモン (iotedged)、Moby コンテナー エンジン、Moby CLI が含まれています。
* Visual C++ 再頒布可能パッケージ (VC ランタイム) の msi

これらのファイルのうち 1 つまたは両方をデバイスに事前にダウンロードし、ファイルを含むディレクトリをインストール スクリプトで指定することができます。 インストーラーはまずディレクトリをチェックし、見つからないコンポーネントのみダウンロードします。 すべてのファイルがオフラインで利用可能な場合、インターネット接続なしでインストールできます。 また、この機能を使用して、特定のバージョンのコンポーネントをインストールすることもできます。  

IoT Edge の最新のインストール ファイルとその以前のバージョンについては、[Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)を参照してください。

オフライン コンポーネントを使用してインストールするには、Deploy-IoTEdge コマンドの一部として `-OfflineInstallationPath` パラメーターを使用し、ファイル ディレクトリへの絶対パスを指定します。 たとえば、次のように入力します。

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

この記事で後ほど紹介する Update-IoTEdge コマンドで、オフライン インストール パス パラメーターを使用することもできます。 

## <a name="verify-successful-installation"></a>インストールの成功を確認する

IoT Edge サービスの状態を確認します。 実行中として一覧表示されるはずです。  

```powershell
Get-Service iotedge
```

過去 5 分のサービス ログを調べます。 IoT Edge ランタイムのインストールが終了した直後は、**Deploy-IoTEdge** と **Initialize-IoTEdge** が実行している間、エラーの一覧が表示される可能性があります。 サービスは構成される前に開始しようとしているので、これらのエラーは予期されるものです。 

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

実行中のモジュールを一覧表示します。 新規インストール後、実行されているモジュールは **edgeAgent** だけです。 初めて [IoT Edge モジュールをデプロイ](how-to-deploy-modules-portal.md)した後、別のシステム モジュール **edgeHub** もデバイスで起動します。 

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>モジュール コンテナーを管理する

IoT Edge サービスでは、デバイス上でコンテナー エンジンが実行されている必要があります。 モジュールをデバイスにデプロイすると、IoT Edge ランタイムでは、コンテナー エンジンを使用してクラウドのレジストリからコンテナー イメージをプルします。 IoT Edge サービスを使用すると、モジュールを操作し、ログを取得できますが、コンテナー エンジンを使用してコンテナー自体を操作することが必要な場合もあります。 

モジュールの概念の詳細については、「[Azure IoT Edge モジュールについて](iot-edge-modules.md)」をご覧ください。 

Windows IoT Edge デバイス上で Windows コンテナーを実行している場合、IoT Edge のインストールに Moby コンテナー エンジンが含まれていました。 Moby エンジンは Docker と同じ標準に基づいており、Docker Desktop と同じマシン上で並列実行されるように設計されています。 そのため、Moby エンジンによって管理されているコンテナーをターゲットにする場合は、Docker ではなくそのエンジンを明確にターゲットにする必要があります。 

たとえば、すべての Docker イメージを一覧表示するには、次のコマンドを使用します。

```powershell
docker images
```

すべての Moby イメージを一覧表示するには、Moby エンジンへのポインターを指定して同じコマンドを変更します。 

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

エンジン URI は、インストール スクリプトの出力に示されてます。また、config.yaml ファイルのコンテナー ランタイム設定セクションでも確認できます。 

![config.yaml の moby_runtime uri](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

デバイス上で実行されているコンテナーやイメージを操作するために使用できるコマンドの詳細については、[Docker コマンド ライン インターフェイス](https://docs.docker.com/engine/reference/commandline/docker/)を参照してください。

## <a name="update-an-existing-installation"></a>既存のインストールを更新する

IoT Edge ランタイムを以前デバイスにインストールしたことがあり、IoT Hub からの ID によってデバイスがプロビジョニングされている場合、デバイスの情報を再入力せずにランタイムを更新できます。 

詳細については、[IoT Edge セキュリティ デーモンおよびランタイムの更新](how-to-update-iot-edge.md)を参照してください。

この例では、既存の構成ファイルを指定し、Windows コンテナーを使用するインストールを示します。 

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Update-IoTEdge
```

IoT Edge を更新するときは、追加のパラメーターを使用して次のように更新を変更できます。

* プロキシ サーバーを経由するようトラフィックを誘導する
* インストーラーでオフライン ディレクトリを指定する 
* 必要な場合は、プロンプトなしで再起動する

IoT Edge エージェント コンテナー イメージは、以前のインストールの構成ファイルですでに設定されているため、スクリプト パラメーターで宣言することはできません。 エージェント コンテナー イメージを変更する場合は、config.yaml ファイルで行います。 

これらの更新オプションについて詳しくは、`Get-Help Update-IoTEdge -full` コマンドを使用するか、「[すべてのインストール パラメーター](#all-installation-parameters)」を参照してください。

## <a name="uninstall-iot-edge"></a>IoT Edge をアンインストールする

IoT Edge のインストールを Windows デバイスから削除する場合は、管理用 PowerShell ウィンドウから次のコマンドを使用します。 このコマンドは、IoT Edge ランタイムを既存の構成と Moby エンジン データと一緒に削除します。 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Uninstall-IoTEdge コマンドは、Windows IoT Core では動作しません。 Windows IoT Core デバイスから IoT Edge を削除するには、Windows IoT Core イメージを再デプロイする必要があります。 

アンインストール オプションの詳細については、`Get-Help Uninstall-IoTEdge -full` コマンドを使用してください。 

## <a name="all-installation-parameters"></a>すべてのインストール パラメーター

前のセクションでは、パラメーターを使用してインストール スクリプトを変更する方法の例を用いた一般的なインストール シナリオについて紹介しました。 このセクションでは、IoT Edge のインストール、更新、アンインストールに使用される一般的なパラメーターの参照テーブルを提供します。 

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

Deploy-IoTEdge コマンドでは、IoT Edge セキュリティ デーモンとその依存関係がダウンロードされてデプロイされます。 デプロイ コマンドでは、たとえば次のような一般的なパラメーターが受け付けられます。 完全なリストについては、`Get-Help Deploy-IoTEdge -full` コマンドを使用してください。  

| パラメーター | 指定可能な値 | 説明 |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** または **Linux** | コンテナーのオペレーティング システムを指定しない場合は、Windows が既定値です。<br><br>Windows コンテナーの場合、IoT Edge ではインストールに含まれる moby コンテナー エンジンが使用されます。 Linux コンテナーの場合、インストールを開始する前にコンテナー エンジンをインストールする必要があります。 |
| **Proxy** | プロキシ URL | デバイスがインターネットに接続するためにプロキシ サーバーを経由する必要がある場合は、このパラメーターを含めます。 詳細については、「[IoT Edge デバイスを構成してプロキシ サーバー経由で通信する](how-to-configure-proxy-support.md)」を参照してください。 |
| **OfflineInstallationPath** | ディレクトリ パス | このパラメーターが含まれる場合、インストーラーはインストールに必要な IoT Edge cab ファイルと VC ランタイム MSI ファイルを、一覧で示されているディレクトリでチェックします。 ディレクトリで見つからないファイルはダウンロードされます。 両方のファイルがディレクトリにある場合は、インターネットに接続せずに IoT Edge をインストールできます。 特定のバージョンを使用するために、このパラメーターを使用することもできます。 |
| **InvokeWebRequestParameters** | パラメーターと値のハッシュ テーブル | インストール中には、いくつかの Web 要求が行われます。 それらの Web 要求のパラメーターを設定するにはこのフィールドを使用します。 このパラメーターは、プロキシ サーバーの資格情報を構成するために使用すると便利です。 詳細については、「[IoT Edge デバイスを構成してプロキシ サーバー経由で通信する](how-to-configure-proxy-support.md)」を参照してください。 |
| **RestartIfNeeded** | なし | このフラグを使用すると、必要に応じて、デプロイ スクリプトでメッセージを表示せずにコンピューターを再起動できます。 |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

Initialize-IoTEdge コマンドは、デバイスの接続文字列と運用の詳細で IoT Edge を構成します。 このコマンドによって生成される情報の多くは、その後 iotedge\config.yaml ファイルに格納されます。 初期化コマンドでは、たとえば次のような一般的なパラメーターが受け付けられます。 完全なリストについては、`Get-Help Initialize-IoTEdge -full` コマンドを使用してください。 

| パラメーター | 指定可能な値 | 説明 |
| --------- | --------------- | -------- |
| **手動** | なし | **スイッチ パラメーター**。 プロビジョニングの種類を指定しない場合、既定値は手動です。<br><br>デバイスをプロビジョニングするためのデバイス接続文字列を手動で指定することを宣言します |
| **Dps** | なし | **スイッチ パラメーター**。 プロビジョニングの種類を指定しない場合、既定値は手動です。<br><br>Device Provisioning Service (DPS) 経由でプロビジョニングするために、DPS のスコープ ID とデバイスの登録 ID を指定することを宣言します。  |
| **DeviceConnectionString** | 単一引用符で囲まれた、IoT Hub に登録されている IoT Edge デバイスからの接続文字列 | 手動インストールで**必須**です。 スクリプト パラメーターに接続文字列を指定しなかった場合は、インストール中にこれを指定するよう促されます。 |
| **ScopeId** | IoT Hub に関連付けられた Device Provisioning Service のインスタンスからのスコープ ID。 | DPS インストールで**必須**です。 スクリプト パラメーターにスコープ ID を指定しなかった場合、インストール中にこれを指定するよう促されます。 |
| **RegistrationId** | デバイスによって生成された登録 ID | DPS インストールで**必須**です。 |
| **SymmetricKey** | DPS の使用時に IoT Edge デバイス ID をプロビジョニングするために使用される対称キー | 対称キー構成証明を使用している場合、DPS インストールで**必須**です。 |
| **ContainerOs** | **Windows** または **Linux** | コンテナーのオペレーティング システムを指定しない場合は、Windows が既定値です。<br><br>Windows コンテナーの場合、IoT Edge ではインストールに含まれる moby コンテナー エンジンが使用されます。 Linux コンテナーの場合、インストールを開始する前にコンテナー エンジンをインストールする必要があります。 |
| **InvokeWebRequestParameters** | パラメーターと値のハッシュ テーブル | インストール中には、いくつかの Web 要求が行われます。 それらの Web 要求のパラメーターを設定するにはこのフィールドを使用します。 このパラメーターは、プロキシ サーバーの資格情報を構成するために使用すると便利です。 詳細については、「[IoT Edge デバイスを構成してプロキシ サーバー経由で通信する](how-to-configure-proxy-support.md)」を参照してください。 |
| **AgentImage** | IoT Edge エージェント イメージの URI | 既定では、新しい IoT Edge のインストールでは、IoT Edge エージェント イメージの最新のローリング タグを使用します。 イメージ バージョンについて特定のタグを設定したり、独自のエージェント イメージを提供したりするには、このパラメーターを使用します。 詳細については、[IoT Edge タグ](how-to-update-iot-edge.md#understand-iot-edge-tags)に関する記事を参照してください。 |
| **ユーザー名** | コンテナー レジストリのユーザー名 | プライベート レジストリ内のコンテナーに - AgentImage パラメーターを設定する場合にのみ、このパラメーターを使用します。 レジストリへのアクセス権を持つユーザー名を指定します。 |
| **パスワード** | セキュリティで保護されたパスワード文字列 | プライベート レジストリ内のコンテナーに - AgentImage パラメーターを設定する場合にのみ、このパラメーターを使用します。 レジストリにアクセスするためのパスワードを指定します。 | 

### <a name="update-iotedge"></a>Update-IoTEdge

| パラメーター | 指定可能な値 | 説明 |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** または **Linux** | コンテナー OS を指定しない場合、Windows が既定値になります。 Windows コンテナーの場合、コンテナー エンジンがインストールに含まれます。 Linux コンテナーの場合、インストールを開始する前にコンテナー エンジンをインストールする必要があります。 |
| **Proxy** | プロキシ URL | デバイスがインターネットに接続するためにプロキシ サーバーを経由する必要がある場合は、このパラメーターを含めます。 詳細については、「[IoT Edge デバイスを構成してプロキシ サーバー経由で通信する](how-to-configure-proxy-support.md)」を参照してください。 |
| **InvokeWebRequestParameters** | パラメーターと値のハッシュ テーブル | インストール中には、いくつかの Web 要求が行われます。 それらの Web 要求のパラメーターを設定するにはこのフィールドを使用します。 このパラメーターは、プロキシ サーバーの資格情報を構成するために使用すると便利です。 詳細については、「[IoT Edge デバイスを構成してプロキシ サーバー経由で通信する](how-to-configure-proxy-support.md)」を参照してください。 |
| **OfflineInstallationPath** | ディレクトリ パス | このパラメーターが含まれる場合、インストーラーはインストールに必要な IoT Edge cab ファイルと VC ランタイム MSI ファイルを、一覧で示されているディレクトリでチェックします。 ディレクトリで見つからないファイルはダウンロードされます。 両方のファイルがディレクトリにある場合は、インターネットに接続せずに IoT Edge をインストールできます。 特定のバージョンを使用するために、このパラメーターを使用することもできます。 |
| **RestartIfNeeded** | なし | このフラグを使用すると、必要に応じて、デプロイ スクリプトでメッセージを表示せずにコンピューターを再起動できます。 |

### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| パラメーター | 指定可能な値 | 説明 |
| --------- | --------------- | -------- |
| **Force** | なし | このフラグは、前回のアンインストールの試行が失敗した場合に、アンインストールを強制します。 
| **RestartIfNeeded** | なし | このフラグを使用すると、必要に応じて、アンインストール スクリプトでメッセージを表示せずにコンピューターを再起動できます。 |

## <a name="next-steps"></a>次の手順

ランタイムがインストールされた IoT Edge デバイスがプロビジョニングされたら、次は [IoT Edge モジュールをデプロイ](how-to-deploy-modules-portal.md)できます。

IoT Edge を正常にインストールできない場合は、[トラブルシューティング](troubleshoot.md)のページを調べてください。

IoT Edge の既存のインストールを最新バージョンに更新するには、「[IoT Edge セキュリティ デーモンおよびランタイムの更新](how-to-update-iot-edge.md)」を参照してください。
