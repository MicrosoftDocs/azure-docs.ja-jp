---
title: Azure Site Recovery で Azure へのテスト フェールオーバー (ディザスター リカバリー訓練) を実行する
description: Azure Site Recovery サービスを使用して、オンプレミスから Azure へのテスト フェールオーバーの実行について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 815a35c167bbcd3ac03dfdaaf6d699e58a791f33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92369388"
---
# <a name="run-a-test-failover-disaster-recovery-drill-to-azure"></a>Azure へのテスト フェールオーバー (ディザスター リカバリー訓練) を実行する 


この記事では、Site Recovery テスト フェールオーバーを使用して Azure に対してディザスター リカバリー ドリルを実行する方法について説明します。  

テスト フェールオーバーを実行し、データの損失またはダウンタイムを発生させることなく、レプリケーションおよびディザスター リカバリー戦略を検証します。 テスト フェールオーバーは、実行中のレプリケーションや運用環境に影響ありません。 テスト フェールオーバーは、特定の仮想マシン (VM) または複数の VM を含む[復旧計画](site-recovery-create-recovery-plans.md)で実行できます。


## <a name="run-a-test-failover"></a>テスト フェールオーバーの実行
この手順では、復旧計画のテスト フェールオーバーを実行する方法について説明します。 1 つの VM のテスト フェールオーバーを実行する場合は、[ここ](tutorial-dr-drill-azure.md#run-a-test-failover-for-a-single-vm)で説明されている手順に従ってください

![Azure portal の [テスト フェールオーバー] ページのスクリーンショット。](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. Azure Portal の Site Recovery で、 **[Recovery Plans] (復旧計画)**  >  *[recoveryplan_name]*  >  **[Test Failover] (テスト フェールオーバー)** をクリックします。
2. フェールオーバーする **[Recovery Point] (復旧ポイント)** を選択します。 次のいずれかのオプションを使うことができます。
    - **最後に処理があった時点**:Site Recovery によって処理された最新の復旧ポイントに、計画内のすべての VM をフェールオーバーします。 特定の VM の最新の復旧ポイントを参照するには、VM 設定の **[Latest Recovery Points] (最新の回復ポイント)** を確認します。 このオプションを使用すると、未処理のデータの処理に時間がかからないため、RTO (目標復旧時間) を低くできます。
    - **最新のアプリ整合性**:Site Recovery によって処理されたアプリケーション整合性の最新の復旧ポイントに、計画内のすべての VM をフェールオーバーします。 特定の VM の最新の復旧ポイントを参照するには、VM 設定の **[Latest Recovery Points] (最新の回復ポイント)** を確認します。
    - **Latest**:このオプションは、Site Recovery サービスに送信されたすべてのデータを最初に処理して、各 VM の復旧ポイントを作成してから、それにフェールオーバーします。 このオプションは、フェールオーバー後に作成された VM は、フェールオーバーがトリガーされた時点で Site Recovery にレプリケートされたすべてのデータを保持しているため、RPO (目標復旧時点) を最も低くすることができます。
    - **最新のマルチ VM 処理**:このオプションは、マルチ VM 整合性が有効にされている 1 台または複数の VM を含む復旧計画で使用できます。 設定が有効にされている VM は、最新の共通マルチ VM 整合性復旧ポイントにフェールオーバーされます。 その他の VM は、最新の処理済み復旧ポイントにフェールオーバーされます。  
    - **最新のマルチ VM アプリ整合性**:このオプションは、マルチ VM 整合性が有効にされている 1 台または複数の VM を含む復旧計画で使用できます。 レプリケーション グループに含まれる VM は、最新の共通マルチ VM アプリケーション整合性復旧ポイントにフェールオーバーされます。 その他の VM は、最新のアプリケーション整合性復旧ポイントにフェールオーバーされます。
    - **Custom**:特定の VM を特定の復旧ポイントにフェールオーバーする場合は、このオプションを使用します。
3. テスト VM を作成する Azure 仮想ネットワークを選択します。

    - Site Recovery は、VM の **[コンピューティングとネットワーク]** の設定で提供されたものと同じ名前と同じ IP アドレスのサブネットに、テスト VM の作成を試みます。
    - テスト フェールオーバーに使用される Azure 仮想ネットワークで同じ名前のサブネットを使用できない場合は、アルファベット順で最初のサブネットにテスト VM が作成されます。
    - サブネットで同じ IP アドレスを使用できない場合、VM はサブネットで使用できる別の IP アドレスを受け取ります。 [詳細については、こちらを参照してください](#create-a-network-for-test-failover)。
4. Azure にフェールオーバーしていて、データ暗号化が有効になっている場合は、 **[暗号化キー]** で、プロバイダーのインストール中に暗号化を有効にしたときに発行された証明書を選びます。 暗号化が有効にされていない場合は、この手順を無視できます。
5. **[ジョブ]** タブで、フェールオーバーの進行状況を追跡します。テスト レプリカ マシンも Azure ポータルで確認できます。
6. Azure VM への RDP 接続を開始するには、フェールオーバーされる VM のネットワーク インターフェイスで、[パブリック IP アドレスを追加](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr)する必要があります。
7. すべて問題なく動作している場合は、 **[Cleanup test failover] (テスト フェールオーバーのクリーンアップ)** をクリックします。 これで、テスト フェールオーバー中に作成された VM が削除されます。
8. **[メモ]** を使用して、テスト フェールオーバーに関連する観察結果をすべて記録し、保存します。


![[テスト フェールオーバー] の [ジョブ] タブのスクリーンショット。](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

テスト フェールオーバーがトリガーされると次の操作が行われます。

1. **前提条件**:フェールオーバーを実行するために必要なすべての条件が満たされていることを確認する前提条件チェックが実行されます。
2. **フェールオーバー**:Azure VM を作成できるように、フェールオーバーによってデータが処理され、準備されます。
3. **Latest**:最新の復旧ポイントを選択した場合、サービスに送信済みのデータから復旧ポイントが作成されます。
4. **[開始]** : 前の手順で処理されたデータを使って Azure 仮想マシンが作成されます。

### <a name="failover-timing"></a>フェールオーバーのタイミング

以下のシナリオでは、フェールオーバーに、通常 8 ～ 10 分かかる特別な中間ステップが必要となります。

* 9\.8 よりも前のバージョンのモビリティ サービスを実行している VMware VM
* 物理サーバー
* VMware Linux VM
* 物理サーバーとして保護されている Hyper-V VM
* 次のドライバーがブート ドライバーでない VMware VM
    * storvsc
    * vmbus
    * storflt
    * intelide
    * atapi
* DHCP が有効になっていない VMware VM (DHCP と静的 IP アドレスのどちらを使用しているかは関係しない)。

その他すべてのケースでは、中間ステップは不要であり、フェールオーバーに要する時間は大幅に短くなります。


## <a name="create-a-network-for-test-failover"></a>テスト フェールオーバー用のネットワークの作成

テスト フェールオーバー用に、各 VM の **[Compute and Network] \(コンピューティングとネットワーク)** 設定で、運用復旧サイト ネットワークから分離されたネットワークを選択することをお勧めします。 既定では、作成した Azure 仮想ネットワークは、他のネットワークから分離されます。 テスト ネットワークは、運用ネットワークを模倣したものにする必要があります。

- テスト ネットワークでは、運用ネットワークと同じ数のサブネットが必要です。 サブネットは、同じ名前を持つ必要があります。
- テスト ネットワークでは、同じ IP アドレス範囲を使用する必要があります。
- テスト ネットワークの DNS を、 **[Compute and Network] \(コンピューティングとネットワーク)** 設定で DNS VM に指定した IP アドレスで更新します。 詳細については、[Active Directory 用のテスト フェールオーバーの考慮事項](site-recovery-active-directory.md#test-failover-considerations)を参照してください。


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>復旧サイトの運用ネットワークへのテスト フェールオーバー

運用ネットワークから分離されたテスト ネットワークを使用することをお勧めしますが、運用ネットワークへのディザスター リカバリー ドリルをテストする必要がある場合は、次のことに注意してください。

- テスト フェールオーバーを実行するときは、プライマリ VM がシャットダウンされていることを確認します。 そうしないと、同じ ID を持つ 2 つの仮想マシンが同じネットワークで同時に実行されることになります。 これは、予期しない結果につながる可能性があります。
- テスト フェールオーバー用に作成された VM の変更は、フェールオーバーをクリーンアップすると失われます。 これらの変更は、プライマリ VM にはレプリケートされません。
- 運用環境でのテストは、運用アプリケーションのダウンタイムにつながります。 テスト フェールオーバーの進行中に、ユーザーは、VM で実行中のアプリを使用しないでください。  



## <a name="prepare-active-directory-and-dns"></a>Active Directory と DNS の準備

アプリケーションのテストのためにテスト フェールオーバーを実行するには、テスト環境内に Active Directory 運用環境のコピーが必要です。 詳細については、[Active Directory のテスト フェールオーバーの考慮事項](site-recovery-active-directory.md#test-failover-considerations)を参照してください。

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>フェールオーバー後に Azure VM に接続するための準備をする

フェールオーバー後に RDP/SSH を使用して Azure VM に接続する場合は、表にまとめられている要件に従います。

**[フェールオーバー]** | **場所** | **アクション**
--- | --- | ---
**Windows で実行中の Azure VM** | フェールオーバー前のオンプレミスのコンピューター | インターネット経由で Azure VM にアクセスするには、RDP を有効にし、TCP と UDP の規則が **[パブリック]** に追加されていることを確認し、 **[Windows ファイアウォール]**  >  **[許可されたアプリ]** のすべてのプロファイルで RDP が許可されていることを確認します。<br/><br/> サイト間接続で Azure VM にアクセスするには、コンピューターで RDP を有効にし、 **[Windows ファイアウォール]**  ->  **[許可されたアプリおよび機能]** の **[ドメイン] と [プライベート]** ネットワークで RDP が許可されていることを確認します。<br/><br/>  オペレーティング システムの SAN ポリシーが **[OnlineAll]** に設定されていることを確認します。 [詳細については、こちらを参照してください](https://support.microsoft.com/kb/3031135)。<br/><br/> フェールオーバーを開始する際は、実行待ちの Windows Update が VM にないことを確認してください。 フェールオーバーの実行時に Windows Update が開始された場合、更新が完了するまで VM にログインできなくなります。
**Windows で実行中の Azure VM** | フェールオーバー後に Azure VM で |  VM の[パブリック IP アドレスを追加](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr)します。<br/><br/> フェールオーバーされる VM (およびその接続先となる Azure サブネット) は、そのネットワーク セキュリティ グループの規則で、RDP ポートへの受信接続を許可する必要があります。<br/><br/> **[ブート診断]** をオンにして、VM のスクリーンショットを確認します。<br/><br/> 接続できない場合は、VM が実行中であることを確認したうえで、[トラブルシューティングのヒント](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を確認してください。
**Linux で実行中の Azure VM** | フェールオーバー前のオンプレミスのコンピューター | VM 上の Secure Shell サービスがシステム起動時に自動的に開始されるよう設定されていることを確認します。<br/><br/> ファイアウォール規則で SSH 接続が許可されていることを確認します。
**Linux で実行中の Azure VM** | フェールオーバー後に Azure VM で | フェールオーバーされた VM (および接続先の Azure サブネット) のネットワーク セキュリティ グループの規則で、SSH ポートへの着信接続を許可する必要があります。<br/><br/> VM の[パブリック IP アドレスを追加](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr)します。<br/><br/> VM のスクリーンショットを得るために、 **[ブート診断]** をオンにします。<br/><br/>

フェールオーバー後の接続の問題をトラブルシューティングするには、[ここ](site-recovery-failover-to-azure-troubleshoot.md)で説明されている手順に従ってください。

## <a name="next-steps"></a>次のステップ
ディザスター リカバリー ドリルが完了したら、他の種類の[フェールオーバー](site-recovery-failover.md)について学習します。