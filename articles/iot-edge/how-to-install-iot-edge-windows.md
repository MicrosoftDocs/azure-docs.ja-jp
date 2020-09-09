---
title: Azure IoT Edge on Windows のインストール | Microsoft Docs
description: Windows 10、Windows Server、Windows IoT Core への Azure IoT Edge のインストール手順
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: kgremban
ms.openlocfilehash: ba3e8b9d7649d56d1639f7f608d85a2da04ff74a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84465560"
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

Windows 用の IoT Edge には Windows バージョン 1809、ビルド 17763 が必要です。これは、最新の [Windows の長期サポートのビルド](https://docs.microsoft.com/windows/release-information/)です。 Windows SKU のサポートについては、運用シナリオまたは開発およびテストのシナリオのどちらに対して準備しているかに基づいて、サポート対象を確認してください。

* **運用**: 運用シナリオ向けに現在サポートされているオペレーティング システムに関する最新の情報については、「[Azure IoT Edge のサポートされるシステム](support.md#operating-systems)」を参照してください。
* **開発とテスト**:開発およびテストのシナリオでは、コンテナー機能がサポートされる Windows ビルド 17763 のあらゆる SKU (Pro、Enterprise、Server など) に、Azure IoT Edge と Windows コンテナーをインストールできます。

IoT Core デバイスには、IoT Edge ランタイムをサポートするための IoT Core Windows コンテナー オプション機能が含まれている必要があります。 [リモート PowerShell セッション](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell)で次のコマンドを使用して、お使いのデバイスで Windows コンテナーがサポートされていることを確認します。

```powershell
Get-Service vmcompute
```

サービスが存在する場合は、正常な応答が返され、サービスの状態が**実行中**として表示されます。 `vmcompute` サービスが見つからない場合、お使いのデバイスは IoT Edge の要件を満たしていません。 この機能のサポートについて、ハードウェア プロバイダーにお問い合わせください。

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

### <a name="option-1-install-and-manually-provision"></a>オプション 1: インストールして手動でプロビジョニングする

この最初のオプションでは、IoT Hub で生成される**デバイス接続文字列**を指定して、デバイスをプロビジョニングします。

この例は、Windows コンテナーでの手動インストールを示します。

1. 新しい IoT Edge デバイスを登録していない場合は登録し、**デバイス接続文字列**を取得します。 このセクションの後半で使用するため、接続文字列をコピーします。 この手順は次のツールで完了できます。

   * [Azure Portal](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

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

この 2 番目のオプションでは、IoT Hub Device Provisioning Service を使用してデバイスをプロビジョニングします。 使用する[構成証明メカニズム](../iot-dps/concepts-security.md#attestation-mechanism)に固有にその他の情報と共に、Device Provisioning Service インスタンスからの**スコープ ID** を指定します。

* [Windows 上で、仮想 TPM を使用するシミュレートされた IoT Edge デバイスを作成し、プロビジョニングする](how-to-auto-provision-simulated-device-windows.md)
* [X.509 証明書を使用してシミュレートされた IoT Edge デバイスを作成およびプロビジョニングする](how-to-auto-provision-x509-certs.md)
* [対称キーの構成証明を使用して IoT Edge デバイスを作成およびプロビジョニングする](how-to-auto-provision-symmetric-keys.md)

デバイスを自動でインストールおよびプロビジョニングするとき、追加のパラメーターを使用して、インストールを次のように変更できます。

* プロキシ サーバーを経由するようトラフィックを誘導する
* インストーラーでオフライン ディレクトリを指定する
* 特定のエージェント コンテナー イメージを宣言し、それがプライベート レジストリに存在する場合は資格情報を提供する

これらのインストール オプションの詳細については、この記事を読み進めるか、[すべてのインストール パラメーター](#all-installation-parameters)にスキップしてください。

## <a name="offline-or-specific-version-installation"></a>オフラインまたは特定のバージョンのインストール

インストール中に、次の 3 つのファイルがダウンロードされます。

* PowerShell スクリプト。これには、インストール手順が含まれています。
* Microsoft Azure IoT Edge cab。これには、IoT Edge セキュリティ デーモン (iotedged)、Moby コンテナー エンジン、および Moby CLI が含まれています。
* Visual C++ 再頒布可能パッケージ (VC ランタイム) インストーラー

インストール中にデバイスがオフラインになる予定の場合、または特定のバージョンの IoT Edge をインストールする場合は、これらのファイルを事前にデバイスにダウンロードできます。 インストール時には、インストール スクリプトによって、ダウンロード済みのファイルを含むディレクトリを指定します。 インストーラーでは、最初にそのディレクトリをチェックし、見つからないコンポーネントのみをダウンロードします。 すべてのファイルがオフラインで利用可能な場合、インターネット接続なしでインストールできます。

オフライン インストール パス パラメーターを使用して IoT Edge を更新することもできます。 詳細については、[IoT Edge セキュリティ デーモンおよびランタイムの更新](how-to-update-iot-edge.md)を参照してください。

1. IoT Edge の最新のインストール ファイルとその以前のバージョンについては、[Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)を参照してください。

2. インストールするバージョンを見つけ、リリース ノートの **[アセット]** セクションから次のファイルを IoT デバイスにダウンロードします。

   * IoTEdgeSecurityDaemon.ps1
   * リリース 1.0.9 以降は Microsoft-Azure-IoTEdge-amd64.cab、リリース 1.0.8 以前は Microsoft-Azure-IoTEdge.cab。

   1\.0.9 以降は、テスト目的でのみ Microsoft-Azure-IotEdge-arm32.cab を使用することもできます。 IoT Edge では、現在、Windows ARM32 デバイスはサポートされていません。

   各リリースの機能をサポートするために機能が変更されるため、使用する .cab ファイルと同じリリースの PowerShell スクリプトを使用することが重要です。

3. ダウンロードした .cab ファイルにアーキテクチャのサフィックスが付いている場合は、そのファイルの名前を **Microsoft-Azure-IoTEdge.cab** のみに変更します。

4. 必要に応じて、Visual C++ 再頒布可能パッケージ インストーラーをダウンロードします。 たとえば、PowerShell スクリプトでは、バージョン [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe) を使用します。 IoT デバイスの IoT Edge ファイルと同じフォルダーにインストーラーを保存します。

5. オフライン コンポーネントを使用してインストールするには、PowerShell スクリプトのローカル コピーを[ドット ソース](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing)で実行します。 次に、`Deploy-IoTEdge` コマンドの一部として `-OfflineInstallationPath` パラメーターを使用して、ファイル ディレクトリへの絶対パスを指定します。 たとえば、次のように入力します。

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   デプロイ コマンドでは、指定されたローカル ファイル ディレクトリにあるコンポーネントが使用されます。 .cab ファイルまたは Visual C++ インストーラーが見つからない場合は、ダウンロードが試行されます。

6. IoT Hub で ID を使用してデバイスをプロビジョニングするには、`Initialize-IoTEdge` コマンドを実行します。 手動プロビジョニング用のデバイス接続文字列を指定するか、前述の[自動的なプロビジョニング](#option-2-install-and-automatically-provision)のセクションで説明されている方法のいずれかを選択します。

   `Deploy-IoTEdge` の実行後にデバイスが再起動された場合は、`Initialize-IoTEdge` を実行する前に、PowerShell スクリプトを再度ドット ソースで実行します。

オフライン インストール オプションの詳細については、「[すべてのインストール パラメーター](#all-installation-parameters)」に進んでください。

## <a name="verify-successful-installation"></a>インストールの成功を確認する

IoT Edge サービスの状態を確認します。 実行中として一覧表示されるはずです。  

```powershell
Get-Service iotedge
```

過去 5 分のサービス ログを調べます。 IoT Edge ランタイムのインストールが終了した直後は、**Deploy-IoTEdge** と **Initialize-IoTEdge** が実行している間、エラーの一覧が表示される可能性があります。 サービスは構成される前に開始しようとしているので、これらのエラーは予期されるものです。

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

[トラブルシューティング ツール](troubleshoot.md#run-the-check-command)を実行して、最も一般的な構成とネットワークのエラーを確認します。

```powershell
iotedge check
```

最初のモジュールをデバイス上の IoT Edge にデプロイするまで、 **$edgeHub** システム モジュールはデバイスに展開されません。 その結果、自動チェックからは `Edge Hub can bind to ports on host` 接続チェックのエラーが返されます。 このエラーは、モジュールをデバイスに展開した後に発生した場合でなければ、無視できます。

最後に、実行中のモジュールを一覧表示します。

```powershell
iotedge list
```

新規インストール後、実行されているモジュールは **edgeAgent** だけです。 初めて [IoT Edge モジュールをデプロイ](how-to-deploy-modules-portal.md)した後、別のシステム モジュール **edgeHub** もデバイスで起動します。

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

## <a name="uninstall-iot-edge"></a>IoT Edge をアンインストールする

IoT Edge のインストールを Windows デバイスから削除する場合は、管理用 PowerShell ウィンドウから次のコマンドを使用します。 このコマンドは、IoT Edge ランタイムを既存の構成と Moby エンジン データと一緒に削除します。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Uninstall-IoTEdge コマンドは、Windows IoT Core では動作しません。 Windows IoT Core デバイスから IoT Edge を削除するには、Windows IoT Core イメージを再デプロイする必要があります。

アンインストール オプションの詳細については、`Get-Help Uninstall-IoTEdge -full` コマンドを使用してください。

## <a name="verify-installation-script"></a>インストール スクリプトを確認する

この記事に記載されているインストール コマンドでは、`aka.ms/iotedge-win` からのインストール スクリプトを要求するために、Invoke-WebRequest コマンドレットを使用します。 このリンクは、最新の [IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)からの `IoTEdgeSecurityDaemon.ps1` スクリプトを指しています。 また、このスクリプト、または特定のリリースのスクリプトのバージョンをダウンロードして、IoT Edge デバイスでインストール コマンドを実行することもできます。

指定されたスクリプトは、セキュリティを強化するために署名されています。 署名を確認するには、スクリプトをデバイスにダウンロードし、次の PowerShell コマンドを実行します。

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

署名が検証されている場合、出力状態は**有効**になります。

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
| **DeviceConnectionString** | 単一引用符で囲まれた、IoT Hub に登録されている IoT Edge デバイスからの接続文字列 | 手動プロビジョニングの場合は**必須**です。 スクリプト パラメーターに接続文字列を指定しなかった場合は、これを指定するよう促されます。 |
| **ScopeId** | IoT Hub に関連付けられた Device Provisioning Service のインスタンスからのスコープ ID。 | DPS プロビジョニングの場合は**必須**です。 スクリプト パラメーターにスコープ ID を指定しなかった場合、これを指定するよう促されます。 |
| **RegistrationId** | デバイスによって生成された登録 ID | DPS プロビジョニングの場合、TPM または対称キー構成証明を使用している場合は**必須**です。 X.509 証明書の構成証明を使用している場合は**省略可能**です。 |
| **X509IdentityCertificate** | デバイス上の X.509 デバイス ID 証明書への URI パス。 | DPS プロビジョニングの場合、X.509 証明書の構成証明を使用している場合は**必須**です。 |
| **X509IdentityPrivateKey** | デバイス上の X.509 デバイス ID 証明書キーへの URI パス。 | DPS プロビジョニングの場合、X.509 証明書の構成証明を使用している場合は**必須**です。 |
| **SymmetricKey** | DPS の使用時に IoT Edge デバイス ID をプロビジョニングするために使用される対称キー | DPS プロビジョニングの場合、対称キー構成証明を使用している場合は**必須**です。 |
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

## <a name="next-steps"></a>次のステップ

ランタイムがインストールされた IoT Edge デバイスがプロビジョニングされたら、次は [IoT Edge モジュールをデプロイ](how-to-deploy-modules-portal.md)できます。

IoT Edge を正常にインストールできない場合は、[トラブルシューティング](troubleshoot.md)のページを調べてください。

IoT Edge の既存のインストールを最新バージョンに更新するには、「[IoT Edge セキュリティ デーモンおよびランタイムの更新](how-to-update-iot-edge.md)」を参照してください。
