---
title: VM 用にルーティング優先設定を構成する - Azure portal
description: Azure portal を使用して、ルーティング優先設定を選択してパブリック IP アドレスを持つ VM を作成する方法について説明します。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: af3d9e9fcf0dad6a5e51a3db87b63567d701970e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84687992"
---
# <a name="configure-routing-preference-for-a-vm-using-the-azure-portal"></a>Azure portal を使用して VM 用にルーティング優先設定を構成する

この記事では、仮想マシン用にルーティング優先設定を構成する方法について説明します。 ルーティング優先設定オプションとして **[インターネット]** を選択すると、VM からインターネットへのトラフィックは ISP ネットワーク経由でルーティングされます。 既定のルーティングは、Microsoft グローバル ネットワーク経由です。

この記事では、Azure portal を使用して、パブリック インターネット経由でトラフィックをルーティングするように設定されたパブリック IP を持つ仮想マシンを作成する方法について説明します。

> [!IMPORTANT]
> 現在、ルーティング優先設定はパブリック プレビューの段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="register-the-feature-for-your-subscription"></a>サブスクリプションに機能を登録する
現在、ルーティング優先設定の機能はプレビューの段階です。 次のように Azure PowerShell を使用して、サブスクリプションに機能を登録する必要があります。
```azurepowershell
Register-AzProviderFeature -FeatureName AllowRoutingPreferenceFeature ProviderNamespace Microsoft.Network
```

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://preview.portal.azure.com/) にサインインします。

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

1. Azure Portal の左上隅にある **[+ リソースの作成]** を選択します。
2. **[Compute]** を選択し、 **[Windows Server 2016 VM]** または別の任意のオペレーティング システムを選択します。
3. 次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、 **[OK]** を選択します。

    |設定|値|
    |---|---|
    |名前|myVM|
    |ユーザー名| 任意のユーザー名を入力します。|
    |Password| 任意のパスワードを入力します。 パスワードは 12 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。|
    |サブスクリプション| サブスクリプションを選択します。|
    |Resource group| **[既存のものを使用]** を選択し、 **[myResourceGroup]** を選択します。|
    |場所| **[米国東部]** を選択します。|

4. VM のサイズを選択して、 **[選択]** を選択します。
5. **[ネットワーク]** タブで、 **[パブリック IP アドレス]** の **[新規作成]** をクリックします。
6. 次の図に示すように、「*myPublicIpAddress*」と入力し、SKU として **[Standard]** を選択し、ルーティング優先設定として **[インターネット]** を選択して、 **[OK]** をクリックします。

   ![[静的] の選択](./media/tutorial-routing-preference-virtual-machine-portal/routing-preference-internet-new.png)

6. **[パブリック受信ポートを選択]** で、ポートを選択するか、ポートなしを選択します。 インターネットから Windows Server 仮想マシンへのリモート アクセスを有効にするために、ポータル 3389 が選択されています。 運用環境のワークロードでは、インターネットからポート 3389 を開くことはお勧めしません。

   ![ポートの選択](./media/tutorial-routing-preference-virtual-machine-portal/pip-ports-new.png)

7. 残りの既定値を受け入れて、 **[OK]** を選択します。
8. **[概要]** ページで、 **[作成]** を選択します。 仮想マシンのデプロイには、数分かかります。
9. 仮想マシンをデプロイしたら、ポータルの上部にある検索ボックスに「*myPublicIpAddress*」と入力します。 検索結果に **myPublicIpAddress** が表示されたら、それを選びます。
10. 次の図に示すように、割り当てられているパブリック IP アドレスと、そのアドレスが **myVM** 仮想マシンに割り当てられていることが表示されます。

    ![パブリック IP アドレスの表示](./media/tutorial-routing-preference-virtual-machine-portal/pip-properties-new.png)

11. **[ネットワーク]** を選択し、NIC **mynic** をクリックし、パブリック IP アドレスを選択して、ルーティング優先設定が **[インターネット]** であることを確認します。
    ![パブリック IP アドレスを表示する](./media/tutorial-routing-preference-virtual-machine-portal/pip-routing-internet-new.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループとそれに含まれるすべてのリソースが不要になったら、それらを削除します。

1. ポータル上部の **[検索]** ボックスに「*myResourceGroup*」と入力します。 検索結果に **[myResourceGroup]** が表示されたら、それを選択します。
2. **[リソース グループの削除]** を選択します。
3. **[TYPE THE RESOURCE GROUP NAME:]\(リソース グループ名を入力してください:\)** に「*myResourceGroup*」と入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ
- [ルーティング優先設定が指定されたパブリック IP アドレス](routing-preference-overview.md)について詳しく学習する。
- Azure での[パブリック IP アドレス](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)について詳しく学習する。
- すべての[パブリック IP アドレスの設定](virtual-network-public-ip-address.md#create-a-public-ip-address)について詳しく学習する。
