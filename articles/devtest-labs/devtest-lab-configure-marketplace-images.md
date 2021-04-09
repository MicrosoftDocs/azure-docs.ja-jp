---
title: Azure DevTest Labs で Azure Marketplace イメージの設定を構成する
description: Azure DevTest Labs で VM を作成する場合にどの Azure Marketplace イメージを使用できるようにするかを構成する
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a5158ed33bf253db1dbe0eb3232bc43d27ce15e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86512437"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Azure DevTest Labs で Azure Marketplace イメージの設定を構成する
DevTest Labs では、実際のラボで使用する Azure Marketplace イメージの構成方法に応じて、Azure Marketplace イメージに基づく VM を作成することができます。 この記事では、ラボで VM を作成する際に使用できるようにする Azure Marketplace イメージ (該当するものがある場合) を指定する方法を説明します。 これにより、チームが必要な Marketplace イメージにのみアクセスできるようになります。 

## <a name="specify-allowed-images-for-creating-vms"></a>VM の作成で許可するイメージを指定する
VM の作成時に許可する Azure Marketplace イメージを指定するには、次の手順に従ってください。 

1. [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
2. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
3. ラボの一覧でラボを選択します。 
4. ラボのページのホーム ページで、 **[構成とポリシー]** を選択します。
5. ラボの **[Configuration and policies]\(構成とポリシー\)** ページの **[Virtual Machine Bases]\(仮想マシンのベース\)** の下で **[Marketplace images]\(Marketplace イメージ\)** を選択します。
6. 対象となるすべての Azure Marketplace イメージを新しい VM のベースとして利用できるようにするかどうかを指定します。 **[はい]** を選択すると、次の条件をすべて満たす Azure Marketplace イメージのすべてがラボで使用できるようになります。
   
   * イメージは 1 つの VM を作成する、 **かつ**
   * イメージは Azure Resource Manager を使用して VM をプロビジョニングする、 **かつ**
   * イメージは追加のライセンス プランの購入を必要としない。
     
     イメージの使用を許可しない場合、または使用できるようにするイメージを指定する場合は、 **[いいえ]** を選択します。
     
     ![すべての Marketplace イメージを VM のベース イメージとして使用できるようにするオプション](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. 前の手順で **[いいえ]** を選択した場合は、 **[Allowed images (許可するイメージ)] の [すべて選択]** チェック ボックスが有効になります。 
   このオプションを検索ボックスと組み合わせて使用すると、一覧に表示されたすべてのアイテムをすばやく選択または選択解除することができます。
   * Azure Marketplace イメージの対応するチェック ボックスをオンにすると、VM の作成に使用するイメージが個別に選択されます。
   * どの Azure Marketplace イメージもラボでの使用を許可しない場合は、一覧で何も選択しないでください。
   
     ![どの Azure Marketplace イメージを VM のベース イメージとして使用できるようにするかを指定できる](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)


## <a name="troubleshoot"></a>トラブルシューティング
ラボで有効にする特定のイメージを見つけることができない場合は、次の手順を実行します。 

- コンピューティング VM の作成中にイメージを表示できるかどうかを確認します。
- 使用しているサブスクリプションの種類でイメージを使用できない可能性があります。 サブスクリプションの種類について、サブスクリプション管理者に確認します (例:MSDN、無料、従量課金制など)。 
- DevTest Labs での Gen 2 イメージのサポートは制限されています。 1 つのイメージに対して Gen 1 と Gen 2 の両方のバージョンを使用できる場合、DevTest Labs では、VM を作成するときに Gen 1 バージョンのイメージのみが表示されます。 回避策として、ラボの外でカスタムの Gen 2 イメージを作成し、それを使用して VM を作成します。 使用可能なイメージの Gen 2 バージョンのみがある場合は、DevTest Labs によってサポートされ、一覧に表示されます。 
      


## <a name="next-steps"></a>次のステップ
VM を作成するときに Azure Marketplace イメージを使用できるようにする方法を構成したら、次は [VM をラボに追加](devtest-lab-add-vm.md)します。

