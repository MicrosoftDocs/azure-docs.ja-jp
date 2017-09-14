---
title: "Azure Active Directory の AppSource 認定を取得する方法 | Microsoft Docs"
description: "Azure Active Directory の AppSource 認定を取得する方法について説明します。"
services: active-directory
documentationcenter: 
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/03/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: d8e2f8fc19ff879e6a7b632f033fd0ed9d77392a
ms.contentlocale: ja-jp
ms.lasthandoff: 08/04/2017

---

# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Azure Active Directory の AppSource 認定を取得する方法
[Microsoft AppSource](https://appsource.microsoft.com/) は、ビジネス ユーザーが基幹業務の SaaS アプリケーション (スタンドアロン SaaS と既存の Microsoft SaaS 製品のアドオン) を検索し、試し、管理するためのサービスです。

AppSource でスタンドアロン SaaS アプリケーションを一覧表示するには、Azure Active Directory を持つ会社または組織の職場アカウントからのシングル サインオンをアプリケーションで許可する必要があります。 サインイン プロセスで [OpenID Connect](./active-directory-protocols-openid-connect-code.md) または [OAuth 2.0](./active-directory-protocols-oauth-code.md) プロトコルを使用する必要があります。 AppSource 認定では SAML 統合はサポートされていません。

## <a name="guides-and-code-samples"></a>ガイドとコード サンプル
OpenID Connect を使用してアプリケーションを Azure Active Directory と統合する方法について学習するには、[Azure Active Directory 開発者ガイド](./active-directory-developers-guide.md#get-started "の開発者用 Azure AD で作業開始")に関するページにある説明とコード サンプルに従ってください。

## <a name="multi-tenant-applications"></a>マルチテナント アプリケーション

Azure Active Directory を持つ会社または組織のユーザーのサインインを許可しているアプリケーションで、別のインスタンス、構成、またはデプロイを必要としないものは*マルチ テナント アプリケーション*と呼ばれています。 AppSource では、アプリケーションにマルチテナント機能を実装し、*シングル クリック*の無料試用版のエクスペリエンスを有効にすることをお勧めしています。

アプリケーションでマルチテナント機能を有効にするには
- [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) のアプリケーションの登録の情報画面で `Multi-Tenanted` プロパティを `Yes` に設定します (既定では、Azure Portal で作成されたアプリケーションは*シングルテナント*として構成されます)
- コードを更新して要求を `common` エンドポイントに送信します (エンドポイントを *https://login.microsoftonline.com/{yourtenant}* から *https://login.microsoftonline.com/common* に更新します)
- ASP.NET などの一部のプラットフォームでは、コードを更新して複数の発行者を許可する必要もあります

マルチテナントの詳細については、「[マルチテナント アプリケーション パターンを使用してすべての Azure Active Directory (AD) ユーザーがサインインできるようにする方法](./active-directory-devhowto-multi-tenant-overview.md)」をご覧ください。

### <a name="single-tenant-applications"></a>シングルテナント アプリケーション
定義された Azure Active Directory インスタンスのユーザーのサインインのみを許可しているアプリケーションは、*シングルテナント アプリケーション*と呼ばれています。 外部ユーザー (他の組織の職場または学校のアカウント、あるいは個人のアカウントを含む) は、アプリケーションが登録されている Azure Active Directory インスタンスに各ユーザーを*ゲスト アカウント*として追加したあと、シングルテナント アプリケーションにサインインできるようになります。 [*Azure AD B2B コラボレーション*](../active-directory-b2b-what-is-azure-ad-b2b.md)を経由して、ユーザーをゲスト アカウントとして Azure Active Directory に追加できます。また、[プログラムで](../active-directory-b2b-code-samples.md)追加することもできます。 ユーザーをゲスト アカウントとして Azure Active Directory に追加すると、招待メールがユーザーに送信されます。ユーザーは招待メール内のリンクをクリックして招待を承認する必要があります。 招待を行った組織が取引先組織のメンバーでもある場合は、その組織の追加ユーザーに送信された招待メール上でユーザーがサインインの招待を承認する必要はありません。

シングルテナント アプリケーションで*お問い合わせ*エクスペリエンスを有効にすることはできますが、AppSource でお勧めしているシングルクリック/無料試用版のエクスペリエンスを有効にする場合は、代わりにアプリケーションのマルチテナント機能を有効にします。


## <a name="appsource-trial-experiences"></a>AppSource の試用エクスペリエンス

### <a name="free-trial-customer-led-trial-experience"></a>無料試用版 (顧客主導の試用エクスペリエンス) 
*顧客主導の試用*は、アプリケーションへのシングルクリックを提供することを AppSource がお勧めするエクスペリエンスです。 次の図は、このエクスペリエンスの流れを示します。<br/><br/>

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%"/><ul><li>ユーザーが AppSource Web サイトでお客様のアプリケーションを見つけます</li><li>[無料試用版] オプションを選択します</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" /><ul><li>AppSource により、ユーザーはお客様の Web サイトの URL にリダイレクトされます</li><li>Web サイトでは<i>シングル サインオン</i> プロセスが (ページの読み込み時に) 自動的に開始されます</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%"/><ul><li>ユーザーは Microsoft のサインイン ページにリダイレクトされます</li><li>ユーザーはサインインに必要な資格情報を入力します</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%"/><ul><li>ユーザーはお客様のアプリケーションに同意します</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>サインインが完了し、ユーザーはお客様の Web サイトにリダイレクトされます</li><li>ユーザーが無料試用版の試用を開始します</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>お問い合わせ (パートナー主導の試用エクスペリエンス)
*パートナー試用エクスペリエンス*は、ユーザーや会社を不定期にプロビジョニングする手動の操作または長期にわたる操作が必要となる場合に使用できます。たとえば、アプリケーションで仮想マシン、データベース インスタンス、または完了までに時間がかかる操作をプロビジョニングする必要がある場合などです。 この場合、ユーザーは *[試用版をリクエストする]* を選択してフォームに入力したあとに、AppSource はお客様にユーザーの連絡先情報を送信します。 この情報を受け取ったら、環境をプロビジョニングし、試用エクスペリエンスへのアクセス方法をユーザーに知らせます。<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%"/><ul><li>ユーザーが AppSource Web サイトでお客様のアプリケーションを見つけます</li><li>[連絡する] オプションを選択します</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%"/><ul><li>フォームに連絡先情報を入力します</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/UserContact.png" width="55%"/></td>
            <td>ユーザー情報を受け取ります</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/SetupEnv.png" width="55%"/></td>
            <td>環境をセットアップします</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/ContactCustomer.png" width="55%"/></td>
            <td>ユーザーに試用版の情報を連絡します</td>
        </tr>
        </table><br/><br/>
        <ul><li>ユーザー情報を受け取り、試用版インスタンスをセットアップします</li><li>お客様のアプリケーションにアクセスするためのハイパーリンクをユーザーに送信します</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%"/><ul><li>ユーザーがお客様のアプリケーションにアクセスし、シングル サインオン プロセスを完了します</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%"/><ul><li>ユーザーはお客様のアプリケーションに同意します</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>サインインが完了し、ユーザーはお客様の Web サイトにリダイレクトされます</li><li>ユーザーが無料試用版の試用を開始します</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>詳細情報
AppSource の試用エクスペリエンスの詳細については、[こちらの動画](https://aka.ms/trialexperienceforwebapps)をご覧ください。 
 
## <a name="next-steps"></a>次のステップ

- Azure Active Directory のサインインをサポートするアプリケーションの作成の詳細については、「[Azure AD の認証シナリオ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios)」をご覧ください 

- AppSource で SaaS アプリケーションを一覧表示する方法については、[AppSource パートナーの情報](https://appsource.microsoft.com/partners)に関するページをご覧ください


## <a name="get-support"></a>サポートを受ける
Azure Active Directory との統合について、Microsoft は [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory) のコミュニティでサポートを提供しています。 

ご質問は最初に Stack Overflow で挙げていただき、既知の問題を検索して過去に同じ質問が挙がっていないかどうか確認することを強くお勧めします。 ご質問またはコメントに `[azure-active-directory]` タグが付けられていることを確認してください。

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#get-started


<!--Image references-->
