---
title: 'チュートリアル: Cerner Central のユーザー プロビジョニング - Azure AD'
description: Azure Active Directory を構成して、Cerner Central のリストに自動でユーザーをプロビジョニングする方法を説明します。
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: arvinh
ms.openlocfilehash: 1f82cab1172e7293e2a5910d35280eefb30ed49e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94357455"
---
# <a name="tutorial-configure-cerner-central-for-automatic-user-provisioning"></a>チュートリアル: Cerner Central を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Azure AD から Cerner Central のユーザー リストにユーザー アカウントを自動的にプロビジョニングおよびプロビジョニング解除するために Cerner Central と Azure AD で実行する必要がある手順について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* Azure Active Directory テナント
* Cerner Central テナント

> [!NOTE]
> Azure Active Directory と Cerner Central の統合には、[SCIM](http://www.simplecloud.info/) プロトコルが使用されます。

## <a name="assigning-users-to-cerner-central"></a>Cerner Central へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。 

プロビジョニング サービスを構成して有効にする前に、Cerner Central へのアクセスが必要なユーザーとなる Azure AD 内のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順でこれらのユーザーを Cerner Central に割り当てることができます。

[エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>ユーザーを Cerner Central に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Cerner Central に割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* 1 人のユーザーが初期テストを完了すると、Cerner ソリューション (Cerner Central ではない) にアクセスし、Cerner のユーザー リストにプロビジョニングされる予定のユーザーについて、ユーザー リストのすべてを割り当てることを Cerner Central が推奨します。  その他の Cerner ソリューションでは、ユーザー リスト内のユーザーのこのリストを利用します。

* ユーザーを Cerner Central に割り当てるときに、割り当てのダイアログで **ユーザー** ロールを選択する必要があります。 "既定のアクセス" ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configuring-user-provisioning-to-cerner-central"></a>Cerner Central へのユーザー プロビジョニングの構成

このセクションでは、Azure AD を Cerner Central の SCIM のユーザー アカウント プロビジョニング API を使用して Cerner Central のユーザー リストに接続する手順のほか、プロビジョニング サービスを構成して、Azure AD のユーザーとグループの割り当てに基づいてロール割り当て済みのユーザー アカウントを Cerner Central で作成、更新、無効化する手順を説明します。

> [!TIP]
> Cerner Central では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Azure portal](https://portal.azure.com) で説明されている手順に従ってください。 シングル サインオンは自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。 詳細については、[Cerner Central のシングル サインオンに関するチュートリアル](cernercentral-tutorial.md)をご覧ください。

### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Azure AD で Cerner Central への自動ユーザー アカウント プロビジョニングを構成する

Cerner Central にユーザー アカウントをプロビジョニングするには、Cerner に Cerner Central システム アカウントを要求し、Azure AD が Cerner の SCIM エンドポイントへ接続する際に使用できる OAuth ベアラー トークンを生成する必要があります。 また、運用環境にデプロイする前に Cerner のサンドボックス環境で統合を実施することが推奨されています。

1. 最初に、Cerner と Azure AD の統合を管理する担当者が CernerCare アカウントを保持していることを確認します。必要なドキュメントにアクセスして手順を完了するためには、このアカウントが必要です。 必要に応じて、下記の URL を使って、該当する各環境に CernerCare アカウントを作成します。

   * サンドボックス: https://sandboxcernercare.com/accounts/create

   * 運用: https://cernercare.com/accounts/create  

2. 次に、Azure AD のためにシステム アカウントを作成する必要があります。 以下の手順を使って、サンドボックス環境と運用環境のシステム アカウントを要求します。

   * 方法: https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * サンドボックス: https://sandboxcernercentral.com/system-accounts/

   * 運用: https://cernercentral.com/system-accounts/

3. 次に、各システム アカウントのために OAuth ベアラー トークンを生成します。 これを実行するには、以下の手順に従ってください。

   * 方法: https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * サンドボックス: https://sandboxcernercentral.com/system-accounts/

   * 運用: https://cernercentral.com/system-accounts/

4. 最後に、Cerner のサンドボックスと運用環境の両方のユーザー リスト領域 ID を取得して構成を完了する必要があります。 取得方法については、https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIM をご覧ください。 

5. この手順から、Azure AD を構成して Cerner にユーザー アカウントをプロビジョニングします。 [Azure Portal](https://portal.azure.com) にサインインし、**[Azure Active Directory] > [エンタープライズ アプリ] > [すべてのアプリケーション]** セクションに移動します。

6. シングル サインオンのために Cerner Central を既に構成している場合は、検索フィールドで Cerner Central のインスタンスを検索します。 または、**[追加]** を選択して、アプリケーション ギャラリーで **[Cerner Central]** を検索します。 検索結果から Cerner Central を選択して、アプリケーションの一覧に追加します。

7. Cerner Central のインスタンスを選択してから、**[プロビジョニング]** タブを選択します。

8. **[プロビジョニング モード]** を **[自動]** に設定します。

   ![Cerner Central でのプロビジョニング](./media/cernercentral-provisioning-tutorial/Cerner.PNG)

9. **[管理者資格情報]** で、以下のフィールドを入力します。

   * **[テナント URL]** フィールドに、次の形式で URL を入力します。その際、"User-Roster-Realm-ID" を手順 4. で取得した領域 ID に置き換えます。

    > サンドボックス: https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 
    > 
    > 運用: https://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * **[シークレット トークン]** フィールドに、手順 3 で生成した OAuth ベアラー トークンを入力し、**[テスト接続]** をクリックします。

   * ポータルの右上に成功通知が表示されます。

1. プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力して、下のチェック ボックスをオンにします。

1. **[保存]** をクリックします。

1. **[属性マッピング]** セクションで、Azure AD から Cerner Central に同期されるユーザーとグループの属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Cerner Central のユーザー アカウントおよびグループとの照合に使用されます。 [保存] ボタンをクリックして変更をコミットします。

1. Cerner Central に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

1. **[保存]** をクリックします。

これで、[ユーザーとグループ] セクションで Cerner Central に割り当てたユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ ログを取得できます。このログには、プロビジョニング サービスによって Cerner Central アプリに対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [Cerner Central: Azure AD を使用して ID データを公開する](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD)
* [チュートリアル: Cerner Central を構成して Azure Active Directory を使ったシングル サインオンに対応する](cernercentral-tutorial.md)
* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)