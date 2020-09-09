---
title: ポータルを使用して Azure 専用ホストをデプロイする
description: ポータルを使用して専用ホストに VM をデプロイします。
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: a4ce9d360e226a9934c30b749ea0284f5ace9cf0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284593"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>ポータルを使用して専用ホストに VM をデプロイする

この記事では、仮想マシン (VM) をホストするための Azure [専用ホスト](dedicated-hosts.md)を作成する方法について説明します。 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>VM の作成

1. Azure portal の左上隅にある **[リソースの作成]** を選択します。
1. **[新規]** ページの **[人気順]** で、 **[Windows Server 2016 Datacenter]** を選択します。
1. **[基本]** タブの **[プロジェクトの詳細]** で、正しいサブスクリプションが選択されていることを確認し、 **[リソース グループ]** として *myDedicatedHostsRG* を選択します。 
1. **[Instance details] (インスタンスの詳細)** で、 **[仮想マシン名]** として「*myVM*」と入力し、 **[場所]** として *[米国東部]* を選択します。
1. **[可用性オプション]** で **[可用性ゾーン]** を選択し、ドロップダウンから *[1]* を選択します。
1. サイズでは、 **[サイズの変更]** を選択します。 使用可能なサイズの一覧で、Esv3 シリーズの 1 つを選択します (**Standard E2s v3** など)。 使用可能なサイズをすべて表示するには、フィルターをクリアすることが必要な場合があります。
1. **[Administrator account] (管理者アカウント)** で、ユーザー名 (*azureuser* など) とパスワードを指定します。 パスワードは 12 文字以上で、[定義された複雑さの要件](faq.md#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。
1. **[受信ポートの規則]** で **[選択したポートを許可する]** を選択し、ドロップダウンから **[RDP (3389)]** を選択します。
1. ページの上部にある **[詳細設定]** タブを選択し、 **[ホスト]** セクションで、 **[ホスト グループ]** として *myHostGroup* を、 **[ホスト]** として *myHost* を選択します。 
    ![ホスト グループとホストを選択する](./media/dedicated-hosts-portal/advanced.png)
1. 残りの既定値はそのままにして、ページの一番下にある **[Review + create] (確認および作成)** ボタンを選択します。
1. 検証が成功したことを示すメッセージが表示されたら、 **[作成]** を選択します。

## <a name="add-an-existing-vm"></a>既存の VM を追加する 

既存の VM を専用のホストに追加することはできますが、最初に VM を Stop\Deallocated とする必要があります。 VM を専用のホストに移動する前に、その VM 構成がサポートされていることを確認してください。

- VM サイズは、専用ホストと同じサイズ ファミリである必要があります。 たとえば、専用のホストが DSv3 の場合、VM のサイズは Standard_D4s_v3 になる可能性がありますが、Standard_A4_v2 となる可能性はありません。 
- VM は、専用ホストと同じリージョンに配置する必要があります。
- VM は、近接通信配置グループの一部になることはありません。 専用のホストに移動する前に、近接通信配置グループから VM を削除してください。 詳細については、「[近接配置グループからの VM の移動](./proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)」を参照してください
- VM は、可用性セット内に置くことはできません。
- VM が可用性ゾーン内にある場合は、ホスト グループと同じ可用性ゾーンである必要があります。 VM とホスト グループの可用性ゾーンの設定が一致している必要があります。

[ポータル](https://portal.azure.com)を使用して、VM を専用ホストに移動します。

1. VM のページを開きます。
1. **[停止]** を選択して VM の停止\割り当ての解除を行います。
1. 左メニューから **[構成]** を選択します。
1. ドロップダウン メニューからホスト グループとホストを選択します。
1. 完了したら、ページの最上部で **[保存]** を選択します。
1. VM がホストに追加されたら、左メニューから **[概要]** を選択します。
1. ページの最上部で **[開始]** を選択して VM を再起動します。

## <a name="next-steps"></a>次のステップ

- 詳細については、[専用ホスト](dedicated-hosts.md)の概要に関するページを参照してください。 

- [こちら](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)には、リージョン内の回復性を最大にするためにゾーンと障害ドメインの両方を使用するサンプル テンプレートがあります。

- [Azure PowerShell](dedicated-hosts-powershell.md) を使用して専用ホストをデプロイすることもできます。
