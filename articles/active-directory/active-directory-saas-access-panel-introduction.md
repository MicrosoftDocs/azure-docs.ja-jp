---
title: "Azure Active Directory のアクセス パネルの概要 | Microsoft Docs"
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
ms.date: 07/31/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bd9066c251188c0f18fe1a9403baa2beaeeb987c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-the-access-panel"></a>アクセス パネルの概要

アクセス パネルは Web ベースのポータルです。 Azure Active Directory の職場または学校アカウントを持っているユーザーは、Azure AD 管理者によってアクセスを許可されたクラウドベースのアプリケーションを表示して起動することができます。 また、アクセス パネルからは、セルフサービスのグループおよびアプリ管理機能を利用することもできます。

アクセス パネルは Azure Portal から独立しており、ユーザーが Azure サブスクリプションを持っている必要はありません。

![アクセス パネル][1]

アクセス パネルでは、ユーザーは自分のプロファイル設定を編集することができ、次のことができます。

- 職場または学校アカウントに関連付けられているパスワードを変更する

- パスワードのリセット設定を編集する

- 多要素認証に関連する連絡先および基本設定を編集する (管理者によって多要素認証の使用を要求されているアカウントの場合)

- ユーザー ID、連絡用メール アドレス、携帯電話番号、会社電話番号、デバイスなどのアカウント詳細を表示する

- Azure AD 管理者によってアクセスを許可されたクラウドベースのアプリケーションを表示および起動する。 エンド ユーザーから見たアクセス パネルについて詳しくは、アクセス パネルの使用に関するトピックをご覧ください。 

- グループを自己管理する。 具体的には、管理者がセキュリティ グループを作成および管理し、Azure AD のセキュリティ グループのメンバーシップを要求することができます。 詳細については、[Azure AD でのユーザーのセルフ サービス グループ管理](active-directory-accessmanagement-self-service-group-management.md)に関する記事および[グループの管理](active-directory-manage-groups.md)に関する記事をご覧ください。




## <a name="accessing-the-access-panel"></a>アクセス パネルへのアクセス

Web ブラウザーで次の URL にアクセスすることで、アクセス パネルを表示できます。`http://myapps.microsoft.com`

サインイン ページ用にカスタム ブランドを構成している場合は、次のように URL の末尾に組織のドメインを追加することで、このブランドを読み込むことができます。`http://myapps.microsoft.com/<your domain>.com`

この場合、Azure Portal で構成されている任意のアクティブなドメイン名または検証済みドメイン名を使うことができます。

![Wingtip Toys のドメイン名][2]  

Azure AD と統合されたアプリケーションにサインインするすべてのユーザーに、URL を配布する必要があります。

## <a name="authentication"></a>認証

アクセス パネルにアクセスするには、Azure AD で職場または学校アカウントによる認証を行う必要があります。 Azure AD で直接認証を行うことができます。 または、組織が Active Directory フェデレーション サービス (AD FS) などのテクノロジを使ってフェデレーションを構成している場合は、Windows Server Active Directory で認証を行うこともできます。

Azure または Office 365 のサブスクリプションを持っていて、Azure Portal または Office 365 アプリケーションを使っている場合は、サインインし直さなくてもアプリケーションの一覧が表示されます。 認証を行っていない場合は、Azure AD アカウントのユーザー名とパスワードを使ってサインインするように求められます。 組織がフェデレーションを構成している場合は、ユーザー名を入力するだけで十分です。

認証を行うと、管理者によってディレクトリに統合されているアプリケーションを使うことができるようになります。 アプリケーションを Azure AD に統合する方法については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="web-browser-requirements"></a>Web ブラウザーの要件

アクセス パネルには、少なくとも、JavaScript をサポートする CSS 対応のブラウザーが必要です。 ユーザーがパスワード ベースのシングル サインオン (SSO) を使ってアプリケーションにサインインするためには、ブラウザーにアクセス パネルの拡張機能をインストールする必要があります。 この拡張機能は、パスワード ベースの SSO 用に構成されているアプリケーションを選ぶと、自動的にダウンロードされます。

現時点では、アクセス パネルの拡張機能は、Internet Explorer 8 以上、Edge、Chrome、および Firefox ブラウザーで使用できます。

