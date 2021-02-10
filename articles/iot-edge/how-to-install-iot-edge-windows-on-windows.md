---
title: Azure IoT Edge for Windows をインストールする | Microsoft Docs
description: Azure IoT Edge for Windows コンテナーを Windows デバイスにインストールする
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: kgremban
ms.openlocfilehash: 7857f93e8c767f270041bb6bf041447786ce19ff
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633579"
---
# <a name="install-and-manage-azure-iot-edge-for-windows"></a>Azure IoT Edge for Windows をインストールおよび管理する

Azure IoT Edge for Windows は、ホスト Windows デバイスで直接実行され、Windows コンテナーを使用してエッジでビジネス ロジックを実行します。

Azure IoT Edge ランタイムを使用すると、デバイスを IoT Edge デバイスに変えることができます。 このランタイムは、Raspberry Pi のような小型デバイスにも、産業用サーバーのような大型デバイスにもデプロイすることができます。 IoT Edge ランタイムを使用してデバイスを構成すると、クラウドからデバイスへのビジネス ロジックのデプロイを開始できます。 詳細については、「[Azure IoT Edge ランタイムとそのアーキテクチャの概要](iot-edge-runtime.md)」を参照してください。

>[!NOTE]
>Azure IoT Edge for Windows は、Azure IoT Edge のバージョン 1.2.0 以降サポートされなくなります。
>
>IoT Edge を Windows デバイス上で、Azure IoT Edge for Linux を Windows 上で実行するための新しい方法を使用することを検討してください。

<!-- TODO: link to EFLOW-->

IoT Edge デバイスを設定するには、2 つのステップがあります。 1 番目のステップでは、ランタイムとその依存関係をインストールします。 2 番目のステップでは、デバイスをクラウド内にあるその ID に接続し、IoT Hub で認証を設定します。

この記事では、Windows デバイスに Azure IoT Edge ランタイムをインストールする手順を示します。 ランタイムをインストールする場合は、Linux コンテナーと Windows コンテナーのどちらを使用するかを選択できます。 現在、運用シナリオでは、Windows 上の Windows コンテナーのみがサポートされています。 Windows 上の linux コンテナーは、開発およびテストのシナリオに役立ちます。特に、Linux デバイスに展開するために Windows PC で開発している場合に便利です。

## <a name="prerequisites"></a>前提条件

