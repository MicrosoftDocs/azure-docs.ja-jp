---
title: "Azure API Management のページ コントロール | Microsoft Docs"
description: "Azure API Management の開発者ポータル テンプレートで使用できるページ コントロールについて説明します。"
services: api-management
documentationcenter: 
author: miaojiang
manager: erikre
editor: 
ms.assetid: 03e0ac8d-64ff-4e9a-b029-d7be14fb31e3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 77fd7b5b339a8ede8a297bec96f91f0a243cc18d
ms.openlocfilehash: 925aff209056f5a63b927dcb95ef6621967a0ffb

---
# <a name="azure-api-management-page-controls"></a>Azure API Management のページ コントロール
Azure API Management には、開発者ポータル テンプレート用に次のページ コントロールが用意されています。  
  
 コントロールを使用するには、開発者ポータル テンプレート内の必要な位置に配置します。 [app-actions](#app-actions) コントロールなど、一部のコントロールには、次の例に示すようなパラメーターがあります。  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
 パラメーターの値は、テンプレートのデータ モデルの一部として渡されます。 ほとんどのケースで、コントロールごとに用意されている例を貼り付けると正常に機能します。 パラメーター値の詳細については、コントロールを使用できる各テンプレートのデータ モデルのセクションを参照してください。  
  
 テンプレートの操作方法の詳細については、「[Azure API Management 開発者ポータルをテンプレートを使用してカスタマイズする方法](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)」を参照してください。  
  
## <a name="developer-portal-template-page-controls"></a>開発者ポータル テンプレートのページ コントロール  
  
-   [app-actions](#app-actions)  
  
-   [basic-signin](#basic-signin)  
  
-   [paging-control](#paging-control)  
  
-   [providers](#providers)  
  
-   [search-control](#search-control)  
  
-   [sign-up](#sign-up)  
  
-   [subscribe-button](#subscribe-button)  
  
-   [subscription-cancel](#subscription-cancel)  
  
##  <a name="a-nameapp-actionsa-app-actions"></a><a name="app-actions"></a> app-actions  
 `app-actions` コントロールは、開発者ポータルのユーザー プロファイル ページでアプリケーションを操作するためのユーザー インターフェイスを提供します。  
  
 ![app&#45;actions control](./media/api-management-page-controls/APIM-app-actions-control.png "APIM app-actions control")  
  
### <a name="usage"></a>使用法  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>parameters  
  
|パラメーター|Description|  
|---------------|-----------------|  
|appId|アプリケーションの ID。|  
  
### <a name="developer-portal-templates"></a>開発者ポータル テンプレート  
 `app-actions` コントロールは、次の開発者ポータル テンプレートで使用できます。  
  
-   [アプリケーション](api-management-user-profile-templates.md#Applications)  
  
##  <a name="a-namebasic-signina-basic-signin"></a><a name="basic-signin"></a> basic-signin  
 `basic-signin` コントロールは、開発者ポータルのサインイン ページでユーザーのサインイン情報を収集するコントロールを提供します。  
  
 ![basic&#45;signin control](./media/api-management-page-controls/APIM-basic-signin-control.png "APIM basic-signin control")  
  
### <a name="usage"></a>使用法  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>parameters  
 なし。  
  
### <a name="developer-portal-templates"></a>開発者ポータル テンプレート  
 `basic-signin` コントロールは、次の開発者ポータル テンプレートで使用できます。  
  
-   [サインイン](api-management-page-templates.md#SignIn)  
  
##  <a name="a-namepaging-controla-paging-control"></a><a name="paging-control"></a> paging-control  
 `paging-control` は、項目の一覧を表示する開発者ポータル ページでページング機能を提供します。  
  
 ![paging control](./media/api-management-page-controls/APIM-paging-control.png "APIM paging control")  
  
### <a name="usage"></a>使用法  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>parameters  
 なし。  
  
### <a name="developer-portal-templates"></a>開発者ポータル テンプレート  
 `paging-control` コントロールは、次の開発者ポータル テンプレートで使用できます。  
  
-   [API リスト](api-management-api-templates.md#APIList)  
  
-   [問題リスト](api-management-issue-templates.md#IssueList)  
  
-   [製品リスト](api-management-product-templates.md#ProductList)  
  
##  <a name="a-nameprovidersa-providers"></a><a name="providers"></a> providers  
 `providers` コントロールは、開発者ポータルのサインイン ページで認証プロバイダーを選択するコントロールを提供します。  
  
 ![providers control](./media/api-management-page-controls/APIM-providers-control.png "APIM providers control")  
  
### <a name="usage"></a>使用法  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>parameters  
 なし。  
  
### <a name="developer-portal-templates"></a>開発者ポータル テンプレート  
 `providers` コントロールは、次の開発者ポータル テンプレートで使用できます。  
  
-   [サインイン](api-management-page-templates.md#SignIn)  
  
##  <a name="a-namesearch-controla-search-control"></a><a name="search-control"></a> search-control  
 `search-control` は、項目の一覧を表示する開発者ポータル ページで検索機能を提供します。  
  
 ![search control](./media/api-management-page-controls/APIM-search-control.png "APIM search control")  
  
### <a name="usage"></a>使用法  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>parameters  
 なし。  
  
### <a name="developer-portal-templates"></a>開発者ポータル テンプレート  
 `search-control` コントロールは、次の開発者ポータル テンプレートで使用できます。  
  
-   [API リスト](api-management-api-templates.md#APIList)  
  
-   [製品リスト](api-management-product-templates.md#ProductList)  
  
##  <a name="a-namesign-upa-sign-up"></a><a name="sign-up"></a> sign-up  
 `sign-up` コントロールは、開発者ポータルのサインアップ ページでユーザーのプロファイル情報を収集するコントロールを提供します。  
  
 ![sign&#45;up control](./media/api-management-page-controls/APIM-sign-up-control.png "APIM sign-up control")  
  
### <a name="usage"></a>使用法  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>parameters  
 なし。  
  
### <a name="developer-portal-templates"></a>開発者ポータル テンプレート  
 `sign-up` コントロールは、次の開発者ポータル テンプレートで使用できます。  
  
-   [サインアップ](api-management-page-templates.md#SignUp)  
  
##  <a name="a-namesubscribe-buttona-subscribe-button"></a><a name="subscribe-button"></a> subscribe-button  
 `subscribe-button` は、ユーザーが成果物へのサブスクリプションを行うためのコントロールを提供します。  
  
 ![subscribe&#45;button control](./media/api-management-page-controls/APIM-subscribe-button-control.png "APIM subscribe-button control")  
  
### <a name="usage"></a>使用法  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>parameters  
 なし。  
  
### <a name="developer-portal-templates"></a>開発者ポータル テンプレート  
 `subscribe-button` コントロールは、次の開発者ポータル テンプレートで使用できます。  
  
-   [成果物](api-management-product-templates.md#Product)  
  
##  <a name="a-namesubscription-cancela-subscription-cancel"></a><a name="subscription-cancel"></a> subscription-cancel  
 `subscription-cancel` コントロールは、開発者ポータルのユーザー プロファイル ページで成果物へのサブスクリプションをキャンセルするコントロールを提供します。  
  
 ![subscription&#45;cancel control](./media/api-management-page-controls/APIM-subscription-cancel-control.png "APIM subscription-cancel control")  
  
### <a name="usage"></a>使用法  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>parameters  
  
|パラメーター|Description|  
|---------------|-----------------|  
|subscriptionId|キャンセルするサブスクリプションの ID。|  
|cancelUrl|サブスクリプション キャンセル URL。|  
  
### <a name="developer-portal-templates"></a>開発者ポータル テンプレート  
 `subscription-cancel` コントロールは、次の開発者ポータル テンプレートで使用できます。  
  
-   [成果物](api-management-product-templates.md#Product)

## <a name="next-steps"></a>次のステップ
テンプレートの操作方法の詳細については、「[Azure API Management 開発者ポータルをテンプレートを使用してカスタマイズする方法](api-management-developer-portal-templates.md)」を参照してください。


<!--HONumber=Jan17_HO2-->


