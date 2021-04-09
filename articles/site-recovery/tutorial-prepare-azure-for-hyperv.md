---
title: Azure Site Recovery を使用して Hyper-V のディザスター リカバリーのために Azure を準備する
description: Azure Site Recovery を使用してオンプレミスの Hyper-V VM のディザスター リカバリーのために Azure を準備する方法について説明します。
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 5ca03336fc60abbb458615e922a2556dd21a79a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739680"
---
# <a name="prepare-azure-resources-for-hyper-v-disaster-recovery"></a>Hyper-V のディザスター リカバリーのために Azure リソースを準備する

 [Azure Site Recovery](site-recovery-overview.md) は、計画された停止や計画外の停止の際にビジネス アプリを実行し続けることで、ビジネス継続性とディザスター リカバリー (BCDR) を支援します。 Site Recovery は、レプリケーション、フェールオーバー、フェールバックなど、オンプレミスのマシンと Azure Virtual Machines (VM) のディザスター リカバリーを管理し、調整します。

このチュートリアルは、オンプレミスの Hyper-V VM のディザスター リカバリーを設定する方法について説明するシリーズの 1 番目のチュートリアルです。

> [!NOTE]
> これらのチュートリアルは、シナリオの最も簡単なデプロイ パスを示すことを目的として作成しました。 これらのチュートリアルでは、可能であれば既定のオプションが使用され、可能な設定とパスがすべて示されているわけではありません。 詳細については、対応する各シナリオの「方法」に関するセクションを参照してください。

このチュートリアルでは、オンプレミスの VM (Hyper-V) を Azure にレプリケートするときに Azure コンポーネントを準備する方法について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * お使いの Azure アカウントにレプリケーションのアクセス許可があることを確認します。
> * レプリケートされたマシンのイメージを格納する Azure Storage アカウントを作成します。
> * VM およびその他のレプリケーション コンポーネントのメタデータと構成情報を格納する Recovery Services コンテナーを作成します。
> * Azure ネットワークをセットアップします。 フェールオーバー後に作成された Azure VM は、このネットワークに参加します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。

## <a name="sign-in"></a>サインイン

