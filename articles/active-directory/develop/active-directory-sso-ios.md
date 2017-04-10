---
title: "iOS で ADAL を使用してクロス アプリ SSO を有効にする方法 | Microsoft Docs"
description: "ADAL SDK の機能を使用して、複数のアプリケーションでシングル サインオンを有効にする方法。 "
services: active-directory
documentationcenter: 
author: xerners
manager: mbaldwin
editor: 
ms.assetid: d042d6da-7503-4e20-bb55-06917de01fcd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/07/2017
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 7fcc00df80ef61f99f6044cd338cbdd16fde0117
ms.lasthandoff: 03/29/2017


---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>iOS で ADAL を使用してクロス アプリ SSO を有効にする方法
シングル サインオン (SSO) を提供すると、ユーザーが資格情報を 1 度入力するだけで、この資格情報が他のアプリケーションでも自動的に使用されるようになります。今では、この機能が一般的になってきました。 小さな画面ではユーザー名とパスワードを入力しづらく、多くの場合は音声通話やテキスト化コードなどの他の要素 (2FA) も組み合わされるため、こうした操作が複数回必要になる製品は評価が低くなります。

さらに、Microsoft アカウント、Office365 の職場アカウントなど、他のアプリケーションでも使用できる ID プラットフォームを利用している場合、ユーザーはこれらの資格情報が、ベンダーを問わず、すべてのアプリケーションで使用できることを求めます。

Microsoft Identity プラットフォームと Microsoft Identity SDK を利用すると、こうした難しい課題を解決し、ユーザーに SSO を提供できます。ブローカー機能と Authenticator アプリケーションも利用すれば、独自のアプリケーション スイート内だけでなく、デバイス全体で SSO を実現できます。

このチュートリアルでは、この利点をユーザーに提供するためにアプリケーション内で SDK を構成する方法について説明します。

このチュートリアルの適用対象:

* Azure Active Directory
* Azure Active Directory B2C
* Azure Active Directory B2B
* Azure Active Directory の条件付きアクセス

このドキュメントは、[従来のポータルで Azure Active Directory 用にアプリケーションをプロビジョニング](active-directory-how-to-integrate.md)し、アプリケーションを [Microsoft Identity iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc) と統合する方法についての知識があることを前提としています。

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>Microsoft Identity プラットフォームでの SSO の概念
### <a name="microsoft-identity-brokers"></a>Microsoft Identity ブローカー
Microsoft は、すべてのモバイル プラットフォーム用に、さまざまなベンダーのアプリケーション間で資格情報をブリッジできるようにするアプリケーションを提供しています。このアプリケーションでは、資格情報を検証する安全な単一の場所を必要とする、特別な拡張機能を利用できます。 これらを**ブローカー**といいます。 iOS および Android では、これらのブローカーはダウンロード可能なアプリケーションを通じて提供されます。ユーザーが個別にインストールすることも、従業員のデバイスの一部またはすべてを管理している会社がデバイスにプッシュすることもできます。 これらのブローカーは、IT 管理者の意図に基づいて、一部のアプリケーションまたはデバイス全体のセキュリティの管理をサポートします。 Windows では、この機能はオペレーティング システムに組み込まれているアカウント セレクターによって提供されます。これは、技術的には Web 認証ブローカーと呼ばれています。

これらのブローカーの使用方法と、Microsoft Identity プラットフォームのログイン フローでユーザーにどのように表示されるのかについては、この後の詳細をお読みください。

### <a name="patterns-for-logging-in-on-mobile-devices"></a>モバイル デバイスでのログインのパターン
デバイスでの資格情報へのアクセスは、Microsoft Identity プラットフォームの 2 つの基本的なパターンに従います。

* 非ブローカーの支援によるログイン
* ブローカーの支援によるログイン

#### <a name="non-broker-assisted-logins"></a>非ブローカーの支援によるログイン
非ブローカーの支援によるログインは、アプリケーションに従って発生するログイン エクスペリエンスであり、そのアプリケーションのデバイスのローカル ストレージを使用します。 このストレージは複数のアプリケーションで共有される場合がありますが、資格情報はアプリに、またはその資格情報を使用するアプリのスイートに、緊密にバインドされています。 これは、多くのモバイル アプリケーションにおいて、アプリケーション内でユーザー名とパスワードを入力するときの一般的なエクスペリエンスです。

このログインには、次のような利点があります。

* ユーザー エクスペリエンス全体が、アプリケーション内に存在します。
* 資格情報は、同じ証明書によって署名されているアプリケーション間で共有することができ、アプリケーションのスイートにシングル サインオン エクスペリエンスを提供します。
* ログインのエクスペリエンスに関する制御は、サインインの前後にアプリケーションに提供されます。

このログインには、次のような欠点があります。

