---
title: Azure Policy を通じて Automanage for virtual machines を有効にする
description: Azure portal で、組み込みの Azure Policy を通した Azure Automanage for VMs を有効にする方法について説明します。
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 8f679626b69bd855e86b94cdde51955edd068e8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91714901"
---
# <a name="enable-automanage-for-virtual-machines-through-azure-policy"></a>Azure Policy を通じて Automanage for virtual machines を有効にする

多数の VM に対して Automanage を有効にする場合は、組み込みの [Azure Policy](..\governance\azure-management.md) を使用してこれを行うことができます。 この記事では、Azure portal で適切なポリシーを見つけ、自動管理を有効にするためにそれを割り当てる方法について説明します。


## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、始める前に[アカウントを作成](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)してください。

> [!NOTE]
> 無料試用版アカウントでは、このチュートリアルで使用されている仮想マシンを利用できません。 従量課金制サブスクリプションにアップグレードしてください。

> [!IMPORTANT]
> Automanage を有効にするには、次の Azure RBAC アクセス許可が必要です。**所有者** ロール、または **共同作成者** ロールと **ユーザー アクセス管理者** ロールの併用。


## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com/) にサインインします。


## <a name="locate-and-assign-the-policy"></a>ポリシーを見つけて割り当てる

1. Azure portal で **[ポリシー]** に移動します。
1. **[定義]** ウィンドウに移動します。
1. **[カテゴリ]** ドロップダウンをクリックして、使用可能なオプションを表示します。
1. **[Enable Automanage – Azure virtual machine best practices]\(Automanage を有効にする - Azure 仮想マシンのベスト プラクティス\)** オプションを選択します。
1. これでリストが更新され、" *[Enable Automanage…]\(Automanage を有効にする…\)* " で始まる名前の組み込みポリシーが表示されます。
1. 組み込みポリシー名 " *[Enable Automanage – Azure virtual machine best practices]\(Automanage を有効にする - Azure 仮想マシンのベスト プラクティス\)* " をクリックします。
1. ポリシーをクリックすると、 **[定義]** タブが表示されます。

    > [!NOTE]
    > Azure Policy 定義を使用して、構成プロファイルやアカウントなどの Automanage のパラメーターを設定します。 ポリシーが適切な VM にのみ適用されるようにするためのフィルターも設定します。

1. **[割り当て]** ボタンをクリックして、割り当てを作成します。
1. **[基本]** タブで、" *[サブスクリプション]* " と" *[リソース グループ]* " を設定することで **[スコープ]** を指定します。

    > [!NOTE]
    > スコープを使用して、このポリシーの適用先となる VM を定義できます。 サブスクリプション レベルまたはリソース グループ レベルでアプリケーションを設定できます。 リソース グループを設定した場合、そのリソース グループ内に現在あるすべての VM または今後追加されるすべての VM 内に対して、Automanage が自動的に有効になります。 

1. **[パラメーター]** タブをクリックし、 **[Automanage アカウント]** と望ましい **[構成プロファイル]** を設定します。 
1. **[確認と作成]** タブで設定を確認します。
1. **[作成]** をクリックして割り当てを適用します。
1. **[定義]** の横にある **[割り当て]** タブで割り当てを確認します。

> [!NOTE]
> リソース グループまたはサブスクリプション内の現在の VM に対してそのポリシーが有効になるまで、しばらく時間がかかります。


## <a name="next-steps"></a>次の手順 

Azure portal を使用して Azure Automanage for virtual machines を有効にする別の方法を確認します。 

> [!div class="nextstepaction"]
> [Azure portal で Azure Automanage for virtual machines を有効にする](quick-create-virtual-machines-portal.md)