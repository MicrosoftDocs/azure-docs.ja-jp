---
title: Microsoft Authentication Library for .NET を使用してトークン キャッシュをクリアする | Azure
description: Microsoft Authentication Library for .NET (MSAL.NET) を使用してトークン キャッシュをクリアする方法を説明します。
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cee6443db0b019f79a80cf5b7c0e2a7a50240f2
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532667"
---
# <a name="clear-the-token-cache-using-msalnet"></a>MSAL.NET を使用してトークン キャッシュをクリアする

Microsoft Authentication Library for .NET (MSAL.NET) を使用して[アクセス トークンを取得](msal-acquire-cache-tokens.md)すると、トークンはキャッシュされます。 トークンが必要な場合に、アプリケーションは最初に `AcquireTokenSilent` メソッドを呼び出して、キャッシュ内に利用可能なトークンがあるかどうかを確認します。 

キャッシュのクリアは、キャッシュからアカウントを削除することによって行います。 ただし、これによってブラウザーにあるセッション Cookie が削除されることはありません。  次の例は、パブリック クライアント アプリケーションをインスタンス化し、アプリケーションのアカウントを取得してから、アカウントを削除します。

```csharp
private readonly IPublicClientApplication _app;
private static readonly string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
private static readonly string Authority = string.Format(CultureInfo.InvariantCulture, AadInstance, Tenant);

_app = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .Build();

var accounts = (await _app.GetAccountsAsync()).ToList();

// clear the cache
while (accounts.Any())
{
   await _app.RemoveAsync(accounts.First());
   accounts = (await _app.GetAccountsAsync()).ToList();
}

```

トークンの取得とキャッシュの詳細については、[アクセス トークンの取得](msal-acquire-cache-tokens.md)に関するページを参照してください。