* ユーザーがシングル サインオンを利用できるのは、アプリケーションが構成した Microsoft ID 間に限られます。Microsoft ID を使用するすべてのアプリにわたるシングル サインオンは利用できません。
* アプリケーションで、条件付きアクセスなどのより高度なビジネス機能を使用したり、InTune 製品スイートを使用したりすることはできません。
* アプリケーションが、ビジネス ユーザーの証明書ベースの認証をサポートできません。

以下に、Microsoft Identity SDK が SSO を有効にするためにアプリケーションの共有ストレージとどのように動作するかを示します。

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| ADAL SDK  |  |  ADAL SDK  |  |  ADAK SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>ブローカーの支援によるログイン
ブローカーの支援によるログインは、ブローカー アプリケーション内で発生するログイン エクスペリエンスです。デバイス上の Microsoft Identity プラットフォームを利用するすべてのアプリケーションで資格情報を共有するために、ブローカーのストレージとセキュリティを使用します。 つまり、各アプリケーションはユーザーのサインインをブローカーに依存します。 iOS および Android では、これらのブローカーはダウンロード可能なアプリケーションを通じて提供されます。ユーザーが個別にインストールすることも、ユーザーのデバイスを管理している会社がデバイスにプッシュすることもできます。 この種類のアプリケーションの例は、iOS での Azure Authenticator アプリケーションです。 Windows では、この機能はオペレーティング システムに組み込まれているアカウント セレクターによって提供されます。これは、技術的には Web 認証ブローカーと呼ばれています。
プラットフォームによってエクスペリエンスが異なるため、適切に管理しないとユーザーが混乱することがあります。 Facebook アプリケーションがインストールされており、別のアプリケーションから Facebook Connect を使用する場合、このパターンが最も一般的でしょう。 Microsoft Identity プラットフォームでは、同じパターンを使用します。

iOS では、このようにすると "切り替え" アニメーションが表示され、アプリケーションはバックグラウンドに送られて、Azure Authenticator アプリケーションがフォアグラウンドになります。そこでユーザーは、どのアカウントでサインインするかを選択できます。  

Android と Windows では、アカウント セレクターがアプリケーションの上に表示されます。この方が、ユーザーにとってはわかりやすい方法です。

#### <a name="how-the-broker-gets-invoked"></a>ブローカーが呼び出されるしくみ
デバイスに互換性のあるブローカー (Azure Authenticator アプリケーションなど) がインストールされている場合、Microsoft Identity SDK は、ユーザーが Microsoft Identity プラットフォームのいずれかのアカウントを使用してログインする意思を示したときに、自動的にブローカーを呼び出す処理を行います。 このアカウントは、個人用の Microsoft アカウント、職場または学校アカウント、B2C および B2B 製品を使用して Azure で提供およびホストしているアカウントのいずれかです。 

 #### <a name="how-we-ensure-the-application-is-valid"></a>アプリケーションが有効であることを確認するしくみ
 
 ブローカーを呼び出したアプリケーションの ID を必ず確認することは、ブローカーの支援によるログインで提供されるセキュリティに不可欠です。 iOS と Android はいずれも、特定のアプリケーションでのみ有効な一意の識別子を適用しないので、悪意のあるアプリケーションが正当なアプリケーションの識別子に "なりすまし"、正当なアプリケーション用のトークンを受け取る可能性があります。 Microsoft では、実行時に常に適切なアプリケーションと通信できるように、アプリケーションを Microsoft に登録するときにカスタム redirectURI を提供するよう開発者に求めています。 **開発者がこのリダイレクト URI を作成する方法については、後ほど詳しく説明します。** このカスタム redirectURI にはアプリケーションのバンドル ID が含まれており、Apple App Store によってそのアプリケーションに固有であることが保証されます。 アプリケーションがブローカーを呼び出すと、ブローカーは、ブローカーを呼び出したバンドル ID を提供するよう iOS オペレーティング システムに要求します。 ブローカーは、ID システムの呼び出しでこのバンドル ID を Microsoft に提供します。 アプリケーションのバンドル ID が登録時に開発者から提供されたバンドル ID と一致しない場合、アプリケーションが要求しているリソースのトークンへのアクセスが拒否されます。 このチェックにより、開発者が登録したアプリケーションだけがトークンを確実に受け取ることができます。

**開発者は、Microsoft Identity SDK がブローカーを呼び出すか、非ブローカーの支援によるフローを使用するかを選択できます。** ただし、ブローカーの支援によるフローを使用しないことを選択した場合、ユーザーがデバイスで既に追加していた可能性のある SSO 資格情報を使用する利点がが失われます。また、Microsoft がユーザーに提供するビジネス機能 (条件付きアクセス、Intune 管理機能、証明書ベースの認証など) をアプリケーションで使用できなくなります。

このログインには、次のような利点があります。

