---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Maxient Conduct Manager Software の統合 | Microsoft Docs
description: Azure Active Directory と Maxient Conduct Manager Software の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2019
ms.author: jeedes
ms.openlocfilehash: 4b57b3fbb338774eb8d66fc4d3c0c817e19aff4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458202"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maxient-conduct-manager-software"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Maxient Conduct Manager Software の統合

このチュートリアルでは、Maxient Conduct Manager Software と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Maxient Conduct Manager Software を統合すると、次のことができます。

* Azure AD を利用して、Maxient Conduct Manager Software のユーザーを認証します
* ユーザーが自分の Azure AD アカウントを使用して Maxient Conduct Manager Software に自動的にサインインできるようになります。


SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) 対応の Maxient Conduct Manager Software サブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、Maxient Conduct Manager Software で使用する Azure AD を構成します。


* Maxient Conduct Manager Software では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-maxient-conduct-manager-software-from-the-gallery"></a>ギャラリーからの Maxient Conduct Manager Software の追加

Azure AD への Maxient Conduct Manager Software の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Maxient Conduct Manager Software を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Maxient Conduct Manager Software**」と入力します。
1. 結果のパネルから **Maxient Conduct Manager Software** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-maxient-conduct-manager-software"></a>Maxient Conduct Manager Software の Azure AD シングル サインオンの構成とテスト

Maxient Conduct Manager Software で Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD と Maxient Conduct Manager Software 間の接続を確立する必要があります。

Maxient Conduct Manager Software を使用して Azure AD の SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO を構成する](#configure-azure-ad-sso)** - ユーザーが Maxient Conduct Manager Software で使用するための認証を行えるようにします
    1. **[Maxient を使用するようにすべてのユーザーを割り当てる](#assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software)** - 組織のすべてのユーザーが認証できるようにします
1. **[Maxient で Azure AD セットアップをテストする](#test-with-maxient)** - 構成が機能するかどうか、正しい属性がリリースされているかどうかを確認します

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Maxient Conduct Manager Software** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションでは、アプリケーションは **IDP** 開始モードで事前に構成されており、必要な URL は既に Azure で事前に設定されています。 構成を保存するには、 **[保存]** ボタンをクリックします。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://cm.maxient.com/<SCHOOLCODE>` という形式で URL を入力します。

    > [!NOTE]
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 Maxient の実装およびサポート担当者と協力して値を取得します。

1. **[Set up single sign-on with SAML]\(SAML でシングル サインオンをセットアップします\)** ページの **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーして、お使いのコンピューターに保存します。  この URL を Maxient の実装およびサポート担当者に提供する必要があります。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

### <a name="assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software"></a>Maxient Conduct Manager Software の認証を行えるようにすべてのユーザーを割り当てる

このセクションでは、Azure システムを使用して Maxient Conduct Manager Software を認証するために、すべてのアカウントにアクセス権を付与します。  Maxient が正常に機能するためには、この手順が **必須** であることに注意することが重要です。  Maxient では Azure AD システムを活用してユーザーが "*認証*" されます。 ユーザーの "*承認*" は、実行しようとしている特定の関数に対して、Maxient システム内で実行されます。 Maxient では、これらの決定を行うためにディレクトリの属性は使用されません。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Maxient Conduct Manager Software]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログで、すべてのユーザー (または適切なグループ) を選択して、Maxient で認証できるように **割り当て** ます。

## <a name="test-with-maxient"></a>Maxient でテストする 

Maxient の実装およびサポート担当者がまだサポート チケットを開いていない場合は、"キャンパス ベースの認証および Azure セットアップ - \<\<School Name\>\>" という件名の電子メールを [support@maxient.com](mailto:support@maxient.com) に送信してください。 電子メールの本文に、**アプリのフェデレーション メタデータ URL** を記載してください。 Maxient のスタッフから、適切な属性がリリースされていることを確認できるテスト リンクを記載した応答を受け取ります。  
    
## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Maxient Conduct Manager Software を試す](https://aad.portal.azure.com/)