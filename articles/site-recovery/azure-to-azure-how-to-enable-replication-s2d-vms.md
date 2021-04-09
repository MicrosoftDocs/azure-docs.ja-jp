---
title: Azure Site Recovery を使用して記憶域スペース ダイレクトを実行している Azure VM をレプリケートする
description: Azure Site Recovery を使用して記憶域スペース ダイレクトを実行している Azure VM をレプリケートする方法について説明します。
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: f438fadb73f7e3bd25cd7ab9aef0bc46285e30e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92424825"
---
# <a name="replicate-azure-vms-running-storage-spaces-direct-to-another-region"></a>記憶域スペース ダイレクトを実行している Azure VM を別のリージョンにレプリケートする

この記事では、記憶域スペース ダイレクトを実行している Azure VM のディザスター リカバリーを有効にする方法について説明します。

>[!NOTE]
>記憶域スペース ダイレクト クラスターでは、クラッシュ整合性復旧ポイントのみがサポートされています。
>

[記憶域スペース ダイレクト (S2D)](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) はソフトウェアで定義されたストレージであり、Azure 上に[ゲスト クラスター](https://techcommunity.microsoft.com/t5/failover-clustering/bg-p/FailoverClustering)を作成する手段を提供します。  Microsoft Azure のゲスト クラスターは、IaaS VM で構成されるフェールオーバー クラスターです。 ホストされた VM ワークロードはゲスト クラスター間でフェールオーバーすることができ、アプリケーションに対して、単独の Azure VM で可能なものより高い可用性 SLA を実現できます。 SQL やスケールアウト ファイル サーバーなどの重要なアプリケーションを VM でホストする場合に便利です。

## <a name="disaster-recovery-with-storage-spaces-direct"></a>記憶域スペース ダイレクトを使用したディザスター リカバリー

一般的なシナリオでは、スケールアウト ファイル サーバーのようなアプリケーションの高い回復性のために、Azure で仮想マシン ゲスト クラスターを使用できます。 これでもアプリケーションに高可用性を提供できますが、リージョン レベルの障害に対して Site Recovery を使用し、これらのアプリケーションを保護することができます。 Site Recovery では、1 つのリージョンから別の Azure リージョンにデータがレプリケートされ、フェールオーバーのときはディザスター リカバリー リージョンでクラスターが稼働されます。

次の図は、記憶域スペース ダイレクトを使用している 2 ノードの Azure VM フェールオーバー クラスターを示しています。

![storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)


- Windows フェールオーバー クラスターには 2 つの Azure 仮想マシンがあり、各仮想マシンは 2 つ以上のデータ ディスクを備えています。
- S2D は、データ ディスク上のデータを同期し、同期されたストレージを記憶域プールとして提供します。
- 記憶域プールは、フェールオーバー クラスターに対するクラスターの共有ボリューム (CSV) になります。
- フェールオーバー クラスターでは、データ ドライブに CSV が使用されています。

**ディザスター リカバリーの考慮事項**

1. クラスターに対する[クラウド監視](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp)をセットアップするときは、監視をディザスター リカバリー リージョン内に維持します。
2. ソース リージョンとは異なる DR リージョン上のサブネットに仮想マシンをフェールオーバーする場合は、フェールオーバーの後でクラスターの IP アドレスを変更する必要があります。  クラスターの IP を変更するには、Site Recovery の[復旧計画スクリプト](./site-recovery-runbook-automation.md)を使用する必要があります。</br>
カスタム スクリプト拡張機能を使用して VM 内でコマンドを実行する[サンプル スクリプト](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>S2D クラスターに対して Site Recovery を有効にする

1. 復旧サービス コンテナー内で、[+replicate]\(+ レプリケート\) をクリックします
1. クラスター内のすべてのノードを選択し、それらを[マルチ VM 整合性グループ](./azure-to-azure-common-questions.md#multi-vm-consistency)の一部にします
1. アプリケーション整合性がオフになっているレプリケーション ポリシーを選択します* (クラッシュ整合性のサポートのみ利用可能)
1. レプリケーションを有効にします

   ![レプリケーション設定を構成する場所を示すスクリーンショット。](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. レプリケートされたアイテムに移動すると、両方の仮想マシンの状態を確認できます。
3. 両方の仮想マシンが保護されており、マルチ VM 整合性グループの一部としても表示されます。

   ![仮想マシンが保護されていることと、マルチ VM 整合性グループの一部を示すスクリーンショット。](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>復旧計画の作成
復旧計画では、フェールオーバー時における多層アプリケーション内の各種階層の順序付けがサポートされます。 順序付けは、アプリケーションの一貫性の保守に役立ちます。 多層 Web アプリケーションの復旧計画を作成する際には、[復旧計画の作成](site-recovery-create-recovery-plans.md)に関するページで説明されている手順を完了します。

### <a name="adding-virtual-machines-to-failover-groups"></a>フェールオーバー グループへの仮想マシンの追加

1.  仮想マシンを追加することによって復旧計画を作成します｡
2.  [カスタマイズ] をクリックして仮想マシンをグループ化します｡ 既定では､すべての仮想マシンが｢グループ 1｣のメンバーです｡


### <a name="add-scripts-to-the-recovery-plan"></a>復旧計画へのスクリプトの追加
お使いのアプリケーションを正常に機能させるには、フェールオーバー後、またはテスト フェールオーバー時に、Azure の仮想マシンに対して一定の操作を実行することが必要な場合があります。 フェールオーバー後の操作は一部自動化することもできます。 たとえば、ここではロード バランサーをアタッチし、クラスターの IP を変更します。


### <a name="failover-of-the-virtual-machines"></a>仮想マシンのフェールオーバー 
Site Recovery の[復旧計画](./site-recovery-create-recovery-plans.md)を使用して VM の両方のノードをフェールオーバーする必要があります 

![storagespacesdirect 保護](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>テスト フェールオーバーの実行
1.  Azure Portal で､Recovery Service コンテナーを選択します。
2.  作成した復旧計画を選択します。
3.  **[テスト フェールオーバー]** を選択します。
4.  テスト フェールオーバー プロセスを開始するには、復旧ポイントと Azure 仮想ネットワークを選択します。
5.  セカンダリ環境が立ち上がったら、検証を実行します。
6.  検証が完了したら、フェールオーバー環境をクリーニングするために、 **[テスト フェールオーバーのクリーンアップ]** を選択します。

詳しくは、「[Site Recovery での Azure へのフェールオーバーをテストする](site-recovery-test-failover-to-azure.md)」をご覧ください。

## <a name="run-a-failover"></a>フェールオーバーの実行

1.  Azure Portal で､Recovery Service コンテナーを選択します。
2.  SAP アプリケーション用に作成した復旧計画を選択します。
3.  **[フェールオーバー]** を選択します。
4.  フェールオーバー プロセスを開始するには、復旧ポイントを選択します。

詳しくは、「[Site Recovery でのフェールオーバー](site-recovery-failover.md)」をご覧ください。
## <a name="next-steps"></a>次のステップ

フェールバックの実行について[さらに学習する](./azure-to-azure-tutorial-failover-failback.md)。
