---
title: 既存の Azure Load Balancer で仮想マシン スケール セットを構成する - Azure portal
description: Azure portal を使用し、既存の Azure Load Balancer で仮想マシン スケール セットを構成する方法について説明します。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: cb7bfb9ac4b10b807ac186d087b0037953abd559
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91439511"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-portal"></a>Azure portal を使用して、既存の Azure Load Balancer で仮想マシン スケール セットを構成する

この記事では、既存の Azure Load Balancer で仮想マシン スケール セットを構成する方法について説明します。 

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。
- 仮想マシン スケール セットがデプロイされるサブスクリプション内の、既存の標準 SKU ロード バランサー。
- 仮想マシン スケール セット用の Azure Virtual Network。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。



## <a name="deploy-virtual-machine-scale-set-with-existing-load-balancer"></a>既存のロード バランサーで仮想マシン スケール セットをデプロイする

このセクションでは、既存の Azure ロード バランサーを使用して Azure portal 内に仮想マシン スケール セットを作成します。

> [!NOTE]
> 次の手順では、**myVNet** という名前の仮想ネットワークと **myLoadBalancer** という名前の Azure ロード バランサーが以前にデプロイされていることを前提としています。

1. 画面の左上にある **[リソースの作成]**  >  **[コンピューティング]**  >  **[仮想マシンスケールセット]** をクリックするか、または、マーケットプレース検索で「**仮想マシンスケールセット**」 を検索します。

2. **［作成］** を選択します

3. **[仮想マシン スケール セットの作成]** 中の **[基本]** タブで、次の情報を入力または選択します。

    | 設定                        | 値                                                                                                 |
    |--------------------------------|-------------------------------------------------------------------------------------------------------|
    | **プロジェクトの詳細**            |                                                                                                       |
    | サブスクリプション                   | Azure サブスクリプションを選択します。                                                                        |
    | リソース グループ                 | [新規作成] を選択し、**myResourceGroup** と入力して [OK] を選択するか、既存のリソース グループを選択します。 |
    | **スケール セットの詳細**          |                                                                                                       |
    | 仮想マシン スケール セットの名前 | 「**myVMSS**」と入力します                                                                                      |
    | リージョン                         | **[米国東部 2]** を選択します                                                                                    |
    | 可用性ゾーン              | **[なし]** を選択します                                                                                       |
    | **インスタンスの詳細**           |                                                                                                       |
    | Image                          | **[Ubuntu Server 18.04 LTS]** を選択します                                                                    |
    | Azure Spot インスタンス            | **[いいえ]**  を選択します                                                                                         |
    | サイズ                           | 既定値のままにします                                                                                      |
    | **管理者アカウント**      |                                                                                                       |
    | 認証の種類            | **[パスワード]** を選択します                                                                                   |
    | ユーザー名                       | 管理者ユーザー名を入力します        |
    | Password                       | 管理者パスワードを入力します    |
    | [パスワードの確認入力]               | 管理者パスワードを再入力します |


    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-01.png" alt-text="[仮想マシン スケール セットの作成] の [基本] タブが表示されているスクリーンショット。" border="true":::

4. **[ネットワーク]** タブを選択します。

5. **[ネットワーク]** タブで、この情報を入力または選択します。

     設定                           | 値                                                    |
    |-----------------------------------|----------------------------------------------------------|
    | **Virtual Network の構成** |                                                          |
    | 仮想ネットワーク                   | **myVNet** または既存の仮想ネットワークを選択します。      |
    | **負荷分散**                |                                                          |
    | ロード バランサーを使用する               | **[はい]** を選択します                                           |
    | **ロード バランサーの設定**       |                                                          |
    | 負荷分散のオプション            | **[Azure ロード バランサー]** を選択する                           |
    | ロード バランサーを選択する            | **myLoadBalancer** または既存のロード バランサーを選択します |
    | バックエンド プールを選択する             | **myBackendPool** または既存のバックエンド プールを選択します。  |

    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-02.png" alt-text="[仮想マシン スケール セットの作成] の [ネットワーク] タブが表示されているスクリーンショット。" border="true":::

6. **[管理]** タブを選択します。

7. **[管理]** タブで、 **[ブート診断]** を **[オフ]** に設定します。

8. 青色の **[確認および作成]** ボタンを選択します。

9. 設定をレビューし、 **[作成]** ボタンを選択します。

## <a name="next-steps"></a>次のステップ

この記事では、既存の Azure Load Balancer で仮想マシン スケール セットをデプロイしました。  仮想マシン スケール セットとロードバランサーの詳細については、以下を参照してください。

- [Azure Load Balancer の概要](load-balancer-overview.md)
- [仮想マシン スケール セットとは](../virtual-machine-scale-sets/overview.md)
