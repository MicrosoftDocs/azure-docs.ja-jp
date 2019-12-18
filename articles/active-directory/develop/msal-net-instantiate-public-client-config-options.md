---
title: パブリック クライアント アプリをインスタンス化する (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: .NET 用 Microsoft Authentication Library (MSAL.NET) を使用して、構成オプションでパブリック クライアント アプリケーションをインスタンス化する方法について説明します。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4052c67b019b5ca6d3936e34ec8a5b2b1aff89b1
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74915839"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>MSAL.NET を使用して、構成オプションでパブリック クライアント アプリケーションをインスタンス化する

この記事では、.NET 用 Microsoft Authentication Library (MSAL.NET) を使用して[パブリック クライアント アプリケーション](msal-client-applications.md)を初期化する方法について説明します。  アプリケーションは、設定ファイルで定義されている構成オプションを使用してインスタンス化されます。

アプリケーションを初期化する前に、まず、そのアプリケーションを[登録](quickstart-register-app.md)して、Microsoft ID プラットフォームに統合できるようにする必要があります。 登録後に、次の情報が必要な場合があります (Azure portal で検索できます)。

- クライアント ID (GUID を表す文字列)
- ID プロバイダーの URL (名前付きインスタンス) とアプリケーションのサインイン対象ユーザー。 これら 2 つのパラメーターは機関と総称されます。
- 組織専用の基幹業務アプリケーション (シングル テナント アプリケーションとも呼ばれます) を作成している場合はテナント ID。
- Web アプリ、および場合によってはパブリック クライアント アプリの場合 (特に、アプリでブローカーを使用する必要がある場合)、ID プロバイダーがセキュリティ トークンを使用してアプリケーションに連絡する redirectUri も設定します。


.NET Core コンソール アプリケーションでは次の*appsettings.json* 構成ファイルを使用することができました。

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

次のコードは、.NET 構成フレームワークを使用してこのファイルを読み取ります。

```csharp
public class SampleConfiguration
{
    /// <summary>
    /// Authentication options
    /// </summary>
    public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

    /// <summary>
    /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
    /// in Microsoft Azure public clouds or national / sovereign clouds
    /// </summary>
    public string MicrosoftGraphBaseEndpoint { get; set; }

    /// <summary>
    /// Reads the configuration from a json file
    /// </summary>
    /// <param name="path">Path to the configuration json file</param>
    /// <returns>SampleConfiguration as read from the json file</returns>
    public static SampleConfiguration ReadFromJsonFile(string path)
    {
        // .NET configuration
        IConfigurationRoot Configuration;
        var builder = new ConfigurationBuilder()
          .SetBasePath(Directory.GetCurrentDirectory())
        .AddJsonFile(path);
        Configuration = builder.Build();

        // Read the auth and graph endpoint config
        SampleConfiguration config = new SampleConfiguration()
        {
            PublicClientApplicationOptions = new PublicClientApplicationOptions()
        };
        Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

次のコードは、設定ファイルから構成を使用して、アプリケーションを作成します。

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

