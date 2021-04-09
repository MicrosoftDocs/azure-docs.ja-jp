---
title: ADAL と MSAL の両アプリ間での SSO (iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: ADAL と MSAL の両アプリ間で SSO を共有する方法について説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 396e9cfeace8791a59dec4a9c9c7203212f57304
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584249"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>方法: macOS および iOS 上の ADAL アプリと MSAL アプリの間での SSO

iOS 向けの Microsoft Authentication Library (MSAL) では、アプリケーションごとの SSO 状態を [ADAL Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc) と共有できます。 ADAL ベースと MSAL ベースのアプリが混在することになってもユーザーがクロスアプリ SSO を引き続き活用できるので、MSAL へのアプリの移行はご自分のペースで進めることができます。

MSAL SDK を使用したアプリ間での SSO 設定のガイダンスを探している場合は、[複数アプリ間でのサイレント SSO](single-sign-on-macos-ios.md#silent-sso-between-apps) に関するページを参照してください。 この記事では、ADAL と MSAL の間での SSO に焦点を当てます。

SSO 実装の詳細は、お客様が使用している ADAL のバージョンによって異なります。

## <a name="adal-27x"></a>ADAL 2.7.x

このセクションでは、MSAL と ADAL 2.7.x の SSO の違いについて説明します

### <a name="cache-format"></a>キャッシュの形式

ADAL 2.7.x では、MSAL のキャッシュ形式を読み取ることができます。 バージョン 2.7.x の ADAL を使用したクロスアプリ SSO では、特別な対応は必要ありません。 ただし、これら 2 つのライブラリでサポートされているアカウント識別子の違いに注意してください。

### <a name="account-identifier-differences"></a>アカウント識別子の違い

MSAL と ADAL では、使用されるアカウント識別子が異なります。 ADAL では、プライマリ アカウント識別子として UPN が使用されます。 MSAL では、AAD アカウントの場合はオブジェクト ID とテナント ID に基づく表示不能なアカウント識別子が、その他の種類のアカウントには `sub` 要求が使用されます。

MSAL の結果で `MSALAccount` オブジェクトを受け取る場合、`identifier` プロパティにアカウント識別子が含まれています。 これ以降、アプリケーションでのサイレント要求にはこの識別子を使用する必要があります。

`MSALAccount` オブジェクトには、`identifier` に加えて、`username` という表示可能な識別子も含まれています。 これは、ADAL では `userId` に変換されます。 `username` は一意の識別子とは見なされず、いつでも変更可能であるので、ADAL との後方互換性を維持するシナリオでのみ使用するようにしてください。 MSAL では `username` と `identifier` のいずれを使用したキャッシュ クエリもサポートされていますが、クエリには `identifier` を使用することをお勧めします。

次の表では、ADAL と MSAL のアカウント識別子の違いをまとめています。

| アカウント識別子                | MSAL                                                         | ADAL 2.7.x      | 古い ADAL (2.7.x より前の ADAL) |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| 表示可能な識別子            | `username`                                                   | `userId`        | `userId`                       |
| 一意の表示不能な識別子 | `identifier`                                                 | `homeAccountId` | N/A                            |
| 既知のアカウント識別子なし               | `MSALPublicClientApplication` の `allAccounts:` API によりすべてのアカウントを照会する | 該当なし             | N/A                            |

これらの識別子を提供する `MSALAccount` インターフェイスを次に示します。

```objc
@protocol MSALAccount <NSObject>

/*!
 Displayable user identifier. Can be used for UI and backward compatibility with ADAL.
 */
@property (readonly, nullable) NSString *username;

/*!
 Unique identifier for the account.
 Save this for account lookups from cache at a later point.
 */
@property (readonly, nullable) NSString *identifier;

/*!
 Host part of the authority string used for authentication based on the issuer identifier.
 */
@property (readonly, nonnull) NSString *environment;

/*!
 ID token claims for the account.
 Can be used to read additional information about the account, e.g. name
 Will only be returned if there has been an id token issued for the client Id for the account's source tenant.
 */
@property (readonly, nullable) NSDictionary<NSString *, NSString *> *accountClaims;

@end
```

### <a name="sso-from-msal-to-adal"></a>MSAL から ADAL への SSO

MSAL アプリと ADAL アプリがあり、ユーザーが初めに MSAL ベースのアプリにサインインする場合は、`MSALAccount` オブジェクトの `username` を保存して ADAL ベースのアプリに `userId` として渡すことで、ADAL アプリへの SSO を実現できます。 以後、ADAL では、`acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:` API を使用してアカウント情報を自動的に照会できます。

### <a name="sso-from-adal-to-msal"></a>ADAL から MSAL への SSO

MSAL アプリと ADAL アプリがあり、ユーザーが初めに ADAL ベースのアプリにサインインする場合は、ADAL のユーザー識別子を使用して MSAL でアカウントを参照できます。 これは、ADAL から MSAL に移行する場合にも該当します。

#### <a name="adals-homeaccountid"></a>ADAL の homeAccountId

ADAL 2.7.x では、このプロパティを介して結果の `ADUserInformation` オブジェクトの `homeAccountId` が返されます。

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

ADAL の `homeAccountId` は、MSAL の `identifier` に相当します。 この識別子を保存して、`accountForIdentifier:error:` API による MSAL 内でのアカウント参照に使用できます。

#### <a name="adals-userid"></a>ADAL の `userId`

`homeAccountId` を利用できない場合や、表示可能な識別子しかない場合は、ADAL の `userId` を使用して MSAL のアカウントを参照できます。

MSAL では、まず `username` または `identifier` によりアカウントが参照されます。 `identifier` がある場合、クエリには必ずこれを使用し、`username` はフォールバックにのみ使用してください。 アカウントが見つかったら、`acquireTokenSilent` 呼び出しでそのアカウントを使用します。

Objective-C:

```objc
NSString *msalIdentifier = @"previously.saved.msal.account.id";
MSALAccount *account = nil;
    
if (msalIdentifier)
{
    // If you have MSAL account id returned either from MSAL as identifier or ADAL as homeAccountId, use it
    account = [application accountForIdentifier:@"my.account.id.here" error:nil];
}
else
{
    // Fallback to ADAL userId for migration
    account = [application accountForUsername:@"adal.user.id" error:nil];
}
    
if (!account)
{
  // Account not found.
  return;
}

MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
        
let msalIdentifier: String?
var account: MSALAccount
        
do {
  if let msalIdentifier = msalIdentifier {
    account = try application.account(forIdentifier: msalIdentifier)
  }
  else {
    account = try application.account(forUsername: "adal.user.id") 
  }
             
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)          
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result
  }  
} catch let error as NSError {
  // handle error or account not found
}
```



MSAL でサポートされているアカウント参照 API:

```objc
/*!
 Returns account for the given account identifier (received from an account object returned in a previous acquireToken call)
 
 @param  error      The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
 */
- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier
                                         error:(NSError * _Nullable __autoreleasing * _Nullable)error;
    
/*!
Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)
    
@param  username    The displayable value in UserPrincipleName(UPN) format
@param  error       The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
*/
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

## <a name="adal-2x-266"></a>ADAL 2.x - 2.6.6

このセクションでは、MSAL と ADAL 2.x - 2.6.6 の SSO の違いについて説明します。

過去のバージョンの ADAL では、MSAL のキャッシュ形式はネイティブにサポートされていません。 ただし、ADAL から MSAL への移行をスムーズに進められるように、MSAL ではユーザー資格情報の再入力を求めることなく古い ADAL キャッシュ形式を読み取ることができます。

過去のバージョンの ADAL では `homeAccountId` を利用できないので、`username` を使用してアカウントを参照する必要があります。

```objc
/*!
 Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)

 @param  username    The displayable value in UserPrincipleName(UPN) format
 @param  error       The error that occurred trying to get the accounts, if any.  If you're not interested in the specific error pass in nil.
 */
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

次に例を示します。

Objective-C:


```objc
MSALAccount *account = [application accountForUsername:@"adal.user.id" error:nil];;
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
do {
  let account = try application.account(forUsername: "adal.user.id")          
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)
  application.acquireTokenSilent(with: silentParameters) { 
    (result: MSALResult?, error: Error?) in
    // handle result
  }   
} catch let error as NSError { 
  // handle error or account not found
}
```



または、すべてのアカウントを読み取ることができます。この場合、ADAL からのアカウント情報も読み取ることになります。

Objective-C:

```objc
NSArray *accounts = [application allAccounts:nil];
    
if ([accounts count] == 0)
{
  // No account found.
  return; 
}
if ([accounts count] > 1)
{
  // You might want to display an account picker to user in actual application
  // For this sample we assume there's only ever one account in cache
  return;
}
    ``
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:accounts[0]];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
      
do {
  let accounts = try application.allAccounts()
  if accounts.count == 0 {
    // No account found.
    return
  }
  if accounts.count > 1 {
    // You might want to display an account picker to user in actual application
    // For this sample we assume there's only ever one account in cache
    return
  }
  
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: accounts[0])
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result or error  
  }  
} catch let error as NSError { 
  // handle error
}
```



## <a name="next-steps"></a>次のステップ

[認証フローとアプリケーションのシナリオ](authentication-flows-app-scenarios.md)の詳細を確認します
