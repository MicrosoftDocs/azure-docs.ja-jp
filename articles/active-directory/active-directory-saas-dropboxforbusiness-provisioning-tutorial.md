---
title: "チュートリアル: Azure Active Directory と Dropbox for Business の統合 | Microsoft Docs"
description: "Azure Active Directory と Dropbox for Business の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 0f3a42e4-6897-4234-af84-b47c148ec3e1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 6f7616e47322242f01a13d763f71c93d4ac06a92
ms.contentlocale: ja-jp
ms.lasthandoff: 07/08/2017


---
# <a name="tutorial-configuring-dropbox-for-business-for-automatic-user-provisioning"></a>チュートリアル: Dropbox for Business を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Azure AD から Dropbox for Business にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除するうえで Dropbox for Business と Azure AD で実行する必要がある手順について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

*   Azure Active Directory テナント。
*   Dropbox for Business でのシングル サインオンが有効なサブスクリプション
*   Team Admin アクセス許可がある Dropbox for Business のユーザー アカウント

## <a name="assigning-users-to-dropbox-for-business"></a>Dropbox for Business へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

プロビジョニング サービスを構成して有効にする前に、Dropbox for Business アプリへのアクセスが必要なユーザーを表す Azure AD 内のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順でこれらのユーザーを Dropbox for Business アプリに割り当てることができます。

[エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>ユーザーを Dropbox for Business に割り当てる際の重要なヒント

*   単一の Azure AD ユーザーを Dropbox for Business に割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

*   Dropbox for Business にユーザーを割り当てるときに、有効なユーザー ロールを選択する必要があります。 "既定のアクセス" ロールはプロビジョニングでは使えません。

## <a name="enable-automated-user-provisioning"></a>自動化されたユーザー プロビジョニングを有効にする

このセクションでは、Azure AD を Dropbox for Business のユーザー アカウント プロビジョニング API に接続する手順のほか、プロビジョニング サービスを構成して、Azure AD のユーザーとグループの割り当てに基づいて割り当て済みのユーザー アカウントを Dropbox for Business で作成、更新、無効化する手順を説明します。

>[!Tip]
>Dropbox for Business では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Azure Portal](https://portal.azure.com) で説明されている手順に従ってください。 シングル サインオンは自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-account-provisioning"></a>自動ユーザー アカウント プロビジョニングを構成するには:

1. [Azure Portal](https://portal.azure.com) で、**[Azure Active Directory]、[エンタープライズ アプリ]、[すべてのアプリケーション]** セクションの順に移動します。

2. シングル サインオンのために Dropbox for Business を既に構成している場合は、検索フィールドで Dropbox for Business のインスタンスを検索します。 構成していない場合は、**[追加]** を選択してアプリケーション ギャラリーで **Dropbox for Business** を検索します。 検索結果から Dropbox for Business を選択してアプリケーションの一覧に追加します。

3. Dropbox for Business のインスタンスを選択してから、**[プロビジョニング]** タブを選択します。

4. **[プロビジョニング モード]** を **[自動]** に設定します。 

    ![プロビジョニング](./media/active-directory-saas-dropboxforbusiness-provisioning-tutorial/provisioning.png)

5. **[管理者資格情報]** セクションにある **[承認する]** をクリックします。 新しいブラウザー ウィンドウで、Dropbox for Business のログイン ダイアログが開きます。

6. **[Sign-in to Dropbox to link with Azure AD]\(Dropbox にサインインして Microsoft Azure AD とリンク\)** ダイアログで、Dropbox for Business テナントにサインインします。

     ![ユーザー プロビジョニング](./media/active-directory-saas-dropboxforbusiness-provisioning-tutorial/ic769518.png "ユーザー プロビジョニング")

7. Dropbox for Business のテナントに対して変更を行うためのアクセス許可を Azure Active Directory に付与することを確認します。 **[Allow]\(許可する\)** をクリックします。
    
      ![ユーザー プロビジョニング](./media/active-directory-saas-dropboxforbusiness-provisioning-tutorial/ic769519.png "ユーザー プロビジョニング")

8. Azure Portal で、**[テスト接続]** をクリックして Azure AD が Dropbox for Business アプリに接続できることを確認します。 接続が失敗した場合、使用中の Dropbox for Business アカウントに Team Admin アクセス許可があることを確認して、**"承認"** の手順をもう一度試してください。

9. プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力して、下のチェック ボックスをオンにします。

10. **[保存]** をクリックします。

11. [マッピング] セクションの **[Synchronize Azure Active Directory Users to Dropbox for Business]\(Azure Active Directory ユーザーを Dropbox for Business に同期する\)** を選びます。

12. **[属性マッピング]** セクションで、Azure AD から Dropbox for Business に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Dropbox for Business のユーザー アカウントとの照合に使用されます。 [保存] ボタンをクリックして変更をコミットします。

13. Dropbox for Business に対して Azure AD プロビジョニング サービスを有効にするには、[設定] セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

14. **[保存]** をクリックします。

これで、[ユーザーとグループ] セクションで Dropbox for Business に割り当てたユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、サービスが実行されている限り約 20 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、プロビジョニング サービスによって Dropbox for Business アプリに対して実行されたすべてのアクションが記載されています。

これでテスト アカウントを作成できるようになりました。 ここで 20 分間待機し、アカウントが Dropbox for Business に同期されたことを確認します。

正常に完了したユーザー プロビジョニング サイクルは、関連する状態で示されます。

![ユーザーの割り当て](./media/active-directory-saas-dropboxforbusiness-provisioning-tutorial/IC769523.png "Assign users")


## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)
* [シングル サインオンの構成](active-directory-saas-dropboxforbusiness-tutorial.md)
