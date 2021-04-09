---
title: Azure Site Recovery を使用したオンプレミス アプリのディザスター リカバリーについて
description: Azure Site Recovery サービスによるディザスター リカバリーを使用して保護できるワークロードについて説明します。
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 2b901425a0020c0ccc7b834ee36d965910028018
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "80062837"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>オンプレミス アプリのディザスター リカバリーについて

この記事では、[Azure Site Recovery](site-recovery-overview.md) サービスによるディザスター リカバリーを使用して保護できるオンプレミスのワークロードとアプリについて説明します。

## <a name="overview"></a>概要

組織には、計画ダウンタイムと計画外ダウンタイムの間にワークロードとデータの安全性と可用性を維持するために、事業継続とディザスター リカバリー (BCDR) の戦略が必要です。 さらに、通常の動作状態に復旧させます。

Site Recovery は、BCDR 戦略に貢献する Azure サービスです。 Site Recovery を使用することで、アプリケーションに対応したレプリケーションをクラウドまたはセカンダリ サイトにデプロイできます。 Site Recovery を使用してレプリケーションを管理し、ディザスター リカバリー テストを実行し、フェールオーバーとフェールバックを実行できます。 アプリは、Windows または Linux ベースのコンピューター、物理サーバー、VMware、または Hyper-V で実行できます。

Site Recovery は、SharePoint、Exchange、Dynamics、SQL Server、Active Directory などの Microsoft アプリケーションと統合します。 Microsoft は、Oracle、SAP、Red Hat などの主要なベンダーと密接に連携しています。 レプリケーション ソリューションをアプリケーションごとにカスタマイズすることができます。

## <a name="why-use-site-recovery-for-application-replication"></a>アプリケーションのレプリケーションに Site Recovery を使用する理由

Site Recovery は、アプリケーション レベルの保護と復旧に次のように役立ちます。

- アプリに依存しないため、サポートされているマシンで実行されるすべてのワークロードに対してレプリケーションを行えます。
- 回復ポイントの目標 (RPO) を使用した近同期レプリケーションにかかる時間はわずか 30 秒間であるため、重要度の高いビジネス アプリケーションのニーズを満たします。
- アプリケーションとの整合性が取られたスナップショットであるため、単一層または複数層のアプリケーションに適しています。
- SQL Server AlwaysOn との統合、および他のアプリケーション レベルのレプリケーション テクノロジとのパートナーシップ。 たとえば、Active Directory Domain Services レプリケーション、SQL AlwaysOn、Exchange データベース可用性グループ (DAG) などです。
- 柔軟な復旧計画を策定し、1 回のクリックでアプリケーション スタック全体を復旧できます。この計画に、外部スクリプトや手動のアクションを含めることもできます。
- Site Recovery と Azure の高度なネットワーク管理により、アプリのネットワーク要件が簡素化されます。 IP アドレスの予約、負荷分散の構成、低い目標復旧時間 (RTO) ネットワーク切り替え用の Azure Traffic Manager との統合などのネットワーク管理機能。
- 豊富な自動化ライブラリに、運用環境ですぐに使えるアプリケーション固有のスクリプトが用意されています。これらのスクリプトをダウンロードして、復旧計画に統合することができます。

## <a name="workload-summary"></a>ワークロードの概要

Site Recovery は、サポート対象のマシンで実行されているすべてのアプリをレプリケートできます。 Microsoft では、製品チームと連携して、次の表に記載されているアプリのテストを追加で実施しました。