## <a name="mobile-app-support"></a>モバイル アプリのサポート

マイ アプリは、Azure Active Directory チームが公開するモバイル アプリです。 アプリをインストールすると、iOS および Android デバイス上のパスワード ベースの SSO アプリケーションにサインインできます。

> [!NOTE]
> Azure AD とのフェデレーションをサポートするアプリケーション (Salesforce、Google Apps、Dropbox、Box、Concur、Workday、Office 365 など 70 を超えるアプリケーション) には、プラグインやモバイル アプリがなくても、事実上すべてのデバイスのすべての Web ブラウザーからサインインすることができます。 他のすべての[アクセス パネル エクスペリエンス](https://myapps.microsoft.com/)も、マイ アプリ モバイル アプリがインストールされていないモバイル デバイスで使うことができます。
>
>

### <a name="my-apps-for-android"></a>Android 用のマイ アプリ

Android 用のマイ アプリは、Android バージョン 4.1 以上を実行するすべての Android デバイスでサポートされています。  
このアプリは、[Google Play ストア](https://play.google.com/store/apps/details?id=com.microsoft.myapps)で入手できます。

![Android 用のマイ アプリ][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>IPhone および iPad 用のマイ アプリ

iOS 用のマイ アプリは、iOS バージョン 7 以上を実行するすべての iPhone および iPad でサポートされています。  
このアプリは、[Apple App Store](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8) で入手できます。

![iOS 用のマイ アプリ][4]    



## <a name="managed-browser-for-my-apps"></a>マイ アプリ用の Managed Browser

マイ アプリは、Intune Managed Browser にも統合されています。 IOS および Android デバイス用の Intune Managed Browser は、モバイル デバイス上のデータを安全な状態に維持する重要な役割を持ちます。 Managed Browser を使うと、会社の情報が含まれる Web ページを安全に表示して Web ページ内を移動でき、セキュリティで保護された Web 閲覧環境が提供されます。  
Managed Browser のホームページおよびブックマークではマイ アプリに簡単にアクセスでき、わずかな操作で目的のアプリケーションに到達できます。

Managed Browser は、[Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) および [Google Play ストア](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser&hl=en)で入手できます。

![マイ アプリ用の Managed Browser][5]    





## <a name="tips-for-testing-the-user-experience"></a>エンド ユーザー エクスペリエンスをテストするためのヒント

Azure 管理者がディレクトリのアカウントを使って Azure Portal にサインインしている場合は、現在のアカウントとしてアクセス パネルに自動的にサインインされます。 この場合は、ユーザーに割り当てられているすべてのアプリケーションが表示されます。

**"*別の*" ユーザー アカウントとしてテストするには:**

1. Azure Portal またはアクセス パネルの右上隅にあるユーザー メニューをクリックして、**[サインアウト]** を選びます。 
2. [アクセス パネル](http://myapps.microsoft.com)に移動します。
3. サインイン ページで、テストするディレクトリのアカウントのユーザー名とパスワードを入力します。


## <a name="starting-applications"></a>アプリケーションの起動

アクセス パネルに表示できるアプリケーションには、いくつかの種類があります。

### <a name="office-365-applications"></a>Office 365 アプリケーション

組織が Office 365 アプリケーションを使っていて、ユーザーがこれらのアプリケーションのライセンスを持っている場合、Office 365 アプリケーションがユーザーのアクセス パネルに表示されます。

ユーザーは、Office 365 アプリケーションのアプリケーション タイルをクリックすると、アプリケーションにリダイレクトされて自動的にサインインされます。

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>フェデレーション ベースの SSO で構成されたマイクロソフトとサード パーティのアプリケーション

管理者は、SSO モードを **Azure AD のシングル サインオン**に設定することで、Azure Portal の Active Directory セクションにアプリケーションを追加できます。 ユーザーの場合は、管理者がアプリケーションへのアクセス権をユーザーに明示的に付与している場合にのみ、これらのアプリケーションが表示されます。

ユーザーは、これらのアプリケーションの 1 つのタイルをクリックすると、アプリケーションにリダイレクトされて自動的にサインインされます。

### <a name="password-based-sso-without-identity-provisioning"></a>パスワード ベースの SSO (ID プロビジョニングなし)

管理者は、SSO モードを**パスワードベースのシングル サインオン**に設定することで、Azure Portal の Active Directory セクションにアプリケーションを追加できます。 ディレクトリ内のすべてのユーザーに、このモードで構成されているすべてのアプリケーションが表示されます。

これらのアプリケーションの 1 つのタイルを初めてクリックすると、Internet Explorer または Chrome のパスワード SSO プラグインをインストールするように求められます。 インストールするには、Web ブラウザーの再起動が必要になる場合があります。 アクセス パネルに戻り、アプリケーションのタイルを再度クリックすると、アプリケーションのユーザー名とパスワードの入力を求められます。 ユーザー名とパスワードを入力すると、この資格情報はセキュリティで保護されて格納され、Azure AD のアカウントにリンクされます。

ユーザーが次にこのアプリケーション タイルをクリックすると、アプリケーションに自動的にサインインされます。  
資格情報を再入力したり、パスワード SSO プラグインをインストールしたりする必要はありません。

ターゲットのサードパーティ アプリケーションで資格情報を変更した場合は、Azure AD に格納されている資格情報も更新する必要があります。 

**資格情報を更新するには:**

1. アプリケーションのタイルでアイコンを選びます。
2. **[資格情報の更新]** を選び、アプリケーションのユーザー名とパスワードを再入力します。


### <a name="password-based-sso-with-identity-provisioning"></a>パスワード ベースの SSO (ID プロビジョニングあり)

管理者は、SSO モードを**パスワードベースのシングル サインオン**に設定し、ID プロビジョニングを設定することで、Azure Portal の **Active Directory** セクションにアプリケーションを追加できます。

これらのアプリケーションの 1 つのアプリケーション タイルを初めてクリックすると、**Internet Explorer または Chrome のパスワード SSO プラグイン**をインストールするように求められます。 インストールするには、Web ブラウザーの再起動が必要になる場合があります。  
アクセス パネルに戻り、アプリケーション タイルを再度クリックすると、アプリケーションに自動的にサインインされます。

一部のアプリケーションでは、最初のサインイン時にパスワードを変更する必要があります。 ターゲットのサードパーティ アプリケーションで資格情報を変更した場合は、Azure AD に格納されている資格情報も更新する必要があります。 

**資格情報を更新するには:**

1. アプリケーションのタイルでアイコンを選びます。
2. **[資格情報の更新]** を選び、アプリケーションのユーザー名とパスワードを再入力します。


### <a name="application-with-existing-sso-solutions"></a>既存の SSO ソリューションとアプリケーション

Azure Portal でアプリケーションの SSO を構成する場合、第 3 のオプションとして**既存のシングル サインオン**が用意されています。 このオプションを選んだ場合、管理者は、アプリケーションへのリンクを作成し、これを選んだユーザーのアクセス パネルに配置できます。

たとえば、AD FS 2.0 を使ってユーザーを認証するように構成されたアプリケーションがある場合、管理者は、**[既存のシングル サインオン]** オプションを使って、アクセス パネルにそのアプリケーションへのリンクを作成することができます。 ユーザーがこのリンクにアクセスすると、ユーザーは AD FS 2.0 またはアプリケーションによって提供された任意の既存の SSO ソリューションを使って認証されます。


## <a name="next-steps"></a>次のステップ

- アプリケーション管理に関するすべてのトピックの一覧を見るには、[Azure Active Directory でのアプリケーション管理に関する記事の索引](active-directory-apps-index.md)をご覧ください。
 
- SaaS アプリを Azure AD に統合する方法については、[SaaS アプリの統合方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)をご覧ください。
 
- Azure AD でのアプリの管理について詳しくは、[Azure Active Directory でのシングル サインオンとアプリのアクセスの管理の概要](active-directory-appssoaccess-whatis.md)に関する記事をご覧ください。
 
- ユーザーのプロビジョニングについて詳しくは、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](active-directory-saas-app-provisioning.md)」をご覧ください。

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/01.png
[2]: ./media/active-directory-saas-access-panel-introduction/02.png
[3]: ./media/active-directory-saas-access-panel-introduction/03.png
[4]: ./media/active-directory-saas-access-panel-introduction/04.png
[5]: ./media/active-directory-saas-access-panel-introduction/05.png
