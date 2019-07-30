---
title: Azure Site Recovery を使用した VMware から Azure へのディザスター リカバリーに関するよくある質問 | Microsoft Docs
description: Azure Site Recovery を使用してオンプレミスの VMware VM を Azure にディザスター リカバリーする場合のよくある質問に対する回答を確認します。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 06/27/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 79118fb053c7064fa29730680feb0434f45f031a
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491690"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>VMware から Azure へのレプリケーションに関するよくある質問

この記事では、オンプレミスの VMware 仮想マシン (VM) の Azure へのディザスター リカバリーをデプロイするときに生じる可能性があるよくある質問に回答します。

## <a name="general"></a>全般

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>VMware VM のディザスター リカバリーには何が必要ですか?

VMware VM のディザスター リカバリーに[関与するコンポーネントについて説明](vmware-azure-architecture.md)します。

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Site Recovery を使って、VMware VM を Azure に移行できますか?

はい。 Site Recovery を使用すると、VMware VM の完全なディザスター リカバリーを設定するだけでなく、オンプレミスの VMware VM を Azure に移行することもできます。 このシナリオでは、オンプレミスの VMware VM を Azure Storage にレプリケートします。 その後、オンプレミスから Azure にフェールオーバーします。 フェールオーバーの後は、Azure VM で実行しているアプリとワークロードを利用できます。 このプロセスは、完全なディザスター リカバリーの設定と似ていますが、移行では Azure からフェールバックできない点が異なります。

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Azure アカウントには VM を作成するアクセス許可が必要ですか?

サブスクリプション管理者の場合は、必要なレプリケーション アクセス許可を持っています。 管理者でない場合、次のアクションを行うにはアクセス許可が必要です。

- Site Recovery を構成するときに指定するリソース グループと仮想ネットワークに Azure VM を作成します。
- 構成に基づいて、選択したストレージ アカウントまたはマネージド ディスクに書き込みます。

必要なアクセス許可の[詳細をご覧ください](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)。

### <a name="what-applications-can-i-replicate"></a>どのアプリケーションをレプリケートできますか?

