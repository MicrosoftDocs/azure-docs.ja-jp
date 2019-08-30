---
title: チュートリアル:Insight4GRC を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Insight4GRC に自動的にプロビジョニングまたはプロビジョニング解除する方法を説明します。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 34718201-4f0e-4260-9af0-b3b70a1e8265
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 7e8c6c2277f29fc114033aac24844e9e85816b78
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950760"
---
# <a name="tutorial-configure-insight4grc--for-automatic-user-provisioning"></a>チュートリアル:Insight4GRC を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Insight4GRC に対してユーザー、グループ、またはその両方のプロビジョニングまたはプロビジョニング解除を自動的に実行するように Azure Active Directory (Azure AD) を構成するために Insight4GRC と Azure AD 内で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント。
* [Insight4GRC テナント](https://www.rsmuk.com/)。
* 管理者アクセス許可がある Insight4GRC のユーザー アカウント。

## <a name="assigning-users-to-insight4grc"></a>Insight4GRC へのユーザーの割り当て 

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Insight4GRC へのアクセスが必要な Azure AD 内のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Insight4GRC に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-insight4grc"></a>ユーザーを Insight4GRC に割り当てる際の重要なヒント 

* 1 名の Azure AD ユーザーを Insight4GRC に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Insight4GRC にユーザーを割り当てるときは、割り当てダイアログで、有効なアプリケーション固有ロール (使用可能な場合) を選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="setup-insight4grc-for-provisioning"></a>プロビジョニングのための Insight4GRC の設定

Azure AD での自動ユーザー プロビジョニング用に Insight4GRC を構成する前に、Insight4GRC で SCIM プロビジョニングを有効にする必要があります。

1. ベアラー トークンを取得するには、エンドカスタマーが[サポート チーム](mailto:support.ss@rsmuk.com)に連絡する必要があり、このチームがカスタマーにベアラー トークンを提供します。

2. SCIM エンドポイント URL を取得するには、SCIM エンドポイント URL の作成に使用される Insight4GRC ドメイン名を準備しておく必要があります。 Insight4GRC ドメイン名は、Insight4GRC を使用した最初のソフトウェア購入の一部として取得できます。


## <a name="add-insight4grc--from-the-gallery"></a>ギャラリーから Insight4GRC を追加する

Azure AD で自動ユーザー プロビジョニング用に Insight4GRC を構成するには、Azure AD アプリケーション ギャラリーから Insight4GRC をマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Insight4GRC を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Insight4GRC**」と入力し、結果ウィンドウで **[Insight4GRC]** を選択してから、 **[追加]** をクリックしてアプリケーションを追加します。

    ![結果一覧の Insight4GRC](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-insight4grc"></a>Insight4GRC への自動ユーザー プロビジョニングの構成  

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて Insight4GRC のユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> Insight4GRC で SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Insight4GRC シングル サインオンのチュートリアル](insight4grc-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-insight4grc--in-azure-ad"></a>Azure AD 内で Insight4GRC の自動ユーザー プロビジョニングを構成するには、次の操作を実行します。

1. [Azure Portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Insight4GRC]** を選択します。

    ![アプリケーションの一覧の [Insight4GRC] リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5.  [管理者資格情報] セクションで、以前に取得した {Insight4GRC Domain Name} の値を使用して、 **[テナント URL]** に「`https://{Insight4GRC Domain Name}.insight4grc.com/public/api/scim/v2`」と入力します。 以前に取得した**トークン値**を **[シークレット トークン]** に入力します。 **[テスト接続]** をクリックして、Azure AD から Insight4GRC への接続を確保します。 接続できない場合は、使用中の Insight4GRC アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[Save]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Insight4GRC]\(Azure Active Directory ユーザーを Insight4GRC に同期する\)** を選択します。

    ![Insight4GRC とユーザーのマッピング](media/insight4grc-provisioning-tutorial/usermapping.png)

9. **[属性マッピング]** セクションで、Azure AD から Insight4GRC に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Insight4GRC のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Insight4GRC とユーザーのマッピング](media/insight4grc-provisioning-tutorial/userattribute.png)

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Group to Insight4GRC]\(Azure Active Directory グループを Insight4GRC に同期する\)** を選択します。

    ![Insight4GRC とグループのマッピング](media/insight4grc-provisioning-tutorial/groupmapping.png)

11. **[属性マッピング]** セクションで、Azure AD から Insight4GRC に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Insight4GRC のグループ アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Insight4GRC とグループのマッピング](media/insight4grc-provisioning-tutorial/groupattribute.png)

10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

11. Insight4GRC に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Insight4GRC にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Insight4GRC に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。


## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)。
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)