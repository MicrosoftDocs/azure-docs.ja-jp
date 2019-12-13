---
title: Azure Active Directory の AppSource 認定を取得する方法 | Microsoft Docs
description: Azure Active Directory の AppSource 認定を取得する方法について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/21/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 789b7d29673bca8a7703349ee46f4595fd24c2ea
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917813"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Azure Active Directory の AppSource 認定を取得する方法

[Microsoft AppSource](https://appsource.microsoft.com/) は、ビジネス ユーザーが基幹業務の SaaS アプリケーション (スタンドアロン SaaS と既存の Microsoft SaaS 製品のアドオン) を検索し、試し、管理するためのサービスです。

AppSource でスタンドアロン SaaS アプリケーションを一覧表示するには、Azure Active Directory (Azure AD) を持つ会社または組織の職場アカウントからのシングル サインオンをアプリケーションで許可する必要があります。 サインイン プロセスで [OpenID Connect](v1-protocols-openid-connect-code.md) または [OAuth 2.0](v1-protocols-oauth-code.md) プロトコルを使用する必要があります。 AppSource 認定では SAML 統合はサポートされていません。

## <a name="guides-and-code-samples"></a>ガイドとコード サンプル

OpenID Connect を使用してアプリケーションを Azure AD と統合する方法について学習するには、[Azure Active Directory 開発者ガイド](v1-overview.md#get-started "開発者向けの Azure AD の概要")のページにある説明とコード サンプルに従ってください。

## <a name="multi-tenant-applications"></a>マルチテナント アプリケーション

*マルチ テナント アプリケーション*とは、Azure AD を持つ会社または組織のユーザーのサインインを許可しているアプリケーションで、別のインスタンス、構成、またはデプロイを必要としないアプリケーションです。 AppSource では、アプリケーションにマルチテナント機能を実装し、*シングル クリック*の無料試用版のエクスペリエンスを有効にすることをお勧めしています。

アプリケーションでマルチテナント機能を有効にするには、次の手順を実行します。
1. [Azure portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) のアプリケーション登録の情報で `Multi-Tenanted` プロパティを `Yes` に設定します。 既定で Azure portal で作成されたアプリケーションは、 *[シングル テナント](#single-tenant-applications)* として構成されます。
1. `common` エンドポイントに要求を送信するようにコードを更新します。 これを行うには、エンドポイントを `https://login.microsoftonline.com/{yourtenant}` から `https://login.microsoftonline.com/common*` に更新します。
1. ASP. NET などの一部のプラットフォームでは、コードを更新して複数の発行者を許可する必要もあります。

マルチテナントの詳細については、[マルチテナント アプリケーション パターンを使用してすべての Azure Active Directory (Azure AD) ユーザーがサインインできるようにする方法](howto-convert-app-to-be-multi-tenant.md)に関するページを参照してください。

### <a name="single-tenant-applications"></a>シングルテナント アプリケーション

*シングル テナント アプリケーション*は、定義された Azure AD インスタンスのユーザーからのサインインのみを許可するアプリケーションです。 外部ユーザー (他の組織の職場または学校のアカウント、あるいは個人のアカウントを含む) は、アプリケーションが登録されている Azure AD インスタンスに各ユーザーをゲスト アカウントとして追加したあと、シングルテナント アプリケーションにサインインできるようになります。 

[Azure AD B2B コラボレーション](../b2b/what-is-b2b.md)を使用して Azure AD にゲスト アカウントとしてユーザーを追加できます。また、この処理は[プログラム](../../active-directory-b2c/code-samples.md)で実行できます。 B2B を使用する場合、ユーザーは、サインインに招待が必要ないセルフサービス ポータルを作成できます。 詳細については、「[Azure AD B2B コラボレーションのサインアップ用のセルフ サービス ポータル](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal)」を参照してください。

シングルテナント アプリケーションで*お問い合わせ*エクスペリエンスを有効にすることはできますが、AppSource でお勧めしているシングルクリック/無料試用版のエクスペリエンスを有効にする場合は、代わりにアプリケーションのマルチテナント機能を有効にします。

## <a name="appsource-trial-experiences"></a>AppSource の試用エクスペリエンス

### <a name="free-trial-customer-led-trial-experience"></a>無料試用版 (顧客主導の試用エクスペリエンス)

顧客主導の試用は、アプリケーションへのシングルクリックを提供することを AppSource がお勧めするエクスペリエンスです。 このエクスペリエンスは、次の例のように表示されます。

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%" alt-text="Shows Free trial for customer-led trial experience"/><ul><li>ユーザーが AppSource Web サイトでお客様のアプリケーションを見つけます</li><li>[無料試用版] オプションを選択します</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" alt-text="Shows how user is redirected to a URL in your web site"/><ul><li>AppSource により、ユーザーはお客様の Web サイトの URL にリダイレクトされます</li><li>Web サイトでは<i>シングル サインオン</i> プロセスが (ページの読み込み時に) 自動的に開始されます</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%" alt-text="Shows the Microsoft sign-in page"/><ul><li>ユーザーは Microsoft のサインイン ページにリダイレクトされます</li><li>ユーザーはサインインに必要な資格情報を入力します</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%" alt-text="Example: Consent page for an application"/><ul><li>ユーザーはお客様のアプリケーションに同意します</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>サインインが完了し、ユーザーはお客様の Web サイトにリダイレクトされます</li><li>ユーザーが無料試用版の試用を開始します</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>お問い合わせ (パートナー主導の試用エクスペリエンス)

パートナー試用エクスペリエンスは、ユーザーや会社を不定期にプロビジョニングする手動の操作または長期にわたる操作が必要となる場合に使用できます。たとえば、アプリケーションで仮想マシン、データベース インスタンス、または完了までに時間がかかる操作をプロビジョニングする必要がある場合などです。 この場合、ユーザーは **[試用版をリクエストする]** を選択してフォームに入力したあとに、AppSource はお客様にユーザーの連絡先情報を送信します。 この情報を受け取ったら、環境をプロビジョニングし、試用エクスペリエンスへのアクセス方法をユーザーに知らせます。<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%" alt-text="Shows Contact me for partner-led trial experience"/><ul><li>ユーザーが AppSource Web サイトでお客様のアプリケーションを見つけます</li><li>[連絡する] オプションを選択します</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%" alt-text="Shows an example form with contact info"/><ul><li>フォームに連絡先情報を入力します</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/UserContact.png" width="55%" alt-text="Shows placeholder for user information"/></td>
            <td>ユーザー情報を受け取ります</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/SetupEnv.png" width="55%" alt-text="Shows placeholder for setup environment info"/></td>
            <td>環境をセットアップします</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/ContactCustomer.png" width="55%" alt-text="Shows placeholder for trial info"/></td>
            <td>ユーザーに試用版の情報を連絡します</td>
        </tr>
        </table><br/><br/>
        <ul><li>ユーザー情報を受け取り、試用版インスタンスをセットアップします</li><li>お客様のアプリケーションにアクセスするためのハイパーリンクをユーザーに送信します</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%" alt-text="Shows the application sign-in screen"/><ul><li>ユーザーがお客様のアプリケーションにアクセスし、シングル サインオン プロセスを完了します</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%" alt-text="Shows an example consent page for an application"/><ul><li>ユーザーはお客様のアプリケーションに同意します</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>サインインが完了し、ユーザーはお客様の Web サイトにリダイレクトされます</li><li>ユーザーが無料試用版の試用を開始します</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>詳細情報

AppSource の試用エクスペリエンスの詳細については、[こちらの動画](https://aka.ms/trialexperienceforwebapps)をご覧ください。 

## <a name="next-steps"></a>次の手順

- Azure AD のサインインをサポートするアプリケーションの作成の詳細については、「[Azure AD の認証シナリオ](https://docs.microsoft.com/azure/active-directory/develop/v1-authentication-scenarios)」を参照してください。
- AppSource で SaaS アプリケーションを一覧表示する方法については、[AppSource パートナーの情報](https://appsource.microsoft.com/partners)に関するページをご覧ください

## <a name="get-support"></a>サポートを受ける

Azure AD との統合について、Microsoft は [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource) のコミュニティでサポートを提供しています。

ご質問は最初に Stack Overflow で挙げていただき、既知の問題を検索して過去に同じ質問が挙がっていないかどうか確認することを強くお勧めします。 ご質問またはコメントに [`[azure-active-directory]` タグと `[appsource]`](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource) タグが付けられていることを確認してください。

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。

<!--Reference style links -->
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:v1-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started

<!--Image references-->
