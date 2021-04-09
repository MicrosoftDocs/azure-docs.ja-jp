---
title: Azure Site Recovery を使用して VMware VM のディザスター リカバリーを準備する
description: Azure Site Recovery サービスを使用して、Azure へのディザスター リカバリーのためにオンプレミス VMware サーバーを準備する方法を説明します。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b8fd34c8f1e3a32a8252074941a49d61aa540207
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86134906"
---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>Azure へのディザスター リカバリーのためにオンプレミス VMware サーバーを準備する

この記事では、[Azure Site Recovery](site-recovery-overview.md) サービスを使用して、Azure へのディザスター リカバリーのためにオンプレミス VMware サーバーを準備する方法を説明します。 

これは、オンプレミスの VMware VM のディザスター リカバリーを Azure に設定する方法について説明するシリーズの 2 番目のチュートリアルです。 1 番目のチュートリアルでは、VMware のディザスター リカバリーに必要な [Azure コンポーネントを設定](tutorial-prepare-azure.md)しました。


この記事では、次のことについて説明します。

> [!div class="checklist"]
> * VM の検出を自動化するために、vCenter Server または vSphere ESXi ホストのアカウントを準備します。
> * モビリティ サービスを VMware VM に自動インストールするためのアカウントを準備する。
> * VMware サーバーと VM の要件とサポートを確認する。
> * フェールオーバー後に Azure VM に接続するための準備をする。

> [!NOTE]
> チュートリアルでは、シナリオの最も簡単なデプロイ パスを示します。 可能であれば既定のオプションを使い、すべての可能な設定とパスを示してはいません。 詳細な手順については、Site Recovery の目次のハウツー セクションにある記事を参照してください。

## <a name="before-you-start"></a>開始する前に

Azure が[このシリーズの最初のチュートリアル](tutorial-prepare-azure.md)で説明されているように準備されていることを確認します。

## <a name="prepare-an-account-for-automatic-discovery"></a>自動検出用のアカウントを準備する

Site Recovery では、次のことを実行するために、VMware サーバーへのアクセスが必要です。

- VM を自動的に検出します。 少なくとも読み取り専用のアカウントが必要です。
- レプリケーション、フェールオーバー、およびフェールバックを調整します。 ディスクを作成および削除する、VM の電源をオンにするなどの操作を実行できるアカウントが必要です。

次の手順に従って、このアカウントを作成します。

1. 専用のアカウントを使用するには、vCenter レベルでロールを作成します。 ロールに **Azure_Site_Recovery** のような名前を付けます。
2. 次の表にまとめられているアクセス許可をそのロールに割り当てます。
3. vCenter サーバーまたは vSphere ホストにユーザーを作成します。 ユーザーにロールを割り当てます。

### <a name="vmware-account-permissions"></a>VMware アカウントのアクセス許可

**タスク** | **ロールとアクセス許可** | **詳細**
--- | --- | ---
**VM 検出** | 読み取り専用以上の権限を持つユーザー<br/><br/> データ センター オブジェクト -> 子オブジェクトへのプロパゲート、ロール=読み取り専用 | ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。<br/><br/> アクセスを制限するには、**子オブジェクトへの伝達** 特権を持つ **アクセスなし** ロールを子オブジェクト (vSphere ホスト、データストア、VM、ネットワーク) に割り当てます。
**完全なレプリケーション、フェールオーバー、フェールバック** |  必要なアクセス許可を備えたロール (Azure_Site_Recovery) を作成し、このロールを VMware のユーザーまたはグループに割り当てる<br/><br/> データ センター オブジェクト –> 子オブジェクトへの伝達、ロール = Azure_Site_Recovery<br/><br/> データストア -> 空間の割り当て、データストアの参照、ファイルの低レベルの操作、ファイルの削除、仮想マシン ファイルの更新<br/><br/> ネットワーク -> ネットワークの割り当て<br/><br/> リソース -> リソース プールへの VM の割り当て、電源がオフの VM の移行、電源がオンの VM の移行<br/><br/> タスク -> タスクの作成、タスクの更新<br/><br/> 仮想マシン -> 構成<br/><br/> 仮想マシン -> 対話 -> 質問への回答、デバイス接続、CD メディアの構成、フロッピー メディアの構成、電源オフ、電源オン、VMware ツールのインストール<br/><br/> 仮想マシン -> インベントリ -> 作成、登録、登録解除<br/><br/> 仮想マシン -> プロビジョニング -> 仮想マシンのダウンロードの許可、仮想マシン ファイルのアップロードの許可<br/><br/> 仮想マシン -> スナップショット -> スナップショットの削除 | ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。<br/><br/> アクセスを制限するには、**子オブジェクトへの伝達** 特権を持つ **アクセスなし** ロールを子オブジェクト (vSphere ホスト、データストア、VM、ネットワーク) に割り当てます。

## <a name="prepare-an-account-for-mobility-service-installation"></a>モビリティ サービスのインストール用のアカウントを準備する

モビリティ サービスは、レプリケートするマシンにインストールする必要があります。 マシンのレプリケーションを有効にしている場合は、Site Recovery でこのサービスをプッシュ インストールできます。または、手動でインストールしたり、インストール ツールを使うこともできます。

