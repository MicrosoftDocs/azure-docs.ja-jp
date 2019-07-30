---
title: チュートリアル:Peakon を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: ユーザー アカウントを Peakon に自動的にプロビジョニング/プロビジョニング解除するように Azure Active Directory を構成する方法について説明します。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: cc572206e20a1f2ef1a77efb8120ad4d2f805174
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67847901"
---
# <a name="tutorial-configure-peakon-for-automatic-user-provisioning"></a>チュートリアル:Peakon を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure Active Directory (Azure AD) が、ユーザー、グループ、またはその両方を Peakon に自動的にプロビジョニングおよびプロビジョニング解除するよう構成するために、Peakon と Azure AD で実行する手順を示すことです。

> [!NOTE]
>  このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。
>
> このコネクタは、現在プレビューの段階です。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。
## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件があることを前提としています。

* Azure AD テナント。
* [Peakon テナント](https://peakon.com/us/pricing/)。
* 管理者アクセス許可がある Peakon のユーザー アカウント。

## <a name="assigning-users-to-peakon"></a>Peakon にユーザーを割り当てる

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成し、有効にする前に、Peakon へのアクセスが必要な Azure AD のユーザー、グループ、またはその両方を特定する必要があります。 特定した後、次の手順に従い、これらのユーザー、グループ、またはその両方を Peakon に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-peakon"></a>ユーザーを Peakon に割り当てる際の重要なヒント 

* 1 名の Azure AD ユーザーを Peakon に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Peakon にユーザーを割り当てるときは、割り当てダイアログで、有効なアプリケーション固有ロール (使用可能な場合) を選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-peakon-for-provisioning"></a>プロビジョニングのために Peakon を設定する

1.  [Peakon 管理コンソール](https://app.Peakon.com/login)にサインインします。 **[Configuration]\(構成\)** をクリックします。 

    ![Peakon 管理コンソール](media/Peakon-provisioning-tutorial/Peakon-admin-configuration.png)

2.  **[Integrations]\(統合\)** を選択します。
    
    ![Peakon-employee-provision](media/Peakon-provisioning-tutorial/Peakon-select-integration.png)

3.  **[Employee Provisioning]\(従業員のプロビジョニング\)** を有効にします。

    ![Peakon-employee-provision](media/Peakon-provisioning-tutorial/peakon05.png)

4.  **[SCIM 2.0 URL]** および **[OAuth Bearer Token]\(OAuth ベアラー トークン\)** の値をコピーします。 これらの値を、Azure portal の Peakon アプリケーションの [プロビジョニング] タブの **[テナント URL]** および **[シークレット トークン]** フィールドに入力します。

    ![Peakon でのトークンの作成](media/Peakon-provisioning-tutorial/peakon04.png)

## <a name="add-peakon-from-the-gallery"></a>ギャラリーから Peakon を追加する

Azure AD を使用した自動ユーザー プロビジョニング用に Peakon を構成するには、Azure AD アプリケーション ギャラリーから管理対象の SaaS アプリケーションの一覧に Peakon を追加する必要があります。

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Peakon**」と入力し、結果パネルで **[Peakon]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の Peakon](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-peakon"></a>Peakon への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザー、グループ、またはその両方の割り当てに基づいて、Peakon でユーザー、グループ、またはその両方が作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> Peakon では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Peakon シングル サインオンのチュートリアル](peakon-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-peakon--in-azure-ad"></a>Azure AD で Peakon の自動ユーザー プロビジョニングを構成するには:

1. [Azure Portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Peakon]** を選択します。

    ![アプリケーションの一覧の [Peakon] リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナント URL]** および **[シークレット トークン]** に、先ほど取得した **[SCIM 2.0 URL]** および **[OAuth Bearer Token]\(OAuth ベアラー トークン\)** の値をそれぞれ入力します。 **[接続テスト]** をクリックして、Azure AD から Peakon に接続できることを確認します。 接続できない場合は、使用中の Peakon アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

7. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

8. **[Save]** をクリックします。

9. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Peakon]\(Azure Active Directory ユーザーを Peakon に同期する\)** を選択します。

    ![Peakon ユーザーのマッピング](media/Peakon-provisioning-tutorial/Peakon-user-mappings.png)

10. **[属性マッピング]** セクションで、Azure AD から Peakon に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Peakon のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Peakon ユーザーの属性](media/Peakon-provisioning-tutorial/Peakon-user-attributes.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。
    
    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Peakon に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="connector-limitations"></a>コネクタの制限事項

* Peakon のすべてのカスタム ユーザー属性は、Peakon の `urn:ietf:params:scim:schemas:extension:peakon:2.0:User` のカスタム SCIM ユーザー拡張から拡張する必要があります。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)