* ユーザーは、ベンダーを問わず、すべてのアプリケーションで SSO を利用できます。
* アプリケーションで、条件付きアクセスなどのより高度なビジネス機能を使用したり、InTune 製品スイートを使用したりできます。
* アプリケーションが、ビジネス ユーザーの証明書ベースの認証をサポートできます。
* アプリケーションとユーザーの ID が、追加のセキュリティ アルゴリズムと暗号化を使用するブローカー アプリケーションによって検証されるため、サインイン エクスペリエンスの安全性が大幅に向上します。

このログインには、次のような欠点があります。

* iOS では、資格情報が選択されている間、ユーザーがアプリケーションのエクスペリエンスの外部に置かれます。
* ユーザーのログイン エクスペリエンスをアプリケーション内で管理できなくなります。

以下に、Microsoft Identity SDK が SSO を有効にするためにブローカー アプリケーションとどのように動作するかを示します。

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
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

この背景情報を知ると、SSO をよりよく理解し、Microsoft Identity プラットフォームと SDK を使用してアプリケーション内で実装できるようになります。

## <a name="enabling-cross-app-sso-using-adal"></a>ADAL を使用した、クロス アプリ SSO の有効化
ここでは、ADAL iOS SDK を使用して、以下の操作を行います。

* アプリのスイートに対して、非ブローカーの支援による SSO を有効にする
* ブローカーの支援による SSO のサポートを有効にする

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>非ブローカーの支援による SSO の有効化
複数のアプリケーション間での非ブローカーの支援による SSO の場合、SSO のほとんどの複雑な作業は Microsoft Identity SDK によって管理されます。 たとえば、クエリに対処するための、キャッシュ内の適切なユーザーの検索や、ログインしたユーザーのリストの保持などです。

所有している複数のアプリケーション間で SSO を有効にするには、以下の操作をする必要があります。

1. すべてのアプリケーションが同じクライアント ID またはアプリケーション ID を使用していることを確認します。
2. キーチェーンを共有できるように、すべてのアプリケーションが Apple の同じ署名証明書を共有していることを確認します。
3. 各アプリケーション用に同じキーチェーン エンタイトルメントを要求します。
4. Microsoft Identity SDK に、使用することを希望する共有されたキーチェーンについて知らせます。

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>アプリ スイートのすべてのアプリケーションで、同じクライアント ID/アプリケーション ID を使用する
Microsoft Identity プラットフォームが複数のアプリケーションでのトークンの共有を許可されていることが判断できるように、各アプリケーションは同じクライアント ID またはアプリケーション ID を共有する必要があります。 これは、ポータルで最初のアプリケーションを登録したときに提供された一意の識別子です。

各アプリが同じアプリケーション ID を使用しているのであれば、Microsoft Identity サービスはどのようにしてアプリケーションを識別できるのでしょうか。 答えは、 **リダイレクト URI**にあります。 各アプリケーションには、オンボード ポータルで、複数のリダイレクト URI を登録することができます。 スイートの各アプリは、異なるリダイレクト URI を持ちます。 次の例は、これがどのようになっているかを示しています。

App1 のリダイレクト URI: `x-msauth-mytestiosapp://com.myapp.mytestapp`

App2 のリダイレクト URI: `x-msauth-mytestiosapp://com.myapp.mytestapp2`

App3 のリダイレクト URI: `x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

これらは同じクライアント ID/アプリケーション ID の下で入れ子になっており、SDK 構成で開発者から返されるリダイレクト URI に基づいて検索されます。

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```


*これらのリダイレクト URI の形式については、以下で説明しています。ブローカーをサポートしないのであれば、任意のリダイレクト URI を使用できます。サポートする場合は、上のようでなければなりません。*

#### <a name="create-keychain-sharing-between-applications"></a>アプリケーション間のキーチェーン共有を作成する
キーチェーン共有の有効化はこのドキュメントでは説明していませんが、Apple のドキュメントの「 [Adding Capabilities (機能の追加)](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html)」で説明されています。 重要なのは、キーチェーンが何と呼ばれるかを決め、すべてのアプリケーションにわたってその機能を追加することです。

エンタイトルメントを正しくセットアップすると、プロジェクト ディレクトリに `entitlements.plist` という名前のファイルが表示されるようになります。そのファイルには、次のような内容が含まれています。

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

各アプリケーションでキーチェーン エンタイトルメントを有効にし、SSO を使用する準備ができたら、キーチェーンについて Microsoft Identity SDK に知らせます。そのためには、`ADAuthenticationSettings` を以下のように設定します。

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> 複数のアプリケーション間でキーチェーンを共有すると、どのアプリケーションでもユーザーを削除できます。さらに、アプリケーション全体のすべてのトークンを削除することもできてしまいます。 これは、バックグラウンド作業をトークンに依存するアプリケーションがある場合に、特に大きな問題になります。 キーチェーンを共有したら、Microsoft Identity SDK によるどの削除作業も、十分に注意する必要があります。
> 
> 

