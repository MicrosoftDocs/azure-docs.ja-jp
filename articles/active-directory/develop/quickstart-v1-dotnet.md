---
title: .NET Desktop (WPF) アプリでユーザーのサインインと Microsoft Graph API の呼び出しを行う | Microsoft Docs
description: サインインするために Azure AD と統合され、OAuth 2.0 を使用して Azure AD によって保護されている API を呼び出す .NET Windows Desktop アプリケーションを構築する方法を説明します。
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 946d376ef8af7d08b7924bd5b126c8b04e555668
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324426"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-a-net-desktop-wpf-app"></a>クイック スタート:.NET Desktop (WPF) アプリでユーザーのサインインと Microsoft Graph API の呼び出しを行う

[Microsoft ID プラットフォーム](v2-overview.md)は、Azure Active Directory (Azure AD) 開発者プラットフォームの進化版です。 これにより、すべての Microsoft ID にサインインして、Microsoft API (Microsoft Graph) や開発者が構築した API を呼び出すためのトークンを取得するアプリケーションを開発者が構築できます。

[Microsoft Authentication Library (MSAL)](msal-overview.md) によって、セキュリティで保護された Web API にアクセスする目的で、開発者は Microsoft ID プラットフォーム エンドポイントからトークンを取得できます。 Active Directory Authentication Library (ADAL) は開発者向け Azure AD (v1.0) エンドポイントと統合されます。このエンドポイントでは、MSAL が Microsoft ID プラットフォーム (v2.0) エンドポイントと統合されます。

新しいデスクトップ アプリケーションでは、Microsoft ID プラットフォーム (v2.0) と MSAL を使用してトークンを取得し、セキュリティで保護された Web API にアクセスすることをお勧めします。[クイック スタート:Windows デスクトップ アプリからトークンを取得し、Microsoft Graph API を呼び出す](quickstart-v2-windows-desktop.md)
