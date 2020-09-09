---
title: Microsoft ID プラットフォームを使用したアプリのサインイン フロー | Azure
titleSuffix: Microsoft identity platform
description: Microsoft ID プラットフォーム (v2.0) での Web アプリ、デスクトップ アプリ、およびモバイル アプリのサインイン フローの詳細について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: af5b27dc85a276c731a61135ab59ab81f5aaf3c2
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772201"
---
# <a name="app-sign-in-flow-with-microsoft-identity-platform"></a>Microsoft ID プラットフォーム を使用したアプリのサインイン フロー

このトピックでは、Microsoft ID プラットフォームを使用した Web アプリ、デスクトップ アプリ、およびモバイル アプリの基本的なサインイン フローについて説明します。 Microsoft ID プラットフォームでサポートされるサインイン シナリオの詳細については、[認証フローとアプリ シナリオ](authentication-flows-app-scenarios.md)に関する記事を参照してください。

## <a name="web-app-sign-in-flow"></a>Web アプリのサインイン フロー

ユーザーがブラウザーで Web アプリに移動すると、次のことが起こります。

* Web アプリで、ユーザーが認証されているかどうかが判断されます。
* ユーザーが認証されていない場合は、ユーザーをサインインさせるように Web アプリから Azure AD に委任されます。 そのサインインは組織のポリシーに準拠します。したがって、ユーザーに資格情報を入力するように求めることもあれば、[多要素認証](../authentication/concept-mfa-howitworks.md) (2 要素認証や 2FA と呼ばれることがあります) を使用することも、パスワードをまったく使用しない (Windows Hello を使用するなど) 場合もあります。
* ユーザーは、クライアント アプリが必要とするアクセスに同意するように求められます。 これは、ユーザーが同意したアクセスを表すトークンを Microsoft ID プラットフォームで配信できるように、クライアント アプリを Azure AD に登録する必要があるためです。

ユーザーが正常に認証されると、次のことが起こります。

* Microsoft ID プラットフォームから、Web アプリにトークンが送信されます。
* Cookie が保存され、Azure AD のドメインに関連付けられ、ブラウザーの cookie jar にユーザーの ID が含まれます。 次回、アプリがブラウザーを使用して Microsoft ID プラットフォーム認証エンドポイントに移動するときに、ユーザーがもう一度サインインする必要がないように、ブラウザーから Cookie が提示されます。 これも SSO の実現方法です。 Cookie は Azure AD によって生成され、内容は Azure AD によってのみ理解されます。
* その後、Web アプリにより、トークンが検証されます。 検証が成功した場合、Web アプリで、保護されたページが表示され、セッション Cookie がブラウザーの cookie jar に保存されます。 ユーザーが別のページに移動すると、Web アプリでは、そのユーザーがセッション Cookie に基づいて認証されていることを認識します。

次のシーケンス図は、この相互作用をまとめたものです。

![Web アプリの認証プロセス](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Web アプリで、ユーザーが認証されているかどうかが判断されるしくみ

Web アプリの開発者は、すべてのページまたは特定のページのみで認証を必要とするかどうかを指定できます。 たとえば、ASP.NET/ASP.NET Core では、`[Authorize]` 属性をコントローラー アクションに追加してこれを行います。

この属性により、ASP.NET で、ユーザーの ID が含まれるセッション Cookie の存在が確認されます。 Cookie が存在しない場合、ASP.NET により、指定された ID プロバイダーに認証がリダイレクトされます。 ID プロバイダーが Azure AD 場合、Web アプリにより、`https://login.microsoftonline.com` に認証がリダイレクトされ、サインイン ダイアログが表示されます。

### <a name="how-a-web-app-delegates-sign-in-to-microsoft-identity-platform-and-obtains-a-token"></a>Web アプリでサインインが Microsoft ID プラットフォームに委任され、トークンが取得されるしくみ

ユーザー認証は、ブラウザーを介して行われます。 OpenID プロトコルで、標準の HTTP プロトコル メッセージが使用されます。

* Web アプリでは、Microsoft ID プラットフォームを使用するために HTTP 302 (リダイレクト) がブラウザーに送信されます。
* ユーザーが認証されると、Microsoft ID プラットフォームでは、ブラウザーからのリダイレクトを使用して Web アプリにトークンが送信されます。
* リダイレクトは、リダイレクト URI の形式で Web アプリから提供されます。 このリダイレクト URI は、Azure AD アプリケーション オブジェクトに登録されます。 アプリケーションは複数の URL でデプロイされる可能性があるため、リダイレクト URI は複数存在する場合があります。 そのため、Web アプリで、使用するリダイレクト URI も指定する必要があります。
* Azure AD では、Web アプリから送信されるリダイレクト URI が、アプリの登録されたリダイレクト URI のいずれかであることを確認します。

## <a name="desktop-and-mobile-app-sign-in-flow"></a>デスクトップ アプリおよびモバイル アプリのサインイン フロー

上述したフローは、デスクトップ アプリケーションとモバイル アプリケーションに適用されますが、若干の違いがあります。

デスクトップ アプリケーションとモバイル アプリケーションでは、認証のために、埋め込み Web コントロールまたはシステム ブラウザーを使用できます。 次の図は、デスクトップ アプリまたはモバイル アプリで Microsoft 認証ライブラリ (MSAL) を使用してアクセス トークンを取得し、Web API を呼び出す方法を示しています。

![デスクトップ アプリのしくみ](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL では、ブラウザーを使用してトークンを取得します。 Web アプリと同様に、認証は Microsoft ID プラットフォームに委任されます。

Azure AD は Web アプリの場合と同じ ID Cookie をブラウザーに保存するため、ネイティブ アプリまたはモバイル アプリでシステム ブラウザーを使用する場合は、対応する Web アプリを使用してすぐに SSO を取得します。

既定では、MSAL ではシステム ブラウザーが使用されます。 埋め込みコントロールを使用して、より統合されたユーザー エクスペリエンスを提供する .NET Framework デスクトップ アプリケーションは、例外となります。

## <a name="next-steps"></a>次のステップ

認証と承認の基本に関するその他のトピックは、次のとおりです。

* Microsoft ID プラットフォームでの認証と承認の基本的な概念については、「[認証と承認](authentication-vs-authorization.md)」を参照してください。
* 認証と承認でアクセス トークン、更新トークン、ID トークンがどのように使用されるかについては、[セキュリティ トークン](security-tokens.md)に関する記事を参照してください。
* Microsoft ID プラットフォームと統合できるようにアプリケーションを登録するプロセスについては、[アプリケーション モデル](application-model.md)に関する記事を参照してください。

アプリのサインイン フローの詳細については、次を参照してください。

* Microsoft ID プラットフォームでサポートされているユーザーを認証するためのその他のシナリオの詳細については、[認証フローとアプリ シナリオ](authentication-flows-app-scenarios.md)に関する記事を参照してください。
* Microsoft アカウント、Azure AD アカウント、および Azure AD B2C ユーザーを 1 つの合理化されたプログラミング モデルで使用するアプリケーションの開発に役立つ Microsoft ライブラリの詳細については、「[Microsoft Authentication Library (MSAL) の概要](msal-overview.md)」を参照してください。
