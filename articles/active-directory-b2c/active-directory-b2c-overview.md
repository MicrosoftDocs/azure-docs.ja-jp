---
title: Azure Active Directory B2C とは | Microsoft Docs
description: Azure Active Directory B2C を使用して、アプリケーションでサインアップ、サインイン、プロファイル管理などの ID エクスペリエンスを作成および管理する方法について説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 02/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ca9d8a8373bd73d527862864d436319eb45b5f48
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227165"
---
# <a name="what-is-azure-active-directory-b2c"></a>Azure Active Directory B2C とは

Azure Active Directory (Azure AD) B2C は、企業-消費者間の ID 管理サービスです。 このサービスを使用すると、ユーザーが Web、デスクトップ、モバイル、またはシングルページ アプリケーションと安全に対話する方法を、カスタマイズしたり制御したりできます。 Azure AD B2C を使用して、ユーザーは、サインアップ、サインイン、パスワードのリセット、およびプロファイルの編集を行うことができます。 Azure AD B2C では、OpenID Connect プロトコルと OAuth 2.0 プロトコルの一形式が実装されます。 これらのプロトコルの実装で重要な点は、リソースへのセキュリティ保護されたアクセスを提供できるようにする、セキュリティ トークンとそのクレームです。

"*ユーザー体験*" は、Azure AD B2C とやり取りするユーザーとアプリケーションの動作を制御するポリシーが指定されている要求です。 Azure AD B2C では、ユーザー体験を定義するために 2 つのパスを使用できます。

ID の専門知識の有無に関わらず、アプリケーション開発者の場合は、Azure portal を使用して一般的な ID ユーザー フローを定義することをお勧めします。 ID の専門家、システム インテグレーター、コンサルタント、社内の ID チームで、OpenID Connect のフローに慣れており、ID プロバイダーおよびクレーム ベースの認証を理解している場合は、XML ベースのカスタム ポリシーを選択することをお勧めします。

ユーザー体験の定義を始める前に、Azure AD B2C テナントを作成し、アプリケーションと API をテナントに登録する必要があります。 これらのタスクを完了した後は、どちらかのユーザー フローまたはカスタム ポリシーを使用して、ユーザー体験の定義を始めることができます。 また、必要に応じて、ID プロバイダーを追加または変更したり、ユーザーが体験を経験する方法をカスタマイズしたりできます。

## <a name="protocols-and-tokens"></a>プロトコルとトークン

Azure AD B2C では、ユーザー体験に対して、[OpenID Connect プロトコルと OAuth 2.0 プロトコル](active-directory-b2c-reference-protocols.md)がサポートされています。 Azure AD B2C の OpenID Connect の実装では、アプリケーションは Azure AD B2C に認証要求を発行することで、このユーザー体験を開始します。

Azure AD B2C に対する要求の結果として、[ID トークンやアクセス トークン](active-directory-b2c-reference-tokens.md)などのセキュリティ トークンが作成されます。 このセキュリティ トークンでは、ユーザーの ID が定義されています。 トークンは、`/token` や `/authorize` などの Azure AD B2C エンドポイントから受信します。 これらのトークンからクレームにアクセスできます。クレームは、ID の検証や、セキュリティ保護されたリソースへのアクセスの許可に使用できます。

## <a name="tenants-and-applications"></a>テナントとアプリケーション

Azure AD B2C では、"*テナント*" は組織を表しており、ユーザーのディレクトリとなっています。 各 Azure AD B2C テナントは、他の Azure AD B2C テナントと区別され分離されています。 Azure Active Directory テナントを既にお持ちかもしれませんが、Azure AD B2C テナントは別のテナントです。 テナントには、アプリケーションを使用するためにサインアップしたユーザーに関する情報が含まれています。 たとえば、パスワード、プロファイル データ、アクセス許可が含まれます。 詳細については、[チュートリアル: Azure Active Directory B2C テナントを作成する](tutorial-create-tenant.md)」の手順に従って作成できます。

Azure AD B2C を使用するようアプリケーションを構成する前に、まず、Azure portal を使用してテナントにアプリケーションを登録する必要があります。 登録プロセスでは、値が収集され、対象のアプリケーションに割り当てられます。 これらの値には、アプリケーションとリダイレクト URI を一意に識別するアプリケーション ID が含まれており、これを使用して応答がアプリケーションに戻されます。

すべてのアプリケーションによるやり取りは、次のような大まかなパターンに従って行われます。

1. アプリケーションが、ポリシーを実行するようにユーザーに指示します。
2. ユーザーがポリシーの定義に従ってポリシーを完了します。
3. アプリケーションがトークンを受け取ります。
4. アプリケーションはトークンを使って、リソースへのアクセスを試みます。
5. リソース サーバーは、トークンを検証し、アクセスを許可できることを確認します。
6. アプリケーションは、トークンを定期的に更新します。

