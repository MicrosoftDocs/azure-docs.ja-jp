---
title: Azure Site Recovery を使用して VMware VM と物理サーバーを Azure にディザスター リカバリーする場合のレプリケーションの問題のトラブルシューティング | Microsoft Docs
description: この記事では、Azure Site Recovery を使用して VMware VM と物理サーバーを Azure にディザスター リカバリーする際の一般的なレプリケーションの問題のトラブルシューティングについて説明します。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 06/27/2019
ms.author: mayg
ms.openlocfilehash: c005dcee78e2a9338dc7a816e06d9a78a2f355b6
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491684"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>VMware VM および物理サーバーのレプリケーション問題のトラブルシューティング

この記事では、オンプレミス VMware VM および物理サーバーを [Site Recovery](site-recovery-overview.md) を使用して Azure にレプリケートする場合に発生する可能性がある、いくつかの一般的な問題と特定のエラーについて説明します。

## <a name="step-1-monitor-process-server-health"></a>手順 1:プロセス サーバーの正常性を監視する

Site Recovery は[プロセス サーバー](vmware-physical-azure-config-process-server-overview.md#process-server)を使用して、レプリケートされたデータを受信して最適化し、それを Azure に送信します。

ポータルでプロセス サーバーの正常性を監視し、それらが正しく接続されて機能しており、レプリケーションがプロセス サーバーに関連付けられているソース マシンに対して進行していることを確認するようにお勧めします。

- プロセス サーバーの監視についての[説明をご覧ください](vmware-physical-azure-monitor-process-server.md)。
- [ベスト プラクティスを確認する](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- プロセス サーバーの正常性を[トラブルシューティングします](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health)。

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>手順 2:接続性とレプリケーションの問題をトラブルシューティングする

初期や継続的に発生するレプリケーションの問題は、ソース サーバーとプロセス サーバーの間、またはプロセス サーバーと Azure の間の接続に関する問題が原因であることが少なくありません。 

これらの問題を解決するために、[接続性とレプリケーションのトラブルシューティングをします](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication)。




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>手順 3:レプリケーションに使用できないソース マシンをトラブルシューティングする

Site Recovery を使用してレプリケーションを有効にするソース マシンを選択しようとした場合、次のいずれかの理由から、そのマシンが使用できないことがあります。

* **2 つの仮想マシンのインスタンス UUID が同じ**:vCenter で管理されている仮想マシンの 2 つが同じインスタンス UUID を持つ場合は、構成サーバーで最初に検出された方の仮想マシンが Azure portal に表示されます。 この問題を解決するには、インスタンス UUID が同じ仮想マシンが 2 つ存在しないようにします。 このシナリオは、バックアップ VM がアクティブになり、探索レコードにログインしているインスタンスでよく見られます。 解決するには、「[Azure Site Recovery VMware-to-Azure:How to clean up duplicate or stale entries (Azure Site Recovery (VMware から Azure へ): 重複エントリまたは古いエントリのクリーンアップ方法)](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)」を参照してください。
* **vCenter ユーザーの資格情報が正しくない**:OVF テンプレートまたは統合設定を使用して構成サーバーを設定するときに追加した vCenter 資格情報が正しいことを確認します。 設定時に追加した資格情報を確認するには、[自動検出のための資格情報の変更に関する記事](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery)を参照してください。
* **vCenter の特権が不足している**:vCenter にアクセスするために提供されたアクセス許可に、必要なアクセス許可がない場合は、仮想マシンを検出できない場合があります。 「[Prepare an account for automatic discovery (アカウントを自動検出のために準備する)](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)」に記載されているアクセス許可が vCenter ユーザー アカウントに追加されていることを確認します。
* **Azure Site Recovery の管理サーバー**:仮想マシンが、構成サーバー/スケールアウト プロセス サーバー/マスター ターゲット サーバー のいずれかまたは複数のロールを持つ管理サーバーとして使用されている場合、ポータルからその仮想マシンを選択することはできません。 管理サーバーはレプリケートできません。
* **Azure Site Recovery サービスによって既に保護されている、またはフェールオーバーされている**:仮想マシンが Site Recovery によって既に保護されているかフェールオーバーされている場合、その仮想マシンは、ポータル内で保護対象として選択することはできません。 ポータル内で探している仮想マシンが他のユーザーや別のサブスクリプションでまだ保護されていないことを確認します。
* **vCenter に接続されていない**:vCenter が接続状態であることを確認します。 確認するには、[Recovery Services コンテナー]、[Site Recovery インフラストラクチャ]、[構成サーバー] の順に移動し、該当する構成サーバーをクリックします。右側にブレードが開き、関連付けられているサーバーの詳細が表示されます。 vCenter が接続されているがどうかをチェックします。 「未接続」状態である場合は、問題を解決し、ポータルで[構成サーバーを更新](vmware-azure-manage-configuration-server.md#refresh-configuration-server)します。 その後、仮想マシンがポータルに表示されます。
* **ESXi の電源がオフになっている**:仮想マシンが存在する ESXi ホストの電源がオフ状態の場合、 Azure portal にその仮想マシンは表示されないか、Azure portal で選択することはできません。 ESXi ホストの電源をオンにし、ポータルで[構成サーバーを更新](vmware-azure-manage-configuration-server.md#refresh-configuration-server)します。 その後、仮想マシンがポータルに表示されます。
* **保留中の再起動**:仮想マシンの保留中の再起動がある場合、Azure portal でそのマシンを選択することはできません。 保留中の再起動アクティビティが完了していることを確認し、[構成サーバーを更新](vmware-azure-manage-configuration-server.md#refresh-configuration-server)します。 その後、仮想マシンがポータルに表示されます。
* **IP が見つからない**:仮想マシンに関連付けられている有効な IP アドレスがない場合、Azure portal でそのマシンを選択することはできません。 有効な IP アドレスが仮想マシンに割り当てられていることを確認し、[構成サーバーを更新](vmware-azure-manage-configuration-server.md#refresh-configuration-server)します。 その後、仮想マシンがポータルに表示されます。

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>ポータルでグレー表示されている保護された仮想マシンのトラブルシューティング

Site Recovery でレプリケートされる仮想マシンは、システム内に重複したエントリが存在する場合、Azure portal 内で使用できません。 古いエントリを削除してこの問題を解決する方法については、「[Azure Site Recovery VMware-to-Azure:How to clean up duplicate or stale entries (Azure Site Recovery (VMware から Azure へ): 重複エントリまたは古いエントリのクリーンアップ方法)](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)」を参照してください。

## <a name="common-errors-and-solutions"></a>一般的なエラーと解決

### <a name="initial-replication-issues-error-78169"></a>初期レプリケーションの問題 [エラー 78169]

接続、帯域幅、時間同期に関係する問題がないことを確認するのに加えて、次の点を確認します。

- ウイルス対策ソフトウェアが Azure Site Recovery をブロックしていない。 Azure Site Recovery で必要なフォルダーの除外について、[詳細](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)を参照してください。

### <a name="missing-app-consistent-recovery-points-error-78144"></a>アプリケーション整合性復旧ポイントが見つからない [エラー 78144]

 これは、ボリューム シャドウ コピー サービス (VSS) に関連した問題が原因で発生します。 解決するには、以下を行います。 
 
- インストールされている Azure Site Recovery エージェントのバージョンが、9.22.2 以上であることを確認します。 
- VSS プロバイダーが Windows サービスのサービスとしてインストールされていることを確認します。また、コンポーネント サービス MMC を確認して、Azure Site Recovery VSS プロバイダーが一覧にあることを確認します。
- VSS プロバイダーがインストールされていない場合は、[インストール エラーのトラブルシューティング記事](vmware-azure-troubleshoot-push-install.md#vss-installation-failures)を参照してください。

- VSS が無効になっている場合は、
    - VSS プロバイダー サービスのスタートアップの種類が **[自動]** に設定されていることを確認します。
    - 次のサービスを再起動します。
        - VSS サービス
        - Azure Site Recovery VSS プロバイダー
        - VDS サービス

- SQL または Exchange ワークロードを実行している場合は、これらのアプリケーション ライターのログでエラーを確認します。 よく発生するエラーとその解決策については、以下の記事に記載されています。
    -  [SQL Server データベースの AUTO-CLOSE オプションが TRUE に設定されている](https://support.microsoft.com/help/4504104)
    - [SQL Server 2008 R2 で再試行できないエラーがスローされる](https://support.microsoft.com/help/4504103)
    - [SQL Server 2016 と 2017 の既知の問題](https://support.microsoft.com/help/4493364)
    - [Exchange Servers 2013 と 2016 の一般的な問題](https://support.microsoft.com/help/4037535)


### <a name="source-machines-with-high-churn-error-78188"></a>ソース マシンのチャーン レートが高い [エラー 78188]

考えられる原因:
- 仮想マシンの一覧上のディスクでのデータ変化率 (書き込みバイト/秒) が、レプリケーション ターゲットのストレージ アカウントの種類に対して [Azure Site Recovery がサポートしている上限](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits)を超えている。
- チャーン レートが突然急増した結果、大量のデータのアップロードが保留になっている。

この問題を解決するには:
- ターゲットのストレージ アカウントの種類 (Standard または Premium) が、ソースのチャーン レート要件に従ってプロビジョニングされていることを確認します。
- 観察されたチャーンが一時的なものである場合は、保留中のデータ アップロード処理が進行し復旧ポイントが作成されるまで、数時間待機します。
- 問題が解決しない場合は、レプリケーションの計画に役立つ Site Recovery [デプロイ プランナー](site-recovery-deployment-planner.md#overview)を使用します。

### <a name="source-machines-with-no-heartbeat-error-78174"></a>ソース マシンにハートビートがない [エラー 78174]

これは、ソース マシンの Azure Site Recovery モビリティ エージェントが構成サーバー (CS) と通信していない場合に発生します。

この問題を解決するには、次の手順に従って、ソース VM から構成サーバーへのネットワーク接続を確認します。

1. ソース マシンが動作していることを確認します。
2. 管理者特権を持つアカウントを使用してソース マシンにサインインします。
3. 次のサービスが実行中であることを確認します。実行されていない場合は、サービスを再起動します。
   - Svagents (InMage Scout VX Agent)
   - InMage Scout アプリケーション サービス
4. ソース マシンで、エラーの詳細が保存されているログを確認します。

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="process-server-with-no-heartbeat-error-806"></a>プロセス サーバーにハートビートがない [error 806]
プロセス サーバー (PS) からハートビートがない場合は、次を確認します。
1. PS VM が稼働している。
2. エラーの詳細に関する PS の次のログを確認します。

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>マスター ターゲット サーバーにハートビートがない [エラー 78022]

これは、マスター ターゲットの Azure Site Recovery モビリティ エージェントが構成サーバーと通信していない場合に発生します。

この問題を解決するには、次の手順に従ってサービスの状態を確認します。

1. マスター ターゲット VM が動作していることを確認します。
2. 管理者特権を持つアカウントを使用してマスター ターゲット VM にサインインします。
    - svagents サービスが実行中であることを確認します。 実行中の場合は、サービスを再起動します。
    - エラーの詳細が保存されているログを確認します。
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log



## <a name="next-steps"></a>次の手順

さらにサポートが必要な場合は、[Azure Site Recovery フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)に質問を投稿してください。 弊社のアクティブなコミュニティを通じて、エンジニアがサポートいたします。
