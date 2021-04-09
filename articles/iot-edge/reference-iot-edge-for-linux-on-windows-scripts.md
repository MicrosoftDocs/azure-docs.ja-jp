---
title: Azure IoT Edge for Linux on Windows 用の PowerShell スクリプト | Microsoft Docs
description: IoT Edge for Linux on Windows 仮想マシンのデプロイ、プロビジョニング、状態取得のための Azure IoT Edge for Linux on Windows PowerShell スクリプトのリファレンス情報。
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 02/16/2021
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: fe04615e234eaf5b006a80e6f584905626c637ba
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489487"
---
# <a name="powershell-scripts-for-iot-edge-for-linux-on-windows"></a>IoT Edge for Linux on Windows 用の PowerShell スクリプト

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

IoT Edge for Linux on Windows 仮想マシンのデプロイ、プロビジョニング、状態取得のための PowerShell スクリプトについて説明します。

この記事で説明されているコマンドは、`AzureEFLOW.psm1` ファイルからのものです。これは、ご使用のシステムの `C:\Program Files\WindowsPowerShellModules\AzureEFLOW` の `WindowsPowerShell` ディレクトリー内にあります。

## <a name="deploy-eflow"></a>Deploy-Eflow

**Deploy-Eflow** コマンドは、メインのデプロイ方法です。 このデプロイ コマンドによって仮想マシンが作成され、ファイルがプロビジョニングされ、IoT Edge エージェント モジュールがデプロイされます。 どのパラメーターも必須ではありませんが、デプロイ中に IoT Edge デバイスをプロビジョニングしたり、作成時に仮想マシンの設定を変更したりするために使用できます。 完全なリストについては、`Get-Help Deploy-Eflow -full` コマンドを使用してください。  

>[!NOTE]
>プロビジョニングの種類に関して、**X509** は現在、[Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md) を使用した X509 のプロビジョニングのみを指します。 手動の X509 プロビジョニング方法は現在サポートされていません。

| パラメーター | 指定可能な値 | 説明 |
| --------- | --------------- | -------- |
| eflowVhdxDir | ディレクトリ パス | VM の VHDX ファイルが格納されるディレクトリ。 |
| provisioningType | **manual**、**TPM**、**X509**、または **symmetric** |  IoT Edge デバイスに使用するプロビジョニングの種類を定義します。 |
| devConnString | 既存の IoT Edge デバイスのデバイス接続文字列 | IoT Edge デバイスを手動でプロビジョニングするためのデバイス接続文字列 (**manual**)。 |
| symmKey | 既存の DPS 登録の主キー、または対称キーを使用して登録された既存の IoT Edge デバイスの主キー | IoT Edge デバイスをプロビジョニングするための対称キー (**X509** または **symmetric**)。 |
| scopeId | 既存の DPS インスタンスのスコープ ID。 | IoT Edge デバイスをプロビジョニングするためのスコープ ID (**X509** または **symmetric**)。 |
| registrationId | 既存の IoT Edge デバイスの登録 ID | IoT Edge デバイスをプロビジョニングするための登録 ID (**X509** または **symmetric**)。 |
| identityCertLocVm | ディレクトリ パス。`iotedge` サービスで所有できるフォルダーに存在している必要があります | IoT Edge デバイスをプロビジョニングする際に使用する、仮想マシン上の ID 証明書の絶対宛先パス (**X509** または **symmetric**)。 |
| identityCertLocWin | ディレクトリ パス | IoT Edge デバイスをプロビジョニングする際に使用する、Windows 内の ID 証明書の絶対ソース パス (**X509** または **symmetric**)。 |
| identityPkLocVm |  | ディレクトリ パス。`iotedge` サービスで所有できるフォルダーに存在している必要があります | IoT Edge デバイスをプロビジョニングする際に使用する、仮想マシン上の ID 秘密キーの絶対宛先パス (**X509** または **symmetric**)。 |
| identityPkLocWin | ディレクトリ パス | IoT Edge デバイスをプロビジョニングする際に使用する、Windows 内の ID 秘密キーの絶対ソース パス (**X509** または **symmetric**)。 |
| vmSizeDefintion | 30 文字以下 | 仮想マシンのコア数と使用可能な RAM の定義。 **既定値**: Standard_K8S_v1。 |
| vmDiskSize | 8 GB から 256 GB | 容量可変の仮想ハード ディスクの最大ディスク サイズ。 **既定値**: 16 GB。 |
| vmUser | 30 文字以下 | 仮想マシンにログオンするためのユーザー名。 |
| vnetType | **Transparent** または **ICS** | 仮想スイッチの種類。 **既定値**: Transparent。 |
| vnetName | 64 文字以下 | 仮想スイッチの名前。 **既定値**: External。 |
| enableVtpm | なし | **スイッチ パラメーター**。 TPM を有効または無効にして仮想マシンを作成します。 |
| mobyPackageVersion | 30 文字以下 |  仮想マシン上で検証またはインストールする Moby パッケージのバージョン。  **既定値**: 19.03.11。 |
| iotedgePackageVersion | 30 文字以下 | 仮想マシン上で検証またはインストールする IoT Edge パッケージのバージョン。 **既定値**: 1.1.0。 |
| installPackages | なし | **スイッチ パラメーター**。 切り替えると、Moby と IoT Edge のパッケージが存在することの確認だけではなく、これらのパッケージのインストールが試行されます。 |

