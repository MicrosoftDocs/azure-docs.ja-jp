---
title: "チュートリアル: Azure Active Directory と Salesforce の統合 | Microsoft Docs"
description: "Azure Active Directory と Salesforce の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 49384b8b-3836-4eb1-b438-1c46bb9baf6f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: a573a7ef79e28c50ae0923849a88f88af40f21be
ms.contentlocale: ja-jp
ms.lasthandoff: 06/23/2017


---
# <a name="tutorial-configuring-salesforce-for-automatic-user-provisioning"></a>チュートリアル: Salesforce を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Azure AD から Salesforce にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除するうえで Salesforce と Azure AD で実行する必要がある手順について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

*   Azure Active Directory テナント。
*   Salesforce for Work または Salesforce for Education の有効なテナントが必要です。 どちらのサービスにも無料試用版のアカウントを使用できます。
*   Team Admin アクセス許可がある Salesforce のユーザー アカウント

## <a name="assigning-users-to-salesforce"></a>Salesforce へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

プロビジョニング サービスを構成して有効にする前に、Salesforce アプリへのアクセスが必要なユーザーを表す Azure AD 内のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順でこれらのユーザーを Salesforce アプリに割り当てることができます。

[エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce"></a>ユーザーを Salesforce に割り当てる際の重要なヒント

*   単一の Azure AD ユーザーを Salesforce に割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

*  Salesforce にユーザーを割り当てるときに、有効なユーザー ロールを選択する必要があります。 "既定のアクセス" ロールはプロビジョニングでは使えません。

    > [!NOTE]
    > このアプリにより、プロビジョニング プロセスの一環として Salesforce からカスタム ロールがインポートされます。顧客はユーザーを割り当てるとき、ロールを選択します。

## <a name="enable-automated-user-provisioning"></a>自動化されたユーザー プロビジョニングを有効にする

このセクションでは、Azure AD を Salesforce のユーザー アカウント プロビジョニング API に接続する手順のほか、プロビジョニング サービスを構成して、Azure AD のユーザーとグループの割り当てに基づいて割り当て済みのユーザー アカウントを Salesforce で作成、更新、無効化する手順を説明します。

>[!Tip]
>Salesforce では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Azure Portal](https://portal.azure.com) で説明されている手順に従ってください。 シングル サインオンは自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-account-provisioning"></a>自動ユーザー アカウント プロビジョニングを構成するには:

このセクションでは、Active Directory のユーザー アカウントのユーザー プロビジョニングを Salesforce に対して有効にする方法について説明します。

1. [Azure Portal](https://portal.azure.com) で、**[Azure Active Directory]、[エンタープライズ アプリ]、[すべてのアプリケーション]** セクションの順に移動します。

2. シングル サインオンのために Salesforce を既に構成している場合は、検索フィールドで Salesforce のインスタンスを検索します。 構成していない場合は、**[追加]** を選択してアプリケーション ギャラリーで **Salesforce** を検索します。 検索結果から Salesforce を選択してアプリケーションの一覧に追加します。

3. Salesforce のインスタンスを選択してから、**[プロビジョニング]** タブを選択します。

4. **[プロビジョニング モード]** を **[自動]** に設定します。 
![プロビジョニング](./media/active-directory-saas-salesforce-provisioning-tutorial/provisioning.png)

5. **[管理者資格情報]** セクションに次の構成設定を指定します。
   
    a. **[管理ユーザー名]** ボックスに、Salesforce.com で**システム管理者**プロファイルが割り当てられている Salesforce アカウント名を入力します。
   
    b. **[管理パスワード]** テキストボックスに、このアカウントのパスワードを入力します。

6. Salesforce のセキュリティ トークンを取得するには、新しいタブを開き、同じ Salesforce の管理者アカウントにサインインします。 ページの右上にある自分の名前をクリックし **[個人の設定]** をクリックします。

     ![Enable automatic user provisioning](./media/active-directory-saas-salesforce-provisioning-tutorial/sf-my-settings.png "Enable automatic user provisioning")
7. 左側のナビゲーション ウィンドウで **[個人用]** をクリックして関連するセクションを展開し、**[個人のセキュリティ トークンのリセット]** をクリックします。
  
    ![Enable automatic user provisioning](./media/active-directory-saas-salesforce-provisioning-tutorial/sf-personal-reset.png "Enable automatic user provisioning")
8. **[個人のセキュリティ トークンのリセット]** ページで、**[セキュリティ トークンのリセット]** ボタンをクリックします。

    ![Enable automatic user provisioning](./media/active-directory-saas-salesforce-provisioning-tutorial/sf-reset-token.png "Enable automatic user provisioning")
9. この管理アカウントに関連付けられている電子メールの受信トレイを確認します。 新しいセキュリティ トークンが記載された Salesforce.com からの電子メールを探します。
10. トークンをコピーして Azure AD のウィンドウに移動し、**[ソケット トークン]** フィールドに貼り付けます。

11. Azure Portal で、**[テスト接続]** をクリックして Azure AD が Salesforce アプリに接続できることを確認します。

12. プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力して、下のチェック ボックスをオンにします。

13. **[保存]** をクリックします。  
    
14.  [マッピング] セクションの **[Azure Active Directory ユーザーを Salesforce に同期する]** を選択します。

15. **[属性マッピング]** セクションで、Azure AD から Salesforce に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Salesforce のユーザー アカウントとの照合に使用されることに注意してください。 [保存] ボタンをクリックして変更をコミットします。

16. Salesforce に対して Azure AD プロビジョニング サービスを有効にするには、[設定] セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

17. **[保存]** をクリックします。

これで、[ユーザーとグループ] セクションで Salesforce に割り当てたユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかることに注意してください。後続の同期は、サービスが実行されている限り約 20 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、プロビジョニング サービスによって Salesforce アプリに対して実行されたすべてのアクションが記載されています。

これでテスト アカウントを作成できるようになりました。 ここで 20 分間待機し、アカウントが Salesforce に同期されたことを確認します。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)
* [シングル サインオンの構成](active-directory-saas-salesforce-tutorial.md)
