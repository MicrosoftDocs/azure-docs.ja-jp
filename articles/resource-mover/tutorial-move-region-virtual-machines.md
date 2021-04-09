---
title: Azure Resource Mover を使用してリージョン間で Azure VM を移動する
description: Azure Resource Mover を使用して、Azure VM を別のリージョンに移動する方法について学習します
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5712448c8c5248d3c84ce43f8a41c669355f1d43
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565735"
---
# <a name="tutorial-move-azure-vms-across-regions"></a>チュートリアル:リージョン間で Azure VM を移動する

この記事では、[Azure Resource Mover](overview.md) を使用して、Azure VM、および関連するネットワークまたはストレージ リソースを別の Azure リージョンに移動する方法について学習します。
.


このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 前提条件と要件を確認する。
> * 移動するリソースを選択する。
> * リソースの依存関係を解決する。
> * ソース リソース グループを準備して移動する。 
> * 他のリソースを準備して移動する。
> * 移動を破棄するか、コミットするかを決定する。 
> * 必要に応じて、移動後にソース リージョンのリソースを削除する。

> [!NOTE]
> 各チュートリアルでは、シナリオを試すための最も簡単な方法を紹介し、既定のオプションを使用します。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。 次に、[Azure Portal](https://portal.azure.com) にサインインします。

## <a name="prerequisites"></a>前提条件
**要件** | **説明**
--- | ---
**Resource Mover のサポート** | サポートされているリージョンとその他の一般的な質問を[確認](common-questions.md)します。
**サブスクリプションのアクセス許可** | 移動するリソースを含むサブスクリプションに "*所有者*" アクセス権があることを確認します<br/><br/> **所有者アクセスが必要な理由:** Azure サブスクリプションの特定のソースと宛先のペアに対してリソースを初めて追加すると、Resource Mover では、サブスクリプションによって信頼されている[システム割り当てマネージド ID](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (旧称: Managed Service ID (MSI)) が作成されます。 ID を作成し、必要なロール (ソース サブスクリプションの共同作成者またはユーザー アクセス管理者) に割り当てるには、リソースを追加するのに使用するアカウントに、サブスクリプションに対する "*所有者*" 権限が必要です。 Azure ロールの詳細については、[こちらを参照してください](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)。
**VM のサポート** |  移動する VM がサポートされていることを確認します。<br/><br/> - サポートされている Windows VM を[確認](support-matrix-move-region-azure-vm.md#windows-vm-support)します。<br/><br/> - サポートされている Linux VM とカーネルのバージョンを[確認](support-matrix-move-region-azure-vm.md#linux-vm-support)します。<br/><br/> - サポートされている[コンピューティング](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings)、[ストレージ](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)、[ネットワーク](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings)の設定を確認します。
**宛先サブスクリプション** | 宛先リージョンのサブスクリプションには、ターゲット リージョンで移動するリソースを作成するのに十分なクォータが必要です。 クォータがない場合は、[追加の制限を要求](../azure-resource-manager/management/azure-subscription-service-limits.md)します。
**宛先リージョンの料金** | VM の移動先となるターゲット リージョンに関連付する料金と課金を確認します。 [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用すると便利です。
    

## <a name="prepare-vms"></a>VM を準備する

1. VM が要件を満たしていることを確認したら、移動する VM をオンにします。 宛先リージョンで利用するすべての VM ディスクを VM にアタッチし、初期化する必要があります。
1. VM に最新の信頼されたルート証明書と、更新された証明書失効リスト (CRL) があることを確認します。 これを行うには、次の手順を実行します。
    - Windows VM で、最新の Windows 更新プログラムをインストールします。
    - Linux VM では、ディストリビューター ガイダンスに従って、マシンに最新の証明書と CRL が存在するようにします。 
1. VM からの送信接続を許可します。
    - 送信接続を制御するために URL ベースのファイアウォール プロキシを使用する場合は、これらの [URL](support-matrix-move-region-azure-vm.md#url-access) へのアクセスを許可します
    - ネットワーク セキュリティ グループ (NSG) ルールを使用して送信接続を制御する場合は、これらの[サービス タグ ルール](support-matrix-move-region-azure-vm.md#nsg-rules)を作成します。

## <a name="select-resources"></a>リソースを選択する 

移動するリソースを選択します。

- 選択されたソース リージョン内のリソース グループでサポートされているすべてのリソースの種類が表示されます。
- リージョン間を移動するために既に追加されているリソースは表示されません。
- ソース リージョンと同じサブスクリプションのターゲット リージョンにリソースを移動します。 サブスクリプションを変更する場合は、リソースの移動後に変更を行うことができます。

1. Azure portal で、*Resource Mover* を検索します。 その後、 **[サービス]** で **[Azure Resource Mover]** を選択します。

    ![Azure portal での Resource Mover の検索結果](./media/tutorial-move-region-virtual-machines/search.png)

2. **[概要]** で、 **[開始]** をクリックします。

    ![別のリージョンに移動するリソースを追加するボタン](./media/tutorial-move-region-virtual-machines/get-started.png)

3. **[リソースの移動]**  >  **[Source + destination]\(ソース + 宛先\)** で、ソース サブスクリプションおよびリージョンを選択します。
4. **[宛先]** で、VM の移動先となるリージョンを選択します。 続けて、 **[次へ]** をクリックします。

    ![ソースと宛先のリージョンを選択するページ](./media/tutorial-move-region-virtual-machines/source-target.png)

6. **[移動するリソース]** で、 **[リソースの選択]** をクリックします。
7. **[リソースの選択]** で、VM を選択します。 [移動がサポートされているリソース](#prepare-vms)のみを追加できます。 次に、 **[Done]** をクリックします。

    ![移動する VM を選択するページ](./media/tutorial-move-region-virtual-machines/select-vm.png)

8.  **[移動するリソース]** で、 **[次へ]** をクリックします。
9. **[確認]** で、ソースと宛先の設定を確認します。 

    ![設定を確認し、移動を続行するためのページ](./media/tutorial-move-region-virtual-machines/review.png)
10. **[続行]** をクリックして、リソースの追加を開始します。
11. 追加プロセスが正常に完了した後、通知アイコンの **[Adding resources for move]\(移動するリソースの追加\)** をクリックします。
12. 通知をクリックした後、 **[Across regions]\(リージョン間\)** ページでリソースを確認します。

> [!NOTE]
> - 追加されたリソースは、"*準備が保留中*" の状態にあります。
> - VM のリソース グループが自動的に追加されます。
> - 移動コレクションからリソースを削除する場合、その方法は移動プロセスのどの段階であるかによって異なります。 [詳細については、こちらを参照してください](remove-move-resources.md)。

## <a name="resolve-dependencies"></a>依存関係を解決する

1. リソースの **[問題]** 列に *[依存関係の検証]* メッセージが表示されている場合は、 **[依存関係の検証]** ボタンをクリックします。 検証プロセスが開始されます。
2. 依存関係が見つかった場合は、 **[依存関係の追加]** をクリックします。 
3. **[依存関係の追加]** では、既定の **[Show all dependencies]\(すべての依存関係を表示する\)** オプションのままにしてください。

    - [Show all dependencies]\(すべての依存関係を表示する\) を選択すると、リソースの直接の依存関係と間接の依存関係がすべて反復処理されます。 たとえば、VM について、NIC、仮想ネットワーク、ネットワーク セキュリティ グループ (NSG) などが表示されます。
    - [Show first level dependencies only]\(第 1 レベルの依存関係のみ表示する\) を選択すると、直接の依存関係のみが表示されます。 たとえば、VM について、NIC は表示されますが、仮想ネットワークは表示されません。


4. 追加する依存リソースを選択し、 **[依存関係の追加]** を選択します。 通知で進行状況を監視します。

    ![依存関係を追加する](./media/tutorial-move-region-virtual-machines/add-dependencies.png)

4. もう一度、依存関係を検証します。 
    ![依存関係をさらに追加するためのページ](./media/tutorial-move-region-virtual-machines/add-additional-dependencies.png)



## <a name="move-the-source-resource-group"></a>ソース リソース グループを移動する 

VM の準備と移動を行う前に、VM リソース グループがターゲット リージョンに存在している必要があります。 

### <a name="prepare-to-move-the-source-resource-group"></a>ソース リソース グループの移動を準備する

準備プロセス中に、Resource Mover によって、リソース グループの設定を使用して Azure Resource Manager (ARM) テンプレートが生成されます。 リソース グループ内のリソースは影響を受けません。

次のように準備します。

1. **[Across regions]\(リージョン間\)** で、ソース リソース グループ、 **[準備]** の順に選択します。
2. **[リソースの準備]** で、 **[準備]** をクリックします。

    ![リソース グループを準備する](./media/tutorial-move-region-virtual-machines/prepare-resource-group.png)

> [!NOTE]
> リソース グループを準備した後、"*移動の開始が保留中*" の状態になります。 

 
### <a name="move-the-source-resource-group"></a>ソース リソース グループを移動する

次のように移動を開始します。

1. **[Across regions]\(リージョン間\)** で、リソース グループ、 **[移動の開始]** の順に選択します
2. **[リソースの移動]** で、 **[移動の開始]** をクリックします。 リソース グループは、"*移動の開始が進行中*" の状態に移ります。
3. 移動を開始した後、生成された ARM テンプレートに基づいて、ターゲット リソース グループが作成されます。 ソース リソース グループは、"*移動のコミットが保留中*" の状態に移ります。

    ![[移動の開始] ボタンをクリックする](./media/tutorial-move-region-virtual-machines/commit-move-pending.png)

移動プロセスをコミットして完了するには、次のようにします。

1. **[Across regions]\(リージョン間\)** で、リソース グループ、 **[Commit move]\(移動のコミット\)** の順に選択します。
2. **[リソースの移動]** で、 **[コミット]** をクリックします。

> [!NOTE]
> 移動をコミットした後、ソース リソース グループは "*ソースの削除が保留中*" の状態になります。

## <a name="prepare-resources-to-move"></a>移動するリソースを準備する

ソース リソース グループを移動したら、"*準備が保留中*" の状態になっている他のリソースを移動するための準備を行うことができます。

1. **[Across regions]\(リージョン間\)** で、リソースが現在、"*準備が保留中*" の状態になっており、問題がないことを確認します。 そのようになっていない場合は、再度検証を実行し、未解決の問題があれば解決します。

    !["準備が保留中" の状態のリソースを示すページ](./media/tutorial-move-region-virtual-machines/prepare-pending.png)

2. 移動を開始する前にターゲット設定を編集する場合は、リソースの **[Destination configuration]\(宛先の構成\)** 列にあるリンクを選択し、その設定を編集します。 ターゲット VM の設定を編集する場合、ターゲット VM のサイズをソース VM のサイズより小さくすることはできません。  

これでソース リソース グループが移動されたので、他のリソースを移動する準備を行うことができます。

3. 準備するリソースを選択します。 

    ![その他のリソースの準備を選択するためのページ](./media/tutorial-move-region-virtual-machines/prepare-other.png)

2. **[準備]** を選択します。 

> [!NOTE]
> - 準備プロセス中に、Azure Site Recovery モビリティ エージェントが、レプリケートする VM にインストールされます。
> - VM データは、ターゲット リージョンに定期的にレプリケートされます。 これはソース VM には影響しません。
> - Resource Mover によって、他のソース グループ用の ARM テンプレートが生成されます。
> - リソースを準備した後、"*移動の開始が保留中*" の状態になります。

![移動の開始が保留中状態のリソースを示すページ](./media/tutorial-move-region-virtual-machines/initiate-move-pending.png)


## <a name="initiate-the-move"></a>移動を開始する

リソースが準備され、移動を開始できるようになりました。 

1. **[Across regions]\(リージョン間\)** で、"*移動の開始が保留中*" 状態のリソースを選択します。 その後、 **[移動の開始]** をクリックします。
2. **[リソースの移動]** で、 **[移動の開始]** をクリックします。

    ![[移動の開始] ボタンをクリックする](./media/tutorial-move-region-virtual-machines/initiate-move.png)

3. 通知バーで移動の進行状況を追跡します。

> [!NOTE]
> - VM の場合、レプリカ VM はターゲット リージョンに作成されます。 ソース VM がシャットダウンされ、ダウンタイム (通常は数分) が発生します。
> - Resource Mover によって、準備された ARM テンプレートを使用して他のリソースが再作成されます。 通常、ダウンタイムは発生しません。
> - リソースを移動した後、"*移動のコミットが保留中*" の状態になります。

![*ソースの削除が保留中* 状態のリソースを示すページ](./media/tutorial-move-region-virtual-machines/delete-source-pending.png)


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
> リソースを破棄した後、VM は "*移動の開始が保留中*" の状態になります。

## <a name="commit-the-move"></a>移動をコミットする

移動プロセスを完了する場合は、移動をコミットします。 

1. **[Across regions]\(リージョン間\)** で、"*移動のコミットが保留中*" 状態のリソースを選択し、 **[Commit move]\(移動のコミット\)** をクリックします。
2. **[Commit resources]\(リソースのコミット\)** で、 **[コミット]** をクリックします。

    ![移動を確定するためにリソースをコミットするページ](./media/tutorial-move-region-virtual-machines/commit-resources.png)

3. 通知バーでコミットの進行状況を追跡します。

> [!NOTE]
> - 移動をコミットした後、VM のレプリケートが停止します。 ソース VM はコミットの影響を受けません。
> - コミットはソース ネットワーク リソースに影響しません。
> - 移動をコミットした後、リソースは "*ソースの削除が保留中*" の状態になります。

![*ソースの削除が保留中* 状態のリソースを示すページ](./media/tutorial-move-region-virtual-machines/delete-source-pending.png)


## <a name="configure-settings-after-the-move"></a>移動後に設定を構成する

- Mobility Service は VM から自動的にアンインストールされません。 手動でアンインストールします。あるいは、サーバーをもう一度移動する予定であれば、そのままにしておきます。
- 移動後、Azure ロールベースのアクセス制御 (Azure RBAC) 規則を変更します。


## <a name="delete-source-resources-after-commit"></a>コミット後にソース リソースを削除する

移動後に、必要に応じて、ソース リージョンのリソースを削除できます。 

> [!NOTE]
> いくつかのリソース (キー コンテナー、SQL Server サーバーなど) はポータルからは削除できません。リソースのプロパティ ページから削除する必要があります。

1. **[Across Regions]\(リージョン間\)** で、削除するソース リソースの名前をクリックします。
2. **[ソースの削除]** を選択します。

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

このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * Azure VM を別の Azure リージョンに移動しました。
> * VM に関連付けられているリソースを別のリージョンに移動しました。

ここで、Azure SQL データベースとエラスティック プールを別のリージョンに移動してみます。

> [!div class="nextstepaction"]
> [Azure SQL リソースを移動する](./tutorial-move-region-sql.md)
