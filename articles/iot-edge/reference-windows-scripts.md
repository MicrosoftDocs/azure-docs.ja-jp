---
title: Windows コンテナーを使用した Azure IoT Edge 用のスクリプト | Microsoft Docs
description: Windows デバイスで IoT Edge PowerShell スクリプトのインストール、アンインストール、更新を行うためのリファレンス情報
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/06/2020
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: a919238e4a62ae8954e101cb21a2fd4943191f6a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489526"
---
# <a name="powershell-scripts-for-iot-edge-with-windows-containers"></a>Windows コンテナーを使用した IoT Edge 用の PowerShell スクリプト

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Windows デバイスで IoT Edge をインストール、更新、またはアンインストールする PowerShell スクリプトについて説明します。

この記事で説明するコマンドは、すべての [IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)と共にリリースされる `IoTEdgeSecurityDaemon.ps1` ファイルからのものです。 最新バージョンのスクリプトは、いつでも aka.ms/iotedge-win で入手できます。

`Invoke-WebRequest` コマンドレットを使用して任意のコマンドを実行すると、最新のスクリプト バージョンにアクセスできます。 次に例を示します。

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge
```

このスクリプト、または特定のリリースに付属するバージョンのスクリプトをダウンロードしてコマンドを実行することもできます。 次に例を示します。

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Deploy-IoTEdge
```

指定されたスクリプトは、セキュリティを強化するために署名されています。 署名を確認するには、スクリプトをデバイスにダウンロードし、次の PowerShell コマンドを実行します。

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

署名が検証されている場合、出力状態は **有効** になります。

## <a name="deploy-iotedge"></a>Deploy-IoTEdge

Deploy-IoTEdge コマンドでは、IoT Edge セキュリティ デーモンとその依存関係がダウンロードされてデプロイされます。 デプロイ コマンドでは、たとえば次のような一般的なパラメーターが受け付けられます。 完全なリストについては、`Get-Help Deploy-IoTEdge -full` コマンドを使用してください。  

| パラメーター | 指定可能な値 | 説明 |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** または **Linux** | コンテナーのオペレーティング システムを指定しない場合は、Windows が既定値です。<br><br>Windows コンテナーの場合、IoT Edge ではインストールに含まれる Moby コンテナー エンジンが使用されます。 Linux コンテナーの場合、インストールを開始する前にコンテナー エンジンをインストールする必要があります。 |
| **Proxy** | プロキシ URL | デバイスがインターネットに接続するためにプロキシ サーバーを経由する必要がある場合は、このパラメーターを含めます。 詳細については、「[IoT Edge デバイスを構成してプロキシ サーバー経由で通信する](how-to-configure-proxy-support.md)」を参照してください。 |
| **OfflineInstallationPath** | ディレクトリ パス | このパラメーターが含まれる場合、インストーラーはインストールに必要な IoT Edge cab ファイルと VC ランタイム MSI ファイルを、一覧で示されているディレクトリでチェックします。 ディレクトリで見つからないファイルはダウンロードされます。 両方のファイルがディレクトリにある場合は、インターネットに接続せずに IoT Edge をインストールできます。 特定のバージョンを使用するために、このパラメーターを使用することもできます。 |
| **InvokeWebRequestParameters** | パラメーターと値のハッシュ テーブル | インストール中には、いくつかの Web 要求が行われます。 それらの Web 要求のパラメーターを設定するにはこのフィールドを使用します。 このパラメーターは、プロキシ サーバーの資格情報を構成するために使用すると便利です。 詳細については、「[IoT Edge デバイスを構成してプロキシ サーバー経由で通信する](how-to-configure-proxy-support.md)」を参照してください。 |
| **RestartIfNeeded** | なし | このフラグを使用すると、必要に応じて、デプロイ スクリプトでメッセージを表示せずにコンピューターを再起動できます。 |

## <a name="initialize-iotedge"></a>Initialize-IoTEdge

Initialize-IoTEdge コマンドは、デバイスの接続文字列と運用の詳細で IoT Edge を構成します。 このコマンドによって生成される情報の多くは、その後 iotedge\config.yaml ファイルに格納されます。 初期化コマンドでは、たとえば次のような一般的なパラメーターが受け付けられます。 完全なリストについては、`Get-Help Initialize-IoTEdge -full` コマンドを使用してください。