| **[ワークロード]** |**Azure VM を Azure にレプリケートする** |**Hyper-V VM をセカンダリ サイトにレプリケート** | **Hyper-V VM を Azure にレプリケート** | **VMware VM をセカンダリ サイトにレプリケート** | **VMware VM を Azure にレプリケート** |
| --- | --- | --- | --- | --- |---|
| Active Directory、DNS |はい |はい |はい |はい |はい|
| Web アプリケーション (IIS、SQL) |はい |はい |はい |はい |はい|
| System Center Operations Manager |はい |はい |はい |はい |はい|
| SharePoint |はい |はい |はい |はい |はい|
| SAP<br/><br/>SAP サイトを Azure にレプリケート (非クラスターの場合) |○ (Microsoft によってテスト済み) |○ (Microsoft によってテスト済み) |○ (Microsoft によってテスト済み) |○ (Microsoft によってテスト済み) |○ (Microsoft によってテスト済み)|
| Exchange (DAG 以外) |はい |はい |はい |はい |はい|
| リモート デスクトップ/VDI |はい |はい |はい |はい |はい|
| Linux (オペレーティング システムおよびアプリケーション) |○ (Microsoft によってテスト済み) |○ (Microsoft によってテスト済み) |○ (Microsoft によってテスト済み) |○ (Microsoft によってテスト済み) |○ (Microsoft によってテスト済み)|
| Dynamics AX |はい |はい |はい |はい |はい|
| Windows ファイル サーバー |はい |はい |はい |はい |はい|
| Citrix XenApp と XenDesktop |はい|該当なし |はい |該当なし |はい |

## <a name="replicate-active-directory-and-dns"></a>Active Directory と DNS のレプリケート

Active Directory と DNS インフラストラクチャは多くのエンタープライズ アプリにとって不可欠な要素です。 ディザスター リカバリーの際は、これらのインフラストラクチャ コンポーネントを保護して復旧した後に、ワークロードとアプリケーションを復旧することが必要になります。

Site Recovery を使用すると、Active Directory と DNS に合わせて完全な自動ディザスター リカバリー計画を作成できます。 たとえば、SharePoint と SAP をプライマリからセカンダリ サイトにフェールオーバーするには、最初に Active Directory Domain Services をフェールオーバーする復旧計画を設定します。 その後、追加のアプリ固有の復旧計画を使用して、Active Directory Domain Services に依存する他のアプリをフェールオーバーします。

Active Directory Domain Services と DNS のディザスター リカバリーの[詳細を確認](site-recovery-active-directory.md)してください。

## <a name="protect-sql-server"></a>SQL Server の保護

SQL Server は、オンプレミスのデータセンターの多くのビジネス アプリにデータ サービス基盤を提供します。 Site Recovery を SQL Server の HA/DR テクノロジと共に使用して、SQL Server を使用する多層エンタープライズ アプリを保護できます。

Site Recovery で提供されるものは次のとおりです。

- SQL Server に対応したシンプルでコスト効率の良いディザスター リカバリー ソリューション。 SQL Server のスタンドアロン サーバーとクラスターの複数のバージョンとエンドポイントを Azure またはセカンダリ サイトにレプリケートします。
- SQL AlwaysOn 可用性グループとの統合。これにより、Azure Site Recovery の復旧計画を使用してフェールオーバーとフェールバックを管理できます。
- SQL Server データベースなど、アプリケーションのすべての層に対応するエンド ツー エンドの復旧計画。
- Site Recovery による、ピーク時の負荷に対応した SQL Server のスケーリング。このために、Azure でより大きな IaaS 仮想マシン サイズに負荷を "_バースト_" します。
- SQL Server ディザスター リカバリーの容易なテスト。 テスト フェールオーバーを実行して、運用環境に影響を与えることなく、データの分析とコンプライアンス チェックを実施できます。

SQL Server のディザスター リカバリーの[詳細](site-recovery-sql.md)について確認してください。

## <a name="protect-sharepoint"></a>SharePoint の保護

Azure Site Recovery は、SharePoint デプロイの保護に次のように役立ちます。

