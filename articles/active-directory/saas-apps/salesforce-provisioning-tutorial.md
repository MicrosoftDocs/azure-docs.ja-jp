---
title: チュートリアル:Salesforce を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory と Salesforce の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 49384b8b-3836-4eb1-b438-1c46bb9baf6f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64de004a1d9b3aa011c447fdded51658582586b0
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68825783"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>チュートリアル:Salesforce を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Azure AD から Salesforce にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除するうえで Salesforce と Azure AD で実行する必要がある手順について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* Azure Active Directory テナント
* Salesforce.com テナント

> [!IMPORTANT]
> Salesforce.com の試用アカウントを使用している場合は、自動化されたユーザー プロビジョニングを構成できません。 試用アカウントの場合、アカウントを購入するまでは、必要な API にアクセスできません。 [無料の開発者アカウント](https://developer.salesforce.com/signup) を使用してこのチュートリアルを完了することで、この制限を回避できます。

Salesforce Sandbox 環境を使用している場合は、 [Salesforce Sandbox の統合に関するチュートリアル](https://go.microsoft.com/fwLink/?LinkID=521879)を参照してください。

## <a name="assigning-users-to-salesforce"></a>Salesforce へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

プロビジョニング サービスを構成して有効にする前に、Salesforce アプリへのアクセスが必要な Azure AD 内のユーザーやグループを決定しておく必要があります。 これを決定したら、[エンタープライズ アプリへのユーザーまたはグループの割り当て](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)に関するページの手順に従って、これらのユーザーを Salesforce アプリに割り当てることができます。

### <a name="important-tips-for-assigning-users-to-salesforce"></a>ユーザーを Salesforce に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Salesforce に割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Salesforce にユーザーを割り当てるときに、有効なユーザー ロールを選択する必要があります。 "既定のアクセス" ロールはプロビジョニングでは使えません。

    > [!NOTE]
    > このアプリにより、プロビジョニング プロセスの一環として Salesforce からプロファイルがインポートされます。顧客は Azure AD でユーザーを割り当てるとき、ロールを選択します。 Salesforce からインポートされたプロファイルは、Azure AD ではロールとして表示されることに注意してください。

## <a name="enable-automated-user-provisioning"></a>自動化されたユーザー プロビジョニングを有効にする

このセクションでは、Azure AD を Salesforce のユーザー アカウント プロビジョニング API に接続する手順のほか、プロビジョニング サービスを構成して、Azure AD のユーザーとグループの割り当てに基づいて割り当て済みのユーザー アカウントを Salesforce で作成、更新、無効化する手順を説明します。

> [!Tip]
> Salesforce では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Azure Portal](https://portal.azure.com) で説明されている手順に従ってください。 シングル サインオンは自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="configure-automatic-user-account-provisioning"></a>自動ユーザー アカウント プロビジョニングを構成する

このセクションでは、Active Directory のユーザー アカウントのユーザー プロビジョニングを Salesforce に対して有効にする方法について説明します。

1. [Azure Portal](https://portal.azure.com) で、 **[Azure Active Directory]、[エンタープライズ アプリ]、[すべてのアプリケーション]** セクションの順に移動します。

2. シングル サインオンのために Salesforce を既に構成している場合は、検索フィールドで Salesforce のインスタンスを検索します。 構成していない場合は、 **[追加]** を選択してアプリケーション ギャラリーで **Salesforce** を検索します。 検索結果から Salesforce を選択してアプリケーションの一覧に追加します。

3. Salesforce のインスタンスを選択してから、 **[プロビジョニング]** タブを選択します。

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![プロビジョニング](./media/salesforce-provisioning-tutorial/provisioning.png)

5. **[管理者資格情報]** セクションに次の構成設定を指定します。

    a. **[管理ユーザー名]** ボックスに、Salesforce.com で**システム管理者**プロファイルが割り当てられている Salesforce アカウント名を入力します。

    b. **[管理パスワード]** テキストボックスに、このアカウントのパスワードを入力します。

6. Salesforce のセキュリティ トークンを取得するには、新しいタブを開き、同じ Salesforce の管理者アカウントにサインインします。 ページの右上にある自分の名前をクリックし、 **[Settings]\(設定\)** をクリックします。

    ![Enable automatic user provisioning](./media/salesforce-provisioning-tutorial/sf-my-settings.png "Enable automatic user provisioning")

7. 左側のナビゲーション ウィンドウで **[私の個人情報]** をクリックして関連するセクションを展開し、 **[私のセキュリティ トークンのリセット]** をクリックします。
  
    ![Enable automatic user provisioning](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "Enable automatic user provisioning")

8. **[Reset Security Token]\(セキュリティ トークンのリセット\)** ページで、 **[Reset Security Token]\(セキュリティ トークンのリセット\)** ボタンをクリックします。

    ![Enable automatic user provisioning](./media/salesforce-provisioning-tutorial/sf-reset-token.png "Enable automatic user provisioning")

9. この管理アカウントに関連付けられている電子メールの受信トレイを確認します。 新しいセキュリティ トークンが記載された Salesforce.com からの電子メールを探します。

10. トークンをコピーして Azure AD のウィンドウに移動し、 **[シークレット トークン]** フィールドに貼り付けます。

11. **テナント URL** は、Salesforce のインスタンスが Salesforce Government クラウドにある場合にのみ入力する必要があります。 それ以外の場合は省略可能です。 テナント URL は、"https://\<your-instance\>.my.salesforce.com" 形式で入力します。\<your-instance\> は、ご利用の Salesforce のインスタンスの名前に置き換えてください。

12. Azure Portal で、 **[テスト接続]** をクリックして Azure AD が Salesforce アプリに接続できることを確認します。

13. プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力して、下のチェック ボックスをオンにします。

14. **[保存]** をクリックします。  

15. [マッピング] セクションの **[Azure Active Directory ユーザーを Salesforce に同期する]** を選択します。

16. **[属性マッピング]** セクションで、Azure AD から Salesforce に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Salesforce のユーザー アカウントとの照合に使用されることに注意してください。 [保存] ボタンをクリックして変更をコミットします。

17. Salesforce に対して Azure AD プロビジョニング サービスを有効にするには、[設定] セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

18. **[保存]** をクリックします。

> [!NOTE]
> ユーザーが Salesforce アプリケーションでプロビジョニングされたら、管理者は言語固有の設定を構成する必要があります。 言語の構成の詳細については、[こちら](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5)の記事を参照してください。

これで、[ユーザーとグループ] セクションで Salesforce に割り当てたユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかることに注意してください。後続の同期は、サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ ログを取得できます。このログには、プロビジョニング サービスによって Salesforce アプリに対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [シングル サインオンの構成](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
