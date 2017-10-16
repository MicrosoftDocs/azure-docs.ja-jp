---
title: "チュートリアル: ServiceNow を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs"
description: "Azure AD から ServiceNow に対してユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除する方法を説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: e338804a0c5fcb09dfdfd9bf18ae4d0466cd9a82
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-servicenow-for-automatic-user-provisioning-with-azure-active-directory"></a>チュートリアル: ServiceNow を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Azure AD から ServiceNow にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除するうえで ServiceNow と Azure AD で実行する必要がある手順について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

*   Azure Active Directory テナント。
*   ServiceNow for Work または ServiceNow for Education の有効なテナントが必要です。 どちらのサービスにも無料試用版のアカウントを使用できます。
*   Team Admin アクセス許可がある ServiceNow のユーザー アカウント

## <a name="assigning-users-to-servicenow"></a>ServiceNow へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

プロビジョニング サービスを構成して有効にする前に、ServiceNow アプリへのアクセスが必要なユーザーを表す Azure AD 内のユーザーやグループを決定しておく必要があります。 決定したら、[エンタープライズ アプリへのユーザーまたはグループの割り当て](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)に関する手順に従って、これらのユーザーを ServiceNow アプリに割り当てることができます。


> [!IMPORTANT]
>*   単一の Azure AD ユーザーを ServiceNow に割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。
>*   ServiceNow にユーザーを割り当てるときに、有効なユーザー ロールを選択する必要があります。 "既定のアクセス" ロールはプロビジョニングでは使えません。

## <a name="enable-automated-user-provisioning"></a>自動化されたユーザー プロビジョニングを有効にする

このセクションでは、Azure AD を ServiceNow のユーザー アカウント プロビジョニング API に接続する手順のほか、プロビジョニング サービスを構成して、Azure AD のユーザーとグループの割り当てに基づいて割り当て済みのユーザー アカウントを ServiceNow で作成、更新、無効化する手順を説明します。

> [!TIP]
>ServiceNow では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Azure Portal](https://portal.azure.com) で説明されている手順に従ってください。 シングル サインオンは自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="configure-automatic-user-account-provisioning"></a>自動ユーザー アカウント プロビジョニングを構成する

1. [Azure Portal](https://portal.azure.com) で、**[Azure Active Directory]、[エンタープライズ アプリ]、[すべてのアプリケーション]** セクションの順に移動します。

2. シングル サインオンのために ServiceNow を既に構成している場合は、検索フィールドで ServiceNow のインスタンスを検索します。 構成していない場合は、**[追加]** を選択してアプリケーション ギャラリーで **ServiceNow** を検索します。 検索結果から ServiceNow を選択してアプリケーションの一覧に追加します。

3. ServiceNow のインスタンスを選択してから、**[プロビジョニング]** タブを選択します。

4. **[プロビジョニング モード]** を **[自動]** に設定します。 

    ![プロビジョニング](./media/active-directory-saas-servicenow-provisioning-tutorial/provisioning.png)

5. [管理者資格情報] セクションで、次の手順を実行します。
   
    a. **[ServiceNow インスタンス名]** ボックスに、ServiceNow インスタンス名を入力します。

    b. **[ServiceNow 管理ユーザー名]** ボックスに、管理者のユーザー名を入力します。

    c. **[ServiceNow 管理パスワード]** ボックスに、管理者のパスワードを入力します。

6. Azure Portal で、**[テスト接続]** をクリックして Azure AD が ServiceNow アプリに接続できることを確認します。 接続が失敗した場合、使用中の ServiceNow アカウントに Team Admin アクセス許可があることを確認して、**"管理者資格情報"** の手順をもう一度試してください。

7. プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力して、下のチェック ボックスをオンにします。

8. **[保存]** をクリックします。

9. [マッピング] セクションの **[Azure Active Directory ユーザーを ServiceNow に同期する]** を選びます。

10. **[属性マッピング]** セクションで、Azure AD から ServiceNow に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で ServiceNow のユーザー アカウントとの照合に使用されます。 [保存] ボタンをクリックして変更をコミットします。

11. ServiceNow に対して Azure AD プロビジョニング サービスを有効にするには、[設定] セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

12. **[保存]** をクリックします。

[ユーザーとグループ] セクションで ServiceNow に割り当てたユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、サービスが実行されている限り約 20 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、プロビジョニング サービスによって ServiceNow アプリに対して実行されたすべてのアクションが記載されています。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)
* [シングル サインオンの構成](active-directory-saas-servicenow-tutorial.md)


