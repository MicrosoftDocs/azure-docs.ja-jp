---
title: 'チュートリアル:Azure Bastion ホストを作成する:Windows VM: ポータル'
description: この記事では、Azure Bastion ホストを作成して Windows VM に接続する方法について学習します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: tutorial
ms.date: 02/12/2021
ms.author: cherylmc
ms.openlocfilehash: 60b49e5b6e103a85d79cf8495f2743b22e434c96
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586798"
---
# <a name="tutorial-configure-bastion-and-connect-to-a-windows-vm-through-a-browser"></a>チュートリアル:Bastion を構成し、ブラウザーを介して Windows VM に接続する

このチュートリアルでは、Azure Bastion と Azure portal を使用して、ブラウザーを介して仮想マシンに接続する方法について説明します。 Azure portal で、Bastion を仮想ネットワークにデプロイします。 Bastion をデプロイしたら、Azure portal を使用して、そのプライベート IP アドレスを介して VM に接続します。 VM には、パブリック IP アドレスや特別なソフトウェアは必要ありません。 サービスがプロビジョニングされると、同じ仮想ネットワーク内のすべての仮想マシンで RDP/SSH エクスペリエンスを使用できるようになります。 Azure Bastion の詳細については、「[Azure Bastion とは](bastion-overview.md)」を参照してください。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * VNet 用の Bastion ホストを作成する
> * Windows 仮想マシンに接続する

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

* 仮想ネットワーク。
* 仮想ネットワーク内の Windows 仮想マシン。
* 次の必須ロール:
  * 仮想マシンに対する閲覧者ロール。
  * 仮想マシンのプライベート IP を使用する NIC に対する閲覧者ロール。
  * Azure Bastion リソースに対する閲覧者ロール。

* ポート: Windows VM に接続するには、お使いの Windows VM で次のポートを開いておく必要があります。
  * 受信ポート:RDP (3389)

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Bastion ホストの作成

このセクションでは、VNet で Bastion オブジェクトを作成する方法について説明します。 これは、VNet 内の VM に対するセキュリティで保護された接続を作成するために必要です。

1. **[ホーム]** ページで、 **[+ リソースの作成]** を選択します。
1. **[新規]** ページの [検索] ボックスに「**Bastion**」と入力し、**Enter** キーを押して検索結果を取得します。 「**Bastion**」の結果で、発行元が Microsoft であることを確認します。
1. **［作成］** を選択します
1. **[Bastion の作成]** ページで、新しい Bastion リソースを構成します。

   :::image type="content" source="./media/tutorial-create-host-portal/bastion-basics.png" alt-text="Bastion ホストの作成" lightbox="./media/tutorial-create-host-portal/bastion-basics.png":::

    * **サブスクリプション**:新しい Bastion リソースの作成に使用する Azure サブスクリプションです。
    * **リソース グループ**:新しい Bastion リソースが作成される Azure リソース グループです。 既存のリソース グループがない場合は、新しいものを作成できます。
    * **名前**: 新しい Bastion リソースの名前です。
    * **[リージョン]** :リソースが作成される Azure パブリック リージョンです。
    * **仮想ネットワーク**:Bastion リソースが作成される仮想ネットワークです。 このプロセス中にポータルで新しい仮想ネットワークを作成したり、既存の仮想ネットワークを使用したりすることができます。 既存の仮想ネットワークを使用している場合、Bastion サブネットの要件を反映するために、既存の仮想ネットワークに十分な空きアドレス空間があることを確認してくます。 ドロップダウンに仮想ネットワークが表示されない場合は、適切なリソースグループを選択していることを確認してください。
    * **サブネット**:仮想ネットワークを作成または選択すると、[サブネット] フィールドが表示されます。 新しい Bastion ホストがデプロイされる仮想ネットワーク内のサブネットです。 このサブネットは Bastion ホスト専用となります。 **[サブネット構成の管理]** を選択して Azure Bastion のサブネットを作成します。 **[+ サブネット]** を選択し、次のガイドラインに従ってサブネットを作成します。

         * サブネットの名前は「**AzureBastionSubnet**」とする必要があります。
         * サブネットは、少なくとも /27 以上である必要があります。

      追加のフィールドに入力する必要はありません。 **[OK]** を選択し、ページの上部にある **[Bastion の作成]** を選択して、Bastion の構成ページに戻ります。
    * **[パブリック IP アドレス]** : RDP/SSH でアクセスされる (ポート 443 経由) Bastion リソースのパブリック IP です。 新しいパブリック IP を作成します。 パブリック IP アドレスは、作成している Bastion リソースと同じリージョン内にある必要があります。 この IP アドレスは、接続先の VM とは何の関係もありません。 Bastion ホスト リソースのパブリック IP です。
    * **パブリック IP アドレス名**:パブリック IP アドレス リソースの名前です。 このチュートリアルでは、既定値のままにしておいてかまいません。
    * **パブリック IP アドレスの SKU**: この設定は既定で **[標準]** に事前入力されます。 Azure Bastion では、標準パブリック IP SKU のみが使用およびサポートされます。
    * **割り当て**: この設定は既定で **[静的]** に事前入力されます。

1. 設定の指定が完了したら、 **[確認および作成]** を選択します。 これにより、値が検証されます。 検証に合格したら、Bastion リソースを作成できます。
1. **［作成］** を選択します
1. デプロイが進行中であることを知らせるメッセージが表示されます。 リソースが作成されたときに、状態がこのページに表示されます。 Bastion リソースを作成してデプロイするには、約 5 分かかります。

## <a name="connect-to-a-vm"></a>VM への接続

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、次の手順を使用してリソースを削除します。

1. ポータルの上部にある **検索** ボックスにリソース グループの名前を入力します。 検索結果にそのリソース グループが表示されたら、選択します。
1. **[リソース グループの削除]** を選択します。
1. **[TYPE THE RESOURCE GROUP NAME]\(リソース グループ名を入力してください\)** にそのリソース グループの名前を入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Bastion ホストを作成し、それを仮想ネットワークに関連付けて、Windows VM に接続しました。 Azure Bastion サブネットでネットワーク セキュリティ グループを使用することを選択できます。 これを行うには、次を参照してください。

> [!div class="nextstepaction"]
> [NSG を使用する](bastion-nsg.md)
