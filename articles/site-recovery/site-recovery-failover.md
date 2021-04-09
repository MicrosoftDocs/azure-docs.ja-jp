---
title: Azure Site Recovery を使用してディザスター リカバリー中にフェールオーバーを実行する
description: Azure Site Recovery で VM/物理サーバーを Azure にフェールオーバーする方法。
ms.service: site-recovery
ms.topic: article
ms.date: 12/10/2019
ms.openlocfilehash: 6737f64773f91ede1631d42cd7f28c7d961c0454
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92368623"
---
# <a name="run-a-failover-from-on-premises-to-azure"></a>オンプレミスから Azure へのフェールオーバーを実行する

この記事では、[Azure Site Recovery](site-recovery-overview.md) でオンプレミスのマシンを Azure にフェールオーバーする方法について説明します

## <a name="before-you-start"></a>開始する前に

- ディザスター リカバリーのフェールオーバー プロセスについて[学習](failover-failback-overview.md)してください。
- 複数のマシンをフェールオーバーする場合は、復旧計画でマシンをまとめる方法について[学習](recovery-plan-overview.md)します。
- 完全なフェールオーバーを実行する前に、[ディザスター リカバリー訓練](site-recovery-test-failover-to-azure.md)を実行して、すべてが想定どおりに機能することを確認してください。

## <a name="prepare-to-connect-after-failover"></a>フェールオーバー後に接続するための準備をする

フェールオーバー後に作成された Azure VM に確実に接続できるようにするために、フェールオーバーの前にオンプレミスで行う必要があるいくつかの事項を次に示します。


### <a name="prepare-on-premises-to-connect-after-failover"></a>フェールオーバー後に接続するためにオンプレミスで準備する

フェールオーバー後に RDP/SSH を使用して Azure VM に接続する場合、フェールオーバーの前にオンプレミスで行う必要がある事項がいくつかあります。

