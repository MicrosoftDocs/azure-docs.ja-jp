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
monikerRange: iotedge-2018-06
ms.openlocfilehash: bb87d09b67658f9a3d7c68f635bfcd9a29de675c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201630"
---
# <a name="install-and-manage-azure-iot-edge-with-windows-containers"></a>Windows コンテナーを使用して Azure IoT Edge をインストールおよび管理する

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Azure IoT Edge ランタイムを使用すると、デバイスを IoT Edge デバイスに変えることができます。 IoT Edge ランタイムを使用してデバイスを構成すると、クラウドからデバイスへのビジネス ロジックのデプロイを開始できます。 詳細については、「[Azure IoT Edge ランタイムとそのアーキテクチャの概要](iot-edge-runtime.md)」を参照してください。

IoT Edge デバイスを設定するには、2 つのステップがあります。 1 番目のステップでは、ランタイムとその依存関係をインストールします。 2 番目のステップでは、デバイスをクラウド内にあるその ID に接続し、IoT Hub で認証を設定します。

この記事では、Windows コンテナーを使用して Azure IoT Edge ランタイムをインストールする手順を示します。 Windows デバイス上で Linux コンテナーを使用することを検討している場合は、[Azure IoT Edge for Linux on Windows](how-to-install-iot-edge-on-windows.md) に関する記事を参照してください。

>[!NOTE]
>Windows コンテナーを使用する Azure IoT Edge は、Azure IoT Edge のバージョン 1.2 以降サポートされなくなります。
>
>IoT Edge を Windows デバイスで実行するための新しい方法である [Azure IoT Edge for Linux on Windows](iot-edge-for-linux-on-windows.md) の使用を検討してください。

## <a name="prerequisites"></a>前提条件

* Windows デバイス

  Windows コンテナーを使用する IoT Edge には、Windows バージョン 1809、ビルド 17763 が必要です。これは、最新の [Windows 長期サポート ビルド](/windows/release-information/)です。 サポートされている SKU の一覧については、[サポートされているシステムの一覧](support.md#operating-systems)を確認してください。

* [登録されたデバイス ID](how-to-register-device.md)

  対称キー認証を使用してデバイスを登録した場合は、デバイス接続文字列を用意しておいてください。

  X.509 自己署名証明書認証を使用してデバイスを登録した場合は、デバイスを登録するために使用した ID 証明書を少なくとも 1 つと、それに対応する秘密キーをデバイス上で使用できるようにしておいてください。

## <a name="install-a-container-engine"></a>コンテナー エンジンをインストールする

Azure IoT Edge は、OCI と互換性のある [Moby](https://github.com/moby/moby) などのコンテナー ランタイムに依存します。 インストール スクリプトに含まれる Moby ベースのエンジンです。 エンジンをインストールするための追加の手順はありません。

## <a name="install-the-iot-edge-security-daemon"></a>IoT Edge セキュリティ デーモンをインストールする

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

3. メッセージが表示されたら、デバイスを再起動します。

デバイスに IoT Edge をインストールするときは、追加のパラメーターを使用して、プロセスを次のように変更できます。

* プロキシ サーバーを経由するようトラフィックを誘導する
* オフライン インストール用にローカル ディレクトリをインストーラーに指定する。

これらの追加パラメーターの詳細については、[Windows コンテナーを使用した IoT Edge 用の PowerShell スクリプト](reference-windows-scripts.md)に関するページを参照してください。

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

これらの追加パラメーターの詳細については、[Windows コンテナーを使用した IoT Edge 用の PowerShell スクリプト](reference-windows-scripts.md)に関するページを参照してください。

### <a name="option-2-authenticate-with-x509-certificates"></a>オプション 2:X.509 証明書を使用した認証

この時点で、IoT Edge ランタイムは Windows デバイスにインストールされています。クラウドの ID と認証情報を使用してデバイスをプロビジョニングする必要があります。

このセクションでは、X.509 証明書認証を使用してデバイスをプロビジョニングする手順について説明します。 デバイスを IoT Hub に登録し、IoT Edge デバイスにある証明書と秘密キーに一致する拇印を提供している必要があります。 そうでない場合は、「[IoT Edge デバイスを IoT Hub に登録する](how-to-register-device.md)」の手順に従ってください。

1. IoT Edge デバイスで、PowerShell を管理者として実行します。

2. [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) コマンドを使用して、お使いのマシンに IoT Edge ランタイムを構成します。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
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

これらの追加パラメーターの詳細については、[Windows コンテナーを使用した IoT Edge 用の PowerShell スクリプト](reference-windows-scripts.md)に関するページを参照してください。

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
   * 1\.1 リリース チャネルからの Microsoft-Azure-IoTEdge-amd64.cab。

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
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge
```

Update-IoTEdge コマンドを実行すると、デバイスから 2 つのランタイム コンテナー イメージと共にセキュリティ デーモンが削除および更新されます。 config.yaml ファイルは、Moby コンテナー エンジンのデータと同様に、デバイス上にも保存されています。 構成情報の保持は、更新プロセス中に、接続文字列または Device Provisioning Service 情報をデバイスに再び提供する必要がないことを意味します。

特定のバージョンのセキュリティ デーモンに更新する場合は、[IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)から、対象となる 1.1 リリース チャネルからのバージョンを見つけます。 そのバージョンで、**Microsoft-Azure-IoTEdge.cab** ファイルをダウンロードします。 次に、`-OfflineInstallationPath` パラメーターを使用してローカル ファイルの場所を指定します。 次に例を示します。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>`-OfflineInstallationPath` パラメーターでは、指定されたディレクトリで **Microsoft-Azure-IoTEdge.cab** という名前のファイルを検索します。 ファイルの名前を変更し、アーキテクチャのサフィックスがある場合は削除します。

デバイスをオフラインで更新する場合は、[Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)から、対象のバージョンを見つけます。 そのバージョンで、*IoTEdgeSecurityDaemon.ps1* と *Microsoft-Azure-IoTEdge.cab* の各ファイルをダウンロードします。 各リリースの機能をサポートするために機能が変更されるため、使用する .cab ファイルと同じリリースの PowerShell スクリプトを使用することが重要です。

ダウンロードした .cab ファイルにアーキテクチャのサフィックスが付いている場合は、そのファイルの名前を **Microsoft-Azure-IoTEdge.cab** のみに変更します。

オフライン コンポーネントを使用して更新するには、PowerShell スクリプトのローカル コピーを[ドット ソース](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing)で使用します。 次に、`Update-IoTEdge` コマンドの一部として `-OfflineInstallationPath` パラメーターを使用し、ファイル ディレクトリへの絶対パスを指定します。 たとえば、次のように入力します。

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

更新オプションの詳細については、コマンド `Get-Help Update-IoTEdge -full` を使用するか、[Windows コンテナーを使用した IoT Edge 用の PowerShell スクリプト](reference-windows-scripts.md)に関するページを参照してください。

## <a name="uninstall-iot-edge"></a>IoT Edge をアンインストールする

デバイスから IoT Edge のインストールを削除する必要がある場合は、次のコマンドを使用します。

IoT Edge のインストールを Windows デバイスから削除する場合は、管理用 PowerShell ウィンドウから [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) コマンドを使用します。 このコマンドは、IoT Edge ランタイムを既存の構成と Moby エンジン データと一緒に削除します。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

アンインストール オプションの詳細については、`Get-Help Uninstall-IoTEdge -full` コマンドを使用してください。

## <a name="next-steps"></a>次のステップ

[IoT Edge モジュールのデプロイ](how-to-deploy-modules-portal.md)に進んで、デバイスにモジュールをデプロイする方法を確認します。
