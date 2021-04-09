---
title: サービス エンドポイント ポリシーの作成と関連付け - Azure portal
titlesuffix: Azure Virtual Network
description: この記事では、Azure portal を使用してサービス エンドポイント ポリシーを設定し、関連付ける方法について説明します。
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: bdf0e87c92a55d0dbb5bbe34334a6de4580cb350
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004961"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Azure portal を使用してサービス エンドポイント ポリシーを作成、変更、または削除する

サービス エンドポイント ポリシーを使用すると、サービス エンドポイントを経由する特定の Azure リソースへの仮想ネットワーク トラフィックをフィルター処理できます。 サービス エンドポイント ポリシーをよく理解していない場合は、[サービス エンドポイント ポリシーの概要](virtual-network-service-endpoint-policies-overview.md)に関するページを参照してください。

 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * サービス エンドポイント ポリシーを作成する
> * サービス エンドポイント ポリシーの定義を作成する
> * サブネットを含む仮想ネットワークを作成する
> * サービス エンドポイント ポリシーをサブネットに関連付ける

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン 

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="create-a-service-endpoint-policy"></a>サービス エンドポイント ポリシーを作成する

1. Azure Portal の左上隅にある **[+ リソースの作成]** を選択します。
2. 検索ウィンドウに「サービス エンドポイント ポリシー」と入力し、 **[サービス エンドポイント ポリシー]** 、 **[作成]** の順に選択します。

![サービス エンドポイント ポリシーを作成する](./media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

3. **[基本]** に次の情報を入力するか、選択します。 

   - サブスクリプション:ポリシーのサブスクリプションを選択
   - リソース グループ: **[新規作成]** を選択し、「*myResourceGroup*」と入力します
   - 名前: myEndpointPolicy
   - 場所:米国中部
 
   ![サービス エンドポイント ポリシーの基本を作成する](./media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

4. **[リソース]** の下にある **[+ 追加]** を選択し **[リソースの追加]** ウィンドウで次の情報を入力または選択します。

   - サービス:サービス エンドポイント ポリシーで使用できるのは **Microsoft. Storage** のみです
   - スコープ: **[単一のアカウント]** 、 **[サブスクリプションのすべてのアカウント]** 、 **[リソース グループのすべてのアカウント]** を選択します。
   - サブスクリプション:ストレージ アカウントのサブスクリプションを選択します。 別のサブスクリプションのポリシーとストレージ アカウントを指定できます。
   - リソース グループ:リソース グループを選択します。 スコープの設定が [サブスクリプションのすべてのアカウント] または [リソース グループのすべてのアカウント] の場合は必須です。  
   - リソース:選択したサブスクリプションまたはリソース グループの下にある Azure Storage リソースを選択します
   - 下部にある **[追加]** をクリックして、リソースの追加を完了します。

   ![サービス エンドポイント ポリシー - リソース](./media/virtual-network-service-endpoint-policies-portal/create-sep-add-resource.png)

   - 必要に応じて上記の手順を繰り返して、さらにリソースを追加します

5. 省略可能: **[タグ]** に次の情報を入力するか、選択します。
   
   - キー:ポリシーのキーを選択します。 例:Dept     
   - 値:キーの値ペアを入力します。 例:Finance

6. **[確認および作成]** を選択します。 情報を確認して **[作成]** をクリックします。 さらに編集するには、 **[前へ]** をクリックします。 

   ![サービス エンドポイント ポリシーの最終的な検証を作成する](./media/virtual-network-service-endpoint-policies-portal/create-sep-review-create.png)
  
## <a name="view-endpoint-policies"></a>エンドポイント ポリシーを表示する 

1. ポータルの *[すべてのサービス]* に「*サービス エンドポイント ポリシー*」と入力します。 **サービス エンドポイント ポリシー** を選択します。
2. **[サブスクリプション]** で、次の図のようにサブスクリプションとリソース グループを選択します

   ![ポリシーを表示する](./media/virtual-network-service-endpoint-policies-portal/sep-view.png)
       
3. ポリシーを選択し、 **[ポリシーの定義]** をクリックしてポリシーの定義を表示または追加します。

   ![ポリシーの定義を表示する](./media/virtual-network-service-endpoint-policies-portal/sep-policy-definition.png)

4. **[関連付けられているサブネット]** を選択してポリシーが関連付けられているサブネットを表示します。 サブネットが関連付けられていない場合は、次のステップの指示に従います。

   ![関連付けられているサブネット](./media/virtual-network-service-endpoint-policies-portal/sep-associated-subnets.png)
 
5. ポリシーをサブネットに関連付ける

>[!WARNING] 
> 指定されたサブネットにポリシーを関連付ける前に、サブネットからアクセスするすべてのリソースがポリシーの定義に追加されていることを確認します。 ポリシーが関連付けられると、*許可された* リソースへのアクセスのみがサービス エンドポイントで許可されます。 
>
> また、サービス エンドポイント ポリシーに関連付けられているサブネットに、マネージド Azure サービスが存在しないことを確認します。

- ポリシーをサブネットに関連付ける前に、仮想ネットワークとサブネットを作成する必要があります。 詳細については、[Virtual Network の作成](./quick-create-portal.md) に関するアーティクルをご覧ください。

- 仮想ネットワークとサブネットを設定したら、Azure Storage の Virtual Network サービス エンドポイントを構成する必要があります。 Virtual Network ブレードで **サービス エンドポイント** を選択し、次のウィンドウで **Microsoft.Storage** を選択し **サブネット** で目的の VNet またはサブネットを選択します。

- 次に示すように、サブネットのサービス エンドポイントを構成する前に、前のウィンドウのドロップダウンからサービス エンドポイント ポリシーを選択することができます。

    ![サービス エンドポイントの作成中にサブネットを関連付ける](./media/virtual-network-service-endpoint-policies-portal/vnet-config-service-endpoint-add-sep.png)

- または、サービス エンドポイントを構成した後でサービスエンド ポイント ポリシーを関連付ける場合は、次に示すように、 **[関連付けられたサブネット]** ウィンドウに移動してから、[サービス エンドポイント ポリシー] ブレード内からサブネットを関連付けることができます。

    ![SEP を使用したサブネットの関連付け](./media/virtual-network-service-endpoint-policies-portal/sep-edit-subnet-association.png)

>[!WARNING] 
>すべてのリージョンの Azure Storage リソースへのアクセスは、このサブネットのサービス エンドポイント ポリシーに従って制限されます。

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、サービス エンドポイント ポリシーを作成し、それをサブネットに関連付けました。 サービス エンドポイント ポリシーの詳細については、[サービス エンドポイント ポリシーの概要](virtual-network-service-endpoint-policies-overview.md)に関するページを参照してください。
