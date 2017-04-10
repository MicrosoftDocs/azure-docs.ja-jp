---
title: " Azure Site Recovery での VMware vCenter サーバーの管理 | Microsoft Docs"
description: "この記事では、Azure Site Recovery でVMware vCenter を追加して管理する方法について説明します。"
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 2/14/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: 96e6696818a0de2fadd55ff7e0ccee350d2666ad
ms.openlocfilehash: 5578dea457f3eeda72e3a1e4e61382cdf0de285d
ms.lasthandoff: 02/22/2017

---

# <a name="manage-vmware-vcenter-server-in-azure-site-recovery"></a>Azure Site Recovery での VMware vCenter サーバーの管理
この記事では、VMware vCenter で実行できるさまざまな Site Recovery 操作について説明します。

## <a name="prerequisites"></a>前提条件

**VMware vCenter および VMware vSphere ESX ホストのサポート** | **詳細**
--- | ---
**オンプレミスの VMware サーバー** | 1 台以上の VMware vSphere サーバー (6.0、5.5、または 5.1 を実行し、最新の更新プログラムがインストール済みであること)。 サーバーは、構成サーバー (または別のプロセス サーバー) と同じネットワークに配置する必要があります。<br/><br/> vCenter サーバー (6.0 または 5.5 を実行し、最新の更新プログラムがインストール済みであること) を使用してホストを管理することをお勧めします。 バージョン 6.0 をデプロイする場合は、5.5 で利用できる機能のみがサポートされます。

## <a name="prepare-an-account-for-automatic-discovery"></a>自動検出用のアカウントを準備する
Site Recovery は、プロセス サーバーで仮想マシンを自動検出するため、また仮想マシンのフェールオーバーおよびフェールバックのために、VMware にアクセスできる必要があります。

* **移行**: フェールバックせずに VMware 仮想マシンを Azure に移行するだけの場合は、読み取り専用のロールを持つ VMware アカウントを使用することができます。 このようなロールではフェールオーバーを実行できますが、保護されたソース マシンをシャットダウンすることはできません。 これは移行では必要ありません。
* **レプリケート/回復**: 完全なレプリケーション (レプリケーション、フェールオーバー、フェールバック) をデプロイする場合は、そのアカウントでディスクの作成および削除や仮想マシンの電源投入などの操作を実行できる必要があります。
* **自動検出**: 少なくとも読み取り専用のアカウントが必要です。


|**タスク** | **必要なアカウントとロール** | **アクセス許可** | **詳細**|
|--- | --- | --- | ---|
|**プロセス サーバーによる VMware 仮想マシンの自動検出** | 少なくとも読み取り専用ユーザーが必要です。 | データ センター オブジェクト -> 子オブジェクトへのプロパゲート、ロール=読み取り専用 | ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。<br/><br/> アクセスを制限するには、**子オブジェクトへの伝達**特権を持つ**アクセスなし**ロールを子オブジェクト (vSphere ホスト、データストア、仮想マシン、ネットワーク) に割り当てます。|
|**フェールオーバー** | 少なくとも読み取り専用ユーザーが必要です。 | データ センター オブジェクト -> 子オブジェクトへのプロパゲート、ロール=読み取り専用 | ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。<br/><br/> アクセスを制限するには、**子オブジェクトへの伝達**特権を持つ**アクセスなし**ロールを子オブジェクト (vSphere ホスト、データストア、仮想マシン、ネットワーク) に割り当てます。<br/><br/> 移行の目的では役立ちますが、完全なレプリケーション、フェールオーバー、フェールバックでは役立ちません。|
|**フェールオーバーとフェールバック** | 必要なアクセス許可を備えたロール (AzureSiteRecoveryRole) を作成し、このロールを VMware のユーザーまたはグループに割り当てることをお勧めします。 | データ センター オブジェクト –> 子オブジェクトへの伝達、ロール = AzureSiteRecoveryRole<br/><br/> データストア -> 空間の割り当て、データストアの参照、ファイルの低レベルの操作、ファイルの削除、仮想マシン ファイルの更新<br/><br/> ネットワーク -> ネットワークの割り当て<br/><br/> リソース -> リソース プールへの VM の割り当て、電源がオフの VM の移行、電源がオンの VM の移行<br/><br/> タスク -> タスクの作成、タスクの更新<br/><br/> 仮想マシン -> 構成<br/><br/> 仮想マシン -> 対話 -> 質問への回答、デバイス接続、CD メディアの構成、フロッピー メディアの構成、電源オフ、電源オン、VMware ツールのインストール<br/><br/> 仮想マシン -> インベントリ -> 作成、登録、登録解除<br/><br/> 仮想マシン -> プロビジョニング -> 仮想マシンのダウンロードの許可、仮想マシン ファイルのアップロードの許可<br/><br/> 仮想マシン -> スナップショット -> スナップショットの削除 | ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。<br/><br/> アクセスを制限するには、**子オブジェクトへの伝達**特権を持つ**アクセスなし**ロールを子オブジェクト (vSphere ホスト、データストア、仮想マシン、ネットワーク) に割り当てます。|

