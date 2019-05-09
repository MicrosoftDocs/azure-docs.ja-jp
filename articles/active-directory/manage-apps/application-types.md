---
title: アプリケーション リストに予期しないアプリケーションがある | Microsoft Docs
description: テナントのすべてのアプリケーションを表示し、[エンタープライズ アプリケーション] の [All Applications (すべてのアプリケーション)] リストにアプリケーションが表示される方法を理解する
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 075a50802a05a9b8254ff6ab1e0a38f43baca970
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58105626"
---
# <a name="unexpected-application-in-my-applications-list"></a>アプリケーション リストに予期しないアプリケーションがある

この記事は、**[エンタープライズ アプリケーション]** の **[All Applications (すべてのアプリケーション)]** リストにアプリケーションが表示される方法を理解するのに役立ちます。 

## <a name="how-to-see-all-applications-in-your-tenant"></a>テナントのすべてのアプリケーションを表示する方法

テナントのすべてのアプリケーションを表示するには、**[フィルター]** コントロールを使用して、**[All Applications (すべてのアプリケーション)]** リストに **[All Applications (すべてのアプリケーション)]** を表示します。 次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**または**共同管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  Azure Active Directory の左側のナビゲーション メニューから **[エンタープライズ アプリケーション]** をクリックします。

5.  **[すべてのアプリケーション]** をクリックして、すべてのアプリケーションの一覧を表示します。

6.  **[All Applications List (すべてのアプリケーション リスト)]** の上部にある **[フィルター]** コントロールをクリックして使用します。

7.  **[フィルター]** ウィンドウで、**[表示]** オプションを **[すべてのアプリケーション]** に設定します。

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>特定のアプリケーションがすべてのアプリケーション リストに表示される理由

**[All Applications (すべてのアプリケーション)]** にフィルターすると、**[All Applications**  **List (すべてのアプリケーション リスト)]** に、テナントのすべてのサービス プリンシパル オブジェクトが表示されます。 サービス プリンシパル オブジェクトは、さまざまな方法でこのリストに表示できます。

1. アプリケーション ギャラリーから、次のようなアプリケーションを追加する場合:

   1. **Azure AD ギャラリー アプリケーション** - シングル サインオンのためにAzure と事前に統合されているアプリケーション

   2. **アプリケーション プロキシ アプリケーション** - 外部へのセキュリティで保護されたシングル サインオンを提供するオンプレミスの環境で実行されるアプリケーション

   3. **カスタム開発アプリケーション** - 組織が Azure AD アプリケーション開発プラットフォームでの開発を望んでいるが、まだ存在しない可能性のあるアプリケーション

   4. **非ギャラリー アプリケーション** – ユーザー独自のアプリケーションを持ち込みます。 必要な Web リンク、またはユーザー名とパスワードのフィールドを表示するアプリケーションは、SAML または OpenID Connect プロトコルをサポートするか、シングル サインオンのために Azure AD と統合する SCIM をサポートします。

2. Azure Active Directory と統合されたサードパーティー製アプリケーションにサインアップまたはサインインする場合。<sup> </sup> 一例として、[Smartsheet](https://app.smartsheet.com/b/home) または [DocuSign](https://www.docusign.net/member/MemberLogin.aspx) があります。

3. [Microsoft Office 365](https://products.office.com/) などのファースト パーティーのアプリケーションにサインアップするか、ユーザーやグループのライセンスを追加する場合

4. [アプリケーション レジストリ](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)でカスタム開発アプリケーションを作成して、新しいアプリケーション登録を追加する場合

5. [V2.0 アプリケーション登録ポータル](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration)でカスタム開発アプリケーションを作成して、新しいアプリケーション登録を追加する場合

6. Visual Studio の [ASP.net の認証方法](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions)または [接続済みサービス](https://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)を使用して開発しているアプリケーションを追加する場合

7. [Azure AD PowerShell モジュール](/powershell/azure/install-adv2?view=azureadps-2.0)を使用してサービス プリンシパル オブジェクトを作成する場合

8. 管理者としてテナントのデータを[アプリケーションが使用することに同意](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)する場合

9. テナントのデータを[アプリケーションが使用することにユーザーが同意](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)する場合

10. 特定のサービスがテナントにデータを格納できるようにする場合。 一例として、パスワード リセット ポリシーを安全に格納するためにサービス プリンシパルとしてモデル化されるパスワード リセットがあります。

アプリがディレクトリに追加される方法について詳しくは、「[アプリケーションを Azure AD に追加する方法と理由](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added)」をご覧ください。

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>アプリケーションへの特定のユーザーまたはグループの割り当てを削除する必要がある

アプリケーションへのユーザーまたはグループの割り当てを削除するには、「[Azure Active Directory プレビューでエンタープライズ アプリケーションからユーザーまたはグループの割り当てを削除する](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal)」の記事に示されている手順に従います。

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>すべてのユーザーに対してアプリケーションへのアクセスをすべて無効にする必要がある

アプリケーションへのすべてのユーザーのサインインを無効にするには、「[Azure Active Directory プレビューでエンタープライズ アプリケーションのユーザー サインインを無効にする](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal)」の記事に示されている手順に従います。

## <a name="i-want-to-delete-an-application-entirely"></a>アプリケーションを完全に削除する必要がある

**アプリケーションを削除**するには、次の手順に従います。

1. [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**または**共同管理者**としてサインインします。

2. 左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3. フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4. Azure Active Directory の左側のナビゲーション メニューから **[エンタープライズ アプリケーション]** をクリックします。

5. **[すべてのアプリケーション]** をクリックして、すべてのアプリケーションの一覧を表示します。

   * ここに表示したいアプリケーションが表示されない場合は、**[All Applications List (すべてのアプリケーション リスト)]** の上部にある **[フィルター]** コントロールを使用して、**[表示]** オプションを **[All Applications (すべてのアプリケーション)]** に設定します。

6. 削除するアプリケーションを選択します。

7. アプリケーションが読み込まれたら、上部アプリケーションの **[概要]** ウィンドウで **[削除]** アイコンをクリックします。

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>すべてのアプリケーションに対して今後のユーザーの同意操作をすべて無効にする必要がある

ディレクトリ全体でユーザーの同意を無効にすると、エンドユーザーはすべてのアプリケーションに同意できなくなります。 管理者は、依然としてユーザーに代わって同意できます。 アプリケーションの同意と、同意する理由または同意しない理由の詳細については、「[ユーザーおよび管理者の同意について](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)」をご覧ください。

**ディレクトリ全体で今後のユーザーの同意動作をすべて無効にする**には、次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  ナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

5.  **[ユーザー設定]** をクリックします。

6.  **[ユーザーはアプリが自分のデータにアクセスすることを許可できる]** トグルを **[いいえ]** に設定し、**[保存]** をクリックして、今後のすべてのユーザーの同意操作を無効にします。

## <a name="next-steps"></a>次の手順
[Azure Active Directory でのアプリケーションの管理](what-is-application-management.md)
