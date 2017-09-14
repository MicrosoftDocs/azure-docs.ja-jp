---
title: "チュートリアル: Azure Active Directory と Citrix GoToMeeting の統合 | Microsoft Docs"
description: "Azure Active Directory と Citrix GoToMeeting の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 0f59fedb-2cf8-48d2-a5fb-222ed943ff78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 1ddfcd991431a11e5c3e306bd5905003d094ac18
ms.contentlocale: ja-jp
ms.lasthandoff: 07/08/2017


---
# <a name="tutorial-configuring-citrix-gotomeeting-for-automatic-user-provisioning"></a>チュートリアル: Citrix GoToMeeting を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Azure AD から Citrix GoToMeeting にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除するうえで Citrix GoToMeeting と Azure AD で実行する必要がある手順について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

*   Azure Active Directory テナント。
*   Citrix GoToMeeting でのシングル サインオンが有効なサブスクリプション。
*   Team Admin アクセス許可がある Citrix GoToMeeting のユーザー アカウント。

## <a name="assigning-users-to-citrix-gotomeeting"></a>Citrix GoToMeeting へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

プロビジョニング サービスを構成して有効にする前に、Citrix GoToMeeting アプリへのアクセスが必要なユーザーを表す Azure AD 内のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順でこれらのユーザーを Citrix GoToMeeting アプリに割り当てることができます。

[エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-citrix-gotomeeting"></a>ユーザーを Citrix GoToMeeting に割り当てる際の重要なヒント

*   単一の Azure AD ユーザーを Citrix GoToMeeting に割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

*   Citrix GoToMeeting にユーザーを割り当てるときに、有効なユーザー ロールを選択する必要があります。 "既定のアクセス" ロールはプロビジョニングでは使えません。

## <a name="enable-automated-user-provisioning"></a>自動化されたユーザー プロビジョニングを有効にする

このセクションでは、Azure AD を Citrix GoToMeeting のユーザー アカウント プロビジョニング API に接続する手順のほか、プロビジョニング サービスを構成して、Azure AD のユーザーとグループの割り当てに基づいて割り当て済みのユーザー アカウントを Citrix GoToMeeting で作成、更新、無効化する手順を説明します。

> [!TIP]
> Citrix GoToMeeting では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Azure Portal](https://portal.azure.com) で説明されている手順に従ってください。 シングル サインオンは自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-account-provisioning"></a>自動ユーザー アカウント プロビジョニングを構成するには:

1. [Azure Portal](https://portal.azure.com) で、**[Azure Active Directory]、[エンタープライズ アプリ]、[すべてのアプリケーション]** セクションの順に移動します。

2. シングル サインオンのために Citrix GoToMeeting を既に構成している場合は、検索フィールドで Citrix GoToMeeting のインスタンスを検索します。 構成していない場合は、**[追加]** を選択してアプリケーション ギャラリーで **Citrix GoToMeeting** を検索します。 検索結果から Citrix GoToMeeting を選択してアプリケーションの一覧に追加します。

3. Citrix GoToMeeting のインスタンスを選択してから、**[プロビジョニング]** タブを選択します。

4. **[プロビジョニング モード]** を **[自動]** に設定します。 

    ![プロビジョニング](./media/active-directory-saas-citrixgotomeeting-provisioning-tutorial/provisioning.png)

5. [管理者資格情報] セクションで、次の手順を実行します。
   
    a. **[Citrix GoToMeeting 管理ユーザー名]** ボックスに、管理者のユーザー名を入力します。

    b. **[Citrix GoToMeeting 管理パスワード]** ボックスに、管理者のパスワードを入力します。

6. Azure Portal で、**[テスト接続]** をクリックして Azure AD が Citrix GoToMeeting アプリに接続できることを確認します。 接続が失敗した場合、使用中の Citrix GoToMeeting アカウントに Team Admin アクセス許可があることを確認して、**"管理者資格情報"** の手順をもう一度試してください。

7. プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力して、下のチェック ボックスをオンにします。

8. **[保存]** をクリックします。

9. [マッピング] セクションの **[Synchronize Azure Active Directory Users to Citrix GoToMeeting]\(Azure Active Directory ユーザーを Citrix GoToMeeting に同期する\)** を選びます。

10. **[属性マッピング]** セクションで、Azure AD から Citrix GoToMeeting に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Citrix GoToMeeting のユーザー アカウントとの照合に使用されます。 [保存] ボタンをクリックして変更をコミットします。

11. Citrix GoToMeeting に対して Azure AD プロビジョニング サービスを有効にするには、[設定] セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

12. **[保存]** をクリックします。

これで、[ユーザーとグループ] セクションで Citrix GoToMeeting に割り当てたユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、サービスが実行されている限り約 20 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、プロビジョニング サービスによって Citrix GoToMeeting アプリに対して実行されたすべてのアクションが記載されています。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)
* [シングル サインオンの構成](active-directory-saas-citrix-gotomeeting-tutorial.md)



