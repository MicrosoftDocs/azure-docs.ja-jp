---
title: "アクセス パネルの概要 | Microsoft Docs"
description: "アクセス パネルの各種構成 (Web ブラウザー、Android アプリ、iPhone および iPad アプリ) を使用して、SaaS アプリにアクセスする方法について説明します。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: d6330397b6e74b62836a2e864b1c37ab172d0028
ms.lasthandoff: 03/15/2017


---
# <a name="what-is-the-access-panel"></a>アクセス パネルとは
アクセス パネルは Web ベースのポータルです。 Azure Active Directory (Azure AD) の職場または学校アカウントを持つユーザーが、Azure AD 管理者によってアクセスを許可されたクラウドベースのアプリケーションを表示および起動することができます。 Azure AD のエディションを持つユーザーは、アクセス パネルを介してセルフサービスのグループ管理機能を利用することもできます。

アクセス パネルは Azure Portal から独立していて、ユーザーが Azure サブスクリプションを持っている必要はありません。

![アクセス パネル][1]

アクセス パネルでは、ユーザーは自分のプロファイル設定を編集することができ、次のことができます。

* 職場または学校アカウントに関連付けられているパスワードを変更する。
* パスワードのリセット設定を編集する。
* Multi-Factor Authentication に関連する連絡先および基本設定 (管理者が使用する必要があるアカウントの設定) を編集する。
* ユーザー ID、連絡用メール アドレス、携帯電話番号、会社電話番号などのアカウント詳細を表示する。
* Azure AD 管理者によってアクセスを許可されたクラウドベースのアプリケーションを表示および起動する。 エンド ユーザーから見たアクセス パネルの詳細については、「[アクセス パネルの使用](https://msdn.microsoft.com/library/azure/dn756411.aspx)」を参照してください。
* グループを自己管理する。 具体的には、管理者がセキュリティ グループを作成および管理し、Azure AD のセキュリティ グループのメンバーシップを要求することができます。 詳細については、[Azure AD でのユーザーのセルフ サービス グループ管理](active-directory-accessmanagement-self-service-group-management.md)に関する記事および[グループの管理](active-directory-manage-groups.md)に関する記事をご覧ください。

## <a name="accessing-the-access-panel"></a>アクセス パネルへのアクセス
アクセス パネルにアクセスするには、Web ブラウザーで次の URL にアクセスします。 <br>
**http://myapps.microsoft.com**

サインイン ページ用にカスタム ブランドを構成している場合は、次のように URL の末尾に組織のドメインを追加することで、このブランドを既定で読み込むことができます。 <br>
**http://myapps.microsoft.com/contosobuild.com**

この場合は、次のスクリーン ショットに示すように、Azure Portal のディレクトリの **[ドメイン]** タブで構成されている、アクティブまたは確認済みのドメイン名を使用できます。

![Wingtip Toys のドメイン名][2]  

この URL は、Azure AD と統合されたアプリケーションにサインインするすべてのユーザーに配布する必要があります。

## <a name="authentication"></a>認証
アクセス パネルにアクセスするには、Azure AD で、職場または学校アカウントを使用してユーザーを認証する必要があります。 ユーザーは、Azure AD で直接認証できます。 または、組織が Active Directory Federation Services (AD FS) などのテクノロジを使用してフェデレーションを構成している場合は、Windows Server Active Directory でユーザーを認証することもできます。

ユーザーが Azure または Office 365 のサブスクリプションを保持し、Azure Portal または Office 365 アプリケーションを使用している場合は、サインインし直さなくてもアプリケーションの一覧が表示されます。 認証されていないユーザーは、Azure AD アカウントのユーザー名とパスワードを使用してサインインするように求められます。 組織がフェデレーションを構成している場合は、ユーザー名を入力するだけで十分です。

ユーザーは認証されると、管理者によってディレクトリに統合されているアプリケーションを使用できるようになります。 アプリケーションを Azure AD に統合する方法については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="web-browser-requirements"></a>Web ブラウザーの要件
アクセス パネルには、少なくとも、JavaScript をサポートする CSS 対応のブラウザーが必要です。 ユーザーがパスワード ベースのシングル サインオン (SSO) を使用してアプリケーションにサインインするためには、ユーザーのブラウザーにアクセス パネルの拡張機能をインストールする必要があります。 このアクセス パネルの拡張機能は、ユーザーがパスワード ベースの SSO 用に構成されているアプリケーションを選択したときに、自動的にダウンロードされます。

現時点では、アクセス パネルの拡張機能は、Internet Explorer 8 以上、Chrome、および Firefox ブラウザーで使用できます。

## <a name="mobile-app-support"></a>モバイル アプリのサポート
マイ アプリは、Azure Active Directory チームが発行するモバイル アプリです。 ユーザーは、このアプリをインストールして、iOS および Android デバイス上のパスワード ベースの SSO アプリケーションにサインインすることができます。

> [!NOTE]
> Azure AD とのフェデレーションをサポートするアプリケーション (Salesforce、Google Apps、Dropbox、Box、Concur、Workday、Office 365 など 70 を超えるアプリケーション) には、プラグインやモバイル アプリがなくても、事実上すべてのデバイスのすべての Web ブラウザーからサインインすることができます。 [アクセス パネル](https://myapps.microsoft.com/)の残りの部分 は、マイ アプリ モバイル アプリがなくても、モバイル デバイスで操作できます。
>
>

### <a name="my-apps-for-android"></a>Android 用のマイ アプリ
Android 用のマイ アプリは、Android バージョン 4.1 以上を実行するすべての Android デバイスでサポートされています。 このアプリは現在、[Google Play ストア](https://play.google.com/store/apps/details?id=com.microsoft.myapps)で入手できます。

![Android 用のマイ アプリの画面][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>IPhone および iPad 用のマイ アプリ
iOS 用のマイ アプリは、iOS バージョン 7 以上を実行するすべての iPhone および iPad でサポートされています。 このアプリは現在、Apple App Store で入手できます。

![iOS 用のマイ アプリの画面][4]    

## <a name="tips-for-testing-the-user-experience"></a>エンド ユーザー エクスペリエンスをテストするためのヒント
Azure 管理者がディレクトリのアカウントを使用して Azure Portal にサインインしている場合は、現在の管理者アカウントとして自動的にアクセス パネルにサインインされます。 この場合は、このアカウントに割り当てられているすべてのアプリケーションが表示されます。

*別のユーザー アカウントとしてテストするには:*

1. Azure Portal またはアクセス パネルの右上隅にあるユーザー メニューをクリックして、**[サインアウト]** を選択します。 これで、Azure AD からサインアウトされます。
2. [アクセス パネル](http://myapps.microsoft.com)に移動します。
3. サインイン ページで、テストするディレクトリのアカウントのユーザー名とパスワードを入力します。

## <a name="starting-applications"></a>アプリケーションの起動
アクセス パネルに表示できるアプリケーションには、いくつかの種類があります。

### <a name="office-365-applications"></a>Office 365 アプリケーション
組織が Office 365 アプリケーションを使用していて、ユーザーがこれらのアプリケーションについてライセンスされている場合は、Office 365 アプリケーションがユーザーのアクセス パネルに表示されます。

ユーザーは、Office 365 アプリケーションのアプリケーション タイルをクリックすると、アプリケーションにリダイレクトされ自動的にサインインされます。

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>フェデレーション ベースの SSO で構成されたマイクロソフトとサード パーティのアプリケーション
管理者は、SSO モードを "**Azure AD のシングル サインオン**" に設定することで、Azure Portal の Active Directory セクションにアプリケーションを追加できます。 ユーザーには、管理者によって明示的にアクセス権が付与されてい場合にのみ、これらのアプリケーションが表示されます。

ユーザーは、これらのアプリケーションの&1; つのタイルをクリックすると、アプリケーションにリダイレクトされ自動的にサインインされます。

### <a name="password-based-sso-without-identity-provisioning"></a>パスワード ベースの SSO (ID プロビジョニングなし)
管理者は、SSO モードを "**パスワードベースのシングル サインオン**" に設定することで、Azure Portal の Active Directory セクションにアプリケーションを追加できます。 ディレクトリ内のすべてのユーザーに、このモードで構成されているすべてのアプリケーションが表示されます。

これらのアプリケーションの&1; つのタイルをユーザーが初めてクリックすると、Internet Explorer または Chrome のパスワード SSO プラグインをインストールするように求められます。 インストールするには、Web ブラウザーの再起動が必要になる場合があります。 アクセス パネルに戻り、アプリケーション タイルを再度クリックすると、アプリケーションのユーザー名とパスワードを求められます。 ユーザー名とパスワードを入力すると、この資格情報は Azure AD にセキュリティで保護されて格納され、Azure AD のアカウントにリンクされます。

ユーザーが次にこのアプリケーション タイルをクリックすると、アプリケーションに自動的にサインインされます。 資格情報を再入力したり、パスワード SSO プラグインを再度インストールしたりする必要はありません。

ユーザーは、ターゲットのサードパーティのアプリケーションでユーザーの資格情報を変更した場合は、Azure AD に格納されている資格情報も更新する必要があります。 資格情報を更新するには、ユーザーは、アプリケーション タイルにあるアイコンを選択し、[**資格情報の更新**] を選択して、そのアプリケーションのユーザー名とパスワードを再入力する必要があります。

### <a name="password-based-sso-with-identity-provisioning"></a>パスワード ベースの SSO (ID プロビジョニングあり)
管理者は、SSO モードを "**パスワードベースのシングル サインオン**" に設定し、ID プロビジョニングを設定することで、Azure Portal の Active Directory セクションにアプリケーションを追加できます。

これらのアプリケーションの&1; つのアプリケーション タイルをユーザーが初めてクリックすると、Internet Explorer または Chrome のパスワード SSO プラグインをインストールするように求められます。 インストールするには、Web ブラウザーの再起動が必要になる場合があります。 アクセス パネルに戻り、アプリケーション タイルを再度クリックすると、アプリケーションに自動的にサインインされます。

一部のアプリケーションでは、ユーザーは、最初のサインイン時にパスワードを変更する必要があります。 ユーザーは、ターゲットのサードパーティのアプリケーションでユーザーの資格情報を変更した場合は、Azure AD に格納されている資格情報も更新する必要があります。 資格情報を更新するには、ユーザーは、アプリケーション タイルにあるアイコンを選択し、[**資格情報の更新**] を選択して、そのアプリケーションのユーザー名とパスワードを再入力する必要があります。

### <a name="application-with-existing-sso-solutions"></a>既存の SSO ソリューションとアプリケーション
Azure Portal でアプリケーションの SSO を構成する場合、第&3; のオプションとして "**既存のシングル サインオン**" が用意されています。 このオプションを選択した場合、管理者は、アプリケーションへのリンクを作成し、これを選択したユーザーのアクセス パネルに配置できます。

たとえば、AD FS 2.0 を使用してユーザーを認証するように構成されたアプリケーションがある場合、管理者は、**[既存のシングル サインオン]** オプションを使用して、アクセス パネルにそのアプリケーションへのリンクを作成することができます。 ユーザーがこのリンクにアクセスすると、ユーザーは AD FS 2.0 またはアプリケーションによって提供された任意の既存の SSO ソリューションを使用して認証されます。

## <a name="related-articles"></a>関連記事
* [Azure Active Directory のアプリケーション構成の管理に関する記事の索引](active-directory-apps-index.md)
* [SaaS アプリを統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のシングル サインオンとアプリケーション アクセスの管理とは](active-directory-appssoaccess-whatis.md)
* [SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](active-directory-saas-app-provisioning.md)

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/ic767166.png
[2]: ./media/active-directory-saas-access-panel-introduction/ic767167.png
[3]: ./media/active-directory-saas-access-panel-introduction/ic767168.png
[4]: ./media/active-directory-saas-access-panel-introduction/ic767169.png