- ディザスター リカバリー用のスタンバイ ファームを用意する必要がなくなり、関連するインフラストラクチャ コストも削減されます。 Site Recovery を使用して、ファーム全体 (Web、アプリ、データベース層) を Azure またはセカンダリ サイトにレプリケートできます。
- アプリケーションのデプロイと管理が簡易になります。 プライマリ サイトに展開された更新は、自動的にレプリケートされます。 更新は、ファームのフェールオーバーとセカンダリ サイトでの復旧後に使用できます。 スタンバイ ファームを最新の状態に保つための管理の複雑さとコストが削減されます。
- テストやデバッグ用に必要に応じて運用環境に似たレプリカ環境のコピーを作成することで、SharePoint アプリケーションの開発とテストが簡単になります。
- Site Recovery を使用して SharePoint のデプロイメントを Azure に移行することで、クラウドへの移行が簡単になります。

SharePoint のディザスター リカバリーの[詳細](site-recovery-sharepoint.md)について確認してください。

## <a name="protect-dynamics-ax"></a>Dynamics AX の保護

Azure Site Recovery は、次のような点で Dynamics AX ERP ソリューションの保護に役立ちます。

- Azure またはセカンダリ サイトへの Dynamics AX 環境全体 (Web および AOS 層、データベース層、SharePoint) のレプリケーションを管理します。
- クラウド (Azure) への Dynamics AX デプロイメントの移行が簡略化されます。
- 必要に応じてテストやデバッグ用に運用環境に似たコピーを作成することで、Dynamics AX アプリケーションの開発とテストが簡単になります。

Dynamic AX のディザスター リカバリーの[詳細](site-recovery-dynamicsax.md)について確認してください。

## <a name="protect-remote-desktop-services"></a>リモート デスクトップ サービスの保護

リモート デスクトップ サービス (RDS) を使用すると、ユーザーが仮想デスクトップ インフラストラクチャ (VDI)、セッションベースのデスクトップ、アプリケーションを使用して、どこにいても作業できるようになります。

Azure Site Recovery では、次のサービスをレプリケートできます。

- 管理対象または管理されていないプールされた仮想デスクトップをセカンダリ サイトにレプリケートする。
- リモート アプリケーションとセッションをセカンダリ サイトまたは Azure にレプリケートする。

次の表に、レプリケーションのオプションを示します。

| **RDS** |**Azure VM を Azure にレプリケートする** | **Hyper-V VM をセカンダリ サイトにレプリケート** | **Hyper-V VM を Azure にレプリケート** | **VMware VM をセカンダリ サイトにレプリケート** | **VMware VM を Azure にレプリケート** | **物理サーバーをセカンダリ サイトにレプリケート** | **物理サーバーを Azure にレプリケート** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **プールされた仮想デスクトップ (管理対象外)** |いいえ|はい |いいえ |はい |いいえ |はい |いいえ |
| **プールされた仮想デスクトップ (管理対象、UPD なし)** |いいえ|はい |いいえ |はい |いいえ |はい |いいえ |
| **リモート アプリケーションとデスクトップ セッション (UPD なし)** |はい|はい |はい |はい |はい |はい |はい |

RDS のディザスター リカバリーの[詳細](/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure)を確認してください。

## <a name="protect-exchange"></a>Exchange の保護

Site Recovery は、Exchange の保護に次のように役立ちます。

- 単一サーバーやスタンドアロン サーバーなど、小規模な Exchange デプロイの場合、Site Recovery では Azure またはセカンダリ サイトへのレプリケートとフェールオーバーを実行できます。
- 大規模なデプロイでは、Exchange DAG と統合します。
- Exchange DAG は、企業における Exchange のディザスター リカバリーに推奨されるソリューションです。  Site Recovery の復旧計画に DAG を含めることで、サイト全体の DAG のフェールオーバーを調整できます。

Exchange のディザスター リカバリーの詳細については、[Exchange の DAG](/Exchange/high-availability/database-availability-groups/database-availability-groups) および [Exchange のディザスター リカバリー](/Exchange/high-availability/disaster-recovery/disaster-recovery)に関する記事を参照してください。

## <a name="protect-sap"></a>SAP の保護

Site Recovery を使用して、SAP デプロイを次のように保護できます。

