---
title: Azure Migrate または Site Recovery を使用して Windows Server 2008 サーバーを Azure に移行する
description: この記事では、オンプレミスの Windows Server 2008 マシンを Azure に移行する方法について説明したうえで、Azure Migrate をお勧めします。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2020
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6cc0855d3a4540de780a566a4613b4dbc647cfc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92369490"
---
# <a name="migrate-servers-running-windows-server-2008-to-azure"></a>Windows Server 2008 を実行しているサーバーを Azure に移行する

このチュートリアルでは、Azure Site Recovery を使用して Windows Server 2008 または 2008 R2 を実行しているオンプレミスのサーバーを Azure に移行する方法を示します。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 移行対象のオンプレミス環境を準備します。
> * ターゲット環境を設定します。
> * レプリケーション ポリシーを設定します。
> * レプリケーションを有効にします。
> * すべてが想定どおりに動作していることを確認するためにテスト移行を実行します。
> * Azure にフェールオーバーして移行を完了します。

## <a name="migrate-with-azure-migrate"></a>Azure Migrate を使用した移行

マシンを Azure に移行する際は、[Azure Migrate](../migrate/migrate-services-overview.md) サービスを使用することをお勧めします。 

- Azure Migrate は、サーバーの移行専用に設計されています。
- Azure Migrate は、オンプレミスのマシンの検出、評価、および Azure への移行を行うための中心的なハブを提供します。 Azure Site Recovery は、移行でなくディザスター リカバリーのみに使用してください。
- Azure Migrate は、Windows Server 2008 を実行しているサーバーの移行をサポートしています。


## <a name="migrate-with-site-recovery"></a>Site Recovery を使用した移行

### <a name="supported-operating-systems"></a>サポートされるオペレーティング システム


|オペレーティング システム  | 環境  |
|---------|---------|
|Windows Server 2008 SP2 - 32 ビットおよび 64 ビット(IA-32 および x86-64)</br>- Standard</br>- Enterprise</br>- Datacenter   |     VMware VM、Hyper-V VM、および物理サーバー    |
|Windows Server 2008 R2 SP1 - 64 bit</br>- Standard</br>- Enterprise</br>- Datacenter     |     VMware VM、Hyper-V VM、および物理サーバー|

> [!WARNING]
> - Server Core を実行しているサーバーの移行は、サポートされていません。
> - 移行前に、最新の Service Pack と Windows 更新プログラムがインストールされていることを確認してください。


### <a name="prerequisites"></a>前提条件

開始する前に、[VMware と物理サーバーの移行](vmware-azure-architecture.md)や [Hyper-V 仮想マシンの移行](hyper-v-azure-architecture.md)のために Azure Site Recovery のアーキテクチャを確認することをお勧めします。 

Windows Server 2008 または Windows Server 2008 R2 を実行している Hyper-V 仮想マシンを移行するには、[オンプレミス マシンを Azure に移行する](migrate-tutorial-on-premises-azure.md)チュートリアルの手順に従います。

このチュートリアルの手順以外の部分では、オンプレミス VMware 仮想マシンと Windows Server 2008 または 2008 R2 を実行している物理サーバーを移行する方法を説明しています。
> [!TIP]
> VMware VM をエージェントレスで Azure に移行する方法については、 [ここをクリック](../migrate/tutorial-migrate-vmware.md)してください


### <a name="limitations-and-known-issues"></a>制限事項と既知の問題

- Windows Server 2008 SP2 サーバーの移行に使用される構成サーバー、追加のプロセス サーバー、およびモビリティ サービスは、Azure Site Recovery ソフトウェアのバージョン 9.19.0.0 以降を実行している必要があります。

- Windows Server 2008 SP2 を実行しているサーバーのレプリケーションでは、アプリケーション整合性復旧ポイントおよびマルチ VM 整合性機能はサポートされません。 Windows Server 2008 SP2 サーバーは、クラッシュ整合性復旧ポイントに移行する必要があります。 クラッシュ整合性復旧ポイントは既定で、5 分ごとに生成されます。 構成済みのアプリケーション整合性スナップショットの頻度を備えたレプリケーション ポリシーを使用すると、アプリケーション整合性復旧ポイントの不足が原因で、レプリケーションの正常性が致命的な状態に変わります。 誤検知を避けるために、レプリケーション ポリシーのアプリケーション整合性スナップショットの頻度を "オフ" に設定します。

- 移行されるサーバーは、モビリティ サービスが動作するように .NET Framework 3.5 Service Pack 1 を備えている必要があります。

