---
title: 承認エージェントとそれらを有効にする方法 | Azure
description: Microsoft Authentication Library (MSAL) によって Android アプリで使用できるさまざまな承認エージェントと、それらを有効にする方法について説明します。
services: active-directory
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/05/2019
ms.author: twhitney
ms.reviewer: shoatman, brianmel, hahamil
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44a0e93c87c50fbc63cddad349ec9b5fbf45d91f
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74843905"
---
# <a name="authorization-agents-android"></a>承認エージェント (Android)

この記事では、Microsoft Authentication Library (MSAL) によってアプリで使用できるさまざまな承認エージェントと、それらを有効にする方法について説明します。

承認エージェントの特定の戦略を選択することは任意であり、アプリでカスタマイズできる追加機能を表します。 ほとんどのアプリでは、MSAL の既定値が使用されます (さまざまな既定値を確認するには、「[Android Microsoft Authentication Library (MSAL) 構成ファイルについて](msal-configuration.md)」を参照してください)。

MSAL では、`WebView` またはシステム ブラウザーを使用した承認がサポートされています。  次の図は、`WebView`、CustomTab 付きのシステム ブラウザー、または CustomTab なしのシステム ブラウザーを使用した認証を示しています。

![MSAL ログインの例](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>シングル サインインの影響

既定では、MSAL と統合されたアプリケーションは、システム ブラウザーのカスタム タブを使用して承認が行われます。 WebView とは異なり、カスタム タブでは cookie jar が既定のシステム ブラウザーと共有されるため、カスタム タブと統合された Web またはその他のネイティブ アプリでのサインインが少なくなります。

アプリケーションで Microsoft Authenticator またはポータル サイト サポートをアプリに統合せずに `WebView` の戦略を使用している場合、ユーザーは、デバイス全体またはネイティブアプリと Web アプリの間でシングル サインオン (SSO) を利用できません。

アプリケーションで Microsoft Authenticator またはポータル サイト サポートを使用した MSAL を使用している場合、ユーザーがいずれかのアプリでアクティブ サインインを持っていれば、アプリケーション間で SSO を利用できます。

## <a name="webview"></a>WebView

アプリ内 WebView を使用するには、MSAL に渡されるアプリ構成 JSON に次の行を追加します。

```json
"authorization_user_agent" : "WEBVIEW"
```

アプリ内 `WebView` を使用すると、ユーザーはアプリに直接サインインします。 トークンはアプリのサンドボックス内に保持され、アプリの cookie jar の外部では使用できません。 そのため、アプリが Authenticator またはポータル サイトと統合されていない限り、ユーザーはアプリケーション間で SSO を利用できません。

ただし、`WebView` には、サインイン UI の外観をカスタマイズする機能が用意されています。 このカスタマイズを行う方法の詳細については、「[Android WebViews](https://developer.android.com/reference/android/webkit/WebView)」(Android の WebView) を参照してください。

## <a name="default-browser-plus-custom-tabs"></a>既定のブラウザーおよびカスタム タブ

既定では、MSAL ではブラウザーおよび[カスタム タブ](https://developer.chrome.com/multidevice/android/customtabs)戦略が使用されます。 カスタム構成ファイルで次の JSON 構成を使用して、この方法を明示的に指定することで、今後のリリースが `DEFAULT` に変更されないようにすることができます。

```json
"authorization_user_agent" : "BROWSER"
```

この方法を使用すると、デバイスのブラウザーで SSO を利用できるようになります。 MSAL では共有 cookie jar が使用されます。これにより、他のネイティブ アプリまたは Web アプリでは、MSAL によって設定された永続セッション cookie を使用して、デバイスで SSO を実現できます。

## <a name="browser-selection-heuristic"></a>ブラウザー選択ヒューリスティック

MSAL では、さまざまな Android フォンで使用するブラウザー パッケージを厳密には指定できないため、最適なクロスデバイス SSO を提供しようとするブラウザー選択ヒューリスティックが実装されています。

MSAL では、デバイスにインストールされているブラウザーの完全なリストを取得して、使用するブラウザーが選択されます。 このリストは、パッケージ マネージャーによって返された順序になっており、ユーザーの設定が間接的に反映されています。 たとえば、既定のブラウザー (設定されている場合) がリストの最初のエントリになります。 カスタム タブがサポートされているかどうかに関係なく、リスト内の _最初の_ ブラウザーが選択されます。 ブラウザーでカスタム タブがサポートされている場合、MSAL ではカスタムタブが起動されます。カスタム タブは、アプリ内 `WebView` に近い外観を持ち、基本的な UI カスタマイズが可能です。 詳細については、「[Custom Tabs in Android](https://developer.chrome.com/multidevice/android/customtabs)」(Android のカスタム タブ) を参照してください。

デバイスにブラウザー パッケージがない場合、MSAL ではアプリ内 `WebView` が使用されます。

ブラウザー リスト内のブラウザーの順序は、オペレーティング システムによって決まります。 優先順位の高い順になっています。 デバイスの既定の設定が変更されていない場合は、サインインごとに同じブラウザーを起動して、SSO エクスペリエンスを確実にする必要があります。

> [!NOTE]
> 別のブラウザーが既定として設定されている場合、MSAL では Chrome が優先されないこともあります。 たとえば、Chrome と別のブラウザーの両方が事前にインストールされているデバイスの場合、MSAL では、ユーザーが既定として設定したブラウザーが使用されます。

### <a name="tested-browsers"></a>テスト済みのブラウザー

次のブラウザーは、構成ファイルで指定されている `"redirect_uri"` に正しくリダイレクトされるかどうかがテスト済みです。

| | 組み込みブラウザー | Chrome | Opera  | Microsoft Edge | UC ブラウザー | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | 合格 | 合格 |適用外 |適用外 |適用外 |適用外 |
| Samsung S7 (API 25) | 合格* | 合格 | 合格 | 合格 | 不合格 |合格 |
| Huawei (API 26) |合格** | 合格 | 不合格 | 合格 | 合格 |合格 |
| Vivo (API 26) |合格|合格|合格|合格|合格|不合格|
| Pixel 2 (API 26) |合格 | 合格 | 合格 | 合格 | 不合格 |合格 |
| Oppo | 合格 | 適用外*** |適用外  |適用外 |適用外 | 適用外|
| OnePlus (API 25) |合格 | 合格 | 合格 | 合格 | 不合格 |合格 |
| Nexus (API 28) |合格 | 合格 | 合格 | 合格 | 不合格 |合格 |
|MI | 合格 | 合格 | 合格 | 合格 | 不合格 |合格 |

*Samsung の組み込みブラウザーは Samsung Internet です。  
**Huawei の組み込みブラウザーは Huawei Browser です。  
***Oppo デバイス設定内では、既定のブラウザーを変更できません。
