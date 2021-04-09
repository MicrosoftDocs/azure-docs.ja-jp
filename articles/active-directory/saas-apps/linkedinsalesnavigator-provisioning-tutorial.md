---
title: チュートリアル:ユーザー プロビジョニング - LinkedIn Sales Navigator、Azure AD
description: Azure Active Directory を構成して、ユーザー アカウントを LinkedIn Sales Navigator に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: 458b527194c1123e266bd6abedf25de18e0cee09
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359359"
---
# <a name="tutorial-configure-linkedin-sales-navigator-for-automatic-user-provisioning"></a>チュートリアル:LinkedIn Sales Navigator を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Azure AD から LinkedIn Sales Navigator にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除するうえで LinkedIn Sales Navigator と Azure AD で実行する必要がある手順について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* Azure Active Directory テナント
* LinkedIn Sales Navigator テナント 
* LinkedIn Account Center へのアクセス許可がある LinkedIn Sales Navigator の管理者アカウント

> [!NOTE]
> Azure Active Directory と LinkedIn Sales Navigator の統合には、[SCIM](http://www.simplecloud.info/) プロトコルが使用されます。

## <a name="assigning-users-to-linkedin-sales-navigator"></a>LinkedIn Sales Navigator へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

プロビジョニング サービスを構成して有効にする前に、LinkedIn Sales Navigator へのアクセスが必要なユーザーを表す Azure AD 内のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順でこれらのユーザーを LinkedIn Sales Navigator に割り当てることができます。

[エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-sales-navigator"></a>ユーザーを LinkedIn Sales Navigator に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを LinkedIn Sales Navigator に割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* ユーザーを LinkedIn Sales Navigator に割り当てるときに、割り当てのダイアログで **ユーザー** ロールを選択する必要があります。 "既定のアクセス" ロールはプロビジョニングでは使えません。

## <a name="configuring-user-provisioning-to-linkedin-sales-navigator"></a>LinkedIn Sales Navigator へのユーザー プロビジョニングの構成

このセクションでは、Azure AD を LinkedIn Sales Navigator の SCIM のユーザー アカウント プロビジョニング API に接続する手順のほか、プロビジョニング サービスを構成して、Azure AD のユーザーとグループの割り当てに基づいて割り当て済みのユーザー アカウントを LinkedIn Sales Navigator で作成、更新、無効化する手順を説明します。

> [!TIP]
> LinkedIn Sales Navigator では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Azure Portal](https://portal.azure.com) で説明されている手順に従ってください。 シングル サインオンは自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-sales-navigator-in-azure-ad"></a>Azure AD で LinkedIn Sales Navigator への自動ユーザー アカウント プロビジョニングを構成するには

まず最初に、LinkedIn アクセス トークンを取得します。 エンタープライズ管理者の場合は、アクセス トークンをセルフ プロビジョニングできます。 アカウント センターで、 **[設定] &gt; [グローバル設定]** をクリックすると、 **[SCIM セットアップ]** パネルが開きます。

> [!NOTE]
> リンクからではなく、アカウント センターに直接アクセスしている場合は、次の手順を使用してアクセスできます。

1. アカウント センターにサインインします。

2. **[管理者] &gt; [管理者設定]** を選択します。

3. 左のサイドバーで **[Advanced Integrations] \(詳細な統合)** をクリックします。 アカウント センターにリダイレクトされます。

4. **[+ SCIM 構成を新しく追加]** をクリックし、各フィールドの入力を行って手順に従います。

    > [!NOTE]
    > ライセンスの自動割り当てを有効にしない場合、ユーザー データのみが同期されるということです。

    ![スクリーンショットには、LinkedIn アカウント センター グローバル設定が示されています。](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_1.PNG)

    > [!NOTE]
    > ライセンスの自動割り当てを有効にする場合、アプリケーション インスタンスとライセンスの種類に注意する必要があります。 ライセンスは、すべてのライセンスが取得されるまで、先着順で割り当てられます。

    ![スクリーンショットには、S C I M セットアップ ページが示されています。](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_2.PNG)

5. **[トークンの生成]** をクリックします。 **[アクセス トークン]** フィールドの下に、アクセス トークンが表示されます。

6. ページを離れる前に、クリップボードまたはコンピューターにアクセス トークンを保存します。

7. 次に、[Azure Portal](https://portal.azure.com) にサインインし、 **[Azure Active Directory] > [エンタープライズ アプリ] > [すべてのアプリケーション]** セクションに移動します。

8. シングル サインオンのために LinkedIn Sales Navigator を既に構成している場合は、検索フィールドで LinkedIn Sales Navigator のインスタンスを検索します。 構成していない場合は、 **[追加]** を選択してアプリケーション ギャラリーで **LinkedIn Sales Navigator** を検索します。 検索結果から LinkedIn Sales Navigator を選択してアプリケーションの一覧に追加します。

9. LinkedIn Sales Navigator のインスタンスを選択してから、 **[プロビジョニング]** タブを選択します。

10. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![スクリーンショットには、[LinkedIn Elevate のプロビジョニング] ページが示されています。](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_3.PNG)

11. **[管理者資格情報]** の下で、以下のフィールドを入力します。

    * **[テナント URL]** フィールドに、「 https://developer.linkedin.com 」と入力します。

    * **[シークレット トークン]** フィールドに、手順 1 で生成したアクセス トークンを入力し、 **[テスト接続]** をクリックします。

    * ポータルの右上に成功通知が表示されます。

12. プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力して、下のチェック ボックスをオンにします。

13. **[保存]** をクリックします。

14. **[属性マッピング]** セクションで、Azure AD から LinkedIn Sales Navigator に同期するユーザーおよびグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で LinkedIn Sales Navigator のユーザー アカウントおよびグループとの照合に使用されることに注意してください。 [保存] ボタンをクリックして変更をコミットします。

    ![スクリーンショットには、属性マッピングを含むマッピングが示されています。](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_4.PNG)

15. LinkedIn Sales Navigator に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

16. **[保存]** をクリックします。

これで、[ユーザーとグループ] セクションで LinkedIn Sales Navigator に割り当てたユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかることに注意してください。後続の同期は、サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ ログを取得できます。このログには、プロビジョニング サービスによって LinkedIn Sales Navigator アプリに対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
