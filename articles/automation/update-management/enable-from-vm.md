---
title: Azure VM から Azure Automation の Update Management を有効にする
description: この記事では、Azure VM から Update Management を有効にする方法について説明します。
services: automation
ms.subservice: update-management
ms.date: 11/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 39b5b1f988a118e609015f19a086fda434797356
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99050264"
---
# <a name="enable-update-management-from-an-azure-vm"></a>Azure VM から Update Management を有効にする

この記事では、1 つまたは複数の Azure 仮想マシン (VM) で [Update Management](overview.md) 機能を有効にする方法について説明します。 Azure VM を大規模に有効にするには、Update Management を使用して既存の Azure VM を有効にする必要があります。

> [!NOTE]
> Update Management を有効にする際、Log Analytics ワークスペースと Automation アカウントのリンクは特定のリージョンでのみサポートされています。 サポートされているマッピング ペアの一覧については、[Automation アカウントと Log Analytics ワークスペースのリージョン マッピング](../how-to/region-mappings.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* マシンを管理する [Automation アカウント](../automation-security-overview.md)。
* [仮想マシン](../../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="enable-the-feature-for-deployment"></a>デプロイために機能を有効にする

1. [[Azure portal]](https://portal.azure.com) で **[仮想マシン]** を選択するか、[ホーム] ページから **[仮想マシン]** を検索して選択します。

2. Update Management を有効にする VM を選択します。 VM は、ご使用の Automation アカウントの場所に関係なく任意のリージョンに存在できます。 あなたが

3. VM のページの **[操作]** で **[ゲスト + ホストの更新プログラム]** を選択します。

    ![左側のウィンドウから [ゲスト + ホストの更新プログラム] を選択します](media/enable-from-vm/select-guest-and-os-updates.png)

4. ワークスペースに対して VM が有効かどうかを判断するには、`Microsoft.OperationalInsights/workspaces/read` アクセス許可が必要です。 必要な追加のアクセス許可については、[マシンを有効にするために必要なアクセス許可](../automation-role-based-access-control.md#feature-setup-permissions)に関する記事をご覧ください。 一度に複数のマシンを有効にする方法については、「[Automation アカウントから Update Management を有効にする](./enable-from-automation-account.md)」をご覧ください。

5. [Update Management の有効化] ページで、Log Analytics ワークスペースおよび Automation アカウントを選択し、 **[有効にする]** をクリックして、Update Management を有効にします。 Update Management を有効にした後、VM から更新プログラムの評価を表示できるようになるまでに、約 15 分かかることがあります。

    ![Update Management の有効化](media/enable-from-vm/enable-update-management.png)

## <a name="next-steps"></a>次のステップ

* VM の Update Management を使用するには、「[Azure VM の更新プログラムとパッチの管理](manage-updates-for-vm.md)」を参照してください。

* Update Management の一般的なエラーのトラブルシューティングについては、[Update Management の問題のトラブルシューティング](../troubleshoot/update-management.md)に関する記事を参照してください。