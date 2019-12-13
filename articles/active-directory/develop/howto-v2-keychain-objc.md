---
title: キーチェーンの構成
titleSuffix: Microsoft identity platform
description: キーチェーンを構成して、アプリでキーチェーン内のトークンをキャッシュできるようにする方法について説明します。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: oldalton
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b4599549e15d6ebe4d0bd04f96c89df86b0c0cd
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917507"
---
# <a name="configure-keychain"></a>キーチェーンの構成

[iOS および macOS 用の Microsoft Authentication Library](msal-overview.md) (MSAL) でユーザーのサインインまたはトークンの更新が行われるときに、キーチェーン内のトークンのキャッシュが試みられます。 キーチェーン内のトークンをキャッシュすることにより、MSAL は、同じ Apple 開発者によって配布された複数のアプリ間でサイレント シングル サインオン (SSO) を提供できます。 SSO は、キーチェーン アクセス グループ機能を介して実現されます。 詳細については、Apple の[キーチェーン項目に関する資料](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)を参照してください。

この記事では、MSAL がキャッシュされたトークンを iOS と macOS のキーチェーンに書き込むことができるように、アプリのエンタイトルメントを構成する方法について説明します。

## <a name="default-keychain-access-group"></a>既定のキーチェーン アクセス グループ

### <a name="ios"></a>iOS

iOS 上の MSAL では、`com.microsoft.adalcache` アクセス グループが既定で使用されます。 これは、MSAL と Azure AD Authentication Library (ADAL) SDK の両方で使用される共有アクセス グループであり、発行元が同じである複数のアプリ間での最適なシングル サインオン (SSO) エクスペリエンスが保証されます。

iOS では、XCode の **[Project settings]\(プロジェクト設定\)**  >  **[Capabilities]\(機能\)**  >  **[Keychain sharing]\(キーチェーン共有\)** で、`com.microsoft.adalcache` キーチェーン グループをアプリのエンタイトルメントに追加します。

### <a name="macos"></a>macOS

macOS 上の MSAL では、`com.microsoft.identity.universalstorage` アクセス グループが既定で使用されます。

macOS のキーチェーンの制限により、macOS 10.14 以前では、MSAL の `access group` はキーチェーン アクセス グループの属性に直接変換されません (「[kSecAttrAccessGroup](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc)」を参照してください)。 ただし、同じ Apple の開発者によって配布された複数のアプリケーションでサイレント SSO を可能にすることで、SSO の観点からは同様に動作します。

macOS 10.15 以降 (macOS Catalina) では、MSAL ではキーチェーン アクセス グループ属性の使用により、iOS と同様のサイレント SSO が実現されます。

## <a name="custom-keychain-access-group"></a>カスタム キーチェーン アクセス グループ

別のキーチェーン アクセス グループを使用する場合は、次のように、`MSALPublicClientApplication` の作成前に `MSALPublicClientApplicationConfig` を作成するときにカスタム グループを渡すことができます。

# <a name="objective-ctabobjc"></a>[Objective-C](#tab/objc)

```objc
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                                                            redirectUri:@"your-redirect-uri"
                                                                                              authority:nil];
    
config.cacheConfig.keychainSharingGroup = @"custom-group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:nil];
    
// Now call acquiretoken. 
// Tokens will be saved into the "custom-group" access group
// and only shared with other applications declaring the same access group
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: nil)
config.cacheConfig.keychainSharingGroup = "custom-group"
        
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```

---

## <a name="disable-keychain-sharing"></a>キーチェーンの共有を無効にする

複数のアプリ間で SSO 状態を共有したくない場合、または任意のキーチェーン アクセス グループを使用する場合は、keychainGroup としてアプリケーション バンドル ID を渡すことで、キーチェーンの共有を無効にします。

# <a name="objective-ctabobjc"></a>[Objective-C](#tab/objc)

```objc
config.cacheConfig.keychainSharingGroup = [[NSBundle mainBundle] bundleIdentifier];
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
if let bundleIdentifier = Bundle.main.bundleIdentifier {
    config.cacheConfig.keychainSharingGroup = bundleIdentifier
}
```

---

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>Handle -34018 エラー (キーチェーンへの項目の設定の失敗)

エラー -34018 は通常、キーチェーンが正しく構成されていないことを意味します。 MSAL で構成されているキーチェーン アクセス グループが、エンタイトルメントで構成されているものと一致していることを確認します。

## <a name="ensure-your-application-is-properly-signed"></a>アプリケーションが正しく署名されていることを確認する

macOS では、アプリケーションは開発者が署名していない場合でも実行できます。 MSAL の機能のほとんどは引き続き動作しますが、キーチェーン アクセスを使用した SSO では、アプリケーションへの署名が必要になります。 複数のキーチェーン プロンプトが発生している場合は、アプリケーションの署名が有効であることを確認します。

## <a name="next-steps"></a>次の手順

キーチェーン アクセス グループの詳細については、Apple の記事「[Sharing Access to Keychain Items Among a Collection of Apps](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)」を参照してください。
