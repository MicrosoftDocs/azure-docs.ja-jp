---
title: Azure API Management 開発者ポータルのテンプレート | Microsoft Docs
description: Azure API Management のテンプレート セットを使用して開発者ポータル ページの内容をカスタマイズする方法について説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 5189f3d8-2a4c-4dc8-ab19-11c7df0114d4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 9bbb7ef46fbde881c8968128870d24a03b046c23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "73176794"
---
# <a name="developer-portal-templates"></a>開発者ポータル テンプレート

Azure API Management には、開発者ポータルの各ページの内容を、内容を構成するテンプレート セットを使用してカスタマイズする機能があります。 [DotLiquid](http://dotliquidmarkup.org/) 構文、好みのエディター ([DotLiquid for Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)など)、および用意されているローカライズされた[文字列リソース](api-management-template-resources.md#strings)、[グリフ リソース](api-management-template-resources.md#glyphs)、および[ページ コントロール](api-management-page-controls.md)のセットをテンプレートで使用して、表示されるページの内容を自由に構成できます。  
  
テンプレートの操作方法の詳細については、[テンプレートを使用して API Management 開発者ポータルをカスタマイズする方法](api-management-developer-portal-templates.md)に関するページを参照してください。  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

##  <a name="developer-portal-templates"></a><a name="DeveloperPortalTemplates"></a> 開発者ポータル テンプレート  
  
-   [API](api-management-api-templates.md)  
    -   [API リスト](api-management-api-templates.md#APIList)  
    -   [Operation](api-management-api-templates.md#Product)  
    -   [コード サンプル](api-management-api-templates.md#CodeSamples)  
        -   [Curl](api-management-api-templates.md#Curl)  
        -   [C#](api-management-api-templates.md#CSharp)  
        -   [Java](api-management-api-templates.md#Stub)  
        -   [JavaScript](api-management-api-templates.md#JavaScript)  
        -   [Objective C](api-management-api-templates.md#ObjectiveC)  
        -   [PHP](api-management-api-templates.md#PHP)  
        -   [Python](api-management-api-templates.md#Python)  
        -   [Ruby](api-management-api-templates.md#Ruby)  
-   [成果物](api-management-product-templates.md)  
    -   [製品リスト](api-management-product-templates.md#ProductList)  
    -   [Product](api-management-product-templates.md#Product)  
-   [アプリケーション](api-management-application-templates.md)  
    -   [アプリケーション リスト](api-management-application-templates.md#ProductList)  
    -   [アプリケーション](api-management-application-templates.md#Application)  
-   [問題](api-management-issue-templates.md)  
    -   [問題リスト](api-management-issue-templates.md#IssueList)  
-   [ユーザー プロファイル](api-management-user-profile-templates.md)  
    -   [プロファイル](api-management-user-profile-templates.md#Profile)  
    -   [サブスクリプション](api-management-user-profile-templates.md#Subscriptions)  
    -   [アプリケーション](api-management-user-profile-templates.md#Applications)  
    -   [アカウント情報の更新](api-management-user-profile-templates.md#UpdateAccountInfo)  
-   [ページ](api-management-page-templates.md)  
    -   [サインイン](api-management-page-templates.md#SignIn)  
    -   [サインアップ](api-management-page-templates.md#SignUp)  
    -   [ページが見つかりません](api-management-page-templates.md#PageNotFound)

## <a name="next-steps"></a>次のステップ  

+ [テンプレート リファレンス](api-management-developer-portal-templates-reference.md)  
+ [データ モデル リファレンス](api-management-template-data-model-reference.md)  
+ [ページ コントロール](api-management-page-controls.md)  
+ [Template resources](api-management-template-resources.md)