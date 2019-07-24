---
title: Azure Site Recovery を使用してオンプレミス マシンのディザスター リカバリーを準備する | Microsoft Docs
description: Azure Site Recovery を使用してオンプレミス マシンのディザスター リカバリーのために Azure を準備する方法について説明します。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 3d2b22fc507b209a96870daa8bf12ea9ab60a466
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617416"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>オンプレミス マシンのディザスター リカバリーのために Azure リソースを準備する

この記事では、[Azure Site Recovery](site-recovery-overview.md) サービスを使用してオンプレミスの VMware VM、Hyper-V VM、または Windows/Linux 物理サーバーの Azure へのディザスター リカバリーを設定できるように、Azure のリソースとコンポーネントを準備する方法について説明します。

この記事は、オンプレミスの VM のディザスター リカバリーを設定する方法について説明するシリーズの 1 番目のチュートリアルです。 


このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure アカウントにレプリケーションのアクセス許可があることを確認します。
> * Recovery Services コンテナーを作成する。 コンテナーには、VM および他のレプリケーション コンポーネントのメタデータと構成情報が保持されます。
> * Azure 仮想ネットワーク (VNet) を設定します。 フェールオーバー後に作成された Azure VM は、このネットワークに参加します。

> [!NOTE]
> チュートリアルでは、シナリオの最も簡単なデプロイ パスを示します。 可能であれば既定のオプションを使い、すべての可能な設定とパスを示してはいません。 詳細な手順については、Site Recovery の目次の操作方法のセクションにある記事を参照してください。

## <a name="before-you-start"></a>開始する前に

- [VMware](vmware-azure-architecture.md)、[Hyper-V](hyper-v-azure-architecture.md)、および[物理サーバー](physical-azure-architecture.md)のディザスター リカバリーのアーキテクチャを確認します。
- [VMware](vmware-azure-common-questions.md) および Hyper-V(hyper-v-azure-common-questions.md) に関するよく寄せられる質問を確認します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。 次に、[Azure Portal](https://portal.azure.com) にサインインします。


## <a name="verify-account-permissions"></a>アカウントのアクセス許可を確認する

無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの管理者になっていて、必要なアクセス許可を持っています。 サブスクリプションの管理者でなければ、管理者に協力を求め、必要なアクセス許可を割り当てます。 新しい仮想マシンのレプリケーションを有効にするには、次を実行するためのアクセス許可が必要です。

- 選択したリソース グループ内に VM を作成する。
- 選択した仮想ネットワーク内に VM を作成する。
- Azure Storage アカウントに書き込む。
- Azure マネージド ディスクに書き込む。

これらのタスクを遂行するには、お使いのアカウントに、"仮想マシン共同作成者" 組み込みロールが割り当てられている必要があります。 また、Site Recovery 操作をコンテナーで管理するには、お使いのアカウントに、"Site Recovery 共同作成者" 組み込みロールが割り当てられている必要があります。


## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

1. Azure portal で **[+ リソースの作成]** をクリックし、Marketplace で "**Recovery**" を検索します。
2. **[Backup and Site Recovery (OMS)]** をクリックし、[Backup and Site Recovery] ページで **[作成]** をクリックします。 
1. **[Recovery Services コンテナー]** > **[名前]** に、コンテナーを識別するフレンドリ名を入力します。 この一連のチュートリアルでは、**ContosoVMVault** を使用します。
2. **[リソース グループ]** で、既存のリソース グループを選択するか、新しいリソース グループを作成します。 このチュートリアルでは **contosoRG** を使用しています。
3. **[場所]** で、コンテナーを配置するリージョンを選びます。 **[西ヨーロッパ]** を使います。
4. ダッシュボードから資格情報コンテナーにすばやくアクセスするには、**[ダッシュボードにピン留めする]** > **[作成]** の順に選択します。

   ![新しい資格情報コンテナーの作成](./media/tutorial-prepare-azure/new-vault-settings.png)

   新しい資格情報コンテナーは、**[ダッシュボード]** > **[すべてのリソース]** と、メインの **[Recovery Services コンテナー]** ページに表示されます。

## <a name="set-up-an-azure-network"></a>Azure ネットワークをセットアップ

オンプレミスのマシンは Azure マネージド ディスクにレプリケートされます。 フェールオーバーが発生すると、Azure VM がこれらのマネージド ディスクから作成され、この手順で指定した Azure ネットワークに参加します。

1. [Azure Portal](https://portal.azure.com) で、**[リソースの作成]** > **[ネットワーク]** > **[仮想ネットワーク]** の順に選択します。
2. デプロイ モデルとして **[リソース マネージャー]** をそのまま選択します。
3. **[名前]** で、ネットワーク名を入力します。 Azure リソース グループ内で一意となる名前を使用してください。 このチュートリアルでは **ContosoASRnet** を使います。
4. ネットワークを作成するリソース グループを指定します。 既存のリソース グループ **contosoRG** を使っています。
5. **[アドレス範囲]** に、ネットワークの範囲を入力します。 ここでは、サブネットを使用せずに **10.1.0.0/24** を使用しています。
6. **[サブスクリプション]** で、ネットワークを作成するサブスクリプションを選択します。
7. **[場所]** で、Recovery Services コンテナーが作成されたリージョンと同じリージョンを選択します。 このチュートリアルでは、"**西ヨーロッパ**" を使用します。 ネットワークは、コンテナーと同じリージョンにある必要があります。
8. 基本的な DDoS 保護の既定のオプションのままにし、ネットワーク上にサービス エンドポイントはありません。
9. **Create** をクリックしてください。

   ![仮想ネットワークの作成](media/tutorial-prepare-azure/create-network.png)

仮想ネットワークの作成には数秒かかります。 作成が完了すると、Azure Portal ダッシュボードに表示されます。




## <a name="next-steps"></a>次の手順

- VMware のディザスター リカバリーの場合は、[オンプレミスの VMware インフラストラクチャを準備します](tutorial-prepare-on-premises-vmware.md)。
- Hyper-V のディザスター リカバリーの場合は、[オンプレミスの Hyper-V サーバーを準備します](hyper-v-prepare-on-premises-tutorial.md)。
- 物理サーバーのディザスター リカバリーの場合は、[構成サーバーとソース環境を設定します](physical-azure-disaster-recovery.md)
- [Azure ネットワーク](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)についての学習。
- [マネージド ディスク](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)についての学習。