## <a name="create-an-account-to-connect-to-vmware-vcenter-server-vmware-vsphere-exsi-host"></a>アカウントを作成して VMware vCenter Server/VMware vSphere EXSi ホストに接続する
1. 構成サーバーにログインして、デスクトップのショートカットを使用して cspsconfigtool.exe を起動します。
2. **[アカウントの管理]** タブの **[アカウントの追加]** をクリックします。

  ![アカウントの追加](./media/site-recovery-vmware-to-azure-manage-vcenter/addaccount.png)
3. アカウント情報を入力し、[OK] をクリックしてアカウントを追加します。 このアカウントには、「[自動検出用のアカウントを準備する](#prepare-an-account-for-automatic-discovery)」セクションに記載されている権限が必要です。

  >[!NOTE]
  アカウント情報を Site Recovery サービスと同期するには、15 分程度かかります。


## <a name="associate-a-vmware-vcenter-vmware-vsphere-esx-host-add-vcenter"></a>VMware vCenter/VMware vSphere ESX ホストの関連付け (vCenter の追加)
* Azure Portal で、*Recovery Services コンテナー* > **[Site Recovery インフラストラクチャ]** > **[構成サーバー]** > *構成サーバー*の順に移動します。
* 構成サーバーの詳細ページで、[+vCenter] ボタンをクリックします。

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials-used-to-connect-to-the-vcenter-server-vsphere-esxi-host"></a>vCenter サーバー/vSphere ESXi ホストへの接続に使用する資格情報を変更する

1. 構成サーバーにログインして、cspsconfigtool.exe を起動します。
2. **[アカウントの管理]** タブの **[アカウントの追加]** をクリックします。

  ![アカウントの追加](./media/site-recovery-vmware-to-azure-manage-vcenter/addaccount.png)
3. 新しいアカウントの情報を入力し、[OK] をクリックしてアカウントを追加します。 このアカウントには、「[自動検出用のアカウントを準備する](#prepare-an-account-for-automatic-discovery)」セクションに記載されている権限が必要です。
4. Azure Portal で、*Recovery Services コンテナー* > **[Site Recovery インフラストラクチャ]** > **[構成サーバー]** > *構成サーバー*の順に移動します。
5. 構成サーバーの詳細ページで、**[サーバーを最新の情報に更新する]** ボタンをクリックします。
6. 更新サーバーのジョブが完了したら、vCenter サーバーを選択して、[vCenter Summary (vCenter の概要)] ページを開きます。
7. **[vCenter Server/vSphere Server host account (vCenter サーバー/vSphere サーバー ホスト アカウント)]** フィールドで、新しく追加したアカウントを選択して、**[保存]** をクリックします。

  ![アカウントの変更](./media/site-recovery-vmware-to-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-in-azure-site-recovery"></a>Azure Site Recovery での vCenter の削除
1. Azure Portal で、*Recovery Services コンテナー* > **[Site Recovery インフラストラクチャ]** > **[構成サーバー]** > *構成サーバー*の順に移動します。
2. 構成サーバーの詳細ページで vCenter サーバーを選択して、[vCenter Summary (vCenter の概要)] ページを開きます。
3. **[削除]** ボタンをクリックして、vCenter を削除します。

  ![アカウントの削除](./media/site-recovery-vmware-to-azure-manage-vcenter/delete-vcenter.png)

> [!NOTE]
vCenter の IP アドレス/FQDN、ポートの詳細を変更する場合は、vCenter サーバーを削除して、再度追加する必要があります。

