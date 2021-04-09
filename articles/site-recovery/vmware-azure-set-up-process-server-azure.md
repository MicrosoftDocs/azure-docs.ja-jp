---
title: Azure Site Recovery でプロセス サーバーの VMware/物理フェールバックを設定する
description: この記事では、Azure VM を VMware にフェールバックするために Azure でプロセス サーバーを設定する方法について説明します。
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: ramamill
ms.openlocfilehash: 16214fa07b14c8db2f32fa2c69739b7fa3dc1907
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008485"
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>フェールバックのために Azure でプロセス サーバーを設定する

[Site Recovery](site-recovery-overview.md) を使用して VMware VM または物理サーバーを Azure にフェールオーバーした後、再度起動して実行されたオンプレミス サイトにそれらをフェールバックできます。 フェールバックするには、Azure からオンプレミスへのレプリケーションを処理するために Azure で一時的なプロセス サーバーを設定する必要があります。 この VM は、フェールバックが完了したら削除できます。

## <a name="before-you-start"></a>開始する前に

[再保護](vmware-azure-reprotect.md)と[フェールバック](vmware-azure-failback.md) プロセスの詳細について学習します。

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]


## <a name="deploy-a-process-server-in-azure"></a>Azure でプロセス サーバーをデプロイする

1. コンテナーで **[Site Recovery インフラストラクチャ]** >  **[管理]**  >  **[構成サーバー]** の順に選択し、構成サーバーを選択します。
2. サーバー ページで、 **[+ プロセス サーバー]** をクリックします
3. **[プロセス サーバーの追加]** ページで、Azure でプロセス サーバーをデプロイすることを選択します。
4. Azure の設定を指定します。これには、フェールオーバーに使用されるサブスクリプション、リソース グループ、フェールオーバーに使用される Azure リージョン、および Azure VM が配置される仮想ネットワークが含まれます。 複数の Azure ネットワークを使用している場合は、それぞれにプロセス サーバーが必要です。

   ![プロセス サーバー ギャラリー アイテムの追加](./media/vmware-azure-set-up-process-server-azure/add-ps-page-1.png)

4. **[サーバー名]** 、 **[ユーザー名]** 、および **[パスワード]** で、プロセス サーバーの名前と、サーバー上で管理者のアクセス許可が割り当てられる資格情報を指定します。
5. サーバー VM ディスクに使用されるストレージ アカウント、プロセス サーバー VM が配置されるサブネット、および VM の起動時に割り当てられるサーバー IP アドレスを指定します。
6. **[OK]** ボタンをクリックして、プロセス サーバー VM のデプロイを開始します。 プロセス サーバーは Standard_A8_v2 SKU にデプロイされます。 この VM SKU がサブスクリプションで使用可能であることを確認してください。

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>(Azure で実行されている) プロセス サーバーを (オンプレミスで実行されている) 構成サーバーに登録する

プロセス サーバー VM が起動して実行されたら、それをオンプレミスの構成サーバーに次のように登録する必要があります。

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


