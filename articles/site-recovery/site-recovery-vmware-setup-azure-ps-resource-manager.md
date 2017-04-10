---
title: " Azure で実行されるプロセス サーバーの管理 (Resource Manager) | Microsoft Docs"
description: "この記事では、Azure でフェールバック プロセス サーバー (Resource Manager) を設定する方法について説明します。"
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
ms.date: 2/2/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: edf190031e87cc5a433eaa8fb86187b41fc1dd4e
ms.openlocfilehash: 61db9c89ff84db89323b55990dc1049d636958f7
ms.lasthandoff: 02/22/2017

---

# <a name="manage-a-process-server-running-in-azure-resource-manager"></a>Azure で実行されるプロセス サーバーの管理 (Resource Manager)
> [!div class="op_single_selector"]
> * [リソース マネージャー](./site-recovery-vmware-setup-azure-ps-resource-manager.md)
> * [クラシック](./site-recovery-vmware-setup-azure-ps-classic.md)

フェールバック中の Azure Virtual Network とオンプレミス ネットワークの間の待ち時間が大きい場合は、Azure にプロセス サーバーをデプロイすることをお勧めします。 この記事では、Azure で実行されるプロセス サーバーを設定、構成、管理する方法について説明します。

> [!NOTE]
> この記事では、フェールオーバー時の仮想マシンのデプロイメント モデルとして **[Resource Manager]** を使用していることを前提としています。 デプロイメント モデルとして **[クラシック]** を使用している場合は、[フェールバック プロセス サーバーを設定および構成する方法 (クラシック)](./site-recovery-vmware-setup-azure-ps-classic.md) に関するページの手順に従ってください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-on-azure"></a>Azure にプロセス サーバーをデプロイする
1. [コンテナー]、**[Site Recovery インフラストラクチャ]** ([管理] 見出しの下)、**[構成サーバー]** ([For VMware and Physical Machines (VMware マシンと物理マシン)] 見出しの下) の順にクリックし、構成サーバーを選択します。
2. 表示された構成サーバーの詳細ページで、[+ プロセス サーバー] をクリックします。

  ![プロセス サーバー ギャラリーの追加](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps.png)

3.  **[プロセス サーバーの追加]** ページで、次の値を選択します。

  ![プロセス サーバー ギャラリー項目の追加](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-1.png)
|**フィールド名**|**値**|
|-|-|
|プロセス サーバーのデプロイ先を選択してください|値 **[Deploy a failback process server in Azure (フェールバック プロセス サーバーを Azure にデプロイする)]** を選択します。 |
|[サブスクリプション]|仮想マシンをフェールオーバーした Azure サブスクリプションを選択します。|
|リソース グループ|このプロセス サーバーをデプロイするリソース グループを作成できます。また、既存のリソース グループ内のプロセス サーバーをデプロイすることもできます。|
|場所|フェールオーバー先の仮想マシンがある Azure データ センターを選択します。|
|Azure ネットワーク|フェールオーバー先の仮想マシンがある Azure Virtual Network (VNet) を選択します。 仮想マシンを複数の Azure VNet にフェールオーバー場合は、VNet ごとにプロセス サーバーをデプロイする必要があります。|

4. プロセス サーバーに関する残りのプロパティを入力します。

  ![プロセス サーバーの概要の追加](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-2.png)
|**フィールド名**|**値**|
|-|-|
|サーバー名|プロセス サーバー仮想マシンの表示名およびホスト名|
| ユーザー名|その仮想マシンの管理者になるユーザーの名前|
|ストレージ アカウント|仮想マシンの仮想ディスクが配置されているストレージ アカウントの名前|
|サブネット|仮想マシンを接続する Azure VNet のサブネット|
| IP アドレス|プロセス サーバーが起動した後に割り当てられる IP アドレス|
5. [OK] をクリックして、プロセス サーバー仮想マシンのデプロイを開始します。

> [!NOTE]
> このプロセス サーバーをフェールバック用に使用できるようにするには、オンプレミスの構成サーバーに登録する必要があります。

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>(Azure で実行されている) プロセス サーバーを (オンプレミスで実行されている) 構成サーバーに登録する

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

## <a name="upgrading-the-process-server-to-latest-version"></a>プロセス サーバーを最新バージョンにアップグレードする

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-upgrade-process-server.md)]

## <a name="unregistering-the-process-server-running-in-azure-from-a-configuration-server-running-on-premises"></a>(Azure で実行されている) プロセス サーバーを (オンプレミスで実行されている) 構成サーバーから登録解除する

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

