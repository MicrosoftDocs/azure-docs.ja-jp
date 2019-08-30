---
title: チュートリアル:Templafy を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Templafy に対してユーザー アカウントの自動的なプロビジョニングとプロビジョニング解除を実行するように Azure Active Directory を構成する方法について説明します。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 230877d5-e466-4449-82c8-88cfa42f6501
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 96270ccccd8f043f116f686938c8f47c5f4f0a41
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68841910"
---
# <a name="tutorial-configure-templafy-for-automatic-user-provisioning"></a>チュートリアル:Templafy を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Templafy に対してユーザー、グループ、またはその両方のプロビジョニングまたはプロビジョニング解除を自動的に実行するように Azure Active Directory (Azure AD) を構成するために Templafy と Azure AD 内で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント。
* [Templafy テナント](https://www.templafy.com/pricing/)。
* 管理者アクセス許可がある Templafy のユーザー アカウント。

## <a name="assigning-users-to-templafy"></a>Templafy へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Templafy へのアクセスが必要な Azure AD 内のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Templafy に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-templafy"></a>ユーザーを Templafy に割り当てるときの重要なヒント

* 1 名の Azure AD ユーザーを Templafy に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Templafy にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="setup-templafy-for-provisioning"></a>プロビジョニングのために Templafy を設定する

Azure AD での自動ユーザー プロビジョニング用に Templafy を構成する前に、Templafy で SCIM プロビジョニングを有効にする必要があります。

1. Templafy 管理コンソールにサインインします。 **[管理]** をクリックします。

    ![Templafy 管理コンソール](media/templafy-provisioning-tutorial/image00.png)

2. **[認証方法]** をクリックします。

    ![Templafy の SCIM の追加](media/templafy-provisioning-tutorial/image01.png)

3. **SCIM Api キー**の値をコピーします。 この値を、Azure portal 上で Templafy アプリケーションの [プロビジョニング] タブ内の **[シークレット トークン]** フィールドに入力します。

    ![Templafy の SCIM の追加](media/templafy-provisioning-tutorial/image02.png)

## <a name="add-templafy-from-the-gallery"></a>ギャラリーからの Templafy の追加

Azure AD で自動ユーザー プロビジョニング用に Templafy を構成するには、Azure AD アプリケーション ギャラリーから Templafy をマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Templafy を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Templafy**」と入力し、結果ウィンドウで **[Templafy]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果リストの Templafy](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-templafy"></a>Templafy への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて Templafy のユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> Templafy では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Templafy シングル サインオンのチュートリアル](templafy-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-templafy-in-azure-ad"></a>Azure AD 内で Templafy の自動ユーザー プロビジョニングを構成するには、次の操作を実行します。

1. [Azure Portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Templafy]** を選択します。

    ![アプリケーションの一覧の Templafy のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://scim.templafy.com/scim`」と入力します。 **[シークレット トークン]** に先ほど取得した **SCIM API キー**の値を入力します。 **[テスト接続]** をクリックして、Azure AD から Templafy への接続を確保します。 接続できない場合は、使用中の Templafy アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[Save]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Templafy]\(Azure Active Directory ユーザーを Templafy に同期する\)** を選択します。

    ![Templafy のユーザー マッピング](media/templafy-provisioning-tutorial/usermapping.png)

9. **[属性マッピング]** セクションで、Azure AD から Templafy に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Templafy のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Templafy ユーザーの属性](media/templafy-provisioning-tutorial/userattribute.png)

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to Templafy]\(Azure Active Directory グループを Templafy に同期する\)** を選択します。

    ![Templafy のグループ マッピング](media/templafy-provisioning-tutorial/groupmapping.png)

11. **[属性マッピング]** セクションで、Azure AD から Templafy に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Templafy のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Templafy のグループ属性](media/templafy-provisioning-tutorial/groupattribute.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Templafy に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Templafy にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

    これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Templafy に対して実行されたすべてのアクションが記載されています。

    Azure AD プロビジョニング ログの読み方について詳しくは、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。
    
## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)
