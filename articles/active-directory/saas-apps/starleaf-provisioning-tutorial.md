---
title: チュートリアル:StarLeaf を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを StarLeaf に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: zhchia
ms.openlocfilehash: 07c476c0de644ac63c577d466f4691b5cf415334
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94357938"
---
# <a name="tutorial-configure-starleaf-for-automatic-user-provisioning"></a>チュートリアル:StarLeaf を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure Active Directory (Azure AD) が、ユーザー、グループ、またはその両方を StarLeaf に対して自動的にプロビジョニングおよびプロビジョニング解除するよう構成するために、StarLeaf と Azure AD で実行する手順を示すことです。

> [!NOTE]
>  このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> このコネクタは、現在プレビューの段階です。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント。
* [StarLeaf テナント](https://starleaf.com/)。
* 管理者アクセス許可がある StarLeaf のユーザー アカウント。

## <a name="assign-users-to-starleaf"></a>ユーザーを StarLeaf に割り当てる
Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、StarLeaf へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 その後、[こちらの手順](../manage-apps/assign-user-or-group-access-portal.md)に従って、ユーザーとグループを StarLeaf に割り当てることができます。

## <a name="important-tips-for-assigning-users-to-starleaf"></a>ユーザーを StarLeaf に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを StarLeaf に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後で追加のユーザーやグループを割り当てることができます。

* StarLeaf にユーザーを割り当てるとき、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 既定のアクセス ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-starleaf-for-provisioning"></a>プロビジョニングのために StarLeaf を設定する

Azure AD での自動ユーザー プロビジョニングのために StarLeaf を構成する前に、StarLeaf 上で SCIM プロビジョニングを構成する必要があります。

1. [StarLeaf 管理コンソール](https://portal.starleaf.com/#page=login)にサインインします。 **[Integrations]\(統合\)**  >  **[Add integration]\(統合の追加\)** に移動します。

    ![[Integrations]\(統合\) と [Add integration]\(統合の追加\) のオプションが四角で囲まれている、StarLeaf 管理コンソールのスクリーンショット。](media/starleaf-provisioning-tutorial/image00.png)

2. **[Type]\(種類\)** で Microsoft Azure Active Directory になるように選択します。 **[Name]\(名前\)** に適切な名前を入力します。 **[Apply]** をクリックします。

    ![[Type]\(種類\) と [Name]\(名前\) のテキスト ボックスが四角で囲まれている、[Add integration]\(統合の追加\) ダイアログ ボックスのスクリーンショット。](media/starleaf-provisioning-tutorial/image01.png)

3.  **[SCIM base URL]\(SCIM ベース URL\)** と **[Access token]\(アクセス トークン\)** の値が表示されます。 これらの値を、Azure portal の StarLeaf アプリケーションの [プロビジョニング] タブの **[テナント URL]** および **[シークレット トークン]** フィールドに入力します。 

    ![[Type]\(種類\)、[Name]\(名前\)、[SCIM base URL]\(SCIM ベース URL\) のテキスト ボックスが四角で囲まれている、[Edit integration]\(統合の編集\) ダイアログ ボックスのスクリーンショット。](media/starleaf-provisioning-tutorial/image02.png)

## <a name="add-starleaf-from-the-gallery"></a>ギャラリーから StarLeaf を追加する

Azure AD を使用した自動ユーザー プロビジョニング用に StarLeaf を構成するには、Azure AD アプリケーション ギャラリーから管理対象の SaaS アプリケーションの一覧に StarLeaf を追加する必要があります。

**Azure AD アプリケーション ギャラリーから StarLeaf を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**StarLeaf**」と入力し、結果パネルで **[StarLeaf]** を選択します。
    ![結果一覧の StarLeaf](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-starleaf"></a>StarLeaf への自動ユーザー プロビジョニングを構成する

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて StarLeaf のユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[StarLeaf]** を選択します。

    ![アプリケーションの一覧の StarLeaf のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. [管理者資格情報] セクションの **[テナント URL]** および **[シークレット トークン]** に、先ほど取得した **[SCIM Base URL]\(SCIM ベース URL\)** と **[Access Token]\(アクセス トークン\)** の値をそれぞれ入力します。 **[接続テスト]** をクリックして、Azure AD から StarLeaf に接続できることを確認します。 接続できない場合は、使用中の StarLeaf アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to StarLeaf]\(Azure Active Directory ユーザーを StarLeaf に同期する\)** を選びます。

    ![[Synchronize Azure Active Directory Users to StarLeaf]\(Azure Active Directory ユーザーを StarLeaf に同期する\) オプションが表示されている、[マッピング] セクションのスクリーンショット。](media/starleaf-provisioning-tutorial/usermapping.png)

9. **[属性マッピング]** セクションで、Azure AD から StarLeaf に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で StarLeaf のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![9 つのマッピングが表示されている [属性マッピング] セクションのスクリーンショット。](media/starleaf-provisioning-tutorial/userattribute.png)


10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。


11. StarLeaf に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[スコープ]** で目的の値を選択して、StarLeaf にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

13. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって StarLeaf に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み方について詳しくは、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="connector-limitations"></a>コネクタの制限事項

* 現在、StarLeaf ではグループのプロビジョニングはサポートされていません。 
* StarLeaf では、 **[emails]** と **[userName]** の値に同じソース値を設定する必要があります。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログを確認してレポートを取得する](../app-provisioning/check-status-user-account-provisioning.md)方法を確認します。
