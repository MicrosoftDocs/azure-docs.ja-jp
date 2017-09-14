---
title: "Azure AD v2 Android の概要 - 入門 | Microsoft Docs"
description: "Android アプリがアクセス トークンを取得し、Microsoft Graph API またはアクセス トークンを必要とする API を、Azure Active Directory v2 エンドポイントから呼び出す方法"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: d933781713456f73aa76db557fdf35672dfb2a29
ms.contentlocale: ja-jp


---

# <a name="call-the-microsoft-graph-api-from-an-android-app"></a>Android アプリから Microsoft Graph API を呼び出す

このガイドでは、ネイティブの Android アプリケーションがアクセス トークンを取得し、Microsoft Graph API またはアクセス トークンを必要とする他の API を、Azure Active Directory v2 エンドポイントから呼び出す方法について説明します。

このガイドの最後では、個人アカウント (outlook.com、live.com などを含む) だけでなく、Azure Active Directory のあるすべての会社や組織の職場/学校アカウントを使用して、保護された API をアプリケーションで呼び出せるようになります。  

### <a name="how-this-sample-works"></a>このサンプルのしくみ
![このサンプルのしくみ](media/active-directory-mobileanddesktopapp-android-intro/android-intro.png)

このガイドで作成するサンプルは、Azure Active Directory v2 エンドポイントからのトークンを受け入れる Web API (ここでは Microsoft Graph API) の照会に Android アプリケーションが使用されている、というシナリオに基づいています。 このシナリオでは、トークンは Authorization ヘッダーを介して HTTP 要求に追加されます。 トークンの取得と更新は、Microsoft Authentication Library (MSAL) で処理されます。

### <a name="pre-requisites"></a>前提条件
* このガイド付きセットアップは Android Studio を想定していますが、その他の Android アプリケーション開発環境でもできます。 
* Android SDK 21 以降が必要です (SDK 25 を推奨)。
* このリリースの Android 向け Microsoft Authentication Library (MSAL) には、Google Chrome またはユーザー設定タブを使用している Web ブラウザーが必要です。

> 注: Google Chrome は、Visual Studio Emulator for Android には含まれていません。 API 25 を持つエミュレーター、または Google Chrome がインストールされている API 21 以降のイメージで、このコードをテストすることをお勧めします。


### <a name="how-to-handle-token-acquisition-to-access-a-protected-web-api"></a>保護された Web API にアクセスするトークンの取得を処理する方法

ユーザーが認証されると、サンプル アプリケーションは、Microsoft Graph API または Microsoft Azure Active Directory v2 で保護された Web API の照会に使用できるトークンを受け取ります。

Microsoft Graph などの API では、特定のリソースにアクセスできるアクセス トークンが必要です。たとえば、ユーザーのプロファイルの読み取り、ユーザーの予定表へのアクセス、電子メールの送信などです。 アプリケーションでは、MSAL を使用してアクセス トークンを要求し、API スコープを指定することによってこれらのリソースにアクセスできます。 このアクセス トークンは、保護されたリソースに対するすべての呼び出しで HTTP 認証ヘッダーに追加されます。 

MSAL がアクセス トークンのキャッシュと更新を管理するため、アプリケーションでは何もする必要がありません。

### <a name="libraries"></a>ライブラリ

このガイドでは、次のライブラリを使用します。

|ライブラリ|Description|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft Authentication Library (MSAL)|

