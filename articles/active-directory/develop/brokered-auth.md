---
title: Android のブローカー認証 | Azure
titlesuffix: Microsoft identity platform
description: Microsoft ID プラットフォームでの Android に対する仲介型認証と承認の概要
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman, hahamil, brianmel
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5204ad71efa2587341600d2c5c1e5195d15445e
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74843718"
---
# <a name="brokered-authentication-in-android"></a>Android のブローカー認証

Microsoft の認証ブローカーの 1 つを使用して、デバイス全体のシングル サインオン (SSO) に参加し、組織の条件付きアクセス ポリシーを満たす必要があります。 ブローカーとの統合には、次の利点があります。

- デバイスのシングル サインオン
- 以下に関する条件付きアクセス:
  - Intune App Protection
  - Device Registration (Workplace Join)
  - モバイル デバイス管理
- デバイス全体のアカウント管理
  -  Android AccountManager およびアカウント設定を使用
  - "職場アカウント" - カスタム アカウントの種類

Android では、Microsoft の認証ブローカーは、[Microsoft Authenticator アプリ](https://play.google.com/store/apps/details?id=com.azure.authenticator)および [Intune ポータル サイト](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)に含まれるコンポーネント

> [!TIP]
> ブローカーをホストするアプリケーションは、一度に 1 つだけがブローカーとしてアクティブになります。 どのアプリケーションがブローカーとしてアクティブになるかは、デバイスへのインストール順によって決まります。 最初にインストールされるもの、またはデバイスに最後に存在するものがアクティブなブローカーになります。

次の図は、アプリ、Microsoft Authentication Library (MSAL)、Microsoft の認証ブローカーの間の関係を示しています。

![ブローカーのデプロイ図](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>ブローカーをホストするアプリのインストール

ブローカーをホストするアプリは、アプリ ストア (通常は Google Play ストア) からデバイス所有者がいつでもインストールできます。 ただし、一部の API (リソース) は条件付きアクセス ポリシーによって保護されており、そのためデバイスを次のようにする必要があります。

- 登録済み (ワークプレースに参加済み)、および/または
- デバイス管理に登録済、または
- Intune App Protection に登録済

そのアプリで対話形式でのトークンの取得が試みられるとすぐに、MSAL によって、デバイスにブローカー アプリがまだインストールされていない場合はインストールするようにユーザーに対して指示されます。 その後、そのアプリで、必要なポリシーにデバイスを準拠させるための手順をユーザーに案内する必要があります。

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>ブローカーのインストールとアンインストールの影響

### <a name="when-a-broker-is-installed"></a>ブローカーがインストールされた場合

ブローカーがデバイスにインストールされると、以降のすべての対話型トークン要求 (`acquireToken()` の呼び出し) は、MSAL によってローカルに処理されるのではなく、ブローカーによって処理されます。 以前に MSAL で使用できた SSO の状態は、ブローカーでは使用できません。 その結果として、ユーザーはもう一度認証を行うか、デバイスで認識されている既存のアカウントの一覧からアカウントを選択する必要があります。

ブローカーをインストールする場合、ユーザーは再度サインインする必要はありません。 ユーザーが `MsalUiRequiredException` を解決する必要がある場合にのみ、次の要求がブローカーに送られます。 `MsalUiRequiredException` はさまざまな理由でスローされ、対話形式で解決する必要があります。 一般的な理由は次のとおりです。

- ユーザーがアカウントに関連付けられているパスワードを変更した。
- ユーザーのアカウントが条件付きアクセス ポリシーに適合しなくなった。
- ユーザーがアプリをアカウントに関連付ける同意を取り消した。

### <a name="when-a-broker-is-uninstalled"></a>ブローカーがアンインストールされた場合

ブローカーをホストするアプリが 1 つだけインストールされていて、それが削除された場合、ユーザーはもう一度サインインする必要があります。 アクティブなブローカーをアンインストールすると、そのアカウントと、関連付けられているトークンがデバイスから削除されます。

Intune ポータル サイトがインストールされていて、アクティブなブローカーとして動作しており、Microsoft Authenticator もインストールされている場合、Intune ポータル サイト (アクティブなブローカー) がアンインストールされると、ユーザーはもう一度サインインする必要があります。 もう一度サインインすると、Microsoft Authenticator アプリがアクティブなブローカーになります。

## <a name="integrating-with-a-broker"></a>ブローカーとの統合

### <a name="generating-a-redirect-uri-for-a-broker"></a>ブローカーのリダイレクト URI を生成する

ブローカーと互換性のあるリダイレクト URI を登録する必要があります。 ブローカーのリダイレクト URI には、アプリのパッケージ名と、base64 エンコードで表されたアプリの署名が含まれている必要があります。

リダイレクト URI の形式は次のとおりです。`msauth://<yourpackagename>/<base64urlencodedsignature>`

アプリの署名キーを使用して、Base64 URL エンコードの署名を生成します。 デバッグ署名キーを使用するコマンドの例を次に示します。

#### <a name="macos"></a>macOS

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

#### <a name="windows"></a>Windows

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

アプリへの署名の詳細については、「[アプリへの署名](https://developer.android.com/studio/publish/app-signing)」を参照してください。

> [!IMPORTANT]
> 運用バージョンのアプリには、実稼働署名キーを使用します。

### <a name="configure-msal-to-use-a-broker"></a>ブローカーを使用するように MSAL を構成する

ご自身のアプリでブローカーを使用するには、ブローカー リダイレクトを構成済みであることを証明する必要があります。 たとえば、MSAL 構成ファイルに以下を含めることで、ブローカー対応リダイレクト URI を含めることと、それを登録したことの両方を示します。

```javascript
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

> [!TIP]
> 新しい Azure portal アプリの登録 UI を使用すると、ブローカーのリダイレクト URI を生成するのに便利です。 アプリを登録するのに以前のエクスペリエンスを使用した場合、または Microsoft アプリ登録ポータルを使用した場合は、必要に応じて、リダイレクト URI を生成し、ポータル上でリダイレクト URI の一覧を手動で更新します。

### <a name="broker-related-exceptions"></a>ブローカー関連の例外

MSAL とブローカーの通信は次の 2 つの方法で行われます。

- ブローカー バインド サービス
- Android AccountManager

MSAL では最初にブローカー バインド サービスが使用されます。このサービスを呼び出す場合、Android のアクセス許可は必要ないためです。 バインドされたサービスへのバインドが失敗した場合、MSAL では Android AccountManager API が使用されます。 MSAL でこれが行われるのは、アプリに既に `"READ_CONTACTS"` のアクセス許可が付与されている場合のみです。

エラー コード `"BROKER_BIND_FAILURE"` の `MsalClientException` が返された場合は、次の 2 つのオプションがあります。

- Microsoft Authenticator アプリと Intune ポータル サイトの電力の最適化を無効にするようにユーザーに依頼します。
- `"READ_CONTACTS"` のアクセス許可を付与するようユーザーに依頼します。
