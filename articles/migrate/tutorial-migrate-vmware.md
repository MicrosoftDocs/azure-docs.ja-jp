---
title: Azure Migrate Server Migration を使用して VMware VM のエージェントレス移行を実行する
description: Azure Migrate を使用して VMware VM のエージェントレス移行を実行する方法について説明します。
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: mvc
ms.openlocfilehash: bebc2e629193944c840948c9c573462a43e3032e
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201700"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>VMware VM を Azure に移行する (エージェントレス)

この記事では、[Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) ツールを使用して、エージェントレス移行でオンプレミスの VMware VM を Azure に移行する方法について説明します。 エージェントベース移行を使用して VMware VM を移行することもできます。 方法を[比較してください](server-migrate-overview.md#compare-migration-methods)。

これは、VMware VM を評価して Azure に移行する方法を示すシリーズの 3 番目のチュートリアルです。 

> [!NOTE]
> チュートリアルでは、概念実証をすばやく設定できるように、シナリオの最も簡単なデプロイ パスを示します。 チュートリアルではできるだけ既定のオプションを使用しており、使用可能な設定とパスをすべて示しているわけではありません。 


このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Migrate:Server Migration ツールを追加します。
> * 移行したい VM を検出します。
> * VM のレプリケートを開始します。
> * すべてが想定どおりに動作していることを確認するためにテスト移行を実行します。
> * 完全な VM 移行を実行します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを始める前に、次の準備が必要です。

1. [最初のチュートリアルを完了](./tutorial-discover-vmware.md)して、移行のために Azure と VMware を準備します。
2. 2 番目のチュートリアルを完了して、Azure に移行する前に [VMware VM を評価](./tutorial-assess-vmware-azure-vm.md)しておくことをお勧めしますが、必須ではありません。 
3. 既に作成されているプロジェクトに移動するか、[新しいプロジェクトを作成](./create-manage-projects.md)します。
4. ご使用の Azure アカウントのアクセス許可を確認します。Azure アカウントには、VM を作成し、Azure マネージド ディスクに書き込むためのアクセス許可が必要です。

## <a name="set-up-the-azure-migrate-appliance"></a>Azure Migrate アプライアンスを設定する

Azure Migrate Server Migration は、VMware VM の検出、評価、エージェントレス移行に使用される軽量の VMware VM アプライアンスを実行します。 [評価のチュートリアル](./tutorial-assess-vmware-azure-vm.md)に従っている場合、アプライアンスは既に設定されています。 まだ設定していない場合は、次のいずれかの方法を使用して設定します。

- **OVA テンプレート**:ダウンロードした OVA テンプレートを使用して VMware VM 上に [設定](how-to-set-up-appliance-vmware.md)します。
- **Script**: PowerShell インストーラ スクリプトを使用して VMware VM 上または物理マシン上に [設定](deploy-appliance-script.md)します。 OVA テンプレートを使用して VM を設定できない場合や、Azure Government をご利用の場合は、この方法を使用してください。

アプライアンスの作成後、Azure Migrate:Server Assessment に接続できることを確認し、最初の構成を行い、Azure Migrate プロジェクトに登録します。

## <a name="replicate-vms"></a>VM をレプリケートする

アプライアンスを設定して検出を完了すると、Azure への VMware VM のレプリケーションを開始できます。 

- 最大 500 件のレプリケーションを同時に実行できます。
- ポータルでは、移行の対象として一度に最大 10 台の VM を選択できます。 より多くのマシンを移行するには、10 台のバッチでグループに追加します。

レプリケーションを有効にするには、次の手順に従います。

1. Azure Migrate プロジェクトの **[サーバー]** の **[Azure Migrate: Server Migration]** で、 **[レプリケート]** をクリックします。

    ![VM をレプリケートする](./media/tutorial-migrate-vmware/select-replicate.png)

2. **[レプリケート]** > **[ソースの設定]**  >  **[マシンは仮想化されていますか?]** で、 **[はい (VMware vSphere の場合)]** を選択します。
3. **[オンプレミスのアプライアンス]** で、自分が設定した Azure Migrate アプライアンスの名前、 **[OK]** の順に選択します。 

    ![ソースの設定](./media/tutorial-migrate-vmware/source-settings.png)

4. **[仮想マシン]** で、レプリケートしたいマシンを選択します。 評価 (実行している場合) から VM のサイズ設定とディスクの種類を適用するには、 **[Azure Migrate Assessment から移行設定をインポートしますか?]** で **[はい]** を選択し、VM グループと評価名を選択します。 評価の設定を使用していない場合は **[いいえ]** を選択します。
   
    ![評価の選択](./media/tutorial-migrate-vmware/select-assessment.png)

5. **[仮想マシン]** で、移行する VM を選択します。 その後、 **[次へ:ターゲット設定]** をクリックします。

    ![VM を選択する](./media/tutorial-migrate-vmware/select-vms.png)

6. **[ターゲット設定]** で、サブスクリプションとターゲット リージョンを選択します。 移行後に Azure VM が属するリソース グループを指定します。
7. **[仮想ネットワーク]** で、移行後に Azure VM が参加する Azure VNet およびサブネットを選択します。
8. **[可用性オプション]** で、以下を選択します。
    -  可用性ゾーン。移行されたマシンをリージョン内の特定の可用性ゾーンにピン留めします。 このオプションを使用して、複数ノードのアプリケーション層を形成するサーバーを可用性ゾーン間で分散させます。 このオプションを選択した場合は、[コンピューティング] タブで選択した各マシンに使用する可用性ゾーンを指定する必要があります。このオプションは、移行用に選択したターゲット リージョンで Availability Zones がサポートされている場合にのみ使用できます。
    -  可用性セット。移行されたマシンを可用性セットに配置します。 このオプションを使用するには、選択されたターゲット リソース グループに 1 つ以上の可用性セットが必要です。
    - [インフラストラクチャ冗長は必要ありません] オプション (移行されたマシンに対してこれらの可用性構成がいずれも不要な場合)。
9. **[Disk encryption type]\(ディスク暗号化の種類\)** で、以下を選択します。
    - プラットフォーム マネージド キーを使用した保存時の暗号化
    - カスタマー マネージド キーを使用した保存時の暗号化
    - プラットフォーム マネージド キーとカスタマー マネージド キーを使用した二重暗号化

   > [!NOTE]
   > CMK を使用して VM をレプリケートするには、ターゲット リソース グループに[ディスク暗号化セットを作成する](https://go.microsoft.com/fwlink/?linkid=2151800)必要があります。 ディスク暗号化セット オブジェクトによって、SSE に使用する CMK を含む Key Vault にマネージド ディスクがマップされます。
  
10. **[Azure ハイブリッド特典]** で、

    - Azure ハイブリッド特典を適用しない場合は、 **[いいえ]** を選択します。 続けて、 **[次へ]** をクリックします。
    - アクティブなソフトウェア アシュアランスまたは Windows Server のサブスクリプションの対象となっている Windows Server マシンがあり、移行中のマシンにその特典を適用する場合は、 **[はい]** を選択します。 続けて、 **[次へ]** をクリックします。

    ![ターゲットの設定](./media/tutorial-migrate-vmware/target-settings.png)

11. **[コンピューティング]** で、VM の名前、サイズ、OS ディスクの種類、および可用性構成 (前の手順で選択した場合) を確認します。 VM は [Azure の要件](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)に準拠している必要があります。

    - **VM サイズ**: 評価の推奨事項を使用している場合は、[VM サイズ] ドロップダウンに推奨サイズが表示されます。 それ以外の場合は、Azure Migrate によって、Azure サブスクリプション内の最も近いサイズが選択されます。 または、 **[Azure VM サイズ]** でサイズを手動で選択します。 
    - **OS ディスク**:VM の OS (ブート) ディスクを指定します。 OS ディスクは、オペレーティング システムのブートローダーとインストーラーがあるディスクです。 
    - **可用性ゾーン**:使用する可用性ゾーンを指定します。
    - **可用性セット**:使用する可用性セットを指定します。

    > [!NOTE]
    > 仮想マシンのセットごとに別の可用性オプションを選択する場合は、手順 1 に進み、仮想マシンの 1 つのセットのレプリケーションを開始した後に別の可用性オプションを選択して各手順を繰り返します。




12. **[ディスク]** で、VM ディスクを Azure にレプリケートするかどうかを指定し、Azure でのディスクの種類 (Standard SSD か HDD、または Premium マネージド ディスク) を選択します。 続けて、 **[次へ]** をクリックします。
   
    ![[レプリケート] ダイアログ ボックスの [ディスク] タブを表示するスクリーンショット。](./media/tutorial-migrate-vmware/disks.png)

13. **[レプリケーションの確認と開始]** で、設定を確認し、 **[レプリケート]** をクリックして、サーバーの初期レプリケーションを開始します。

> [!NOTE]
> レプリケーションが開始される前であれば、レプリケーションの設定をいつでも更新できます( **[管理]**  >  **[マシンのレプリケート]** )。 レプリケーションの開始後は、設定を変更できません。

### <a name="provisioning-for-the-first-time"></a>初回のプロビジョニング

これがプロジェクトでレプリケートする最初の VM である場合、Server Migration によって、プロジェクトと同じリソース グループにこれらのリソースが自動的にプロビジョニングされます。

- **サービス バス**: Server Migration では、サービス バスを使用して、レプリケーション オーケストレーション メッセージをアプライアンスに送信します。
- **ゲートウェイ ストレージ アカウント**: Server Migration では、ゲートウェイ ストレージ アカウントを使用して、レプリケートされる VM に関する状態情報を格納します。
- **ログ ストレージ アカウント**: Azure Migrate アプライアンスでは、VM のレプリケーション ログをログ ストレージ アカウントにアップロードします。 Azure Migrate により、レプリケーション情報がレプリカ マネージド ディスクに適用されます。
- **キー コンテナー**: Azure Migrate アプライアンスでは、キー コンテナーを使用して、サービス バスの接続文字列と、レプリケーションで使用されるストレージ アカウントのアクセス キーを管理します。

## <a name="track-and-monitor"></a>追跡して監視する

1. ポータルの通知で、ジョブの状態を追跡します。
2. レプリケーションの状態を監視するには、**サーバーをレプリケートしています** をクリックします (**Azure Migrate:Server Migration** 内)。

     ![レプリケーションを監視します](./media/tutorial-migrate-vmware/replicating-servers.png)

レプリケーションは、次のように行われます。
- レプリケーションの開始ジョブが正常に終了すると、マシンで Azure への初期レプリケーションが開始されます。
- 初期レプリケーション中に、VM スナップショットが作成されます。 スナップショットのディスク データは、Azure のレプリカ マネージド ディスクにレプリケートされます。
- 初期レプリケーションが完了すると、差分レプリケーションが開始されます。 オンプレミスのディスクに対する増分変更は、Azure のレプリカ ディスクに定期的にレプリケートされます。

## <a name="run-a-test-migration"></a>テスト移行を実行する


差分レプリケーションが開始されるとき、Azure への完全な移行を実行する前に、VM のテスト移行を実行できます。 各マシンで少なくとも 1 回は、移行前にこれを実行することを強くお勧めします。

- テスト移行を実行すると、移行が想定どおりに動作することが確認されます。オンプレミスのマシンに影響はなく、稼働状態が維持され、レプリケーションが続行されます。 
- テスト移行では、レプリケートされたデータを使用して Azure VM を作成することによって、移行がシミュレートされます (通常は、自分の Azure サブスクリプション内の非運用 VNet に移行されます)。
- レプリケートされたテスト Azure VM を使用して、移行を検証し、アプリのテストを実行して、完全な移行前に問題に対処することができます。

テスト移行を実行するには、次のようにします。


1. **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[移行されたサーバーをテストします]** をクリックします。

     ![移行されたサーバーのテスト](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. テストする VM を右クリックし、 **[テスト移行]** をクリックします。

    ![テスト移行](./media/tutorial-migrate-vmware/test-migrate.png)

3. **[テスト移行]** で、移行後に Azure VM が配置される Azure VNet を選択します。 非運用環境の VNet を使用することをお勧めします。
4. **テスト移行** ジョブが開始されます。 ポータルの通知でジョブを監視します。
5. 移行の完了後、Azure portal の **[仮想マシン]** で、移行された Azure VM を確認します。 マシン名には、サフィックス **-Test** が含まれています。
6. テストが完了したら、 **[マシンのレプリケート]** で Azure VM を右クリックし、 **[テスト移行をクリーンアップ]** をクリックします。

    ![移行のクリーンアップ](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>VM の移行

テスト移行が想定どおりに動作することを確認したら、オンプレミスのマシンを移行できます。

1. Azure Migrate プロジェクトの **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[サーバーをレプリケートしています]** をクリックします。

    ![サーバーをレプリケートしています](./media/tutorial-migrate-vmware/replicate-servers.png)

2. **[マシンのレプリケート]** で VM を右クリックし、 **[移行]** を選択します。
3. **[移行]**  >  **[仮想マシンをシャットダウンし、データ損失のない計画された移行を実行しますか]** で、 **[はい]**  >  **[OK]** の順に選択します。
    - 既定では、Azure Migrate によってオンプレミスの VM がシャットダウンされ、前回のレプリケーションが発生した後に発生したすべての VM の変更を同期するためにオンデマンド レプリケーションが実行されます。 こうすることで、データ損失がなくなります。
    - VM をシャットダウンしたくない場合は、 **[いいえ]** を選択します
4. VM に対して移行ジョブが開始されます。 Azure 通知でジョブを追跡します。
5. ジョブが完了したら、 **[仮想マシン]** ページで VM を表示して管理できます。

## <a name="complete-the-migration"></a>移行を完了する

1. 移行が完了したら、VM を右クリックして、 **[レプリケーションの停止]** を選択します。 これで、オンプレミスのマシンのレプリケーションが停止し、VM のレプリケーション状態情報がクリーンアップされます。
2. Windows VM および Linux の VM エージェントは、移行中に自動的にインストールされます。 マシンに Linux OS がある場合は、Linux VM エージェントのインストールが適切に行われるように、移行マシンで Azure VM Linux エージェントの[要件](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux#requirements)を確認します。 
3. データベース接続文字列、および Web サーバー構成の更新など、移行後のアプリの微調整を実行します。
4. Azure で現在実行されている移行後のアプリケーション上で、最終的なアプリケーションと移行の受け入れのテストを実行します。
5. 移行された Azure VM インスタンスにトラフィックを切り替えます。
6. ローカル VM インベントリからオンプレミスの VM を削除します。
7. ローカル バックアップからオンプレミスの VM を削除します。
8. Azure VM の新しい場所と IP アドレスを示すように内部ドキュメントを更新します。 

## <a name="post-migration-best-practices"></a>移行後のベスト プラクティス

- 復元性の向上:
    - Azure Backup サービスを使用して、Azure VM をバックアップすることで、データの安全性を保持します。 [詳細については、こちらを参照してください](../backup/quick-backup-vm-portal.md)。
    - Azure VM を Site Recovery のセカンダリ リージョンにレプリケートし、継続的にワークロードを実行して利用可能にします。 [詳細については、こちらを参照してください](../site-recovery/azure-to-azure-tutorial-enable-replication.md)。
- セキュリティの強化：
    - [Azure Security Center のジャスト イン タイム管理](../security-center/security-center-just-in-time.md)を利用して、インバウンド トラフィック アクセスをロックダウンして制限します。
    - [ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)を使って、ネットワーク トラフィックを管理エンドポイントに制限します。
    - [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) をデプロイして、ディスクをセキュリティ保護し、盗難や不正アクセスからデータを安全に保護します。
    - [IaaS リソースのセキュリティ保護](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)に関する詳細を読み、[Azure Security Center](https://azure.microsoft.com/services/security-center/) を確認してください。
- 監視と管理：
-  [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) をデプロイして、リソースの使用率と消費量を監視します。


## <a name="next-steps"></a>次のステップ

Azure クラウド導入フレームワークでの[クラウド移行の工程](/azure/architecture/cloud-adoption/getting-started/migrate)を調査します。
