---
title: "Azure Automation DSC の概要 | Microsoft Docs"
description: "Azure Automation Desired State Configuration (DSC) の概要、その利用規約、および既知の問題"
services: automation
documentationcenter: dev-center-name
author: eslesar
manager: carmonm
keywords: "powershell dsc, 必要な状態の構成, powershell dsc azure"
ms.assetid: fd40cb68-c1a6-48c3-bba2-710b607d1555
ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 02/02/2017
ms.author: magoedte;eslesar
translationtype: Human Translation
ms.sourcegitcommit: f0d3a5c1929bb6dafef735ae3f4291e329861cc9
ms.openlocfilehash: b750878703baf143a2e8247bdd01f358e462e598
ms.lasthandoff: 02/03/2017

---
# <a name="azure-automation-dsc-overview"></a>Azure Automation DSC Overview

## <a name="what-is-azure-automation-dsc"></a>Azure Automation DSC とは

サーバーとアプリケーションのリソースをデプロイし、目的の状態を維持することは、単調でエラーが発生しやすい作業です。 Azure Automation Desired State Configuration (DSC) を使用すると、クラウドの規模で、すべての IT リソースの目的の状態を一貫した方法でデプロイし、確実に監視し、自動的に更新できます。 Automation DSC は PowerShell DSC に基づいて構築されているので、Windows または Linux を使用して、クラウド内またはオンプレミス内の物理および仮想マシン (VM) 全体で、マシン構成を特定の状態に合わせることができます。 一貫した制御で継続的な IT サービス配信を有効にし、異種ハイブリッド IT 環境全体の急な変更を管理する作業も簡単になります。