- このチュートリアルでは、プッシュ インストールでモビリティ サービスをインストールします。
- このプッシュ インストールの場合は、Site Recovery で VM へのアクセスに使用できるアカウントを準備する必要があります。 Azure コンソールでディザスター リカバリーを設定するときに、このアカウントを指定します。

アカウントを次のように準備します。

VM にインストールするアクセス許可を持つドメイン アカウントまたはローカル アカウントを準備します。

- **Windows VM**: ドメイン アカウントを使っていない場合に Windows VM にインストールするには、ローカル マシンでリモート ユーザー アクセス コントロールを無効にします。 これを行うには、レジストリ **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** に DWORD エントリ **LocalAccountTokenFilterPolicy** を追加し、値を 1 に設定します。
- **Linux VM**: Linux VM にインストールするには、ソースの Linux サーバーにルート アカウントを準備します。


## <a name="check-vmware-requirements"></a>VMware の要件を確認する

VMware サーバーと VM が要件に準拠していることを確認します。

1. VMware サーバーの要件を[確認](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers)します。
2. Linux VM の場合は、ファイル システムとストレージの要件を[チェック](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)します。 
3. オンプレミスの[ネットワーク](vmware-physical-azure-support-matrix.md#network)と[ストレージ](vmware-physical-azure-support-matrix.md#storage)のサポートをチェックします。 
4. [Azure のネットワーク](vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)、[ストレージ](vmware-physical-azure-support-matrix.md#azure-storage)、[コンピューティング](vmware-physical-azure-support-matrix.md#azure-compute)に関して、フェールオーバー後のサポートをチェックします。
5. Azure にレプリケートするオンプレミスの VM は、「[Azure VM の要件](vmware-physical-azure-support-matrix.md#azure-vm-requirements)」に準拠している必要があります。
6. Linux 仮想マシンでは、デバイス名またはマウント ポイント名が一意である必要があります。 同じ名前を持つ複数のデバイス/マウント ポイントが存在しないことを確認します。 名前の大文字と小文字は区別されないことに注意してください。 たとえば、同じ VM の 2 つのデバイスに _device1_ と _Device1_ という名前を付けることは許可されません。


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>フェールオーバー後に Azure VM に接続するための準備をする

フェールオーバーの後、オンプレミスのネットワークから Azure VM に接続することがあります。

フェールオーバー後に RDP を使用して Windows VM に接続するには、次の操作を行います。

- **インターネットへのアクセス**。 フェールオーバーの前に、オンプレミスの VM で RDP を有効にします。 TCP と UDP の規則が **[パブリック]** プロファイルに追加されていることを確認し、 **[Windows ファイアウォール]**  >  **[許可されたアプリ]** で、すべてのプロファイルで RDP が許可されていることを確認します。
- **サイト間 VPN アクセス**:
    - フェールオーバーの前に、オンプレミスのマシンで RDP を有効にします。
    - RDP は、 **[Windows ファイアウォール]**  ->  **[許可されたアプリおよび機能]** から、**ドメインとプライベート** ネットワークでの使用を許可する必要があります。
    - オペレーティング システムの SAN ポリシーが **[OnlineAll]** に設定されていることを確認します。 [詳細については、こちらを参照してください](https://support.microsoft.com/kb/3031135)。
- フェールオーバーをトリガーするときに、VM に保留中の Windows 更新プログラムがないようにします。 ある場合は、更新が完了するまで、仮想マシンにサインインすることはできません。
- フェールオーバー後の Microsoft Azure VM で **[ブート診断]** をオンにして、VM のスクリーンショットを確認します。 接続できない場合は、VM が実行中であることを確認したうえで、[トラブルシューティングのヒント](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を確認してください。

フェールオーバー後に SSH を使用して Linux VM に接続するには、次の操作を行います。

- フェールオーバーする前に、オンプレミスのマシンで、システム起動時に Secure Shell サービスが自動的に開始するように設定されていることを確認します。
- ファイアウォール規則で SSH 接続が許可されていることを確認します。
- フェールオーバー後の Azure VM で、フェールオーバーされた VM とその接続先の Azure サブネットのネットワーク セキュリティ グループの規則について、SSH ポートへの受信接続を許可します。
- VM の[パブリック IP アドレスを追加](./site-recovery-monitor-and-troubleshoot.md)します。
- **[ブート診断]** をオンにすると、VM のスクリーンショットを確認できます。


## <a name="failback-requirements"></a>フェールバックの要件
オンプレミス サイトへのフェールバックを計画している場合は、[フェールバックのための前提条件](vmware-azure-reprotect.md#before-you-begin)がいくつかあります。 これらは今すぐ準備できますが、そうする必要はありません。 Azure にフェールオーバーした後に準備できます。



## <a name="next-steps"></a>次のステップ

ディザスター リカバリーを設定します。 複数の VM をレプリケートする場合は、容量を計画します。
> [!div class="nextstepaction"]
> [VMware VM の Azure へのディザスター リカバリーを設定する](vmware-azure-tutorial.md)
> [容量計画を実行する](site-recovery-deployment-planner.md)。
