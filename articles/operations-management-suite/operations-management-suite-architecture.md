---
title: "Operations Management Suite (OMS) アーキテクチャ | Microsoft Docs"
description: "Microsoft Operations Management Suite (OMS) は、Microsoft のクラウドベースの IT 管理ソリューションです。OMS を使用して、オンプレミスとクラウドのインフラストラクチャを管理し、保護することができます。  この記事では、OMS に含まれるさまざまなサービスについて説明し、詳細なコンテンツへのリンクを提供します。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 40e41686-7e35-4d85-bbe8-edbcb295a534
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: df1a5b2c200b7fa69961247ac036ee35fd14f54d


---
# <a name="oms-architecture"></a>OMS アーキテクチャ
[Operations Management Suite (OMS)](https://azure.microsoft.com/documentation/services/operations-management-suite/) は、オンプレミス環境とクラウド環境を管理するためのクラウドベース サービスの集合体です。  この記事では、OMS のさまざまなオンプレミスおよびクラウドのコンポーネントと、クラウド コンピューティング アーキテクチャの概要について説明します。  詳細については、各サービスのドキュメントをご覧ください。

## <a name="log-analytics"></a>Log Analytics
[Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) によって収集されるデータはすべて、Azure にホストされている OMS リポジトリに格納されます。  OMS リポジトリに収集されるデータは、接続されたソースで生成されます。  現在、接続されたソースは 3 つの種類がサポートされています。

* OMS に直接接続された [Windows](../log-analytics/log-analytics-windows-agents.md) コンピューターまたは [Linux](../log-analytics/log-analytics-linux-agents.md) コンピューターにインストールされているエージェント。
* [Log Analytics に接続された](../log-analytics/log-analytics-om-agents.md) System Center Operations Manager (SCOM) 管理グループ。  SCOM エージェントが管理サーバーと継続的に通信し、管理サーバーはイベントとパフォーマンス データを Log Analytics に転送します。
* Azure の worker ロール、Web ロール、または仮想マシンから [Azure 診断データ](../cloud-services/cloud-services-dotnet-diagnostics.md)を収集する [Azure ストレージ アカウント](../log-analytics/log-analytics-azure-storage.md)。

イベント ログやパフォーマンス カウンターなど、接続されたソースから Log Analytics が収集するデータは、データ ソースによって定義されます。  ソリューションを使用すると、OMS に機能を追加できます。ソリューションは [OMS ソリューション ギャラリー](../log-analytics/log-analytics-add-solutions.md)からワークスペースに簡単に追加できます。  ソリューションの中には、SCOM エージェントから Log Analytics への直接接続が必要なものや、追加エージェントのインストールが必要なものがあります。

Log Analytics には Web ベースのポータルが用意されており、OMS リソースの管理、OMS ソリューションの追加と構成、OMS リポジトリ内のデータの表示と分析を実行できます。

![Log Analytics high level architecture](media/operations-management-suite-architecture/log-analytics.png)

## <a name="azure-automation"></a>Azure Automation
[Azure Automation Runbook](http://azure.microsoft.com/documentation/services/automation) は、Azure クラウド上で実行される自動化タスクで、Azure や他のクラウド サービス上にあるリソースにアクセスできます。また、パブリック インターネットからアクセスできます。  [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md) を使用してローカル データセンターのオンプレミス マシンを指定し、Runbook がローカル リソースにアクセスできるようにすることもできます。

[DSC 構成](../automation/automation-dsc-overview.md) は、Azure Virtual Machines に直接適用できます。  それ以外の物理マシンと仮想マシンは、Azure Automation DSC プル サーバーに構成を要求できます。

Azure Automation には、なんらかの操作用に Azure ポータルを起動するためのリンクや統計情報を表示する OMS ソリューションが用意されています。

![Azure Automation high level architecture](media/operations-management-suite-architecture/automation.png)

## <a name="azure-backup"></a>Azure Backup
[Azure Backup](http://azure.microsoft.com/documentation/services/backup) で保護されるデータは、特定の地域リージョンにあるバックアップ コンテナーに格納されます。  データは同じリージョン内でレプリケートされます。コンテナーの種類によっては、冗長性を高めるために別のリージョンにレプリケートされることもあります。

Azure Backup には、3 つの基本的なシナリオがあります。

* Azure Backup エージェントがインストールされた Windows マシン。  この場合、Windows サーバーまたはクライアントから直接 Azure Backup コンテナーにファイルやフォルダーをバックアップできます。  
* System Center Data Protection Manager (DPM) または Microsoft Azure Backup Server。 この場合、DPM または Microsoft Azure Backup Server を利用して、SQL や SharePoint などのアプリケーション ワークロードに加えてファイルとフォルダーも、ローカル ストレージにバックアップした後、Azure バックアップ コンテナーにレプリケートできます。
* Azure 仮想マシン拡張機能。  この場合、Azure 仮想マシンを Azure Backup コンテナーにバックアップできます。

Azure Backup には、なんらかの操作用に Azure ポータルを起動するためのリンクや統計情報を表示する OMS ソリューションが用意されています。

![Azure Backup high level architecture](media/operations-management-suite-architecture/backup.png)

## <a name="azure-site-recovery"></a>Azure Site Recovery
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) は、仮想マシンと物理サーバーのレプリケーション、フェールオーバー、およびフェールバックを調整します。 レプリケーション データは、プライマリ データセンターとセカンダリ データセンターの Hyper-V ホスト間、VMware ハイパーバイザー間、物理サーバー間、またはデータセンターと Azure ストレージの間でやり取りされます。  Site Recovery では、特定の Azure リージョンにあるコンテナーにメタデータが格納されます。 レプリケートされたデータは、格納されません。

Azure Site Recovery には、3 つの基本的なレプリケーション シナリオがあります。

**Hyper-V 仮想マシンのレプリケーション**

* Hyper-V 仮想マシンが VMM クラウドで管理されている場合、セカンダリ データセンターまたは Azure Storage にレプリケートできます。  Azure へのレプリケーションは、セキュリティで保護されたインターネット接続を経由して行われます。  セカンダリ データセンターへのレプリケーションは、LAN 経由で行われます。
* Hyper-V 仮想マシンが VMM で管理されていない場合は、Azure Storage のみにレプリケートできます。  Azure へのレプリケーションは、セキュリティで保護されたインターネット接続を経由して行われます。

**VMWare 仮想マシンのレプリケーション**

* VMware を実行するセカンダリ データセンターまたは Azure Storage に VMware 仮想マシンをレプリケートできます。  Azure へのレプリケーションは、サイト間 VPN、Azure ExpressRoute、またはセキュリティで保護されたインターネット接続を経由して行われます。 セカンダリ データセンターへのレプリケーションは、InMage Scout データ チャネルを経由して行われます。

**Windows および Linux の物理サーバーのレプリケーション** 

* セカンダリ データセンターまたは Azure Storage に物理サーバーをレプリケートできます。 Azure へのレプリケーションは、サイト間 VPN、Azure ExpressRoute、またはセキュリティで保護されたインターネット接続を経由して行われます。 セカンダリ データセンターへのレプリケーションは、InMage Scout データ チャネルを経由して行われます。  Azure Site Recovery には、複数の統計情報を表示する OMS ソリューションが用意されていますが、なんらかの操作を行うには Azure ポータルを使用する必要があります。

![Azure Site Recovery high level architecture](media/operations-management-suite-architecture/site-recovery.png)

## <a name="next-steps"></a>次のステップ
* [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics)の詳細を確認する。
* [Azure Automation](https://azure.microsoft.com/documentation/services/automation)の詳細を確認する。
* [Azure Backup](http://azure.microsoft.com/documentation/services/backup)の詳細を確認する。
* [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery)の詳細を確認する。




<!--HONumber=Dec16_HO2-->


