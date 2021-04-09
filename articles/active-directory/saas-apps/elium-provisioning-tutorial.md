---
title: チュートリアル:Elium を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Elium に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: e8f027ccc577df79e561fca7194c20b6cc7ef2c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005505"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>チュートリアル:Elium を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Elium にユーザーまたはグループを自動的にプロビジョニングまたはプロビジョニング解除するように、Elium と Azure Active Directory (Azure AD) を構成する方法について説明します。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービス上に構築されるコネクタについて説明します。 このサービスが実行する内容やしくみについての重要な情報と、よく寄せられる質問については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> このコネクタは、現在プレビューの段階です。 プレビュー段階の Azure 機能の一般的な使用条件については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、次の前提条件が満たされていることを前提としています。

* Azure AD テナント
* [Elium テナント](https://www.elium.com/pricing/)
* 管理者アクセス許可がある Elium のユーザー アカウント

## <a name="assigning-users-to-elium"></a>Elium へのユーザーの割り当て

Azure AD では、選択したアプリへのアクセス権を付与するユーザーを決定するために *割り当て* という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Elium にアクセスする必要がある Azure AD のユーザーとグループを決定しておく必要があります。 その後、[エンタープライズ アプリケーションへのユーザーまたはグループの割り当て](../manage-apps/assign-user-or-group-access-portal.md)に関する記事の手順に従って、それらのユーザーとグループを Elium に割り当てます。

## <a name="important-tips-for-assigning-users-to-elium"></a>ユーザーを Elium に割り当てる際の重要なヒント 

単一の Azure AD ユーザーを Elium に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後で追加のユーザーやグループを割り当てることができます。

Elium にユーザーを割り当てるときは、アプリケーション固有の有効なロール (使用可能な場合) を割り当てダイアログ ボックスで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="set-up-elium-for-provisioning"></a>Elium をプロビジョニング用に設定する

Azure AD を使用して自動ユーザー プロビジョニング用に Elium を構成する前に、Elium で System for Cross-domain Identity Management (SCIM) プロビジョニングを有効にする必要があります。 次の手順に従います。

1. Elium にサインインし、 **[マイ プロファイル]**  >  **[設定]** に移動します。

    ![Elium の [設定] メニュー項目](media/Elium-provisioning-tutorial/setting.png)

1. 左下隅にある **[ADVANCED]\(詳細設定\)** の **[セキュリティ]** を選択します。

    ![Elium の [セキュリティ] リンク](media/Elium-provisioning-tutorial/security.png)

1. **[テナントの URL]** と **[シークレット トークン]** の値をコピーします。 これらの値は後で、Azure portal で Elium アプリケーションの **[プロビジョニング]** タブの対応するフィールドで使用します。

    ![Elium の [テナントの URL] フィールドと [シークレット トークン] フィールド](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>ギャラリーから Elium を追加する

Azure AD を使用した自動ユーザー プロビジョニング用に Elium を構成するには、Azure AD アプリケーション ギャラリーから管理対象のサービスとしてのソフトウェア (SaaS) アプリケーションの一覧に Elium を追加する必要があります。 次の手順に従います。

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![[Azure Active Directory] メニュー項目](common/select-azuread.png)

1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

     ![Azure AD の[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

1. 新しいアプリケーションを追加するには、ウィンドウの上部の **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] リンク](common/add-new-app.png)

1. 検索ボックスに「**Elium**」と入力し、結果一覧で **[Elium]** を選択してから、 **[追加]** ボタンを選択してアプリケーションを追加します。

    ![ギャラリーの検索ボックス](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>Elium への自動ユーザー プロビジョニングを構成する

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて Elium のユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> [Elium シングル サインオンのチュートリアル](Elium-tutorial.md)の手順に従って、SAML (Security Assertion Markup Language) に基づいて Elium のシングル サインオンを有効にできます。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

Azure AD で Elium の自動ユーザー プロビジョニングを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインし、 **[エンタープライズ アプリケーション]** **[すべてのアプリケーション]** の順に選択します。

    ![Azure AD の[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

1. アプリケーションの一覧で **[Elium]** を選択します。

    ![[エンタープライズ アプリケーション] ブレードのアプリケーション一覧](common/all-applications.png)

1. **[プロビジョニング]** タブを選択します。

    ![[エンタープライズ アプリケーション] ブレードの [プロビジョニング] タブ](common/provisioning.png)

1. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング モード] の [自動] 設定](common/provisioning-automatic.png)

1. **[管理者資格情報]** セクションの **[テナントの URL]** フィールドに「 **\<tenantURL\>/scim/v2**」と入力します。 (**tenantURL** は、以前に Elium 管理コンソールから取得した値です。)また、 **[シークレット トークン]** フィールドに Elium の **シークレット トークン** を入力します。 最後に、 **[テスト接続]** を選択して、Azure AD から Elium に接続できることを確認します。 接続できない場合は、使用中の Elium アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![[管理者資格情報] の [テナントの URL] フィールドと [シークレット トークン] フィールド](common/provisioning-testconnection-tenanturltoken.png)

1. プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力し、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

1. **[保存]** をクリックします。

1. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Elium]\(Azure Active Directory ユーザーを Elium に同期する\)** を選択します。

    ![Elium に Azure AD ユーザーを同期するための同期リンク](media/Elium-provisioning-tutorial/usermapping.png)

1. **[属性マッピング]** セクションで、Azure AD から Elium に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Elium のユーザー アカウントとの照合に使用されます。 すべての変更をコミットするには、 **[保存]** を選択します。

    ![Azure AD と Elium の間の属性マッピング](media/Elium-provisioning-tutorial/userattribute.png)

1. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の手順を参照してください。

1. Elium に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![[オン] に設定された [プロビジョニング状態]](common/provisioning-toggle-on.png)

1. **[設定]** セクションの **[スコープ]** ドロップダウン リスト ボックスで目的の値を選択して、Elium にプロビジョニングするユーザーとグループを定義します。

    ![プロビジョニングの [スコープ] リスト ボックス](common/provisioning-scope.png)

1. プロビジョニングの準備ができたら、 **[保存]** を選択します。

    ![プロビジョニング構成の [保存] ボタン](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期が開始されます。 この初期同期プロセスは、以降の同期よりも時間がかかります。 プロビジョニングに必要な時間の詳細については、「[ユーザーをプロビジョニングするにはどのくらいの時間がかかりますか](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)」を参照してください。

**[現在の状態]** セクションを使用して進行状況を監視し、リンクをクリックしてプロビジョニング アクティビティ レポートを取得します。 プロビジョニング アクティビティ レポートには、Azure AD プロビジョニング サービスによって Elium で実行されたすべてのアクションが記述されます。 詳細については、「[ユーザー プロビジョニングの状態を確認する](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)」を参照してください。 Azure AD プロビジョニング ログを読むには、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)。
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
