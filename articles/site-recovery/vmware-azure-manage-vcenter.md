---
title: Azure Site Recovery を使用した VMware VM の Azure へのディザスター リカバリーのために VMware vCenter サーバーを管理する | Microsoft Docs
description: この記事では、Azure Site Recovery を使用した VMware VM の Azure へのディザスター リカバリーのために VMware vCenter を追加して管理する方法について説明します。
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ramamill
ms.openlocfilehash: 9694c682f171ab715812b05fed2064c9bbcd36b3
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518084"
---
# <a name="manage-vmware-vcenter-server"></a>VMware vCenter サーバーを管理する

この記事では、VMware vCenter で実行できるさまざまな Site Recovery 操作について説明します。 開始する前に、[前提条件](vmware-physical-azure-support-matrix.md#replicated-machines)を確認してください。


## <a name="set-up-an-account-for-automatic-discovery"></a>自動検出用のアカウントを設定する

Site Recovery は、プロセス サーバーで仮想マシンを自動検出するため、また仮想マシンのフェールオーバーおよびフェールバックのために、VMware にアクセスできる必要があります。 アクセス用のアカウントを次のように作成します。

1. 構成サーバー マシンにログオンします。
2. デスクトップのショートカットを使用して cspsconfigtool.exe を起動します。
3. **[アカウントの管理]** タブの **[アカウントの追加]** をクリックします。

   ![アカウントの追加](./media/vmware-azure-manage-vcenter/addaccount.png)
1. アカウントの詳細を指定し、**[OK]** をクリックしてそれを追加します。  このアカウントには、次の表に要約されている特権が必要です。 

アカウント情報を Site Recovery サービスと同期するには、15 分程度かかります。

### <a name="account-permissions"></a>アカウントのアクセス許可

|**タスク** | **アカウント** | **アクセス許可** | **詳細**|
|--- | --- | --- | ---|
|**自動検出/移行 (フェールバックなし)** | 少なくとも読み取り専用ユーザーが必要です。 | データ センター オブジェクト -> 子オブジェクトへのプロパゲート、ロール=読み取り専用 | ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。<br/><br/> アクセスを制限するには、**子オブジェクトへの伝達**特権を持つ**アクセスなし**ロールを子オブジェクト (vSphere ホスト、データストア、仮想マシン、ネットワーク) に割り当てます。|
|**レプリケーション/フェールオーバー** | 少なくとも読み取り専用ユーザーが必要です。| データ センター オブジェクト -> 子オブジェクトへのプロパゲート、ロール=読み取り専用 | ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。<br/><br/> アクセスを制限するには、**子オブジェクトへの伝達**特権を持つ**アクセスなし**ロールを子オブジェクト (vSphere ホスト、データストア、仮想マシン、ネットワーク) に割り当てます。<br/><br/> 移行の目的では役立ちますが、完全なレプリケーション、フェールオーバー、フェールバックでは役立ちません。|
|**レプリケーション/フェールオーバー/フェールバック** | 必要なアクセス許可を備えたロール (AzureSiteRecoveryRole) を作成し、このロールを VMware のユーザーまたはグループに割り当てることをお勧めします。 | データ センター オブジェクト –> 子オブジェクトへの伝達、ロール = AzureSiteRecoveryRole<br/><br/> データストア -> 空間の割り当て、データストアの参照、ファイルの低レベルの操作、ファイルの削除、仮想マシン ファイルの更新<br/><br/> ネットワーク -> ネットワークの割り当て<br/><br/> リソース -> リソース プールへの VM の割り当て、電源がオフの VM の移行、電源がオンの VM の移行<br/><br/> タスク -> タスクの作成、タスクの更新<br/><br/> 仮想マシン -> 構成<br/><br/> 仮想マシン -> 対話 -> 質問への回答、デバイス接続、CD メディアの構成、フロッピー メディアの構成、電源オフ、電源オン、VMware ツールのインストール<br/><br/> 仮想マシン -> インベントリ -> 作成、登録、登録解除<br/><br/> 仮想マシン -> プロビジョニング -> 仮想マシンのダウンロードの許可、仮想マシン ファイルのアップロードの許可<br/><br/> 仮想マシン -> スナップショット -> スナップショットの削除 | ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。<br/><br/> アクセスを制限するには、**子オブジェクトへの伝達**特権を持つ**アクセスなし**ロールを子オブジェクト (vSphere ホスト、データストア、仮想マシン、ネットワーク) に割り当てます。|


## <a name="add-vmware-server-to-the-vault"></a>コンテナーに VMware サーバーを追加する

1. Azure Portal で、コンテナー > **[Site Recovery インフラストラクチャ]** > **[Configuration Severs] (構成サーバー)** を開き、構成サーバーを開きます。
2. **[詳細]** ページで、**[+vCenter]** をクリックします。

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>資格情報を変更する

vCenter サーバーまたは ESXi ホストに接続するために使用される資格情報を次のように変更します。

1. 構成サーバーにログオンし、デスクトップから cspsconfigtool.exe を起動します。
2. **[アカウントの管理]** タブの **[アカウントの追加]** をクリックします。

   ![アカウントの追加](./media/vmware-azure-manage-vcenter/addaccount.png)
3. 新しいアカウントの詳細を指定し、**[OK]** をクリックしてそれを追加します。 このアカウントには、[上に](#account-permissions)一覧表示されている特権が必要です。
4. Azure Portal で、コンテナー > **[Site Recovery インフラストラクチャ]** > **[Configuration Severs] (構成サーバー)** を開き、構成サーバーを開きます。
5. **[詳細]** ページで、**[サーバーを最新の情報に更新する]** をクリックします。
6. [サーバーを最新の情報に更新する] ジョブが完了したら、vCenter サーバーを選択して vCenter の **[概要]** ページを開きます。
7. **[vCenter server/vSphere host account] (vCenter サーバー/vSphere ホストのアカウント)** フィールドで新しく追加されたアカウントを選択し、**[保存]** をクリックします。

   ![アカウントの変更](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>vCenter サーバーを削除する

1. Azure Portal で、コンテナー > **[Site Recovery インフラストラクチャ]** > **[Configuration Severs] (構成サーバー)** を開き、構成サーバーを開きます。
2. **[詳細]** ページで、vCenter サーバーを選択します。
3. **[削除]** ボタンをクリックします。

   ![アカウントの削除](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-vcenter-ip-address-and-port"></a>vCenter の IP アドレスとポートを変更する

1. Azure ポータルにサインインします。
2. **[Recovery Services コンテナー]** > **[Site Recovery インフラストラクチャ]** > **[構成サーバー]** の順に移動します。
3. vCenter の割り当て先とする構成サーバーをクリックします。
4. **[vCenter サーバー]** セクションで、変更する vCenter をクリックします。
5. vCenter の概要ページで、vCenter の IP アドレスとポートをそれぞれのフィールドで更新し、次に変更内容を保存します。

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

6. 変更を有効にするには、15 分間待つか、または[構成サーバーを更新](vmware-azure-manage-configuration-server.md#refresh-configuration-server)します。

## <a name="migrate-all-protected-virtual-machines-to-a-new-vcenter"></a>保護されているすべての仮想マシンを、新しい vCenter に移行する

すべての仮想マシンを新しい vCenter に移行する場合に、別の vCenter アカウントを追加しないでください。 それを行うと、エントリの重複を招く可能性があります。 新しい vCenter の IP アドレスを更新するだけとします。

1. Azure ポータルにサインインします。
2. **[Recovery Services コンテナー]** > **[Site Recovery インフラストラクチャ]** > **[構成サーバー]** の順に移動します。
3. 古い vCenter の割り当て先とする構成サーバーをクリックします。
4. **[vCenter サーバー]** セクションで、移行元の vCenter をクリックします。
5. vCenter の概要ページで、**[vCenter サーバー/vSphere ホスト名または IP アドレス]** フィールドで新しい vCenter の IP アドレスを更新します。 変更を保存します。

IP アドレスが更新されるとすぐに、Site Recovery コンポーネントでは新しい vCenter から仮想マシンの検出情報を受信し始めます。 これは、進行中のレプリケーション アクティビティに影響しません。

## <a name="migrate-few-protected-virtual-machines-to-a-new-vcenter"></a>保護されたいくつかの仮想マシンを新しい vCenter に移行する

> [!NOTE]
> このセクションは、ご利用の保護された仮想マシンのいくつかを新しい vCenter 移行する場合にのみ適用可能です。 新しい vCenter から新しい一連の仮想マシンを保護する場合は、[新しい vCenter の詳細を構成サーバーに追加](#add-vmware-server-to-the-vault)し、**[保護の有効化](vmware-azure-tutorial.md#enable-replication)** を開始します。

仮想マシンのいくつかを新しい vCenter に移動するには: 

1. [新しい vCenter の詳細を構成サーバーに追加](#add-vmware-server-to-the-vault)します。
2. 移行する[仮想マシンのレプリケーションを無効](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)にします。
3. 新しい vCenter への選択した仮想マシンの移行を完了します。
4. 次に、[保護を有効にする際に新しい vCenter を選択](vmware-azure-tutorial.md#enable-replication)して、移行された仮想マシンを保護します。

> [!TIP]
> 移行される仮想マシンの数が古い vCenter に保持されている仮想マシンの数**より多い**場合は、ここに記載された手順を使用して新しい vCenter の IP アドレスを更新します。 古い vCenter 上に保持されているいくつかの仮想マシンについては、[レプリケーションを無効](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)にし、[新しい vCenter の詳細を構成サーバーに追加](#add-vmware-server-to-the-vault)し、**[保護の有効化](vmware-azure-tutorial.md#enable-replication)** を開始します。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

1. 保護された仮想マシンを ESXi ホスト間で移動する場合、これはレプリケーションに影響するでしょうか?

    いいえ。進行中のレプリケーションには影響ありません。 ただし、[十分な特権を使用してマスター ターゲット サーバーを確実にデプロイしてください](vmware-azure-reprotect.md#deploy-a-separate-master-target-server)

2. vCenter とその他の Site Recovery コンポーネントとの通信には、どのようなポート番号が使用されていますか?

    既定のポートは 443 です。 構成サーバーはこのポートを介して vCenter/vSphere のホスト情報にアクセスします。 この情報を更新する場合は、[こちら](#modify-the-vcenter-ip-address-and-port)をクリックします。
