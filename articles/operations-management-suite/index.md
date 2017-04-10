---
title: "Microsoft Operations Management Suite (OMS) のドキュメント - チュートリアル | Microsoft Docs"
description: "Microsoft Operations Management Suite (OMS) は、Microsoft のクラウドベースの IT 管理ソリューションです。OMS を使用して、オンプレミスとクラウドのインフラストラクチャを管理し、保護することができます。 この記事では、OMS に含まれるさまざまなサービスについて説明し、詳細なコンテンツへのリンクを提供します。"
services: operations-management-suite
author: carolz
manager: carolz
layout: LandingPage
ms.assetid: 
ms.service: operations-management-suite
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing-page
ms.date: 01/23/2017
ms.author: carolz
translationtype: Human Translation
ms.sourcegitcommit: ccfbd45def1ecf036a2464f560aa89817c37b419
ms.openlocfilehash: 54b3ce2c8c3966ee36350fed71bdc48aa5bffa63

---
# <a name="what-is-operations-management-suite-oms"></a>Operations Management Suite (OMS) とは
Microsoft Operations Management Suite (OMS) は、Microsoft のクラウドベースの IT 管理ソリューションです。OMS を使用して、オンプレミスとクラウドのインフラストラクチャを管理し、保護することができます。  OMS はクラウドベースのサービスとして実装されるため、インフラストラクチャ サービスに最小限の投資をするだけで、すぐに稼働させることができます。  新しい機能は自動的に配信されるため、継続的なメンテナンスやアップグレードのコストが節約されます。

OMS は、価値のある独自のサービスに加え、System Center Operations Manager などの System Center のコンポーネントと統合して、管理のための既存の投資をクラウドに拡張できます。  System Center と OMS を連携させることで、本格的なハイブリッド管理を実現できます。

次のセクションでは、OMS が価値をもたらすさまざまな領域とその価値を実装するサービスについての概要を説明します。  それぞれの詳細な説明を確認する前に、OMS アーキテクチャを確認し、さまざまな OMS コンポーネントの概要を把握することができます。

## <a name="insight-and-analyticsmediaoperations-management-suite-overviewicon-insight-analyticspng-insight-and-analytics"></a>![洞察と分析](media/operations-management-suite-overview/icon-insight-analytics.png) 洞察と分析
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) は、オペレーティング システムやアプリケーションによって生成されるログやパフォーマンス データを収集、関連付け、検索、および処理するうえで役立ちます。 統合された検索とカスタム ダッシュボードを使用してオペレーション インサイトがリアルタイムで得られるため、物理的な場所に関係なく、ワークロードやサーバー全体に散在する何百万件のレコードもすぐに分析できます。

収集されるデータとその分析のロジックを定義するソリューションを Log Analytics に簡単に追加できます。  ソリューションには、最小の構成または構成なしでエージェントに自動的に配信される追加機能が含まれているものもあります。  個々のソリューションによって提供される分析ツールを使用できるだけでなく、データセット全体を対象としてカスタム検索を実行し、システムとアプリケーションの間でデータを関連付けることができます。  

## <a name="automation--controlmediaoperations-management-suite-overviewicon-automation-controlpng-automation--control"></a>![オートメーションと制御](media/operations-management-suite-overview/icon-automation-control.png) オートメーションと制御
Azure Automation では、PowerShell をベースとして Azure クラウド上で実行される [Runbook](../automation/automation-runbook-types.md) を使用して、管理プロセスを自動化します。  Runbook は、PowerShell で管理できる製品またはサービスにアクセスできます。これには、Amazon Web Services (AWS) など他のクラウドのリソースも含まれます。  ローカル データセンターのサーバーで Runbook を実行してローカル リソースを管理することもできます。

Azure Automation では、[PowerShell DSC](../automation/automation-dsc-overview.md) を使用して構成を管理できます。  Azure でホストされる DSC リソースを作成して管理し、クラウドやオンプレミス システムに適用して、構成を定義したり自動的に強制適用したりできます。

## <a name="protection-and-recoverymediaoperations-management-suite-overviewicon-protection-recoverypng-protection-and-disaster-recovery"></a>![保護と回復](media/operations-management-suite-overview/icon-protection-recovery.png) 保護と障害復旧
[Azure Backup](http://azure.microsoft.com/documentation/services/backup) は、アプリケーション データを保護し、長年にわたってデータを保管できます。設備投資は不要で、運用コストも最小限で済みます。  SQL Server や SharePoint などのアプリケーション ワークロードだけでなく、Windows の物理サーバーや仮想サーバーのデータもバックアップできます。  また、System Center Data Protection Manager (DPM) では、Azure Backup を使用して、保護データを Azure にレプリケートし、冗長性と長期保存を実現しています。

[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) は、オンプレミスの Hyper-V 仮想マシン、VMware 仮想マシン、および物理 Windows/Linux サーバーのレプリケーション、フェールオーバー、および回復を調整して、ビジネスの継続性と障害復旧 (BCDR) の戦略に貢献します。 マシンをセカンダリ データセンターにレプリケートしたり、Azure にレプリケートしてデータセンターを拡張したりすることができます。 Site Recovery で、ワークロードの単純なフェールオーバーと回復も実行できます。 SQL Server AlwaysOn などの障害復旧メカニズムと統合して、複数のマシンにわたって階層化されたワークロードの簡単なフェールオーバーを行うための復旧計画を提供します。

## <a name="oms-security-and-compliancemediaoperations-management-suite-overviewicon-security-compliancepng-security-and-compliance"></a>![OMS Security and Compliance](media/operations-management-suite-overview/icon-security-compliance.png) セキュリティとコンプライアンス
セキュリティとコンプライアンスのソリューションを使用して、インフラストラクチャに対するセキュリティ上のリスクを識別、評価、軽減することができます。  OMS のこれらの機能は、エージェント システムのログ データと構成を分析する Log Analytics 内の複数の OMS ソリューションによって実装されており、環境の継続的なセキュリティを確保するうえで役立ちます。

* [セキュリティと監査のソリューション](oms-security-getting-started.md)は、疑わしいアクティビティを特定するために、管理対象システム上のセキュリティ イベントを収集して分析します。
* [マルウェア対策ソリューション](../log-analytics/log-analytics-malware.md)は、管理対象システム上のマルウェア保護の状態を報告します。  
* システムの更新ソリューションは、管理対象システム上のセキュリティ更新プログラムや他の更新プログラムを分析するため、修正プログラムが必要なシステムを簡単に特定できます。

## <a name="next-steps"></a>次のステップ
* [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics)の詳細を確認する。
* [Azure Automation](../automation/automation-intro.md)の詳細を確認する。
* [Azure Backup](http://azure.microsoft.com/documentation/services/backup)の詳細を確認する。
* [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery)の詳細を確認する。




<!--HONumber=Feb17_HO2-->


