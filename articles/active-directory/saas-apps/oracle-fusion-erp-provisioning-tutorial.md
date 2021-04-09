---
title: チュートリアル:Oracle Fusion ERP を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: ユーザー アカウントを Oracle Fusion ERP に対して自動的にプロビジョニングおよびプロビジョニング解除するように Azure Active Directory を構成する方法について説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: da6e1a8ba31f8f4991bde4803191598a015a68b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358441"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>チュートリアル:Oracle Fusion ERP を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure Active Directory (Azure AD) が、ユーザー、グループ、またはその両方を Oracle Fusion ERP に対して自動的にプロビジョニングおよびプロビジョニング解除するよう構成するために、Oracle Fusion ERP と Azure AD で実行する手順を示すことです。

> [!NOTE]
>  このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> このコネクタは、現在プレビューの段階です。 プレビュー機能を使用するための一般的な Microsoft Azure の使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* [Oracle Fusion ERP テナント](https://www.oracle.com/applications/erp/)。
* Admin アクセス許可がある Oracle Fusion ERP のユーザー アカウント。

## <a name="assign-users-to-oracle-fusion-erp"></a>Oracle Fusion ERP にユーザーを割り当てる 
Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成し、有効にする前に、Oracle Fusion ERP へのアクセスが必要な Azure AD のユーザー、グループ、またはその両方を特定する必要があります。 特定した後、次の手順に従い、これらのユーザー、グループ、またはその両方を Oracle Fusion ERP に割り当てることができます。
 
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>ユーザーを Oracle Fusion ERP に割り当てるときの重要なヒント 

 * 1 名の Azure AD ユーザーを Oracle Fusion ERP に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Oracle Fusion ERP にユーザーを割り当てるときは、割り当てダイアログで、有効なアプリケーション固有ロール (使用可能な場合) を選択する必要があります。 既定のアクセス ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>プロビジョニングのために Oracle Fusion ERP を設定する

Azure AD での自動ユーザー プロビジョニング用に Oracle Fusion ERP を構成する前に、Oracle Fusion ERP で SCIM プロビジョニングを有効にする必要があります。

1. [Oracle Fusion ERP 管理コンソール](https://cloud.oracle.com/sign-in)にサインインします。

2. 左上隅にある [Navigator]\(ナビゲーター\) をクリックします。 **[Tools]\(ツール\)** で **[Security Console]\(セキュリティ コンソール\)** を選択します。

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/login.png" alt-text="Oracle Fusion E R P 管理コンソールの [Navigator]\(ナビゲーター\) ページのスクリーンショット。[Tools]\(ツール\) と [Security Console]\(セキュリティ コンソール\) が強調表示されています。" border="false":::

3. **[Users]\(ユーザー\)** に移動します。
    
    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user.png" alt-text="Oracle Fusion E R P 管理コンソールのパネルのスクリーンショット。[Users]\(ユーザー\) 項目が強調表示されています。" border="false":::

4. Oracle Fusion ERP 管理コンソールにログインするために使用する、管理者ユーザー アカウントのユーザー名とパスワードを保存します。 Azure portal で、これらの値を Oracle Fusion ERP アプリケーションの [プロビジョニング] タブの **[管理ユーザー名]** フィールドと **[パスワード]** フィールドに入力する必要があります。

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>ギャラリーからの Oracle Fusion ERP の追加

Azure AD を使用した自動ユーザー プロビジョニング用に Oracle Fusion ERP を構成するには、Azure AD アプリケーション ギャラリーから管理対象の SaaS アプリケーションの一覧に Oracle Fusion ERP を追加する必要があります。

**Azure AD アプリケーション ギャラリーから Oracle Fusion ERP を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Oracle Fusion ERP**」と入力し、結果パネルで **[Oracle Fusion ERP]** を選択します。

    ![結果リストの Oracle Fusion ERP](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>Oracle Fusion ERP への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザー、グループ、またはその両方の割り当てに基づいて、Oracle Fusion ERP でユーザー、グループ、またはその両方が作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> [Oracle Fusion ERP シングル サインオンのチュートリアル](oracle-fusion-erp-tutorial.md)で説明されている手順に従うことで、Oracle Fusion ERP に対して SAML ベースのシングル サインオンを有効にすることもできます。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

> [!NOTE]
> Oracle Fusion ERP の SCIM エンドポイントについて詳しくは、「[Oracle アプリケーション クラウドの一般的な機能の REST API](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html)」をご覧ください。

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Azure AD で Fuze の自動ユーザー プロビジョニングを構成するには、次の操作を行います。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Oracle Fusion ERP]** を選択します。

    ![アプリケーションの一覧の [Oracle Fusion ERP] リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/`」と入力します。 前の手順で取得した管理者ユーザー名とパスワードを **[管理者ユーザー名]** フィールドと **[パスワード]** フィールドに入力します。 Azure AD と Oracle Fusion ERP 間の **[テスト接続]** をクリックします。 

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/admin.png" alt-text="[管理者資格情報] セクションのスクリーンショット。[テスト接続] ボタンのほか、[テナントの U R L]、[管理者ユーザー名]、[管理者パスワード] の各フィールドが表示されています。" border="false":::

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Oracle Fusion ERP]\(Azure Active Directory ユーザーを Oracle Fusion ERP に同期する\)** を選択します。

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png" alt-text="[マッピング] セクションのスクリーンショット。[Name]\(名前\) の [Synchronize Azure Active Directory Users to Oracle Fusion E R P]\(Azure Active Directory ユーザーを Oracle Fusion E R P に同期する\)" border="false"::: が表示されています。

9. **[属性マッピング]** セクションで、Azure AD から Oracle Fusion ERP に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Oracle Fusion ERP のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png" alt-text="[属性マッピング] ページのスクリーンショット。テーブルに、Azure Active Directory と Oracle Fusion E R P の属性および対応する優先順位が表示されています。" border="false":::

10. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Groups to Oracle Fusion ERP]\(Azure Active Directory グループを Oracle Fusion ERP に同期する\)** を選択します。

    ![Oracle Fusion ERP グループのマッピング](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. **[属性マッピング]** セクションで、Azure AD から Oracle Fusion ERP に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新操作で Oracle Fusion ERP のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Oracle Fusion ERP グループの属性](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. Oracle Fusion ERP に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Oracle Fusion ERP にプロビジョニングするユーザー、グループ、またはこれらの両方を定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

    これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Oracle Fusion ERP に対して実行されたすべてのアクションが記載されています。

    Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="connector-limitations"></a>コネクタの制限事項

* Oracle Fusion ERP では、SCIM エンドポイントに対して基本認証のみがサポートされています。
* Oracle Fusion ERP では、グループ プロビジョニングはサポートされていません。
* Oracle Fusion ERP のロールは、Azure AD のグループにマップされます。 Azure AD から Oracle Fusion ERP のユーザーにロールを割り当てるには、Oracle Fusion ERP でロールの後に名前が付けられている目的の Azure AD グループに、ユーザーを割り当てる必要があります。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
