---
title: Azure API Management のページ コントロール | Microsoft Docs
description: Azure API Management の開発者ポータル テンプレートで使用できるページ コントロールについて説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: b826c986a0af26bb79fb0823e4e8626f0165d460
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86250024"
---
# <a name="azure-api-management-page-controls"></a>Azure API Management のページ コントロール
Azure API Management には、開発者ポータル テンプレート用に次のページ コントロールが用意されています。  
  
コントロールを使用するには、開発者ポータル テンプレート内の必要な位置に配置します。 [app-actions](#app-actions) コントロールなど、一部のコントロールには、次の例に示すようなパラメーターがあります。  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
パラメーターの値は、テンプレートのデータ モデルの一部として渡されます。 ほとんどのケースで、コントロールごとに用意されている例を貼り付けると正常に機能します。 パラメーター値の詳細については、コントロールを使用できる各テンプレートのデータ モデルのセクションを参照してください。  

テンプレートの操作方法の詳細については、[テンプレートを使用して API Management 開発者ポータルをカスタマイズする方法](./api-management-developer-portal-templates.md)に関するページを参照してください。  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>開発者ポータル テンプレートのページ コントロール  
  
-   [app-actions](#app-actions)  
-   [basic-signin](#basic-signin)  
-   [paging-control](#paging-control)  
-   [providers](#providers)  
-   [search-control](#search-control)  
-   [sign-up](#sign-up)  
-   [subscribe-button](#subscribe-button)  
-   [subscription-cancel](#subscription-cancel)  
  
##  <a name="app-actions"></a><a name="app-actions"></a> app-actions  
 `app-actions` コントロールは、開発者ポータルのユーザー プロファイル ページでアプリケーションを操作するためのユーザー インターフェイスを提供します。  
  
 ![app&#45;actions コントロール](./media/api-management-page-controls/APIM-app-actions-control.png "APIM app-actions コントロール")  
  
### <a name="usage"></a>使用法  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>パラメーター  
  
|パラメーター|説明|  
|---------------|-----------------|  
|appId|アプリケーションの ID。|  
  
### <a name="developer-portal-templates"></a>開発者ポータル テンプレート  
 `app-actions` コントロールは、以下の開発者ポータル テンプレートで使用できます。  
  
-   [アプリケーション](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a><a name="basic-signin"></a> basic-signin  
 `basic-signin` コントロールは、開発者ポータルのサインイン ページでユーザーのサインイン情報を収集するコントロールを提供します。  
  
 ![basic&#45;signin コントロール](./media/api-management-page-controls/APIM-basic-signin-control.png "APIM basic-signin コントロール")  
  
### <a name="usage"></a>使用法  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>パラメーター  
 [なし] :  
  
### <a name="developer-portal-templates"></a>開発者ポータル テンプレート  
 `basic-signin` コントロールは、以下の開発者ポータル テンプレートで使用できます。  
  
-   [サインインする](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a><a name="paging-control"></a> paging-control  
 `paging-control` は、項目の一覧を表示する開発者ポータル ページでページング機能を提供します。  
  
 ![paging コントロール](./media/api-management-page-controls/APIM-paging-control.png "APIM paging コントロール")  
  
### <a name="usage"></a>使用法  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>パラメーター  
 [なし] :  
  
### <a name="developer-portal-templates"></a>開発者ポータル テンプレート  
 `paging-control` コントロールは、以下の開発者ポータル テンプレートで使用できます。  
  
-   [API リスト](api-management-api-templates.md#APIList)  
  
-   [問題リスト](api-management-issue-templates.md#IssueList)  
  
-   [製品リスト](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a><a name="providers"></a> providers  
 `providers` コントロールは、開発者ポータルのサインイン ページで認証プロバイダーを選択するコントロールを提供します。  
  
 ![providers コントロール](./media/api-management-page-controls/APIM-providers-control.png "APIM providers コントロール")  
  
### <a name="usage"></a>使用法  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>パラメーター  
 [なし] :  
  
### <a name="developer-portal-templates"></a>開発者ポータル テンプレート  
 `providers` コントロールは、以下の開発者ポータル テンプレートで使用できます。  
  
-   [サインインする](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a><a name="search-control"></a> search-control  
 `search-control` は、項目の一覧を表示する開発者ポータル ページで検索機能を提供します。  
  
 ![search コントロール](./media/api-management-page-controls/APIM-search-control.png "APIM search コントロール")  
  
### <a name="usage"></a>使用法  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>パラメーター  
 [なし] :  
  
### <a name="developer-portal-templates"></a>開発者ポータル テンプレート  
 `search-control` コントロールは、以下の開発者ポータル テンプレートで使用できます。  
  
-   [API リスト](api-management-api-templates.md#APIList)  
  
-   [製品リスト](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a><a name="sign-up"></a> sign-up  
 `sign-up` コントロールは、開発者ポータルのサインアップ ページでユーザーのプロファイル情報を収集するコントロールを提供します。  
  
 ![sign&#45;up コントロール](./media/api-management-page-controls/APIM-sign-up-control.png "APIM sign-up コントロール")  
  
### <a name="usage"></a>使用法  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>パラメーター  
 [なし] :  
  
### <a name="developer-portal-templates"></a>開発者ポータル テンプレート  
 `sign-up` コントロールは、以下の開発者ポータル テンプレートで使用できます。  
  
-   [サインアップ](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a><a name="subscribe-button"></a> subscribe-button  
 `subscribe-button` は、ユーザーが成果物へのサブスクリプションを行うためのコントロールを提供します。  
  
 ![subscribe&#45;button コントロール](./media/api-management-page-controls/APIM-subscribe-button-control.png "APIM subscribe-button コントロール")  
  
### <a name="usage"></a>使用法  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>パラメーター  
 [なし] :  
  
### <a name="developer-portal-templates"></a>開発者ポータル テンプレート  
 `subscribe-button` コントロールは、以下の開発者ポータル テンプレートで使用できます。  
  
-   [Product](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a><a name="subscription-cancel"></a> subscription-cancel  
 `subscription-cancel` コントロールは、開発者ポータルのユーザー プロファイル ページで成果物へのサブスクリプションを取り消すコントロールを提供します。  
  
 ![subscription&#45;cancel コントロール](./media/api-management-page-controls/APIM-subscription-cancel-control.png "APIM subscription-cancel コントロール")  
  
### <a name="usage"></a>使用法  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>パラメーター  
  
|パラメーター|説明|  
|---------------|-----------------|  
|subscriptionId|キャンセルするサブスクリプションの ID。|  
|cancelUrl|サブスクリプション キャンセル URL。|  
  
### <a name="developer-portal-templates"></a>開発者ポータル テンプレート  
 `subscription-cancel` コントロールは、以下の開発者ポータル テンプレートで使用できます。  
  
-   [Product](api-management-product-templates.md#Product)

## <a name="next-steps"></a>次のステップ
テンプレートの操作方法の詳細については、[テンプレートを使用して API Management 開発者ポータルをカスタマイズする方法](api-management-developer-portal-templates.md)に関するページを参照してください。
