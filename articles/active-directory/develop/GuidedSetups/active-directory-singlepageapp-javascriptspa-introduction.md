---
title: "Azure AD v2 JS SPA のガイド付きセットアップ - 入門 | Microsoft Docs"
description: "JavaScript SPA アプリケーションで、アクセス トークンを必要とする API を Azure Active Directory v2 エンドポイントから呼び出す方法"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2017
ms.author: andret
ms.openlocfilehash: 3d195d0d67f8f82c9450ffd93767917698addee3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>JavaScript Single Page Application (SPA) から Microsoft Graph API を呼び出す

このガイドでは、JavaScript Single Page Application (SPA) が個人アカウント、または職場および学校アカウントにサインインし、アクセス トークンを取得し、Microsoft Graph API またはアクセス トークンを必要とする他の API を、Azure Active Directory v2 エンドポイントから呼び出す方法について説明します。

### <a name="how-this-guide-works"></a>このガイドの利用法

![このガイドで生成されたサンプル アプリの動作](media/active-directory-singlepageapp-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>詳細情報

このガイドで作成したサンプル アプリケーションにより、JavaScript SPA で、Microsoft Graph API または Azure Active Directory v2 エンドポイントからトークンを受け取る Web API にクエリを実行できるようになります。 このシナリオでは、ユーザーのサインイン後に、アクセス トークンが要求され、Authorization ヘッダーを介して HTTP 要求に追加されます。 トークンの取得と更新は、Microsoft Authentication Library (MSAL) で処理されます。

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>ライブラリ

このガイドでは、次のライブラリを使用します。

|ライブラリ|Description|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|JavaScript プレビュー用の Microsoft Authentication Library|

> [!NOTE]
> *msal.js* は、"*Azure Active Directory v2 エンドポイント*" を対象とします。これにより、個人アカウント、または学校および職場アカウントでサインインして、トークンを取得することができます。 "*Azure Active Directory v2 エンドポイント*" には、[いくつかの制限](..\active-directory-v2-limitations.md)があります。 学校および職場アカウントのみを使用する場合は、*adal.js* と "*V1 エンドポイント*" を使用します。 v1 エンドポイントと v2 のエンドポイントの相違点を理解するには、[v1 と v2 の比較](..\active-directory-v2-compare.md)に関するページを参照してください。

<!--end-collapse-->
