---
title: Azure Site Recovery で VMware vCenter サーバーを管理する
description: この記事では、Azure Site Recovery を使用して VMware VM を Azure にディザスター リカバリーするために、VMware vCenter を追加して管理する方法について説明します。
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.author: ramamill
ms.openlocfilehash: 01aef3aca4f6967b1681bff9598c7dd7a24739cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84692521"
---
# <a name="manage-vmware-vcenter-server"></a>VMware vCenter Server を管理する

この記事では、[Azure Site Recovery](site-recovery-overview.md) の VMware vCenter Server での管理操作についてまとめます。

## <a name="verify-prerequisites-for-vcenter-server"></a>vCenter Server の前提条件を確認する

VMware VM を Azure にディザスター リカバリーする際の vCenter Server と VM の前提条件は、[サポート マトリックス](vmware-physical-azure-support-matrix.md#replicated-machines)に記載されています。

## <a name="set-up-an-account-for-automatic-discovery"></a>自動検出用のアカウントを設定する

オンプレミスの VMware VM のディザスター リカバリーを設定する際に、Site Recovery には vCenter Server または vSphere ホストへのアクセス権が必要になります。 アクセスできる場合、Site Recovery プロセス サーバーは自動的に VM を検出し、必要に応じてフェールオーバーできます。 既定では、プロセス サーバーは Site Recovery 構成サーバー上で実行されます。 構成サーバーのアカウントを追加して、次のように vCenter Server/vSphere ホストに接続します。

1. 構成サーバーにサインインします。
1. デスクトップ ショートカットを使用して、構成サーバー ツール (_cspsconfigtool.exe_) を開きます。
1. **[アカウントの管理]** タブの **[アカウントの追加]** をクリックします。

   ![アカウントの追加](./media/vmware-azure-manage-vcenter/addaccount.png)

1. アカウントの詳細を指定し、 **[OK]** をクリックしてそれを追加します。 アカウントには、アカウントのアクセス許可の表に要約されている特権が必要です。

   > [!NOTE]
   > アカウント情報を Site Recovery と同期するには、約 15 分かかります。

### <a name="account-permissions"></a>アカウントのアクセス許可

|**タスク** | **アカウント** | **アクセス許可** | **詳細**|
|--- | --- | --- | ---|
|**VM の検出または移行 (フェールバックなし)** | 読み取り専用以上のユーザー アカウント。 | データ センター オブジェクト -> 子オブジェクトへのプロパゲート、ロール=読み取り専用 | ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。<br/><br/> アクセスを制限するには、**子オブジェクトへの伝達** 特権を持つ **アクセスなし** ロールを子オブジェクト (vSphere ホスト、データストア、仮想マシン、ネットワーク) に割り当てます。|
|**レプリケーションまたはフェールオーバー** | 読み取り専用以上のユーザー アカウント。 | データ センター オブジェクト -> 子オブジェクトへのプロパゲート、ロール=読み取り専用 | ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。<br/><br/> アクセスを制限するには、**子オブジェクトへの伝達** 特権を持つ **アクセスなし** ロールを子オブジェクト (vSphere ホスト、データストア、仮想マシン、ネットワーク) に割り当てます。<br/><br/> 移行の目的では役立ちますが、完全なレプリケーション、フェールオーバー、フェールバックでは役立ちません。|
|**レプリケーション/フェールオーバー/フェールバック** | 必要なアクセス許可を備えたロール (AzureSiteRecoveryRole) を作成し、このロールを VMware のユーザーまたはグループに割り当てることをお勧めします。 | データ センター オブジェクト –> 子オブジェクトへの伝達、ロール = AzureSiteRecoveryRole<br/><br/> データストア -> 空間の割り当て、データストアの参照、ファイルの低レベルの操作、ファイルの削除、仮想マシン ファイルの更新<br/><br/> ネットワーク -> ネットワークの割り当て<br/><br/> リソース -> リソース プールへの VM の割り当て、電源がオフの VM の移行、電源がオンの VM の移行<br/><br/> タスク -> タスクの作成、タスクの更新<br/><br/> 仮想マシン -> 構成<br/><br/> 仮想マシン -> 対話 -> 質問への回答、デバイス接続、CD メディアの構成、フロッピー メディアの構成、電源オフ、電源オン、VMware ツールのインストール<br/><br/> 仮想マシン -> インベントリ -> 作成、登録、登録解除<br/><br/> 仮想マシン -> プロビジョニング -> 仮想マシンのダウンロードの許可、仮想マシン ファイルのアップロードの許可<br/><br/> 仮想マシン -> スナップショット -> スナップショットの削除 | ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。<br/><br/> アクセスを制限するには、**子オブジェクトへの伝達** 特権を持つ **アクセスなし** ロールを子オブジェクト (vSphere ホスト、データストア、仮想マシン、ネットワーク) に割り当てます。|

## <a name="add-vmware-server-to-the-vault"></a>コンテナーに VMware サーバーを追加する

オンプレミスの VMware VM のディザスター リカバリーを設定する場合は、次のように、Site Recovery コンテナーに対して VM を検出する vCenter Server または vSphere ホストを追加します。

1. コンテナーの **[Site Recovery Infrastructure]\(Site Recovery のインフラストラクチャ\)**  >  **[Configuration Severs]\(構成\)** で、構成サーバーを開きます。
1. **[詳細]** ページで **[vCenter]** をクリックします。
1. **[Add vCenter]\(vCenter の追加\)** で、vSphere ホストまたは vCenter サーバーのフレンドリ名を指定します。
1. サーバーの IP アドレスまたは FQDN を指定します。
1. 別のポートで要求をリッスンするように VMware サーバーが構成されている場合を除き、ポートは 443 のままにしておきます。
1. VMware vCenter または vSphere ESXi サーバーへの接続に使用するアカウントを選択します。 次に、 **[OK]** をクリックします

## <a name="modify-credentials"></a>資格情報を変更する

必要に応じて、次のように、vCenter Server または vSphere ホストへの接続に使用する資格情報を変更できます。

1. 構成サーバーにサインインします。
1. デスクトップ ショートカットを使用して、構成サーバー ツール (_cspsconfigtool.exe_) を開きます。
1. **[アカウントの管理]** タブの **[アカウントの追加]** をクリックします。

   ![アカウントの追加](./media/vmware-azure-manage-vcenter/addaccount.png)

1. 新しいアカウントの詳細を指定し、 **[OK]** をクリックします。 アカウントには、[アカウントのアクセス許可](#account-permissions)の表に記載されているアクセス許可が必要です。
1. コンテナーの **[Site Recovery Infrastructure]\(Site Recovery のインフラストラクチャ\)**  >  **[Configuration Severs]\(構成\)** で、構成サーバーを開きます。
1. **[詳細]** で **[サーバーを最新の情報に更新する]** をクリックします。
1. [サーバーを最新の情報に更新する] ジョブが完了したら、vCenter Server を選択します。
1. **[概要]** の **[vCenter server/vSphere host account]\(vCenter Server または vSphere ホスト アカウント\)** で、新しく追加したアカウントを選択し、 **[保存]** をクリックします。

   ![アカウントの変更](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>vCenter サーバーを削除する

1. コンテナーの **[Site Recovery Infrastructure]\(Site Recovery のインフラストラクチャ\)**  >  **[Configuration Severs]\(構成\)** で、構成サーバーを開きます。
1. **[詳細]** ページで、vCenter サーバーを選択します。
1. **[削除]** ボタンをクリックします。

   ![アカウントの削除](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-ip-address-and-port"></a>IP アドレスとポートを変更する

vCenter Server の IP アドレス、またはサーバーと Site Recovery 間の通信に使用されるポートを変更できます。 既定で、Site Recovery からはポート 443 を介して vCenter Server または vSphere のホスト情報にアクセスします。

1. コンテナーの **[Site Recovery Infrastructure]\(Site Recovery のインフラストラクチャ\)**  >  **[Configuration Severs]\(構成\)** で、vCenter Server を追加する先の構成サーバーをクリックします。
1. **[vCenter servers]\(vCenter サーバー\)** で、変更する vCenter Server をクリックします。
1. **[概要]** で、IP アドレスとポートを更新し、変更を保存します。

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

1. 変更を有効にするには、15 分間待つか、または[構成サーバーを更新](vmware-azure-manage-configuration-server.md#refresh-configuration-server)します。

## <a name="migrate-all-vms-to-a-new-server"></a>すべての VM を新しいサーバーに移行する

すべての VM を移行して新しい vCenter Server を使用する場合は、vCenter Server に割り当てられた IP アドレスを更新するだけで済みます。 エントリの重複につながる可能性があるため、別の VMware アカウントを追加しないでください。 次のようにアドレスを更新します。

1. コンテナーの **[Site Recovery Infrastructure]\(Site Recovery のインフラストラクチャ\)**  >  **[Configuration Severs]\(構成\)** で、vCenter Server を追加する先の構成サーバーをクリックします。
1. **[vCenter servers]\(vCenter サーバー\)** セクションで、移行元の vCenter Server をクリックします。
1. **[概要]** で、IP アドレスを新しい vCenter Server の IP アドレスに更新し、変更を保存します。
1. IP アドレスが更新されるとすぐに、Site Recovery では新しい vCenter Server からの VM 検出情報の受信が開始されます。 これは、進行中のレプリケーション アクティビティには影響しません。

## <a name="migrate-a-few-vms-to-a-new-server"></a>いくつかの VM を新しいサーバーに移行する

レプリケートするいくつかの VM のみを新しい vCenter Server に移行する場合は、次の手順を実行します。

1. 新しい vCenter Server を構成サーバーに[追加します](#add-vmware-server-to-the-vault)。
1. 新しいサーバーに移行する VM の[レプリケーションを無効にします](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)。
1. VMware で、新しい vCenter Server に VM を移行します。
1. 移行した VM の[レプリケーションを再び有効にして](vmware-azure-tutorial.md#enable-replication)、新しい vCenter Server を選択します。

## <a name="migrate-most-vms-to-a-new-server"></a>ほとんどの VM を新しいサーバーに移行する

新しい vCenter Server に移行する VM の数が、元の vCenter Server に残す VM の数よりも多い場合は、次の手順を実行します。

1. 構成サーバー設定の vCenter Server に割り当てられている [IP アドレスを新しい vCenter Server のアドレスに更新します](#modify-the-ip-address-and-port)。
1. 以前のサーバーに残すいくつかの VM の[レプリケーションを無効にします](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)。
1. 構成サーバーに[以前の vCenter Server とその IP アドレスを追加します](#add-vmware-server-to-the-vault)。
1. 以前のサーバーに残っている VM の[レプリケーションを再び有効にします](vmware-azure-tutorial.md#enable-replication)。

## <a name="next-steps"></a>次のステップ

問題が発生した場合に、[vCenter Server の検出エラーをトラブルシューティングする](vmware-azure-troubleshoot-vcenter-discovery-failures.md)。