Web アプリケーションを登録するには、[アプリケーションを登録して Azure AD B2C を使用したサインアップとサインインを有効にするチュートリアル](tutorial-register-applications.md)の手順を完了します。 [Web API アプリケーションを Azure Active Directory B2C テナントに追加する](add-web-application.md)こと、または[ネイティブ クライアント アプリケーションを Azure Active Directory B2C テナントに追加する](add-native-application.md)こともできます。

## <a name="user-journeys"></a>ユーザー体験

ユーザー体験内のポリシーは、[ユーザー フロー](active-directory-b2c-reference-policies.md)または[カスタム ポリシー](active-directory-b2c-overview-custom.md)として定義することができます。 Azure portal では、サインアップ、サインイン、プロファイル編集など、最も一般的な ID タスク用の定義済みのユーザー フローを利用できます。

ユーザー体験では、次の設定を構成することにより動作を制御できます。

- ユーザーがアプリケーションへのサインアップに使用するソーシャル アカウント
- 名や郵便番号など、ユーザーから収集されたデータ
- 多要素認証
- ページのルック アンド フィール
- アプリケーションに返される情報

カスタム ポリシーは、Azure AD B2C テナントでの [Identity Experience Framework](trustframeworkpolicy.md) の動作を定義する構成ファイルです。 Identity Experience Framework は、マルチパーティの信頼を確立してユーザー体験の手順を完了する、基になるプラットフォームです。

カスタム ポリシーは、多くのタスクを完了するために変更できます。 カスタム ポリシーは、階層型チェーンで互いを参照する 1 つまたは複数の XML 形式ファイルです。 カスタム ポリシーの[スターター パック](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)を使用して、一般的な ID タスクを有効にすることができます。

Azure AD B2C テナントでは必要に応じてさまざまな種類のカスタム ポリシーまたはユーザー フローが使用され、複数のアプリケーションでそれらを再利用できます。 この柔軟性により、最小限のコード変更で、またはコードをまったく変更せずに、ユーザー ID エクスペリエンスを定義および変更できます。 ポリシーは、HTTP 認証要求に特別なクエリ パラメーターを追加することによって使用されます。 独自のカスタム ポリシーを作成するには、「[Azure Active Directory B2C でのカスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)」をご覧ください。

## <a name="identity-providers"></a>ID プロバイダー

アプリケーションでは、ユーザーが異なる ID プロバイダーでサインインできるようにすることができます。 "*ID プロバイダー*" では、ID 情報の作成、保守、管理、およびアプリケーションへの認証サービスの提供が行われます。 Azure portal を使用して、Azure AD B2C によってサポートされる ID プロバイダーを追加することができます。

通常、アプリケーションでは ID プロバイダーを 1 つだけ使用しますが、さらに追加することもできます。 Azure AD B2C テナントで ID プロバイダーを構成するには、最初に ID プロバイダーの開発者サイトでアプリケーションを作成した後、作成する ID プロバイダー アプリケーションからの、アプリケーション ID またはクライアント ID と、パスワードまたはクライアント シークレットを記録する必要があります。 この ID とパスワードはその後、アプリケーションを構成するのに使用されます。

次の記事では、いくつかの一般的な ID プロバイダーをユーザー フローに追加する手順が説明されています。

- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Microsoft アカウント](active-directory-b2c-setup-msa-app.md)

次の記事では、いくつかの一般的な ID プロバイダーをカスタム ポリシーに追加する手順が説明されています。
- [Amazon](setup-amazon-custom.md)
- [Google](active-directory-b2c-custom-setup-goog-idp.md)
- [Microsoft アカウント](active-directory-b2c-custom-setup-msa-idp.md)

詳細については、[チュートリアル: Azure Active Directory B2C でアプリケーションに ID プロバイダーを追加する](tutorial-add-identity-providers.md)」をご覧ください。


## <a name="page-customization"></a>ページのカスタマイズ

ユーザー体験で顧客に表示される HTML および CSS コンテンツのほとんどを制御できます。 ページのカスタマイズを使用することで、ユーザー フローの任意のカスタム ポリシーの外観をカスタマイズできます。 このカスタマイズ機能を使用することで、アプリケーションと Azure AD B2C との間で、ブランドと視覚的な一貫性を維持することができます。

Azure AD B2C では、ユーザーのブラウザーでコードが実行され、クロス オリジン リソース共有 (CORS) と呼ばれる最新の手法が使用されます。 最初に、カスタマイズした HTML コンテンツを含むポリシーで URL を指定します。 Azure AD B2C により、ユーザー インターフェイス要素が URL から読み込まれた HTML コンテンツとマージされ、ユーザーにページが表示されます。

