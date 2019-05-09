---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 03/20/2019
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: 971ae8cd44f1b345d3a71b8fa4f256c8f25ef961
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59503271"
---
# <a name="call-the-microsoft-graph-api-from-an-ios-application"></a>iOS アプリケーションから Microsoft Graph API を呼び出す

このガイドでは、ネイティブ iOS アプリケーション (Swift) で Microsoft ID プラットフォーム エンドポイントからのアクセス トークンを必要とする API を呼び出す方法を説明します。 アクセス トークンを取得し、Microsoft Graph API や他の API の呼び出しでそれを使う方法について説明します。

このガイドの演習を完了すると、Azure AD を使用する会社や組織の保護された API をアプリケーションで呼び出すことができるようになります。 アプリケーションで outlook.com や live.com などの個人アカウントおよび職場または学校アカウントを使って、保護された API を呼び出すことができます。

## <a name="prerequisites"></a>前提条件

- このガイドで作成するサンプルには、XCode バージョン 10.x が必要です。 XCode は、[iTunes の Web サイト](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode ダウンロード URL")からダウンロードできます。
- パッケージの管理には、[Carthage](https://github.com/Carthage/Carthage) 依存関係マネージャーが必要です。

## <a name="how-this-guide-works"></a>このガイドの利用法

![このチュートリアルで生成されたサンプル アプリの動作の紹介](media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

このガイドのサンプル アプリケーションを参考にすると、iOS アプリケーションで、Microsoft Graph API または Microsoft ID プラットフォーム エンドポイントからトークンを受け取る Web API に対してクエリを実行できるようになります。 このシナリオでは、トークンは **Authorization** ヘッダーを使って HTTP 要求に追加されます。 トークンの取得と更新は、Microsoft Authentication Library (MSAL) で処理されます。

### <a name="handle-token-acquisition-for-access-to-protected-web-apis"></a>保護された Web API にアクセスするためのトークンの取得を処理する

ユーザー認証の後、サンプル アプリケーションはトークンを受け取ります。 トークンは、Microsoft Graph API や、Microsoft ID プラットフォーム エンドポイントによって保護された Web API に対してクエリを実行するために使われます。

Microsoft Graph などの API では、特定のリソースへのアクセスを許可するためにアクセス トークンが必要になります。 トークンは、ユーザーのプロファイルの読み取り、ユーザーの予定表へのアクセス、メールの送信などに必要です。 アプリケーションでは、MSAL を使って API スコープを指定することで、アクセス トークンを要求できます。 アクセス トークンは、保護されたリソースに対するすべての呼び出しで、HTTP の **Authorization** ヘッダーに追加されます。

MSAL がアクセス トークンのキャッシュと更新を管理するため、アプリケーションでは何もする必要がありません。

## <a name="libraries"></a>ライブラリ

このガイドでは、次のライブラリを使用します。

|ライブラリ|説明|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|Microsoft Authentication Library の iOS 用プレビュー|
