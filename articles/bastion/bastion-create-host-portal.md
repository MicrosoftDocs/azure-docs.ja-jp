---
title: 'Azure Bastion ホストを作成する: ポータル'
description: この記事では、仮想ネットワーク内のすべての仮想マシンに RDP/SSH 接続を提供する Azure Bastion ホストを作成する方法について説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 04/24/2020
ms.author: cherylmc
ms.openlocfilehash: 1e0bc9a17a12afac6d91867b19aba0f90b7f6edb
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2020
ms.locfileid: "88270752"
---
# <a name="create-an-azure-bastion-host-using-the-portal"></a>ポータルを使用して Azure Bastion ホストを作成する

この記事では、Azure portal を利用し、Azure Bastion ホストを作成する方法について示します。 お使いの仮想ネットワークに Azure Bastion サービスをプロビジョニングすると、同じ仮想ネットワークのすべての VM でシームレスに RDP/SSH をご利用いただけます。 Azure Bastion デプロイは、サブスクリプションやアカウント、仮想マシン単位ではなく、仮想ネットワーク単位です。

すべての設定を手動で指定するか、既存の VM に対応する設定を使用することで、ポータルで新しい Bastion ホスト リソースを作成できます。 VM 設定を使用して要塞ホストを作成するには、[クイックスタート](quickstart-host-portal.md)に関する記事を参照してください。 任意で、[Azure PowerShell](bastion-create-host-powershell.md) を使用して Azure Bastion ホストを作成することができます。

## <a name="before-you-begin"></a>開始する前に

Bastion は、以下の Azure パブリック リージョンで利用できます:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Bastion ホストの作成

このセクションは、Azure portal から新しい Azure Bastion リソースを作成するのに役立ちます。

1. [[Azure portal]](https://portal.azure.com) メニュー上または **[ホーム]** ページから **[リソースの作成]** を選択します。

1. **[新規]** ページで、 *[Marketplace の検索]* フィールドに「**Bastion**」を入力し、**Enter** キーをクリックして検索結果を取得します。

1. 結果から **[Bastion]** をクリックします。 公開元が *Microsoft* で、カテゴリが*ネットワーク*であることを確認してください。

1. **[Bastion]** ページで、 **[作成]** をクリックして **[Bastion の作成]** ページを開きます。

1. **Bastion の作成**ページで、新しい Bastion リソースを構成します。 Bastion リソースに対する構成設定を指定します。

    ![Bastion の作成](./media/bastion-create-host-portal/settings.png)

    * **サブスクリプション**:新しい Bastion リソースの作成に使用する Azure サブスクリプションです。
    * **リソース グループ**:新しい Bastion リソースが作成される Azure リソース グループです。 既存のリソース グループがない場合は、新しいものを作成できます。
    * **Name**:新しい Bastion リソースの名前です。
    * **[リージョン]** :リソースが作成される Azure パブリック リージョンです。
    * **仮想ネットワーク**:Bastion リソースが作成される仮想ネットワークです。 このプロセス中にポータルで新しい仮想ネットワークを作成したり、既存の仮想ネットワークを使用したりすることができます。 既存の仮想ネットワークを使用している場合、Bastion サブネットの要件を反映するために、既存の仮想ネットワークに十分な空きアドレス空間があることを確認してくます。
    * **サブネット**:新しい Bastion ホストがデプロイされる仮想ネットワーク内のサブネットです。 このサブネットは Bastion ホスト専用となり、**AzureBastionSubnet** という名前を付ける必要があります。 このサブネットは、少なくとも /27 以上である必要があります。
    
       **AzureBastionSubnet** では[ユーザー定義ルート](../virtual-network/virtual-networks-udr-overview.md#custom-routes)はサポートされませんが、[ネットワーク セキュリティ グループ](bastion-nsg.md)はサポートされています。
    * **[パブリック IP アドレス]** : RDP/SSH でアクセスされる (ポート 443 経由) Bastion リソースのパブリック IP です。 新しいパブリック IP を作成するか、既存のものを使用します。 パブリック IP アドレスは、作成している Bastion リソースと同じリージョン内にある必要があります。
    * **パブリック IP アドレス名**:パブリック IP アドレス リソースの名前です。
    * **パブリック IP アドレスの SKU**: この設定は既定で **[標準]** に事前入力されます。 Azure Bastion では、標準パブリック IP SKU のみが使用およびサポートされます。
    * **割り当て**: この設定は既定で **[静的]** に事前入力されます。

1. 設定の指定が完了したら、 **[レビュー + 作成]** をクリックします。 これにより、値が検証されます。 検証をパスすると、作成プロセスを開始できます。
1. **[Bastion の作成]** ページで **[作成]** をクリックします。
1. デプロイが進行中であることを知らせるメッセージが表示されます。 リソースが作成されたときに、状態がこのページに表示されます。 Bastion リソースを作成してデプロイするには、約 5 分かかります。

## <a name="next-steps"></a>次のステップ

* 詳細については、[Bastion に関する FAQ](bastion-faq.md) に関するページを参照してください。

* Azure Bastion サブネットでネットワーク セキュリティ グループを使用する方法については、[NSG の使用](bastion-nsg.md)に関するページを参照してください。
