---
title: Azure Migrate での Hyper-V の移行のサポート
description: Azure Migrate を使用した Hyper-V の移行のサポートについて説明します。
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 90da16789344754c02d46022160db71ee261a056
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754064"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Hyper-V の移行のサポート マトリックス

この記事では、[Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) を使用した Hyper-V VM の移行のサポート設定および制限事項について概要を説明します。 Azure への移行についての Hyper-V VM の評価に関する情報については、[評価のサポート マトリックス](migrate-support-matrix-hyper-v.md)を確認してください。

## <a name="migration-limitations"></a>移行に関する制限事項

レプリケーションでは、一度に最大 10 個の VM を選択できます。 より多くのマシンを移行する場合は、10 台単位のグループでレプリケートしてください。


## <a name="hyper-v-host-requirements"></a>Hyper-V ホストの要件

| **サポート**                | **詳細**               
| :-------------------       | :------------------- |
| **デプロイ**       | Hyper-V ホストは、スタンドアロンにすることも、クラスターにデプロイすることもできます。 <br/>Azure Migrate レプリケーション ソフトウェア (Hyper-V レプリケーション プロバイダー) は Hyper-V ホストにインストールします。|
| **アクセス許可**           | Hyper-V ホストに対する管理者のアクセス許可が必要です。 |
| **ホスト オペレーティング システム** | 最新の更新プログラムが適用された Windows Server 2019、Windows Server 2016、Windows Server 2012 R2 これらのオペレーティング システムの Server コア インストールもサポートされていることに注意してください。 |
| **その他のソフトウェア要件** | .NET Framework 4.7 以降 |
| **ポート アクセス** |  VM レプリケーション データを送信するための HTTPS ポート 443 での送信接続。
| **空きディスク領域 (キャッシュ)** |  600 GB |
| **ディスクの空き領域 (リテンション ディスク)** |  600 GB |


## <a name="hyper-v-vms"></a>Hyper-V VM

