---
title: Azure Site Recovery の更新情報 | Microsoft Docs
description: サービスの更新情報の概要と Azure Site Recovery で使用されるコンポーネントのアップグレード方法を示します。
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/25/2019
ms.author: rajanaki
ms.openlocfilehash: bde341063fb6742bbe2a92592981d4a2a437d214
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203429"
---
# <a name="service-updates-in-azure-site-recovery"></a>Azure Site Recovery のサービスの更新情報
組織として、機能が停止したときに、それが計画されたものであれ、計画外のものであれ、どのような方法でデータの安全性を確保し、アプリやワークロードの実行状態を維持するかを把握しておく必要があります。 Azure Site Recovery は、サイトがダウンした場合でも利用可能な VM や物理サーバー上でアプリの実行状態を維持することで、BCDR 戦略に貢献します。 VM や物理サーバー上で実行されているワークロードは、プライマリ サイトが利用できなくなった場合でもセカンダリ ロケーションで利用できるように、Site Recovery によってレプリケートされます。 ワークロードは、プライマリ サイトが稼働状態に戻った時点でプライマリ サイトに復元されます。

Site Recovery で対応できるレプリケーションは次のとおりです。

- [Azure リージョン間で Azure VM をレプリケートする](azure-to-azure-tutorial-dr-drill.md)。
- オンプレミスの仮想マシンと物理サーバーを Azure (またはセカンダリ サイト) にレプリケートする。
詳細については、[こちら](https://docs.microsoft.com/azure/site-recovery)のドキュメントを参照してください。

Azure Site Recovery は、新機能の追加、サポート マトリックスの向上、バグの修正 (ある場合) など、サービスの更新情報を定期的に公開します。 最新情報を入手し、最新機能、拡張機能、バグの修正 (ある場合) をすべて利用するために、ユーザーは、常に Azure SIte Recovery コンポーネントの最新バージョンに更新することが推奨されます。 
 
## <a name="support-statement-for-azure-site-recovery"></a>Azure Site Recovery のサポートに関する声明 

> [!IMPORTANT]
> **Azure Site Recovery コンポーネントの新バージョン "N" がリリースされるたびに、"N-4" よりも前のすべてのバージョンはサポート対象外と見なされます**。 そのため、常に入手可能な最新バージョンにアップグレードすることをお勧めします。

> [!IMPORTANT]
> アップグレードの正式なサポートは N-4 から N までのバージョンです (N は最新バージョンです)。 N-6 を使用している場合は、まず N-4 にアップグレードしてから、N にアップグレードする必要があります。

## <a name="expiry-of-components"></a>コンポーネントの有効期限
Site Recovery のお客様は、コンポーネントの有効期限が近づいたとき、または期限が切れたときに、電子メール (登録している場合) またはポータルのコンテナー ダッシュボード上で通知を受け取ります。 Hyper-V VM を保護している場合、現時点ではコンテナー ダッシュボードの通知は利用できません。 また、お客様のシナリオの対応するインフラストラクチャ ビューに移動し、コンポーネントの横の [更新プログラムが利用可能です] ボタンをクリックすると、最新バージョンをダウンロードできるリンクにリダイレクトされます。

コンポーネントの有効期限が近づいたときの電子メール通知の頻度を以下に示します。
- コンポーネントの有効期限が切れる 60 日前: 2 週間に 1 回
- 次の 53 日間: 1 週間に 1 回
- 最終 7 日前:1 日 1 回
- 有効期限が切れた後: 2 週間に 1 回



### <a name="upgrading-when-the-difference-between-current-version-and-latest-released-version-is-greater-than-4"></a>現在のバージョンとリリースされている最新のバージョンの差が 4 を超える場合のアップグレード

1. 最初の手順として、現在インストールされているコンポーネントをバージョン N から N+4 にアップグレードした後、次に互換性のあるバージョンに移行します。 たとえば、最新のバージョンが 9.24 で、使用しているバージョンが 9.16 の場合は、まず 9.20 にアップグレードしてから 9.24 にアップグレードします。
2. シナリオに応じて、すべてのコンポーネントで同じプロセスに従います。

### <a name="support-for-latest-oskernel-versions"></a>最新の OS/カーネル バージョンのサポート

> [!NOTE]
> メンテナンス期間がスケジュールされていて、再起動がその一環となっている場合は、まず Site Recovery コンポーネントをアップグレードし、スケジュールされたアクティビティの残りを続行することをお勧めします。

1. カーネルまたは OS のバージョンをアップグレードする前に、まず、ターゲット バージョンが Azure Site Recovery でサポートされているかどうかを確認します。 この情報については、Azure VM、[VMware VM](vmware-physical-azure-support-matrix.md)、Hyper-V VM に関するドキュメントを参照してください。
2. [サービスの更新情報](https://azure.microsoft.com/updates/?product=site-recovery)を参照し、Site Recovery コンポーネントのどのバージョンで、アップグレード後の特定のバージョンがサポートされているかを確認してください。
3. 最初に、Site Recovery の最新バージョンにアップグレードします。
4. ここで、OS またはカーネルを目的のバージョンにアップグレードします。
5. 再起動を実行します。
6. これにより、マシン上の OS またはカーネルのバージョンが最新バージョンにアップグレードされます。また、新しいバージョンのサポートに必要な Site Recovery の最新の変更もソース マシンに読み込まれます。



## <a name="azure-vm-disaster-recovery-to-azure"></a>Azure への Azure VM のディザスター リカバリー
このシナリオでは、自動更新を[有効にする](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-autoupdate)ことを強くお勧めします。 Site Recovery による更新プログラムの管理は次の方法で選択できます。

- レプリケーションを有効にする手順の一環として
- コンテナー内の拡張機能の更新設定の切り替え

更新プログラムを手動で管理することを選択した場合は、次の手順に従います。

1. Azure portal に移動し、[Recovery Services コンテナー] に移動します。
2. Azure portal で、[Recovery Services コンテナー] の [レプリケートされたアイテム] ウィンドウに移動します。
3. 画面上部にある次の通知をクリックします。
    
    "*Site Recovery レプリケーション エージェントの新しい更新プログラムが利用可能です。* "
    
    "*クリックしてインストールしてください ->* "

4. 更新プログラムを適用する VM を選択し、 **[OK]** をクリックします。

## <a name="between-two-on-premises-vmm-sites"></a>2 つのオンプレミス VMM サイトの間
1. Microsoft Azure Site Recovery Provider 向けの最新の更新プログラム ロールアップをダウンロードします。
2. 復旧サイトを管理しているオンプレミスの VMM サーバーにまず更新プログラム ロールアップをインストールします。
3. 復旧サイトが更新されたら、プライマリ サイトを管理している VMM サーバーに更新プログラム ロールアップをインストールします。

> [!NOTE]
> VMM が高可用性 VMM (クラスター化 VMM) の場合は、VMM サービスがインストールされているクラスターのノードすべてにアップグレードをインストールしていることを確認してください。

## <a name="between-an-on-premises-vmm-site-and-azure"></a>オンプレミス VMM サイトと Azure の間
1. Microsoft Azure Site Recovery Provider 向けの更新プログラム ロールアップをダウンロードします。
2. オンプレミスの VMM サーバーに更新プログラム ロールアップをインストールします。
3. すべての Hyper-V ホストに最新の MARS エージェントをインストールします。

> [!NOTE]
> VMM が高可用性 VMM (クラスター化 VMM) の場合は、VMM サービスがインストールされているクラスターのノードすべてにアップグレードをインストールしていることを確認してください。

## <a name="between-an-on-premises-hyper-v-site-and-azure"></a>オンプレミス Hyper-V サイトと Azure の間

1. Microsoft Azure Site Recovery Provider 向けの更新プログラム ロールアップをダウンロードします。
2. Azure Site Recovery に登録した Hyper-V サーバーの各ノードにプロバイダーをインストールします。

> [!NOTE]
> お使いの Hyper-V がホストのクラスター化された Hyper-V サーバーの場合は、クラスターのノードすべてにアップグレードをインストールしていることを確認してください

## <a name="between-an-on-premises-vmware-or-physical-site-to-azure"></a>オンプレミス VMware または物理サイトと Azure の間

更新を実行する前に、アップグレード パスを理解するために、「[Site Recovery のサポートに関する声明」](#support-statement-for-azure-site-recovery)を参照します。

1. 前述の現在のバージョンおよびサポートに関する声明に基づいて、[ここ](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server)で提供されているガイドラインに従って、オンプレミス管理サーバーに最初に更新をインストールします。 これは、構成サーバーとプロセス サーバーの役割を持つサーバーです。
2. スケールアウト プロセス サーバーがある場合は、[ここ](vmware-azure-manage-process-server.md#upgrade-a-process-server)で提供されているガイドラインに従って、次にそれらを更新します。
3. 次に、保護されている各項目のモビリティ エージェントを更新するために、Azure portal に移動して、 **[保護された項目 (Protected Items)]**  >  **[レプリケートされた項目 (Replicated Items)]** ページに移動します。 このページで VM を選択します。 各 VM のページの下部に表示される **[エージェントの更新]** ボタンを選択します。 これで、保護されているすべての VM でモビリティ サービス エージェントが更新されます。

### <a name="reboot-of-source-machine-after-mobility-agent-upgrade"></a>モビリティ エージェントのアップグレード後のソース マシンの再起動

最新のすべての変更がソース マシンに読み込まれるように、モビリティ エージェントをアップグレードするたびに再起動することをお勧めします。 ただし、これは**必須ではありません**。 前回の再起動時のエージェント バージョンと現在のバージョンの差が 4 を超える場合は、再起動が必須です。 詳細については、次の表を参照してください。

|**前回再起動時のエージェント バージョン** | **アップグレード後** | **再起動は必須か?**|
|---------|---------|---------|
|9.16 |  9.18 | 必須ではない|
|9.16 | 9.19 | 必須ではない|
| 9.16 | 9.20 | 必須ではない
 | 9.16 | 9.21 | はい。最初に 9.20 にアップグレードした後、バージョン間の差が 4 を超えるため (前回再起動時は 9.16、ターゲット バージョンは 9.21)、9.21 にアップグレードする前に再起動します。

## <a name="links-to-currently-supported-update-rollups"></a>現在サポートされている更新プログラム ロールアップへのリンク

|更新プログラム ロールアップ  |プロバイダー  |統合セットアップ| OVF  |MARS|
|---------|---------|---------|---------|--------|
|[更新プログラム ロールアップ 37](https://support.microsoft.com/help/4508614/update-rollup-37-for-azure-site-recovery)     |   5.1.4300.0  |  9.25.5241.1   |  5.1.4300.0  | 2.0.9163.0
|[更新プログラム ロールアップ 36](https://support.microsoft.com/en-in/help/4503156)     |   5.1.4150.0  |  9.24.5211.1   |  5.1.4150.0  | 2.0.9160.0
|[更新プログラム ロールアップ 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery)     |   5.1.4000.0  |  9.23.5163.1   |  5.1.4000.0  | 2.0.9156.0
|[更新プログラム ロールアップ 34](https://support.microsoft.com/en-us/help/4490016/update-rollup-34-for-azure-site-recovery) - ホット フィックス     |   5.1.3950.0  |  9.22.5142.1   |  5.1.3950.0  | 2.0.9155.0
|[更新プログラム ロールアップ 33](https://support.microsoft.com/en-us/help/4489582/update-rollup-33-for-azure-site-recovery)     |   5.1.3900.0  |  9.22.5109.1   |  5.1.3900.0  | 2.0.9155.0
|[更新プログラム ロールアップ 32](https://support.microsoft.com/en-us/help/4485985/update-rollup-32-for-azure-site-recovery)     |   5.1.3800.0  |  9.21.5091.1   |  5.1.3800.0  |2.0.9144.0

## <a name="previous-update-rollups"></a>以前の更新プログラム ロールアップ

- [更新プログラム ロールアップ 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)
- [更新プログラム ロールアップ 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30)
- [更新プログラム ロールアップ 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery)
- [更新プログラム ロールアップ 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery)
- [更新プログラム ロールアップ 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery)
- [更新プログラム ロールアップ 26](https://support.microsoft.com/help/4344054/update-rollup-26-for-azure-site-recovery)  
- [更新プログラム ロールアップ 25](https://support.microsoft.com/help/4278275/update-rollup-25-for-azure-site-recovery) 
- [更新プログラム ロールアップ 23](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) 
- [更新プログラム ロールアップ 22](https://support.microsoft.com/help/4072852/update-rollup-22-for-azure-site-recovery) 
- [更新プログラム ロールアップ 21](https://support.microsoft.com/help/4051380/update-rollup-21-for-azure-site-recovery) 
- [更新プログラム ロールアップ 20](https://support.microsoft.com/help/4041105/update-rollup-20-for-azure-site-recovery) 
- [更新プログラム ロールアップ 19](https://support.microsoft.com/help/4034599/update-rollup-19-for-azure-site-recovery) 
