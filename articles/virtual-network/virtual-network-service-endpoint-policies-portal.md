---
title: サービス エンドポイント ポリシーの作成と関連付け - Azure portal
titlesuffix: Azure Virtual Network
description: この記事では、Azure portal を使用してサービス エンドポイント ポリシーを設定し、関連付ける方法について説明します。
services: virtual-network
documentationcenter: virtual-network
author: kumudd
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 09/18/2018
ms.author: kumud
ms.openlocfilehash: b1d2d04e74828323166810d93c52a60671bf71e8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64710919"
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
2. 検索ウィンドウに「サービス エンドポイント ポリシー」と入力し、 **[Service endpoint policy (Preview)]\(サービス エンドポイント ポリシー (プレビュー)\)** 、 **[作成]** の順に選択します。
3. **[基本]** に次の情報を入力するか、選択します。 

   - サブスクリプション:ポリシーのサブスクリプションを選択します。    
   - リソース グループ: **[新規作成]** を選択し、「*myResourceGroup*と入力します。     
   - 名前: myEndpointPolicy
   - 場所:米国中西部     
 
   ![サービス エンドポイント ポリシーの基本を作成する](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-startpane.PNG)
   
4. **[ポリシー定義]** に次の情報を入力するか、選択します。

   - **[+ リソースの追加]** をクリックし、次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、 **[追加]** をクリックします。  
   - スコープ: **[単一のアカウント]** または **[サブスクリプションのすべてのアカウント]** または **[リソース グループのすべてのアカウント]** を選択します。    
   - サブスクリプション:ストレージ アカウントのサブスクリプションを選択します。 別のサブスクリプションのポリシーとストレージ アカウントを指定できます。   
   - リソース グループ:リソース グループを選択します。 スコープの設定が [サブスクリプションのすべてのアカウント] または [リソース グループのすべてのアカウント] の場合は必須です。  
   - リソース: mystorageaccountportal    
   - さらにリソースを追加するには、 **[+ リソースを追加する]** をクリックします。
   
   ![サービス エンドポイント ポリシーの定義を作成する](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-policydefinitionspane.PNG)
   
5. 省略可能: **[タグ]** に次の情報を入力するか、選択します。
   
   - キー:ポリシーのキーを選択します。 例:Dept     
   - 値:キーの値ペアを入力します。 例:Finance

6. **[確認および作成]** を選択します。 情報を確認して **[作成]** をクリックします。 さらに編集するには、 **[前へ]** をクリックします。 

   ![サービス エンドポイント ポリシーの最終的な検証を作成する](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-finalcreatereview.PNG)
  
 
## <a name="view-endpoint-policies"></a>エンドポイント ポリシーを表示する 

1. ポータルの *[すべてのサービス]* に「*サービス エンドポイント ポリシー*」と入力します。 **[Service Endpoint Policies(Preview)]\(サービス エンドポイント ポリシー (プレビュー)\)** を選択します。
2. **[サブスクリプション]** で、次の図のようにサブスクリプションとリソース グループを選択します

   ![ポリシーを表示する](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-viewpolicies.PNG)
       
3. ポリシーを選択し、 **[ポリシーの定義]** をクリックしてポリシーの定義を表示または追加します。

   ![ポリシーの定義を表示する](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-viewpolicy-adddefinitions.PNG)

4. **[関連付けられているサブネット]** を選択してポリシーが関連付けられているサブネットを表示します。 ポリシーをサブネットに関連付けるには、[Navigate to virtual network in the same region]\(同じリージョン内の仮想ネットワークに移動する\) をクリックします。

   ![関連付けられているサブネットを表示する](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-view-associatedsubnets.PNG)
 
## <a name="associate-a-policy-to-a-subnet"></a>ポリシーをサブネットに関連付ける

>[!WARNING] 
> 必ず、選択されているサービスについて、サブネットからアクセスされるすべてのリソースをポリシーに追加してから、ポリシーを関連付けます。 ポリシーが関連付けられると、サービスのエンドポイント リージョンについて、ポリシーに登録されているリソースへのアクセスのみが許可されます。 

ポリシーをサブネットに関連付けるには、仮想ネットワークとサブネットを作成してから、ポリシーをサブネットに関連付ける必要があります。

1. Azure Portal の左上隅にある **[+ リソースの作成]** を選択します。
2. **[ネットワーク]** を選択してから、 **[仮想ネットワーク]** を選択します。
3. **[仮想ネットワークの作成]** で、次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、 **[作成]** を選択します。
   - 名前: myVirtualNetwork      
   - アドレス空間:10.0.0.0/16      
   - サブスクリプション:サブスクリプションを選択します。 ポリシーは VNet と同じサブスクリプションにする必要があります     
   - リソース グループ: **[既存のものを使用]** 、 *[myResourceGroup]* の順に選択します。     
   - 場所:米国中西部     
   - サブネット名: プライベート     
   - アドレス範囲:10.0.0.0/24
     
4. ポータルの上部にある **[Search resources, services, and docs]\(リソース、サービス、ドキュメントの検索\)** ボックスで、「*myVirtualNetwork*」の入力を開始します。 検索結果に **[myVirtualNetwork]** が表示されたら、それを選択します。
5. **[設定]** で **[サブネット]** を選択し、 **[プライベート]** を選択します。
6. 次の図のように、 **[サービス エンドポイント]** 、 **[Microsoft.Storage]** 、 **[サービス エンドポイント ポリシー]** 、 **[myEndpointPolicy]** 、 **[保存]** の順に選択します。

   ![ポリシーを関連付ける](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-associatepolicies.PNG)

>[!WARNING] 
>他のリージョン内のサービス リソースへのアクセスは、ネットワーク セキュリティ グループ (NSG) に基づいてこのサブネットから許可されます。 エンドポイントのリージョンのみにアクセスを制限するには、エンドポイント リージョン内のサービス トラフィックのみに NSG を制限します。 リージョンごとのサービス タグを使用して NSG を作成する方法については、[NSG Azure サービス タグ](manage-network-security-group.md?toc=%2fcreate-a-security-rule%2f.json)に関するページを参照してください。

次の例の NSG は、優先度の低いルールとして "すべて拒否" ルールを使用して、WestCentralUS および WestUS2 内の Azure Storage リソースのみへのアクセスに制限されています。

![すべて拒否 NSG](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-nsg-rules.PNG)


## <a name="next-steps"></a>次の手順
このチュートリアルでは、サービス エンドポイント ポリシーを作成し、それをサブネットに関連付けました。 サービス エンドポイント ポリシーの詳細については、[サービス エンドポイント ポリシーの概要](virtual-network-service-endpoint-policies-overview.md)に関するページを参照してください。