## <a name="verify-eflowvm"></a>Verify-EflowVm

**Verify-EflowVm** コマンドは、IoT Edge for Linux on Windows 仮想マシンが作成されたことを確認するために公開されている関数です。 共通のパラメーターのみを受け取り、仮想マシンが作成されていた場合は **true**、そうでない場合は **false** を返します。 追加情報については、`Get-Help Verify-EflowVm -full` コマンドを使用してください。

## <a name="provision-eflowvm"></a>Provision-EflowVm

**Provision-EflowVm** コマンドを実行すると、IoT Edge デバイスのプロビジョニング情報が仮想マシンの IoT Edge の `config.yaml` ファイルに追加されます。 **Deploy-Eflow** コマンドにパラメーターを設定することによって、デプロイ フェーズ中にプロビジョニングも実行できます。 追加情報については、`Get-Help Provision-EflowVm -full` コマンドを使用してください。

| パラメーター | 指定可能な値 | 説明 |
| --------- | --------------- | -------- |
| vmUser | 30 文字以下 | 仮想マシンにログオンするためのユーザー名。 |
| provisioningType | **manual**、**TPM**、**X509**、または **symmetric** |  IoT Edge デバイスに使用するプロビジョニングの種類を定義します。 |
| devConnString | 既存の IoT Edge デバイスのデバイス接続文字列 | IoT Edge デバイスを手動でプロビジョニングするためのデバイス接続文字列 (**manual**)。 |
| symmKey | 既存の DPS 登録の主キー、または対称キーを使用して登録された既存の IoT Edge デバイスの主キー | IoT Edge デバイスをプロビジョニングするための対称キー (**DPS**、**symmetric**)。 |
| scopeId | 既存の DPS インスタンスのスコープ ID。 | IoT Edge デバイスをプロビジョニングするためのスコープ ID (**DPS**)。 |
| registrationId | 既存の IoT Edge デバイスの登録 ID | IoT Edge デバイスをプロビジョニングするための登録 ID (**DPS**)。 |
| identityCertLocVm | ディレクトリ パス。`iotedge` サービスで所有できるフォルダーに存在している必要があります | IoT Edge デバイスをプロビジョニングする際に使用する、仮想マシン上の ID 証明書の絶対宛先パス (**DPS**、**X509**)。 |
| identityCertLocWin | ディレクトリ パス | IoT Edge デバイスをプロビジョニングする際に使用する、Windows 内の ID 証明書の絶対ソース パス (**dps**、**X509**)。 |
| identityPkLocVm |  | ディレクトリ パス。`iotedge` サービスで所有できるフォルダーに存在している必要があります | IoT Edge デバイスをプロビジョニングする際に使用する、仮想マシン上の ID 秘密キーの絶対宛先パス (**DPS**、**X509**)。 |
| identityPkLocWin | ディレクトリ パス | IoT Edge デバイスをプロビジョニングする際に使用する、Windows 内の ID 秘密キーの絶対ソース パス (**dps**、**X509**)。 |

## <a name="get-eflowvmname"></a>Get-EflowVmName

**Get-EflowVmName** コマンドは、仮想マシンの現在のホスト名を照会するために使用されます。 このコマンドは、Windows ホスト名が時間の経過と共に変わる場合があるという事実を考慮して存在しています。 共通のパラメーターのみを受け取り、仮想マシンの現在のホスト名を返します。 追加情報については、`Get-Help Get-EflowVmName -full` コマンドを使用してください。

## <a name="get-eflowlogs"></a>Get-EflowLogs

**Get-EflowLogs** コマンドは、IoT Edge for Linux on Windows のデプロイからログを収集してバンドルするために使用されます。 バンドルされたログは、`.zip` フォルダーの形式で出力されます。 追加情報については、`Get-Help Get-EflowLogs -full` コマンドを使用してください。

| パラメーター | 指定可能な値 | 説明 |
| --------- | --------------- | -------- |
| vmUser | 30 文字以下 | 仮想マシンにログオンするためのユーザー名。 |

## <a name="get-eflowvmtpmprovisioninginfo"></a>Get-EflowVmTpmProvisioningInfo

