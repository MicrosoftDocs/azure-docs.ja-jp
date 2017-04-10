---
title: "Azure Active Directory の AppSource 認定を取得する方法 | Microsoft Docs"
description: "Azure Active Directory の AppSource 認定を取得する方法について説明します。"
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/28/2017
ms.author: skwan;bryanla
translationtype: Human Translation
ms.sourcegitcommit: e1299c1f7f8a31f7034fc0736fcd9d66153a9758
ms.openlocfilehash: 3290a375963bc3e625cbdb05b5f9686e8cfb34f6
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-get-appsource-certified-for-azure-active-directory-ad"></a>Azure Active Directory (AD) の AppSource 認定を取得する方法
Azure AD の AppSource 認定を取得するには、アプリケーションで OpenID Connect または OAuth 2.0 プロトコルを使用して、Azure AD にマルチテナント サインイン パターンを実装する必要があります。  

Azure AD サインインまたはマルチテナント アプリケーションの開発に慣れていない場合は、次の手順に従ってください。

1. まず、[「Azure AD の認証シナリオ」のブラウザー対 Web App アプリケーションのシナリオ][AAD-Auth-Scenarios-Browser-To-WebApp]を読みます。  
2. 次に、Azure AD の [Web アプリケーション クイックスタート ガイド][AAD-QuickStart-Web-Apps]を確認します。これらのガイドには、サインインの実装方法の説明とコード サンプルが記載されています。 
   
   > [!TIP]
   > Azure Active Directory をほんの数分で稼働するために役立つ、新しい [開発者ポータル](https://identity.microsoft.com/Docs/Web) のプレビュー版をお試しください。  開発者ポータルでは、アプリを登録して、コードに Azure AD を統合するプロセスを説明してあります。  完了すると、テナント内のユーザーを認証できる単純なアプリケーションと、トークンを受け取って検証を実行できるバックエンドを手に入れることになります。
   > 
   > 
3. Azure AD にマルチテナント サインインを実装する方法の詳細について、「[マルチテナント アプリケーション パターンを使用してすべての Azure Active Directory (AD) ユーザーがサインインできるようにする方法][AAD-Howto-Multitenant-Overview]」を確認します。

## <a name="related-content"></a>関連コンテンツ
Azure AD サインインがサポートされるアプリケーションの構築についての詳細、またはヘルプとサポートが必要な場合は、「[Azure Active Directory 開発者ガイド][AAD-Dev-Guide]」を参照してください。

Microsoft のコンテンツ改善のため、この記事の後の Disqus コメント セクションよりご意見をお寄せください。

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#web-application-quick-start-guides


<!--Image references-->











