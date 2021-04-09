---
title: 'チュートリアル: Azure Active Directory を使用した自動ユーザー プロビジョニングのための Velpic の構成 | Microsoft Docs'
description: ユーザー アカウントを Velpic に自動的にプロビジョニング/プロビジョニング解除するように Azure Active Directory を構成する方法について説明します。
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
ms.openlocfilehash: cdd4fb96a42d154ccd8b508950283978ddf58ef4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94354905"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>チュートリアル: 自動ユーザー プロビジョニングのための Velpic の構成

このチュートリアルでは、Azure AD から Velpic にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除するために Velpic と Azure AD で実行する必要がある手順について説明します。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* Azure Active Directory テナント
* [Enterprise プラン](https://www.velpic.com/pricing.html)以上の有効な Velpic テナント
* 管理者アクセス許可がある Velpic のユーザー アカウント

## <a name="assigning-users-to-velpic"></a>Velpic へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。 

プロビジョニング サービスを構成して有効にする前に、Velpic アプリへのアクセスが必要なユーザーを表す Azure AD 内のユーザーやグループを決定しておく必要があります。 決定したら、次の手順に従って、それらのユーザーを Velpic アプリに割り当てることができます。

[エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>ユーザーを Velpic に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Velpic に割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Velpic にユーザーを割り当てるときは、割り当てダイアログで **ユーザー** ロールまたは別の有効なアプリケーション固有ロール (使用可能な場合) を選択する必要があります。 **[既定のアクセス]** ロールはプロビジョニングに使用できないので、このロールのユーザーはスキップされます。

## <a name="configuring-user-provisioning-to-velpic"></a>Velpic へのユーザー プロビジョニングの構成

このセクションでは、Azure AD を Velpic のユーザー アカウント プロビジョニング API に接続する手順と、Azure AD のユーザーとグループの割り当てに基づいて、割り当て済みのユーザー アカウントを Velpic で作成、更新、無効化するようにプロビジョニング サービスを構成する手順を説明します。

> [!TIP]
> [Azure portal](https://portal.azure.com) で提供される手順に従って、Velpic で SAML ベースのシングル サインオンを有効にすることもできます。 シングル サインオンは自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>Azure AD で Velpic への自動ユーザー アカウント プロビジョニングを構成するには

1. [Azure Portal](https://portal.azure.com) で、 **[Azure Active Directory]、[エンタープライズ アプリ]、[すべてのアプリケーション]** セクションの順に移動します。

2. シングル サインオンのために Velpic を既に構成している場合は、検索フィールドで Velpic のインスタンスを検索します。 構成していない場合は、**[追加]** を選択し、アプリケーション ギャラリーで **Velpic** を検索します。 検索結果から Velpic を選択し、アプリケーションの一覧に追加します。

3. Velpic のインスタンスを選択し、**[プロビジョニング]** タブを選択します。

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![Velpic のプロビジョニング](./media/velpic-provisioning-tutorial/Velpic1.png)

5. **[管理者資格情報]** セクションで、Velpic の **テナントの URL とシークレット トークン** を入力します (これらの値は Velpic アカウントで確認できます ( **[Manage]\(管理\)**  >  **[Integration]\(統合\)**  >  **[Plugin]\(プラグイン\)**  >  **[SCIM]** ))

    ![承認の値](./media/velpic-provisioning-tutorial/Velpic2.png)

6. Azure portal で、**[テスト接続]** をクリックして Azure AD が Velpic アプリに接続できることを確認します。 接続できない場合は、お使いの Velpic アカウントに管理者アクセス許可があることを確認してから、手順 5. をもう一度試します。

7. プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力して、下のチェック ボックスをオンにします。

8. **[保存]** をクリックします。

9. [マッピング] セクションの **[Synchronize Azure Active Directory Users to Asana]\(Azure Active Directory ユーザーを Velpic に同期する\)** を選択します。

10. **[属性マッピング]** セクションで、Azure AD から Velpic に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新操作で Velpic のユーザー アカウントとの照合に使用されます。 [保存] ボタンをクリックして変更をコミットします。

11. Velpic に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

12. **[保存]** をクリックします。

これで、[ユーザーとグループ] セクションで Velpic に割り当てたユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかることに注意してください。後続の同期は、サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、プロビジョニング サービスによって実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)