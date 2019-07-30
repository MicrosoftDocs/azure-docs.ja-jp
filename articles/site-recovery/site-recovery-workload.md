---
title: Azure Site Recovery で保護できるワークロード | Microsoft Docs
description: Azure Site Recovery サービスによるディザスター リカバリーを使用して保護できるワークロードについて説明します。
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: raynew
ms.openlocfilehash: 6a0e07f338da039be1660e59510b24bcd44eed0c
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491753"
---
# <a name="what-workloads-can-you-protect-with-azure-site-recovery"></a>Azure Site Recovery で保護できるワークロード

この記事では、[Azure Site Recovery](site-recovery-overview.md) サービスによるディザスター リカバリーを使用して保護できるワークロードとアプリケーションについて説明します。



## <a name="overview"></a>概要

組織には、計画ダウンタイムと計画外ダウンタイムの間にワークロードとデータの安全性と可用性を維持し、できるだけ早く通常の動作状態に復旧させる、ビジネス継続性とディザスター リカバリー (BCDR) の戦略が必要です。

Site Recovery は、BCDR 戦略に貢献する Azure サービスです。 Site Recovery を使用することで、アプリケーションに対応したレプリケーションをクラウドまたはセカンダリ サイトにデプロイできます。 アプリが Windows ベースと Linux ベースのどちらであっても、また、物理サーバー、VMware、Hyper-V のいずれで実行されていても、Site Recovery を使用して、レプリケートの調整、ディザスター リカバリー テスト、フェールオーバーとフェールバックを実行できます。

Site Recovery は、SharePoint、Exchange、Dynamics、SQL Server、Active Directory などの Microsoft アプリケーションと統合されています。 Microsoft はまた、Oracle、SAP、Red Hat などの主要なベンダーと密接に連携しています。 レプリケーション ソリューションをアプリケーションごとにカスタマイズすることができます。

## <a name="why-use-site-recovery-for-application-replication"></a>アプリケーションのレプリケーションに Site Recovery を使用する理由

Site Recovery は、アプリケーション レベルの保護と復旧に次のように役立ちます。

* アプリに依存しないため、サポートされているマシンで実行されるすべてのワークロードに対してレプリケーションを行えます。
* RPO を使用した近同期レプリケーションにかかる時間はわずか 30 秒間であるため、重要度の高いビジネス アプリケーションのニーズを満たします。
* アプリケーションとの整合性が取られたスナップショットであるため、単一層または複数層のアプリケーションに適しています。
* SQL Server AlwaysOn と統合できるほか、その他のアプリケーション レベルのレプリケーション テクノロジ (AD のレプリケーション、SQL AlwaysOn、Exchange データベース可用性グループ (DAG) など) と組み合わせて使用できます。
* 柔軟な復旧計画を策定し、1 回のクリックでアプリケーション スタック全体を復旧できます。この計画に、外部スクリプトや手動のアクションを含めることもできます。
* Site Recovery と Azure の高度なネットワーク管理により、IP アドレスの予約、負荷分散の構成、低 RTO ネットワーク切り替え用の Azure Traffic Manager との統合など、アプリのネットワーク要件が簡略化されます。
* 豊富な自動化ライブラリに、運用環境ですぐに使えるアプリケーション固有のスクリプトが用意されています。これらのスクリプトをダウンロードして、復旧計画に統合することができます。

## <a name="workload-summary"></a>ワークロードの概要
Site Recovery は、サポート対象のマシンで実行されているすべてのアプリをレプリケートできます。 また、Microsoft では、製品チームと連携して、アプリに特化したテストを追加で実施しました。