Azure Automation DSC は、構成管理をより容易にするために PowerShell DSC で導入された基礎をベースとします。 Azure Automation DSC は、今日 Azure Automation が PowerShell スクリプト用に提供するものと同じ管理レイヤーを [PowerShell Desired State Configuration](https://msdn.microsoft.com/powershell/dsc/overview) に提供します。

Azure Automation DSC を使用することで、[PowerShell Desired State Configuration の作成や管理](https://technet.microsoft.com/library/dn249918.aspx)、[DSC リソース](https://technet.microsoft.com/library/dn282125.aspx)のインポート、および DSC ノード構成 (MOF ドキュメント) の生成がすべてクラウドで可能になります。 これらの DSC 項目は、Azure Automation [DSC プル サーバー](https://technet.microsoft.com/library/dn249913.aspx)にあるため、ターゲット ノードが自動的に構成を受信して、目的の状態に適合し、その準拠の状態に関するレポートを返すことができます。 Azure Automation は、クラウドまたはオンプレミスの仮想マシンや物理マシンを対象とすることができます。 

読むより見る方がよければ、 Azure Automation DSC が初めて発表された 2015年 5 月に公開された次のビデオをご覧ください。 **注:** このビデオで解説されている概念とライフサイクルは正しいものですが、このビデオが作成されてから Azure Automation DSC はかなり進歩しています。 Azure Automation DSC は一般公開され、Azure ポータルでさまざまな UI を使用できるようになり、多数の機能がサポートされています。

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="azure-automation-dsc-terms"></a>Azure Automation DSC の利用規約

### <a name="configuration"></a>構成

PowerShell DSC では、構成という新しい概念が導入されました。 構成では、PowerShell 構文を使用して、環境の必要な状態を定義することができます。 DSC を使用して環境を構成するには、まず、構成キーワードを使用して Windows PowerShell スクリプト ブロックを定義し、識別子を付けてから、ブロックを区切る中かっこ ({}) を付加します。

![alt text](./media/automation-dsc-overview/AADSC_1.png)

構成ブロック内では、ノード構成を定義して、環境内で同じ構成にするべきノード (コンピューター) のセット向けに、必要な構成を指定できます。 このようにして、ノード構成では想定される&1; つ以上のノードの「ロール」を示します。 ノードの構成ブロックはノード キーワードで始まります。 このキーワードの後にロール名 (変数または式の場合あり) を付加します。 ロール名の後に中かっこ {} を使用して、ノードの構成ブロックを区切ります。

![alt text](./media/automation-dsc-overview/AADSC_2.png)

ノードの構成ブロック内には、特定の DSC リソースを構成するためのリソース ブロックを定義できます。 リソース ブロックはリソース名で始まり、その後にブロックに対して指定する必要がある識別子と、ブロックを区切るための中かっこ {} が続きます。

![alt text](./media/automation-dsc-overview/AADSC_3.png)

構成キーワードの詳細については、「[DSC 構成](https://msdn.microsoft.com/en-us/powershell/dsc/configurations "DSC 構成")」を参照してください。

DSC 構成を実行 (コンパイル) すると、1 つ以上の DSC ノード構成 (MOF ドキュメント) が生成されます。これらは、DSC ノードが必要な状態に準拠するために適用するものです。

Azure Automation DSC では、Azure Automation での DSC 構成のインポート、作成、およびコンパイルが可能です。これは、Azure Automation で Runbook をインポート、作成、および開始する方法に似ています。

> [!IMPORTANT]
> Azure Automation DSC では、構成にそれと同じ名前の構成ブロックを&1; つだけ含める必要があります。

### <a name="node-configuration"></a>ノード構成

DSC 構成がコンパイルされると、構成内のノード ブロックに応じて、1 つ以上のノード構成が生成されます。 ノード構成は、"MOF" や "構成ドキュメント" と同じです。 ノード構成は webserver や worker などの "ロール" のことで、1 つまたは複数のノードが目的の状態として想定したり、コンプライアンスの確認に使用したりします。 Azure Automation DSC でのノード構成の名前は、"Configuration Name.NodeConfigurationBlockName" という形式です。

PS DSC ノードは、DSC プッシュまたはプル メソッドのいずれかを使用して制定する必要があるノード構成を認識するようになります。 Azure Automation DSC は、Azure Automation DSC プル サーバーから適用する必要があるノード構成をノードが要求する DSC プル方式に依存します。 ノードは Azure Automation DSC に対して要求を行うため、ファイアウォールの内側に配置したり、受信ポートをすべて閉じたりすることができます。インターネットへの (直接またはプロキシ経由の) 送信アクセスのみが必要です。

### <a name="node"></a>ノード

DSC ノードは、その構成が DSC によって管理される任意のコンピューターです。 たとえば、Windows または Linux Azure VM、オンプレミス VM または物理ホスト、他のパブリック クラウド上のコンピューターなどです。 ノードにノード構成が適用されると、ノードは定義された目的の状態に準拠し、その状態を維持します。 ノードはレポート サーバーに、ノード構成と準拠の状態に関するレポートを返します。

Azure Automation DSC によって、ノードのオンボードが簡単かつ柔軟になります。 Azure Automation DSC で、各ノードに割り当てられているノード構成を変更できます。 ノード構成に変更が発生すると、ノードは自らの構成を変更して自動的に別のロールを想定し、新しいロールに対するレポートを開始します。 

### <a name="resource"></a>リソース

DSC リソースは、Windows PowerShell Desired State Configuration (DSC) 構成を定義するために使用できる構成要素です。 DSC には、ファイルやフォルダー、サーバー機能やロール、レジストリ設定、環境変数、およびサービスやプロセスなど、一連の組み込みリソースが用意されています。 組み込みの DSC リソースの完全な一覧と使用方法については、「 [組み込みの Windows PowerShell Desired State Configuration リソース](https://msdn.microsoft.com/powershell/dsc/builtinresource)」を参照してください。

DSC リソースは、一連の組み込み DSC リソースを拡張するために PowerShell モジュールの一部としてインポートすることもできます。 ノードに適用される必要があるノード構成に既定以外のリソースへの参照が含まれている場合、DSC プル サーバーから DSC ノードによってそのリソースがプルダウンされます。 カスタム リソースの作成方法については、「 [カスタムの Windows PowerShell Desired State Configuration リソースの作成](https://msdn.microsoft.com/powershell/dsc/authoringresource)」を参照してください。

Azure Automation DSC には、PS DSC と同じ組み込み DSC リソースがすべて付属しています。 Azure Automation にリソースを含む PowerShell モジュールをインポートすることで、Azure Automation DSC にリソースをさらに追加することができます。

### <a name="compilation-job"></a>コンパイル ジョブ

Azure Automation DSC のコンパイル ジョブは、1 つ以上のノード構成を作成するための構成のコンパイルのインスタンスです。 Azure Automation Runbook ジョブに似ていますが、ノード構成を作成する場合を除き、タスクを実際に実行しないという点が異なります。 コンパイル ジョブによって作成されたノード構成は、Azure Automation DSC プル サーバーに自動的に配置され、以前のバージョンのノード構成は、この構成のために存在している場合、上書きされます。 コンパイル ジョブによって生成されたノード構成の名前には、"ConfigurationName.NodeConfigurationBlockName" という形式を使用します。 たとえば、以下の構成をコンパイルすると、"MyConfiguration.webserver" という単一のノード構成が生成されます。

![alt text](./media/automation-dsc-overview/AADSC_5.png)

> [!NOTE]
> Runbook と同じように、構成を発行することができます。 これは、Azure Automation DSC プル サーバーへの DSC 項目の配置とは関係ありません。 コンパイル ジョブにより、DSC 項目は Azure Automation DSC プル サーバーに配置されます。 Azure Automation での「発行」の詳細については、「 [Runbook の公開](https://msdn.microsoft.com/library/dn903765.aspx)」をご覧ください。


## <a name="azure-automation-dsc-lifecycle"></a>Azure Automation DSC のライフサイクル

空のオートメーション アカウントから適切に構成されたノードの管理対象セットを作成するには、構成を定義し、定義した構成をノード構成に変更して、ノードを Azure Automation DSC とこれらのノード構成にオンボードする一連のプロセスを行います。 次の図に、Azure Automation DSC のライフサイクルを示します。

![alt text](./media/automation-dsc-overview/DSCLifecycle.png)

次の図は、DSC のライフサイクルのプロセスをステップごとに詳細に示しています。 この図には、Azure Automation に構成をインポートし、ノードに適用するさまざまな方法、オンプレミス コンピューターが DSC をサポートするために必要なコンポーネント、さまざまなコンポーネント間のやり取りが含まれています。

![DSC アーカイブ](./media/automation-dsc-overview/dsc-architecture.png)

## <a name="gotchas--known-issues"></a>認識されている既知の問題

* Windows 用 PowerShell DSC エージェントを Azure Automation と通信できるようにするには、最新バージョンの WMF 5 をインストールする必要があります。 Linux を Azure Automation と通信できるようにするには、最新バージョンの Linux 用 PowerShell DSC エージェントをインストールする必要があります。
* WMF 5 にアップグレードする前にコンピューターが Azure Automation DSC のノードとして既に登録されている場合は、Azure Automation DSC からノードの登録を解除し、WMF 5 のインストール後に再登録してください。 再登録する前に、$env:windir\system32\configuration\DSCEngineCache.mof ファイルを削除します。
* WMF 5 RTM が WMF 5 Production Preview の上にインストールされている場合は、DSC が機能しないことがあります。 この問題を解決するには、管理者特権の PowerShell セッションで次のコマンドを実行します (管理者として実行)。`mofcomp $env:windir\system32\wbem\DscCoreConfProv.mof`
* 現時点では、Azure Automation DSC では部分的または複合 DSC 構成はサポートされていません。 ただし、ローカル PowerShell と同様に、DSC の複合リソースをインポートして Azure Automation DSC 構成で使用することで、構成を再利用できます。
* 従来の PowerShell DSC プル サーバーでは、**ModuleName_Version.zip"** という形式のモジュール zip をプル サーバーに配置する必要があります。 Azure Automation では、 **ModuleName.zip**という形式の名前の PowerShell モジュールをインポートする必要があります。 Azure Automation にモジュールをインポートするために必要な統合モジュールの詳細については、「 [このブログの投稿](https://azure.microsoft.com/blog/2014/12/15/authoring-integration-modules-for-azure-automation/) 」を参照してください。
* Azure Automation にインポートした PowerShell モジュールには、.doc ファイルや .docx ファイルを含めることはできません。 DSC リソースを含むいくつかの PowerShell モジュールには、参照用にこれらのファイルが含まれます。 Azure Automation にインポートする前に、モジュールからこれらのファイルを削除してください。
* ノードが初めて Azure Automation アカウントに登録されたとき、またはノードがサーバー側の別のノード構成にマップされるように変更されたときには、ノードの状態は "準拠" になります。 これは、ノードの状態が、現在マップされているノード構成に実際には準拠していない場合であっても当てはまります。 ノードが登録後またはノード構成のマッピング変更後に最初のレポートを送信した後であれば、ノードの状態を信頼できます。
* いずれかの直接的なオンボーディング方法を使用して、Azure Automation DSC による管理のために Azure Windows VM の利用を開始する場合、Azure Automation に DSC ノードとして VM が表示されるまで最長&1; 時間かかる可能性があります。 これは、VM を Azure Automation DSC にオンボードするために必要な Windows Management Framework 5.0 を、Azure VM DSC 拡張機能が VM にインストールするためです。
* 登録すると、各ノードは、1 年後に有効期限が切れる認証用の一意の証明書を自動的にネゴシエートします。 現時点では、PowerShell DSC 登録プロトコルは、有効期限が近づいたときに証明書を自動的に更新することはできないため、1 年後にノードを再登録する必要があります。 再登録する前に、各ノードで Windows Management Framework 5.0 RTM が実行されていることを確認します。 ノードの認証証明書の有効期限が切れるときにノードが再登録されない場合、ノードは Azure Automation と通信できなくなり、[反応なし] とマークされます。 再登録は、ノードを初めて登録したのと同じ方法で実行されます。 証明書の有効期限が切れる 90 日以内または証明書の有効期限が切れた後で再登録を実行すると、新しい証明書が生成されて使用されます。


## <a name="related-articles"></a>関連記事

* [Azure Automation DSC による管理のためのマシンのオンボード](automation-dsc-onboarding.md)
* [Azure Automation DSC での構成のコンパイル](automation-dsc-compile.md)
* [Azure Automation DSC cmdlets (Azure Automation DSC コマンドレット)](https://msdn.microsoft.com/library/mt244122.aspx)
* [Azure Automation DSC cmdlets (Azure Automation DSC の価格)](https://azure.microsoft.com/pricing/details/automation/)
* [Azure Automation DSC と Chocolatey を使用した IaaS VM への継続的なデプロイ](automation-dsc-cd-chocolatey.md)