- サーバーに動的ディスクがある場合、特定の構成では、フェイルオーバーされたサーバー上のこれらのディスクはオフラインとしてマークされ、形式の異なるディスクとして表示されることが確認できます。 また、複数の動的ディスクにわたるミラー ボリュームのミラー化設定ステータスは、"Failed redundancy"\(冗長に失敗しました\) とマークされます。 これらのディスクを手動でインポートして再アクティブ化することで、diskmgmt.msc からこの問題を修正できます。

- 移行されるサーバーは、vmstorfl.sys ドライバーを備えている必要があります。 移行されるサーバーにドライバーが存在しない場合、フェールオーバーに失敗することがあります。 
  > [!TIP]
  >ドライバーが "C:\Windows\system32\drivers\vmstorfl.sys" にあるかどうかを確認します。 ドライバーが見つからない場合は、適切な場所にダミー ファイルを作成することで、問題を回避できます。 
  >
  > コマンド プロンプトを開き ([実行] > [cmd])、"copy nul c:\Windows\system32\drivers\vmstorfl.sys" を実行します。

- フェールオーバーされた後、または Azure へのフェール オーバーをテストした後に、32 ビットのオペレーティング システムを実行している Windows Server 2008 SP2 サーバーに対して、すぐに RDP できない場合があります。 Azure Portal からフェールオーバーした仮想マシンを再起動し、接続を再試行します。 まだ接続できない場合は、リモート デスクトップ接続を許可するようにサーバーが構成されているかどうかをチェックし、接続をブロックするファイアウォール ルールまたはネットワーク セキュリティ グループがないことを確認します。 
  > [!TIP]
  > サーバーを移行する前に、テスト フェールオーバーを行うことを強くお勧めします。 移行中の各サーバー上で少なくとも 1 回、正常にテスト フェールオーバーを実行したことを確認します。 テスト フェールオーバーの一環として、テスト フェールオーバーのマシンに接続して、期待通りに動作することを確認します。
  >
  >テスト フェールオーバー操作は中断がなく、選択した分離ネットワークに仮想マシンを作成することで、移行のテストを支援します。 フェールオーバー操作とは異なり、テスト フェールオーバー操作中は、データ レプリケーションは進行を続けます。 移行の準備が整う前に、好きな回数だけ、テスト フェールオーバーを実行できます。 
  >
  


### <a name="get-started"></a>はじめに

次のタスクを実行して、Azure サブスクリプションおよびオンプレミス VMware/物理環境を準備します。

1. [Azure を準備する](tutorial-prepare-azure.md)
2. オンプレミスの [VMware](vmware-azure-tutorial-prepare-on-premises.md) を準備する


### <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

