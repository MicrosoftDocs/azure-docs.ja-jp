---
title: Azure Automation State Configuration の概要
description: Azure Automation State Configuration (DSC) の概要、その利用規約、および既知の問題
keywords: powershell dsc, 必要な状態の構成, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a5d4657f87b0a6cbae0699c5a2f95773ff55f633
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798446"
---
# <a name="azure-automation-state-configuration-overview"></a>Azure Automation State Configuration の概要

Azure Automation State Configuration は、PowerShell の Desired State Configuration (DSC) [構成](/powershell/dsc/configurations)の書き込み、管理、コンパイル、[DSC リソース](/powershell/dsc/resources)のインポート、および構成のターゲット ノードへの割り当てを、すべてクラウドで実行できる Azure サービスです。

## <a name="why-use-azure-automation-state-configuration"></a>Azure Automation State Configuration を使用する理由

Azure Automation State Configuration には、Azure の外部で DSC を使用する際にいくつかの利点があります。

### <a name="built-in-pull-server"></a>組み込みのプル サーバー

Azure Automation State Configuration には、ターゲット ノードが自動的に構成を受信して、目的の状態に適合し、その準拠の状態に関するレポートを返すことができる [Windows Feature DSC-Service](/powershell/dsc/pullserver) と似た DSC プル サーバーが用意されています。 Azure Automation の組み込みのプル サーバーでは、独自のプル サーバーを設定して管理する必要はありません。 Azure Automation は、クラウドまたはオンプレミスの仮想または物理的な Windows コンピューターや Linux コンピューターを対象とすることができます。

### <a name="management-of-all-your-dsc-artifacts"></a>すべての DSC アーティファクトの管理

Azure Automation State Configuration は、Azure Automation が PowerShell スクリプト用に提供するものと同じ管理レイヤーを [PowerShell Desired State Configuration](/powershell/dsc/overview) に提供します。

Azure Portal から、または PowerShell から、すべての DSC 構成、リソース、およびターゲット ノードを管理できます。