これで完了です。 Microsoft Identity SDK が、すべてのアプリケーション間で資格情報を共有するようになりました。 ユーザーの一覧も、アプリケーション インスタンス間で共有されます。

### <a name="turning-on-sso-for-broker-assisted-sso"></a>ブローカーの支援による SSO の有効化
デバイスにインストールされている任意のブローカーを使用するアプリケーションの機能は、 **既定ではオフ**になっています。 アプリケーションでブローカーを使用するには、追加の構成を行い、アプリケーションにいくつかのコードを追加する必要があります。

実行する手順を次に示します。

1. アプリケーション コード内の MS SDK の呼び出しで、ブローカー モードを有効にします。
2. 新しいリダイレクト URI を確立し、アプリとアプリ登録の両方に提供します。
3. URL スキームを登録します。
4. iOS9 サポート: info.plist ファイルにアクセス許可を追加します。

#### <a name="step-1-enable-broker-mode-in-your-application"></a>手順 1: アプリケーションでブローカー モードを有効にする
ブローカーを使用するアプリケーションの機能は、認証オブジェクトの "コンテキスト" または初期セットアップを作成すると有効になります。 これを実行するには、コードで次のように資格情報型を設定します。

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
`AD_CREDENTIALS_AUTO` 設定は、Microsoft Identity SDK がブローカーへの呼び出しを試行できるようにします。`AD_CREDENTIALS_EMBEDDED` は、Microsoft Identity SDK がブローカーを呼び出せないようにします。

#### <a name="step-2-registering-a-url-scheme"></a>Step 2: URL スキームを登録する
Microsoft Identity プラットフォームは、URL を使用してブローカーを起動してから、制御をアプリケーションに返します。 このラウンド トリップを完了するには、アプリケーションに登録した URL スキームが必要であり、それを Microsoft Identity プラットフォームが知っている必要があります。 このスキームは、以前にアプリケーションに登録した他のアプリ スキームに加えて登録できます。

> [!WARNING]
> 別のアプリが同じ URL スキームを使用する可能性を最小限にするために、ほぼ一意の URL スキームを作成することをお勧めします。 Apple は、アプリ ストアに登録される URL スキームの一意性を強制しません。
> 
> 

以下に、プロジェクト構成内の例を示します。 これは、XCode でも行うことができます。

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>手順 3: URI スキームで新しいリダイレクト URL を確立する
常に資格情報トークンを正しいアプリケーションに返せるようにするために、iOS オペレーティング システムが検証できるような方法で確実にアプリケーションにコールバックする必要があります。 iOS オペレーティング システムは、Microsoft ブローカー アプリケーションに、呼び出し元アプリケーションのバンドル ID を報告します。 これは、悪意のあるアプリケーションによってなりすまされることがありません。 そのため、これとブローカー アプリケーションの URI を利用して、トークンが正しいアプリケーションに確実に返されるようにしています。 この一意のリダイレクト URI をアプリケーションで確立し、開発者ポータルでリダイレクト URI として設定する必要があります。

リダイレクト URI は、次のような適切な形式である必要があります。

`<app-scheme>://<your.bundle.id>`

例: *x-msauth-mytestiosapp://com.myapp.mytestapp*

このリダイレクト URI は、[Azure Portal](https://portal.azure.com/) を使って、アプリの登録で指定する必要があります。 Azure AD のアプリ登録の詳細については、「[Integrating with Azure Active Directory (Azure Active Directory との統合)](active-directory-how-to-integrate.md)」を参照してください。

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>手順 3a: 証明書ベースの認証をサポートするために、アプリと開発者ポータルでリダイレクト URI を追加する
証明書ベースの認証をアプリケーションに追加してサポートする場合は、証明書認証を処理するために、アプリケーションと [Azure Portal](https://portal.azure.com/) で 2 番目の "msauth" を登録する必要があります。

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

例: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*

#### <a name="step-4-ios9-add-a-configuration-parameter-to-your-app"></a>手順 4: iOS9: アプリに構成パラメーターを追加する
ADAL は、ブローカーがデバイスにインストールされているかどうかを確認するために、–canOpenURL: を使用します。 iOS 9 では、どのスキーマにアプリケーションがクエリを行うことができるかを、Apple がロックダウンしました。 "msauth" を `info.plist file`の LSApplicationQueriesSchemes セクションに追加する必要があります。

<key>LSApplicationQueriesSchemes</key>

<array>
     <string>msauth</string>
</array>

### <a name="youve-configured-sso"></a>SSO の構成の終了
これで、Microsoft Identity SDK が自動的に複数のアプリケーションにわたって資格情報を共有し、デバイスにブローカーがあればそれを呼び出すようになります。


