---
title: Azure Resource Mover を使用してリソースを別のリージョンに移動する
description: Azure Resource Mover を使用して、リソース グループ内のリソースを別のリージョンに移動する方法について説明します。
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 79224c14fc5182df7a699864af3d78c9be36259f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97797280"
---
# <a name="move-resources-across-regions-from-resource-group"></a>(リソース グループ) のリソースをリージョン間で移動する

この記事では、特定のリソース グループのリソースを別の Azure リージョンに移動する方法について説明します。 リソース グループで、移動するリソースを選択します。 次に、[Azure Resource Mover](overview.md) を使用してそれらを移動します。

> [!IMPORTANT]
> Azure Resource Mover は現在、パブリックプレビュー段階にあります。


## <a name="prerequisites"></a>前提条件

- 移動するリソースが配置されているサブスクリプションの "*所有者*" アクセス権が必要です。
    - Azure サブスクリプションの特定のソースと宛先のマッピングに対してリソースを初めて追加すると、そのサブスクリプションによって信頼される[システム割り当てマネージド ID](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (旧称 Managed Service Identify (MSI)) が Resource Mover により作成されます。
    - ID を作成し、必要なロール (ソース サブスクリプションの共同作成者またはユーザー アクセス管理者) に割り当てるには、リソースを追加するのに使用するアカウントに、サブスクリプションに対する "*所有者*" 権限が必要です。 Azure ロールの詳細については、[こちらを参照してください](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)。
- このサブスクリプションには、ターゲット リージョン内にソース リソースを作成するための十分なクォータが必要です。 これがない場合は、追加の制限を要求してください。 [詳細については、こちらを参照してください](../azure-resource-manager/management/azure-subscription-service-limits.md)。
- VM の移動先となるターゲット リージョンに関連付する料金と課金を確認します。 [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用すると便利です。
- 移動するリソースが Resource Mover によってサポートされていることを確認します。
    - Azure VM および関連付けられているディスク
    - NIC
    - 可用性セット
    - Azure 仮想ネットワーク
    - パブリック IP アドレス
    - ネットワーク セキュリティ グループ (NSG)
    - 内部およびパブリック ロード バランサー
    - Azure SQL データベースとエラスティック プール


## <a name="check-vm-requirements"></a>VM の要件を確認する

1. 移動する VM がサポートされていることを確認します。

    - サポートされている Windows VM を[確認](support-matrix-move-region-azure-vm.md#windows-vm-support)します。
    - サポートされている Linux VM とカーネルのバージョンを[確認](support-matrix-move-region-azure-vm.md#linux-vm-support)します。
    - サポートされている[コンピューティング](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings)、[ストレージ](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)、[ネットワーク](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings)の設定を確認します。
2. VM に最新の信頼されたルート証明書と、更新された証明書失効リスト (CRL) があることを確認します。 
    - Windows を実行している Azure VM では、最新の Windows 更新プログラムをインストールします。
    - Linux を実行している VM では、Linux ディストリビューターのガイダンスに従って、そのマシンに最新の証明書と CRL があることを確認します。 
3. VM からの送信接続を許可します。
    - 送信接続を制御するために URL ベースのファイアウォール プロキシを使用する場合は、これらの [URL](support-matrix-move-region-azure-vm.md#url-access) へのアクセスを許可します
    - ネットワーク セキュリティ グループ (NSG) ルールを使用して送信接続を制御する場合は、これらの[サービス タグ ルール](support-matrix-move-region-azure-vm.md#nsg-rules)を作成します。

## <a name="select-resources-to-move"></a>移動するリソースを選択する

移動するリソースを選択します。 ソース リージョンのサブスクリプションのターゲット リージョンにリソースを移動します。 サブスクリプションを変更する場合は、リソースの移動後に変更を行うことができます。

> [!NOTE]
>  関連するディスクを選択しないと、操作が失敗します。 関連するディスクは、VM の移動に自動的に含まれます。

1. Azure portal で、関連するリソース グループを開きます。
2. リソース グループ ページで、移動するリソースを選択します。
3. **[移動]**  >  **[Move to another region]\(別のリージョンに移動する\)** の順に選択します。

    ![別のリージョンへのリソースの移動を選択](./media/move-region-within-resource-group/select-move-region.png)
    
4. **[Source + destination]\(移動元 + 移動先\)** で、リソースの移動先となるターゲット リージョンを選択します。 **[次へ]** を選択します。


    ![ターゲット リージョンを選択するための [Source and destination]\(移動元と移動先\) ページ](./media/move-region-within-resource-group/source-target.png)


7. **[移動するリソース]** で、 **[次へ]** を選択します。  
8. **[リソースの選択]** で、移動するリソースを選択します。 移動がサポートされているリソースのみを追加できます。 **[完了]** を選択します。
9. **[リソースの移動]** で、 **[次へ]** を選択します。 
10. **[Review + Add]\(確認 + 追加\)** で、ソースとターゲットの詳細を確認します。
11. 移動するリソースに関するメタデータはこの目的のために作成されたリソース グループに保存されることを理解していること、Resource Mover に対してサブスクリプション リソースにアクセスするためのシステム管理 ID の作成を許可することを確認します。
1. **[続行]** を選択して、リソースの追加を開始します。

    ![詳細を確認して移動を続行するための概要ページ](./media/move-region-within-resource-group/summary.png)    

11. リソースの追加操作が開始されます。 操作が完了すると、リソースが追加されたこと、デプロイが成功したことが通知されます。
13. 通知で、 **[Adding resources for move]\(移動するリソースの追加\)** を選択します。

    ![通知で示されるメッセージ](./media/move-region-within-resource-group/notification.png)    


14. 通知を選択した後、選択したリソースが Azure Resource Mover ハブの移動コレクションに追加されます。  Resource Mover を使用すると、依存関係を確認し、ターゲット リージョンへのリソースの移動を開始できます。

## <a name="resolve-dependencies"></a>依存関係を解決する

移動するリソースは、 **[Across regions]\(リージョン間\)** ページに表示され、"*準備が保留中*" の状態です。 次のように検証を開始します。

1. リソースの **[問題]** 列に *[依存関係の検証]* メッセージが表示されている場合は、 **[依存関係の検証]** ボタンを選択します。 検証プロセスが開始されます。

    ![依存関係を検証するためのボタン](./media/move-region-within-resource-group/validate-dependencies.png)

2. 依存関係が見つかった場合は、 **[依存関係の追加]** を選択します。 
3. **[依存関係の追加]** で、依存リソース、 **[依存関係の追加]** の順に選択します。 通知で進行状況を監視します。

    ![依存関係を追加するためのボタン](./media/move-region-within-resource-group/add-dependencies.png)

3. 必要に応じて依存関係をさらに追加し、依存関係を検証します。 **[更新]** を選択して、リソースの最新の状態を表示します。

4. **[Across regions]\(リージョン間\)** ページで、リソースが現在、"*準備が保留中*" の状態になっており、問題がないことを確認します。

    ![すべてのリソースについて "準備が保留中" の状態が表示されているページ](./media/move-region-within-resource-group/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>ソース リソース グループを移動する 

リソースを準備して移動するには、ターゲット リージョンにソース リソース グループが存在している必要があります。 

### <a name="prepare-to-move-the-source-resource-group"></a>ソース リソース グループの移動を準備する

次のように準備します。

1. **[Across regions]\(リージョン間\)** で、ソース リソース グループ、 **[準備]** の順に選択します。
2. **[リソースの準備]** で、 **[準備]** を選択します。
1. 
    ![ソース リソース グループを準備するためのボタン](./media/move-region-within-resource-group/prepare-source-resource-group.png)

    準備プロセス中に、Resource Mover によって、リソース グループの設定を使用して Azure Resource Manager (ARM) テンプレートが生成されます。 リソース グループ内のリソースは影響を受けません。
    
> [!NOTE]
>  リソース グループを準備した後、"*移動の開始が保留中*" の状態になります。 更新して最新の状態を表示します。

![開始が保留中の状態を示すステータス](./media/move-region-within-resource-group/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>ソース リソース グループを移動する

次のように移動を開始します。

1. **[Across regions]\(リージョン間\)** で、リソース グループ、 **[移動の開始]** の順に選択します
2. **[リソースの移動]** で、 **[移動の開始]** を選択します。 リソース グループは、"*移動の開始が進行中*" の状態に移ります。
3. 移動を開始した後、生成された ARM テンプレートに基づいて、ターゲット リソース グループが作成されます。 ソース リソース グループは、"*移動のコミットが保留中*" の状態に移ります。

![移動のコミットを示すステータス](./media/move-region-availability-zone/commit-move-pending.png)

移動プロセスをコミットして完了するには、次のようにします。

1. **[Across regions]\(リージョン間\)** で、リソース グループ、 **[Commit move]\(移動のコミット\)** の順に選択します。
2. **[リソースの移動]** で、 **[コミット]** を選択します。

> [!NOTE]
> 移動をコミットした後、ソース リソース グループは "*ソースの削除が保留中*" の状態になります。

## <a name="modify-target-settings"></a>ターゲット設定の変更

ソース リソースを移動しない場合は、次のいずれかの操作を実行できます。

- ソース リージョンのリソースと名前と設定が同じリソースをターゲット リージョンで作成します。
- ターゲット リージョンで新しい同等のリソースを作成します。 指定した設定を除き、ターゲット リソースはソースと同じ設定で作成されます。
- ターゲット リージョンの既存のリソースを使用します。

次のように設定を変更します。

1. 設定を変更するには、リソースの **[Destination configuration]\(宛先の構成\)** 列でエントリを選択します。
2. **[Destination configuration]\(宛先の構成\)** ページで、使用するターゲット設定を指定します。
    変更は、編集しているリソースに対してのみ行われます。 依存リソースはすべて、個別に更新する必要があります。   
    
実際に変更する設定は、リソースの種類によって異なります。 ターゲット設定の編集については、[こちら](modify-target-settings.md)を参照してください。

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

1. **[Across regions]\(リージョン間\)** で、"*移動の開始が保留中*" 状態のリソースを選択します。 次に、 **[移動の開始]** を選択します。
2. **[リソースの移動]** で、 **[移動の開始]** を選択します。

    ![[移動の開始] ボタンの選択](./media/move-region-within-resource-group/initiate-move.png)

3. 通知バーで移動の進行状況を追跡します。


> [!NOTE]
> - VM の場合、レプリカ VM はターゲット リージョンに作成されます。 ソース VM がシャットダウンされ、ダウンタイム (通常は数分) が発生します。<br/>
> - Resource Mover によって、準備された ARM テンプレートを使用して他のリソースが再作成されます。 通常、ダウンタイムは発生しません。<br/> 
> - ロード バランサーの場合、NAT 規則はコピーされません。 移動をコミットした後、ターゲット リージョンでそれらを作成してください。
> - パブリック IP アドレスの場合、DNS 名ラベルはコピーされません。 移動をコミットした後、ラベルを再作成してください。
> - リソースを準備すると、それらのリソースは "*移動のコミットが保留中*" の状態になります。


## <a name="discard-or-commit"></a>破棄またはコミットしますか?

最初の移動後に、移動をコミットするか、破棄するかを決定できます。 

- **破棄**: テストする場合は移動を破棄できますが、実際にはソース リソースを移動したくありません。 移動を破棄すると、リソースが "*移動の開始が保留中*" の状態に戻ります。
- **コミット**:コミットすると、ターゲット リージョンへの移動が完了します。 コミット後、ソース リソースは "*ソースの削除が保留中*" の状態になり、削除するかどうかを決定できます。


## <a name="discard-the-move"></a>移動を破棄する 

移動は次のように破棄できます。

1. **[Across regions]\(リージョン間\)** で、"*移動のコミットが保留中*" 状態のリソースを選択し、 **[移動の破棄]** を選択します。
2. **[移動の破棄]** で、 **[破棄]** を選択します。
3. 通知バーで移動の進行状況を追跡します。
4. 移動が正常に完了したことを示す通知が表示されたら、 **[更新]** を選択します。 

> [!NOTE]
> VM の場合は、リソースを破棄すると、それらのリソースは "*移動の開始が保留中*" の状態になります。

## <a name="commit-the-move"></a>移動をコミットする

移動プロセスを完了する場合は、移動をコミットします。 


1. **[Across regions]\(リージョン間\)** で、"*移動のコミットが保留中*" 状態のリソースを選択し、 **[移動のコミット]** を選択します。
2. **[Commit resources]\(リソースのコミット\)** で、 **[コミット]** を選択します。

    ![移動を確定するためにリソースをコミットするページ](./media/move-region-within-resource-group/commit-resources.png)

3. 通知バーでコミットの進行状況を追跡します。

> [!NOTE]
> - 移動をコミットした後、VM のレプリケートが停止します。 ソース VM はコミットの影響を受けません。
> - コミットはソース ネットワーク リソースに影響しません。
> - 移動をコミットした後、リソースは "*ソースの削除が保留中*" の状態になります。

## <a name="configure-settings-after-the-move"></a>移動後に設定を構成する

1. パブリック IP アドレスの DNS 名ラベルはコピーされないため、移動が完了したら、ターゲット リソースに移動し、ラベルを更新します。 
2. 内部ロード バランサーについては NAT 規則がコピーされないため、ターゲット リージョンで作成されたリソースに移動し、NAT 規則を更新します。
3. Mobility Service は VM から自動的にアンインストールされません。 手動でアンインストールします。あるいは、サーバーをもう一度移動する予定であれば、そのままにしておきます。
## <a name="delete-source-resources-after-commit"></a>コミット後にソース リソースを削除する

移動後に、必要に応じて、ソース リージョンのリソースを削除できます。 

1. **[Across Regions]\(リージョン間\)** で、削除する各ソース リソースの名前を選択します。
2. 各リソースのプロパティ ページで、 **[削除]** を選択します。

## <a name="delete-additional-resources-created-for-move"></a>移動用に作成されたその他のリソースを削除する

移動後に、移動コレクションと、作成された Site Recovery リソースを手動で削除することができます。

- 既定では、移動コレクションは非表示になっています。 これを表示するには、非表示のリソースを有効にする必要があります。
- キャッシュ ストレージには、事前に削除しておく必要があるロックがあります。

次のように削除します。 

1. ソース リージョンのリソース グループ ```RegionMoveRG-<sourceregion>-<target-region>``` でリソースを見つけます。
2. VM および移動コレクション内の他のソース リソースがすべて移動または削除されていることを確認します。 これにより、それらを使用している保留中のリソースがないことが保証されます。
2. リソースを削除します。

    - 移動コレクションの名前は ```movecollection-<sourceregion>-<target-region>``` です。
    - キャッシュ ストレージ アカウントの名前は ```resmovecache<guid>``` です
    - コンテナーの名前は ```ResourceMove-<sourceregion>-<target-region>-GUID``` です。

## <a name="next-steps"></a>次のステップ


移動プロセス[について学習します](about-move-process.md)。