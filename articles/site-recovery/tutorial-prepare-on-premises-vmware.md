---
title: "VMware VM から Azure へのディザスター リカバリーのためにオンプレミス VMware サーバーを準備する | Microsoft Docs"
description: "Azure Site Recovery サービスを使用して、Azure へのディザスター リカバリーのためにオンプレミス VMware サーバーを準備する方法を説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 90a4582c-6436-4a54-a8f8-1fee806b8af7
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: 2e59a78f2c348b581155484d77e272a050da1f1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>Azure へのディザスター リカバリーのためにオンプレミス VMware サーバーを準備する

このチュートリアルでは、VMware VM を Azure にレプリケートするときに、オンプレミス VMware インフラストラクチャを準備する方法を説明します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * VM の検出を自動化するために、vCenter サーバーまたは vSphere ESXi ホストのアカウントを準備する
> * モビリティ サービスを VMware VM に自動インストールするためのアカウントを準備する
> * VMware サーバーの要件を確認する
> * VMware VM の要件を確認する

このチュートリアル シリーズでは、Azure Site Recovery を使用して、単一の VM をバックアップする方法を説明します。 複数の VMware VM を保護する場合は、VMware レプリケーションの [Deployment Planner ツール](https://aka.ms/asr-deployment-planner) をダウンロードする必要があります。 このツールを使用して、VM の互換性、VM あたりのディスク数、ディスクあたりのデータ チャーンに関する情報を収集します。 また、このツールでは、ネットワーク帯域幅の要件に加えて、レプリケーションとテスト フェールオーバーを正常に実行するために必要な Azure インフラストラクチャに関する情報も収集されます。 このツールの実行については、[こちら](site-recovery-deployment-planner.md)をご覧ください。

これは、このシリーズの 2 番目のチュートリアルです。 前のチュートリアルで説明されているように、[Azure コンポーネントを設定](tutorial-prepare-azure.md)しておいてください。

## <a name="prepare-an-account-for-automatic-discovery"></a>自動検出用のアカウントを準備する

Site Recovery では、次のことを実行するために、VMware サーバーへのアクセスが必要です。

- VM を自動的に検出します。 少なくとも読み取り専用のアカウントが必要です。
- レプリケーション、フェールオーバー、およびフェールバックを調整します。 ディスクを作成および削除する、VM の電源をオンにするなどの操作を実行できるアカウントが必要です。

次の手順に従って、このアカウントを作成します。

1. 専用のアカウントを使用するには、vCenter レベルでロールを作成します。 ロールに **Azure_Site_Recovery** のような名前を付けます。
2. 次の表にまとめられているアクセス許可をそのロールに割り当てます。
3. vCenter サーバー または vSphere ホストにユーザーを作成します。 ユーザーにロールを割り当てます。

### <a name="vmware-account-permissions"></a>VMware アカウントのアクセス許可

**タスク** | **ロールとアクセス許可** | **詳細**
--- | --- | ---
**VM 検出** | 読み取り専用以上の権限を持つユーザー<br/><br/> データ センター オブジェクト -> 子オブジェクトへのプロパゲート、ロール=読み取り専用 | ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。<br/><br/> アクセスを制限するには、**子オブジェクトへの伝達**特権を持つ**アクセスなし**ロールを子オブジェクト (vSphere ホスト、データストア、VM、ネットワーク) に割り当てます。
**完全なレプリケーション、フェールオーバー、フェールバック** |  必要なアクセス許可を備えたロール (Azure_Site_Recovery) を作成し、このロールを VMware のユーザーまたはグループに割り当てる<br/><br/> データ センター オブジェクト –> 子オブジェクトへの伝達、ロール = Azure_Site_Recovery<br/><br/> データストア -> 空間の割り当て、データストアの参照、ファイルの低レベルの操作、ファイルの削除、仮想マシン ファイルの更新<br/><br/> ネットワーク -> ネットワークの割り当て<br/><br/> リソース -> リソース プールへの VM の割り当て、電源がオフの VM の移行、電源がオンの VM の移行<br/><br/> タスク -> タスクの作成、タスクの更新<br/><br/> 仮想マシン -> 構成<br/><br/> 仮想マシン -> 対話 -> 質問への回答、デバイス接続、CD メディアの構成、フロッピー メディアの構成、電源オフ、電源オン、VMware ツールのインストール<br/><br/> 仮想マシン -> インベントリ -> 作成、登録、登録解除<br/><br/> 仮想マシン -> プロビジョニング -> 仮想マシンのダウンロードの許可、仮想マシン ファイルのアップロードの許可<br/><br/> 仮想マシン -> スナップショット -> スナップショットの削除 | ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。<br/><br/> アクセスを制限するには、**子オブジェクトへの伝達**特権を持つ**アクセスなし**ロールを子オブジェクト (vSphere ホスト、データストア、VM、ネットワーク) に割り当てます。

## <a name="prepare-an-account-for-mobility-service-installation"></a>モビリティ サービスのインストール用のアカウントを準備する

モビリティ サービスは、レプリケートする VM にインストールする必要があります。 VM のレプリケーションを有効にすると、Site Recovery が自動的にこのサービスをインストールします。 自動的にインストールする場合は、Site Recovery で VM へのアクセスに使用するアカウントを準備する必要があります。 Azure コンソールでディザスター リカバリーを設定するときに、このアカウントを指定します。

1. VM にインストールするアクセス許可を持つドメイン アカウントまたはローカル アカウントを準備します。
2. Windows VM へのインストールでドメイン アカウントを使用していない場合は、ローカル マシンでリモート ユーザー アクセス コントロールを無効にします。
   - **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** にあるレジストリから、値に 1 を指定した DWORD エントリ **LocalAccountTokenFilterPolicy** を追加します。
3. Linux VM にインストールするには、ソースの Linux サーバーにルート アカウントを準備します。


## <a name="check-vmware-server-requirements"></a>VMware サーバーの要件を確認する

VMware サーバーが次の要件を満たしていることを確認します。

**コンポーネント** | **要件**
--- | ---
**vCenter サーバー** | vCenter 6.5、6.0、5.5
**vSphere ホスト** | vSphere 6.5、6.0、5.5

## <a name="check-vmware-vm-requirements"></a>VMware VM の要件を確認する

VM が次の表にまとめた Azure の要件に準拠していることを確認します。

**VM 要件** | **詳細**
--- | ---
**オペレーティング システムのディスク サイズ** | 最大 2048 GB
**オペレーティング システムのディスク数** | 1
**データ ディスク数** | 64 以下
**データ ディスク VHD のサイズ** | 最大 4095 GB
**ネットワーク アダプター** | 複数のアダプターがサポートされます。
**共有 VHD** | サポートされていません
**FC ディスク** | サポートされていません
**ハード ディスク フォーマット** | VHD または VHDX。<br/><br/> VHDX は現在、Azure でサポートされていませんが、Azure にフェールオーバーするとき、Site Recovery が VHDX を VHD に自動的に変換します。 オンプレミスにフェールバックした場合、VM は引き続き VHDX 形式を使用します。
**Bitlocker** | サポートされていません。 VM のレプリケーションを有効にする前に無効にします。
**VM 名** | 1 ～ 63 文字で指定します。<br/><br/> 名前に使用できるのは、英文字、数字、およびハイフンのみです。 VM 名の最初と最後は、文字か数字とする必要があります。
**VM の種類** | 第 1 世代 - Linux または Windows<br/><br/>第 2 世代 - Windows のみ

VM もサポートされているオペレーティング システムを実行している必要があります。 サポートされているバージョンの完全な一覧については [Site Recovery のサポート マトリックス](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)関する記事をご覧ください。

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>フェールオーバー後に Azure VM に接続するための準備をする

フェールオーバー シナリオ中に、オンプレミスのネットワークから Azure のレプリケートされた VM に接続することがあります。

フェールオーバー後に RDP を使用して Windows VM に接続するには、次の操作を行います。

1. インターネット経由でアクセスするには、フェールオーバーの前に、オンプレミスの VM 上の RDP を有効にします。 TCP と UDP の規則が **[パブリック]** プロファイルに追加されていることを確認し、**[Windows ファイアウォール]** > **[許可されたアプリ]** で、すべてのプロファイルで RDP が許可されていることを確認します。
2. サイト間 VPN 経由でアクセスするには、オンプレミスのコンピューターで RDP を有効にします。 RDP は、**[Windows ファイアウォール]** -> **[許可されたアプリおよび機能]** から、**ドメインとプライベート** ネットワークでの使用を許可する必要があります。
   オペレーティング システムの SAN ポリシーが **[OnlineAll]** に設定されていることを確認します。 [詳細情報](https://support.microsoft.com/kb/3031135) フェールオーバーをトリガーするときに、VM に保留中の Windows 更新プログラムがないようにします。 ある場合は、更新が完了するまで、仮想マシンにログインすることはできません。
3. フェールオーバー後の Microsoft Azure VM で **[ブート診断]** をオンにして、VM のスクリーンショットを確認します。 接続できない場合は、VM を実行していることを確認したうえで、[トラブルシューティングのヒント](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)をレビューしてください。

フェールオーバー後に SSH を使用して Linux VM に接続するには、次の操作を行います。

1. フェールオーバーする前に、オンプレミスのマシンで、システム起動時に Secure Shell サービスが自動的に開始するように設定されていることを確認します。 ファイアウォール規則で SSH 接続が許可されていることを確認します。

2. フェールオーバー後の Azure VM で、フェールオーバーされた VM とその接続先の Azure サブネットのネットワーク セキュリティ グループの規則について、SSH ポートへの受信接続を許可します。
   VM の[パブリック IP アドレスを追加](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine)します。 **[ブート診断]** をオンにすると、VM のスクリーンショットを確認できます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [VMware VM で Azure へのディザスター リカバリーを設定する](tutorial-vmware-to-azure.md)
