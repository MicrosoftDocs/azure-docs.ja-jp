---
title: "Azure Site Recovery で使用するリソースを作成する | Microsoft Docs"
description: "Azure Site Recovery サービスを利用したオンプレミス マシンのレプリケーションために Azure を準備する方法について説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 321e304f-b29e-49e4-aa64-453878490ea7
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: 506b625905abf52963230a787af66f956bc292b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="prepare-azure-resources-for-replication-of-on-premises-machines"></a>オンプレミス マシンのレプリケーションために Azure リソースを準備する

[Azure Site Recovery](site-recovery-overview.md) サービスは、計画された停止や計画外の停止の際にビジネス アプリを実行し続け、使用できるようにすることで、ビジネス継続性とディザスター リカバリー (BCDR) 戦略に貢献します。 Site Recovery は、レプリケーション、フェールオーバー、フェールバックなど、オンプレミスのマシンと Azure Virtual Machines (VM) のディザスター リカバリーを管理し、調整します。

このチュートリアルでは、オンプレミス VM や物理サーバーを Azure にレプリケートするときの Azure コンポーネントを準備する方法について説明します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * お使いのアカウントでレプリケーションがアクセス許可されていることを確認します。
> * Azure ストレージ アカウントを作成します。
> * Azure ネットワークを設定します。 フェールオーバー後に作成された Azure VM は、この Azure ネットワークに参加します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。

## <a name="log-in-to-azure"></a>Azure へのログイン

Azure Portal (http://portal.azure.com) にログインします。

## <a name="verify-account-permissions"></a>アカウントのアクセス許可を確認する

無料 Azure アカウントを作成したところであれば、自分のサブスクリプションの管理者になっています。 サブスクリプションの管理者でなければ、管理者に協力をあおぎ、必要なアクセス許可を割り当てます。 新しい仮想マシンのレプリケーションを有効にするには、次のアクセス許可が必要です。

- 選択されたリソース グループ内に仮想マシンを作成するためのアクセス許可
- 選択された仮想ネットワーク内に仮想マシンを作成するためのアクセス許可
- 選択したストレージ アカウントに書き込むためのアクセス許可

'仮想マシン共同作成者' 組み込みロールには以上のアクセス許可が与えられています。 Azure Site Recovery 操作を管理するためのアクセス許可も必要です。 'Site Recovery 共同作成者' ロールには、Recovery Services コンテナーでの Site Recovery の操作の管理に必要なすべてのアクセス許可があります。

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

レプリケートされたマシンのイメージは Azure Storage に保存されます。 オンプレミスから Azure にフェールオーバーするとき、ストレージから Azure VM が作成されます。

1. [Azure Portal](https://portal.azure.com) メニューで、**[新規]** -> **[ストレージ]** -> **[ストレージ アカウント]** の順にクリックします。
2. ストレージ アカウントの名前を入力します。 この一連のチュートリアルでは、**contosovmsacct1910171607** という名前を使用します。 名前は Azure 内で一意にする必要があります。長さは 3 ～ 24 文字で、使用できるのは数字と小文字のみです。
3. **Resource Manager** デプロイメント モデルを使用する。
4. **[汎用]**、 > **[標準]** の順に選択します。
5. ストレージの冗長性には既定の **[RA-GRS]** を選択します。
6. 新しいストレージ アカウントを作成するサブスクリプションを選択します。
7. 新しいリソース グループを指定します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 この一連のチュートリアルでは、**ContosoRG** という名前を使用します。
8. ストレージ アカウントの地理的な場所を選択します。 ストレージ アカウントは、Recovery Services コンテナーと同じリージョンに存在する必要があります。 この一連のチュートリアルでは、場所を**西ヨーロッパ**にします。

   ![create-storageacct](media/tutorial-prepare-azure/create-storageacct.png)

9. **[作成]** をクリックしてストレージ アカウントを作成します。

## <a name="create-a-vault"></a>コンテナーの作成

1. Azure Portal メニューで、**[新規]**、>**[監視 + 管理]**、>
   **[Backup and Site Recovery]** の順にクリックします。
2. **[名前]**に、コンテナーを識別するフレンドリ名を入力します。 このチュートリアルでは **ContosoVMVault** を使用します。
3. **contosoRG** という名前の既存のリソース グループを選択します。
4. Azure リージョンに **[西ヨーロッパ]** を指定します。
5. ダッシュボードからコンテナーにすばやくアクセスするには、**[ダッシュボードにピン留めする]**、 > **[作成]** の順にクリックします。

   ![新しいコンテナー](./media/tutorial-prepare-azure/new-vault-settings.png)

   新しいコンテナーは、**[ダッシュボード]** > **[すべてのリソース]** と、メインの **[Recovery Services コンテナー]** ページに表示されます。

## <a name="set-up-an-azure-network"></a>Azure ネットワークをセットアップする

フェールオーバー後にストレージから作成された Azure VM は、このネットワークに参加します。

1. [[Azure Portal]](https://portal.azure.com) メニューで、**[新規]**、>**[ネットワーク]**、>
   **[仮想ネットワーク]** の順にクリックします。
2. デプロイ モデルとして **[リソース マネージャー]** をそのまま選択します。 [リソース マネージャー] が推奨されるデプロイ モデルです。
   - ネットワーク名を指定します。 Azure リソース グループ内で一意となる名前を使用してください。 **ContosoASRnet** という名前を使用します。
   - 既存のリソース グループ **contosoRG** を選択します。
   - ネットワーク アドレス範囲として 10.0.0.0/24 を指定します。
   - このチュートリアルでは、サブネットは必要ありません。
   - ネットワークを作成するサブスクリプションを選択します。
   - 場所には **[西ヨーロッパ]** を選択します。 ネットワークは、Recovery Services コンテナーと同じリージョンにある必要があります。
3. **Create** をクリックしてください。

   ![create-network](media/tutorial-prepare-azure/create-network.png)

   仮想ネットワークの作成には数秒かかります。 作成後、Azure Portal ダッシュボードに表示されます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure にディザスター リカバリーするためにオンプレミス VMware インフラストラクチャを準備する](tutorial-prepare-on-premises-vmware.md)