[レプリケーション要件](vmware-physical-azure-support-matrix.md#replicated-machines)を満たす VMware VM で実行しているあらゆるアプリまたはワークロードをレプリケートできます。

- Site Recovery はアプリケーション対応レプリケーションをサポートするため、アプリをフェールオーバーし、インテリジェントな状態にフェールバックできます。
- Site Recovery は、SharePoint、Exchange、Dynamics、SQL Server、Active Directory などの Microsoft アプリケーションと統合します。 また、Oracle、SAP、IBM、Red Hat などの主要なベンダーと密接に連携しています。

[詳細情報](site-recovery-workload.md) を参照してください。

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Azure でゲスト OS のサーバー ライセンスを使用できますか?

はい、マイクロソフト ソフトウェア アシュランスのお客様は、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を使用して、Azure に移行する Windows Server マシンのライセンス コストを節約したり、Azure をディザスター リカバリーに使用したりできます。

## <a name="security"></a>セキュリティ

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Site Recovery では、VMware サーバーへのどのようなアクセスが必要ですか?

Site Recovery では、次のことを実行するために、VMware サーバーへのアクセスが必要です。

- Site Recovery 構成サーバーを実行する VMware VM を設定します。
- レプリケーション対象の VM を自動的に検出します。

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Site Recovery では、VMware VM へのどのようなアクセスが必要ですか?

- レプリケートするには、VMware VM に Site Recovery のモビリティ サービスがインストールされて実行していることが必要です。 ツールを手動でデプロイすることも、VM のレプリケーションを有効にするときにサービスのプッシュ インストールを行うよう Site Recovery に指定することもできます。
- レプリケーションの間に、VM は Site Recovery と次のように通信します。
    - VM は、レプリケーション管理のために、HTTPS ポート 443 で構成サーバーと通信します。
    - VM は HTTPS ポート 9443 でレプリケーション データをプロセス サーバーに送信します。 (この設定は変更できます)。
    - マルチ VM 整合性を有効にすると、VM はポート 20004 で相互に通信します。

### <a name="is-replication-data-sent-to-site-recovery"></a>レプリケーション データは Site Recovery に送信されますか?

いいえ。Site Recovery は、レプリケートされたデータをインターセプトすることも、VM での実行内容に関するどのような情報を持つこともありません。 レプリケーション データは、VMware ハイパーバイザーと Azure Storage の間で交換されます。 Site Recovery には、これらのデータをインターセプトする能力はありません。 レプリケーションとフェールオーバーを調整するために必要なメタデータのみが、Site Recovery サービスに送信されます。  

Site Recovery は ISO 27001:2013 および 27018、HIPAA、DPA の認証を受けています。 SOC2 および FedRAMP JAB の評価が進行中です。

## <a name="pricing"></a>価格

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>VMware ディザスター リカバリーの概算料金は、どのようにして計算しますか?

Site Recovery を使用している間は、[料金計算ツール](https://aka.ms/asr_pricing_calculator)を利用してコストを見積もります。

コストの詳細な見積もりのためには、[VMware](https://aka.ms/siterecovery_deployment_planner) に対して Deployment Planner ツールを実行し、[コスト見積もりレポート](https://aka.ms/asr_DP_costreport)を使用します。

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>ストレージにレプリケートする場合とマネージド ディスクに直接レプリケートする場合との間にコストの差はありますか?

マネージド ディスクに対する課金は、ストレージ アカウントに対するものとは若干異なります。 マネージド ディスクの価格に関する[詳細を参照](https://azure.microsoft.com/pricing/details/managed-disks/)してください。

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>General Purpose v2 ストレージ アカウントにレプリケートする場合、コストに差はありますか?

Azure Site Recovery のトランザクションは大量にあるため、一般的には GPv2 ストレージ アカウントについて発生するトランザクション コストの増加が見られます。 差を見積もるには、[こちら](../storage/common/storage-account-upgrade.md#pricing-and-billing)をご覧ください。

## <a name="mobility-service"></a>モビリティ サービス

### <a name="where-can-i-find-the-mobility-service-installers"></a>モビリティ サービスのインストーラーはどこにありますか?

インストーラーは、構成サーバーの %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository フォルダーにあります。

## <a name="how-do-i-install-the-mobility-service"></a>モビリティ サービスはどのようにしてインストールしますか?

レプリケートする各 VM で、次の複数の方法のいずれかでサービスをインストールします。

- [プッシュ インストール](vmware-physical-mobility-service-overview.md#push-installation)
- UI または PowerShell からの[手動インストール](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui)
- [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md) などのデプロイ ツールを使用したデプロイ

## <a name="managed-disks"></a>マネージド ディスク

### <a name="where-does-site-recovery-replicate-data-to"></a>Site Recovery では、データはどこにレプリケートされますか?

Site Recovery では、オンプレミスの VMware VM と物理サーバーが Azure のマネージド ディスクにレプリケートされます。

- Site Recovery のプロセス サーバーは、レプリケーション ログをターゲット リージョンのキャッシュ ストレージ アカウントに書き込みます。
- これらのログは、**asrseeddisk** のプレフィックスを持つ Azure マネージド ディスクの復旧ポイントを作成するために使用されます。
- フェールオーバーが発生すると、選択された復旧ポイントを使用して、新しいターゲット マネージド ディスクが作成されます。 このマネージド ディスクは、Azure で VM にアタッチされます。
- 以前 (2019 年 3 月より前) にストレージ アカウントにレプリケートされた VM は影響を受けません。

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>新しいマシンをストレージ アカウントにレプリケートできますか?

いいえ。 いいえ。2019 年 3 月以降、Azure portal では、Azure のマネージド ディスクだけにレプリケートできます。

ストレージ アカウントへの新しい VM のレプリケーションは、PowerShell または REST API (バージョン 2018-01-10 または 2016-08-10) を使用する場合のみ、行うことができます。

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>マネージド ディスクのレプリケートにはどのようなメリットがありますか?

マネージド ディスクを使用することで Site Recovery でのディザスター リカバリーが[どのように簡単になるかを確認](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/)してください。

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>マシンに保護を適用した後で、マネージド ディスクの種類を変更できますか?

はい。実行中のレプリケーションの場合、[マネージド ディスクの種類は簡単に変更](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage)できます。 種類を変更する前に、マネージド ディスクで Shared Access Signature の URL が生成されていないことを確認します。

1. Azure portal の**マネージド ディスク** リソースに移動し、 **[概要]** ブレードに Shared Access Signature の URL バナーがあるかどうかを確認します。
1. バナーが表示されている場合は、これを選択して進行中のエクスポートをキャンセルします。
1. 数分以内にディスクの種類を変更します。 種類がマネージド ディスクであるディスクを変更する場合は、Azure Site Recovery によって新しい復旧ポイントが生成されるまで待機します。
1. すべてのテスト フェールオーバーまたは今後のフェールオーバーで、新しい復旧ポイントを使用します。

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>マネージド ディスクからアンマネージド ディスクにレプリケーションを切り替えることはできますか?

いいえ。 マネージドからアンマネージドへの切り替えはサポートされていません。

## <a name="replication"></a>レプリケーション

### <a name="what-are-the-replicated-vm-requirements"></a>レプリケートされる VM にはどのような要件がありますか?

VMware VM と物理サーバーのサポート要件に関する[詳細をご覧ください](vmware-physical-azure-support-matrix.md#replicated-machines)。

### <a name="how-often-can-i-replicate-to-azure"></a>どのくらいの頻度で Azure にレプリケートできますか?

VMware VM を Azure にレプリケートするときは、レプリケーションは継続的に行われます。

### <a name="can-i-extend-replication"></a>レプリケーションを拡張することはできますか?

拡張またはチェーン レプリケーションはサポートされていません。 [フィードバック フォーラム](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)でこの機能を要求してください。

### <a name="can-i-do-an-offline-initial-replication"></a>オフラインの初期レプリケーションを行うことはできますか?

オフライン レプリケーションはサポートされていません。 [フィードバック フォーラム](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)でこの機能を要求してください。

### <a name="what-is-asrseeddisk"></a>asrseeddisk とは何ですか?

すべてのソース ディスクで、データは Azure のマネージド ディスクにレプリケートされます。 このディスクには、**asrseeddisk** のプレフィックスがあります。 ここには、ソース ディスクのコピーとすべての回復ポイントのスナップショットが格納されます。

### <a name="can-i-exclude-disks-from-replication"></a>レプリケーションからディスクを除外できますか?

はい。ディスクを除外できます。

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>ダイナミック ディスクを含む VM をレプリケートできますか?

ダイナミック ディスクをレプリケートすることができます。 オペレーティング システム ディスクはベーシック ディスクである必要があります。

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>マルチ VM 整合性用のレプリケーション グループを使用する場合、新しい VM を既存のレプリケーション グループに追加できますか?

はい、既存のレプリケーション グループのレプリケーションを有効にするときに、そのグループに新しい VM を追加できます。 ただし

- レプリケーションが開始された後で既存のレプリケーション グループに VM を追加することはできません。
- 既存の VM 用のレプリケーション グループを作成することはできません。

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>レプリケートされる VM を、ディスクの追加またはサイズ変更によって変更することはできますか?

Azure への VMware のレプリケーションでは、ディスクのサイズを変更できます。 新しいディスクを追加する場合は、ディスクを追加し、VM の保護を再度有効にする必要があります。

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>進行中のレプリケーションに影響を与えることなく、オンプレミスのマシンを新しい vCenter Server に移行できますか?

いいえ。 VMware Vcenter を変更したり、移行を行ったりすると、進行中のレプリケーションに影響が及びます。 新しい vCenter Server を使用して Site Recovery を設定し、マシンのレプリケーションを再度有効にします。

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>仮想ネットワーク (Azure Storage ファイアウォールあり) が構成されているキャッシュまたはターゲット ストレージ アカウントにレプリケートすることはできますか?

いいえ、Site Recovery では、仮想ネットワーク上の Azure Storage へのレプリケーションはサポートされていません。

## <a name="component-upgrade"></a>コンポーネントのアップグレード

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>モビリティ サービス エージェントまたは構成サーバーのバージョンが古く、アップグレードが失敗しました。 どうすればよいですか。

Site Recovery は、N-4 サポート モデルに従っています。 非常に古いバージョンからアップグレードする方法に関する[詳細を参照](https://aka.ms/asr_support_statement)してください。

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>Azure Site Recovery のリリース ノートと更新プログラムのロールアップはどこで入手できますか?

[新しい更新プログラムに関する詳細を確認](site-recovery-whats-new.md)し、[ロールアップ情報を取得](service-updates-how-to.md)してください。

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Azure へのディザスター リカバリーのアップグレードに関する情報はどこで入手できますか?

[アップグレードに関する詳細を参照してください](https://aka.ms/asr_vmware_upgrades)。

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>アップグレードごとにソース マシンを再起動する必要がありますか?

アップグレードごとに再起動することをお勧めしますが、必須ではありません。 [詳細情報](https://aka.ms/asr_vmware_upgrades)。

## <a name="configuration-server"></a>構成サーバー

### <a name="what-does-the-configuration-server-do"></a>構成サーバーは何を行いますか?

構成サーバーは、次のようなオンプレミスの Site Recovery コンポーネントを実行します。

- 構成サーバー自体。 サーバーは、オンプレミス コンポーネントと Azure の間の通信を調整し、データのレプリケーションを管理します。
- プロセス サーバー。レプリケーション ゲートウェイとして機能します。 このサーバーは次の操作を行います。
    1. レプリケーション データを受信する。
    2. キャッシュ、圧縮、暗号化を使用してデータを最適化します。
    3. データを Azure Storage に送信します。
  また、プロセス サーバーは、VM への Mobility Service のプッシュ インストールや、オンプレミスの VMware VM の自動検出も行います。
- マスター ターゲット サーバー。Azure からのフェールバック中にレプリケーション データを処理します。

構成サーバーのコンポーネントとプロセスの[詳細をご確認ください](vmware-azure-architecture.md)。

### <a name="where-do-i-set-up-the-configuration-server"></a>構成サーバーはどこに設定しますか?

構成サーバーには、高可用性のオンプレミス VMware VM が 1 つ必要です。 物理サーバーのディザスター リカバリーの場合は、構成サーバーを物理マシンにインストールします。

### <a name="what-do-i-need-for-the-configuration-server"></a>構成サーバーには何が必要ですか?

前提条件については、[こちら](vmware-azure-deploy-configuration-server.md#prerequisites)をご覧ください。

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>テンプレートを使う代わりに、手動で構成サーバーを設定できますか?

最新バージョンの Open Virtual Machine Format (OVF) テンプレートを使用して[構成サーバー VM を作成する](vmware-azure-deploy-configuration-server.md)ことをお勧めします。 テンプレートを使用できない場合 (VMware サーバーにアクセスできない場合など)、ポータルからセットアップ ファイルを[ダウンロード](physical-azure-set-up-source.md)して、構成サーバーを設定します。

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>構成サーバーは複数のリージョンにレプリケートできますか?

いいえ。 複数のリージョンにレプリケートするには、各リージョンに構成サーバーが必要です。

### <a name="can-i-host-a-configuration-server-in-azure"></a>Azure で構成サーバーをホストできますか?

可能ですが、構成サーバーを実行している Azure VM は、オンプレミスの VMware のインフラストラクチャや VM と通信する必要があります。 この通信により待ち時間が長くなるほか、進行中のレプリケーションに影響が生じます。

### <a name="how-do-i-update-the-configuration-server"></a>構成サーバーはどのようにして更新できますか?

構成サーバーを更新する方法を[参照](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)してください。

- 最新の更新情報については、[Azure の更新情報のページ](https://azure.microsoft.com/updates/?product=site-recovery)を参照してください。
- 最新バージョンは、ポータルからダウンロードできます。 または、構成サーバーの最新バージョンは、[Microsoft ダウンロード センター](https://aka.ms/asrconfigurationserver)から直接ダウンロードできます。
- お使いの古いバージョンと最新のバージョンの差が 4 を超える場合は、[サポートに関する声明](https://aka.ms/asr_support_statement)でアップグレードのガイダンスを確認してください。

### <a name="should-i-back-up-the-configuration-server"></a>構成サーバーはバックアップする必要がありますか?

構成サーバーの定期的なスケジュールされたバックアップを実行することをお勧めします。

- フェールバックが成功するには、障害が発生している VM が構成サーバーのデータベースに存在している必要があります。
- 構成サーバーは、実行中で、接続状態である必要があります。
- 構成サーバーの一般的な管理タスクに関する[詳細を確認](vmware-azure-manage-configuration-server.md)してください。

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>構成サーバーを設定しているときに、MySQL を手動でダウンロードしてインストールできますか?

はい。 MySQL をダウンロードし、C:\Temp\ASRSetup フォルダーに配置します。 その後、手動でインストールします。 構成サーバー VM を設定し、ご契約条件に同意すると、MySQL は **[Download and install]\(ダウンロードとインストール\)** に **"インストール済み"** と表示されます。

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>MySQL をダウンロードせず、Site Recovery によってインストールすることはできますか?

はい。 MySQL インストーラーをダウンロードし、C:\Temp\ASRSetup フォルダーに配置します。 構成サーバーの VM を設定する際に、条件に同意し、 **[ダウンロードしてインストール]** を選択します。 ポータルでは、MySQL をインストールするために追加したインストーラーが使用されます。

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>構成サーバー VM を他の目的で使用することはできますか?

いいえ。 VM は構成サーバーにのみ使用します。

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>構成サーバーを複製してオーケストレーションに使用することはできますか?

いいえ。 登録に関する問題を避けるために、新しい構成サーバーを設定します。

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>構成サーバーが登録されているコンテナーを変更することはできますか?

いいえ。 コンテナーは、構成サーバーに関連付けられた後に変更することはできません。 構成サーバーを別のコンテナーに登録する方法を[ご覧ください](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault)。

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>VMware VM と物理サーバーの両方のディザスター リカバリーに同じ構成サーバーを使用できますか?

はい。ただし、物理マシンは VMware VM にのみフェールバックできることにご注意ください。

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>構成サーバーのパスフレーズはどこでダウンロードできますか?

パスフレーズをダウンロードする方法を[ご覧ください](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)。

### <a name="where-can-i-download-vault-registration-keys"></a>コンテナー登録キーはどこでダウンロードできますか?

Recovery Services コンテナーで、 **[Site Recovery インフラストラクチャ]**  >  **[管理]** の **[構成サーバー]** を選択します。 次に、 **[サーバー]** で **[登録キーのダウンロード]** を選択して、コンテナーの資格情報ファイルをダウンロードします。

## <a name="process-server"></a>プロセス サーバー

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>レプリケーションを有効にしたときに、プロセス サーバーを選択できないのはなぜですか?

バージョン 9.24 以降には、[レプリケーションを有効にしたときのプロセス サーバーの正常性](vmware-azure-enable-replication.md#enable-replication)が表示されるようになりました。 この機能は、プロセス サーバーのスロットリングを回避し、異常なプロセス サーバーの使用を最小限に抑える場合に役立ちます。

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>正確な正常性情報を得るためにバージョン 9.24 以降に更新するにはどうすればよいですか?

[バージョン 9.24](service-updates-how-to.md#links-to-currently-supported-update-rollups) 以降、プロセス サーバーの正常性を示すためにさらに多くのアラートが追加されました。 すべてのアラートが生成されるように、[Site Recovery コンポーネントをバージョン 9.24 以降に更新](service-updates-how-to.md#links-to-currently-supported-update-rollups)してください。

## <a name="failover-and-failback"></a>フェールオーバーとフェールバック

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>オンプレミスのプロセス サーバーをフェールバックに使用できますか?

データ転送の遅延を避けるために、Azure にフェールバック用のプロセス サーバーを作成することを強くお勧めします。 また、構成サーバーでソース VM のネットワークと Azure が接続されたネットワークを分離している場合、Azure 内に作成したプロセス サーバーをフェールバックで必ず使用する必要があります。

### <a name="can-i-keep-the-ip-address-on-failover"></a>フェールオーバーの際に IP アドレスを保持することはできますか?

はい、フェールオーバーの際に IP アドレスを保持することは可能です。 フェールオーバー前に VM の **[コンピューティングとネットワーク]** 設定でターゲット IP アドレスを必ず指定してください。 また、フェールバック中の IP アドレスの競合を避けるために、フェールオーバー時には必ずマシンをシャットダウンしてください。

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>フェールオーバー前にターゲットの VM のサイズまたは VM の種類を変更できますか?

はい、フェールオーバーの前に、いつでも VM の種類やサイズを変更できます。 ポータルで、レプリケートされた VM の **[コンピューティングとネットワーク]** 設定を使用します。

### <a name="how-far-back-can-i-recover"></a>過去のどの時点まで遡って復旧できますか?

VMware から Azure の場合、使うことができる最も古い復旧ポイントは 72 時間です。

### <a name="how-do-i-access-azure-vms-after-failover"></a>フェールオーバー後にはどのようにして Azure VM にアクセスできますか?

フェールオーバー後、Azure VM には、セキュリティで保護されたインターネット接続、サイト間 VPN、または Azure ExpressRoute 経由でアクセスできます。 接続するには、いくつかの準備が必要です。 [詳細情報](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)。

### <a name="is-failed-over-data-resilient"></a>フェールオーバーされたデータに回復力はありますか?

Azure は復元するように設計されています。 Site Recovery は、Azure サービスレベル アグリーメント (SLA) で要求されているように、セカンダリ Azure データセンターへのフェールオーバーが設計されています。 フェールオーバーが発生した場合、お客様のメタデータとコンテナーは、お客様が選択したコンテナーと同じリージョン内に保持されます。

### <a name="is-failover-automatic"></a>フェールオーバーは自動で行われますか。

[フェールオーバー](site-recovery-failover.md)は自動では行われません。 ポータルで単一の選択を行うだけでフェールオーバーを開始できます。または [PowerShell](/powershell/module/az.recoveryservices) を使ってフェールオーバーをトリガーすることもできます。

### <a name="can-i-fail-back-to-a-different-location"></a>異なる場所にフェールバックすることはできますか?

はい。 Azure にフェールオーバーした場合、元の場所が利用できないときは、別の場所にフェールバックできます。 [詳細情報](concepts-types-of-failback.md#alternate-location-recovery-alr)。

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>フェールバックに、プライベート ピアリングを使用した VPN または ExpressRoute が必要なのはなぜですか?

Azure からフェールバックするときは、Azure からオンプレミスの VM にデータがコピーされるので、プライベート アクセスが必要です。

### <a name="can-i-resize-the-azure-vm-after-failover"></a>フェールオーバー後に Azure VM をサイズ変更することはできますか?

いいえ、フェールオーバー後にターゲット VM のサイズや種類を変更することはできません。

## <a name="automation-and-scripting"></a>自動化とスクリプト

### <a name="can-i-set-up-replication-with-scripting"></a>スクリプトでレプリケーションを設定できますか?

はい。 Rest API、PowerShell、Azure SDK のいずれかを使用して、Site Recovery ワークフローを自動化することができます。 [詳細情報](vmware-azure-disaster-recovery-powershell.md)。

## <a name="performance-and-capacity"></a>パフォーマンスと容量

### <a name="can-i-throttle-replication-bandwidth"></a>レプリケーションの帯域幅を調整することができますか?

はい。 [詳細情報](site-recovery-plan-capacity-vmware.md)。

## <a name="next-steps"></a>次の手順

- サポート要件を[確認する](vmware-physical-azure-support-matrix.md)。
- VMware から Azure へのレプリケーションを[設定する](vmware-azure-tutorial.md)。
