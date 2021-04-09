---
title: チュートリアル:MerchLogix を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを MerchLogix に自動的に対してプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: zhchia
ms.openlocfilehash: 4d0a52f06a751fba57a00615e2d57485ff740d04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359699"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>チュートリアル:MerchLogix を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、MerchLogix に対するユーザーまたはグループのプロビジョニングまたはプロビジョニング解除を自動的に実行するように Azure Active Directory (Azure AD) を構成するために、MerchLogix と Azure AD で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント
* MerchLogix テナント
* ユーザー プロビジョニングに必要な SCIM エンドポイント URL とシークレット トークンを提供できる MerchLogix の技術担当者

## <a name="adding-merchlogix-from-the-gallery"></a>ギャラリーからの Merchlogix の追加

Azure AD を使用して自動的にユーザー プロビジョニングを行うように MerchLogix を構成する前に、Azure AD アプリケーション ギャラリーからご利用のマネージド SaaS アプリケーションの一覧に MerchLogix を追加する必要があります。

**Azure AD アプリケーション ギャラリーから MerchLogix を追加するには、以下の手順を行います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] セクション][2]

3. MerchLogix を追加するには、ダイアログの上部にある **[新しいアプリケーション]** ボタンをクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに、「**MerchLogix**」と入力します。

5. 結果パネルで **[MerchLogix]** を選択してから、 **[追加]** ボタンをクリックして MerchLogix を SaaS アプリケーションの一覧に追加します。

    ![ギャラリーからの追加セクションのスクリーンショット。[名前を入力] テキスト ボックスが強調表示されています。][4]

## <a name="assigning-users-to-merchlogix"></a>MerchLogix へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。 

自動ユーザー プロビジョニングを構成して有効にする前に、MerchLogix へのアクセスが必要な Azure AD 内のユーザーやグループを決定しておく必要があります。 決定したら、次の手順に従って、これらのユーザーやグループを MerchLogix に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>ユーザーを MerchLogix に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを MerchLogix に割り当てて、ご自分の初期の自動ユーザー プロビジョニング構成をテストすることをお勧めします。 テストが成功すれば、後でユーザーやグループを追加で割り当てられます。

* MerchLogix にユーザーを割り当てるときは、アプリケーション固有の有効なロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>MerchLogix への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて MerchLogix でのユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> MerchLogix では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[MerchLogix シングル サインオンのチュートリアル](merchlogix-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Azure AD で MerchLogix に対する自動ユーザー プロビジョニングを構成するには:

1. [Azure Portal](https://portal.azure.com) にサインインし、 **[Azure Active Directory] > [エンタープライズ アプリ] > [すべてのアプリケーション]** に移動します。

2. SaaS アプリケーションの一覧から MerchLogix を選択します。

3. **[プロビジョニング]** タブを選択します。

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[MerchLogix - プロビジョニング] セクションのスクリーンショット。[プロビジョニング] オプションが選択されています。[プロビジョニング モード] が [自動] に設定され、[接続のテスト] オプションが選択されています。](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. **[管理者資格情報]** セクションで、次の操作を行います。

    * **[テナント URL]** フィールドに、MerchLogix の技術担当者から提供された SCIM エンドポイント URL を入力します。

    * **[シークレット トークン]** フィールドに、MerchLogix の技術担当者から提供されたシークレット トークンを入力します。

6. 手順 5 に示されたフィールドに値を入力したら、 **[テスト接続]** をクリックして、Azure AD が MerchLogix に確実に接続されるようにします。 接続できない場合は、使用中の MerchLogix アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

7. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

8. **[保存]** をクリックします。

9. **[マッピング]** セクションで **[Synchronize Azure Active Directory Users to MerchLogix]\(Azure Active Directory ユーザーを MerchLogix に同期する\)** を選択します。

10. **[属性マッピング]** セクションで、Azure AD から MerchLogix に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で MerchLogix のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

11. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to MerchLogix]\(Azure Active Directory グループを MerchLogix に同期する\)** を選択します。

12. **[属性マッピング]** セクションで、Azure AD から MerchLogix に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新操作で MerchLogix のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

13. MerchLogix に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

14. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって MerchLogix に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
