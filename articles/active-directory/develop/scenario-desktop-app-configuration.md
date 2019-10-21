---
title: Web API を呼び出すデスクトップ アプリ (コードの構成) - Microsoft ID プラットフォーム
description: Web API を呼び出すデスクトップ アプリを構築する方法 (アプリのコード構成) について説明します
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0926e6800dbcd81d2e542e27afe3afb1240cff22
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268409"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>Web API を呼び出すデスクトップ アプリ - コードの構成

お使いのアプリケーションを作成したところで、アプリケーションの座標を使用してコードを構成する方法について説明します。

## <a name="msal-libraries"></a>MSAL ライブラリ

現在、複数のプラットフォーム上でデスクトップ アプリケーションをサポートしている唯一の MSAL ライブラリは、MSAL.NET です

iOS および macOS 用の MSAL では、macOS 上でのみ実行されるデスクトップアプリケーションがサポートされています。 

## <a name="public-client-application-with-msalnet"></a>MSAL.NET を使用したパブリック クライアント アプリケーション

コードの観点から、デスクトップ アプリケーションは、パブリック クライアント アプリケーションであり、このため、MSAL.NET `IPublicClientApplication` を構築して操作します。 また、対話型認証を使用するかどうかにかかわらず、状況は少し異なります。

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>コードで排他的に

以下のコードでは、パブリック クライアント アプリケーションをインスタンス化して、職場および学校のアカウントまたは個人の Microsoft アカウントを使用して、Microsoft Azure のパブリック クラウドにユーザーをサインインさせます。

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

前述のように、対話型認証またはデバイス コード フローを使用する場合は、`.WithRedirectUri` 修飾子を使用することが必要になる可能性があります。

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="using-configuration-files"></a>構成ファイルの使用

次のコードは、構成オブジェクトからパブリック クライアント アプリケーションをインスタンス化します。構成オブジェクトは、プログラムで入力することも、構成ファイルから読み取ることもできます

```CSharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>より詳細な構成

多数の修飾子を追加して、アプリケーションの構築を細かく設定できます。 たとえば、お使いのアプリケーションを国内クラウド (ここでは米国政府) のマルチテナント アプリケーションにする場合、次のように記述できます。

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET には、ADFS 2019 用の修飾子も含まれています。

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

最後に、Azure AD B2C テナントのトークンを取得する場合、次のコード スニペットで示されているとおり、お使いのテナントを指定できます。

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>詳細情報

MSAL.NET デスクトップ アプリケーションを構成する方法の詳細については、以下のとおりです。

- `PublicClientApplicationBuilder` で使用可能なすべての修飾子の一覧については、リファレンス ドキュメント [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods) を参照してください
- `PublicClientApplicationOptions` に公開されているすべてのオプションの説明については、リファレンス ドキュメント内の [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions) を参照してください

## <a name="complete-example-with-configuration-options"></a>構成オプション付きの詳細な例

以下の `appsettings.json` 構成ファイルが含まれている .NET Core コンソール アプリケーションを想像してください。

```JSon
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

.NET で提供される構成フレームワークを使用してこのファイルを読み込むコードはほとんどありません。

```CSharp
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
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

アプリケーションを作成するには、以下のコードの記述するだけで済みます。

```CSharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

また、`.Build()` メソッドを呼び出す前に、前述のように `.WithXXX` メソッドを呼び出すことで構成を上書きできます。

## <a name="public-client-application-with-msal-for-ios-and-macos"></a>iOS および macOS 用の MSAL を使用したパブリック クライアント アプリケーション

以下のコードでは、パブリック クライアント アプリケーションをインスタンス化して、職場および学校のアカウントまたは個人の Microsoft アカウントを使用して、Microsoft Azure のパブリック クラウドにユーザーをサインインさせます。

### <a name="quick-configuration"></a>クイック構成

Objective-C:

```objc
NSError *msalError = nil;
    
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift:
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>より詳細な構成

多数の修飾子を追加して、アプリケーションの構築を細かく設定できます。 たとえば、お使いのアプリケーションを国内クラウド (ここでは米国政府) のマルチテナント アプリケーションにする場合、次のように記述できます。

Objective-C:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];
                                                          
MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];
                                                                  
NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

Swift:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)
        
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [デスクトップ アプリのトークンの取得](scenario-desktop-acquire-token.md)
