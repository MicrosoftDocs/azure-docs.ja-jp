---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 301abe95b245603e5414eef84ce74cdc8de01d19
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509880"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Windows デスクトップ アプリから Microsoft Graph API を呼び出す

このガイドでは、ネイティブの Windows デスクトップ .NET アプリケーションでアクセス トークンを使用して Microsoft Graph API を呼び出す方法を紹介します。 また、このアプリは、開発者用の Microsoft ID プラットフォーム v2.0 エンドポイントのアクセス トークンを必要とする他の API にもアクセスできます。 このプラットフォームは、以前 Azure AD という名前でした。

このガイドを完了すると、アプリケーションで個人アカウント (outlook.com、live.com など) を使用する保護された API を呼び出すことができるようになります。 このアプリケーションでは、Azure Active Directory を使用する会社または組織の職場および学校のアカウントも使用します。  

> [!NOTE]
> このガイドでは、Visual Studio 2015 Update 3、Visual Studio 2017、または Visual Studio 2019 が必要です。 これらのバージョンをお持ちでない場合は、 [Visual Studio 2019 を無料でダウンロードできます](https://www.visualstudio.com/downloads/)。

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>このガイドで生成されたサンプル アプリの動作

![このチュートリアルで生成されたサンプル アプリの動作の紹介](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

このガイドで作成するサンプル アプリケーションにより、Windows デスクトップ アプリケーションにおいて、Microsoft ID プラットフォーム エンドポイントからのトークンを受け付ける Microsoft Graph API または Web API に対してクエリを実行できるようになります。 このシナリオでは、Authorization ヘッダーを使用して HTTP 要求にトークンを追加します。 トークンの取得と更新は、Microsoft Authentication Library (MSAL) で処理されます。

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>保護された Web API にアクセスするためのトークン取得処理

ユーザーが認証されると、サンプル アプリケーションは、開発者用の Microsoft ID プラットフォームによって保護されている Microsoft Graph API または Web API のクエリに使用できるトークンを受け取ります。

Microsoft Graph などの API では、特定のリソースへのアクセスを許可するためにトークンが必要になります。 たとえば、トークンは、ユーザーのプロファイルの読み取り、ユーザーの予定表へのアクセス、メールの送信などに必要です。 アプリケーションでは、MSAL を使用してアクセス トークンを要求し、API スコープを指定することによってこれらのリソースにアクセスできます。 このアクセス トークンは、保護されたリソースに対するすべての呼び出しで HTTP Authorization ヘッダーに追加されます。

アクセス トークンのキャッシュと更新は MSAL が管理するため、アプリケーションが管理する必要はありません。

## <a name="nuget-packages"></a>NuGet パッケージ

このガイドでは、次の NuGet パッケージを使用します。

|ライブラリ|説明|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL.NET)|
