---
title: " Azure で実行されるプロセス サーバーの管理 (クラシック) | Microsoft Docs"
description: "この記事では、Azure でフェールバック プロセス サーバー (クラシック) を設定する方法について説明します。"
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
ms.sourcegitcommit: bb24e1c79f6a77ec157fa05a8a212109ce8ae7ad
ms.openlocfilehash: cc2d4d92f7d900f8dc231e11b65acf4b742ed893

---

# <a name="manage-a-process-server-running-in-azure-classic"></a>Azure で実行されるプロセス サーバーの管理 (クラシック)
> [!div class="op_single_selector"]
> * [Azure クラシック](./site-recovery-vmware-setup-azure-ps-classic.md)
> * [リソース マネージャー](./site-recovery-vmware-setup-azure-ps-resource-manager.md)

フェールバック中の Azure Virtual Network とオンプレミス ネットワークの間の待ち時間が大きい場合は、Azure にプロセス サーバーをデプロイすることをお勧めします。 この記事では、Azure で実行されるプロセス サーバーを設定、構成、管理する方法について説明します。

> [!NOTE]
> この記事では、フェールオーバー時の仮想マシンのデプロイメント モデルとして **[Resource Manager]** を使用していることを前提としています。 デプロイメント モデルとして **[クラシック]** を使用している場合は、[フェールバック プロセス サーバー (Resource Manager) を設定および構成する方法](./site-recovery-vmware-setup-azure-ps-resource-manager.md)に関するページの手順に従ってください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [site-recovery-vmware-process-server-prereq](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-on-azure"></a>Azure にプロセス サーバーをデプロイする

1. Azure Marketplace で、**Microsoft Azure Site Recovery Process Server V2** を使って仮想マシンを作成します。 </br>
    ![Marketplace_image_1](./media/site-recovery-vmware-setup-azure-ps-classic/marketplace-ps-image.png)
2. デプロイメント モデルとして **[クラシック]** を選択します。 </br>
  ![Marketplace_image_2](./media/site-recovery-vmware-setup-azure-ps-classic/marketplace-ps-image-classic.png)
3. 仮想マシンの作成ウィザードの [基本設定] で、サブスクリプションと、仮想マシンのフェールオーバー先の場所を選択します。</br>
  ![create_image_1](./media/site-recovery-vmware-setup-azure-ps-classic/azureps-classic-basic-info.png)
4. フェールオーバー先の仮想マシンが接続されている Azure Virtual Network に仮想マシンが接続されていることを確認します。</br>
  ![create_image_2](./media/site-recovery-vmware-setup-azure-ps-classic/azureps-classic-settings.png)
5. プロセス サーバーの仮想マシンがプロビジョニングされたら、ログインし、構成サーバーに登録する必要があります。

> [!NOTE]
> このプロセス サーバーをフェールバック用に使用できるようにするには、オンプレミスの構成サーバーに登録する必要があります。

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>(Azure で実行されている) プロセス サーバーを (オンプレミスで実行されている) 構成サーバーに登録する

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

## <a name="upgrading-the-process-server-to-latest-version"></a>プロセス サーバーを最新バージョンにアップグレードする

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-upgrade-process-server.md)]

## <a name="unregistering-the-process-server-running-in-azure-from-a-configuration-server-running-on-premises"></a>(Azure で実行されている) プロセス サーバーを (オンプレミスで実行されている) 構成サーバーから登録解除する

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]



<!--HONumber=Feb17_HO1-->


