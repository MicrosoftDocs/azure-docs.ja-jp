---
title: Azure Site Recovery を使用した VMware/物理ディザスター リカバリーのスケーリング
description: Azure Site Recovery を使用して多数のオンプレミスの VMware VM または物理サーバーの Azure へのディザスター リカバリーを設定する方法について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: cc87429f269fba5083b87e2c328f0e21de9707ff
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102454349"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>VMware VM/物理サーバーの大規模なディザスター リカバリーを設定する

この記事では、[Azure Site Recovery](site-recovery-overview.md) サービスを使用して、1000 を超える多数のオンプレミスの VMware VM または運用環境の物理サーバーの Azure へのディザスター リカバリーを設定する方法について説明します。


## <a name="define-your-bcdr-strategy"></a>BCDR 戦略を定義する

ビジネス継続性とディザスター リカバリー (BCDR) 戦略の一環として、ビジネス アプリとワークロードの目標復旧時点 (RPO) と目標復旧時間 (RTO) を定義します。 RTO は、継続性の問題を回避するために、ビジネス アプリやプロセスを復元して使用可能にしなければならない時間の長さとサービス レベルを測定します。
- Site Recovery は、VMware VM と物理サーバー用の継続的なレプリケーションと、RTO のための [SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) を備えています。
- VMware VM の大規模なディザスター リカバリーを計画し、必要な Azure リソースを把握する場合は、容量計算に使用される RTO 値を指定できます。


## <a name="best-practices"></a>ベスト プラクティス

大規模なディザスター リカバリーに関する一般的ないくつかのベスト プラクティス。 これらのベスト プラクティスについては、このドキュメントの次のセクションで詳しく説明します。

- **ターゲットの要件を特定する**:ディザスター リカバリーを設定する前に、Azure での容量とリソースのニーズを見積もります。
- **Site Recovery コンポーネントの計画を立てる**:見積もった容量に対応するために必要な Site Recovery コンポーネント (構成サーバー、プロセス サーバー) を把握します。
- **1 つまたは複数のスケールアウト プロセス サーバーを設定する**:構成サーバーで既定で実行されているプロセス サーバーは使用しないでください。 
- **最新の更新プログラムを実行する**:Site Recovery チームは定期的に新しいバージョンの Site Recovery コンポーネントをリリースしているため、最新バージョンが実行されていることを確認してください。 そのためには、更新プログラムの[新機能](site-recovery-whats-new.md)を確認し、リリースされた時点で[更新プログラムを有効にしてインストール](service-updates-how-to.md)してください。
- **予防的な監視**:ディザスター リカバリーを稼働させる場合は、レプリケーション対象のマシンと、インフラストラクチャのリソースの状態と正常性を、予防的に監視する必要があります。
- **ディザスター リカバリーの訓練**:定期的にディザスター リカバリーの訓練を実行する必要があります。 これらは運用環境には影響を与えませんが、Azure へのフェールオーバーが、必要なときに期待どおりに機能することを確認できます。



## <a name="gather-capacity-planning-information"></a>容量計画情報を収集する

ターゲット (Azure) の容量のニーズを評価して見積もるため、オンプレミス環境に関する情報を収集します。
- VMware の場合、これを行うには VMware VM のために Deployment Planner を実行します。
- 物理サーバーの場合は、手動で情報を収集します。

### <a name="run-the-deployment-planner-for-vmware-vms"></a>VMware VM 用の Deployment Planner を実行する

Deployment Planner は、VMware のオンプレミス環境に関する情報を収集するのに役立ちます。

