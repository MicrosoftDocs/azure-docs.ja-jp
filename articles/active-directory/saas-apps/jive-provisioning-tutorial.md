---
title: チュートリアル:Jive を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure AD から Jive へのユーザー アカウントの自動的なプロビジョニングおよびプロビジョニング解除を行うために、Jive と Azure AD で実行する必要がある手順について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: ebee5d986007e07d497056620f0cfc437b2da4d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94356401"
---
# <a name="tutorial-configure-jive-for-automatic-user-provisioning"></a>チュートリアル:Jive を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Azure AD から Jive にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除するうえで Jive と Azure AD で実行する必要がある手順について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

*   Azure Active Directory テナント。
*   Jive でのシングル サインオンが有効なサブスクリプション
*   Team Admin アクセス許可がある Jive のユーザー アカウント

## <a name="assigning-users-to-jive"></a>Jive へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

プロビジョニング サービスを構成して有効にする前に、Jive アプリへのアクセスが必要なユーザーを表す Azure AD 内のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順でこれらのユーザーを Jive アプリに割り当てることができます。

[エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-jive"></a>ユーザーを Jive に割り当てる際の重要なヒント

*   単一の Azure AD ユーザーを Jive に割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

*   Jive にユーザーを割り当てるときに、有効なユーザー ロールを選択する必要があります。 "既定のアクセス" ロールはプロビジョニングでは使えません。

## <a name="enable-user-provisioning"></a>ユーザー プロビジョニングの有効化

このセクションでは、Azure AD を Jive のユーザー アカウント プロビジョニング API に接続する手順のほか、プロビジョニング サービスを構成して、Azure AD のユーザーとグループの割り当てに基づいて割り当て済みのユーザー アカウントを Jive で作成、更新、無効化する手順を説明します。

> [!TIP]
> Jive では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Azure Portal](https://portal.azure.com) で説明されている手順に従ってください。 シングル サインオンは自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-user-account-provisioning"></a>ユーザー アカウント プロビジョニングを構成するには

このセクションでは、Active Directory のユーザー アカウントのプロビジョニングを Jive に対して有効にする方法を説明します。
この手順の一環として、Jive.com からの要求に必要なユーザー セキュリティ トークンを指定する必要があります。

1. [Azure Portal](https://portal.azure.com) で、 **[Azure Active Directory]、[エンタープライズ アプリ]、[すべてのアプリケーション]** セクションの順に移動します。

1. シングル サインオンのために Jive を既に構成している場合は、検索フィールドで Jive のインスタンスを検索します。 構成していない場合は、 **[追加]** を選択してアプリケーション ギャラリーで **Jive** を検索します。 検索結果から Jive を選択してアプリケーションの一覧に追加します。

1. Jive のインスタンスを選択してから、 **[プロビジョニング]** タブを選択します。

1. **[プロビジョニング モード]** を **[自動]** に設定します。 

    ![スクリーンショットは、Jive のプロビジョニング ページを示しています。[プロビジョニング モード] は [自動] に設定され、ユーザーが設定できるその他の値が表示されています。](./media/jive-provisioning-tutorial/provisioning.png)

1. **[管理者資格情報]** セクションに次の構成設定を指定します。
   
    a. **[JIVE 管理ユーザー名]** テキストボックスに、Jive.com の **システム管理者** プロファイルが割り当てられている Jive アカウント名を入力します。
   
    b. **[JIVE 管理パスワード]** テキストボックスに、このアカウントのパスワードを入力します。
   
    c. **[JIVE テナント URL]** テキストボックスに、Jive テナント URL を入力します。
      
      > [!NOTE]
      > Jive テナント URL は、組織が Jive にログインするために使用する URL です。  
      > 通常、URL の形式は **www.\<organization\>.jive.com** のようになります。          

1. Azure Portal で、 **[テスト接続]** をクリックして Azure AD が Jive アプリに接続できることを確認します。

1. プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力して、下のチェック ボックスをオンにします。

1. **[保存]** をクリックします。

1. [マッピング] セクションの **[Synchronize Azure Active Directory Users to Jive]\(Azure Active Directory ユーザーを Jive に同期する\)** を選択します。

1. **[属性マッピング]** セクションで、Azure AD から Jive に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Jive のユーザー アカウントとの照合に使用されます。 [保存] ボタンをクリックして変更をコミットします。

1. Jive に対して Azure AD プロビジョニング サービスを有効にするには、[設定] セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

1. **[保存]** をクリックします。

[ユーザーとグループ] セクションで Jive に割り当てたユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ ログを取得できます。このログには、プロビジョニング サービスによって Jive アプリに対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [シングル サインオンの構成](jive-tutorial.md)