| パラメーター | 指定可能な値 | 説明 |
| --------- | --------------- | -------- |
| **ManualConnectionString** | なし | **スイッチ パラメーター**。 **既定値**。 プロビジョニングの種類が指定されていない場合は、接続文字列を使用した手動プロビジョニングが既定値です。<br><br>デバイス接続文字列を指定して、手動でデバイスをプロビジョニングすることを宣言します。 |
| **ManualX509** | なし | **スイッチ パラメーター**。 プロビジョニングの種類が指定されていない場合は、接続文字列を使用した手動プロビジョニングが既定値です。<br><br>ID 証明書と秘密キーを指定して、手動でデバイスをプロビジョニングすることを宣言します。
| **DpsTpm** | なし | **スイッチ パラメーター**。 プロビジョニングの種類が指定されていない場合は、接続文字列を使用した手動プロビジョニングが既定値です。<br><br>Device Provisioning Service (DPS) 経由でプロビジョニングするために、DPS のスコープ ID とデバイスの登録 ID を指定することを宣言します。  |
| **DpsSymmetricKey** | なし | **スイッチ パラメーター**。 プロビジョニングの種類が指定されていない場合は、接続文字列を使用した手動プロビジョニングが既定値です。<br><br>構成証明用の対称キーと一緒に Device Provisioning Service (DPS) のスコープ ID とデバイスの登録 ID を指定して、DPS を通してプロビジョニングすることを宣言します。 |
| **DpsX509** | なし | **スイッチ パラメーター**。 プロビジョニングの種類が指定されていない場合は、接続文字列を使用した手動プロビジョニングが既定値です。<br><br>構成証明用の X.509 ID 証明書および秘密キーと一緒に、Device Provisioning Service (DPS) のスコープ ID とデバイスの登録 ID を指定して、DPS を通してプロビジョニングすることを宣言します。  |
| **DeviceConnectionString** | 単一引用符で囲まれた、IoT Hub に登録されている IoT Edge デバイスからの接続文字列 | 接続文字列を使用した手動プロビジョニングには **必須** です。 スクリプト パラメーターに接続文字列を指定しなかった場合は、これを指定するよう促されます。 |
| **IotHubHostName** | デバイスの接続先となる IoT ハブのホスト名。 | X.509 証明書を使用した手動プロビジョニングには **必須** です。 *{ハブ名}.azure-devices.net* の形式にします。 |
| **DeviceId** | IoT Hub に登録されているデバイス ID からのデバイス ID。 | X.509 証明書を使用した手動プロビジョニングには **必須** です。 |
| **ScopeId** | IoT Hub に関連付けられた Device Provisioning Service のインスタンスからのスコープ ID。 | DPS プロビジョニングの場合は **必須** です。 スクリプト パラメーターにスコープ ID を指定しなかった場合、これを指定するよう促されます。 |
| **RegistrationId** | デバイスによって生成された登録 ID | DPS プロビジョニングの場合、TPM または対称キー構成証明を使用している場合は **必須** です。 X.509 証明書の構成証明を使用している場合は **省略可能** です。 |
| **X509IdentityCertificate** | デバイス上の X.509 デバイス ID 証明書への URI パス。 | X.509 証明書の構成証明を使用している場合、手動または DPS のいずれかのプロビジョニングには **必須** です。 |
| **X509IdentityPrivateKey** | デバイス上の X.509 デバイス ID 証明書キーへの URI パス。 | X.509 証明書の構成証明を使用している場合、手動または DPS のいずれかのプロビジョニングには **必須** です。 |
| **SymmetricKey** | DPS の使用時に IoT Edge デバイス ID をプロビジョニングするために使用される対称キー | DPS プロビジョニングの場合、対称キー構成証明を使用している場合は **必須** です。 |
| **ContainerOs** | **Windows** または **Linux** | コンテナーのオペレーティング システムを指定しない場合は、Windows が既定値です。<br><br>Windows コンテナーの場合、IoT Edge ではインストールに含まれる Moby コンテナー エンジンが使用されます。 Linux コンテナーの場合、インストールを開始する前にコンテナー エンジンをインストールする必要があります。 |
| **DeviceCACertificate** | デバイス上の X.509 デバイス CA 証明書への URI パス。 | `C:\ProgramData\iotedge\config.yaml` ファイル内で構成することもできます。 詳細については、「[IoT Edge デバイスで証明書を管理する](how-to-manage-device-certificates.md)」を参照してください。 |
| **DeviceCAPrivateKey** | デバイス上の X.509 デバイス CA 秘密キーへの URI パス。 | `C:\ProgramData\iotedge\config.yaml` ファイル内で構成することもできます。 詳細については、「[IoT Edge デバイスで証明書を管理する](how-to-manage-device-certificates.md)」を参照してください。 |
| **InvokeWebRequestParameters** | パラメーターと値のハッシュ テーブル | インストール中には、いくつかの Web 要求が行われます。 それらの Web 要求のパラメーターを設定するにはこのフィールドを使用します。 このパラメーターは、プロキシ サーバーの資格情報を構成するために使用すると便利です。 詳細については、「[IoT Edge デバイスを構成してプロキシ サーバー経由で通信する](how-to-configure-proxy-support.md)」を参照してください。 |
| **AgentImage** | IoT Edge エージェント イメージの URI | 既定では、新しい IoT Edge のインストールでは、IoT Edge エージェント イメージの最新のローリング タグを使用します。 イメージ バージョンについて特定のタグを設定したり、独自のエージェント イメージを提供したりするには、このパラメーターを使用します。 詳細については、[IoT Edge タグ](how-to-update-iot-edge.md#understand-iot-edge-tags)に関する記事を参照してください。 |
| **ユーザー名** | コンテナー レジストリのユーザー名 | プライベート レジストリ内のコンテナーに - AgentImage パラメーターを設定する場合にのみ、このパラメーターを使用します。 レジストリへのアクセス権を持つユーザー名を指定します。 |
| **パスワード** | セキュリティで保護されたパスワード文字列 | プライベート レジストリ内のコンテナーに - AgentImage パラメーターを設定する場合にのみ、このパラメーターを使用します。 レジストリにアクセスするためのパスワードを指定します。 |

## <a name="update-iotedge"></a>Update-IoTEdge

| パラメーター | 指定可能な値 | 説明 |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** または **Linux** | コンテナー OS を指定しない場合、Windows が既定値になります。 Windows コンテナーの場合、コンテナー エンジンがインストールに含まれます。 Linux コンテナーの場合、インストールを開始する前にコンテナー エンジンをインストールする必要があります。 |
| **Proxy** | プロキシ URL | デバイスがインターネットに接続するためにプロキシ サーバーを経由する必要がある場合は、このパラメーターを含めます。 詳細については、「[IoT Edge デバイスを構成してプロキシ サーバー経由で通信する](how-to-configure-proxy-support.md)」を参照してください。 |
| **InvokeWebRequestParameters** | パラメーターと値のハッシュ テーブル | インストール中には、いくつかの Web 要求が行われます。 それらの Web 要求のパラメーターを設定するにはこのフィールドを使用します。 このパラメーターは、プロキシ サーバーの資格情報を構成するために使用すると便利です。 詳細については、「[IoT Edge デバイスを構成してプロキシ サーバー経由で通信する](how-to-configure-proxy-support.md)」を参照してください。 |
| **OfflineInstallationPath** | ディレクトリ パス | このパラメーターが含まれる場合、インストーラーはインストールに必要な IoT Edge cab ファイルと VC ランタイム MSI ファイルを、一覧で示されているディレクトリでチェックします。 ディレクトリで見つからないファイルはダウンロードされます。 両方のファイルがディレクトリにある場合は、インターネットに接続せずに IoT Edge をインストールできます。 特定のバージョンを使用するために、このパラメーターを使用することもできます。 |
| **RestartIfNeeded** | なし | このフラグを使用すると、必要に応じて、デプロイ スクリプトでメッセージを表示せずにコンピューターを再起動できます。 |

## <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| パラメーター | 指定可能な値 | 説明 |
| --------- | --------------- | -------- |
| **Force** | なし | このフラグは、前回のアンインストールの試行が失敗した場合に、アンインストールを強制します。
| **RestartIfNeeded** | なし | このフラグを使用すると、必要に応じて、アンインストール スクリプトでメッセージを表示せずにコンピューターを再起動できます。 |

## <a name="next-steps"></a>次のステップ

以下の記事で、これらのコマンドの使用方法を学習します。

* [Azure IoT Edge for Windows をインストールまたはアンインストールする](how-to-install-iot-edge-windows-on-windows.md)