1. [Azure Portal](https://portal.azure.com) > **Recovery Services** にサインインします。
2. **[リソースの作成]**  >  **[管理ツール]**  >  **[バックアップおよびサイトの回復]** の順にクリックします。
3. **[名前]** に、フレンドリ名 **W2K8-migration** を指定します。 複数のサブスクリプションがある場合は、適切なものを選択します。
4. リソース グループ **w2k8migrate** を作成します。
5. Azure リージョンを指定します。 サポートされているリージョンを確認するには、[Azure Site Recovery の価格の詳細](https://azure.microsoft.com/pricing/details/site-recovery/)に関するページでご利用可能な地域をご覧ください。
6. ダッシュボードからコンテナーにすばやくアクセスするには、 **[ダッシュボードにピン留めする]** 、 **[作成]** の順にクリックします。

   ![新しいコンテナーの作成オプションを示すスクリーンショット。](media/migrate-tutorial-windows-server-2008/migrate-windows-server-2008-vault.png)

新しいコンテナーは、 **[ダッシュボード]** の **[すべてのリソース]** と、メインの **[Recovery Services コンテナー]** ページに追加されます。


### <a name="prepare-your-on-premises-environment-for-migration"></a>移行対象のオンプレミス環境を準備する

- VMware で実行されている Windows Server 2008 の仮想マシンを移行するには、[VMware にオンプレミス構成サーバーをセットアップします](vmware-azure-tutorial.md#set-up-the-source-environment)。
- 構成サーバーを VMware 仮想マシンとしてセットアップできない場合は、[オンプレミス物理サーバーまたは仮想マシンに構成サーバーをセットアップします](physical-azure-disaster-recovery.md#set-up-the-source-environment)。

### <a name="set-up-the-target-environment"></a>ターゲット環境をセットアップする

ターゲット リソースを選択して確認します。

1. **[インフラストラクチャの準備]**  >  **[ターゲット]** の順にクリックし、使用する Azure サブスクリプションを選択します。
2. Resource Manager デプロイ モデルを指定します。
3. Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが 1 つ以上あるかどうかが確認されます。


### <a name="set-up-a-replication-policy"></a>レプリケーション ポリシーを設定する

1. 新しいレプリケーション ポリシーを作成するには、 **[Site Recovery インフラストラクチャ]**  >  **[レプリケーション ポリシー]**  >  **[+ レプリケーション ポリシー]** の順にクリックします。
2. **[レプリケーション ポリシーの作成]** で、ポリシー名を指定します。
3. **[RPO しきい値]** で、復旧ポイントの目標 (RPO) の上限を指定します。 レプリケーション RPO がこの制限を超えると、アラートが生成されます。
4. **[復旧ポイントの保持期間]** で、各復旧ポイントのリテンション期間の長さ (時間単位) を指定します。 レプリケートされたサーバーは、この期間内の任意の時点に復旧できます。 Premium Storage にレプリケートされたマシンでは最大 24 時間のリテンション期間がサポートされ、Standard Storage の場合は 72 時間です。
5. **[アプリ整合性スナップショットの頻度]** で、 **[オフ]** を指定します。 **[OK]** をクリックしてポリシーを作成します。

このポリシーは自動的に構成サーバーに関連付けられます。

> [!WARNING]
> レプリケーション ポリシーのアプリ整合性スナップショットの頻度の設定で、 **[オフ]** を指定していることを確認してください。 Windows Server 2008 を実行しているサーバーのレプリケート時は、クラッシュ整合性復旧ポイントだけがサポートされます。 アプリ整合性スナップショットの頻度にその他の値を指定すると、アプリケーション整合性復旧ポイントの不足が原因で、サーバーのレプリケーションの正常性が致命的な状態に変わり、エラーのアラートが発生します。

   ![レプリケーション ポリシー作成のオプションを示すスクリーンショット。](media/migrate-tutorial-windows-server-2008/create-policy.png)

### <a name="enable-replication"></a>レプリケーションを有効にする

Windows Server 2008 SP2 / Windows Server 2008 R2 SP1 サーバーが移行されるように、[レプリケーションを有効にします](physical-azure-disaster-recovery.md#enable-replication)。
   
   ![物理コンピューターを追加するためのオプションを示すスクリーンショット。](media/migrate-tutorial-windows-server-2008/Add-physical-server.png)

   ![レプリケーションを有効にするためのオプションを示すスクリーンショット。](media/migrate-tutorial-windows-server-2008/Enable-replication.png)

### <a name="run-a-test-migration"></a>テスト移行を実行する

初期レプリケーションが完了し、サーバーのステータスが **[保護済み]** に変わった後、サーバーのレプリケーションのテスト フェールオーバーを実行できます。

Azure への[テスト フェールオーバー](tutorial-dr-drill-azure.md)を実行して、すべて想定どおりに動作していることを確認します。

   ![テスト フェールオーバー コマンドを示すスクリーンショット。](media/migrate-tutorial-windows-server-2008/testfailover.png)


### <a name="migrate-to-azure"></a>Azure への移行

移行するマシンのフェールオーバーを実行します。

1. **[設定]**  >  **[レプリケートされたアイテム]** で、[マシン] > **[フェールオーバー]** をクリックします。
2. **[フェールオーバー]** で、フェールオーバーする **[復旧ポイント]** を選択します。 最新の復旧ポイントを選択します。
3. **[フェールオーバーを開始する前にマシンをシャットダウンします]** を選択します。 Site Recovery は、フェールオーバーを開始する前にサーバーをシャットダウンしようとします。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。 フェールオーバーの進行状況は **[ジョブ]** ページで確認できます。
4. 想定どおりに Azure VM が Azure に表示されることを確認します。
5. **[レプリケートされたアイテム]** でサーバーを右クリックし、 **[移行の完了]** を選択します。 次の処理が実行されます。

    - 移行プロセスが終了し、サーバーのレプリケーションが停止して、そのサーバーでの Site Recovery の課金が停止します。
    - この手順でレプリケーション データがクリーンアップされます。 移行した VM は削除されません。

   ![[移行の完了] コマンドを示すスクリーンショット。](media/migrate-tutorial-windows-server-2008/complete-migration.png)


> [!WARNING]
> **進行中のフェールオーバーを取り消さないでください**: フェールオーバーが開始される前に、サーバーのレプリケーションが停止されます。 進行中のフェールオーバーを取り消すと、フェールオーバーは停止しますが、サーバーによるレプリケーションが続行されなくなります。

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> Azure Migrate について[よく寄せられる質問を確認](../migrate/resources-faq.md)する。