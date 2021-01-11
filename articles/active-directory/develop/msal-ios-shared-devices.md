---
title: iOS デバイスの共有デバイス モード
titleSuffix: Microsoft identity platform | Azure
description: Firstline Worker (現場の最前線で働く従業員) が iOS デバイスを共有できるようにする共有デバイス モードを有効にする方法について説明します
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 5abb6852e267fa7d7277e0e0674a4099343e14ca
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88114916"
---
# <a name="shared-device-mode-for-ios-devices"></a>iOS デバイスの共有デバイス モード

> [!NOTE]
> この機能はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

小売店員、航空機乗組員、フィールド サービス ワーカーなどの Firstline Worker (現場の最前線で働く従業員) は多くの場合、共有モバイル デバイスを使用して作業を行います。 これらの共有デバイスでは、ユーザーが意図的か否かにかかわらず、パスワードや PIN を共有して、共有デバイス上の顧客データやビジネス データにアクセスする場合に、セキュリティ上のリスクをもたらす可能性があります。

共有デバイス モードを使用すると、iOS 13 以上のデバイスを従業員で、より容易にかつ安全に共有できるようになります。 従業員はサインインし、顧客情報にすばやくアクセスできます。 自分のシフトまたはタスクが完了したら、デバイスからサインアウトでき、そのデバイスは直ちに次の従業員が使用できるよう準備が整います。

共有デバイス モードでは、Microsoft ID に基づくデバイスの管理も提供されます。

この機能では、[Microsoft Authenticator アプリ](../user-help/user-help-auth-app-overview.md)を使用して、デバイス上のユーザーを管理し、[Apple デバイス用の Microsoft Enterprise SSO プラグイン](apple-sso-plugin.md)を配布します。

## <a name="create-a-shared-device-mode-app"></a>共有デバイス モード アプリを作成する

共有デバイス モード アプリを作成するには、開発者とクラウド デバイス管理者が協力して作業します。

1. **アプリケーション開発者**は、単一アカウント アプリ (共有デバイス モードでは複数アカウント アプリはサポートされていません) を記述し、共有デバイスのサインアウトなどを処理するためのコードを記述します。

1. **デバイス管理者**は、組織内のデバイスを管理するために Microsoft Intune のようなモバイル デバイス管理 (MDM) プロバイダーを使用して、デバイスの共有を準備します。 MDM は、デバイスに Microsoft Authenticator アプリをプッシュし、デバイスのプロファイルの更新を通じて、各デバイスの "共有モード" をオンにします。 この共有モード設定は、デバイスでサポートされているアプリの動作を変更するものです。 MDM プロバイダーからのこの構成によって、デバイスの共有デバイス モードを設定し、共有デバイス モードで必要とされる [Apple デバイス用の Microsoft Enterprise SSO プラグイン](apple-sso-plugin.md)を有効にします。