| **サポート**                  | **詳細**               
| :----------------------------- | :------------------- |
| **オペレーティング システム** | Azure でサポートされているすべての [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) および [Linux](../virtual-machines/linux/endorsed-distros.md) オペレーティング システム。 |
**Windows Server 2003** | Windows Server 2003 を実行している VM では、移行の前に [Hyper-V 統合サービスをインストールする](prepare-windows-server-2003-migration.md)必要があります。 | 
**Azure での Linux VM** | 一部の VM は、Azure で実行できるように変更が必要な場合があります。<br/><br/> Linux の場合、Azure Migrate によって、次のオペレーティング システム用に自動的に変更が行われます。<br/> - Red Hat Enterprise Linux 7.8、7.7、7.6、7.5、7.4、7.0、6.x<br/> - Cent OS 7.7、7.6、7.5、7.4、6.x</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - SUSE Linux Enterprise Server 15 SP1 <br/>- Ubuntu 19.04、19.10、14.04LTS、16.04LTS、18.04LTS<br/> - Debian 7、8 <br/> Oracle Linux 7.7、7.7-CI<br/> その他のオペレーティング システムの場合は、手動で[必要な変更](prepare-for-migration.md#verify-required-changes-before-migrating)を行います。
| **Azure に必要な変更** | 一部の VM は、Azure で実行できるように変更が必要な場合があります。 移行の前に手動で調整してください。 関連する記事には、その手順が記載されています。 |
| **Linux ブート**                 | /boot が専用パーティションに存在する場合は、OS ディスク上に存在する必要があり、複数のディスクに分散していてはいけません。<br/> /boot がルート (/) パーティションに含まれている場合は、"/" パーティションは OS ディスク上に存在する必要があり、他のディスクにまたがっていてはいけません。 |
| **UEFI ブート**                  | サポートされています。 UEFI ベースの VM は、Azure 第 2 世代 VM に移行されます。  |
| **UEFI - セキュア ブート**         | 移行はサポートされません。|
| **ディスク サイズ**                  | OS ディスク (BIOS ブート) 用に 2 TB、OS ディスク (UEFI ブート) 用に 4 TB、データ ディスク用に 4 TB。|
| **ディスクの数** | VM あたり最大で 16 台のディスク。|
| **暗号化されたディスクまたはボリューム**    | 移行はサポートされません。|
| **RDM またはパススルー ディスク**      | 移行はサポートされません。|
| **共有ディスク** | 共有ディスクを使用する VM の移行はサポートされません。|
| **NFS**                        | VM 上のボリュームとしてマウントされた NFS ボリュームはレプリケートされません。|
| **iSCSI**                      | iSCSI ターゲットを含む VM の移行はサポートされていません。
| **ターゲット ディスク**                | マネージド ディスクのみを含む Azure VM に移行することができます。 |
| **IPv6** | サポートされていません。|
| **NIC チーミング** | サポートされていません。|
| **Azure Site Recovery** | VM で Azure Site Recovery によるレプリケーションが有効になっている場合、Azure Migrate Server Migration を使用してレプリケートすることはできません。|
| **ポート** | VM レプリケーション データを送信するための HTTPS ポート 443 での送信接続。|

### <a name="url-access-public-cloud"></a>URL アクセス (パブリック クラウド)

Hyper-V ホストのレプリケーション プロバイダー ソフトウェアは、次の URL にアクセスできる必要があります。

**URL** | **詳細**
--- | ---
login.microsoftonline.com | Active Directory を使用したアクセス制御と ID 管理。
backup.windowsazure.com | レプリケーション データの転送と調整。
*.hypervrecoverymanager.windowsazure.com | レプリケーションの管理に使用されます。
*.blob.core.windows.net | ストレージ アカウントにデータをアップロードします。 
dc.services.visualstudio.com | 内部監視に使用するアプリ ログをアップロードします。
time.windows.com | システム時刻とグローバル時刻間の時刻同期を確認します。

### <a name="url-access-azure-government"></a>URL アクセス (Azure Government)

Hyper-V ホストのレプリケーション プロバイダー ソフトウェアは、次の URL にアクセスできる必要があります。

**URL** | **詳細**
--- | ---
login.microsoftonline.us | Active Directory を使用したアクセス制御と ID 管理。
backup.windowsazure.us | レプリケーション データの転送と調整。
*.hypervrecoverymanager.windowsazure.us | レプリケーションの管理に使用されます。
*.blob.core.usgovcloudapi.net | ストレージ アカウントにデータをアップロードします。
dc.services.visualstudio.com | 内部監視に使用するアプリ ログをアップロードします。
time.nist.gov | システム時刻とグローバル時刻間の時刻同期を確認します。

## <a name="azure-vm-requirements"></a>Azure VM の要件

Azure にレプリケートされたオンプレミス VM はすべて、この表にまとめられている Azure VM の要件を満たしている必要があります。

**コンポーネント** | **必要条件** | **詳細**
--- | --- | ---
オペレーティング システムのディスク サイズ | 最大 2,048 GB。 | サポートされていない場合、確認は失敗します。
オペレーティング システムのディスク数 | 1 | サポートされていない場合、確認は失敗します。
データ ディスク数 | 16 以下 | サポートされていない場合、確認は失敗します。
データ ディスク サイズ | 最大 4,095 GB | サポートされていない場合、確認は失敗します。
ネットワーク アダプター | 複数のアダプターがサポートされます。 |
共有 VHD | サポートされていません。 | サポートされていない場合、確認は失敗します。
FC ディスク | サポートされていません。 | サポートされていない場合、確認は失敗します。
BitLocker | サポートされていません。 | マシンのレプリケーションを有効にする前に、BitLocker を無効にする必要があります。
VM 名 | 1 から 63 文字。<br/> 名前に使用できるのは、英文字、数字、およびハイフンのみです。<br/><br/> マシン名の最初と最後は、文字か数字とする必要があります。 |  Site Recovery でマシンのプロパティの値を更新します。
移行後の接続 - Windows | 移行後に、Windows が実行されている Azure VM に接続するには:<br/><br/> - 移行前に、オンプレミス VM で RDP を有効にします。 TCP と UDP の規則が **[パブリック]** プロファイルに追加されていることを確認し、 **[Windows ファイアウォール]**  >  **[許可されたアプリ]** で、すべてのプロファイルで RDP が許可されていることを確認します。<br/><br/> - サイト間 VPN アクセスの場合は、RDP を有効にし、 **[Windows ファイアウォール]**  ->  **[許可されたアプリおよび機能]** で **ドメイン ネットワークとプライベート ネットワーク** の RDP を許可します。 さらに、オペレーティング システムの SAN ポリシーが **[OnlineAll]** に設定されていることを確認します。 [詳細については、こちらを参照してください](prepare-for-migration.md)。 |
移行後の接続 - Linux | 移行後に、SSH を使用して Azure VM に接続するには:<br/><br/> - 移行前に、オンプレミスのマシンで、Secure Shell サービスが [開始] に設定されていることと、ファイアウォール規則で SSH 接続が許可されていることを確認します。<br/><br/> - 移行後の Azure VM で、フェールオーバーされた VM とその接続先の Azure サブネットのネットワーク セキュリティ グループ規則について、SSH ポートへの受信接続を許可します。 さらに、VM のパブリック IP アドレスを追加します。 |  

## <a name="next-steps"></a>次のステップ

移行のために [Hyper-V VM を移行します](tutorial-migrate-hyper-v.md)。
