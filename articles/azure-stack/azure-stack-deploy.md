---
title: "Azure Stack 開発キットのデプロイの前提条件 | Microsoft Docs"
description: "Azure Stack 開発キットの環境とハードウェアの要件を確認できます (クラウド オペレーター)。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 32a21d9b-ee42-417d-8e54-98a7f90f7311
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/11/2017
ms.author: erikje
ms.openlocfilehash: 73e7efb7d789fe12846d68066c0927bb123831a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stack-deployment-prerequisites"></a>Azure Stack のデプロイの前提条件

*適用対象: Azure Stack 開発キット*

[Azure Stack Development Kit](azure-stack-poc.md) をデプロイする前に、コンピューターが次の要件を満たしていることを確認してください。


## <a name="hardware"></a>ハードウェア
| コンポーネント | 最小値 | 推奨 |
| --- | --- | --- |
| ディスク ドライブ: オペレーティング システム |システム パーティション用に最低 200 GB の空きがある1 OS ディスク (SSD または HDD) |システム パーティション用に最低 200 GB の空きがある1 OS ディスク (SSD または HDD) |
| ディスク ドライブ: 開発キット データ全般* |4 つのディスク。 各ディスクに最低 140 GB の空き容量が必要 (SSD または HDD) すべての使用可能なディスクが使われます。 |4 つのディスク。 各ディスクに最低 250 GB の空き容量が必要 (SSD または HDD)。 すべての使用可能なディスクが使われます。 |
| コンピューティング: CPU |デュアル ソケット: 12 個の物理コア (合計) |デュアル ソケット: 16 個の物理コア (合計) |
| コンピューティング: メモリ |96 GB RAM |128 GB RAM (これは、PaaS リソース プロバイダーをサポートするための最低限の量です)。|
| コンピューティング: BIOS |Hyper-V 有効 (SLAT サポートあり) |Hyper-V 有効 (SLAT サポートあり) |
| ネットワーク: NIC |NIC には Windows Server 2012 R2 の認定が必要です。特別な機能は必要ありません |NIC には Windows Server 2012 R2 の認定が必要です。特別な機能は必要ありません |
| ハードウェア ロゴ認定 |[Windows Server 2012 R2 認定](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Windows Server 2012 R2 認定](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |

\* Azure から [Marketplace の項目](azure-stack-download-azure-marketplace-item.md)の多くを追加する計画の場合は、この推奨容量より多くが必要です。

**データ ディスク ドライブの構成:** すべてのデータ ドライブは同じ種類 (すべて SAS またはすべて SATA)、同じ容量である必要があります。 SAS ディスク ドライブを使う場合、ディスク ドライブは 1 つのパス経由で接続する必要があります (MPIO なし、マルチパスのサポートあり)。

**HBA 構成オプション**

* (推奨) 単純な HBA
* RAID HBA – "パススルー" モードでアダプターを構成する必要があります。
* RAID HBA – ディスクは単一のディスク、RAID-0 として構成する必要があります。

**サポート対象のバスとメディアの組み合わせ**

* SATA HDD
* SAS HDD
* RAID HDD
* RAID SSD (メディアの種類が指定されていない場合、または不明な場合\*)
* SATA SSD + SATA HDD
* SAS SSD + SAS HDD

\* パススルー機能のない RAID コントローラーは、メディアの種類を認識できません。 このようなコントローラーでは、SSD と HDD の両方が "未指定" としてマークされます。 その場合、SSD がキャッシュ デバイスではなく、永続的なストレージとして使用されます。 したがって、それらの SSD には開発キットをデプロイできます。

**サンプル HBA**: パススルー モードの LSI 9207-8i、LSI-9300-8i、または LSI-9265-8i

サンプル OEM 構成を使用できます。

## <a name="operating-system"></a>オペレーティング システム
|  | **要件** |
| --- | --- |
| **OS バージョン** |Windows Server 2012 R2 以降。 Azure Stack のインストールに含まれる VHD でホスト コンピューターを起動するので、デプロイが開始する前のオペレーティング システムのバージョンは重要ではありません。 OS とすべての必要な更新プログラムは、イメージに既に統合されています。 開発キットで使われている Windows Server インスタンスをアクティブ化するためにキーを使わないでください。 |

## <a name="deployment-requirements-check-tool"></a>デプロイ要件チェック ツール
オペレーティング システムをインストールした後は、[Deployment Checker for Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) を使って、ハードウェアがすべての要件を満たしていることを確認できます。

## <a name="account-requirements"></a>アカウントの要件
通常は、Microsoft Azure に接続できるようにインターネットに接続された状態で開発キットをデプロイします。 この場合、開発キットをデプロイするために Azure Active Directory (Azure AD) アカウントを構成する必要があります。

環境がインターネットに接続されていない場合、または Azure AD を使いたくない場合は、Active Directory フェデレーション サービス (AD FS) を使って Azure Stack をデプロイできます。 開発キットには、専用の AD FS および Active Directory Domain Services のインスタンスが含まれています。 このオプションを使ってデプロイする場合は、事前にアカウントを設定する必要はありません。

>[!NOTE]
AD FS のオプションを使ってデプロイする場合は、Azure Stack を再デプロイして Azure AD に切り替える必要があります。

### <a name="azure-active-directory-accounts"></a>Azure Active Directory アカウント
Azure AD アカウントを使って Azure Stack をデプロイするには、デプロイ用の PowerShell スクリプトを実行する前に、Azure AD アカウントを準備する必要があります。 このアカウントは、Azure AD テナントの全体管理者になります。 このアカウントは、Azure Active Directory および Graph API と対話するすべての Azure Stack サービス用のアプリケーションおよびサービス プリンシパルのプロビジョニングと委任に使われます。 また、既定のプロバイダー サブスクリプションの所有者としても使われます (これは後で変更できます)。 このアカウントを使って、Azure Stack システムの管理ポータルにログインできます。

1. 少なくとも 1 つの Azure AD のディレクトリ管理者である Azure AD アカウントを作成します。 既にある場合は、それを使うことができます。 ない場合は、[http://azure.microsoft.com/en-us/pricing/free-trial/](http://azure.microsoft.com/pricing/free-trial/) (中国の場合は <http://go.microsoft.com/fwlink/?LinkID=717821>) において無料で作成できます。 後で [Azure Stack を Azure に登録する](azure-stack-register.md)予定の場合は、この新規作成したアカウントでのサブスクリプションも必要です。
   
    「[Deploy the development kit](azure-stack-run-powershell-script.md#deploy-the-development-kit)」(開発キットをデプロイする) の手順 6 で使うので、資格情報を保存しておきます。 この*サービス管理者*アカウントは、リソース クラウド、ユーザー アカウント、テナント プラン、クォータ、価格を構成、管理することができます。 ポータルで、Web サイト クラウド、仮想マシン プライベート クラウドを作成したり、プランの作成やユーザーのサブスクリプションの管理を行えます。
2. テナントとして開発キットにサインインできるように、少なくとも 1 つのアカウントを[作成](azure-stack-add-new-user-aad.md)します。
   
   | **Azure Active Directory アカウント** | **サポートの有無** |
   | --- | --- |
   | 有効なパブリック Azure サブスクリプションのある職場または学校アカウント |あり |
   | 有効なパブリック Azure サブスクリプションの Microsoft アカウント |あり |
   | 有効な中国の Azure サブスクリプションのある職場または学校アカウント |あり |
   | 有効な米国政府の Azure サブスクリプションのある職場または学校アカウント |あり |

## <a name="network"></a>ネットワーク
### <a name="switch"></a>Switch
スイッチの 1 つのポートを開発キット マシン用に使用できること。  

開発キット マシンでは、スイッチのアクセス ポートまたはトランク ポートへの接続がサポートされています。 スイッチでは特別な機能は必要ありません。 トランク ポートを使用している場合、または VLAN ID を構成する必要がある場合は、デプロイ パラメーターとして VLAN ID を指定する必要があります。 [デプロイ パラメーターの一覧](azure-stack-run-powershell-script.md)で例を見ることができます。

### <a name="subnet"></a>サブネット
次のサブネットには、開発キット マシンを接続しないでください。

* 192.168.200.0/24
* 192.168.100.0/27
* 192.168.101.0/26
* 192.168.102.0/24
* 192.168.103.0/25
* 192.168.104.0/25

これらのサブネットは、開発キット環境内の内部ネットワーク専用です。

### <a name="ipv4ipv6"></a>IPv4/IPv6
IPv4 のみがサポートされています。 IPv6 ネットワークを作成することはできません。

### <a name="dhcp"></a>DHCP
NIC を接続するネットワークで使用できる DHCP サーバーがあることを確認します。 DHCP が使用できない場合は、ホストが使用するためのものに加え、追加の静的 IPv4 ネットワークを準備する必要があります。 デプロイ パラメーターとしてその IP アドレスとゲートウェイも指定する必要があります。 [デプロイ パラメーターの一覧](azure-stack-run-powershell-script.md)で例を見ることができます。

### <a name="internet-access"></a>インターネットへのアクセス
Azure Stack は、直接または透過プロキシ経由で、インターネットにアクセスする必要があります。 Azure Stack は、インターネット アクセスを有効にするための Web プロキシの構成をサポートしていません。 ホスト IP と (DHCP または静的 IP アドレス) によって MAS-BGPNAT01 に割り当てられた新しい IP の両方が、インターネットにアクセスできる必要があります。 graph.windows.net および login.microsoftonline.com ドメインのポート 80 と 443 を使用します。

## <a name="telemetry"></a>テレメトリ

テレメトリは、Azure Stack の将来のバージョンの構想に役立ちます。 フィードバックに迅速に対応し、新しい機能を提供し、品質を向上させることができます。 Microsoft Azure Stack には、Windows Server 2016 と SQL Server 2014 が含まれています。 これらの製品はどちらも既定の設定から変更されておらず、Microsoft Enterprise のプライバシーに関する声明で説明されていません。 また、Azure Stack には、Microsoft にテレメトリを送信するように変更されていないオープン ソース ソフトウェアが含まれます。 Azure Stack のテレメトリ データの例を次に示します。

- デプロイ登録情報
- アラートが開かれた日時と閉じられた日時
- ネットワーク リソースの数

テレメトリ データ フローをサポートするには、ネットワークでポート 443 (HTTPS) を開く必要があります。 クライアント エンドポイントは https://vortex-win.data.microsoft.com です。

Azure Stack のテレメトリを提供したくない場合は、以下で説明するように、開発キット ホストおよびインフラストラクチャの仮想マシンでオフにすることができます。

### <a name="turn-off-telemetry-on-the-development-kit-host-optional"></a>開発キット ホストでテレメトリをオフにする (省略可能)

>[!NOTE]
開発キット ホストでテレメトリをオフにする場合は、デプロイ スクリプトの実行前に行う必要があります。

[asdk-installer.ps1 スクリプトを実行]()して開発キット ホストをデプロイする前に、CloudBuilder.vhdx で起動し、管理者特権の PowerShell ウィンドウで次のスクリプトを実行します。
```powershell
### Get current AllowTelmetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host 
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0'  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

**AllowTelemetry** を 0 に設定すると、Windows と Azure Stack 両方のデプロイのテレメトリがオフになります。 オペレーティング システムからの重要なセキュリティ イベントのみが送信されます。 この設定は、すべてのホストとインフラストラクチャ VM の Windows テレメトリを制御し、スケールアウト操作が実行されると新しいノード/VM に再適用されます。


### <a name="turn-off-telemetry-on-the-infrastructure-virtual-machines-optional"></a>インフラストラクチャの仮想マシンでテレメトリをオフにする (省略可能)

デプロイが成功した後、開発キット ホスト上の管理者特権の PowerShell ウィンドウで (AzureStack\AzureStackAdmin ユーザーとして)、次のスクリプトを実行します。

```powershell
$AzSVMs= get-vm |  where {$_.Name -like "AzS-*"}
### Show current AllowTelemetry value for all AzS-VMs
invoke-command -computername $AzSVMs.name {(Get-ItemProperty -Path `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" -Name AllowTelemetry).AllowTelemetry}
### Set & Get updated AllowTelemetry value for all AzS-VMs
invoke-command -computername $AzSVMs.name {Set-ItemProperty -Path `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" -Name "AllowTelemetry" -Value '0'}
invoke-command -computername $AzSVMs.name {(Get-ItemProperty -Path `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" -Name AllowTelemetry).AllowTelemetry}
```

SQL Server のテレメトリの構成については、「[フィードバックをマイクロソフトに送信するように SQL Server 2016 を構成する方法](https://support.microsoft.com/en-us/help/3153756/how-to-configure-sql-server-2016-to-send-feedback-to-microsoft)」を参照してください。

### <a name="usage-reporting"></a>使用状況レポート

登録により、Azure Stack は Azure に使用状況情報を転送するようにも構成されます。 使用状況レポートはテレメトリとは別に制御されます。 [登録](azure-stack-register.md)時に Github のスクリプトを使って使用状況レポートを無効にできます。 **$reportUsage** パラメーターを **$false** に設定するだけです。

使用状況データは、「[Report Azure Stack usage data to Azure](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-usage-reporting)」(Azure に Azure Stack 使用状況データを報告する) で詳しく説明されているように書式設定されます。 Azure Stack 開発キットのユーザーに実際に料金がかかることはありません。 この機能は、使用状況レポートの動作をテストして理解できるように、開発キットに含まれています。 


## <a name="next-steps"></a>次のステップ
[Azure Stack 開発キット デプロイ パッケージをダウンロードする](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

[Azure Stack 開発キットをデプロイする](azure-stack-run-powershell-script.md)

