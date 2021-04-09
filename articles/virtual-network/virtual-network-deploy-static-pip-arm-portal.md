---
title: 静的パブリック IP アドレスを持つ VM を作成する - Azure portal
description: Azure Portal を使用して、静的パブリック IP アドレスを持つ VM を作成する方法について説明します。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/12/2020
ms.author: allensu
ms.openlocfilehash: d416af3d3a8eb8ab8057f13cc0d9a133adcb849a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98221158"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Azure portal を使用して静的パブリック IP アドレスを持つ仮想マシンを作成する

パブリック IP アドレスを使用して、インターネットから仮想マシンへの通信を行うことができます。 

動的アドレスではなく静的パブリック IP アドレスを割り当てることで、アドレスの変更がないことが保証されます。   

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

1. ポータルの左上で、 **[リソースの作成]**  >  **[Compute]**  >  **[仮想マシン]** の順に選択するか、検索ボックスで「**仮想マシン**」を検索します。
   
2. **[仮想マシンの作成]** の **[Basic]** タブに、値を入力するか選択します。

    | 設定 | 値                                          |
    |-----------------------|----------------------------------|
    | **プロジェクトの詳細** |  |
    | サブスクリプション | Azure サブスクリプションを選択します。 |
    | リソース グループ | **[新規作成]** を選択します。 </br> **[名前]** に「**myResourceGroup**」と入力します。 </br> **[OK]** を選択します。 |
    | **インスタンスの詳細** |  |
    | 仮想マシン名 | 「**myVM**」と入力します |
    | リージョン | **[米国東部]** を選択します。 |
    | 可用性オプション | **[インフラストラクチャ冗長は必要ありません]** を選択します |
    | Image | **[Windows Server 2019 Datacenter - Gen1]** を選択します |
    | Azure Spot インスタンス | **[いいえ]**  を選択します |
    | サイズ | VM サイズを選択するか、既定の設定を使用します |
    | **管理者アカウント** |  |
    | ユーザー名 | ユーザー名を入力します |
    | Password | [パスワード] を入力します |
    | [パスワードの確認入力] | パスワードを再入力します |

3. **[ネットワーク]** タブまたは **[次へ: ディスク]** を選択してから **[次へ: ネットワーク]** を選択します。
  
4. [ネットワーク] タブで、次を選択または入力します。

    | 設定 | 値 |
    |-|-|
    | **ネットワーク インターフェイス** |  |
    | 仮想ネットワーク | 既定のネットワーク名を受け入れます。 |
    | Subnet | 既定のサブネット構成を受け入れます。 |
    | パブリック IP | **[新規作成]** を選択します。 </br> **[パブリック IP アドレスの作成]** で、名前に「**myPublicIP**」と入力します。 </br> **[SKU]** で **[Standard]** を選択します。 </br> **[割り当て]** で **[静的]** を選択します。 </br> **[OK]** を選択します。  |
    | NIC ネットワーク セキュリティ グループ | **[Basic]** を選択します|
    | パブリック受信ポート | **[選択したポートを許可する]** を選択します。 |
    | 受信ポートの選択 | **[RDP (3389)]** を選択します |

    > [!WARNING]
    > インターネットから Windows Server 仮想マシンへのリモート アクセスを有効にするために、ポータル 3389 が選択されています。 運用環境のワークロードを管理するために、インターネットに対してポート 3389 を開くことはお勧めしません。 </br> Azure 仮想マシンへのアクセスをセキュリティで保護する方法については、「 **[Azure Bastion とは](../bastion/bastion-overview.md)** 」を参照してください。
   
5. **[Review + create]\(レビュー + 作成\)** を選択します。 
  
6. 設定を確認し、 **[作成]** を選択します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループとそれに含まれるすべてのリソースが不要になったら、それらを削除します。

1. ポータル上部の **[検索]** ボックスに「**myResourceGroup**」と入力します。 検索結果に **[myResourceGroup]** が表示されたら、それを選択します。
2. **[リソース グループの削除]** を選択します。
3. **[TYPE THE RESOURCE GROUP NAME:]\(リソース グループ名を入力してください:\)** に「**myResourceGroup**」と入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

[IP アドレスの追加、変更、削除](virtual-network-network-interface-addresses.md)に関するページを参照してください。

* パブリック IP アドレスを動的から静的に変更する場合。
* プライベート IP アドレスを使用する。

パブリック IP アドレスには、[わずかながら料金が発生](https://azure.microsoft.com/pricing/details/ip-addresses)します。 サブスクリプションごとに使用できるパブリック IP アドレスの数には[制限](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)があります。

仮想マシンのパブリック IP アドレスの SKU は、バックエンド プールに追加されたときに Azure Load Balancer のパブリック IP SKU と一致している必要があります。 詳細については、[Azure Load Balancer](../load-balancer/skus.md) に関する記事を参照してください。

Azure [Public](https://www.microsoft.com/download/details.aspx?id=56519)、[US Government](https://www.microsoft.com/download/details.aspx?id=57063)、[China](https://www.microsoft.com/download/details.aspx?id=57062)、および [Germany](https://www.microsoft.com/download/details.aspx?id=57064) クラウドの範囲 (プレフィックス) の一覧をダウンロードできます。

- [静的パブリック IP アドレス](./public-ip-addresses.md#allocation-method)の詳細を参照してください。
- Azure での[パブリック IP アドレス](./public-ip-addresses.md#public-ip-addresses)について詳しく学習する。
- すべての[パブリック IP アドレスの設定](virtual-network-public-ip-address.md#create-a-public-ip-address)について詳しく学習する。
- [プライベート IP アドレス](./private-ip-addresses.md)と Azure 仮想マシンへの[静的プライベート IP アドレス](virtual-network-network-interface-addresses.md#add-ip-addresses)の割り当ての詳細を確認します。
