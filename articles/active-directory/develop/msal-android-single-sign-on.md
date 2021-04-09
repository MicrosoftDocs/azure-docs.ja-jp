---
title: Android で MSAL を使用してクロスアプリ SSO を有効にする方法 | Azure
titleSuffix: Microsoft identity platform
description: Android 用の Microsoft Authentication Library (MSAL) を使用して、アプリケーション間のシングル サインオンを有効にする方法について説明します。
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: how-to
ms.date: 10/15/2020
ms.author: hahamil
ms.reviewer: marsma
ms.openlocfilehash: f3d4ec8db89e9bebfdcd594e842a6c19d3d66d54
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100104095"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-msal"></a>方法:Android で MSAL を使用してクロスアプリ SSO を有効にする

シングル サインオン (SSO) を使用すると、ユーザーは資格情報を一度入力するだけで、その資格情報をアプリケーション間で自動的に機能させることができます。

[Microsoft ID プラットフォーム](./index.yml)と Microsoft Authentication Library (MSAL) は、アプリのスイート間で SSO を有効にするのに役立ちます。 ブローカー機能と Authenticator アプリケーションを使用することで、デバイス全体に SSO を拡張できます。

この攻略ガイドでは、SSO をユーザーに提供するためにアプリケーションで使用される SDK を構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

この攻略ガイドでは、次を行う方法がわかっていることを前提としています。

