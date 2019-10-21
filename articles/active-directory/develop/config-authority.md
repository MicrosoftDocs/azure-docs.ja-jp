---
title: さまざまな ID プロバイダーを使用するように iOS および macOS 用 MSAL を構成する | Microsoft ID プラットフォーム
description: B2C、ソブリン クラウド、ゲスト ユーザーなどのさまざまな機関を、iOS および macOS 用 MSAL で使用する方法について説明します。
services: active-directory
documentationcenter: ''
author: tylermsft
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 114e67e2dca7ba304cb92b21a894e045cbe0c9e9
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71269397"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>方法:さまざまな ID プロバイダーを使用するように iOS および macOS 用 MSAL を構成する

この記事では、Azure Active Directory (Azure AD)、企業-消費者間 (B2C)、ソブリン クラウド、ゲスト ユーザーなどのさまざまな機関向けに、iOS および macOS 用の Microsoft Authentication Library アプリを構成する方法について説明します。  この記事では、一般に、ID プロバイダーとしての機関を考えてみましょう。

## <a name="default-authority-configuration"></a>既定の機関の構成

`MSALPublicClientApplication` は `https://login.microsoftonline.com/common` の既定の機関 URL を使用して構成されています。これは、ほとんどの Azure Active Directory (AAD) シナリオに適しています。 各国のクラウドのような高度なシナリオを実装する場合や B2C を使用する場合を除いて、変更する必要はありません。

> [!NOTE]
> ID プロバイダーとしての Active Directory フェデレーション サービス (ADFS) を使用した先進認証はサポートされていません (詳細については、「[開発者向けの AD FS のシナリオ](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-scenarios-for-developers)」を参照してください)。 ADFS は、フェデレーションを通じてサポートされます。

## <a name="change-the-default-authority"></a>既定の機関を変更する

企業-消費者間 (B2C) などの一部のシナリオでは、既定の機関を変更することが必要になる場合があります。

### <a name="b2c"></a>B2C

B2C を使用するには、[Microsoft Authentication Library (MSAL)](reference-v2-libraries.md) で異なる機関構成が必要となります。 MSAL では、1 つの機関の URL 形式が B2C として認識されています。 認識されている B2C 機関の形式は `https://<host>/tfp/<tenant>/<policy>` です。例えば、`https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy` となります。 ただし、機関を B2C 機関として明示的に宣言することで、他の任意のサポートされている B2C 機関 URL を使用することもできます。

B2C の任意の URL 形式をサポートするには、次のように、任意の URL を使用して `MSALB2CAuthority` を設定します。

Objective-C
```objc
NSURL *authorityURL = [NSURL URLWithString:@"arbitrary URL"];
MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
```
Swift
```swift
guard let authorityURL = URL(string: "arbitrary URL") else {
    // Handle error
    return
}
let b2cAuthority = try MSALB2CAuthority(url: authorityURL)
```

既定の B2C 機関の形式を使用しないすべての B2C 機関は、既知の機関として宣言されている必要があります。

各 B2C 機関を、機関のポリシーのみが異なる場合であっても、既知の機関の一覧に追加します。

Objective-C
```objc
MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:b2cAuthority];
b2cApplicationConfig.knownAuthorities = @[b2cAuthority];
```
Swift
```swift
let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)
b2cApplicationConfig.knownAuthorities = [b2cAuthority]
```

アプリから新しいポリシーが要求された場合、機関 URL はポリシーごとに異なるため、機関の URL を変更する必要があります。 

B2C アプリケーションを構成するには、次のように、`MSALPublicClientApplication` を作成する前に `MSALPublicClientApplicationConfig` 内で `MSALB2CAuthority` のインスタンスを使用して `@property MSALAuthority *authority` を設定します。