1. **[パブリック プレビュー中にのみ必要]** [クラウド デバイス管理者](../users-groups-roles/directory-assign-admin-roles.md#cloud-device-administrator) ロールを持つユーザーは、[Microsoft Authenticator アプリ](../user-help/user-help-auth-app-overview.md)を起動し、デバイスを組織に参加させる必要があります。

    Azure portal で組織ロールのメンバーシップを構成するには: **[Azure Active Directory]**  >  **[ロールと管理者]**  >  **[クラウド デバイス管理者]**

次のセクションは、共有デバイス モードをサポートするようにアプリケーションを更新するのに役立ちます。

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>Intune を使用して共有デバイス モードと SSO 拡張を有効にする

> [!NOTE]
> 次の手順は、パブリック プレビュー中にのみ必要です。

デバイスは、共有デバイス モードをサポートするように構成されている必要があります。 iOS 13 以降がインストールされ、MDM に登録されている必要があります。 また、MDM 構成で [Apple デバイス用の Microsoft Enterprise SSO プラグイン](apple-sso-plugin.md)を有効にする必要もあります。 SSO 拡張の詳細については、[Apple ビデオ](https://developer.apple.com/videos/play/tech-talks/301/)を参照してください。

1. Intune 構成ポータルで、デバイスに次の構成で [Apple デバイス用の Microsoft Enterprise SSO プラグイン](apple-sso-plugin.md)を有効にするように指示します。

    - **[種類]** :リダイレクト
    - **拡張 ID**: com.microsoft.azureauthenticator.ssoextension
    - **チーム ID**:SGGM6D27TK
    - **URL**: https://login.microsoftonline.com
    - 構成する追加データ:
      - キー: sharedDeviceMode
      - 型: Boolean
      - 値:True

    Intune による構成の詳細については、[Intune の構成に関するドキュメント](/intune/configuration/ios-device-features-settings)を参照してください。

1. 次に、MDM プロファイルによってデバイスに Microsoft Authenticator アプリをプッシュするように MDM を構成します。

    次の構成オプションを設定して、共有デバイス モードを有効にします。

    - 構成 1:
      - キー: sharedDeviceMode
      - 型: Boolean
      - 値:True

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>共有デバイス モードをサポートするように iOS アプリケーションを変更する

ユーザーはデータが他のユーザーに漏洩することがないように、管理者に依存しています。 次のセクションでは、アプリケーションに、変更が発生し、処理する必要があることを示すために役立つシグナルを提供します。

管理者は、アプリが使用されるたびにデバイス上のユーザーの状態を確認し、以前のユーザーのデータをクリアする必要があります。 これには、マルチタスクの実行中にバックグラウンドから再読み込みするかどうかを含みます。

ユーザーの変更時に、前のユーザーのデータがクリアされ、アプリケーションで表示されるキャッシュされたデータがすべて削除されるようにする必要があります。 共有デバイス モードをサポートするようにアプリを更新したら、管理者と会社でセキュリティ レビュー プロセスを実施することを強くお勧めします。

### <a name="detect-shared-device-mode"></a>共有デバイス モードを検出する

共有デバイス モードの検出は、アプリケーションに重要です。 アプリケーションが共有デバイスで使用されている場合、多くのアプリケーションで、ユーザー エクスペリエンス (UX) を変更する必要があります。 たとえば、アプリケーションに "サインアップ" 機能がある場合があります。これは、Firstline Worker (現場の最前線で働く従業員) は、既にアカウントを持っている可能性があるため、適していません。 また、共有デバイス モードになっている場合は、アプリケーションのデータ処理に特別なセキュリティを追加することもできます。

`MSALPublicClientApplication` で `getDeviceInformationWithParameters:completionBlock:` API を使用すると、共有デバイス モードのデバイスでアプリが実行しているかどうかを特定できます。

次のコード スニペットは、`getDeviceInformationWithParameters:completionBlock:` API の使用例を示しています。

#### <a name="swift"></a>Swift

```swift
application.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

    guard let deviceInfo = deviceInformation else {
        return
    }

    let isSharedDevice = deviceInfo.deviceMode == .shared
    // Change your app UX if needed
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
[application getDeviceInformationWithParameters:nil
                                completionBlock:^(MSALDeviceInformation * _Nullable deviceInformation, NSError * _Nullable error)
{
    if (!deviceInformation)
    {
        return;
    }

    BOOL isSharedDevice = deviceInformation.deviceMode == MSALDeviceModeShared;
    // Change your app UX if needed
}];
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>サインインしているユーザーを取得し、デバイスでユーザーが変更されたかどうかを特定する

共有デバイス モードをサポートするもう 1 つの重要な部分は、デバイス上のユーザーの状態を判断することと、ユーザーが変更された場合、またはデバイスにユーザーがいない場合に、アプリケーション データをクリアすることです。 管理者は、データが別のユーザーに漏洩されることがないようにする必要があります。

`getCurrentAccountWithParameters:completionBlock:` API を使用して、デバイス上の現在サインインしているアカウントを照会できます。

#### <a name="swift"></a>Swift

```swift
let msalParameters = MSALParameters()
msalParameters.completionBlockQueue = DispatchQueue.main

application.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALParameters *parameters = [MSALParameters new];
parameters.completionBlockQueue = dispatch_get_main_queue();

[application getCurrentAccountWithParameters:parameters
                             completionBlock:^(MSALAccount * _Nullable account, MSALAccount * _Nullable previousAccount, NSError * _Nullable error)
{
    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
}];
```

### <a name="globally-sign-in-a-user"></a>ユーザーをグローバルにサインインさせる

デバイスが共有デバイスとして構成されている場合、アプリケーションでは `acquireTokenWithParameters:completionBlock:` API を呼び出してアカウントをサインインできます。 最初のアプリがアカウントをサインインすると、デバイス上のすべての対象アプリで、アカウントがグローバルに利用できるようになります。

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>ユーザーをグローバルにサインアウトさせる

次のコードは、サインインしているアカウントを削除し、キャッシュされているトークンをアプリだけでなく共有デバイス モードのデバイスからもクリアします。 ただし、これによって、アプリケーションから*データ*はクリアされません。 アプリケーションからデータをクリアするだけでなく、アプリケーションでユーザーに表示している可能性があるキャッシュ データもクリアする必要があります。

#### <a name="clear-browser-state"></a>ブラウザーの状態をクリアする

> [!NOTE]
> 次の手順は、パブリック プレビュー中にのみ必要です。

このパブリック プレビュー バージョンでは、[Apple デバイス用の Microsoft Enterprise SSO プラグイン](apple-sso-plugin.md)によって、アプリケーションの状態のみがクリアされます。 これによって、Safari ブラウザーでは状態がクリアされません。 ユーザー状態のトレースが確実に残らないようにするために、ブラウザー セッションを手動でクリアすることをお勧めします。 次に示す省略可能な `signoutFromBrowser` プロパティを使用して、Cookie をクリアできます。 これにより、デバイスでブラウザーが短時間で起動するようになります。

#### <a name="swift"></a>Swift

```swift
let account = .... /* account retrieved above */

let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParamaters!)
signoutParameters.signoutFromBrowser = true // Only needed for Public Preview.

application.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

    if let error = error {
        // Signout failed
        return
    }

    // Sign out completed successfully
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALAccount *account = ... /* account retrieved above */;

MSALSignoutParameters *signoutParameters = [[MSALSignoutParameters alloc] initWithWebviewParameters:webViewParameters];
signoutParameters.signoutFromBrowser = YES; // Only needed for Public Preview.

[application signoutWithAccount:account signoutParameters:signoutParameters completionBlock:^(BOOL success, NSError * _Nullable error)
{
    if (!success)
    {
        // Signout failed
        return;
    }

    // Sign out completed successfully
}];
```

## <a name="next-steps"></a>次のステップ

共有デバイス モードの動作を確認するために、GitHub の次のコード サンプルに、共有デバイス モードのiOS デバイスで Firstline Worker アプリを実行する例が含まれています。

[MSAL iOS Swift Microsoft Graph API サンプル](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)