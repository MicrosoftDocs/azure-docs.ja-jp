---
title: Azure Security Center での Endpoint Protection ソリューションの検出と正常性評価 | Microsoft Docs
description: Endpoint Protection ソリューションを検出し、正常と識別する方法。
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2019
ms.author: v-mohabe
ms.openlocfilehash: a5cd0f88173abb65a120aa305206505af51d9f9e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861378"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Azure Security Center での Endpoint Protection の評価と推奨事項

Azure Security Center の Endpoint Protection の評価と推奨事項では、Endpoint Protection ソリューションの[サポートされている](https://docs.microsoft.com/azure/security-center/security-center-os-coverage)バージョンが検出され、正常性評価が行われます。 このトピックでは、Azure Security Center による Endpoint Protection ソリューションに対する次の 2 つの推奨事項が生成されるシナリオについて説明します。

* **仮想マシンに Endpoint Protection ソリューションをインストールする**
* **マシンの Endpoint Protection の正常性の問題を解決する**

## <a name="windows-defender"></a>Windows Defender

* "**仮想マシンに Endpoint Protection ソリューションをインストールする**" の推奨事項は、[Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) が実行され、結果が **AMServiceEnabled:False** の場合に生成されます

* "**マシンの Endpoint Protection の正常性の問題を解決する**" の推奨事項は、[Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) が実行され、次のいずれかまたは両方が発生した場合に生成されます。

  * 次のプロパティの少なくとも 1 つが false である。

     **AMServiceEnabled**

     **AntispywareEnabled**

     **RealTimeProtectionEnabled**

     **BehaviorMonitorEnabled**

     **IoavProtectionEnabled**

     **OnAccessProtectionEnabled**

  * 次のプロパティのいずれかまたは両方が 7 以上である場合。

     **AntispywareSignatureAge**

     **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Microsoft System Center Endpoint Protection

* "**仮想マシンに Endpoint Protection ソリューションをインストールする**" の推奨事項は、**SCEPMpModule ("$env:ProgramFiles\Microsoft Security Client\MpProvider\MpProvider.psd1")** をインポートし、**AMServiceEnabled = false** で **Get-MProtComputerStatus** 結果を実行している場合に生成されます

* "**マシンの Endpoint Protection の正常性の問題を解決する**" の推奨事項は、**Get-MprotComputerStatus** が実行され、次のいずれかまたは両方が発生した場合に生成されます。

    * 次のプロパティの少なくとも 1 つが false である。

       **AMServiceEnabled**
    
       **AntispywareEnabled**
    
       **RealTimeProtectionEnabled**
    
       **BehaviorMonitorEnabled**
    
       **IoavProtectionEnabled**
    
       **OnAccessProtectionEnabled**
          
    * 次のシグネチャの更新のいずれかまたは両方が 7 以上である場合。 

       **AntispywareSignatureAge**
    
       **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* "**仮想マシンに Endpoint Protection ソリューションをインストールする**" の推奨事項は、次の 1 つまたは複数のチェックが満たされていない場合に生成されます。
    * **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent** が存在する
    * **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder** が存在する
    * インストール フォルダーに **dsq_query.cmd** ファイルがある
    * **Component.AM.mode: on - Trend Micro Deep Security Agent detected** で **dsa_query.cmd** 結果が実行されている

## <a name="symantec-endpoint-protection"></a>Symantec Endpoint Protection
"**仮想マシンに Endpoint Protection ソリューションをインストールする**" の推奨事項は、次のいずれかのチェックが満たされていない場合に生成されます。

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

または

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

"**マシンの Endpoint Protection の正常性の問題を解決する**" の推奨事項は、次のいずれかのチェックが満たされていない場合に生成されます。  

* Symantec バージョン 12 以上のチェック:レジストリの場所:**HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion" - 値 "PRODUCTVERSION"**

* リアルタイム保護の状態のチェック:**HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**

* シグネチャの更新の状態のチェック:**HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 日**

* フル スキャンの状態のチェック:**HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 日**

* Symantec 12 のシグネチャのバージョンへのシグネチャ バージョン番号パスの検索:**レジストリ パス + "CurrentVersion\SharedDefs" - 値 "SRTSP"** 

* Symantec 14 のシグネチャのバージョンへのパス:**レジストリ パス + "CurrentVersion\SharedDefs\SDSDefs" - 値 "SRTSP"**

レジストリ パス:

* **"HKLM:\Software\Symantec\Symantec Endpoint Protection" + $Path;**
* **"HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>McAfee Endpoint Protection for Windows

"**仮想マシンに Endpoint Protection ソリューションをインストールする**" の推奨事項は、次のチェックが満たされていない場合に生成されます。

* **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion** が存在する

* **HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

"**マシンの Endpoint Protection の正常性の問題を解決する**" の推奨事項は、次のチェックが満たされていない場合に生成されます。

* McAfee バージョン:**HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**

* シグネチャのバージョンの検索:**HKLM:\Software\McAfee\AVSolution\DS\DS - 値 "dwContentMajorVersion"**

* シグネチャの日付の検索:**HKLM:\Software\McAfee\AVSolution\DS\DS - 値 "szContentCreationDate" >= 7 日**

* スキャン日の検索:**HKLM:\Software\McAfee\Endpoint\AV\ODS - 値 "LastFullScanOdsRunTime" >= 7 日**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>Linux 向け McAfee エンドポイント セキュリティの脅威防止 

**仮想マシンに Endpoint Protection ソリューションをインストールする** の推奨事項は、次の一方または両方のチェックが満たされていない場合に生成されます。  

- ファイル **/opt/isec/ens/threatprevention/bin/isecav** が終了する 

- **"/opt/isec/ens/threatprevention/bin/isecav --version"** の出力:**McAfee name = McAfee Endpoint Security for Linux Threat Prevention and McAfee version >= 10**

**マシンのエンドポイント保護の正常性に関する問題を解決する** 推奨事項は、次の一方または両方のチェックが満たされていない場合に生成されます。

- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** から **Quick scan, Full scan** が返され、両方のスキャンが <= 7 日間

- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** から **DAT and engine Update time** が返され、両方が <= 7 日間

- **"/opt/isec/ens/threatprevention/bin/isecav --getoasconfig --summary"** から **On Access Scan** ステータスが返される

## <a name="sophos-antivirus-for-linux"></a>Linux 向け Sophos Antivirus 

**Install endpoint protection solutions on virtual machine**\(仮想マシンにエンドポイント保護ソリューションをインストールする\) 推奨事項は、次の一方または両方のチェックが満たされていない場合に生成されます。

- ファイル **/opt/sophos-av/bin/savdstatus** が終了する。あるいは、カスタマイズされた場所 **"readlink $(which savscan)"** を探す

- **"/opt/sophos-av/bin/savdstatus --version"** から Sophos 名 = **Sophos Anti-Virus and Sophos version >= 9** が返される

**マシンのエンドポイント保護の正常性に関する問題を解決する** 推奨事項は、次の一方または両方のチェックが満たされていない場合に生成されます。

- **"/opt/sophos-av/bin/savlog --maxage=7 | grep -i "Scheduled scan .\* completed" | tail -1"** から値が返される   

- **"/opt/sophos-av/bin/savlog --maxage=7 | grep "scan finished"** | tail -1" から値が返される   

- **"/opt/sophos-av/bin/savdstatus --lastupdate"** から lastUpdate が返される。これは <= 7 日間になる必要がある 

- **"/opt/sophos-av/bin/savdstatus -v"** が **"On-access scanning is running"** に等しい 

- **"/opt/sophos-av/bin/savconfig get LiveProtection"** から有効が返される  

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

Microsoft マルウェア対策拡張機能ログは次から入手できます。  
**%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(Or PaaSAntimalware)\1.5.5.x(version#)\CommandExecution.log**

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。