- Azure portal を使用してアプリをプロビジョニングする。 このトピックの詳細については、[Android チュートリアル](./tutorial-v2-android.md#create-a-project)のアプリ作成手順を参照してください。
- アプリケーションを Android 用の [Microsoft Authentication Library](https://github.com/AzureAD/microsoft-authentication-library-for-android) と統合する。

## <a name="methods-for-single-sign-on"></a>シングル サインオンのメソッド

アプリケーションで Android 用 MSAL を使用して SSO を実現するには、次の 2 つの方法があります。

* [ブローカー アプリケーションを使用する](#sso-through-brokered-authentication)
* [システム ブラウザーを使用する](#sso-through-system-browser)


   デバイス全体の SSO、アカウント管理、条件付きアクセスなどの利点を得るために、ブローカー アプリケーションを使用することをお勧めします。 ただし、ユーザーは追加のアプリケーションをダウンロードする必要があります。

## <a name="sso-through-brokered-authentication"></a>ブローカー認証による SSO

Microsoft の認証ブローカーの 1 つを使用することで、デバイス全体のシングル サインオン (SSO) に参加し、組織の条件付きアクセス ポリシーを満たすことをお勧めします。 ブローカーとの統合には、次の利点があります。

- デバイスのシングル サインオン
- 以下に関する条件付きアクセス:
  - Intune App Protection
  - Device Registration (Workplace Join)
  - モバイル デバイス管理
- デバイス全体のアカウント管理
  -  Android AccountManager およびアカウント設定を使用
  - "職場アカウント" - カスタム アカウントの種類

Android では、Microsoft の認証ブローカーは、[Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) および [Intune ポータル サイト](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) アプリに含まれるコンポーネントです。

次の図は、アプリ、Microsoft Authentication Library (MSAL)、Microsoft の認証ブローカーの間の関係を示しています。

![アプリケーションが MSAL、ブローカー アプリ、および Android アカウント マネージャーとどのように関連しているかを示す図。](./media/brokered-auth/brokered-deployment-diagram.png)

### <a name="installing-apps-that-host-a-broker"></a>ブローカーをホストするアプリのインストール

ブローカーをホストするアプリは、アプリ ストア (通常は Google Play ストア) からデバイス所有者がいつでもインストールできます。 ただし、一部の API (リソース) は条件付きアクセス ポリシーによって保護されており、そのためデバイスを次のようにする必要があります。

- 登録済み (ワークプレースに参加済み)、および/または
- デバイス管理に登録済、または
- Intune App Protection に登録済

そのアプリで対話形式でのトークンの取得が試みられるとすぐに、MSAL によって、デバイスにブローカー アプリがまだインストールされていない場合はインストールするようにユーザーに対して指示されます。 その後、そのアプリで、必要なポリシーにデバイスを準拠させるための手順をユーザーに案内する必要があります。

### <a name="effects-of-installing-and-uninstalling-a-broker"></a>ブローカーのインストールとアンインストールの影響

#### <a name="when-a-broker-is-installed"></a>ブローカーがインストールされた場合

ブローカーがデバイスにインストールされると、以降のすべての対話型トークン要求 (`acquireToken()` の呼び出し) は、MSAL によってローカルに処理されるのではなく、ブローカーによって処理されます。 以前に MSAL で使用できた SSO の状態は、ブローカーでは使用できません。 その結果として、ユーザーはもう一度認証を行うか、デバイスで認識されている既存のアカウントの一覧からアカウントを選択する必要があります。

ブローカーをインストールする場合、ユーザーは再度サインインする必要はありません。 ユーザーが `MsalUiRequiredException` を解決する必要がある場合にのみ、次の要求がブローカーに送られます。 `MsalUiRequiredException` はさまざまな理由でスローでき、対話形式で解決する必要があります。 次に例を示します。

- ユーザーがアカウントに関連付けられているパスワードを変更した。
- ユーザーのアカウントが条件付きアクセス ポリシーに適合しなくなった。
- ユーザーがアプリをアカウントに関連付ける同意を取り消した。

**複数のブローカー** - デバイスに複数のブローカーがインストールされている場合、最初にインストールされたブローカーが常にアクティブなブローカーです。 デバイス上でアクティブにできるブローカーは 1 つだけです。

#### <a name="when-a-broker-is-uninstalled"></a>ブローカーがアンインストールされた場合

ブローカーをホストするアプリが 1 つだけインストールされていて、それが削除された場合、ユーザーはもう一度サインインする必要があります。 アクティブなブローカーをアンインストールすると、そのアカウントと、関連付けられているトークンがデバイスから削除されます。

Intune ポータル サイトがインストールされていて、アクティブなブローカーとして動作しており、Microsoft Authenticator もインストールされている場合、Intune ポータル サイト (アクティブなブローカー) がアンインストールされると、ユーザーはもう一度サインインする必要があります。 もう一度サインインすると、Microsoft Authenticator アプリがアクティブなブローカーになります。

### <a name="integrating-with-a-broker"></a>ブローカーとの統合

#### <a name="generate-a-redirect-uri-for-a-broker"></a>ブローカーのリダイレクト URI を生成する

ブローカーと互換性のあるリダイレクト URI を登録する必要があります。 ブローカーのリダイレクト URI には、アプリのパッケージ名と、base64 エンコードで表されたアプリの署名が含まれている必要があります。

リダイレクト URI の形式は次のとおりです。`msauth://<yourpackagename>/<base64urlencodedsignature>`

[keytool](https://manpages.debian.org/buster/openjdk-11-jre-headless/keytool.1.en.html) を使用して、アプリの署名キーを使用し Base64 でエンコードされた署名ハッシュを生成し、その後 Azure portal を使用して、そのハッシュを使用してリダイレクト URI を生成することができます。

Linux および macOS:

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

Windows:

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

*keytool* で署名ハッシュを生成したら、Azure portal を使用してリダイレクト URI を生成します。

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインし、 **[アプリの登録]** で Android アプリを選択します。
1. **[認証]**  >  **[プラットフォームを追加]**  >  **[Android]** を選択します。
1. **[お使いの Android アプリを構成する]** ウィンドウが開いたら、先ほど生成した **署名ハッシュ** と **パッケージ名** を入力します。
1. **[構成]** ボタンを選択します。

Azure portal によってリダイレクト URI が生成され、 **[Android configuration]\(Android 構成\)** ウィンドウの **[リダイレクト URI]** フィールドに表示されます。

アプリに署名する方法の詳細については、Android Studio ユーザーガイドの「[アプリへの署名](https://developer.android.com/studio/publish/app-signing)」を参照してください。

> [!IMPORTANT]
> 運用バージョンのアプリには、実稼働署名キーを使用します。

#### <a name="configure-msal-to-use-a-broker"></a>ブローカーを使用するように MSAL を構成する

ご自身のアプリでブローカーを使用するには、ブローカー リダイレクトを構成済みであることを証明する必要があります。 たとえば、MSAL 構成ファイルに以下を含めることで、ブローカー対応リダイレクト URI を含めることと、それを登録したことの両方を示します。

```json
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

#### <a name="broker-related-exceptions"></a>ブローカー関連の例外

MSAL とブローカーの通信は次の 2 つの方法で行われます。

- ブローカー バインド サービス
- Android AccountManager

MSAL では最初にブローカー バインド サービスが使用されます。このサービスを呼び出す場合、Android のアクセス許可は必要ないためです。 バインドされたサービスへのバインドが失敗した場合、MSAL では Android AccountManager API が使用されます。 MSAL でこれが行われるのは、アプリに既に `"READ_CONTACTS"` のアクセス許可が付与されている場合のみです。

エラー コード `"BROKER_BIND_FAILURE"` の `MsalClientException` が返された場合は、次の 2 つのオプションがあります。

- Microsoft Authenticator アプリと Intune ポータル サイトの電力の最適化を無効にするようにユーザーに依頼します。
- `"READ_CONTACTS"` のアクセス許可を付与するようユーザーに依頼します。

### <a name="verify-broker-integration"></a>ブローカーの統合の検証

ブローカーの統合が機能しているかどうかはすぐにはわかりませんが、次の手順を使用して確認できます。

1. Android デバイスで、ブローカーを使用して要求を完了します。
1. Android デバイスの設定で、認証に使用したアカウントに対応する新しく作成されたアカウントを探します。 アカウントの種類は *職場アカウント* である必要があります。

テストを繰り返す場合は、設定からアカウントを削除することができます。

## <a name="sso-through-system-browser"></a>システム ブラウザーを使用した SSO

Android アプリケーションでは、認証ユーザー エクスペリエンスに WebView、システム ブラウザー、または Chrome カスタム タブの使用を選択できます。 アプリケーションでブローカー認証が使用されていない場合は、SSO を実現するためにネイティブ Web ビューではなくシステム ブラウザーを使用する必要があります。

### <a name="authorization-agents"></a>承認エージェント

承認エージェントの特定の戦略を選択することは任意であり、アプリでカスタマイズできる追加機能を表します。 ほとんどのアプリでは、MSAL の既定値が使用されます (さまざまな既定値を確認するには、[Android MSAL の構成ファイルについての理解](msal-configuration.md)に関するページを参照してください)。

MSAL では、`WebView` またはシステム ブラウザーを使用した承認がサポートされています。  次の図は、`WebView`、CustomTab 付きのシステム ブラウザー、または CustomTab なしのシステム ブラウザーを使用した認証を示しています。

![MSAL ログインの例](./media/authorization-agents/sign-in-ui.jpg)

### <a name="single-sign-on-implications"></a>シングル サインオンによる影響

既定では、MSAL と統合されたアプリケーションは、システム ブラウザーのカスタム タブを使用して承認が行われます。 WebView とは異なり、カスタム タブでは cookie jar が既定のシステム ブラウザーと共有されるため、カスタム タブと統合された Web またはその他のネイティブ アプリでのサインインが少なくなります。

アプリケーションで Microsoft Authenticator またはポータル サイト サポートをアプリに統合せずに `WebView` の戦略を使用している場合、ユーザーは、デバイス全体またはネイティブ アプリと Web アプリの間でシングル サインオン エクスペリエンスを利用できません。

アプリケーションで Microsoft Authenticator や Intune ポータル サイトなどのブローカーを使用した MSAL を使用している場合、ユーザーがいずれかのアプリでアクティブ サインインを持っていれば、アプリケーション間で SSO を利用できます。

### <a name="webview"></a>WebView

アプリ内 WebView を使用するには、MSAL に渡されるアプリ構成 JSON に次の行を追加します。

```json
"authorization_user_agent" : "WEBVIEW"
```

アプリ内 `WebView` を使用すると、ユーザーはアプリに直接サインインします。 トークンはアプリのサンドボックス内に保持され、アプリの cookie jar の外部では使用できません。 そのため、アプリが Authenticator またはポータル サイトと統合されていない限り、ユーザーはアプリケーション間で SSO を利用できません。

ただし、`WebView` には、サインイン UI の外観をカスタマイズする機能が用意されています。 このカスタマイズを行う方法の詳細については、「[Android WebViews](https://developer.android.com/reference/android/webkit/WebView)」(Android の WebView) を参照してください。

### <a name="default-browser-plus-custom-tabs"></a>既定のブラウザーおよびカスタム タブ

既定では、MSAL ではブラウザーおよび[カスタム タブ](https://developer.chrome.com/multidevice/android/customtabs)戦略が使用されます。 カスタム構成ファイルで次の JSON 構成を使用して、この方法を明示的に指定することで、今後のリリースが `DEFAULT` に変更されないようにすることができます。

```json
"authorization_user_agent" : "BROWSER"
```

この方法を使用すると、デバイスのブラウザーで SSO を利用できるようになります。 MSAL では共有 cookie jar が使用されます。これにより、他のネイティブ アプリまたは Web アプリでは、MSAL によって設定された永続セッション cookie を使用して、デバイスで SSO を実現できます。

### <a name="browser-selection-heuristic"></a>ブラウザー選択ヒューリスティック

MSAL では、さまざまな Android フォンで使用するブラウザー パッケージを厳密には指定できないため、最適なクロスデバイス SSO を提供しようとするブラウザー選択ヒューリスティックが実装されています。

MSAL では、初めにパッケージ マネージャーから既定のブラウザーを取得し、それがテスト済みの安全なブラウザーの一覧にあるかどうかを確認します。 ない場合、MSAL によってセーフ リストから別の既定以外のブラウザーが起動されるのではなく、Webview を使用するようにフォール バックされます。 カスタム タブがサポートされているかどうかに関係なく、既定のブラウザーが選択されます。 ブラウザーでカスタム タブがサポートされている場合、MSAL ではカスタムタブが起動されます。カスタム タブは、アプリ内 `WebView` に近い外観を持ち、基本的な UI カスタマイズが可能です。 詳細については、「[Custom Tabs in Android](https://developer.chrome.com/multidevice/android/customtabs)」(Android のカスタム タブ) を参照してください。

デバイスにブラウザー パッケージがない場合、MSAL ではアプリ内 `WebView` が使用されます。 デバイスの既定の設定が変更されていない場合は、サインインごとに同じブラウザーを起動して、SSO エクスペリエンスを確実にする必要があります。

#### <a name="tested-browsers"></a>テスト済みのブラウザー

次のブラウザーは、構成ファイルで指定されている `"redirect_uri"` に正しくリダイレクトされるかどうかがテスト済みです。

| Device | 組み込みブラウザー | Chrome | Opera  | Microsoft Edge | UC ブラウザー | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | 合格 | 合格 |適用外 |適用外 |適用外 |適用外 |
| Samsung S7 (API 25) | 合格<sup>1</sup> | 合格 | 合格 | 合格 | 不合格 |合格 |
| Huawei (API 26) |合格<sup>2</sup> | 合格 | 不合格 | 合格 | 合格 |合格 |
| Vivo (API 26) |合格|合格|合格|合格|合格|不合格|
| Pixel 2 (API 26) |合格 | 合格 | 合格 | 合格 | 不合格 |合格 |
| Oppo | 合格 | 適用外<sup>3</sup>|適用外  |適用外 |適用外 | 適用外|
| OnePlus (API 25) |合格 | 合格 | 合格 | 合格 | 不合格 |合格 |
| Nexus (API 28) |合格 | 合格 | 合格 | 合格 | 不合格 |合格 |
|MI | 合格 | 合格 | 合格 | 合格 | 不合格 |合格 |

<sup>1</sup>Samsung の組み込みブラウザーは Samsung Internet です。<br/>
<sup>2</sup>Huawei の組み込みブラウザーは Huawei Browser です。<br/>
<sup>3</sup>Oppo デバイス設定内では、既定のブラウザーを変更できません。

## <a name="next-steps"></a>次の手順

[Android デバイス向け共有デバイス モード](msal-android-shared-devices.md)を使用すると、Android デバイスを複数の従業員で容易に共有できるように構成できます。