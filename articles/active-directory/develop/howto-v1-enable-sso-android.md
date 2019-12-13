---
title: Android で ADAL を使用してクロス アプリ SSO を有効にする方法 | Microsoft Docs
description: ADAL SDK の機能を使用して、複数のアプリケーションでシングル サインオンを有効にする方法。
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 40710225-05ab-40a3-9aec-8b4e96b6b5e7
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4d247c569cdc0beff499cee191b95711a603e42
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917558"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>方法:Android で ADAL を使用してクロス アプリ SSO を有効にする

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

シングルサインオン (SSO) を使用すると、ユーザーは、資格情報を 1 回入力するだけで、それらの資格情報を自動的に、パブリッシャーに関係なく、アプリケーション間や他のアプリケーションが使用する可能性のあるプラットフォーム (Microsoft アカウントや Microsoft 365 の職場アカウントなど) 間で使用させることができます。

Microsoft の ID プラットフォームと SDK により、独自のアプリのスイート内や、ブローカー機能と Authenticator アプリケーションを使用してデバイス全体で、SSO を簡単に有効にすることができます。

この手順では、SSO をユーザーに提供するためにアプリケーション内で SDK を構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

この方法では、次の操作方法がわかっていることを前提としています。

- Azure Active Directory (Azure AD) の以前のポータルを使用してアプリをプロビジョニングする。 詳細については、[アプリの登録](quickstart-register-app.md)に関するページを参照してください
- アプリケーションを [Azure AD Android SDK](https://github.com/AzureAD/azure-activedirectory-library-for-android) に統合する。

## <a name="single-sign-on-concepts"></a>シングルサインオンの概念

### <a name="identity-brokers"></a>Identity ブローカー

Microsoft は、すべてのモバイル プラットフォーム用に、さまざまなベンダーのアプリケーション間で資格情報をブリッジできるようにするアプリケーションを提供しています。このアプリケーションでは、資格情報を検証する安全な単一の場所を必要とする拡張機能を利用できます。 これらは**ブローカー**と呼ばれています。

iOS および Android では、これらのブローカーはダウンロード可能なアプリケーションを通じて提供され、ユーザーが個別にインストールするか、従業員のデバイスの一部またはすべてを管理している会社がデバイスにプッシュします。 ブローカーは、IT 管理者の構成に基づいて、一部のアプリケーションまたはデバイス全体のセキュリティの管理をサポートします。 Windows では、この機能はオペレーティング システムに組み込まれているアカウント セレクターによって提供されます。これは、技術的には Web 認証ブローカーと呼ばれています。

#### <a name="broker-assisted-login"></a>ブローカーの支援によるログイン

ブローカーの支援によるログインは、ブローカー アプリケーション内で発生するログイン エクスペリエンスです。デバイス上の Identity プラットフォームを利用するすべてのアプリケーションで資格情報を共有するために、ブローカーのストレージとセキュリティを使用します。 これは、各アプリケーションがユーザーのサインインをブローカーに依存していることを意味しています。 iOS および Android では、これらのブローカーはダウンロード可能なアプリケーションを通じて提供されます。ユーザーが個別にインストールすることも、ユーザーのデバイスを管理している会社がデバイスにプッシュすることもできます。 この種のアプリケーションの例として、iOS 上の Microsoft Authenticator アプリケーションがあります。 Windows では、この機能はオペレーティング システムに組み込まれているアカウント セレクターによって提供されます。これは、技術的には Web 認証ブローカーと呼ばれています。
プラットフォームによってエクスペリエンスが異なるため、適切に管理しないとユーザーが混乱することがあります。 Facebook アプリケーションがインストールされており、別のアプリケーションから Facebook Connect を使用する場合、このパターンが最も一般的でしょう。 ID プラットフォームでは、同じパターンを使用します。

Android では、アカウント セレクターがアプリケーションの上に表示されます。この方が、ユーザーにとってはわかりやすい方法です。

#### <a name="how-the-broker-gets-invoked"></a>ブローカーが呼び出されるしくみ

デバイスに互換性のあるブローカー (Microsoft Authenticator アプリケーションなど) がインストールされている場合、Identity SDK は、ユーザーが ID プラットフォームのいずれかのアカウントを使用してログインする意思を示したときに、ブローカーを呼び出す処理を自動的に行います。

#### <a name="how-microsoft-ensures-the-application-is-valid"></a>Microsoft ではアプリケーションが有効であることをどのように保証しているか

ブローカーを呼び出したアプリケーションの ID を必ず確認することは、ブローカーの支援によるログインで提供されるセキュリティに不可欠です。 iOS と Android はいずれも、特定のアプリケーションでのみ有効な一意の識別子を適用しないので、悪意のあるアプリケーションが正当なアプリケーションの識別子に "なりすまし"、正当なアプリケーション用のトークンを受け取る可能性があります。 Microsoft では、実行時に常に適切なアプリケーションと通信できるように、アプリケーションを Microsoft に登録するときにカスタム redirectURI を提供するよう開発者に求めています。 **開発者がこのリダイレクト URI を作成する方法については、後ほど詳しく説明します。** このカスタム redirectURI にはアプリケーションの証明書の拇印が含まれており、Google Play ストアによってそのアプリケーションに固有であることが保証されます。 アプリケーションがブローカーを呼び出すと、ブローカーは、ブローカーを呼び出した証明書の拇印を提供するよう Android オペレーティング システムに要求します。 ブローカーは、ID システムの呼び出しでこの証明書の拇印を Microsoft に提供します。 アプリケーションの証明書の拇印が登録時に開発者から提供された証明書の拇印と一致しない場合、アプリケーションが要求しているリソースのトークンへのアクセスが拒否されます。 このチェックにより、開発者が登録したアプリケーションだけがトークンを確実に受け取ることができます。

仲介型 SSO ログインには、次の利点があります。

* ユーザーは、ベンダーを問わず、すべてのアプリケーションで SSO を利用できます。
* アプリケーションで、条件付きアクセスなどのより高度なビジネス機能を使用でき、Intune シナリオをサポートできます。
* アプリケーションが、ビジネス ユーザーの証明書ベースの認証をサポートできます。
* アプリケーションとユーザーの ID が、追加のセキュリティ アルゴリズムと暗号化を使用するブローカー アプリケーションによって検証されるため、サインイン エクスペリエンスの安全性が向上します。

以下に、SDK が SSO を有効にするためにブローカー アプリケーションとどのように動作するかを示します。

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
|  ADAL SDK  | |  ADAL SDK  |   |  ADAL SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+

```

### <a name="turning-on-sso-for-broker-assisted-sso"></a>ブローカーの支援による SSO の有効化

デバイスにインストールされている任意のブローカーを使用するアプリケーションの機能は、既定ではオフになっています。 アプリケーションでブローカーを使用するには、追加の構成を行い、アプリケーションにいくつかのコードを追加する必要があります。

実行する手順を次に示します。

1. アプリケーション コード内の MS SDK の呼び出しで、ブローカー モードを有効にします。
2. 新しいリダイレクト URI を確立し、アプリとアプリ登録の両方に提供します。
3. Android マニフェストで適切なアクセス許可をセットアップします。

#### <a name="step-1-enable-broker-mode-in-your-application"></a>手順 1:アプリケーションでブローカー モードを有効にする

ブローカーを使用するアプリケーションの機能は、認証インスタンスの "設定" または初期セットアップを作成すると有効になります。 アプリでこれを実行するには:

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>手順 2:URL スキームで新しいリダイレクト URI を確立する

返された資格情報トークンを適切なアプリケーションが確実に受信するようにするには、Android オペレーティング システムが検証できるような方法でアプリケーションにコールバックする必要があります。 Android のオペレーティング システムは、Google Play ストアの証明書のハッシュを使用します。 この証明書のハッシュは、悪意のあるアプリケーションが偽装することはできません。 ブローカー アプリケーションの URI と併せて、Microsoft は、トークンが正しいアプリケーションに返されるようにしています。 アプリケーションに登録するには、一意のリダイレクト URI が必要です。

リダイレクト URI は、次のような適切な形式である必要があります。

`msauth://packagename/Base64UrlencodedSignature`

例: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

このリダイレクト URI は、[Azure Portal](https://portal.azure.com/) を使用して、アプリ登録に登録できます。 Azure AD のアプリ登録の詳細については、「[Integrating with Azure Active Directory (Azure Active Directory との統合)](active-directory-how-to-integrate.md)」を参照してください。

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>手順 3:アプリケーションで適切なアクセス許可をセットアップする

Android のブローカー アプリケーションは、Android OS のアカウント マネージャー機能を使用して、複数のアプリケーションにわたって資格情報を管理します。 Android でブローカーを使用するには、アプリ マニフェストが AccountManager アカウントを使用するためのアクセス許可を持っている必要があります。 アクセス許可の詳細については、[こちら](https://developer.android.com/reference/android/accounts/AccountManager.html)のアカウント マネージャーについての Google ドキュメントを参照してください。

特に、以下のアクセス許可が必要です。

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>SSO の構成の終了

これで、ID SDK が自動的に複数のアプリケーションにわたって資格情報を共有し、デバイスにブローカーがあればそれを呼び出すようになります。

## <a name="next-steps"></a>次の手順

* [シングル サインオンの SAML プロトコル](single-sign-on-saml-protocol.md)の詳細について説明します
