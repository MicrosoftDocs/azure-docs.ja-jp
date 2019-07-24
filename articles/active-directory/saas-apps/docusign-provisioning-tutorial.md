---
title: チュートリアル:DocuSign を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory と DocuSign の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 294cd6b8-74d7-44bc-92bc-020ccd13ff12
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 121d147a3f8c91f17e955120b2c14f7dbd3da592
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56191845"
---
# <a name="tutorial-configure-docusign-for-automatic-user-provisioning"></a>チュートリアル:DocuSign を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Azure AD から DocuSign にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除するうえで DocuSign と Azure AD で実行する必要がある手順について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

*   Azure Active Directory テナント。
*   DocuSign でのシングル サインオンが有効なサブスクリプション。
*   Team Admin アクセス許可がある DocuSign のユーザー アカウント。

## <a name="assigning-users-to-docusign"></a>DocuSign へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

プロビジョニング サービスを構成して有効にする前に、DocuSign アプリへのアクセスが必要なユーザーを表す Azure AD 内のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順でこれらのユーザーを DocuSign アプリに割り当てることができます。

[エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-docusign"></a>ユーザーを DocuSign に割り当てる際の重要なヒント

*   単一の Azure AD ユーザーを DocuSign に割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後で追加のユーザーを割り当てられます。

*   DocuSign にユーザーを割り当てるときに、有効なユーザー ロールを選択する必要があります。 "既定のアクセス" ロールはプロビジョニングでは使えません。

> [!NOTE]
> Azure AD は、Docusign のアプリケーションでのグループ プロビジョニングをサポートしていません。ユーザーのみをプロビジョニングすることができます。

## <a name="enable-user-provisioning"></a>ユーザー プロビジョニングの有効化

このセクションでは、Azure AD を DocuSign のユーザー アカウント プロビジョニング API に接続する手順のほか、プロビジョニング サービスを構成して、Azure AD のユーザーとグループの割り当てに基づいて割り当て済みのユーザー アカウントを DocuSign で作成、更新、無効化する手順を説明します。

> [!Tip]
> DocuSign では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Azure Portal](https://portal.azure.com) で説明されている手順に従ってください。 シングル サインオンは自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-user-account-provisioning"></a>ユーザー アカウント プロビジョニングを構成するには

このセクションでは、Active Directory のユーザー アカウントのプロビジョニングを DocuSign に対して有効にする方法を説明します。

1. [Azure Portal](https://portal.azure.com) で、**[Azure Active Directory]、[エンタープライズ アプリ]、[すべてのアプリケーション]** セクションの順に移動します。

1. シングル サインオンのために DocuSign を既に構成している場合は、検索フィールドで DocuSign のインスタンスを検索します。 構成していない場合は、**[追加]** を選択してアプリケーション ギャラリーで **DocuSign** を検索します。 検索結果から DocuSign を選択してアプリケーションの一覧に追加します。

1. DocuSign のインスタンスを選択してから、**[プロビジョニング]** タブを選択します。

1. **[プロビジョニング モード]** を **[自動]** に設定します。 

    ![プロビジョニング](./media/docusign-provisioning-tutorial/provisioning.png)

1. **[管理者資格情報]** セクションに次の構成設定を指定します。
   
    a. **[管理ユーザー名]** テキストボックスに、DocuSign の**システム管理者**プロファイルが割り当てられている DocuSign アカウント名を入力します。
   
    b. **[管理パスワード]** テキストボックスに、このアカウントのパスワードを入力します。

1. Azure Portal で、**[テスト接続]** をクリックして Azure AD が DocuSign アプリに接続できることを確認します。

1. プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力して、チェック ボックスをオンにします。

1. **[保存]** をクリックします。

1. [マッピング] セクションの **[Azure Active Directory ユーザーを DocuSign に同期する]** を選択します。

1. **[属性マッピング]** セクションで、Azure AD から DocuSign に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で DocuSign のユーザー アカウントとの照合に使用されます。 [保存] ボタンをクリックして変更をコミットします。

1. DocuSign に対して Azure AD プロビジョニング サービスを有効にするには、[設定] セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

1. **[保存]** をクリックします。

[ユーザーとグループ] セクションで DocuSign に割り当てたユーザーの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ ログを取得できます。このログには、プロビジョニング サービスによって DocuSign アプリに対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [シングル サインオンの構成](docusign-tutorial.md)