| **ワークロード** |**Azure VM を Azure にレプリケートする** |**Hyper-V VM をセカンダリ サイトにレプリケート** | **Hyper-V VM を Azure にレプリケート** | **VMware VM をセカンダリ サイトにレプリケート** | **VMware VM を Azure にレプリケート** |
| --- | --- | --- | --- | --- |---|
| Active Directory、DNS |Y |Y |Y |Y |Y|
| Web アプリケーション (IIS、SQL) |Y |Y |Y |Y |Y|
| System Center Operations Manager |Y |Y |Y |Y |Y|
| SharePoint |Y |Y |Y |Y |Y|
| SAP<br/><br/>SAP サイトを Azure にレプリケート (非クラスターの場合) |Y (Microsoft によってテスト済み) |Y (Microsoft によってテスト済み) |Y (Microsoft によってテスト済み) |Y (Microsoft によってテスト済み) |Y (Microsoft によってテスト済み)|
| Exchange (DAG 以外) |Y |Y |Y |Y |Y|
| リモート デスクトップ/VDI |Y |Y |Y |Y |Y|
| Linux (オペレーティング システムおよびアプリケーション) |Y (Microsoft によってテスト済み) |Y (Microsoft によってテスト済み) |Y (Microsoft によってテスト済み) |Y (Microsoft によってテスト済み) |Y (Microsoft によってテスト済み)|
| Dynamics AX |Y |Y |Y |Y |Y|
| Windows ファイル サーバー |Y |Y |Y |Y |Y|
| Citrix XenApp と XenDesktop |Y|該当なし |Y |該当なし |Y |

## <a name="replicate-active-directory-and-dns"></a>Active Directory と DNS のレプリケート
Active Directory と DNS インフラストラクチャは多くのエンタープライズ アプリにとって不可欠な要素です。 障害復旧の際は、これらのインフラストラクチャ コンポーネントを保護して復旧した後に、ワークロードとアプリケーションを復旧することが必要になります。

Site Recovery を使用すると、Active Directory と DNS に合わせて完全な自動ディザスター リカバリー計画を作成できます。 たとえば、プライマリ サイトからセカンダリ サイトに SharePoint や SAP をフェールオーバーする場合は、まず Active Directory をフェールオーバーする復旧計画を策定してから、Active Directory に依存する他のアプリをフェールオーバーするためのアプリごとの復旧計画を追加で策定します。

[こちら](site-recovery-active-directory.md) を参照してください。

## <a name="protect-sql-server"></a>SQL Server の保護
SQL Server は、オンプレミスのデータセンターの多くのビジネス アプリにデータ サービス基盤を提供します。  Site Recovery を SQL Server HA/DR テクノロジと共に使用して、SQL Server を使用する多層エンタープライズ アプリを保護できます。 Site Recovery で提供されるものは次のとおりです。

* SQL Server に対応したシンプルでコスト効率の良いディザスター リカバリー ソリューション。 SQL Server のスタンドアロン サーバーとクラスターの複数のバージョンとエンドポイントを Azure またはセカンダリ サイトにレプリケートします。  
* SQL AlwaysOn 可用性グループとの統合。これにより、Azure Site Recovery の復旧計画を使用してフェールオーバーとフェールバックを管理できます。
* SQL Server データベースなど、アプリケーションのすべての層に対応するエンド ツー エンドの復旧計画。
* Site Recovery による、ピーク時の負荷に対応した SQL Server のスケーリング。このために、Azure でより大きな IaaS 仮想マシン サイズに負荷を "バースト" します。
* SQL Server ディザスター リカバリーの容易なテスト。 テスト フェールオーバーを実行して、運用環境に影響を与えることなく、データの分析とコンプライアンス チェックを実施できます。

[こちら](site-recovery-sql.md) を参照してください。

## <a name="protect-sharepoint"></a>SharePoint の保護
Azure Site Recovery は、SharePoint デプロイの保護に次のように役立ちます。

