---
title: Azure AD B2C の新しいアプリの登録エクスペリエンス
description: Azure AD B2C の新しいアプリの登録エクスペリエンスの概要です。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/25/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 430da23986fc36a0e94c049512ef716aff1fed5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98660252"
---
# <a name="the-new-app-registrations-experience-for-azure-active-directory-b2c"></a>Azure Active Directory B2C の新しいアプリの登録エクスペリエンス

Azure Active Directory B2C (Azure AD B2C) の新しい **[アプリの登録](https://aka.ms/b2cappregistrations)** エクスペリエンスが一般提供されました。 アプリケーションを Azure AD B2C に登録するための **アプリケーション** エクスペリエンスに慣れている場合、ここでは「従来のエクスペリエンス」として触れられています。本ガイドは新しいエクスペリエンスの使用開始をサポートします。

## <a name="overview"></a>概要
以前は、従来のエクスペリエンスを使用して、コンシューマー向けの Azure AD B2C アプリケーションを他のアプリとは別に管理する必要がありました。 これは、Azure のさまざまな場所で、さまざまなアプリ作成エクスペリエンスが存在していたことを意味します。

新しいエクスペリエンスでは、すべての Azure AD B2C アプリの登録と Azure AD アプリの登録を 1 か所で表示し、一貫した方法で管理することができます。 顧客向けアプリの作成から、リソース管理のための Microsoft Graph アクセス許可を持つアプリの管理まで、1 つの方法を学習するだけで済みます。

新しいエクスペリエンスにアクセスするには、Azure portal の **Azure AD B2C** または **Azure Active Directory** サービスのいずれかから、Azure AD B2C テナント内の **[アプリの登録]** に移動します。

Azure AD B2C のアプリの登録エクスペリエンスは、すべての Azure AD テナント向けの一般的な[アプリの登録エクスペリエンス](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/)に基づいていますが、Azure AD B2C テナント向けにカスタマイズされています。

## <a name="whats-not-changing"></a>変更のない事項
- お使いのアプリケーションや関連する構成は、新しいエクスペリエンス内でそのまま見つかります。 アプリケーションを再度登録する必要はなく、アプリケーションのユーザーは再度サインインする必要はありません。

> [!NOTE]
> 以前に作成したすべてのアプリケーションを表示するには、 **[アプリの登録]** ブレードに移動し、 **[すべてのアプリケーション]** タブを選択します。これにより、従来のエクスペリエンスで作成されたアプリ、新しいエクスペリエンスで作成されたアプリ、および Azure AD サービスで作成されたアプリが表示されます。

## <a name="key-new-features"></a>主要な新機能

-   **統合されたアプリの一覧** には、Azure AD B2C と Azure AD で認証されているすべてのアプリケーションが、アクセスしやすいよう 1 か所に表示されます。 さらに、**作成日** の日付、**証明書とシークレット** の状態、検索バーなどの、Azure AD アプリケーションで既に利用可能な機能を利用できます。

-   **統合されたアプリの登録** により、顧客向けのアプリであるか Microsoft Graph にアクセスするアプリであるかにかかわらず、アプリをすばやく登録できます。

- **エンドポイント** ウィンドウを使用すると、シナリオに関連するエンドポイント (OpenID Connect の構成、SAML メタデータ、Microsoft Graph API、[OAuth 2.0 ユーザー フロー エンドポイント](tokens-overview.md#endpoints)を含む) をすばやく特定できます。

- **API のアクセス許可** および **API の公開** によって、より広範なスコープ、アクセス許可、および同意の管理が提供されます。 また、MS Graph と Azure AD Graph のアクセス許可をアプリに割り当てられるようになりました。

-   Azure AD B2C で認証するアプリに対して、**所有者** および **マニフェスト** を使用できるようになりました。 [マニフェスト エディターを使用](../active-directory/develop/reference-app-manifest.md)して、登録の所有者を追加したり、アプリケーションのプロパティを直接編集したりすることができます。


## <a name="new-supported-account-types"></a>新たにサポートされているアカウントの種類

新しいエクスペリエンスでは、次のオプションからサポート アカウントの種類を選択します。
- この組織のディレクトリ内のアカウントのみ
- 任意の組織ディレクトリ内のアカウント (任意の Azure AD ディレクトリ - マルチテナント)
- Accounts in any identity provider or organizational directory (for authenticating users with user flows) (任意の ID プロバイダーまたは組織のディレクトリ内のアカウント (ユーザー フローでユーザーを認証する場合))

アカウントの種類の違いを理解するには、[作成] エクスペリエンスで **[選択に関する詳細]** を選択します。

従来のエクスペリエンスでは、アプリは常に顧客向けアプリケーションとして作成されていました。 このようなアプリの場合、アカウントの種類は **[Accounts in any identity provider or organizational directory (for authenticating users with user flows)]\(任意の ID プロバイダーまたは組織のディレクトリ内のアカウント (ユーザー フローでユーザーを認証する場合)\)** に設定されます。
> [!NOTE]
> このオプションは、このアプリケーション用にユーザーを認証するための Azure AD B2C ユーザー フローを実行できるようにするために必要です。 [ユーザー フローで使用するアプリケーションを登録する方法](tutorial-register-applications.md)を参照してください。

このオプションを使用して、SAML サービス プロバイダーとして Azure AD B2C を使用することもできます。 [詳細については、こちらを参照してください](identity-provider-adfs.md)。

## <a name="applications-for-devops-scenarios"></a>DevOps シナリオ用のアプリケーション

その他のアカウントの種類を使用して、たとえば、Microsoft Graph を使用して Identity Experience Framework ポリシーをアップロードしたり、ユーザーをプロビジョニングしたりといった、DevOps シナリオを管理するアプリを作成できます。 [Microsoft Graph アプリケーションを登録して Azure AD B2C リソースを管理する方法](microsoft-graph-get-started.md)を参照してください。

アクセス許可の多くは Azure B2C コンシューマー ユーザーには適用されないため、すべての Microsoft Graph のアクセス許可が表示されない場合があります。 [Microsoft Graph を使用したユーザー管理の詳細については、こちらを参照してください](microsoft-graph-operations.md)。

## <a name="admin-consent-and-offline_accessopenid-scopes"></a>管理者の同意と offline_access+openid スコープ
<!-- Azure AD B2C doesn't support user consent. That is, when a user signs into an application, the user doesn't see a screen requesting consent for the application permissions. All permissions have to be granted through admin consent.  -->

Azure AD B2C を使用してユーザーをアプリにサインインできるようにするには、**openid** スコープが必要です。 ユーザーに更新トークンを発行するには、**offline_access** スコープが必要です。 これらのスコープは以前に追加されており、管理者の同意が既定で付与されています。 **[openid と offline_access アクセス許可に対して管理者の同意を付与します]** オプションを確実に選択することにより、作成プロセス中にこれらのスコープに対するアクセス許可を簡単に追加できるようになりました。 それ以外の場合、Microsoft Graph のアクセス許可は、管理者の同意とともに既存のアプリの **[API のアクセス許可]** 設定で追加できます。

[アクセス許可と同意](../active-directory/develop/v2-permissions-and-consent.md)についてご確認ください。

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>プラットフォーム/認証:応答 URL/リダイレクト URI
従来のエクスペリエンスでは、さまざまなプラットフォームの種類が **[プロパティ]** で管理されており、Web アプリや API の応答 URL として、またネイティブ クライアントのリダイレクト URI として使用されていました。 "ネイティブ クライアント" は "パブリック クライアント" とも呼ばれ、iOS、macOS、Android、およびその他の種類のモバイルおよびデスクトップ アプリケーション向けのアプリが含まれます。

新しいエクスペリエンスでは、応答 URL とリダイレクト URI は両方ともリダイレクト URI と呼ばれ、アプリの **[認証]** セクションにあります。 アプリの登録は、Web アプリまたはネイティブ アプリケーションのいずれかに制限されているわけではありません。 それぞれのリダイレクト URI を登録することで、これらすべてのプラットフォームの種類に対して、同じアプリの登録を使用できます。

リダイレクト URI は、Web またはパブリック (モバイルおよびデスクトップ) のアプリの種類のいずれかに関連付けられている必要があります。 [リダイレクト URI の詳細情報についてご確認ください](../active-directory/develop/quickstart-register-app.md#add-a-redirect-uri)

<!-- Whether an application should be treated as a public client is inferred at run-time from the Redirect URI platform type, if possible. The **Treat application as a public client** setting should be set to **Yes** for flows that might not use a redirect URI, such as ROPC flows. -->

**iOS/macOS** と **Android** プラットフォームは、パブリック クライアントの一種です。 これらでは、MSAL で使用するための対応するリダイレクト URI を使用して、iOS/macOS または Android アプリを簡単に構成できるようにする方法が提供されています。 [アプリケーション構成オプション](../active-directory/develop/msal-client-applications.md)の詳細をご確認ください。


## <a name="application-certificates--secrets"></a>アプリケーション証明書とシークレット

新しいエクスペリエンスでは、 **[キー]** ではなく **[証明書とシークレット]** ブレードを使用して、証明書とシークレットを管理します。 証明書とシークレットを使用すると、アプリケーションは Web アドレス指定可能な場所でトークンを受信するときに、認証サービスに対して身元を証明できます (HTTPS スキームを使用します)。 Azure AD に対して認証する場合は、クライアント シークレットではなく証明書をクライアントの資格情報シナリオに使用することをお勧めします。 Azure AD B2C に対しては、証明書を使用して認証を行うことはできません。


## <a name="features-not-applicable-in-azure-ad-b2c-tenants"></a>Azure AD B2C テナントに適用されない機能
次の Azure AD アプリの登録機能は、Azure AD B2C テナントに適用されないか、テナントで利用できません。
- **ロールと管理者** - 現在、Azure AD B2C では使用できません。
- **ブランド** - UI や UX のカスタマイズは、**会社のブランド** エクスペリエンスまたはユーザー フローの一部として構成されます。 [Azure Active Directory B2C 内のユーザー インターフェイスをカスタマイズする](customize-ui-with-html.md)方法を参照してください。
- **発行元ドメインの検証** - アプリは、検証済みのドメインではない *onmicrosoft.com* に登録されます。 また、発行元ドメインは主にユーザーの同意を与えるために使用され、ユーザー認証のために Azure AD B2C アプリに適用されるものではありません。 [発行元ドメインの詳細についてご確認ください](../active-directory/develop/howto-configure-publisher-domain.md)。
- **トークン構成** - トークンは、アプリではなくユーザー フローの一部として構成されます。
- **クイックスタート** エクスペリエンスは現在、Azure AD B2C テナントでは使用できません。
<!-- - The **Integration assistant** blade is currently not available for Azure AD B2C tenants. -->

## <a name="limitations"></a>制限事項
新しいエクスペリエンスには次のような制限があります。
- 現時点では、Azure AD B2C は暗黙的なフローでアクセス トークンと ID トークンのどちらが発行可能であるかを区別していません。 **[認証]** ブレードで **[ID トークン]** オプションが選択されている場合、両方の種類のトークンが暗黙的な許可フローに使用できます。
<!-- - Azure AD B2C doesn't currently support the single-page application "SPA" app type.  -->
- サポートされているアカウントの値の変更は、UI ではサポートされていません。 Azure AD のシングル テナントとマルチ テナントの切り替えを行う場合を除き、アプリ マニフェストを使用する必要があります。

## <a name="next-steps"></a>次のステップ

新しいアプリの登録エクスペリエンスを開始するには、以下に従ってください。
* [Web アプリケーションを登録する方法](tutorial-register-applications.md)を参照してください。
* [Web API を登録する方法](add-web-api-application.md)を参照してください。
* [ネイティブ クライアント アプリケーションを登録する方法](add-native-application.md)を参照してください。
* [Microsoft Graph アプリケーションを登録して Azure AD B2C リソースを管理する方法](microsoft-graph-get-started.md)を参照してください。
* [Azure AD B2C を SAML サービス プロバイダーとして使用する方法](identity-provider-adfs.md)を参照してください。
* [アプリケーションの種類](application-types.md)を参照してください。