- Deployment Planner は、VM で通常のチャーンを表す期間中に実行してください。 これにより、より正確な見積もりと推奨事項が生成されます。
- Deployment Planner ではそれが実行されているサーバーからのスループットが計算されるため、Planner は構成サーバー マシンで実行することをお勧めします。 スループットの測定については[こちら](site-recovery-vmware-deployment-planner-run.md#get-throughput)をご覧ください。
- まだ構成サーバーが設定されていない場合は、次の手順を実行します。
    - Site Recovery コンポーネントの[概要を取得](vmware-physical-azure-config-process-server-overview.md)します。
    - Deployment Planner を実行するために、[構成サーバーを設定](vmware-azure-deploy-configuration-server.md)します。

その後、次のように Planner を実行します。

1. Deployment Planner については[こちら](site-recovery-deployment-planner.md)をご覧ください。 最新バージョンをポータルからダウンロードすることも、[直接ダウンロード](https://aka.ms/asr-deployment-planner)することもできます。
2. Deployment Planner の[前提条件](site-recovery-deployment-planner.md#prerequisites)と[最新の更新プログラム](site-recovery-deployment-planner-history.md)を確認し、ツールを[ダウンロードして抽出](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool)します。
3. 構成サーバーで [Deployment Planner を実行](site-recovery-vmware-deployment-planner-run.md)します。
4. 見積もりと推奨事項を要約する[レポートを生成](site-recovery-vmware-deployment-planner-run.md#generate-report)します。
5. [レポートの推奨事項](site-recovery-vmware-deployment-planner-analyze-report.md)と[コストの見積もり](site-recovery-vmware-deployment-planner-cost-estimation.md)を分析します。

>[!NOTE]
> 既定では、このツールは最大で 1,000 台の VM をプロファイリングしてレポートを生成するよう構成されています。 この上限を変更するには、ASRDeploymentPlanner.exe.config ファイルの MaxVMsSupported キー値を大きくします。

## <a name="plan-target-azure-requirements-and-capacity"></a>ターゲット (Azure) の要件と容量を計画する

収集した見積もりと推奨事項を使用して、ターゲット リソースと容量を計画することができます。 VMware VM 用の Deployment Planner を実行した場合は、多数の[レポートの推奨事項](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations)を使用すると役に立ちます。

- **Compatible VMs (適合 VM)** :この数を使用して、Azure へのディザスター リカバリーの準備ができている VM の数を特定します。 ネットワーク帯域幅、Azure コアに関する推奨事項は、この数に基づいています。
- **必要なネットワーク帯域幅**:適合 VM の差分レプリケーションに必要な帯域幅に注意してください。 
    - Planner を実行するときは、目的の RPO を分単位で指定します。 推奨事項は、その RPO を 100% と 90% の時間満たすために必要な帯域幅を示しています。 
    - ネットワーク帯域幅に関する推奨事項は、Planner で推奨される構成サーバーとプロセス サーバーの総数に対して必要な帯域幅を考慮に入れます。
- **必要な Azure コア数**:適合 VM の数に基づいて、ターゲットの Azure リージョンで必要なコア数を確認してください。 十分なコアがないと、フェールオーバー時に Site Recovery で必要な Azure VM を作成できなくなります。
- **推奨 VM バッチ サイズ**:推奨バッチ サイズは、100% の RPO を満たしながら、既定値の 72 時間以内にバッチの初回レプリケーションを完了する機能に基づいています。 時間の値は変更可能です。

これらの推奨事項を使用して、Azure リソース、ネットワーク帯域幅、および VM のバッチ処理を計画することができます。

## <a name="plan-azure-subscriptions-and-quotas"></a>Azure のサブスクリプションとクォータを計画する

ターゲット サブスクリプションで使用可能なクォータが、フェールオーバーを処理するのに十分であることを確認する必要があります。

**タスク** | **詳細** | **操作**
--- | --- | ---
**コア数を確認する** | フェールオーバーの時点で使用可能なクォータ内のコア数がターゲットの総数以上でない場合、フェールオーバーは失敗します。 | VMware VM の場合は、Deployment Planner のコアの推奨事項を満たすために、ターゲット サブスクリプションに十分なコアがあることを確認してください。<br/><br/> 物理サーバーの場合は、Azure コア数が、手動での見積もりを満たすことを確認してください。<br/><br/> クォータを確認するには、Azure portal > **[サブスクリプション]** で **[使用量 + クォータ]** をクリックします。<br/><br/> クォータの増加については[こちら](../azure-portal/supportability/resource-manager-core-quotas-request.md)をご覧ください。
**フェールオーバーの上限を確認する** | フェールオーバーの数は、Site Recovery のフェールオーバーの上限を超えてはいけません。 |  フェールオーバーが上限を超える場合、サブスクリプションを追加して複数のサブスクリプションにフェールオーバーするか、サブスクリプションのクォータを増やすことができます。 


### <a name="failover-limits"></a>フェールオーバーの上限

この上限は、コンピューターごとに 3 つのディスクがある場合に、1 時間以内に Site Recovery によってサポートされるフェールオーバーの数を示します。

準拠が意味すること Azure VM を起動するには、Azure で、いくつかのドライバーがブート開始状態であり、DHCP などのサービスが自動開始状態である必要があります。
- 準拠するマシンには、あらかじめこれらの設定が適切に組み込まれます。
- Windows を実行しているマシンでは、事前にコンプライアンスを確認し、必要に応じて準拠させることができます。 [詳細については、こちらを参照してください](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010)。
- Linux マシンは、フェールオーバーの時点でのみ準拠の状態になります。

**マシンが Azure に準拠しているかどうか** | **Azure VM の上限 (マネージド ディスクのフェールオーバー)**
--- | --- 
はい | 2000
いいえ | 1000

- 上限は、サブスクリプションのターゲット リージョンで進行中のその他のジョブが最小限であることを前提としています。
- 一部の Azure リージョンはより小さく、上限が若干低い場合があります。

## <a name="plan-infrastructure-and-vm-connectivity"></a>インフラストラクチャと VM の接続を計画する

Azure へのフェールオーバー後は、オンプレミスと同様に動作するワークロードと、ユーザーが Azure VM で実行されているワークロードにアクセスできるようにするワークロードが必要です。

- Azure への Active Directory または DNS のオンプレミスのインフラストラクチャのフェールオーバーについては、[こちら](site-recovery-active-directory.md#test-failover-considerations)をご覧ください。
- フェールオーバー後の Azure VM への接続の準備については、[こちら](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)をご覧ください。



## <a name="plan-for-source-capacity-and-requirements"></a>ソースの容量と要件を計画する

容量に関する要件に対応するには、十分な構成サーバーとスケールアウト プロセス サーバーがあることが重要です。 大規模なデプロイを開始する場合は、最初に 1 つの構成サーバーと、1 つのスケールアウト プロセス サーバーから開始します。 規定されている上限に達したらと、追加のサーバーを追加します。

>[!NOTE]
> VMware VM の場合は、Deployment Planner により、必要な構成サーバーとプロセス サーバーが提案されます。 Deployment Planner の提案に従うのではなく、次の手順に含まれる表を利用することをお勧めします。 


## <a name="set-up-a-configuration-server"></a>構成サーバーを設定する
 
構成サーバーの容量は、データのチャーン レートではなく、レプリケートするマシンの数の影響を受けます。 追加の構成サーバーが必要かどうかを確認するには、以下の定義済みの VM の制限を利用してください。

**CPU** | **[メモリ]** | **キャッシュ ディスク** | **レプリケーション対象のマシンの制限**
 --- | --- | --- | ---
8 vCPU<br> 2 ソケット * 4 コア @ 2.5 GHz | 16 GB | 600 GB | 最大 550 台のマシン<br> 各ソース マシンは、それぞれ 100 GB のディスクを 3 個備えていることを前提としています。

- これらの制限は、OVF テンプレートを使用して設定された構成サーバーに基づいています。
- この制限は、構成サーバーで既定で実行されているプロセス サーバーは使用していないことを前提としています。

新しい構成サーバーを追加する必要がある場合は、以下の手順を実行します。

- OVF テンプレートを使用して、VMware VM のディザスター リカバリー用の[構成サーバーを設定します](vmware-azure-deploy-configuration-server.md)。
- 物理サーバーの場合、または OVF テンプレートを使用できない VMware デプロイの場合は、手動で[構成サーバーを設定します](physical-azure-set-up-source.md)。

構成サーバーを設定する際は、次の点に注意してください。

- 構成サーバーを設定する際は、配置先のサブスクリプションとコンテナーを考慮することが重要です。これらは設定後に変更することができないためです。 コンテナーを変更する必要がある場合は、コンテナーから構成サーバーの関連付けを解除してから、再度登録する必要があります。 これにより、コンテナー内の VM のレプリケーションが停止します。
- 複数のネットワーク アダプターのある構成サーバーを設定する場合は、設定中に行う必要があります。 これは、コンテナーに構成サーバーを登録した後では実行できません。

## <a name="set-up-a-process-server"></a>プロセス サーバーを設定する

プロセス サーバーの容量は、レプリケーションが有効になっているマシンの数ではなく、データのチャーン レートの影響を受けます。

- 大規模なデプロイの場合は、常に少なくとも 1 つのスケールアウト プロセス サーバーが必要です。
- 追加のサーバーが必要かどうかを確認するには、以下の表を利用してください。
- 最高性能のサーバーを追加することをお勧めします。 


**CPU** | **[メモリ]** | **キャッシュ ディスク** | **チャーン レート**
 --- | --- | --- | --- 
12 vCPU<br> 2 ソケット * 6 コア @ 2.5 GHz | 24 GB | 1 GB | 最大 2 TB/日

次のようにプロセス サーバーを設定します。

1. 前提条件については、[こちら](vmware-azure-set-up-process-server-scale.md#prerequisites)をご覧ください。
2. [ポータル](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)で、または[コマンドライン](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line)から、サーバーをインストールします。
3. 新しいサーバーを使用するように、レプリケーション対象のマシンを構成します。 レプリケートするマシンが既にある場合は、次のことが可能です。
    - プロセス サーバーのワークロード全体を新しいプロセス サーバーに[移動](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server)することができます。
    - または、特定の VM を新しいプロセス サーバーに[移動](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load)することができます。



## <a name="enable-large-scale-replication"></a>大規模なレプリケーションを有効にする

容量を計画し、必要なコンポーネントとインフラストラクチャをデプロイしたら、多数の VM のレプリケーションを有効にします。

1. マシンをバッチに振り分けます。 1 つのバッチ内の VM のレプリケーションを有効にしてから、次のバッチに進みます。

    - VMware VM の場合は、Deployment Planner レポートの[推奨 VM バッチ サイズ](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication)を使用することができます。
    - 物理マシンの場合は、サイズとデータの量が類似するマシンと、使用可能なネットワーク スループットに基づいて、バッチを識別することをお勧めします。 目的は、ほぼ同じ時間内に初回レプリケーションが終了する可能性のあるマシンをバッチ処理することです。
    
2. マシンのディスク変更頻度が高い場合、または Deployment Planner の制限を超えた場合は、レプリケートする必要のない重要でないファイル (ログのダンプや一時ファイルなど) をマシンから移動することができます。 VMware VM の場合は、これらのファイルを別のディスクに移動してから、レプリケーションから[そのディスクを除外する](vmware-azure-exclude-disk.md)ことができます。
3. レプリケーションを有効にする前に、マシンが[レプリケーション要件](vmware-physical-azure-support-matrix.md#replicated-machines)を満たしていることを確認します。
4. [VMware VM](vmware-azure-set-up-replication.md#create-a-policy) または[物理サーバー](physical-azure-disaster-recovery.md#create-a-replication-policy)のレプリケーション ポリシーを構成します。
5. [VMware VM](vmware-azure-enable-replication.md) または[物理サーバー](physical-azure-disaster-recovery.md#enable-replication)のレプリケーションを有効にします。 これにより、選択したマシンの初回レプリケーションが開始します。

## <a name="monitor-your-deployment"></a>デプロイを監視する

VM の最初のバッチのレプリケーションを開始したら、次のようにデプロイの監視を開始します。  

1. レプリケーション対象のマシンの正常性状態を監視するディザスター リカバリー管理者を割り当てます。
2. レプリケーション対象の項目とインフラストラクチャに関する[イベントを監視](site-recovery-monitor-and-troubleshoot.md)します。
3. スケールアウト プロセス サーバーの[正常性を監視](vmware-physical-azure-monitor-process-server.md)します。
4. 監視しやすくするために、サインアップしてイベントに関する[メール通知](./site-recovery-monitor-and-troubleshoot.md#subscribe-to-email-notifications)を取得します。
5. 定期的な[ディザスター リカバリー訓練](site-recovery-test-failover-to-azure.md)を実施して、すべてが予想どおりに動作することを確認します。


## <a name="plan-for-large-scale-failovers"></a>大規模なフェールオーバーを計画する

災害発生時には、多数のマシン/ワークロードを Azure にフェールオーバーする必要がある場合があります。 次のように、この種類のイベントに備えます。

次のように、フェールオーバーについて前もって準備することができます。

- フェールオーバー後にワークロードが使用可能になるように、また、ユーザーが Azure VM にアクセスできるように、[インフラストラクチャと VM を準備します](#plan-infrastructure-and-vm-connectivity)。
- このドキュメントの前述の「[フェールオーバーの上限](#failover-limits)」に注意してください。 フェールオーバーがこれらの上限の範囲内に収まるようにしてください。
- 定期的な[ディザスター リカバリー訓練](site-recovery-test-failover-to-azure.md)を行います。 訓練により以下のことができます。
    - フェールオーバー前のデプロイのギャップを見つける。
    - アプリのエンド ツー エンドの RTO を見積もる。
    - ワークロードのエンド ツー エンドの RPO を見積もる。
    - IP アドレス範囲の競合を識別する。
    - 訓練を行う場合は、訓練に運用ネットワークを使用せず、訓練の後に毎回テスト フェールオーバーをクリーンアップすることをお勧めします。

大規模なフェールオーバーを実行する場合は、次のようにすることをお勧めします。

1. ワークロードのフェールオーバーの復旧計画を作成します。
    - それぞれの復旧計画で、最大 100 台のマシンのフェールオーバーをトリガーできます。
    - [こちら](recovery-plan-overview.md) をご覧ください。
2. Azure での手動タスクを自動化するため、復旧計画に Azure Automation Runbook スクリプトを追加します。 一般的なタスクには、ロード バランサーの構成や DNS の更新などがあります。 [詳細情報](site-recovery-runbook-automation.md)
2. フェールオーバーの前に、Windows マシンが Azure 環境に準拠するように準備します。 準拠するマシンでは[フェールオーバーの上限](#plan-azure-subscriptions-and-quotas)が大きくなります。 Runbook については[こちら](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010)をご覧ください。
4.  復旧計画と共に、[Start-AzRecoveryServicesAsrPlannedFailoverJob](/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob) PowerShell コマンドレットを使用してフェールオーバーをトリガーします。



## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Site Recovery の監視](site-recovery-monitor-and-troubleshoot.md)