* ディザスター リカバリー用のスタンバイ ファームを用意する必要がなくなり、関連するインフラストラクチャ コストも削減されます。 Site Recovery を使用して、ファーム全体 (Web 層、アプリケーション層、データベース層) を Azure またはセカンダリ サイトにレプリケートできます。
* アプリケーションのデプロイと管理が簡易になります。 プライマリ サイトにデプロイされた更新プログラムは自動的にレプリケートされるため、ファームをセカンダリ サイトにフェールオーバーして復旧した後で利用できます。 また、スタンバイ ファームを最新の状態に保つための複雑な管理が不要になり、関連するコストも削減されます。
* テストやデバッグ用に必要に応じて運用環境に似たレプリカ環境のコピーを作成することで、SharePoint アプリケーションの開発とテストが簡単になります。
* Site Recovery を使用して SharePoint のデプロイメントを Azure に移行することで、クラウドへの移行が簡単になります。

[こちら](site-recovery-sharepoint.md) を参照してください。

## <a name="protect-dynamics-ax"></a>Dynamics AX の保護
Azure Site Recovery は、次のような点で Dynamics AX ERP ソリューションの保護に役立ちます。

* Azure またはセカンダリ サイトへの Dynamics AX 環境全体 (Web 層、AOS 層、データベース層、SharePoint) のレプリケーションを調整します。
* クラウド (Azure) への Dynamics AX デプロイメントの移行が簡略化されます。
* 必要に応じてテストやデバッグ用に運用環境に似たコピーを作成することで、Dynamics AX アプリケーションの開発とテストが簡単になります。

[こちら](site-recovery-dynamicsax.md) を参照してください。

## <a name="protect-rds"></a>RDS の保護
リモート デスクトップ サービス (RDS) を使用すると、ユーザーが仮想デスクトップ インフラストラクチャ (VDI)、セッションベースのデスクトップ、アプリケーションを使用して、どこにいても作業できるようになります。 Azure Site Recovery を使用すると、次のことができます。

* 管理対象か管理対象外かを問わず、プールされた仮想デスクトップをセカンダリ サイトにレプリケートし、リモート アプリケーションとセッションをセカンダリ サイトまたは Azure にレプリケートできます。

* レプリケートできるシナリオは次のとおりです。

| **RDS** |**Azure VM を Azure にレプリケートする** | **Hyper-V VM をセカンダリ サイトにレプリケート** | **Hyper-V VM を Azure にレプリケート** | **VMware VM をセカンダリ サイトにレプリケート** | **VMware VM を Azure にレプリケート** | **物理サーバーをセカンダリ サイトにレプリケート** | **物理サーバーを Azure にレプリケート** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **プールされた仮想デスクトップ (管理対象外)** |いいえ|はい |いいえ |はい |いいえ |はい |いいえ |
| **プールされた仮想デスクトップ (管理対象、UPD なし)** |いいえ|はい |いいえ |はい |いいえ |はい |いいえ |
| **リモート アプリケーションとデスクトップ セッション (UPD なし)** |はい|はい |はい |はい |はい |はい |はい |

[Azure Site Recovery を使用して RDS のディザスター リカバリーを設定します](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure)。

[こちら](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) を参照してください。

## <a name="protect-exchange"></a>Exchange の保護
Site Recovery は、Exchange の保護に次のように役立ちます。

* 単一サーバーやスタンドアロン サーバーなど、小規模な Exchange デプロイの場合、Site Recovery では Azure またはセカンダリ サイトへのレプリケートとフェールオーバーを実行できます。
* 大規模なデプロイでは、Exchange DAG と統合します。
* Exchange DAG は、企業における Exchange のディザスター リカバリーに推奨されるソリューションです。  Site Recovery の復旧計画に DAG を含めることで、サイト全体の DAG のフェールオーバーを調整できます。

[こちら](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) を参照してください。

## <a name="protect-sap"></a>SAP の保護
Site Recovery を使用して、SAP デプロイを次のように保護できます。