クエリ文字列で Azure AD B2C にパラメーターを送信します。 パラメーターを HTML エンドポイントに渡すことで、ページのコンテンツが動的に変更されます。 たとえば、Web アプリケーションまたはモバイル アプリケーションから渡すパラメーターに基づいて、サインアップまたはサインイン ページの背景イメージを変更します。

ユーザー フロー内のページのカスタマイズについては、「[チュートリアル:Azure Active Directory B2C でユーザー エクスペリエンスのインターフェイスをカスタマイズする](tutorial-customize-ui.md)」をご覧ください。 カスタム ポリシー内のページのカスタマイズについては、「[Azure Active Directory B2C でカスタム ポリシーを使用してアプリケーションのユーザー インターフェイスをカスタマイズする](active-directory-b2c-ui-customization-custom.md)」または「[Azure Active Directory B2C: カスタム ポリシーを使用して動的コンテンツを含む UI を構成する](active-directory-b2c-ui-customization-custom-dynamic.md)」をご覧ください。

## <a name="developer-resources"></a>開発者リソース

### <a name="client-applications"></a>クライアント アプリケーション

[iOS](active-directory-b2c-devquickstarts-ios.md)、[Android](active-directory-b2c-devquickstarts-android.md)、.NET などのアプリケーションを選択できます。 Azure AD B2C は、これらのアクションを可能にする一方で、同時にユーザー ID を保護します。

ASP.NET Web アプリケーション開発者である場合は、[Web アプリケーションが Azure AD B2C を使用してアカウントを認証できるようにするチュートリアル](active-directory-b2c-tutorials-web-app.md)の手順を使用して、アカウントを認証するようにアプリケーションを設定します。

デスクトップ アプリケーション開発者である場合は、[デスクトップ アプリケーションが Azure AD B2C を使用してアカウントを認証できるようにするチュートリアル](active-directory-b2c-tutorials-desktop-app.md)の手順を使用して、アカウントを認証するようにアプリケーションを設定します。

Node.js を使用するシングルページ アプリケーションの開発者である場合は、[シングルページ アプリケーションが Azure AD B2C を使用してアカウントを認証できるようにするチュートリアル](active-directory-b2c-tutorials-spa.md)の手順を使用して、アカウントを認証するようにアプリケーションを設定します。

### <a name="apis"></a>API
クライアントまたは Web アプリケーションで API を呼び出す必要がある場合は、Azure AD B2C でそれらのリソースへのセキュリティ保護されたアクセスを設定することができます。

ASP.NET Web アプリケーション開発者の場合は、「[チュートリアル: Azure Active Directory B2C を使用して ASP.NET Web API へのアクセスを許可する](active-directory-b2c-tutorials-web-api.md)」の手順を使用して、保護された API を呼び出すようにアプリケーションを設定します。

デスクトップ アプリケーション開発者の場合は、「[チュートリアル: Azure Active Directory B2C を使用してデスクトップ アプリから Node.js Web API へのアクセスを許可する](active-directory-b2c-tutorials-desktop-app-webapi.md)」の手順を使用して、保護された API を呼び出すようにアプリケーションを設定します。

Node.js を使用するシングルページ アプリケーションの開発者である場合は、[Azure Active Directory B2C を使用してシングルページ アプリケーションから ASP.NET Core Web API へのアクセスを許可するチュートリアル](active-directory-b2c-tutorials-spa-webapi.md)の手順を使用して、保護された API を呼び出すようにアプリケーションを設定します。

### <a name="javascript"></a>JavaScript

独自の JavaScript クライアント側コードを、Azure AD B2C のアプリケーションに追加できます。 アプリケーションで JavaScript を設定するには、ユーザー フローまたはカスタム ポリシーで[ページ レイアウト](page-layout.md)を定義し、[JavaScript](javascript-samples.md) を有効にします。

### <a name="user-accounts"></a>ユーザー アカウント

多くの一般的なテナント管理タスクをプログラムで実行する必要があります。 主な例にはユーザーの管理があります。 たとえば、既存のユーザー ストアを Azure AD B2C テナントに移行することがあります。 その場合、自分のページでユーザー登録をホストし、バックグラウンドで Azure AD B2C ディレクトリのユーザー アカウントを作成することがあります。 この種のタスクでは、ユーザー アカウントの作成、読み取り、更新、削除を実行する機能が必要です。 [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md) を使用してこれらの操作を実行できます。

## <a name="next-steps"></a>次の手順

次のチュートリアルを続けて、サインアップおよびサインイン エクスペリエンスのためのアプリケーションの構成を開始します。

> [!div class="nextstepaction"]
> [チュートリアル:Azure Active Directory B2C テナントを作成する](tutorial-create-tenant.md)
