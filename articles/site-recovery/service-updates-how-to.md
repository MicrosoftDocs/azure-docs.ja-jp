---
title: Azure Site Recovery の更新プログラムとコンポーネントのアップグレード
description: Azure Site Recovery サービスの更新プログラムとコンポーネントのアップグレードの概要について説明します。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: ramamill
ms.openlocfilehash: a1ea8b6fb9800d796670161288be0d86ce6ffc42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89424941"
---
# <a name="service-updates-in-site-recovery"></a>Site Recovery のサービスの更新プログラム

この記事では [Azure Site Recovery](site-recovery-overview.md) の更新プログラムの概要と、Site Recovery コンポーネントをアップグレードする方法について説明します。

Site Recovery は、定期的にサービスの更新プログラムを発行します。 更新プログラムには、新機能、サポートの強化、コンポーネントの更新プログラム、バグの修正が含まれています。 最新の機能と修正プログラムを活用するために、Site Recovery コンポーネントの最新バージョンを実行することをお勧めします。 
 
 
## <a name="updates-support"></a>更新プログラムのサポート

### <a name="support-statement-for-azure-site-recovery"></a>Azure Site Recovery のサポートに関する声明

常に最新のコンポーネント バージョンにアップグレードすることをお勧めします。

**Azure Site Recovery コンポーネントの新バージョン "N" がリリースされるたびに、"N-4" よりも前のすべてのバージョンはサポート対象外と見なされます**。 

> [!IMPORTANT]
> 正式なサポートとして、N-4 以降のバージョンから N バージョンにアップグレードすることができます。 たとえば、N-6 を実行している場合は、まず N-4 にアップグレードしてから N にアップグレードする必要があります。


### <a name="links-to-currently-supported-update-rollups"></a>現在サポートされている更新プログラム ロールアップへのリンク

 最新の更新プログラムのロールアップ (バージョン N) については、[この記事](site-recovery-whats-new.md)を参照してください。 Site Recovery では、N-4 バージョンがサポートされていることに注意してください。



## <a name="component-expiry"></a>コンポーネントの有効期限

Site Recovery では、期限切れの (または、有効期限が近づいている) コンポーネントについて、(メール通知を購読している場合は) メールで、またはポータルのコンテナー ダッシュボードで通知します。

- また、更新プログラムを利用できる場合、ポータルのシナリオのインフラストラクチャ ビューで、コンポーネントの横に **[更新プログラムが利用可能]** ボタンが表示されます。 このボタンをクリックすると、最新のコンポーネント バージョンをダウンロードするためのリンクが表示されます。
-  Hyper-V VM をレプリケートする場合、コンテナーのダッシュボード通知は使用できません。 

メール通知は次のように送信されます。

**Time** | **頻度**
--- | ---
コンポーネントの有効期限の 60 日前 | 2 週間に 1 回
次の 53 日間 | 1 週間に 1 回
過去 7 日間 | 1 回 (1 日あたり)
有効期限後 | 2 週間に 1 回


### <a name="upgrading-outside-official-support"></a>正式なサポート外でのアップグレード

コンポーネントのバージョンと最新リリース バージョンの差が 4 より大きい場合は、サポート対象外と見なされます。 この場合は、次のようにアップグレードします。 

1. 現在インストールされているコンポーネントを現在のバージョン + 4 にアップグレードします。 たとえば、バージョン 9.16 の場合は、9.20 にアップグレードします。
2. 次に、互換性のある次のバージョンにアップグレードします。 この例では、9.16 から9.20 にアップグレードした後、9.24 にアップグレードします。 

すべての関連コンポーネントに対して同じプロセスを実行します。

### <a name="support-for-latest-operating-systemskernels"></a>最新のオペレーティング システム/カーネルのサポート

> [!NOTE]
> メンテナンス期間がスケジュールされていて、それに再起動が含まれている場合は、最初に Site Recovery コンポーネントをアップグレードしてから、メンテナンス期間内のスケジュールされたアクティビティの残りの部分を続行することをお勧めします。

