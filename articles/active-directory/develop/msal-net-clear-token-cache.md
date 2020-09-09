---
title: トークン キャッシュをクリアする (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library for .NET (MSAL.NET) を使用してトークン キャッシュをクリアする方法を説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 9a86a535bf429dcc81810c6c39ba415a158b20ec
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88166214"
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
