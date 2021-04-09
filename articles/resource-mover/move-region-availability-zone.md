---
title: Azure Resource Mover を使用して Azure VM を別のリージョン内の可用性ゾーンに移動する
description: Azure Resource Mover を使用して Azure VM を可用性ゾーンに移動する方法について説明します。
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 88006fb354af2673496c6476090d7f73c8a005e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95543002"
---
# <a name="move-azure-vms-to-an-availability-zone-in-another-region"></a>Azure VM を別のリージョン内の可用性ゾーンに移動する

この記事では、[Azure Resource Mover](overview.md) を使用して、Azure VM (および関連するネットワーク/ストレージ リソース) を別の Azure リージョン内の可用性ゾーンに移動する方法について説明します。

[Azure 可用性ゾーン](../availability-zones/az-overview.md#availability-zones)は、Azure のデプロイをデータセンターの障害から保護するために役立ちます。 それぞれの可用性ゾーンは、独立した電源、冷却手段、ネットワークを備えた 1 つまたは複数のデータセンターで構成されています。 回復性を確保するために、[有効になっているリージョン](../availability-zones/az-region.md)にはいずれも少なくとも 3 つの個別のゾーンが存在します。 Resource Mover を使用すると、次の移動を行うことができます。

- 単一インスタンス VM をターゲット リージョン内の可用性ゾーン/可用性セットに移動する。
- 可用性セット内の VM をターゲット リージョン内の可用性ゾーン/可用性セットに移動する。
- ソース リージョンの可用性ゾーン内の VM をターゲット リージョン内の可用性ゾーンに移動する。


> [!IMPORTANT]
> Azure Resource Mover は現在、パブリックプレビュー段階にあります。

VM を同じリージョン内の別の可用性ゾーンに移動する場合は、[この記事を確認](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)してください。

## <a name="prerequisites"></a>前提条件

- 移動するリソースが配置されているサブスクリプション上の *所有者* アクセス権。
    - Azure サブスクリプションの特定のソースと宛先のマッピングに対してリソースを初めて追加すると、Resource Mover では、そのサブスクリプションによって信頼される[システム割り当てマネージド ID](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (以前の Managed Service Identify (MSI)) を作成します。
    - ID を作成し、必要なロール (ソース サブスクリプションの共同作成者またはユーザー アクセス管理者) に割り当てるには、リソースを追加するのに使用するアカウントに、サブスクリプションに対する "*所有者*" 権限が必要です。 Azure ロールの詳細については、[こちらを参照してください](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)。
- このサブスクリプションには、ターゲット リージョン内にソース リソースを作成するための十分なクォータが必要です。 これがない場合は、追加の制限を要求してください。 [詳細については、こちらを参照してください](../azure-resource-manager/management/azure-subscription-service-limits.md)。
- VM の移動先となるターゲット リージョンに関連付する料金と課金を確認します。 [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用すると便利です。
    


## <a name="check-vm-requirements"></a>VM の要件を確認する

1. 移動する VM がサポートされていることを確認します。

    - サポートされている Windows VM を[確認](support-matrix-move-region-azure-vm.md#windows-vm-support)します。
    - サポートされている Linux VM とカーネルのバージョンを[確認](support-matrix-move-region-azure-vm.md#linux-vm-support)します。
    - サポートされている[コンピューティング](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings)、[ストレージ](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)、[ネットワーク](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings)の設定を確認します。
2. 移動する VM が有効になっていることを確認します。
3. VM に最新の信頼されたルート証明書と更新された証明書失効リスト (CRL) があることを確認します。 
    - Windows を実行している Azure VM では、最新の Windows 更新プログラムをインストールします。
    - Linux を実行している VM では、Linux ディストリビューターのガイダンスに従って、そのマシンに最新の証明書と CRL があることを確認します。 
4. VM からの送信接続を許可します。
    - 送信接続を制御するために URL ベースのファイアウォール プロキシを使用する場合は、これらの [URL](support-matrix-move-region-azure-vm.md#url-access) へのアクセスを許可します
    - ネットワーク セキュリティ グループ (NSG) ルールを使用して送信接続を制御する場合は、これらの[サービス タグ ルール](support-matrix-move-region-azure-vm.md#nsg-rules)を作成します。

## <a name="select-resources-to-move"></a>移動するリソースを選択する

移動するリソースを選択します。

- 選択したソース リージョン内のリソース グループにわたって、サポートされている任意のリソースの種類を選択できます。
- ソース リージョンのサブスクリプションのターゲット リージョンにリソースを移動します。 サブスクリプションを変更する場合は、リソースの移動後に変更を行うことができます。

1. Azure portal で、Resource Mover を検索します。 その後、 **[サービス]** で **[Azure Resource Mover]** を選択します。

    ![Resource Mover を検索する](./media/move-region-availability-zone/search.png)

2. **[概要]** で、 **[開始]** を選択します。

    ![開始するためのボタン](./media/move-region-availability-zone/get-started.png)

3. **[リソースの移動]**  >  **[Source + destination]\(ソース + 宛先\)** で、ソース サブスクリプションおよびリージョンを選択します。
4. **[宛先]** で、VM の移動先となるリージョンを選択します。 続けて、 **[次へ]** をクリックします。

     ![ソースと宛先のサブスクリプション/リージョンを入力するためのページ](./media/move-region-availability-zone/source-target.png)

6. **[移動するリソース]** で、 **[リソースの選択]** をクリックします。
7. **[リソースの選択]** で、VM を選択します。 移動がサポートされているリソースのみを追加できます。 次に、 **[Done]** をクリックします。 **[移動するリソース]** で、 **[次へ]** をクリックします。

    ![移動する VM を選択するページ](./media/move-region-availability-zone/select-vm.png)
8. **[確認 + 追加]** で、ソースと宛先の設定を確認します。

    ![設定を確認し、移動を続行するためのページ](./media/move-region-availability-zone/review.png)

9. **[続行]** をクリックして、リソースの追加を開始します。
10. 追加プロセスが正常に完了した後、通知アイコンの **[Adding resources for move]\(移動するリソースの追加\)** をクリックします。

    ![通知のメッセージ](./media/move-region-availability-zone/notification.png)

通知をクリックすると、 **[リージョン間]** ページにリソースが表示されます。

> [!NOTE]
> 通知をクリックすると、 **[リージョン間]** ページにリソースが "*準備が保留中*" の状態で表示されます。
> - 移動コレクションからリソースを削除する場合、その方法は移動プロセスのどの段階であるかによって異なります。 [詳細については、こちらを参照してください](remove-move-resources.md)。

## <a name="resolve-dependencies"></a>依存関係を解決する

1. リソースの **[問題]** 列に *[依存関係の検証]* メッセージが表示されている場合は、 **[依存関係の検証]** ボタンをクリックします。 検証プロセスが開始されます。
2. 依存関係が見つかった場合は、 **[依存関係の追加]** をクリックします。 
3. **[依存関係の追加]** で、依存リソース、 **[依存関係の追加]** の順に選択します。 通知で進行状況を監視します。

    ![依存関係を追加するためのボタン](./media/move-region-availability-zone/add-dependencies.png)

3. 必要に応じて依存関係をさらに追加し、依存関係をもう一度検証します。 

    ![依存関係をさらに追加するためのページ](./media/move-region-availability-zone/add-additional-dependencies.png)

4. **[Across regions]\(リージョン間\)** ページで、リソースが現在、"*準備が保留中*" の状態になっており、問題がないことを確認します。

    !["準備が保留中" の状態のリソースを示すページ](./media/move-region-availability-zone/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>ソース リソース グループを移動する 

VM を準備して移動するには、ターゲット リージョンにソース リソース グループが存在している必要があります。 

### <a name="prepare-to-move-the-source-resource-group"></a>ソース リソース グループの移動を準備する

次のように準備します。

1. **[Across regions]\(リージョン間\)** で、ソース リソース グループ、 **[準備]** の順に選択します。
2. **[リソースの準備]** で、 **[準備]** をクリックします。

    ![ソース リソース グループを準備するためのボタン](./media/move-region-availability-zone/prepare-resource-group.png)

    準備プロセス中に、Resource Mover によって、リソース グループの設定を使用して Azure Resource Manager (ARM) テンプレートが生成されます。 リソース グループ内のリソースは影響を受けません。

> [!NOTE]
>  リソース グループを準備した後、"*移動の開始が保留中*" の状態になります。 

![開始が保留中の状態を示すステータス](./media/move-region-availability-zone/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>ソース リソース グループを移動する

次のように移動を開始します。

1. **[Across regions]\(リージョン間\)** で、リソース グループ、 **[移動の開始]** の順に選択します
2. **[リソースの移動]** で、 **[移動の開始]** をクリックします。 リソース グループは、"*移動の開始が進行中*" の状態に移ります。
3. 移動を開始した後、生成された ARM テンプレートに基づいて、ターゲット リソース グループが作成されます。 ソース リソース グループは、"*移動のコミットが保留中*" の状態に移ります。

![移動のコミットを示すステータス](./media/move-region-availability-zone/commit-move-pending.png)

移動プロセスをコミットして完了するには、次のようにします。

1. **[リージョン間]** で、リソース グループ、 **[移動のコミット]** の順に選択します。
2. **[リソースの移動]** で、 **[コミット]** をクリックします。

> [!NOTE]
> 移動をコミットした後、ソース リソース グループは "*ソースの削除が保留中*" の状態になります。


## <a name="add-a-target-availability-zone"></a>ターゲット可用性ゾーンを追加する

残りのリソースを移動する前に、VM のターゲット可用性ゾーンを設定します。

1. **[リージョン間]** ページで、移動する VM の **[宛先の構成]** 列にあるリンクをクリックします。

    ![VM のプロパティ](./media/move-region-availability-zone/select-vm-settings.png)

3. **[構成設定]** で、宛先 VM の設定を指定します。 ターゲット リージョン内の VM は次のように構成できます。
    -  ターゲット リージョン内に新しい同等のリソースを作成します。 指定した設定を除き、ターゲット リソースはソースと同じ設定で作成されます。
    - ターゲット リージョン内の既存の VM を選択します。 

4. **[ゾーン]** で、移動時に VM を配置するゾーンを選択します。

    変更は、編集しているリソースに対してのみ行われます。 依存リソースはすべて、個別に更新する必要があります。

5. **[SKU]** で、ターゲット VM に割り当てる [Azure のレベル](..//virtual-machines/sizes.md)を指定します。
6. **[可用性セット]** で、ターゲット VM を可用性ゾーンの可用性セット内で実行したい場合は可用性セットを選択します。
7. **[変更の保存]** を選択します。

    ![VM 設定](./media/move-region-availability-zone/vm-settings.png)


## <a name="prepare-resources-to-move"></a>移動するリソースを準備する

これでソース リソース グループが移動されたので、他のリソースを移動する準備を行うことができます。

1. **[Across regions]\(リージョン間\)** で、準備するリソースを選択します。 

    ![その他のリソースの準備を選択するためのページ](./media/move-region-availability-zone/prepare-other.png)

2. **[準備]** を選択します。 

> [!NOTE]
> - 準備プロセス中、レプリケーションのために、VM に Azure Site Recovery モビリティ エージェントがインストールされます。
> - VM データは、ターゲット リージョンに定期的にレプリケートされます。 これはソース VM には影響しません。
> - Resource Mover によって、他のソース グループ用の ARM テンプレートが生成されます。
> - リソースを準備した後、"*移動の開始が保留中*" の状態になります。

![移動の開始が保留中状態のリソースを示すページ](./media/move-region-availability-zone/initiate-move-pending.png)

## <a name="initiate-the-move"></a>移動を開始する

リソースが準備され、移動を開始できるようになりました。 

1. **[Across regions]\(リージョン間\)** で、"*移動の開始が保留中*" 状態のリソースを選択します。 次に、 **[移動の開始]** をクリックします。
2. **[リソースの移動]** で、 **[移動の開始]** をクリックします。

    ![リソースの移動を開始するためのページ](./media/move-region-availability-zone/initiate-move.png)

3. 通知バーで移動の進行状況を追跡します。


> [!NOTE]
> - VM の場合、レプリカ VM はターゲット リージョンに作成されます。 ソース VM がシャットダウンされ、ダウンタイム (通常は数分) が発生します。
> - Resource Mover によって、準備された ARM テンプレートを使用して他のリソースが再作成されます。 通常、ダウンタイムは発生しません。
> - リソースを準備すると、それらのリソースは "*移動のコミットが保留中*" の状態になります。


![移動のコミットが保留中の状態にあるリソースを表示するためのページ](./media/move-region-availability-zone/resources-commit-move-pending.png)

## <a name="discard-or-commit"></a>破棄またはコミットしますか?

最初の移動後に、移動をコミットするか、破棄するかを決定できます。 

- **破棄**: テストする場合は移動を破棄できますが、実際にはソース リソースを移動したくありません。 移動を破棄すると、リソースが "*移動の開始が保留中*" の状態に戻ります。
- **コミット**:コミットすると、ターゲット リージョンへの移動が完了します。 コミット後、ソース リソースは "*ソースの削除が保留中*" の状態になり、削除するかどうかを決定できます。

## <a name="discard-the-move"></a>移動を破棄する 

移動は次のように破棄できます。

1. **[Across regions]\(リージョン間\)** で、"*移動のコミットが保留中*" 状態のリソースを選択し、 **[移動の破棄]** をクリックします。
2. **[移動の破棄]** で、 **[破棄]** をクリックします。
3. 通知バーで移動の進行状況を追跡します。
 

> [!NOTE]
> VM の場合は、リソースを破棄すると、それらのリソースは "*移動の開始が保留中*" の状態になります。

## <a name="commit-the-move"></a>移動をコミットする

移動プロセスを完了する場合は、移動をコミットします。 

1. **[Across regions]\(リージョン間\)** で、"*移動のコミットが保留中*" 状態のリソースを選択し、 **[Commit move]\(移動のコミット\)** をクリックします。
2. **[Commit resources]\(リソースのコミット\)** で、 **[コミット]** をクリックします。

    ![移動を確定するためにリソースをコミットするページ](./media/move-region-availability-zone/commit-resources.png)

3. 通知バーでコミットの進行状況を追跡します。

> [!NOTE]
> - 移動をコミットした後、VM のレプリケートが停止します。 ソース VM はコミットの影響を受けません。
> - コミットはソース ネットワーク リソースに影響しません。
> - 移動をコミットした後、リソースは "*ソースの削除が保留中*" の状態になります。

![*ソースの削除が保留中* 状態のリソースを示すページ](./media/move-region-availability-zone/delete-source-pending.png)

## <a name="configure-settings-after-the-move"></a>移動後に設定を構成する

Mobility Service は VM から自動的にアンインストールされません。 手動でアンインストールします。あるいは、サーバーをもう一度移動する予定であれば、そのままにしておきます。


## <a name="delete-source-resources-after-commit"></a>コミット後にソース リソースを削除する

移動後に、必要に応じて、ソース リージョンのリソースを削除できます。

1. **[Across Regions]\(リージョン間\)** で、削除する各ソース リソースの名前をクリックします。
2. 各リソースのプロパティ ページで、 **[削除]** を選択します。

## <a name="delete-additional-resources-created-for-move"></a>移動用に作成されたその他のリソースを削除する

移動後に、移動コレクションと、作成された Site Recovery リソースを手動で削除することができます。

- 既定では、移動コレクションは非表示になっています。 これを表示するには、非表示のリソースを有効にする必要があります。
- キャッシュ ストレージには、事前に削除しておく必要があるロックがあります。

次のように削除します。 

1. リソース グループ ```RegionMoveRG-<sourceregion>-<target-region>``` でリソースを見つけます。
2. ソース リージョン内のすべての VM とその他のソース リソースが移動または削除されていることを確認します。 これにより、それらを使用している保留中のリソースがないことが保証されます。
2. リソースを削除します。

    - 移動コレクションの名前は ```movecollection-<sourceregion>-<target-region>``` です。
    - キャッシュ ストレージ アカウントの名前は ```resmovecache<guid>``` です
    - コンテナーの名前は ```ResourceMove-<sourceregion>-<target-region>-GUID``` です。

## <a name="next-steps"></a>次のステップ

移動プロセス[について学習します](about-move-process.md)。