1. オペレーティング システム/カーネルのバージョンをアップグレードする前に、ターゲット バージョンが Site Recovery をサポートしているかどうかを確認してください。 

    - [Azure VM](azure-to-azure-support-matrix.md#replicated-machine-operating-systems) のサポート。
    - [VMware/物理サーバー](vmware-physical-azure-support-matrix.md#replicated-machines)のサポート
    - [Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms) のサポート。
2. [利用できる更新プログラム](site-recovery-whats-new.md)を確認して、アップグレードする対象を見つけます。
3. 最新の Site Recovery バージョンにアップグレードします。
4. オペレーティング システム/カーネルを必要なバージョンにアップグレードします。
5. 再起動します。


このプロセスにより、コンピューターのオペレーティング システム/カーネルが最新のバージョンにアップグレードされ、新しいバージョンをサポートするために必要な最新の Site Recovery の変更がコンピューターに読み込まれます。

## <a name="azure-vm-disaster-recovery-to-azure"></a>Azure への Azure VM のディザスター リカバリー

このシナリオでは、[自動更新を有効にする](azure-to-azure-autoupdate.md)ことを強くお勧めします。 Site Recovery で更新プログラムを管理するには、次の手順を実行します。

- レプリケーションを有効にするプロセスの実行中。
- コンテナー内の拡張機能の更新設定を指定する。

更新プログラムを手動で管理する場合、次のいずれかのオプションを選択できます。

1. 新しいエージェントの更新が利用できるとき、Site Recovery では、ページの上部付近でコンテナーの通知が提供されます。 コンテナーの **[レプリケートされたアイテム]** で、画面の上部にあるこの通知をクリックします。 
    
    **Site Recovery レプリケーション エージェントの新しい更新プログラムが利用可能です。クリックしてインストールしてください ->** <br/><br/>更新プログラムを適用する VM を選択し、 **[OK]** をクリックします。

2. VM のディザスター リカバリー概要ページには、[エージェントの状態] フィールドがありますが、エージェントが期限切れになると、[Critical Upgrade]\(重大なアップグレード\) と表示されます。 それをクリックし、後続の指示に従い、仮想マシンを手動でアップグレードします。

## <a name="vmware-vmphysical-server-disaster-recovery-to-azure"></a>VMware VM/物理サーバーの Azure へのディザスター リカバリー

1. 現在のバージョンと[サポートに関する声明](#support-statement-for-azure-site-recovery)に基づき、[この手順](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server)に従って、まずオンプレミスの構成サーバーに更新プログラムをインストールします。 
2. スケールアウト プロセス サーバーがある場合は、[この手順](vmware-azure-manage-process-server.md#upgrade-a-process-server)を実行します。
3. 保護されている各マシン上のモビリティ エージェントを更新するには、[こちら](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal)の記事を参照してください。

### <a name="reboot-after-mobility-service-upgrade"></a>モビリティ サービスのアップグレード後に再起動する

最新のすべての変更がソース マシンに読み込まれるように、モビリティ サービスをアップグレードするたびに再起動することをお勧めします。

前回の再起動時のエージェントのバージョンと現在のバージョンの差が 4 を超える場合を除き、再起動は必須ではありません。

表の例でこのしくみを説明します。

|**エージェントのバージョン (前回の再起動)** | **アップグレード先** | **強制的に再起動する?**|
|---------|---------|---------|
|9.16 |  9.18 | 必須ではない|
|9.16 | 9.19 | 必須ではない|
| 9.16 | 9.20 | 必須ではない
 | 9.16 | 9.21 | 必須。<br/><br/> 9\.20 にアップグレードし、再起動してから 9.21 にアップグレードします。

## <a name="hyper-v-vm-disaster-recovery-to-azure"></a>Azure への Hyper-V VM のディザスター リカバリー

### <a name="between-a-hyper-v-site-and-azure"></a>Hyper-V サイトと Azure の間

1. Microsoft Azure Site Recovery Provider の更新プログラムをダウンロードします。
2. Site Recovery に登録されている各 Hyper-V サーバーにプロバイダーをインストールします。 クラスターを実行している場合は、すべてのクラスター ノード上でアップグレードします。


## <a name="between-an-on-premises-vmm-site-and-azure"></a>オンプレミス VMM サイトと Azure の間
1. Microsoft Azure Site Recovery Provider の更新プログラムをダウンロードします。
2. VMM サーバーにプロバイダーをインストールします。 VMM がクラスターにデプロイされている場合は、すべてのクラスター ノードにプロバイダーをインストールします。
3. すべての Hyper-V ホストまたはクラスター ノードに最新の Microsoft Azure Recovery Services エージェントをインストールします。


## <a name="between-two-on-premises-vmm-sites"></a>2 つのオンプレミス VMM サイトの間
1. Microsoft Azure Site Recovery Provider の最新の更新プログラムをダウンロードします。
2. セカンダリ回復サイトを管理する VMM サーバーに、最新のプロバイダーをインストールします。 VMM がクラスターにデプロイされている場合は、すべてのクラスター ノードにプロバイダーをインストールします。
3. 復旧サイトが更新されたら、プライマリ サイトを管理している VMM サーバーにプロバイダーをインストールします。

## <a name="next-steps"></a>次のステップ

[Azure の更新プログラム](https://azure.microsoft.com/updates/?product=site-recovery)のページに従って、新しい更新プログラムとリリースを追跡します。
