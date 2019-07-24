---
title: チュートリアル:Salesforce Sandbox を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory と Salesforce Sandbox の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: bab73fda-6754-411d-9288-f73ecdaa486d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e0a4eed020728bea5de196eebe438947ae509e4
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56191991"
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>チュートリアル:Salesforce Sandbox を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Azure AD から Salesforce Sandbox にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除するうえで Salesforce Sandbox と Azure AD で実行する必要がある手順について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

*   Azure Active Directory テナント。
*   Salesforce Sandbox for Work または Salesforce Sandbox for Education の有効なテナント。 どちらのサービスにも無料試用版のアカウントを使用できます。
*   Team Admin アクセス許可がある Salesforce Sandbox のユーザー アカウント

## <a name="assigning-users-to-salesforce-sandbox"></a>Salesforce Sandbox へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

プロビジョニング サービスを構成して有効にする前に、Salesforce Sandbox アプリへのアクセスが必要な Azure AD 内のユーザーやグループを決定しておく必要があります。 これを決定したら、[エンタープライズ アプリへのユーザーまたはグループの割り当て](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)に関する手順に従って、これらのユーザーを Salesforce Sandbox アプリに割り当てることができます。

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>ユーザーを Salesforce Sandbox に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Salesforce Sandbox に割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Salesforce Sandbox にユーザーを割り当てるときに、有効なユーザー ロールを選択する必要があります。 "既定のアクセス" ロールはプロビジョニングでは使えません。

> [!NOTE]
> このアプリにより、プロビジョニング プロセスの一環として Salesforce Sandbox からカスタム ロールがインポートされます。顧客はユーザーを割り当てるとき、ロールを選択します。

## <a name="enable-automated-user-provisioning"></a>自動化されたユーザー プロビジョニングを有効にする

このセクションでは、Azure AD を Salesforce Sandbox のユーザー アカウント プロビジョニング API に接続する手順のほか、プロビジョニング サービスを構成して、Azure AD のユーザーとグループの割り当てに基づいて割り当て済みのユーザー アカウントを Salesforce Sandbox で作成、更新、無効化する手順を説明します。

>[!Tip]
>Salesforce Sandbox では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Azure Portal](https://portal.azure.com) で説明されている手順に従ってください。 シングル サインオンは自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="configure-automatic-user-account-provisioning"></a>自動ユーザー アカウント プロビジョニングを構成する

このセクションでは、Active Directory のユーザー アカウントのユーザー プロビジョニングを Salesforce Sandbox に対して有効にする方法について説明します。

1. [Azure Portal](https://portal.azure.com) で、**[Azure Active Directory]、[エンタープライズ アプリ]、[すべてのアプリケーション]** セクションの順に移動します。

1. シングル サインオンのために Salesforce Sandbox を既に構成している場合は、検索フィールドで Salesforce Sandbox のインスタンスを検索します。 構成していない場合は、**[追加]** を選択してアプリケーション ギャラリーで **Salesforce Sandbox** を検索します。 検索結果から Salesforce Sandbox を選択してアプリケーションの一覧に追加します。

1. Salesforce Sandbox のインスタンスを選択してから、**[プロビジョニング]** タブを選択します。

1. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![プロビジョニング](./media/salesforce-sandbox-provisioning-tutorial/provisioning.png)

1. **[管理者資格情報]** セクションに次の構成設定を指定します。
   
    a. **[管理ユーザー名]** ボックスに、Salesforce.com で**システム管理者**プロファイルが割り当てられている Salesforce Sandbox アカウント名を入力します。
   
    b. **[管理パスワード]** テキストボックスに、このアカウントのパスワードを入力します。

1. Salesforce Sandbox のセキュリティ トークンを取得するには、新しいタブを開き、同じ Salesforce Sandbox の管理者アカウントにサインインします。 ページの右上にある自分の名前をクリックし、**[Settings]\(設定\)** をクリックします。

     ![Enable automatic user provisioning](./media/salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "Enable automatic user provisioning")

1. 左側のナビゲーション ウィンドウで **[私の個人情報]** をクリックして関連するセクションを展開し、**[私のセキュリティ トークンのリセット]** をクリックします。
  
    ![Enable automatic user provisioning](./media/salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "Enable automatic user provisioning")

1. **[セキュリティ トークンのリセット]** ページで、**[セキュリティ トークンのリセット]** ボタンをクリックします。

    ![Enable automatic user provisioning](./media/salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "Enable automatic user provisioning")

1. この管理アカウントに関連付けられている電子メールの受信トレイを確認します。 新しいセキュリティ トークンが記載された Salesforce Sandbox.com からの電子メールを探します。

1. トークンをコピーして Azure AD のウィンドウに移動し、**[シークレット トークン]** フィールドに貼り付けます。

1. Azure Portal で、**[テスト接続]** をクリックして Azure AD が Salesforce Sandbox アプリに接続できることを確認します。

1. プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力して、チェック ボックスをオンにします。

1. **[保存]** をクリックします。  
    
1.  [マッピング] セクションの **[Azure Active Directory ユーザーを Salesforce Sandbox に同期する]** を選択します。

1. **[属性マッピング]** セクションで、Azure AD から Salesforce Sandbox に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Salesforce Sandbox のユーザー アカウントとの照合に使用されることに注意してください。 [保存] ボタンをクリックして変更をコミットします。

1. Salesforce Sandbox に対して Azure AD プロビジョニング サービスを有効にするには、[設定] セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

1. **[保存]** をクリックします。

これで、[ユーザーとグループ] セクションで Salesforce Sandbox に割り当てたユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ ログを取得できます。このログには、プロビジョニング サービスによって Salesforce Sandbox アプリに対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [シングル サインオンの構成](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-sandbox-tutorial)
