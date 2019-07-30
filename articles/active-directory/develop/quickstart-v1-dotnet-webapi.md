---
title: 認証と承認のために Azure AD を利用する .NET Web API を構築する | Microsoft Docs
description: 認証と承認のために Azure AD と連携する .NET MVC Web API を構築する方法
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7891ea6aa2dbe10456c701b5e0216117f6ed27b
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68380704"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>クイック スタート:認証と認可のために Azure AD と連携する .NET Web API を構築する

[Microsoft ID プラットフォーム](v2-overview.md)は、Azure Active Directory (Azure AD) 開発者プラットフォームの進化版です。 これにより、すべての Microsoft ID にサインインして、Microsoft API (Microsoft Graph) や開発者が構築した API を呼び出すためのトークンを取得するアプリケーションを開発者が構築できます。

[Microsoft Authentication Library (MSAL)](msal-overview.md) によって、セキュリティで保護された Web API にアクセスする目的で、開発者は Microsoft ID プラットフォーム エンドポイントからトークンを取得できます。 Active Directory Authentication Library (ADAL) は開発者向け Azure AD (v1.0) エンドポイントと統合されます。このエンドポイントでは、MSAL が Microsoft ID プラットフォーム (v2.0) エンドポイントと統合されます。

新しい Web API では、Microsoft ID プラットフォーム (v2.0) と MSAL を使用してトークンを取得し、セキュリティで保護された Web API にアクセスすることをお勧めします:[クイック スタート:ASP.NET Web アプリへの "Microsoft でサインイン" の追加](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2#calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2)