* コンポーネントを Azure にレプリケートすることによって、オンプレミスで実行されている SAP NetWeaver および非 NetWeaver 運用アプリケーションの保護を有効にします。
* コンポーネントを他の Azure データセンターにレプリケートすることによって、Azure で実行されている SAP NetWeaver および非 NetWeaver 運用アプリケーションの保護を有効にします。
* Site Recovery を使用して SAP デプロイを Azure に移行することで、クラウド移行を簡略化します。
* SAP アプリケーションをテストするためのオンデマンドの運用クローンを作成することによって、SAP プロジェクトのアップグレード、テスト、および、プロトタイプ作成を簡略化します。

[こちら](site-recovery-sap.md) を参照してください。

## <a name="protect-iis"></a>IIS の保護
Site Recovery を使用して、IIS デプロイを次のように保護できます。

Azure Site Recovery では、環境内の重要なコンポーネントをリモートのコールド サイトまたはパブリック クラウド (Microsoft Azure など) にレプリケートして、ディザスター リカバリーを実行できます。 Web サーバーとデータベースを備えた仮想マシンが復旧サイトにレプリケートされるため、構成ファイルまたは証明書を別々にバックアップする必要はありません。 アプリケーションのマッピングとバインドはフェールオーバー後に変更される環境変数に依存しますが、これらはディザスター リカバリー計画に統合されたスクリプトを使用して更新できます。 仮想マシンが復旧サイトで起動されるのはフェールオーバーの発生時だけです。 さらに、Azure Site Recovery はエンド ツー エンドのフェールオーバーを調整する場合にも役立ちます。これは次の機能を備えているためです。

-   各種階層における仮想マシンのシャットダウンと起動のシーケンス処理。
-   仮想マシン上のアプリケーションの依存関係とバインドを仮想マシンの起動後に更新できるスクリプトの追加。 スクリプトは、復旧サイトを指すよう DNS サーバーを更新するためにも使用できます。
-   プライマリ ネットワークと復旧ネットワークをマッピングして、フェールオーバー前に IP アドレスを仮想マシンに割り当てます。そのため、使用するスクリプトはフェールオーバー後に更新する必要がありません。
-   Web サーバー上の複数の Web アプリケーションをワンクリックでフェールオーバーする機能。この機能により、障害発生時の混乱を回避できます。
-   DR ドリル用の分離された環境で復旧計画のテストを実行する機能。

IIS Web ファームの保護の詳細については、[こちら](https://aka.ms/asr-iis)を参照してください。

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Citrix XenApp と XenDesktop の保護
Site Recovery を使用して、Citrix XenApp と XenDesktop のデプロイを次のように保護できます。

* (AD DNS サーバー、SQL データベース サーバー、Citrix Delivery Controller、StoreFront サーバー、XenApp マスター (VDA)、Citrix XenApp ライセンス サーバーなどの) 各種のデプロイ層を Azure にレプリケートして、Citrix XenApp と XenDesktop のデプロイの保護を有効にします。
* Site Recovery を使用して Citrix XenApp と XenDesktop のデプロイを Azure に移行することで、クラウド移行を簡略化します。
* 必要に応じてテストとデバッグ用に運用環境に似たコピーを作成して、Citrix XenApp/XenDesktop のテストを簡略化します。
* このソリューションは、Windows Server オペレーティング システム仮想デスクトップにのみ適用でき、クライアント仮想デスクトップには適用できません。これは、Azure のライセンスでクライアント仮想デスクトップがまだサポートされていないためです。
Azure におけるクライアント/サーバー デスクトップのライセンスについては、[こちら](https://azure.microsoft.com/pricing/licensing-faq/)を参照してください。

Citrix XenApp と XenDesktop のデプロイの保護については、[こちら](site-recovery-citrix-xenapp-and-xendesktop.md)を参照してください。 同じ情報は、[Citrix のホワイトペーパー](https://aka.ms/citrix-xenapp-xendesktop-with-asr)でもご覧いただけます。

## <a name="next-steps"></a>次の手順

Azure VM レプリケーションの[使用を開始します](azure-to-azure-quickstart.md)。