![Azure Automation ページのスクリーンショット](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Azure Monitor ログへのレポート データのインポート

Azure Automation State Configuration で管理されているノードは、組み込みのプル サーバーに詳細なレポート ステータス データを送信します。 Azure Automation State Configuration を構成して、このデータを Log Analytics ワークスペースに送信できます。 Log Analytics ワークスペースに State Configuration 状態データを送信する方法については、「[Azure Monitor ログへの Azure Automation State Configuration レポート データの転送](automation-dsc-diagnostics.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure Automation State Configuration (DSC) を使用する場合は、次の要件を検討してください。

### <a name="operating-system-requirements"></a>オペレーティング システムの要件

Windows を実行しているノードの場合は、次のバージョンがサポートされます。

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

Linux を実行しているノードの場合は、次のディストリビューション/バージョンがサポートされます。

DSC Linux 拡張機能では、以下を除き、[Azure で動作保証済みの](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) Linux ディストリビューションがすべてサポートされます。

ディストリビューション | バージョン
-|-
Debian  | すべてのバージョン
Ubuntu  | 18.04

### <a name="dsc-requirements"></a>DSC 要件

Azure で実行されているすべての Windows ノードに対して、[WMF 5.1](https://docs.microsoft.com/powershell/wmf/setup/install-configure) がオンボード中にインストールされます。  Windows Server 2012 および Windows 7 を実行しているノードでは、[WinRM が有効になります](https://docs.microsoft.com/powershell/dsc/troubleshooting/troubleshooting#winrm-dependency)。

Azure で実行しているすべての Linux ノードで、オンボード中に [Linux 用の PowerShell DSC](https://github.com/Microsoft/PowerShell-DSC-for-Linux) がインストールされます。

### <a name="network-planning"></a>プライベート ネットワークを構成する

ノードが非公開ネットワーク内にある場合、State Configuration (DSC) が Automation と通信するには、次のポートと URL が必要です。

* ポート: 送信インターネット アクセスには TCP 443 のみが必要です。
* グローバル URL: *.azure-automation.net
* 米国バージニア州 (政府機関向け) のグローバル URL: *.azure automation.us
* エージェント サービス: https://\<workspaceId\>.agentsvc.azure-automation.net

これにより、マネージド ノードが Azure Automation と通信するためのネットワーク接続が提供されます。
ノード間で通信する DSC リソース ([WaitFor * リソース](https://docs.microsoft.com/powershell/dsc/reference/resources/windows/waitForAllResource)など) を使用している場合、ノード間のトラフィックを許可する必要もあります。
これらのネットワーク要件を理解するには、各 DSC リソースのドキュメントを参照してください。

#### <a name="proxy-support"></a>プロキシのサポート

DSC エージェントのプロキシは、Windows バージョン 1809 以降でサポートされます。
このオプションを構成するには、ノードの登録に使用される [metaconfiguration スクリプト](automation-dsc-onboarding.md#generating-dsc-metaconfigurations)の **ProxyURL** と **ProxyCredential** の値を設定します。
以前のバージョンの Windows では、DSC のプロキシは利用できません。

Linux ノードでは、DSC エージェントがプロキシをサポートしており、http_proxy 変数を利用して URL が特定されます。

#### <a name="azure-state-configuration-network-ranges-and-namespace"></a>Azure State Configuration のネットワークの範囲と名前空間

例外を定義するときは、一覧に示されているアドレスを使用することをお勧めします。 [Microsoft Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)の IP アドレスをダウンロードできます。 このファイルは毎週更新され、現在デプロイされている範囲と今後変更される IP 範囲が反映されます。

特定のリージョンに対して定義された Automation アカウントがある場合は、そのリージョン データセンターへの通信を制限できます。 次の表は、リージョンごとの DNS レコードを示しています。

| **[リージョン]** | **DNS レコード** |
| --- | --- |
| 米国中西部 | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| 米国中南部 |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| 米国東部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| カナダ中部 |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| 西ヨーロッパ |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| 北ヨーロッパ |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| 東南アジア |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| インド中部 |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| 東日本 |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| オーストラリア東南部 |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| 英国南部 | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| 米国政府バージニア州 | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

リージョン名の代わりにリージョン IP アドレスの一覧を入手するには、Microsoft ダウンロード センターから [Azure データセンター IP アドレス](https://www.microsoft.com/download/details.aspx?id=41653) XML ファイルをダウンロードします。

> [!NOTE]
> Azure データセンター IP アドレス XML ファイルには、Microsoft Azure データセンターで使用されている IP アドレス範囲が一覧表示されています。 このファイルには、計算、SQL、およびストレージの範囲が含まれています。
>
>更新されたファイルが毎週投稿されます。 このファイルには、現在デプロイされている範囲と、次に予定されている IP 範囲の変更が反映されています。 このファイルに現れる新しい範囲は、少なくとも 1 週間はデータセンターで使用されません。
>
> 新しい XML ファイルを毎週ダウンロードすることをお勧めします。 その後、Azure で実行されているサービスを正しく識別するようにサイトを更新します。 Azure ExpressRoute ユーザーは、このファイルが、毎月第 1 週に Azure 領域の Border Gateway Protocol (BGP) アドバタイズを更新するために使用されることに注意してください。

## <a name="introduction-video"></a>紹介ビデオ

読むより見る方がよければ、 Azure Automation State Configuration が初めて発表された 2015 年 5 月に公開された次のビデオをご覧ください。

> [!NOTE]
> このビデオで解説されている概念とライフ サイクルは正しいものですが、このビデオが作成されてから Azure Automation State Configuration はかなり進歩しています。 Azure Automation DSC は一般公開され、Azure ポータルでさまざまな UI を使用できるようになり、多数の機能がサポートされています。

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>次の手順

- 使用を開始するには、「[Azure Automation State Configuration の使用](automation-dsc-getting-started.md)」をご覧ください。
- ノードをオンボードにする方法は、「[Azure Automation State Configuration による管理のためのマシンのオンボード](automation-dsc-onboarding.md)」をご覧ください。
- DSC 構成をコンパイルしてターゲット ノードに割り当てることができるようにする方法の詳細については、「[Azure Automation State Configuration での構成のコンパイル](automation-dsc-compile.md)」をご覧ください。
- PowerShell コマンドレットのリファレンスについては、[Azure Automation State Configuration のコマンドレット](/powershell/module/azurerm.automation/#automation)に関するページをご覧ください。
- 料金情報については、[Azure Automation State Configuration の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。
- 継続的なデプロイ パイプラインで Azure Automation State Configuration を使う例については、「[Automation State Configuration と Chocolatey を使用した仮想マシンへの継続的なデプロイ](automation-dsc-cd-chocolatey.md)」をご覧ください。
