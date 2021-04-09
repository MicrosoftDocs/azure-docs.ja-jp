---
title: Azure Active Directory の AppSource 認定を取得する | Microsoft Docs
description: Azure Active Directory の AppSource 認定を取得する方法について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/23/2020
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 7731aa2eb1a962d4674ed382911ba0058e1ebdaf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101647508"
---
# <a name="get-appsource-certified-for-azure-active-directory"></a>Azure Active Directory の AppSource 認定を取得する

[Microsoft AppSource](https://appsource.microsoft.com/) は、ビジネス ユーザーが基幹業務の SaaS アプリケーション (スタンドアロン SaaS と既存の Microsoft SaaS 製品のアドオン) を検索し、試し、管理するためのサービスです。

AppSource でスタンドアロン SaaS アプリケーションを一覧表示するには、Azure Active Directory (Azure AD) を持つ会社または組織の職場アカウントからのシングル サインオンをアプリケーションで許可する必要があります。 サインイン プロセスで [OpenID Connect](v2-protocols-oidc.md) または [OAuth 2.0](v2-oauth2-auth-code-flow.md) プロトコルを使用する必要があります。 AppSource 認定では SAML 統合はサポートされていません。

## <a name="multi-tenant-applications"></a>マルチテナント アプリケーション

*マルチ テナント アプリケーション* とは、Azure AD を持つ会社または組織のユーザーのサインインを許可しているアプリケーションで、別のインスタンス、構成、またはデプロイを必要としないアプリケーションです。 AppSource では、アプリケーションにマルチテナント機能を実装し、*シングル クリック* の無料試用版のエクスペリエンスを有効にすることをお勧めしています。

アプリケーションでマルチテナント機能を有効にするには、次の手順を実行します。
1. [Azure portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) のアプリケーション登録の情報で `Multi-Tenanted` プロパティを `Yes` に設定します。 既定で Azure portal で作成されたアプリケーションは、 *[シングル テナント](#single-tenant-applications)* として構成されます。
1. `common` エンドポイントに要求を送信するようにコードを更新します。 これを行うには、エンドポイントを `https://login.microsoftonline.com/{yourtenant}` から `https://login.microsoftonline.com/common*` に更新します。
1. ASP.NET などの一部のプラットフォームでは、コードを更新して複数の発行者を許可する必要もあります。

マルチテナントの詳細については、[マルチテナント アプリケーション パターンを使用してすべての Azure Active Directory (Azure AD) ユーザーがサインインできるようにする方法](howto-convert-app-to-be-multi-tenant.md)に関するページを参照してください。

### <a name="single-tenant-applications"></a>シングルテナント アプリケーション

*シングル テナント アプリケーション* は、定義された Azure AD インスタンスのユーザーからのサインインのみを許可するアプリケーションです。 外部ユーザー (他の組織の職場または学校のアカウント、あるいは個人のアカウントを含む) は、アプリケーションが登録されている Azure AD インスタンスに各ユーザーをゲスト アカウントとして追加したあと、シングルテナント アプリケーションにサインインできるようになります。 

[Azure AD B2B コラボレーション](../external-identities/what-is-b2b.md)を使用して Azure AD にゲスト アカウントとしてユーザーを追加できます。また、この処理は[プログラム](../../active-directory-b2c/code-samples.md)で実行できます。 B2B を使用する場合、ユーザーは、サインインに招待が必要ないセルフサービス ポータルを作成できます。 詳細については、「[Azure AD B2B コラボレーションのサインアップ用のセルフ サービス ポータル](../external-identities/self-service-portal.md)」を参照してください。

シングルテナント アプリケーションで *お問い合わせ* エクスペリエンスを有効にすることはできますが、AppSource でお勧めしているシングルクリック/無料試用版のエクスペリエンスを有効にする場合は、代わりにアプリケーションのマルチテナント機能を有効にします。

## <a name="appsource-trial-experiences"></a>AppSource の試用エクスペリエンス

### <a name="free-trial-customer-led-trial-experience"></a>無料試用版 (顧客主導の試用エクスペリエンス)

顧客主導の試用は、アプリケーションへのシングルクリックを提供することを AppSource がお勧めするエクスペリエンスです。 このエクスペリエンスは、次の例のように表示されます。

1.  ユーザーが AppSource Web サイトでアプリケーションを見つけ、**無料試用版** オプションを選択します。

    ![顧客主導の試用エクスペリエンスのための無料試用版を示しています](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png)

2.  AppSource により、ユーザーはお客様の Web サイトの URL にリダイレクトされます。 Web サイトでは、*シングル サインオン* プロセスが (ページ読み込み時に) 自動的に開始されます。

    ![ユーザーがお客様の Web サイトの URL にリダイレクトされる様子を示しています](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png)

3.  ユーザーは Microsoft のサインイン ページにリダイレクトされて、サインインするための資格情報を入力します。

    ![Microsoft のサインイン ページを示しています](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png)

4. ユーザーは、お客様のアプリケーションについて同意します。

    ![例:アプリケーションの同意ページ](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png)

5.  サインインが完了し、ユーザーはお客様の Web サイトにリダイレクトされます。  ユーザーが無料試用版を起動します。

    ![ユーザーがお客様のサイトにリダイレクトされたときに表示されるエクスペリエンスを示しています](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png)

### <a name="contact-me-partner-led-trial-experience"></a>お問い合わせ (パートナー主導の試用エクスペリエンス)

パートナー試用エクスペリエンスは、ユーザーや会社を不定期にプロビジョニングする手動の操作または長期にわたる操作が必要となる場合に使用できます。たとえば、アプリケーションで仮想マシン、データベース インスタンス、または完了までに時間がかかる操作をプロビジョニングする必要がある場合などです。 この場合、ユーザーは **[試用版をリクエストする]** を選択してフォームに入力したあとに、AppSource はお客様にユーザーの連絡先情報を送信します。 この情報を受け取ったら、環境をプロビジョニングし、試用エクスペリエンスへのアクセス方法をユーザーに知らせます。<br/><br/>

1. ユーザーが AppSource Web サイトでアプリケーションを見つけ、 **[Contact Me] (連絡してください)** を選択します。

    ![パートナー主導の試用エクスペリエンスのための [Contact Me] (連絡してください) を示しています](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png)

2. ユーザーは、連絡先情報をフォームに記入します。

    ![連絡先情報が記入されたフォームの例を示しています](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png)

3. お客様はユーザーの情報を受け取り、試用版インスタンスを設定し、アプリケーションにアクセスするためのハイパーリンクをユーザーに送信します。

    ![ユーザー情報のプレースホルダーを示しています](./media/active-directory-devhowto-appsource-certified/usercontact.png)

4. ユーザーがお客様のアプリケーションにアクセスし、シングル サインオン プロセスを完了します。

    ![アプリケーションのサインイン画面を示しています](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png)

5. ユーザーは、お客様のアプリケーションについて同意します。

    ![アプリケーションの同意ページの例を示しています](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png)

6. サインインが完了し、ユーザーはお客様の Web サイトにリダイレクトされます。 ユーザーが無料試用版を起動します。

    ![ユーザーがお客様のサイトにリダイレクトされたときに表示されるエクスペリエンスを示しています](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png)

### <a name="more-information"></a>詳細情報

AppSource の試用エクスペリエンスの詳細については、[こちらの動画](https://aka.ms/trialexperienceforwebapps)をご覧ください。 

## <a name="get-support"></a>サポートを受ける

Azure AD との統合について、Microsoft は [Microsoft Q&A](/answers/topics/azure-active-directory.html) のコミュニティでサポートを提供しています。

ご質問は最初に [Microsoft Q&A](/answers/topics/azure-active-directory.html) で挙げていただき、既知の問題を検索して過去に同じ質問が挙がっていないかどうか確認することを強くお勧めします。 自分の質問またはコメントに [`[azure-active-directory]`](/answers/topics/azure-active-directory.html) タグが付けられていることを確認してください。

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。

## <a name="next-steps"></a>次のステップ

- Azure AD のサインインをサポートするアプリケーションの作成の詳細については、「[Azure AD の認証シナリオ](authentication-flows-app-scenarios.md)」を参照してください。
- AppSource で SaaS アプリケーションを一覧表示する方法については、[AppSource パートナーの情報](https://appsource.microsoft.com/partners)に関するページをご覧ください