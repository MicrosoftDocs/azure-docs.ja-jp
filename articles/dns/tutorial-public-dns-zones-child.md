---
title: チュートリアル:Azure 子 DNS ゾーンの作成
titleSuffix: Azure DNS
description: Azure portal で子 DNS ゾーンを作成する方法に関するチュートリアル。
author: jonbeck
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: tutorial
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 7/16/2020
ms.author: jonbeck
ms.openlocfilehash: 1e2eddd821bb7a9d2050913efef3d73b406e32f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101733212"
---
# <a name="tutorial-creating-a-new-child-dns-zone"></a>チュートリアル:新しい子 DNS ゾーンの作成

このチュートリアルでは、以下の内容を学習します。 

> [!div class="checklist"]
> * Azure portal にサインインする。
> * 新しい DNS ゾーンを使用して子 DNS ゾーンを作成する。
> * 親 DNS ゾーンを使用して子 DNS ゾーンを作成する。
> * 新しい子 DNS ゾーンに対する NS の委任を検証する。



## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。  アカウントを持っていない場合は、[無料でアカウントを作成する](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)ことができます。
* 既存の親 Azure DNS ゾーン。  

このチュートリアルでは、親ゾーンとして contoso.com を使用し、子ドメイン名として subdomain.contoso.com を使用します。  *contoso.com* を実際の親ドメイン名に、*subdomain* を実際の子ドメインに置き換えてください。  親 DNS ゾーンを作成していない場合は、[Azure portal を使用して DNS ゾーンを作成する](./dns-getstarted-portal.md#create-a-dns-zone)ための手順を参照してください。 


## <a name="sign-in-to-azure-portal"></a>Azure Portal にサインインする

Azure アカウントで [Azure Portal](https://portal.azure.com/) にサインインします。
Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。

子 DNS ゾーンを作成するには、次の 2 つの方法があります。
1.  [DNS ゾーンの作成] ポータル ページから。
1.  親 DNS ゾーンの構成ページから。


## <a name="create-child-dns-zone-via-create-dns-zone"></a>DNS ゾーンの作成を使用して子 DNS ゾーンを作成する

この手順では、**subdomain.contoso.com** という名前の新しい子 DNS ゾーンを作成し、それを既存の親 DNS ゾーン **contoso.com** に委任します。 **[DNS ゾーンの作成]** ページのタブを使用して、DNS ゾーンを作成します。
1.  Azure portal メニュー上または **[ホーム]** ページから **[リソースの作成]** を選択します。 **[新規作成]** ウィンドウが表示されます。
1.  **[ネットワーク]** を選択し、 **[DNS ゾーン]** を選択して、 **[追加]** ボタンを選択します。

1.  **[基本]** タブで、次の値を入力または選択します。
    * **サブスクリプション**:ゾーンの作成先となるサブスクリプションを選択します。
    * **[リソース グループ]** :既存のリソース グループを入力します。または、 **[新規作成]** を選択し、「*MyResourceGroup*」と入力して、 **[OK]** を選択することで、新しく作成することもできます。 Azure サブスクリプション内で一意となるリソース グループ名を使用してください。
    * このチェック ボックスをオンにします: **[このゾーンは、既に Azure DNS でホストされている既存のゾーンの子です]**
    * **[Parent zone subscription]\(親ゾーンのサブスクリプション\)** : このドロップダウンから、親 DNS ゾーン *contoso.com* が作成されたサブスクリプション名を検索および選択します。
    * **[Parent zone]\(親ゾーン\)** : 検索バーに「*contoso.com*」と入力し、ドロップダウン リストでそれを読み込みます。 読み込んだら、ドロップダウン リストから *contoso.com* を選択します。
    * **[名前]:** このチュートリアルの例では、「*subdomain*」と入力します。 上のステップで親ゾーンを選択すると、親 DNS ゾーンの名前 *contoso.com* がサフィックスとして自動的に追加されることに注意してください。

1. **確認と作成** をクリックします。
1. **[確認および作成]** タブで概要を確認し、検証エラーを修正してから、 **[作成]** を選択します。
ゾーンの作成には数分かかることがあります。

 
    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-inline.png" alt-text="[DNS ゾーンの作成] ページのスクリーンショット。" lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-expanded.png":::

## <a name="create-child-dns-zone-via-parent-dns-zone-overview-page"></a>親 DNS ゾーンの概要ページを使用して子 DNS ゾーンを作成する
また、親ゾーンの概要ページの **[Child Zone]\(子ゾーン\)** ボタンを使用することで、新しい子 DNS ゾーンを作成して親 DNS ゾーンに委任することもできます。 このボタンを使用すると、子ゾーンに対して親のパラメーターが自動的に事前設定されます。 

1.  Azure portal の **[すべてのリソース]** で、**MyResourceGroup** リソース グループの *contoso.com* DNS ゾーンを開きます。 **[名前でフィルター]** ボックスに「*contoso.com*」と入力すると簡単に見つけることができます。
1.  DNS ゾーンの概要ページで、 **[+Child Zone]\(+ 子ゾーン\)** ボタンを選択します。

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-inline.png" alt-text="[Child Zone]\(子ゾーン\) ボタンのスクリーンショット。" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-expanded.png":::

1.  [DNS ゾーンの作成] ページが開きます。 [Child zone]\(子ゾーン\) オプションは既にオンになっており、親ゾーンのサブスクリプションと親ゾーンがこのページに既に設定されています。
1.  このチュートリアルの例では、名前として「*child*」と入力します。 親 DNS ゾーンの名前 contoso.com が、プレフィックスとして名前に自動的に追加されることに注意してください。
1.  **タグ**、**次へ:確認と作成** をクリックします。
1.  **[確認および作成]** タブで概要を確認し、検証エラーを修正してから、 **[作成]** を選択します。

    :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-inline.png" alt-text="選択された子ゾーンのスクリーンショット" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-dns-zone-child-expanded.png":::

## <a name="verify-child-dns-zone"></a>子 DNS ゾーンを検証する
これで、新しい子 DNS ゾーン *subdomain.contoso.com* が作成されました。 委任が正しく行われたことを確認するには、以下で説明するように、子ゾーンのネーム サーバー (NS) レコードが親ゾーンにあることを確認します。  

**子 DNS ゾーンのネーム サーバーを取得する:**

1.  Azure portal の **[すべてのリソース]** で、**MyResourceGroup** リソース グループの *subdomain.contoso.com* DNS ゾーンを開きます。 **[名前でフィルター]** ボックスに「*subdomain.contoso.com*」と入力すると簡単に見つけることができます。
1.  DNS ゾーンの概要ページでネーム サーバーを取得します。 この例では、ゾーン contoso.net に、ネーム サーバー *ns1-08.azure-dns.com、ns2-08.azure-dns.net、ns3-08.azure-dns.org、* *ns4-08.azure-dns.info* が割り当てられています。

      :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-inline.png" alt-text="子ゾーンのネーム サーバーのスクリーンショット" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-expanded.png":::
**親 DNS ゾーンで NS レコードを確認する:**

このステップでは、親 DNS ゾーン *contoso.com* に移動し、子ゾーンのネーム サーバーに対する NS レコード セット エントリが作成されていることを確認します。

1. Azure portal の **[すべてのリソース]** で、**MyResourceGroup** リソース グループの contoso.com DNS ゾーンを開きます。 **[名前でフィルター]** ボックスに「contoso.com」と入力すると簡単に見つけることができます。
1.  *contoso.com* DNS ゾーンの概要ページで、レコード セットを確認します。
1.  種類が NS で名前が subdomain のレコード セットが、親 DNS ゾーンに既に作成されていることがわかります。 このレコード セットの値を確認します。上のステップで子 DNS ゾーンから取得したネーム サーバーのリストに似ています。

     :::image type="content" source="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-inline.png" alt-text="子ゾーンのネーム サーバーの検証のスクリーンショット" border="true" lightbox="./media/dns-delegate-domain-azure-dns/create-child-zone-ns-validate-expanded.png":::
## <a name="clean-up-resources"></a>リソースをクリーンアップする
このチュートリアルで作成したリソースが必要なくなったら、**MyResourceGroup** リソース グループを削除することで削除できます。 **MyResourceGroup** リソース グループを開き、**[リソース グループの削除]** を選択します。



## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure DNS Private Zones のシナリオ](private-dns-scenarios.md)