**フェールオーバー後** | **場所** | **アクション**
--- | --- | ---
**Windows で実行中の Azure VM** | フェールオーバー前のオンプレミスのコンピューター | インターネット経由で Azure VM にアクセスするには、RDP を有効にし、TCP と UDP の規則が **[パブリック]** に追加されていることを確認し、 **[Windows ファイアウォール]**  >  **[許可されたアプリ]** のすべてのプロファイルで RDP が許可されていることを確認します。<br/><br/> サイト間接続で Azure VM にアクセスするには、コンピューターで RDP を有効にし、 **[Windows ファイアウォール]**  ->  **[許可されたアプリおよび機能]** の **[ドメイン] と [プライベート]** ネットワークで RDP が許可されていることを確認します。<br/><br/> <br/><br/> 静的な固定ルートと WinHTTP プロキシを削除します。 オペレーティング システムの SAN ポリシーが **[OnlineAll]** に設定されていることを確認します。 [詳細については、こちらを参照してください](https://support.microsoft.com/kb/3031135)。<br/><br/> フェールオーバーを開始する際は、実行待ちの Windows Update が VM にないことを確認してください。 フェールオーバーの実行時に Windows Update が開始された場合、更新が完了するまで VM にログインできなくなります。
**Linux で実行中の Azure VM** | フェールオーバー前のオンプレミスのコンピューター | VM 上の Secure Shell サービスがシステム起動時に自動的に開始されるよう設定されていることを確認します。<br/><br/> ファイアウォール規則で SSH 接続が許可されていることを確認します。


## <a name="run-a-failover"></a>フェールオーバーの実行

この手順では、[復旧計画](site-recovery-create-recovery-plans.md)のフェールオーバーを実行する方法について説明します。 1 つの VM のフェールオーバーを実行する場合は、[VMware VM](vmware-azure-tutorial-failover-failback.md)、[物理サーバー](physical-to-azure-failover-failback.md)、または [Hyper-V VM](hyper-v-azure-failover-failback-tutorial.md) の手順に従います。


次のように復旧計画のフェールオーバーを実行します。

1. Site Recovery コンテナーで、 **[復旧計画]**  > *recoveryplan_name* を選択します。
2. **[フェールオーバー]** をクリックします。

    ![Azure Site Recovery のスクリーンショット。ADRP ウィンドウの [詳細] メニューから [フェールオーバー] が選択されています。](./media/site-recovery-failover/Failover.png)

3. **[フェールオーバー]**  >  **[フェールオーバーの方向]** では、Azure にレプリケートする場合は規定値のままにします。
4. **[フェールオーバー]** で、フェールオーバーする **[復旧ポイント]** を選択します。

    - **Latest**:最新のポイントを使用します。 これにより、Site Recovery サービスに送信されたすべてのデータが処理され、各マシンの復旧ポイントが作成されます。 このオプションでは、フェールオーバー後に作成された VM は、フェールオーバーがトリガーされた時点で Site Recovery にレプリケートされたすべてのデータを保持しているため、RPO (回復ポイントの目標) が最も低くなります。
    ソース リージョンがダウンした場合、ログ処理を実行できなくなることに注意してください。 そのため、処理された最新の復旧ポイントにフェールオーバーする必要があります。 詳細については、次の点をご覧ください。
   - **最後に処理があった時点**:このオプションを使用して、Site Recovery によって既に処理された最新の復旧ポイントに VM をフェールオーバーします。 処理された最新の復旧ポイントは、 **[Latest Recovery Points]\(最新の復旧ポイント\)** で確認できます。 このオプションでは、未処理のデータの処理に時間がかからないため、RTO が低くなります。
   - **最新のアプリ整合性**:このオプションを使用して、Site Recovery によって処理されたアプリケーション整合性の最新の復旧ポイントに VM をフェールオーバーします。
   - **最新のマルチ VM 処理**:このオプションを使用すると、レプリケーション グループに含まれる VM は、最新の共通マルチ VM 整合性復旧ポイントにフェールオーバーされます。 その他の仮想マシンは、処理された最新の復旧ポイントにフェールオーバーされます。 このオプションは、マルチ VM 整合性がオンになっている 1 つ以上の VM を含む復旧計画のみを対象としています。
   - **最新のマルチ VM アプリ整合性**:このオプションを使用すると、レプリケーション グループに含まれる VM は、最新の共通マルチ VM アプリケーション整合性復旧ポイントにフェールオーバーされます。 その他の仮想マシンは、最新のアプリケーション整合性復旧ポイントにフェールオーバーされます。 マルチ VM 整合性がオンになっている 1 つ以上の VM を含む復旧計画のみを対象としています。
   - **Custom**:復旧計画には使用できません。 このオプションは、個々の VM のフェールオーバーのみを対象としています。

5. Site Recovery でフェールオーバーを開始する前にソース VM をシャットダウンする場合は、 **[フェールオーバーを開始する前にマシンをシャットダウンします]** を選択します。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。  

    > [!NOTE]
    > Hyper-V VM をフェールオーバーする場合、シャットダウンでは、フェールオーバーをトリガーする前に、サービスにまだ送信されていないオンプレミス データの同期とレプリケートが試行されます。 

6. **[ジョブ]** ページで、フェールオーバーの進行状況を確認します。 エラーが発生しても、復旧計画は最後まで実行されます。
7. フェールオーバー後、VM にサインインしてそれを検証します。 
8. フェールオーバーに使用する別の復旧ポイントに切り替える場合は、 **[復旧ポイントの変更]** を使用します。
9. 準備ができたら、フェールオーバーをコミットできます。 **[コミット]** アクションを行うと、サービスで使用可能なすべての復旧ポイントが削除されます。 以降、 **[復旧ポイントの変更]** オプションは使用できなくなります。

## <a name="run-a-planned-failover-hyper-v"></a>計画されたフェールオーバーの実行 (Hyper-V)

Hyper-V VM の計画されたフェールオーバーを実行できます。

- 計画されたフェールオーバーは、データの損失がまったく発生しないフェールオーバー方式です。
- 計画されたフェールオーバーを開始すると、まず、ソース仮想マシンがシャットダウンされ、最新のデータが同期されてから、フェールオーバーがトリガーされます。
- 計画されたフェールオーバーは、 **[計画されたフェールオーバー]** オプションを使用して実行します。 これは通常のフェールオーバーと同様の方法で実行されます。
 
## <a name="track-failovers"></a>フェールオーバーを追跡する

フェールオーバーに関連するジョブがいくつかあります。

![[ジョブ] ページのスクリーンショット。グループ 1 のジョブの一覧が表示されています。名前列で [開始 (1)] が展開されています。 SQLServer ジョブの行が強調表示されています。](./media/site-recovery-failover/FailoverJob.png)

- **前提条件チェック**:フェールオーバーに必要な条件がすべて満たされていることを確認します。
- **フェールオーバー**:データを処理し、そこから Azure VM を作成できるようにします。 **最新** の復旧ポイントを選択した場合、サービスに送信済みのデータから復旧ポイントが作成されます。
- **[開始]** : 前の手順で処理されたデータを使用して、Azure VM を作成します。

> [!WARNING]
> **進行中のフェールオーバーを取り消さないでください**: フェールオーバーが開始される前に、VM のレプリケーションが停止されます。 進行中のジョブをキャンセルした場合、フェールオーバーは停止しますが、VM のレプリケートは開始されません。 レプリケーションを再度開始することはできません。


### <a name="extra-failover-time"></a>追加のフェールオーバー時間

場合によっては、VM のフェールオーバーに、完了までに通常 8 ～ 10 分かかる中間ステップが必要になることがあります。 この追加のステップ/時間に影響されるマシンは次のとおりです。

* バージョン 9.8 より前の Mobility Service が実行されている VMware 仮想マシン。
* 物理サーバーと、物理サーバーとして保護されている Hyper-V VM。
* VMware Linux VM。
* これらのドライバーがブート ドライバーとして存在しない VMware VM。
    * storvsc
    * vmbus
    * storflt
    * intelide
    * atapi
* DHCP が有効になっていない VMware VM (DHCP と静的 IP アドレスのどちらを使用しているかは関係しません)。


## <a name="automate-actions-during-failover"></a>フェールオーバー中のアクションを自動化する

フェールオーバー中のアクションを自動化することができます。 これを行うために、復旧計画でスクリプトや Azure Automation Runbook を使用できます。

- スクリプトの追加など、復旧計画の作成とカスタマイズについて[学習](site-recovery-create-recovery-plans.md)してください。
- 復旧計画への Azure Automation Runbook の追加について[学習](site-recovery-runbook-automation.md)してください。


## <a name="configure-settings-after-failover"></a>フェールオーバーの後に設定を構成する

### <a name="retain-drive-letters-after-failover"></a>フェールオーバー後のドライブ文字の保持

Site Recovery では、ドライブ文字の保持が処理されます。 VM のレプリケーション時にディスクを除外する場合、その仕組みを示す[例を確認](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk)してください。

### <a name="prepare-in-azure-to-connect-after-failover"></a>フェールオーバー後に接続するために Azure で準備する

フェールオーバー後に作成された Azure VM に RDP または SSH を使用して接続する場合は、表にまとめられている要件に従います。

**[フェールオーバー]** | **場所** | **アクション**
--- | --- | ---
**Windows で実行中の Azure VM** | フェールオーバー後に Azure VM で |  VM の[パブリック IP アドレスを追加](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr)します。<br/><br/> フェールオーバーされる VM (およびその接続先となる Azure サブネット) は、そのネットワーク セキュリティ グループの規則で、RDP ポートへの受信接続を許可する必要があります。<br/><br/> **[ブート診断]** をオンにして、VM のスクリーンショットを確認します。<br/><br/> 接続できない場合は、VM が実行中であることを確認したうえで、[トラブルシューティングのヒント](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を確認してください。
**Linux で実行中の Azure VM** | フェールオーバー後に Azure VM で | フェールオーバーされた VM (および接続先の Azure サブネット) のネットワーク セキュリティ グループの規則で、SSH ポートへの着信接続を許可する必要があります。<br/><br/> VM の[パブリック IP アドレスを追加](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr)します。<br/><br/> VM のスクリーンショットを得るために、 **[ブート診断]** をオンにします。<br/><br/>

フェールオーバー後の接続の問題をトラブルシューティングするには、[ここ](site-recovery-failover-to-azure-troubleshoot.md)で説明されている手順に従ってください。

## <a name="set-up-ip-addressing"></a>IP アドレス指定の設定

- **内部 IP アドレス**:フェールオーバー後に Azure VM の内部 IP アドレスを設定するには、いくつかのオプションがあります。
    - 同じ IP アドレスを保持する:オンプレミスのマシンに割り当てられている IP アドレスと同じものを Azure VM で使用できます。
    - 異なる IP アドレスを使用する:別の IP アドレスを Azure VM に使用できます。
    - 内部 IP アドレスの設定については、[詳細情報](concepts-on-premises-to-azure-networking.md#assign-an-internal-address)を参照してください。
- **外部 IP アドレス**:フェールオーバーの際にパブリック IP アドレスを保持できます。 フェールオーバー プロセスの一部として作成された Azure VM には、Azure リージョンで利用可能な Azure パブリック IP アドレスを割り当てる必要があります。 パブリック IP アドレスは、手動で、または復旧計画でそのプロセスを自動化して割り当てることができます。 [詳細については、こちらを参照してください](concepts-public-ip-address-with-site-recovery.md)。


## <a name="next-steps"></a>次のステップ

フェールオーバーが完了したら、オンプレミス サイトへの Azure VM のレプリケートを開始するために再保護する必要があります。 レプリケーションが稼働するようになったら、準備ができた時点でオンプレミスにフェールバックできます。

- 再保護とフェールバックの[詳細を確認します](failover-failback-overview.md#reprotectionfailback)。
- VMware の再保護とフェールバックを[準備](vmware-azure-reprotect.md)します。
- Hyper-V VM を[フェールバック](hyper-v-azure-failback.md)します。
- 物理サーバーのフェールオーバーとフェールバックのプロセスについて[学習](physical-to-azure-failover-failback.md)します。