---
title: 'チュートリアル: GoToMeeting を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs'
description: Azure Active Directory と GoToMeeting の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: ff377b0f93968eb6743187e4e659f4e888e5010e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358901"
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>チュートリアル: GoToMeeting を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Azure AD から GoToMeeting にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除するうえで GoToMeeting と Azure AD で実行する必要がある手順について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

*   Azure Active Directory テナント。
*   GoToMeeting でのシングル サインオンが有効なサブスクリプション。
*   Team Admin アクセス許可がある GoToMeeting のユーザー アカウント。

## <a name="assigning-users-to-gotomeeting"></a>GoToMeeting へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

プロビジョニング サービスを構成して有効にする前に、GoToMeeting アプリへのアクセスが必要なユーザーを表す Azure AD 内のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順でこれらのユーザーを GoToMeeting アプリに割り当てることができます。

[エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>ユーザーを GoToMeeting に割り当てる際の重要なヒント

*   単一の Azure AD ユーザーを GoToMeeting に割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

*   GoToMeeting にユーザーを割り当てるときに、有効なユーザー ロールを選択する必要があります。 "既定のアクセス" ロールはプロビジョニングでは使えません。

## <a name="enable-automated-user-provisioning"></a>自動化されたユーザー プロビジョニングを有効にする

このセクションでは、Azure AD を GoToMeeting のユーザー アカウント プロビジョニング API に接続する手順のほか、プロビジョニング サービスを構成して、Azure AD のユーザーとグループの割り当てに基づいて割り当て済みのユーザー アカウントを GoToMeeting で作成、更新、無効化する手順を説明します。

> [!TIP]
> GoToMeeting では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Azure Portal](https://portal.azure.com) で説明されている手順に従ってください。 シングル サインオンは自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-account-provisioning"></a>自動ユーザー アカウント プロビジョニングを構成するには:

1. [Azure Portal](https://portal.azure.com) で、 **[Azure Active Directory]、[エンタープライズ アプリ]、[すべてのアプリケーション]** セクションの順に移動します。

1. シングル サインオンのために GoToMeeting を既に構成している場合は、検索フィールドで GoToMeeting のインスタンスを検索します。 構成していない場合は、 **[追加]** を選択してアプリケーション ギャラリーで **GoToMeeting** を検索します。 検索結果から GoToMeeting を選択してアプリケーションの一覧に追加します。

1. GoToMeeting のインスタンスを選択してから、 **[プロビジョニング]** タブを選択します。

1. **[プロビジョニング モード]** を **[自動]** に設定します。 

    ![Azure portal の GoToMeeting の [プロビジョニング] タブのスクリーンショット。 [プロビジョニング モード] が [自動] に設定され、管理ユーザー名、パスワード、および [テスト接続] が強調表示されています。](./media/citrixgotomeeting-provisioning-tutorial/provisioning.png)

1. [管理者資格情報] セクションで、次の手順を実行します。
   
    a. **[GoToMeeting 管理ユーザー名]** ボックスに、管理者のユーザー名を入力します。

    b. **[GoToMeeting 管理パスワード]** ボックスに、管理者のパスワードを入力します。

1. Azure Portal で、**[テスト接続]** をクリックして Azure AD が GoToMeeting アプリに接続できることを確認します。 接続が失敗した場合、使用中の GoToMeeting アカウントに Team Admin アクセス許可があることを確認して、**"管理者資格情報"** の手順をもう一度試してください。

1. プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力して、下のチェック ボックスをオンにします。

1. **[保存]** をクリックします。

1. [マッピング] セクションの **[Synchronize Azure Active Directory Users to GoToMeeting]\(Azure Active Directory ユーザーを GoToMeeting に同期する\)** を選択します。

1. **[属性マッピング]** セクションで、Azure AD から GoToMeeting に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で GoToMeeting のユーザー アカウントとの照合に使用されます。 [保存] ボタンをクリックして変更をコミットします。

1. GoToMeeting に対して Azure AD プロビジョニング サービスを有効にするには、[設定] セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

1. **[保存]** をクリックします。

これで、[ユーザーとグループ] セクションで GoToMeeting に割り当てたユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ ログを取得できます。このログには、プロビジョニング サービスによって GoToMeeting アプリに対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [シングル サインオンの構成](./citrix-gotomeeting-tutorial.md)