**Get-EflowVmTpmProvisioningInfo** コマンドは、仮想マシンの vTPM プロビジョニング情報を収集して表示するために使用されます。 仮想マシンが vTPM を使用せずに作成された場合は、TPM プロビジョニング情報を検出できなかったという内容が返されます。 追加情報については、`Get-Help Get-EflowVmTpmProvisioningInfo -full` コマンドを使用してください。

| パラメーター | 指定可能な値 | 説明 |
| --------- | --------------- | -------- |
| vmUser | 30 文字以下 | 仮想マシンにログオンするためのユーザー名。 |

## <a name="get-eflowvmaddr"></a>Get-EflowVmAddr

**Get-EflowVmAddr** コマンドは、仮想マシンの IP と MAC アドレスを検索して表示するために使用されます。 共通のパラメーターのみを受け取ります。 追加情報については、`Get-Help Get-EflowVmAddr -full` コマンドを使用してください。

## <a name="get-eflowvmsysteminformation"></a>Get-EflowVmSystemInformation

**Get-EflowVmSystemInformation** コマンドは、メモリやストレージの使用状況など、仮想マシンからシステム情報を収集して表示するために使用されます。 追加情報については、`Get-Help Get-EflowVmSystemInformation -full` コマンドを使用してください。

| パラメーター | 指定可能な値 | 説明 |
| --------- | --------------- | -------- |
| vmUser | 30 文字以下 | 仮想マシンにログオンするためのユーザー名。 |

## <a name="get-eflowvmedgeinformation"></a>Get-EflowVmEdgeInformation

**Get-EflowVmEdgeInformation** コマンドは、仮想マシンで実行されている IoT Edge のバージョンなど、仮想マシンから IoT Edge 情報を収集して表示するために使用されます。 追加情報については、`Get-Help Get-EflowVmEdgeInformation -full` コマンドを使用してください。

| パラメーター | 指定可能な値 | 説明 |
| --------- | --------------- | -------- |
| vmUser | 30 文字以下 | 仮想マシンにログオンするためのユーザー名。 |

## <a name="get-eflowvmedgemodulelist"></a>Get-EflowVmEdgeModuleList

**Get-EflowVmEdgeModuleList** コマンドは、仮想マシンで実行されている IoT Edge モジュールの一覧を照会して表示するために使用します。 追加情報については、`Get-Help Get-EflowVmEdgeModuleList -full` コマンドを使用してください。

| パラメーター | 指定可能な値 | 説明 |
| --------- | --------------- | -------- |
| vmUser | 30 文字以下 | 仮想マシンにログオンするためのユーザー名。 |

## <a name="get-eflowvmedgestatus"></a>Get-EflowVmEdgeStatus

**Get-EflowVmEdgeStatus** コマンドは、仮想マシン上の IoT Edge ランタイムの状態を照会して表示するために使用されます。 追加情報については、`Get-Help Get-EflowVmEdgeStatus -full` コマンドを使用してください。

| パラメーター | 指定可能な値 | 説明 |
| --------- | --------------- | -------- |
| vmUser | 30 文字以下 | 仮想マシンにログオンするためのユーザー名。 |

## <a name="get-eflowvmusername"></a>Get-EflowVmUserName

**Get-EflowVmUserName** コマンドは、仮想マシンを作成するために使用された仮想マシンのユーザー名をレジストリから照会して表示するために使用されます。 共通のパラメーターのみを受け取ります。 追加情報については、`Get-Help Get-EflowVmUserName -full` コマンドを使用してください。

## <a name="get-eflowvmsshkey"></a>Get-EflowVmSshKey

**Get-EflowVmSshKey** コマンドは、仮想マシンによって使用される SSH キーを照会して表示するために使用されます。 共通のパラメーターのみを受け取ります。 追加情報については、`Get-Help Get-EflowVmSshKey -full` コマンドを使用してください。

## <a name="ssh-eflowvm"></a>Ssh-EflowVm

**Ssh-EflowVm** コマンドは、仮想マシンへの SSH 接続に使用されます。 追加情報については、`Get-Help Ssh-EflowVm -full` コマンドを使用してください。

| パラメーター | 指定可能な値 | 説明 |
| --------- | --------------- | -------- |
| vmUser | 30 文字以下 | 仮想マシンにログオンするためのユーザー名。 |

## <a name="next-steps"></a>次のステップ

以下の記事で、これらのコマンドの使用方法を学習します。

* [Azure IoT Edge for Linux on Windows をインストールする](how-to-install-iot-edge-windows.md)

* PowerShell で使用可能なすべてのコマンドについては、[IoT Edge for Linux on Windows PowerShell スクリプトのリファレンス](reference-iot-edge-for-linux-on-windows-scripts.md#deploy-eflow)に関する記事を参照してください。
