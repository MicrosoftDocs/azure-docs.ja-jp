---
title: チュートリアル:iProva を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: iProva に対してユーザー アカウントの自動的なプロビジョニングとプロビジョニング解除を実行するように Azure Active Directory を構成する方法について説明します。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 4edba747-242d-4795-9539-649f33af4c13
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 320a3178448da93a04a332183ad5d34453f702ef
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896813"
---
# <a name="tutorial-configure-iprova-for-automatic-user-provisioning"></a>チュートリアル:iProva を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure Active Directory (Azure AD) が、ユーザー、グループ、またはその両方を iProva に対して自動的にプロビジョニングおよびプロビジョニング解除するよう構成するために、iProva と Azure AD で実行する手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [iProva テナント](https://www.iProva.com/)
* 管理者のアクセス許可を持つ iProva のユーザー アカウント。

## <a name="assigning-users-to-iprova"></a>ユーザーを iProva に割り当てる

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、iProva へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 特定した後、次の手順に従い、これらのユーザー、グループ、またはその両方を iProva に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-iprova"></a>ユーザーを iProva に割り当てる際の重要なヒント

* 1 名の Azure AD ユーザーを iProva に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* iProva にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-iprova-for-provisioning"></a>プロビジョニングのために iProva を設定する

1. [iProva Admin Console](https://www.iProva.com/) にサインインします。 **[Go to]\(移動\) > [Application Management]\(\アプリケーション管理)** に移動します。

    ![iProva Admin Console](media/iprova-provisioning-tutorial/admin.png)

2.  **[External user management]\(外部ユーザー管理\)** をクリックします。

    ![iProva の SCIM の追加](media/iprova-provisioning-tutorial/external.png)

3. 新しいプロバイダーを追加するには、**プラス** アイコンをクリックします。 新しい **[Add provider]\(プロバイダーの追加\)** ダイアログ ボックスに **[Title]\(タイトル\)** を入力します。 **[IP-based access restriction]\(IP ベースのアクセス制限\)** の追加を選択することもできます。 **[OK]** ボタンをクリックします。

    ![iProva の新規追加](media/iprova-provisioning-tutorial/add.png)

    ![iProva のプロバイダーの追加](media/iprova-provisioning-tutorial/addprovider.png)

4.  **[Permanent token]\(永続的なトークン\)** ボタンをクリックします。 これが確認できる唯一のタイミングなので、 **[Permanent token]\(永続的なトークン\)** をコピーして保存しておきます。 この値を、Azure portal で iProva アプリケーションの [プロビジョニング] タブ内の [シークレット トークン] フィールドに入力します。

    ![iProva のトークンの作成](media/iprova-provisioning-tutorial/token.png)

## <a name="add-iprova-from-the-gallery"></a>ギャラリーからの iProva の追加

Azure AD での自動ユーザー プロビジョニング用に iProva を構成する前に、Azure AD アプリケーション ギャラリーから iProva をマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから iProva を追加するには、次の手順を行います。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**iProva**」と入力し、結果パネルで **[iProva]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の iProva](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-iprova"></a>iProva への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザー、グループ、またはその両方の割り当てに基づいて、iProva でユーザー、グループ、またはその両方が作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> iProva では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[iProva シングル サインオンのチュートリアル](https://docs.microsoft.com/azure/active-directory/saas-apps/iProva-tutorial)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-iprova-in-azure-ad"></a>Azure AD で iProva の自動ユーザー プロビジョニングを構成するには:

1. [Azure Portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[iProva]** を選択します。

    ![アプリケーションの一覧の iProva のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブ](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://identitymanagement.services.iProva.nl/scim`」と入力します。 先ほど取得した **[Permanent token]\(永続的なトークン\)** の値を **[シークレット トークン]** に入力します。 **[テスト接続]** をクリックして、Azure AD から iProva への接続を確保します。 接続できない場合は、使用中の iProva アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[Save]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to iProva]\(Azure Active Directory ユーザーを iProva に同期する\)** を選択します。

    ![iProva のユーザー マッピング](media/iprova-provisioning-tutorial/usermappings.png)

9. **[属性マッピング]** セクションで、Azure AD から iProva に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で iProva のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![iProva のユーザー属性](media/iprova-provisioning-tutorial/userattributes.png)

10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to iProva]\(Azure Active Directory グループを iProva に同期する\)** を選択します。

    ![iProva グループのマッピング](media/iprova-provisioning-tutorial/groupmappings.png)

11. **[属性マッピング]** セクションで、Azure AD から iProva に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で iProva のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![iProva のグループ属性](media/iprova-provisioning-tutorial/groupattributes.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. iProva に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、iProva にプロビジョニングするユーザー、グループ、またはこれらの両方を定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって iProva に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)