[Azure portal](https://portal.azure.com) にサインインする

## <a name="verify-account-permissions"></a>アカウントのアクセス許可を確認する

無料の Azure アカウントを作成したばかりであれば、自分がそのサブスクリプションの管理者になっています。 管理者でなければ、管理者に協力を求め、必要なアクセス許可を割り当てます。 新しい仮想マシンのレプリケーションを有効にするには、次を実行するためのアクセス許可が必要です。

- 選択したリソース グループ内に VM を作成する。
- 選択した仮想ネットワーク内に VM を作成する。
- 選択したストレージ アカウントに書き込む。

これらのタスクを遂行するには、お使いのアカウントに、"仮想マシン共同作成者" 組み込みロールが割り当てられている必要があります。 Site Recovery 操作をコンテナーで管理するには、お使いのアカウントに、"Site Recovery 共同作成者" 組み込みロールが割り当てられている必要があります。

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

レプリケートされたマシンのイメージは Azure Storage に保存されます。 オンプレミスから Azure にフェールオーバーするとき、ストレージから Azure VM が作成されます。 ストレージ アカウントは、Recovery Services コンテナーと同じリージョンに存在する必要があります。

1. [Azure portal](https://portal.azure.com) のメニューで、 **[リソースの作成]**  >  **[Storage]**  >  **[ストレージ アカウント - Blob、File、Table、Queue]** の順に選択します。
2. **[ストレージ アカウントの作成]** で、アカウントの名前を入力します。  選択する名前は Azure 内で一意で、3 から 24 文字の長さで、小文字と数字のみを使用する必要があります。 このチュートリアルでは **contosovmsacct1910171607** を使用します。
3. **[デプロイ モデル]** で、 **[Resource Manager]** を選択します。
4. **[アカウントの種類]** で **[ストレージ (汎用 v1)]** を選択します。 Blob ストレージを選択しないでください。
5. **[レプリケーション]** では、ストレージの冗長性のために、既定の **[読み取りアクセス geo 冗長ストレージ]** を選択します。
6. **[パフォーマンス]** で **[Standard]** を選択します。 次に、 **[アクセス層]** で、既定のオプションの **[ホット]** を選択します。
7. **[サブスクリプション]** で、新しいストレージ アカウントを作成するサブスクリプションを選択します。
8. **[リソース グループ]** で、新しいリソース グループ名を入力します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 このチュートリアルでは **ContosoRG** を使用します。
9. **[場所]** で、ストレージ アカウントの地理的な場所を選択します。 このチュートリアルでは、"**西ヨーロッパ**" を使用します。
10. **[作成]** をクリックしてストレージ アカウントを作成します。

   ![[ストレージ アカウントの作成] のオプションのスクリーンショット。](media/tutorial-prepare-azure/create-storageacct.png)

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーの作成

1. Azure portal で **[+ リソースの作成]** を選択し、Azure Marketplace で "Recovery services" を検索します。
2. **[Backup and Site Recovery (OMS)]** を選択します。 次に、 **[Backup and Site Recovery]** ページで **[作成]** を選択します。
1. **[Recovery Services コンテナー] > [名前]** に、コンテナーを識別するフレンドリ名を入力します。 このチュートリアルでは **ContosoVMVault** を使用します。
2. **[リソース グループ]** で、既存のリソース グループを選択するか、新しいリソース グループを作成します。 このチュートリアルでは **contosoRG** を使用します。
3. **[場所]** で、コンテナーを配置するリージョンを選びます。 このチュートリアルでは、"**西ヨーロッパ**" を使用します。
4. ダッシュボードから資格情報コンテナーにすばやくアクセスするには、 **[ダッシュボードにピン留めする]**  >  **[作成]** の順に選択します。

![[Recovery Services コンテナーの作成] ページのスクリーンショット。](./media/tutorial-prepare-azure/new-vault-settings.png)

新しい資格情報コンテナーは、 **[ダッシュボード]**  >  **[すべてのリソース]** と、メインの **[Recovery Services コンテナー]** ページに表示されます。

## <a name="set-up-an-azure-network"></a>Azure ネットワークをセットアップ

フェールオーバー後にストレージから作成された Azure VM は、このネットワークに参加します。

1. [Azure portal](https://portal.azure.com) で、 **[リソースの作成]**  >  **[ネットワーク]**  >  **[仮想ネットワーク]** の順に選択します。 デプロイ モデルとして [Resource Manager] を選択したままにします。
2. **[名前]** で、ネットワーク名を入力します。 Azure リソース グループ内で一意となる名前を使用してください。 このチュートリアルでは **ContosoASRnet** を使用します。
3. ネットワークを作成するリソース グループを指定します。 このチュートリアルでは、既存のリソース グループ **contosoRG** を使用します。
4. **[アドレス範囲]** で、ネットワークの範囲として **10.0.0.0/24** を入力します。 このネットワークにはサブネットがありません。
5. **[サブスクリプション]** で、ネットワークを作成するサブスクリプションを選択します。
6. **[場所]** では **[西ヨーロッパ]** を選択します。 ネットワークは、Recovery Services コンテナーと同じリージョンにある必要があります。
7. 基本的な DDoS 保護の既定のオプションはそのままにして、ネットワーク上にサービス エンドポイントを置きません。
8. **作成** を選択します。

![[仮想ネットワークの作成] のオプションのスクリーンショット。](media/tutorial-prepare-azure/create-network.png)

仮想ネットワークの作成には数秒かかります。 作成が完了すると、Azure portal ダッシュボードに表示されます。

## <a name="useful-links"></a>便利なリンク

内容は次のとおりです。
- [Azure ネットワーク](../virtual-network/virtual-networks-overview.md)
- [マネージド ディスク](../virtual-machines/managed-disks-overview.md)



## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure へのディザスター リカバリーのためにオンプレミス Hyper-V インフラストラクチャを準備する](hyper-v-prepare-on-premises-tutorial.md)
