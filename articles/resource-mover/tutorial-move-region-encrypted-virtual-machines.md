---
title: Azure Resource Mover を使用してリージョン間で暗号化された Azure VM を移動する
description: Azure Resource Mover を使用して、暗号化された Azure VM を別のリージョンに移動する方法について説明します
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 014b4d09a991ae4d0bb31ec0b9adee0c9e3b3553
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361011"
---
# <a name="tutorial-move-encrypted-azure-vms-across-regions"></a>チュートリアル:暗号化された Azure VM をリージョン間で移動する

この記事では、[Azure Resource Mover](overview.md) を使用して、暗号化された Azure VM を別の Azure リージョンに移動する方法について説明します。 ここで言う "暗号化" の意味は次のとおりです。

- Azure Disk Encryption を有効にしたディスクが使用されている VM。 [詳細情報](../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- または、カスタマー マネージド キー (CMK) を使用して保存時の暗号化 (サーバー側暗号化) が適用されている VM。 [詳細情報](../virtual-machines/disks-enable-customer-managed-keys-portal.md)


このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 前提条件を確認する。 
> * Azure Disk Encryption が有効になっている VM について、ソース リージョンのキー コンテナーから宛先リージョンのキー コンテナーにキーとシークレットをコピーする。
> * VM を移動に備えて準備し、ソース リージョンから移動するリソースを選択する。
> * リソースの依存関係を解決する。
> * Azure Disk Encryption が有効になっている VM について、宛先キー コンテナーを手動で割り当てる。 カスタマー マネージド キーによるサーバー側暗号化が使用されている VM について、宛先リージョンのディスク暗号化セットを手動で割り当てる。
> * キー コンテナーとディスク暗号化セットのどちらかまたは両方を移動する。
> * ソース リソース グループを準備して移動する。 
> * 他のリソースを準備して移動する。
> * 移動を破棄するか、コミットするかを決定する。 
> * 必要に応じて、移動後にソース リージョンのリソースを削除する。

> [!NOTE]
> 各チュートリアルでは、シナリオを試すための最も簡単な方法を紹介し、既定のオプションを使用します。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。 次に、[Azure Portal](https://portal.azure.com) にサインインします。

## <a name="prerequisites"></a>前提条件

**要件** |**詳細**
--- | ---
**サブスクリプションのアクセス許可** | 移動するリソースを含むサブスクリプションに "*所有者*" アクセス権があることを確認します。<br/><br/> **所有者アクセスが必要な理由:** Azure サブスクリプションの特定のソースと宛先のペアに対してリソースを初めて追加すると、Resource Mover では、サブスクリプションによって信頼されている[システム割り当てマネージド ID](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (旧称: Managed Service ID (MSI)) が作成されます。 ID を作成し、必要なロール (ソース サブスクリプションの共同作成者とユーザー アクセス管理者) に割り当てるには、リソースを追加するのに使用するアカウントに、サブスクリプションに対する "*所有者*" 権限が必要です。 Azure ロールの詳細については、[こちらを参照してください](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)。
**VM のサポート** | 移動する VM がサポートされていることを確認します。<br/><br/> - サポートされている Windows VM を[確認](support-matrix-move-region-azure-vm.md#windows-vm-support)します。<br/><br/> - サポートされている Linux VM とカーネルのバージョンを[確認](support-matrix-move-region-azure-vm.md#linux-vm-support)します。<br/><br/> - サポートされている[コンピューティング](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings)、[ストレージ](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)、[ネットワーク](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings)の設定を確認します。
**キー コンテナーの要件 (Azure Disk Encryption)** | VM に対して Azure Disk Encryption を有効にしている場合、ソース リージョンのキー コンテナーに加え、宛先リージョンにもキー コンテナーが必要となります。 [キー コンテナーを作成](../key-vault/general/quick-create-portal.md)してください。<br/><br/> ソース リージョンとターゲット リージョンのキー コンテナーについて、次のアクセス許可が必要になります。<br/><br/> - キーのアクセス許可: キーの管理操作 (取得、一覧)、暗号化操作 (暗号化解除、暗号化)。<br/><br/> - シークレットのアクセス許可: シークレットの管理操作 (取得、一覧表示、設定)<br/><br/> - 証明書 (一覧表示、取得)。
**ディスク暗号化セット (CMK によるサーバー側暗号化)** | お使いの VM に CMK によるサーバー側暗号化が使用されている場合、ソース リージョンのディスク暗号化セットに加え、宛先リージョンにもディスク暗号化セットが必要となります。 [ディスク暗号化セットを作成](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set)してください。<br/><br/> カスタマー マネージド キーに HSM キーを使用している場合、リージョン間の移動はサポートされません。
**ターゲット リージョンのクォータ** | サブスクリプションには、ターゲット リージョンで移動するリソースを作成するのに十分なクォータが必要です。 クォータがない場合は、[追加の制限を要求](../azure-resource-manager/management/azure-subscription-service-limits.md)します。
**ターゲット リージョンの料金** | VM の移動先となるターゲット リージョンに関連付する料金と課金を確認します。 [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用すると便利です。


## <a name="verify-user-permissions-on-key-vault-for-vms-using-azure-disk-encryption-ade"></a>Azure Disk Encryption (ADE) を使用して VM のキー コンテナーに対するユーザーのアクセス許可を確認する

Azure Disk Encryption が有効になっている VM を移動する場合、[以下](#copy-the-keys-to-the-destination-key-vault)に記載されているスクリプトを実行する必要があります。スクリプトを実行するユーザーには適切なアクセス許可が必要です。 必要なアクセス許可については、下の表を参照してください。 アクセス許可を変更するオプションは、Azure portal でキー コンテナーに移動して確認できます。 **[設定]** の **[アクセス ポリシー]** を選択してください。

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png" alt-text="キー コンテナーのアクセス ポリシーを開くためのボタン。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png":::

ユーザーのアクセス許可がない場合、 **[アクセス ポリシーの追加]** を選択してアクセス許可を指定します。 ユーザー アカウントに既にポリシーがある場合は、 **[ユーザー]** で、下の表に従ってアクセス許可を設定します。

ADE を使用する Azure VM には次のバリエーションが考えられ、また、それに応じて、関連するコンポーネントのアクセス許可を設定する必要があります。
- 既定のオプション (ディスクはシークレットのみを使用して暗号化されます)
- [キー暗号化キー](../virtual-machines/windows/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek)を使用して強化されたセキュリティ

### <a name="source-region-keyvault"></a>ソース リージョンのキー コンテナー

スクリプトを実行するユーザーには、次のアクセス許可を設定する必要があります。 

**コンポーネント** | **必要なアクセス許可**
--- | ---
シークレット|  取得のアクセス許可 <br> </br> **[シークレットのアクセス許可]** >   **[シークレットの管理操作]** で **[取得]** を選択します 
[キー] <br> </br> キー暗号化キー (KEK) を使用している場合は、シークレットに加えて、このアクセス許可が必要です| 取得と暗号化解除のアクセス許可 <br> </br> **[キーのアクセス許可]**  >  **[キーの管理操作]** で **[取得]** を選択します。 **[暗号化操作]** で、 **[暗号化解除]** を選択します。

### <a name="destination-region-keyvault"></a>宛先リージョンのキー コンテナー

**[アクセス ポリシー]** で、 **[Azure Disk Encryption (ボリューム暗号化用)]** が有効になっていることを確認します。 

スクリプトを実行するユーザーには、次のアクセス許可を設定する必要があります。 

**コンポーネント** | **必要なアクセス許可**
--- | ---
シークレット|  設定のアクセス許可 <br> </br> **[シークレットのアクセス許可]** >   **[シークレットの管理操作]** で **[設定]** を選択します 
[キー] <br> </br> キー暗号化キー (KEK) を使用している場合は、シークレットに加えて、このアクセス許可が必要です| 取得、作成、暗号化のアクセス許可 <br> </br> **[キーのアクセス許可]**  >  **[キーの管理操作]** で **[取得]** と **[作成]** を選択します。 **[暗号化操作]** で **[暗号化]** を選択します。

上のアクセス許可に加え、Resource Mover が皆さんに代わって Azure リソースへのアクセスに使用する[マネージド システム ID](./common-questions.md#how-is-managed-identity-used-in-resource-mover) のアクセス許可を宛先キー コンテナーに追加する必要があります。 

1. **[設定]** の **[アクセス ポリシーの追加]** を選択します。 
2. **[プリンシパルの選択]** で MSI を検索します。 MSI の名前は ```movecollection-<sourceregion>-<target-region>-<metadata-region>``` です。 
3. MSI に関して、次のアクセス許可を追加します。

**コンポーネント** | **必要なアクセス許可**
--- | ---
シークレット|  取得と一覧のアクセス許可 <br> </br> **[シークレットのアクセス許可]** >   **[シークレットの管理操作]** で **[取得]** と **[一覧]** を選択します 
[キー] <br> </br> キー暗号化キー (KEK) を使用している場合は、シークレットに加えて、このアクセス許可が必要です| 取得と一覧のアクセス許可 <br> </br> **[キーのアクセス許可]**  >  **[キーの管理操作]** で **[取得]** と **[一覧]** を選択します。



### <a name="copy-the-keys-to-the-destination-key-vault"></a>宛先キー コンテナーにキーをコピーする

提供されているスクリプトを使用して、暗号化のシークレットとキーをソース キー コンテナーから宛先キー コンテナーにコピーする必要があります。

- PowerShell でスクリプトを実行します。 最新の PowerShell バージョンを実行することをお勧めします。
- このスクリプトには、具体的に次のモジュールが必要となります。
    - Az.Compute
    - Az.KeyVault (バージョン 3.0.0)
    - Az.Accounts (バージョン 2.2.3)

次のように実行します。

1. GitHub で[スクリプト](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1)に移動します。
2. スクリプトの内容をローカル ファイルにコピーし、*Copy-keys.ps1* という名前を付けます。
3. スクリプトを実行します。
4. Azure にサインインします。
5. **[ユーザー入力]** ポップアップで、ソース サブスクリプション、リソース グループ、ソース VM を選択します。 次に、ターゲットの場所を選択し、ディスク暗号化とキー暗号化に使用するターゲット コンテナーを選択します。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/script-input.png" alt-text="スクリプトの値を入力するためのポップアップ。" :::


6. スクリプトが完了すると、CopyKeys の成功を示す情報が画面に出力されます。

## <a name="prepare-vms"></a>VM を準備する

1. [VM が要件を満たしていることを確認](#prerequisites)したら、移動する VM をオンにします。 宛先リージョンで利用するすべての VM ディスクを VM にアタッチし、初期化する必要があります。
3. VM に最新の信頼されたルート証明書と、更新された証明書失効リスト (CRL) があることを確認します。 これを行うには、次の手順を実行します。
    - Windows VM で、最新の Windows 更新プログラムをインストールします。
    - Linux VM では、ディストリビューター ガイダンスに従って、マシンに最新の証明書と CRL が存在するようにします。 
4. 次のようにして VM からのアウトバウンド接続を許可します。
    - 送信接続を制御するために URL ベースのファイアウォール プロキシを使用する場合は、これらの [URL](support-matrix-move-region-azure-vm.md#url-access) へのアクセスを許可します
    - ネットワーク セキュリティ グループ (NSG) ルールを使用して送信接続を制御する場合は、これらの[サービス タグ ルール](support-matrix-move-region-azure-vm.md#nsg-rules)を作成します。

## <a name="select-resources-to-move"></a>移動するリソースを選択する


- 選択したソース リージョン内の任意のリソース グループから、サポートされている任意のリソースの種類を選択できます。  
- ソース リージョンと同じサブスクリプション内のターゲット リージョンにリソースを移動します。 サブスクリプションを変更する場合は、リソースの移動後に変更を行うことができます。

次のようにリソースを選択します。

1. Azure portal で、*Resource Mover* を検索します。 その後、 **[サービス]** で **[Azure Resource Mover]** を選択します。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/search.png" alt-text="Azure portal での resource mover の検索結果" :::

2. **[概要]** で **[Move across regions]\(リージョン間で移動する\)** をクリックします。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png" alt-text="別のリージョンに移動するリソースを追加するためのボタン。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png":::

3. **[リソースの移動]**  >  **[Source + destination]\(ソース + 宛先\)** で、ソース サブスクリプションおよびリージョンを選択します。
4. **[宛先]** で、VM の移動先となるリージョンを選択します。 続けて、 **[次へ]** をクリックします。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/source-target.png" alt-text="ソースと宛先のリージョンを選択するページ。" :::

5. **[移動するリソース]** で、 **[リソースの選択]** をクリックします。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-resources.png" alt-text="移動するリソースを選択するためのボタン。" :::

6. **[リソースの選択]** で、VM を選択します。 追加できるのは、[移動がサポートされているリソース](#prepare-vms)のみです。 次に、 **[Done]** をクリックします。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-vm.png" alt-text="移動する VM を選択するページ。" :::

    > [!NOTE]
    >  このチュートリアルでは、カスタマー マネージド キーによるサーバー側暗号化を使用する VM (rayne-vm) と、ディスク暗号化が有効になった VM (rayne-vm-ade) を選択します。

7.  **[移動するリソース]** で、 **[次へ]** をクリックします。
8. **[確認]** で、ソースと宛先の設定を確認します。 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/review.png" alt-text="設定を確認し、移動を続行するためのページ。" :::

9. **[続行]** をクリックして、リソースの追加を開始します。
10. 進行状況を追跡するための通知アイコンを選択します。 追加プロセスが正常に完了した後、通知の **[Added resources for move]\(移動対象に追加されたリソース\)** をクリックします。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png" alt-text="リソースが正常に追加されたことを確認するための通知。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png":::
    
    
11. 通知をクリックした後、 **[Across regions]\(リージョン間\)** ページでリソースを確認します。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-prepare-pending.png" alt-text="追加されて準備が保留中となっているリソースを表示するページ。" :::

> [!NOTE]
> - 追加したリソースは "*準備が保留中*" の状態になります。
> - VM のリソース グループが自動的に追加されます。
> - 宛先リージョン内の既存のリソースを使用するように **[Destination configuration]\(宛先の構成\)** エントリを変更する場合、そのリソースについては移動を開始する必要がないため、リソースの状態が "*コミット保留中*" に設定されます。
> - 追加済みのリソースを削除する場合、その方法は、移動プロセスのどの段階にあるかによって異なります。 [詳細については、こちらを参照してください](remove-move-resources.md)。


## <a name="resolve-dependencies"></a>依存関係を解決する

1. いずれかのリソースの **[問題]** 列に *[依存関係の検証]* メッセージが表示されている場合は、 **[依存関係の検証]** ボタンを選択します。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png" alt-text="依存関係を確認するためのボタン。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png":::

    検証プロセスが開始されます。
2. 依存関係が見つかった場合は、 **[依存関係の追加]** をクリックします  

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png" alt-text="依存関係を追加するためのボタン。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png":::


3. **[依存関係の追加]** では、既定の **[Show all dependencies]\(すべての依存関係を表示する\)** オプションのままにしてください。

    - **[Show all dependencies]\(すべての依存関係を表示する\)** を選択すると、リソースの直接の依存関係と間接の依存関係がすべて反復処理されます。 たとえば、VM について、NIC、仮想ネットワーク、ネットワーク セキュリティ グループ (NSG) などが表示されます。
    - **[Show first level dependencies only]\(第 1 レベルの依存関係のみ表示する\)** を選択すると、直接の依存関係のみが表示されます。 たとえば、VM について、NIC は表示されますが、仮想ネットワークは表示されません。
 
4. 追加する依存リソースを選択し、 **[依存関係の追加]** を選択します。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png" alt-text="依存関係リストから依存関係を選択する。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png":::

5. もう一度、依存関係を検証します。 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png" alt-text="再度検証するためのページ。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png":::

## <a name="assign-destination-resources"></a>宛先のリソースを割り当てる

暗号化に関連付けられている宛先のリソースは、手動で割り当てる必要があります。

- Azure Disk Encryption (ADE) が使用されている VM を移動する場合、宛先リージョン内のキー コンテナーが依存関係として表示されます。
- カスタマー マネージド キー (CMK) によるサーバー側暗号化が使用されている VM を移動する場合は、宛先リージョン内のディスク暗号化セットが依存関係として表示されます。 
- このチュートリアルでは、ADE が有効にされている VM と CMK が使用されている VM を移動するため、宛先のキー コンテナーとディスク暗号化セットの両方が依存関係として表示されます。

手動での割り当ては、次のようにして行います。

1. ディスク暗号化セットのエントリで、 **[Destination configuration]\(宛先の構成\)** 列の **[Resource not assigned]\(リソースが割り当てられていません\)** を選択します。
2. **[構成設定]** で、宛先のディスク暗号化セットを選択します。 続けて、 **[変更の保存]** を選択します。
3. 変更しているリソースの依存関係を保存して検証するか、単に変更を保存し、変更したものすべてをまとめて検証するかを選択できます。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png" alt-text="宛先リージョンのディスク暗号化セットを選択するためのページ。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png":::

    宛先リソースを追加すると、ディスク暗号化セットの状態が "*移動のコミットが保留中*" になります。
3. キー コンテナーのエントリで、 **[Destination configuration]\(宛先の構成\)** 列の **[Resource not assigned]\(リソースが割り当てられていません\)** を選択します。 **[構成設定]** で、宛先キー コンテナーを選択します。 変更を保存します。 

この段階で、ディスク暗号化セットとキー コンテナーがどちらも "*移動のコミットが保留中*" の状態になります。

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png" alt-text="その他のリソースの準備を選択するためのページ。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png":::

暗号化リソースの移動プロセスをコミットして完了するには、次のようにします。

1. **[Across regions]\(リージョン間\)** でリソース (ディスク暗号化セットまたはキー コンテナー) を選択し、 **[Commit move]\(移動のコミット\)** を選択します。
2. **[リソースの移動]** で、 **[コミット]** をクリックします。

> [!NOTE]
> 移動をコミットした後、リソースは "*ソースの削除が保留中*" の状態になります。


## <a name="move-the-source-resource-group"></a>ソース リソース グループを移動する 

VM の準備と移動を行う前に、VM リソース グループがターゲット リージョンに存在している必要があります。 

### <a name="prepare-to-move-the-source-resource-group"></a>ソース リソース グループの移動を準備する

準備プロセス中に、Resource Mover によって、リソース グループの設定を使用して Azure Resource Manager (ARM) テンプレートが生成されます。 リソース グループ内のリソースは影響を受けません。

次のように準備します。

1. **[Across regions]\(リージョン間\)** で、ソース リソース グループ、 **[準備]** の順に選択します。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png" alt-text="リソース グループを準備する。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png":::

2. **[リソースの準備]** で、 **[準備]** をクリックします。

> [!NOTE]
> リソース グループを準備した後、"*移動の開始が保留中*" の状態になります。 

 
### <a name="move-the-source-resource-group"></a>ソース リソース グループを移動する

次のように移動を開始します。

1. **[Across regions]\(リージョン間\)** で、リソース グループ、 **[移動の開始]** の順に選択します

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png" alt-text="移動を開始するためのボタン。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png":::

2. **[リソースの移動]** で、 **[移動の開始]** をクリックします。 リソース グループは、"*移動の開始が進行中*" の状態に移ります。   
3. 移動を開始した後、生成された ARM テンプレートに基づいて、ターゲット リソース グループが作成されます。 ソース リソース グループは、"*移動のコミットが保留中*" の状態に移ります。

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png" alt-text="移動のコミットが保留中の状態を確認する。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png":::

移動プロセスをコミットして完了するには、次のようにします。

1. **[Across regions]\(リージョン間\)** で、リソース グループ、 **[Commit move]\(移動のコミット\)** の順に選択します。
2. **[リソースの移動]** で、 **[コミット]** をクリックします。

> [!NOTE]
> 移動をコミットした後、ソース リソース グループは "*ソースの削除が保留中*" の状態になります。

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png" alt-text="移動の削除が保留中の状態を確認する。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png":::

## <a name="prepare-resources-to-move"></a>移動するリソースを準備する

暗号化リソースとソース リソース グループを移動したら、"*準備が保留中*" の状態になっている他のリソースを移動するための準備を行うことができます。


1. **[Across regions]\(リージョン間\)** で再度検証を実行し、問題があれば解決します。
2. 移動を開始する前にターゲット設定を編集する場合は、リソースの **[Destination configuration]\(宛先の構成\)** 列にあるリンクを選択し、その設定を編集します。 ターゲット VM の設定を編集する場合、ターゲット VM のサイズをソース VM のサイズより小さくすることはできません。
3. "*準備が保留中*" の状態になっている移動対象リソースの **[Prepare]\(準備\)** を選択します。
3. **[リソースの準備]** で、 **[準備]** を選択します

    - 準備プロセス中に、Azure Site Recovery モビリティ エージェントが、レプリケートする VM にインストールされます。
    - VM データは、ターゲット リージョンに定期的にレプリケートされます。 これはソース VM には影響しません。
    - Resource Mover によって、他のソース グループ用の ARM テンプレートが生成されます。

リソースを準備した後、"*移動の開始が保留中*" の状態になります。

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png" alt-text="移動の開始が保留中状態のリソースを示すページ。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png":::



## <a name="initiate-the-move"></a>移動を開始する

リソースが準備され、移動を開始できるようになりました。 

1. **[Across regions]\(リージョン間\)** で、"*移動の開始が保留中*" 状態のリソースを選択します。 その後、 **[移動の開始]** をクリックします。
2. **[リソースの移動]** で、 **[移動の開始]** をクリックします。
3. 通知バーで移動の進行状況を追跡します。

    - VM の場合、レプリカ VM はターゲット リージョンに作成されます。 ソース VM がシャットダウンされ、ダウンタイム (通常は数分) が発生します。
    - Resource Mover によって、準備された ARM テンプレートを使用して他のリソースが再作成されます。 通常、ダウンタイムは発生しません。
    - リソースを移動した後、"*移動のコミットが保留中*" の状態になります。

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" alt-text="移動のコミットが保留中の状態にあるリソースを表示するページ。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" :::


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

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" alt-text="移動を確定するためにリソースをコミットするページ。" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" :::

3. 通知バーでコミットの進行状況を追跡します。

> [!NOTE]
> - 移動をコミットした後、VM のレプリケートが停止します。 ソース VM はコミットの影響を受けません。
> - コミットはソース ネットワーク リソースに影響しません。
> - 移動をコミットした後、リソースは "*ソースの削除が保留中*" の状態になります。



## <a name="configure-settings-after-the-move"></a>移動後に設定を構成する

- Mobility Service は VM から自動的にアンインストールされません。 手動でアンインストールします。あるいは、サーバーをもう一度移動する予定であれば、そのままにしておきます。
- 移動後、Azure ロールベースのアクセス制御 (Azure RBAC) 規則を変更します。

## <a name="delete-source-resources-after-commit"></a>コミット後にソース リソースを削除する

移動後に、必要に応じて、ソース リージョンのリソースを削除できます。 

1. **[Across Regions]\(リージョン間\)** で、削除する各ソース リソースを選択します。 次に、 **[ソースの削除]** を選択します。
2. **[ソースの削除]** で、削除しようとしているリソースを確認し、 **[削除の確認]** で「**yes**」と入力します。 この操作は元に戻せません。十分に確認してください。
3. 「**yes**」と入力したら、 **[ソースの削除]** を選択します。

> [!NOTE]
>  Resource Move ポータルで、リソース グループ、キー コンテナー、SQL Server サーバーを削除することはできません。 各リソースのプロパティ ページから個別に削除する必要があります。


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

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * 暗号化された Azure VM とその依存リソースを別の Azure リージョンに移動しました。


ここで、Azure SQL データベースとエラスティック プールを別のリージョンに移動してみます。

> [!div class="nextstepaction"]
> [Azure SQL リソースを移動する](./tutorial-move-region-sql.md)