- コンポーネントを Azure にレプリケートすることによって、オンプレミスで実行されている SAP NetWeaver および非 NetWeaver 運用アプリケーションの保護を有効にします。
- コンポーネントを他の Azure データセンターにレプリケートすることによって、Azure で実行されている SAP NetWeaver および非 NetWeaver 運用アプリケーションの保護を有効にします。
- Site Recovery を使用して SAP デプロイを Azure に移行することで、クラウド移行を簡略化します。
- SAP アプリケーションをテストするためのオンデマンドの運用クローンを作成することによって、SAP プロジェクトのアップグレード、テスト、および、プロトタイプ作成を簡略化します。

SAP のディザスター リカバリーの[詳細](site-recovery-sap.md)を確認してください。

## <a name="protect-internet-information-services"></a>インターネット インフォメーション サービスの保護

Site Recovery を使用して、インターネット インフォメーション サービス (IIS) デプロイを次のように保護できます。

Azure Site Recovery では、環境内の重要なコンポーネントをリモートのコールド サイトまたはパブリック クラウド (Microsoft Azure など) にレプリケートして、ディザスター リカバリーを実行できます。 Web サーバーとデータベースを備えた仮想マシンが復旧サイトにレプリケートされるため、構成ファイルまたは証明書の個別のバックアップは必要はありません。 アプリケーションのマッピングとバインドはフェールオーバー後に変更される環境変数に依存しますが、これらはディザスター リカバリー計画に統合されたスクリプトを使用して更新できます。 仮想マシンが復旧サイトで起動されるのはフェールオーバーの間だけです。 Azure Site Recovery はエンドツーエンドのフェールオーバーを調整する場合にも役立ちます。これは次の機能を備えているためです。

- 各種階層における仮想マシンのシャットダウンと起動のシーケンス処理。
- 仮想マシン上のアプリケーションの依存関係とバインドをそれらの起動後に更新できるスクリプトの追加。 スクリプトは、復旧サイトを指すよう DNS サーバーを更新するためにも使用できます。
- プライマリ ネットワークと復旧ネットワークをマッピングして、フェールオーバー前に IP アドレスを仮想マシンに割り当てます。使用するスクリプトはフェールオーバー後に更新する必要がありません。
- 複数の Web アプリケーションをワンクリックでフェールオーバーする機能。これにより、障害発生時の混乱を回避できます。
- DR ドリル用の分離された環境で復旧計画のテストを実行する機能。

IIS のディザスター リカバリーの[詳細](site-recovery-iis.md)を確認してください。

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Citrix XenApp と XenDesktop の保護

Site Recovery を使用して、Citrix XenApp と XenDesktop のデプロイを次のように保護できます。

- Citrix XenApp と XenDesktop のデプロイの保護を有効にします。 さまざまなデプロイ層を Azure にレプリケートします。Active Directory Domain Services、DNS サーバー、SQL データベース サーバー、Citrix Delivery Controller、StoreFront サーバー、XenApp マスター (VDA)、Citrix XenApp ライセンス サーバー。
- Site Recovery を使用して Citrix XenApp と XenDesktop のデプロイを Azure に移行することで、クラウド移行を簡略化します。
- 必要に応じてテストとデバッグ用に運用環境に似たコピーを作成して、Citrix XenApp/XenDesktop のテストを簡略化します。
- このソリューションは、クライアント仮想デスクトップではなく、Windows Server 仮想デスクトップのみに適用されます。 クライアント仮想デスクトップは、Azure でのライセンスがまだサポートされていません。 Azure におけるクライアント/サーバー デスクトップのライセンスについては、[こちら](https://azure.microsoft.com/pricing/licensing-faq/)を参照してください。

Citrix XenApp および XenDesktop デプロイのディザスター リカバリーについて[詳細](site-recovery-citrix-xenapp-and-xendesktop.md)を確認してください。 または、[Citrix のホワイトペーパー](https://aka.ms/citrix-xenapp-xendesktop-with-asr)を参照してください。

## <a name="next-steps"></a>次のステップ

Azure VM のディザスター リカバリーの[詳細](azure-to-azure-quickstart.md)を確認してください。
