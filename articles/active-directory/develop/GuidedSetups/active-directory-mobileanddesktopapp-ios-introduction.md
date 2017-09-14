---
title: "Azure AD v2 iOS の概要 - 紹介 | Microsoft Docs"
description: "iOS (Swift) アプリケーションで、アクセス トークンを必要とする API を Azure Active Directory v2 エンドポイントから呼び出す方法"
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
ms.date: 05/09/2017
ms.author: andret
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: e1a28c70edddca878c18d860b3029324955fff15
ms.contentlocale: ja-jp

---

# <a name="call-the-microsoft-graph-api-from-an-ios-app"></a>iOS アプリから Microsoft Graph API を呼び出す

このガイドでは、ネイティブの iOS アプリケーション (Swift) がアクセス トークンを取得し、Microsoft Graph API またはアクセス トークンを必要とする他の API を、Azure Active Directory v2 エンドポイントから呼び出す方法について説明します。

このガイドの最後では、個人アカウント (outlook.com、live.com などを含む) だけでなく、Azure Active Directory のあるすべての会社や組織の職場/学校アカウントを使用して、保護された API をアプリケーションで呼び出せるようになります。

> ### <a name="pre-requisites"></a>前提条件
> - このガイドには XCode 8.x が必要です。 XCode は[ここから]ダウンロードできます(https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode ダウンロード URL")。
> - パッケージ管理の [Carthage](https://github.com/Carthage/Carthage)

### <a name="how-this-guide-works"></a>このガイドの利用法

![このガイドの利用法](media/active-directory-mobileanddesktopapp-ios-introduction/iosintro.png)

このガイドで作成したサンプル アプリケーションにより、iOS アプリケーションで、Microsoft Graph API または Azure Active Directory v2 エンドポイントからトークンを受け取る Web API にクエリを実行できるようになります。 このシナリオでは、トークンは Authorization ヘッダーを介して HTTP 要求に追加されます。 トークンの取得と更新は、Microsoft Authentication Library (MSAL) で処理されます。


### <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>保護された Web API にアクセスするためのトークン取得処理

ユーザーが認証されると、サンプル アプリケーションは、Microsoft Graph API または Microsoft Azure Active Directory v2 で保護された Web API の照会に使用できるトークンを受け取ります。

Microsoft Graph などの API では、特定のリソースにアクセスできるアクセス トークンが必要です。たとえば、ユーザーのプロファイルの読み取り、ユーザーの予定表へのアクセス、電子メールの送信などです。 API スコープを指定することで、アプリケーションでは、MSAL を使用してアクセス トークンを要求できます。 このアクセス トークンは、保護されたリソースに対するすべての呼び出しで HTTP 認証ヘッダーに追加されます。

MSAL がアクセス トークンのキャッシュと更新を管理するため、アプリケーションでは何もする必要がありません。


### <a name="nuget-packages"></a>NuGet パッケージ

このガイドでは、次の NuGet パッケージを使用します。

|ライブラリ|説明|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|Microsoft Authentication Library の iOS 用プレビュー|


