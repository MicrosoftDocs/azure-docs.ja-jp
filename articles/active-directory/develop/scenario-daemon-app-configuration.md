---
title: Web API を呼び出すデーモン アプリ (アプリの構成) - Microsoft ID プラットフォーム
description: Web API を呼び出すデーモン アプリをビルドする方法について説明します (アプリの構成)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: fd2da6baecdce3ab85a45347f27f573bf814445d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055755"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Web API を呼び出すデーモン アプリ - コードの構成

Web API を呼び出すデーモン アプリケーションのコードを構成する方法について説明します。

## <a name="msal-libraries-supporting-daemon-apps"></a>デーモン アプリをサポートする MSAL ライブラリ

デーモン アプリをサポートしている Microsoft ライブラリは次のとおりです。

  MSAL ライブラリ | 説明
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | デーモン アプリケーションの構築でサポートされているプラットフォームは、.NET Framework と .NET Core プラットフォームです (UWP、Xamarin.iOS、Xamarin.Android はサポートされていません。これらのプラットフォームはパブリック クライアント アプリケーションをビルドするために使用されるからです)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | 進行中の開発 - パブリック プレビュー中
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | 進行中の開発 - パブリック プレビュー中

## <a name="configuration-of-the-authority"></a>機関の構成

デーモン アプリケーションが、委任されたアクセス許可を使用せず、アプリケーションのアクセス許可を使用することを考慮すると、それらの*サポートされているアカウントの種類*を、*任意の組織のディレクトリ内のアカウントおよび個人の Microsoft アカウント (たとえば、Skype、Xbox、Outlook.com)* にすることはできません。 実際、Microsoft の個人アカウントについてデーモン アプリケーションに同意を付与するテナント管理者がいないのです。 *自分の所属組織のアカウント*または*任意の組織のアカウント*を選択する必要があります。

したがって、アプリケーション構成に指定された機関は、(組織に関連付けられたテナント ID またはドメイン名を指定して) テナント化する必要があります。

マルチテナント ツールを提供する ISV の場合は、`organizations` を使用することができます。 ただし、管理者の同意を付与する方法をお客様に説明することも必要になる点に留意してください。 詳細については、「[テナント全体の同意の要求する](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)」を参照してください。 また、現在、MSAL には、クライアント資格情報が (証明書ではなく) アプリケーション シークレットである場合にのみ、`organizations` が許可されるという制限があります。 [MSAL.NET バグ #891](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/891) に関するページを参照してください

## <a name="application-configuration-and-instantiation"></a>アプリケーションの構成とインスタンス化

MSAL ライブラリでは、クライアントの資格情報 (シークレットまたは証明書) は、機密クライアント アプリケーション構築のパラメーターとして渡されます。

> [!IMPORTANT]
> アプリケーションが、サービスとして実行されているコンソール アプリケーションであっても、それがデーモン アプリケーションの場合は、機密クライアント アプリケーションである必要があります。

### <a name="msalnet"></a>MSAL.NET

[Microsoft.IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet パッケージをアプリケーションに追加します。

MSAL.NET 名前空間を使用します

```CSharp
using Microsoft.Identity.Client;
```

デーモン アプリケーションが `IConfidentialClientApplication` によって提示されます。

```CSharp
IConfidentialClientApplication app;
```

アプリケーション シークレットを使用してアプリケーションをビルドするコードを次に示します。

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

証明書を使用してアプリケーションをビルドするコードを次に示します。

```CSharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

### <a name="msalpython"></a>MSAL.Python

```Python
# Create a preferably long-lived app instance which maintains a token cache.

app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache

    )
```

### <a name="msaljava"></a>MSAL.Java

```Java
PrivateKey key = getPrivateKey();
X509Certificate publicCertificate = getPublicCertificate();

// create clientCredential with public and private key
IClientCredential credential = ClientCredentialFactory.create(key, publicCertificate);

ConfidentialClientApplication cca = ConfidentialClientApplication
  .builder(CLIENT_ID, credential)
  .authority(AUTHORITY_MICROSOFT)
  .build();
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [デーモン アプリ - アプリのトークンの取得](./scenario-daemon-acquire-token.md)