---
title: チュートリアル:Slack のユーザー プロビジョニング - Azure AD
description: Azure Active Directory を構成して、ユーザー アカウントを Slack に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cd8e483d6c189e311fdb1925ad0f2effc2affe1
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849150"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>チュートリアル: Slack を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Azure AD から Slack にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除するうえで Slack と Azure AD で実行する必要がある手順について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* Azure Active Directory テナント
* [Plus プラン](https://aadsyncfabric.slack.com/pricing)以上の有効な Slack テナント
* Team Admin アクセス許可がある Slack のユーザー アカウント

注:Azure AD プロビジョニング統合では、Plus プラン以上の Slack チームで使用できる [Slack SCIM API](https://api.slack.com/scim) が必要です。

## <a name="assigning-users-to-slack"></a>Slack へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

プロビジョニング サービスを構成して有効にする前に、Slack アプリへのアクセスが必要なユーザーを表す Azure AD 内のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順でこれらのユーザーを Slack アプリに割り当てることができます。

[エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>ユーザーを Slack に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Slack に割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* ユーザーを Slack に割り当てるときに、割り当てのダイアログで**ユーザー**または "グループ" のロールを選択する必要があります。 "既定のアクセス" ロールはプロビジョニングでは使えません。

## <a name="configuring-user-provisioning-to-slack"></a>Slack へのユーザー プロビジョニングの構成 

このセクションでは、Azure AD を Slack のユーザー アカウント プロビジョニング API に接続する手順のほか、プロビジョニング サービスを構成して、Azure AD のユーザーとグループの割り当てに基づいて割り当て済みのユーザー アカウントを Slack で作成、更新、無効化する手順を説明します。

**ヒント:** Slack では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Azure portal](https://portal.azure.com) 上に示される手順に従ってください。 シングル サインオンは自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Azure AD で Slack への自動ユーザー アカウント プロビジョニングを構成するには

1. [Azure Portal](https://portal.azure.com) で、 **[Azure Active Directory]、[エンタープライズ アプリ]、[すべてのアプリケーション]** セクションの順に移動します。

2. シングル サインオンのために Slack を既に構成している場合は、検索フィールドで Slack のインスタンスを検索します。 構成していない場合は、 **[追加]** を選択してアプリケーション ギャラリーで **Slack** を検索します。 検索結果から Slack を選択してアプリケーションの一覧に追加します。

3. Slack のインスタンスを選択してから、 **[プロビジョニング]** タブを選択します。

4. **[プロビジョニング モード]** を **[自動]** に設定します。

   ![Slack のプロビジョニング](./media/slack-provisioning-tutorial/slack1.png)

5. **[管理者資格情報]** セクションにある **[承認する]** をクリックします。 これで、ブラウザーの新しいウィンドウで Slack 承認ダイアログが開きます。

6. 新しいウィンドウで、Team Admin アカウントを使用して Slack にサインインします。 表示された承認ダイアログで、プロビジョニングを有効にしたい Slack チームを選択して **[承認する]** を選択します。 終わったら Azure Portal に戻り、プロビジョニング構成を完了します。

    ![承認ダイアログ](./media/slack-provisioning-tutorial/slackauthorize.png)

7. Azure Portal で、 **[テスト接続]** をクリックして Azure AD が Slack アプリに接続できることを確認します。 接続が失敗した場合、使用中の Slack アカウントに Team Admin アクセス許可があることを確認して、"承認" の手順をもう一度試してください。

8. プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力して、下のチェック ボックスをオンにします。

9. **[Save]** をクリックします。

10. [マッピング] セクションの **[Synchronize Azure Active Directory Users to Slack (Azure Active Directory ユーザーを Slack に同期する)]** を選択します。

11. **[属性マッピング]** セクションで、Azure AD から Slack に同期されるユーザー属性を確認します。 **[Matching (照合)]** プロパティとして選択されている属性は、更新処理で Slack のユーザー アカウントとの照合に使用されることに注意してください。 [保存] ボタンをクリックして変更をコミットします。

12. Slack に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

13. **[Save]** をクリックします。

これで、[ユーザーとグループ] セクションで Slack に割り当てたユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかることに注意してください。後続の同期は、サービスが実行されている限り約 10 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、プロビジョニング サービスによって Slack アプリに対して実行されたすべてのアクションが記載されています。

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>(省略可能) Slack へのグループ オブジェクト プロビジョニングの構成

必要に応じて、Azure AD から Slack へのグループ オブジェクトのプロビジョニングを有効にできます。 これは "ユーザーのグループの割り当て" とは異なります。ここでは、グループのメンバーに加えて実際のグループ オブジェクトが Azure AD から Slack にレプリケートされます。 たとえば、Azure AD に "自分のグループ" という名前のグループがある場合、Slack 内に "自分のグループ" という同一のグループが作成されます。

### <a name="to-enable-provisioning-of-group-objects"></a>グループ オブジェクトのプロビジョニングを有効にするには:

1. [マッピング] セクションの **[Synchronize Azure Active Directory Groups to Slack (Azure Active Directory グループを Slack に同期する)]** を選択します。

2. [属性マッピング] ブレードで、[有効] を [はい] に設定します。

3. **[属性マッピング]** セクションで、Azure AD から Slack に同期するグループ属性を確認します。 **[Matching (照合)]** プロパティとして選択されている属性は、更新処理で Slack のグループとの照合に使用されることに注意してください。 

4. **[Save]** をクリックします。

これで、 **[ユーザーとグループ]** セクションで Slack に割り当てられたグループ オブジェクトが、Azure AD から Slack に完全に同期されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ ログを取得できます。このログには、プロビジョニング サービスによって Slack アプリに対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="connector-limitations"></a>コネクタの制限事項

* Slack の **displayName**属性を構成する際、次の動作に注意してください。

  * 値は (例: 2 人のユーザーは同じ表示名を持てる) が完全に一意ではありません

  * 非英語の文字、スペース、大文字と小文字をサポートしています。 
  
  * 許可されている句読点はピリオド、アンダースコア、ハイフン、アポストロフィ、かっこ (例: **( [ { } ] )** ) と区切り記号 (例: **, /;** ) です。
  
  * Slack のワークプレイスまたは組織内でこれらの 2 つの設定が構成されている場合のみ、更新を行います。**プロファイル同期が有効**、**ユーザーが表示名を変更できない**。
  
* Slack の **userName** 属性は 21 文字未満で、一意の値を持つ必要があります。

* Slack では、属性 **userName** と **email** との照合のみが許可されます。  

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