Objective-C
```ObjC
    // Create B2C authority URL
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy"];
    
    MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
    if (!b2cAuthority)
    {
        // Handle error
        return;
    }
    
    // Create MSALPublicClientApplication configuration
    MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                                   initWithClientId:@"your-client-id"
                                                                   redirectUri:@"your-redirect-uri"
                                                                   authority:b2cAuthority];

    // Initialize MSALPublicClientApplication
    MSALPublicClientApplication *b2cApplication =
    [[MSALPublicClientApplication alloc] initWithConfiguration:b2cApplicationConfig error:&error];
    
    if (!b2cApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    // Create B2C authority URL
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy") else {
        // Handle error
        return
    }
    let b2cAuthority = try MSALB2CAuthority(url: authorityURL)

    // Create MSALPublicClientApplication configuration
    let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)

    // Initialize MSALPublicClientApplication
    let b2cApplication = try MSALPublicClientApplication(configuration: b2cApplicationConfig)
} catch {
    // Handle error
}
```

### <a name="sovereign-clouds"></a>ソブリン クラウド

アプリがソブリン クラウドで実行されている場合、`MSALPublicClientApplication` で機関 URL の変更が必要になる場合があります。 次の例では、ドイツの AAD クラウドで機能するように機関の URL を設定します。

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.de/common"];
    MSALAuthority *sovereignAuthority = [MSALAuthority authorityWithURL:authorityURL error:&authorityError];
    
    if (!sovereignAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:sovereignAuthority];
    
    
    MSALPublicClientApplication *sovereignApplication = [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    
    if (!sovereignApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.de/common") else {
        //Handle error
        return
    }
    let sovereignAuthority = try MSALAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: sovereignAuthority)
            
    let sovereignApplication = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

場合によっては、各ソブリン クラウドに異なるスコープを渡す必要があります。 どのスコープを送信するかは、使用しているリソースによって異なります。 たとえば、世界全体のクラウドでは `"https://graph.microsoft.com/user.read"` を、ドイツのクラウドでは `"https://graph.microsoft.de/user.read"` を使用することができます。

### <a name="signing-a-user-into-a-specific-tenant"></a>特定のテナントにユーザーをサインインさせる

機関 URL が `"login.microsoftonline.com/common"` に設定されている場合、ユーザーはホーム テナントにサインインすることになります。 ただし、ユーザーが別のテナントにサインインする必要があるアプリもあれば、単一のテナントでのみ動作するアプリもあります。

特定のテナントにユーザーをサインインさせるには、特定の機関で `MSALPublicClientApplication` を構成します。 例:

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

次は、ユーザーを特定のテナントにサインインさせる方法を示しています。

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4"];
    MSALAADAuthority *tenantedAuthority = [[MSALAADAuthority alloc] initWithURL:authorityURL error:&authorityError];
    
    if (!tenantedAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:tenantedAuthority];
    
    MSALPublicClientApplication *application =
    [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    if (!application)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4") else {
        //Handle error
        return
    }    
    let tenantedAuthority = try MSALAADAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: tenantedAuthority)
            
    let application = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

## <a name="supported-authorities"></a>サポート対象の機関

### <a name="msalauthority"></a>MSALAuthority

`MSALAuthority` クラスは、MSAL 機関クラスの基本抽象クラスです。 このインスタンスを `alloc` または `new` を使用して作成しないようにしてください。 代わりに、そのサブクラスの 1 つを直接作成するか (`MSALAADAuthority`、`MSALB2CAuthority`)、またはファクトリ メソッド `authorityWithURL:error:` を使用して、機関 URL を使用してサブクラスを作成します。

正規化された機関 URL を取得するには、`url` プロパティを使用します。 機関の一部ではない余分なパラメーターおよびパス コンポーネントまたはフラグメントは、返される正規化された機関 URL に含まれません。

次に示すのは、使用する機関に応じてインスタンス化できる `MSALAuthority` のサブクラスです。

### <a name="msalaadauthority"></a>MSALAADAuthority

`MSALAADAuthority` は AAD 機関を表します。 機関 URL は次の形式にする必要があります (`<port>` は省略可能です): `https://<host>:<port>/<tenant>`。

### <a name="msalb2cauthority"></a>MSALB2CAuthority

`MSALB2CAuthority` は B2C 機関を表します。 既定では、B2C 機関 URL は次の形式にする必要があります (`<port>` は省略可能です): `https://<host>:<port>/tfp/<tenant>/<policy>`。 ただし、MSAL では、他の任意の B2C 機関の形式もサポートされています。

## <a name="next-steps"></a>次の手順

[認証フローとアプリケーションのシナリオ](authentication-flows-app-scenarios.md)の詳細を確認します
