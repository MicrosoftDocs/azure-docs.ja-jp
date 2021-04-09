---
title: Azure DevTest Labs でカスタマー マネージド キーを使用して OS ディスクを暗号化する
description: Azure DevTest Labs でカスタマー マネージド キーを使用してオペレーティング システム (OS) のディスクを暗号化する方法について説明します。
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: 26ef4ff1529483da9956c6dcc43807af0ffd6463
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96341210"
---
# <a name="encrypt-operating-system-os-disks-using-customer-managed-keys-in-azure-devtest-labs"></a>Azure DevTest Labs でカスタマー マネージド キーを使用してオペレーティング システム (OS) のディスクを暗号化する
サーバー側暗号化 (SSE) によってデータが保護され、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。 SSE では、Azure のマネージド ディスク (OS およびデータ ディスク) に格納されているお使いのデータをクラウドに永続化するときに、既定で保存時に自動的に暗号化されます。 Azure での[ディスク暗号化](../virtual-machines/disk-encryption.md)について確認してください。 

DevTest Labs では、ラボの一部として作成されたすべての OS ディスクとデータ ディスクが、プラットフォームで管理されるキーを使用して暗号化されます。 ただし、ラボの所有者は、独自のキーを使用してラボの仮想マシンの OS ディスクを暗号化することを選択できます。 独自のキーを使用して暗号化を管理する場合は、ラボの OS ディスク内のデータの暗号化に使用する **カスタマー マネージド キー** を指定できます。 カスタマー マネージド キーを使用したサーバー側暗号化 (SSE) と、その他のマネージド ディスクの暗号化の種類の詳細については、「[カスタマー マネージド キー](../virtual-machines/disk-encryption.md#customer-managed-keys)」を参照してください。 また、[カスタマー マネージド キーを使用する場合の制限](../virtual-machines/disks-enable-customer-managed-keys-portal.md#restrictions)に関するページも参照してください。

> [!NOTE]
> - 現在、カスタマー マネージド キーを使用したディスク暗号化は、DevTest Labs の OS ディスクでのみサポートされています。 
> 
> - この設定は、ラボで新しく作成された OS ディスクに適用されます。 ある時点でディスク暗号化セットを変更することを選択した場合、ラボ内の古いディスクは以前のディスク暗号化セットを使用して引き続き暗号化されたままになります。 

次の項では、ラボ所有者がカスタマー マネージド キーを使用して暗号化を設定する方法について説明します。

## <a name="pre-requisites"></a>前提条件

1. ディスク暗号化が設定されていない場合は、この記事の手順に従って、[Key Vault とディスク暗号化セットを設定](../virtual-machines/disks-enable-customer-managed-keys-portal.md)します。 ディスク暗号化セットの次の要件に注意してください。 

    - ディスク暗号化セットは、**ラボと同じリージョンおよびサブスクリプション** にある必要があります。 
    - ラボの OS ディスクを暗号化するために使用されるディスク暗号化セットに対する少なくとも **閲覧者レベルのアクセス許可** がユーザー (ラボ所有者) にあることを確認します。 
1. 2020 年 8 月 1 日より前に作成されたラボの場合、ラボ所有者はラボのシステム割り当て ID が有効になっていることを確認する必要があります。 これを行うには、ラボ所有者は自分のラボに移動して、 **[構成とポリシー]** をクリックし、 **[ID (プレビュー)]** ブレードをクリックし、システム割り当て ID の **[状態]** を **[オン]** に変更して、 **[保存]** をクリックします。 2020 年 8 月 1 日以降に作成された新しいラボの場合、ラボのシステム割り当て ID は既定で有効になります。 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/managed-keys.png" alt-text="マネージド キー":::
1. ラボですべてのラボの OS ディスクの暗号化を処理するには、ラボ所有者はディスク暗号化セットに対する閲覧者ロールと基になる Azure サブスクリプションに対する仮想マシン共同作成者ロールをラボの **システム割り当て ID** に明示的に付与する必要があります。 ラボ所有者は、次の手順を実行してこれを行うことができます。

   
    1. Azure リソースへのユーザー アクセスを管理できるように、Azure のサブスクリプション レベルで[ユーザー アクセス管理者ロール](../role-based-access-control/built-in-roles.md#user-access-administrator)のメンバーになっていることを確認します。 
    1. **[ディスク暗号化の設定]** ページで、左側のメニューから **[アクセス制御 (IAM)]** を選択します。 
    1. ツールバーの **[+ 追加]** を選択し、 **[ロールの割り当ての追加]** を選択します。  

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/add-role-management-menu.png" alt-text="[ロールの割り当ての追加] - メニュー":::
    1. **[ロールの割り当ての追加]** ページで、 **[閲覧者]** ロール、またはより高いアクセス許可を持つロールを選択します。 
    1. ディスク暗号化セットが使用されるラボ名を入力し、ドロップダウン リストからラボ名 (ラボのシステム割り当て ID) を選択します。 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/select-lab.png" alt-text="システムで管理されているラボの ID を選択します":::        
    1. ツールバーの **[保存]** を選択します。 

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/save-role-assignment.png" alt-text="ロールの割り当ての保存":::
3. **[サブスクリプション]**  ->  **[アクセス制御 (IAM)]** ページを使用して、ラボの **システム割り当て ID** を **仮想マシン共同作成者** ロールに追加します。 この手順は前の手順と似ています。 

    
    1. Azure Portal の **[サブスクリプション]** ページに移動します。 
    1. **[アクセス制御 (IAM)]** を選択します。 
    1. ツールバーの **[+ 追加]** を選択し、 **[ロールの割り当ての追加]** を選択します。 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/subscription-access-control-page.png" alt-text="[サブスクリプション] -> [アクセス制御 (IAM)] ページ":::
    1. **[ロールの割り当ての追加]** ページで、ロールに **[仮想マシン共同作成者]** を選択します。
    1. ラボ名を入力し、ドロップダウン リストから **ラボ名** (ラボのシステム割り当て ID) を選択します。 
    1. ツールバーの **[保存]** を選択します。 

## <a name="encrypt-lab-os-disks-with-a-customer-managed-key"></a>カスタマー マネージド キーを使用してラボの OS ディスクを暗号化する 

1. Azure portal のラボのホーム ページで、左側のメニューから **[構成とポリシー]** を選択します。 
1. **[構成とポリシー]** ページで、 **[暗号化]** セクションの **[ディスク (プレビュー)]** を選択します。 既定では、 **[暗号化の種類]** には **[プラットフォームで管理されたキーを使用した保存時の暗号化]** が設定されます。

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disks-page.png" alt-text="[構成とポリシー] ページの [ディスク] タブ":::
1. **[暗号化の種類]** で、ドロップダウン リストから **[カスタマー マネージド キーを使用した保存時の暗号化]** を選択します。 
1. **[ディスク暗号化セット]** で、前の手順で作成したディスク暗号化セットを選択します。 これは、ラボのシステム割り当て ID がアクセスできるディスク暗号化セットと同じです。
1. ツールバーの **[保存]** を選択します。 

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disk-encryption-set.png" alt-text="カスタマー マネージド キーによる暗号化を有効にする":::
1. メッセージ ボックス (「*この設定は、ラボで新しく作成されたマシンに適用されます。古い OS ディスクは、古いディスク暗号化設定で暗号化されたままになります*」というテキストが表示されます) で、 **[OK]** を選択します。 

    構成が完了すると、ラボの OS ディスクは、ディスク暗号化セットを使用して取得されたカスタマー マネージド キーで暗号化されます。 
   
## <a name="how-to-validate-if-disks-are-being-encrypted"></a>ディスクが暗号化されているかどうかを検証する方法

1. ラボでカスタマー マネージド キーを使用してディスク暗号化を有効にした後に作成されたラボ仮想マシンにアクセスします。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/enabled-encryption-vm.png" alt-text="ディスク暗号化が有効になっている VM":::
1. VM のリソース グループをクリックし、OS ディスクをクリックします。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/vm-resource-group.png" alt-text="VM リソース グループ":::
1. [暗号化] に移動し、選択した [ディスク暗号化セット] で暗号化が [カスタマー マネージド キー] に設定されているかどうかを確認します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/validate-encryption.png" alt-text="暗号化を検証する":::
  
## <a name="next-steps"></a>次のステップ

次の記事をご覧ください。 

- [Azure Disk Encryption](../virtual-machines/disk-encryption.md)。 
- [カスタマー マネージド キー](../virtual-machines/disk-encryption.md#customer-managed-keys)。