* Windows デバイス

  Windows コンテナーを使用する IoT Edge には、Windows バージョン 1809、ビルド 17762 が必要です。これは、最新の [Windows 長期サポート ビルド](/windows/release-information/)です。 開発とテストのシナリオの場合は、コンテナー機能をサポートするどの SKU (Pro、Enterprise、Server など) でも機能します。 ただし、運用環境に移行する前に、[サポートされているシステムの一覧](support.md#operating-systems)を必ず確認してください。

  Linux コンテナーを使用する IoT Edge は、[Docker Desktop の要件](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)を満たす任意のバージョンの Windows で実行できます。

* デバイスでのコンテナーのサポート

  Azure IoT Edge は、[OCI と互換性のある](https://www.opencontainers.org/)コンテナー エンジンに依存します。 デバイスがコンテナーをサポートできることを確認します。

  IoT Edge を仮想マシンに インストールしている場合は、入れ子になった仮想化を有効にし、2 GB 以上のメモリを割り当てます。 Hyper-V の場合、第 2 世代仮想マシンでは入れ子になった仮想化が既定で有効になっています。 VMWare の場合、仮想マシンでこの機能を有効に切り替えます。

  IoT Edge を IoT Core デバイスにインストールしている場合は、[リモート PowerShell セッション](/windows/iot-core/connect-your-device/powershell)で次のコマンドを使用して、お使いのデバイスで Windows コンテナーがサポートされているかどうかを確認します。

  ```powershell
  Get-Service vmcompute
  ```

* [登録されたデバイス ID](how-to-register-device.md)

  対称キー認証を使用してデバイスを登録した場合は、デバイス接続文字列を用意しておいてください。

  X.509 自己署名証明書認証を使用してデバイスを登録した場合は、デバイスを登録するために使用した ID 証明書を少なくとも 1 つと、それに対応する秘密キーをデバイス上で使用できるようにしておいてください。

## <a name="install-a-container-engine"></a>コンテナー エンジンをインストールする

Azure IoT Edge は、OCI と互換性のあるコンテナー ランタイムに依存します。 運用環境のシナリオでは、Moby ベースのエンジンを使用することをお勧めします。 Moby エンジンは、Azure IoT Edge で公式にサポートされている唯一のコンテナー エンジンです。 Docker CE/EE コンテナー イメージは、Moby ランタイムと互換性があります。

運用環境のシナリオでは、インストール スクリプトに含まれる Moby ベースのエンジンを使用します。 エンジンをインストールするための追加の手順はありません。

Linux コンテナーを使用する IoT Edge の場合は、独自のコンテナー ランタイムを用意する必要があります。 続ける前に、[Docker Desktop](https://docs.docker.com/docker-for-windows/install/) をデバイスにインストールし、[Linux コンテナーを使用する](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)ように構成します。

## <a name="install-the-iot-edge-security-daemon"></a>IoT Edge セキュリティ デーモンをインストールする

>[!TIP]
>IoT Core デバイスの場合、リモート PowerShell セッションを使用してインストール コマンドを実行することをお勧めします。 詳細については、「[Windows IoT 用 PowerShell の使用](/windows/iot-core/connect-your-device/powershell)」を参照してください。

1. PowerShell を管理者として実行します。

   PowerShell(x86) ではなく、PowerShell の AMD64 セッションを使用します。 使用しているセッションの種類がわからない場合は、次のコマンドを実行します。

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) コマンドを実行します。これにより、次のタスクが実行されます。

   * お使いの Windows コンピューターがサポートされているバージョンであることを確認します。
   * コンテナー機能をオンにします。
   * Moby エンジンと IoT Edge ランタイムをダウンロードします。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

   `Deploy-IoTEdge` コマンドの既定値では Windows コンテナーが使用されます。 Linux コンテナーを使用する場合は、`ContainerOs` パラメーターを追加します。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

3. この時点で、IoT Core デバイスが自動的に再起動することがあります。 Windows 10 デバイスまたは Windows Server デバイスでは、再起動が求められることがあります。 その場合、デバイスをすぐに再起動してください。

デバイスに IoT Edge をインストールするときは、追加のパラメーターを使用して、プロセスを次のように変更できます。

* プロキシ サーバーを経由するようトラフィックを誘導する
* オフライン インストール用にローカル ディレクトリをインストーラーに指定する。

これらの追加パラメーターの詳細については、「[Windows 上の IoT Edge 用の PowerShell スクリプト](reference-windows-scripts.md)」を参照してください。

## <a name="provision-the-device-with-its-cloud-identity"></a>クラウド ID を使用してデバイスをプロビジョニングする

これでコンテナー エンジンと IoT Edge ランタイムがデバイスにインストールされたので、次のステップに進み、クラウド ID と認証情報を使用してデバイスを設定できます。

使用する認証の種類に基づいて、次のセクションを選択してください。

* [オプション 1: 対称キーを使用した認証](#option-1-authenticate-with-symmetric-keys)
* [オプション 2: X.509 証明書を使用した認証](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>オプション 1: 対称キーを使用した認証

この時点で、IoT Edge ランタイムは Windows デバイスにインストールされています。クラウドの ID と認証情報を使用してデバイスをプロビジョニングする必要があります。

このセクションでは、対称キー認証を使用してデバイスをプロビジョニングする手順について説明します。 デバイスを IoT Hub に登録し、デバイス情報から接続文字列を取得している必要があります。 そうでない場合は、「[IoT Edge デバイスを IoT Hub に登録する](how-to-register-device.md)」の手順に従ってください。

1. IoT Edge デバイスで、PowerShell を管理者として実行します。

2. [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) コマンドを使用して、お使いのマシンに IoT Edge ランタイムを構成します。 このコマンドでは、Windows コンテナーを使用した手動プロビジョニングが既定で設定されます。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * Linux コンテナーを使用している場合は、`-ContainerOs` パラメーターをフラグに追加します。 前に実行した `Deploy-IoTEdge` コマンドで選択したコンテナー オプションと一致させてください。

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ContainerOs Linux
      ```

   * オフラインまたは特定のバージョンのインストール用に IoTEdgeSecurityDaemon.ps1 スクリプトをデバイスにダウンロードした場合は、スクリプトのローカル コピーを必ず参照してください。

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. 入力を求められたら、前のセクションで取得したデバイス接続文字列を指定します。 このデバイス接続文字列により、物理デバイスと IoT Hub のデバイス ID が関連付けられ、認証情報が提供されます。

   デバイス接続文字列は次の形式をとります。疑問符は入れないでください。`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

デバイスを手動でプロビジョニングする場合は、追加のパラメーターを使用して、プロセスを次のように変更できます。

* プロキシ サーバーを経由するようトラフィックを誘導する
* 特定の edgeAgent コンテナー イメージを宣言し、それがプライベート レジストリに存在する場合は資格情報を提供する

これらの追加パラメーターの詳細については、「[Windows 上の IoT Edge 用の PowerShell スクリプト](reference-windows-scripts.md)」を参照してください。

### <a name="option-2-authenticate-with-x509-certificates"></a>オプション 2:X.509 証明書を使用した認証

この時点で、IoT Edge ランタイムは Windows デバイスにインストールされています。クラウドの ID と認証情報を使用してデバイスをプロビジョニングする必要があります。

このセクションでは、X.509 証明書認証を使用してデバイスをプロビジョニングする手順について説明します。 デバイスを IoT Hub に登録し、IoT Edge デバイスにある証明書と秘密キーに一致する拇印を提供している必要があります。 そうでない場合は、「[IoT Edge デバイスを IoT Hub に登録する](how-to-register-device.md)」の手順に従ってください。

1. IoT Edge デバイスで、PowerShell を管理者として実行します。

2. [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) コマンドを使用して、お使いのマシンに IoT Edge ランタイムを構成します。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * Linux コンテナーを使用している場合は、`-ContainerOs` パラメーターをフラグに追加します。 前に実行した `Deploy-IoTEdge` コマンドで選択したコンテナー オプションと一致させてください。

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ManualX509 -ContainerOs Linux
      ```

   * オフラインまたは特定のバージョンのインストール用に IoTEdgeSecurityDaemon.ps1 スクリプトをデバイスにダウンロードした場合は、スクリプトのローカル コピーを必ず参照してください。

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. 入力を求められたら、次の情報を指定します。

   * **IotHubHostName**: デバイスの接続先となる IoT ハブのホスト名。 たとえば、`{IoT hub name}.azure-devices.net` のようにします。
   * **DeviceId**: デバイスを登録したときに指定した ID。
   * **X509IdentityCertificate**: デバイス上の ID 証明書の絶対パス。 たとえば、`C:\path\identity_certificate.pem` のようにします。
   * **X509IdentityPrivateKey**: 指定された ID 証明書の秘密キー ファイルの絶対パス。 たとえば、`C:\path\identity_key.pem` のようにします。

デバイスを手動でプロビジョニングする場合は、追加のパラメーターを使用して、プロセスを次のように変更できます。

* プロキシ サーバーを経由するようトラフィックを誘導する
* 特定の edgeAgent コンテナー イメージを宣言し、それがプライベート レジストリに存在する場合は資格情報を提供する

これらの追加パラメーターの詳細については、「[Windows 上の IoT Edge 用の PowerShell スクリプト](reference-windows-scripts.md)」を参照してください。

## <a name="offline-or-specific-version-installation-optional"></a>オフラインまたは特定のバージョンのインストール (省略可能)

このセクションの手順は、標準のインストール手順では説明されていないシナリオを対象としています。 次のような場合が含まれます。

* オフラインの間に IoT Edge をインストールする
* リリース候補バージョンをインストールする
* 最新以外のバージョンをインストールする

インストール中に、次の 3 つのファイルがダウンロードされます。

* PowerShell スクリプト。これには、インストール手順が含まれています。
* Microsoft Azure IoT Edge cab。これには、IoT Edge セキュリティ デーモン (iotedged)、Moby コンテナー エンジン、および Moby CLI が含まれています。
* Visual C++ 再頒布可能パッケージ (VC ランタイム) インストーラー

インストール中にデバイスがオフラインになる予定の場合、または特定のバージョンの IoT Edge をインストールする場合は、これらのファイルを事前にデバイスにダウンロードできます。 インストール時には、インストール スクリプトによって、ダウンロード済みのファイルを含むディレクトリを指定します。 インストーラーでは、最初にそのディレクトリをチェックし、見つからないコンポーネントのみをダウンロードします。 すべてのファイルがオフラインで利用可能な場合、インターネット接続なしでインストールできます。

1. IoT Edge の最新のインストール ファイルとその以前のバージョンについては、[Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)を参照してください。

2. インストールするバージョンを見つけ、リリース ノートの **[アセット]** セクションから次のファイルを IoT デバイスにダウンロードします。

   * IoTEdgeSecurityDaemon.ps1
   * リリース 1.0.9 以降は Microsoft-Azure-IoTEdge-amd64.cab、リリース 1.0.8 以前は Microsoft-Azure-IoTEdge.cab。

   1\.0.9 以降は、テスト目的でのみ Microsoft-Azure-IotEdge-arm32.cab を使用することもできます。 IoT Edge では、現在、Windows ARM32 デバイスはサポートされていません。

   各リリースの機能をサポートするために機能が変更されるため、使用する .cab ファイルと同じリリースの PowerShell スクリプトを使用することが重要です。

3. ダウンロードした .cab ファイルにアーキテクチャのサフィックスが付いている場合は、そのファイルの名前を **Microsoft-Azure-IoTEdge.cab** のみに変更します。

4. 必要に応じて、Visual C++ 再頒布可能パッケージ インストーラーをダウンロードします。 たとえば、PowerShell スクリプトでは、バージョン [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe) を使用します。 IoT デバイスの IoT Edge ファイルと同じフォルダーにインストーラーを保存します。

5. オフライン コンポーネントを使用してインストールするには、PowerShell スクリプトのローカル コピーを[ドット ソース](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing)で実行します。

6. `-OfflineInstallationPath` パラメーターを指定して [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) コマンドを実行します。 ファイル ディレクトリへの絶対パスを指定します。 たとえば、次のように入力します。

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   デプロイ コマンドでは、指定されたローカル ファイル ディレクトリにあるコンポーネントが使用されます。 .cab ファイルまたは Visual C++ インストーラーが見つからない場合は、ダウンロードが試行されます。

## <a name="update-iot-edge"></a>IoT Edge を更新する

`Update-IoTEdge` コマンドを使用して、セキュリティ デーモンを更新します。 スクリプトにより、セキュリティ デーモンの最新バージョンが自動的にプルされます。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Update-IoTEdge コマンドを実行すると、デバイスから 2 つのランタイム コンテナー イメージと共にセキュリティ デーモンが削除および更新されます。 Moby コンテナー エンジン (Windows コンテナーを使用している場合) からのデータおよび config.yaml ファイルはデバイス上に保持されます。 構成情報の保持は、更新プロセス中に、接続文字列または Device Provisioning Service 情報をデバイスに再び提供する必要がないことを意味します。

特定のバージョンのセキュリティ デーモンに更新する場合は、[IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)から対象となるバージョンを見つけます。 そのバージョンで、**Microsoft-Azure-IoTEdge.cab** ファイルをダウンロードします。 次に、`-OfflineInstallationPath` パラメーターを使用してローカル ファイルの場所を指定します。 次に例を示します。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>`-OfflineInstallationPath` パラメーターでは、指定されたディレクトリで **Microsoft-Azure-IoTEdge.cab** という名前のファイルを検索します。 IoT Edge バージョン 1.0.9-rc4 以降では、2 つの .cab ファイル (AMD64 デバイス用と ARM32 デバイス用に 1 つずつ) を使用できます。 お使いのデバイスに合ったファイルをダウンロードし、ファイルの名前を変更して、アーキテクチャのサフィックスを削除します。

デバイスをオフラインで更新する場合は、[Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)から、対象のバージョンを見つけます。 そのバージョンで、*IoTEdgeSecurityDaemon.ps1* と *Microsoft-Azure-IoTEdge.cab* の各ファイルをダウンロードします。 各リリースの機能をサポートするために機能が変更されるため、使用する .cab ファイルと同じリリースの PowerShell スクリプトを使用することが重要です。

ダウンロードした .cab ファイルにアーキテクチャのサフィックスが付いている場合は、そのファイルの名前を **Microsoft-Azure-IoTEdge.cab** のみに変更します。

オフライン コンポーネントを使用して更新するには、PowerShell スクリプトのローカル コピーを[ドット ソース](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing)で使用します。 次に、`Update-IoTEdge` コマンドの一部として `-OfflineInstallationPath` パラメーターを使用し、ファイル ディレクトリへの絶対パスを指定します。 たとえば、次のように入力します。

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

更新オプションに関する詳細については、コマンド `Get-Help Update-IoTEdge -full` を使用するか、「[Windows の IoT Edge の PowerShell スクリプト](reference-windows-scripts.md)」を参照してください。

## <a name="uninstall-iot-edge"></a>IoT Edge をアンインストールする

デバイスから IoT Edge のインストールを削除する必要がある場合は、次のコマンドを使用します。

IoT Edge のインストールを Windows デバイスから削除する場合は、管理用 PowerShell ウィンドウから [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) コマンドを使用します。 このコマンドは、IoT Edge ランタイムを既存の構成と Moby エンジン データと一緒に削除します。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

`Uninstall-IoTEdge` コマンドは、Windows IoT Core では動作しません。 IoT Edge を削除するには、Windows IoT Core イメージを再デプロイする必要があります。

アンインストール オプションの詳細については、`Get-Help Uninstall-IoTEdge -full` コマンドを使用してください。

## <a name="next-steps"></a>次のステップ

[IoT Edge モジュールのデプロイ](how-to-deploy-modules-portal.md)に進んで、デバイスにモジュールをデプロイする方法を確認します。
