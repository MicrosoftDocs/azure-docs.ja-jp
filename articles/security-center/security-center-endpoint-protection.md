---
title: Azure Security Center での Endpoint Protection の推奨事項
description: Endpoint Protection ソリューションを検出し、正常と識別する方法。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: memildin
ms.openlocfilehash: 1ce20deed8b26dc5f5bebf4656dd3f1c370d766f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561230"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Azure Security Center での Endpoint Protection の評価と推奨事項

Azure Security Center は、Endpoint Protection ソリューションの[サポートされる](security-center-services.md#endpoint-supported)バージョンの正常性評価を提供します。 この記事では、Security Center で次の 2 つの推奨事項を生成するシナリオについて説明します。

* **仮想マシンに Endpoint Protection ソリューションをインストールする**
* **マシンの Endpoint Protection の正常性の問題を解決する**

## <a name="windows-defender"></a>Windows Defender

* Security Center は、[Get-MpComputerStatus](/powershell/module/defender/get-mpcomputerstatus) が実行され、結果が **AMServiceEnabled: Fasle の場合に、 **"仮想マシンに Endpoint Protection ソリューションをインストールする"** ことを推奨します。False**

* Security Center は、[Get-MpComputerStatus](/powershell/module/defender/get-mpcomputerstatus) が実行され、次のいずれかが発生した場合に、 **"マシンの Endpoint Protection の正常性の問題を解決する"** ことを推奨します。

  * 次のプロパティのいずれかが false である場合。

    - **AMServiceEnabled**
    - **AntispywareEnabled**
    - **RealTimeProtectionEnabled**
    - **BehaviorMonitorEnabled**
    - **IoavProtectionEnabled**
    - **OnAccessProtectionEnabled**

  * 次のプロパティの一方または両方が 7 以上である場合。

    - **AntispywareSignatureAge**
    - **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Microsoft System Center Endpoint Protection

* Security Center では、**SCEPMpModule ("$env:ProgramFiles\Microsoft Security Client\MpProvider\MpProvider.psd1")** をインポートし、**Get-MProtComputerStatus** を実行して、その結果が **AMServiceEnabled = false** の場合は、 **"仮想マシンに Endpoint Protection ソリューションをインストールする"** ことを推奨しています。

* Security Center は、**Get-MprotComputerStatus** が実行され、次のいずれかが発生した場合に、 **"マシンの Endpoint Protection の正常性の問題を解決する"** ことを推奨します。

  * 次のプロパティの少なくとも 1 つが false である。

    - **AMServiceEnabled**
    - **AntispywareEnabled**
    - **RealTimeProtectionEnabled**
    - **BehaviorMonitorEnabled**
    - **IoavProtectionEnabled**
    - **OnAccessProtectionEnabled**

  * 次のシグネチャの更新のいずれかまたは両方が 7 以上である場合。

    * **AntispywareSignatureAge**
    * **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* Security Center は、次のいずれかのチェックが満たされていない場合、 **"仮想マシンに Endpoint Protection ソリューションをインストールする"** ことを推奨します。
    - **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent** が存在する
    - **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder** が存在する
    - インストール フォルダーに **dsa_query.cmd** ファイルがある
    - **Component.AM.mode: on - Trend Micro Deep Security Agent detected** で **dsa_query.cmd** 結果が実行されている

## <a name="symantec-endpoint-protection"></a>Symantec Endpoint Protection
Security Center は、次のいずれかのチェックが満たされていない場合、 **"仮想マシンに Endpoint Protection ソリューションをインストールする"** ことを推奨します。

- **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**
- **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

または

- **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**
- **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Security Center は、次のいずれかのチェックが満たされていない場合、 **"マシンの Endpoint Protection の正常性の問題を解決する"** ことを推奨します。

- Symantec バージョン 12 以上のチェック:レジストリの場所:**HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion" - 値 "PRODUCTVERSION"**
- リアルタイム保護の状態のチェック: **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**
- シグネチャの更新の状態のチェック:**HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 日**
- フル スキャンの状態のチェック:**HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 日**
- Symantec 12 のシグネチャのバージョンへのシグネチャ バージョン番号パスの検索:**レジストリ パス + "CurrentVersion\SharedDefs" - 値 "SRTSP"** 
- Symantec 14 のシグネチャのバージョンへのパス:**レジストリ パス + "CurrentVersion\SharedDefs\SDSDefs" - 値 "SRTSP"**

レジストリ パス:
- **"HKLM:\Software\Symantec\Symantec Endpoint Protection" + $Path;**
- **"HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>McAfee Endpoint Protection for Windows

Security Center は、次のいずれかのチェックが満たされていない場合、 **"仮想マシンに Endpoint Protection ソリューションをインストールする"** ことを推奨します。

- **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion** が存在する
- **HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

Security Center は、次のいずれかのチェックが満たされていない場合、 **"マシンの Endpoint Protection の正常性の問題を解決する"** ことを推奨します。

- McAfee バージョン:**HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**
- シグネチャのバージョンの検索:**HKLM:\Software\McAfee\AVSolution\DS\DS - 値 "dwContentMajorVersion"**
- シグネチャの日付の検索:**HKLM:\Software\McAfee\AVSolution\DS\DS - 値 "szContentCreationDate" >= 7 日**
- スキャン日の検索:**HKLM:\Software\McAfee\Endpoint\AV\ODS - 値 "LastFullScanOdsRunTime" >= 7 日**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>Linux 向け McAfee エンドポイント セキュリティの脅威防止 

Security Center は、次のいずれかのチェックが満たされていない場合、 **"仮想マシンに Endpoint Protection ソリューションをインストールする"** ことを推奨します。

- ファイル **/opt/isec/ens/threatprevention/bin/isecav** が存在する
- **"/opt/isec/ens/threatprevention/bin/isecav --version"** の出力:**McAfee name = McAfee Endpoint Security for Linux Threat Prevention and McAfee version >= 10**

Security Center は、次のいずれかのチェックが満たされていない場合、 **"マシンの Endpoint Protection の正常性の問題を解決する"** ことを推奨します。

- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** から **Quick scan, Full scan** が返され、両方のスキャンが <= 7 日間
- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** から **DAT and engine Update time** が返され、両方が <= 7 日間
- **"/opt/isec/ens/threatprevention/bin/isecav --getoasconfig --summary"** から **On Access Scan** ステータスが返される

## <a name="sophos-antivirus-for-linux"></a>Linux 向け Sophos Antivirus 

Security Center は、次のいずれかのチェックが満たされていない場合、 **"仮想マシンに Endpoint Protection ソリューションをインストールする"** ことを推奨します。
- ファイル **/opt/sophos-av/bin/savdstatus** が終了する。あるいは、カスタマイズされた場所 **"readlink $(which savscan)"** を探す
- **"/opt/sophos-av/bin/savdstatus --version"** から Sophos 名 = **Sophos Anti-Virus and Sophos version >= 9** が返される

Security Center は、次のいずれかのチェックが満たされていない場合、 **"マシンの Endpoint Protection の正常性の問題を解決する"** ことを推奨します。
- **"/opt/sophos-av/bin/savlog --maxage=7 | grep -i "Scheduled scan .\* completed" | tail -1"** から値が返される
- **"/opt/sophos-av/bin/savlog --maxage=7 | grep "scan finished"** | tail -1" から値が返される
- **"/opt/sophos-av/bin/savdstatus --lastupdate"** から lastUpdate (<= 7 日間でなければならない) が返される 
- **"/opt/sophos-av/bin/savdstatus -v"** が **"On-access scanning is running"** に等しい 
- **"/opt/sophos-av/bin/savconfig get LiveProtection"** から有効が返される

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

Microsoft Antimalware 拡張機能のログは、 **%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(または PaaSAntimalware)\1.5.5.x(バージョン #)\CommandExecution.log** で入手できます

### <a name="support"></a>サポート

さらに支援が必要な場合は、[MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートにお問い合わせください。 または、Azure サポート インシデントを送信します。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。