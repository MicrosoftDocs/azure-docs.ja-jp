---
title: B2B コラボレーション用の SaaS アプリの構成 - Azure AD
description: Azure Active Directory B2B コラボレーション用の SaaS アプリを構成したり、追加の使用可能なリソースを表示したりする方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0db5e609a5e6b7d74809810a50f86d72ae85675
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90705512"
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>B2B コラボレーション用の SaaS アプリの構成

Azure Active Directory (Azure AD) B2B コラボレーションは、Azure AD と統合されているほとんどのアプリで動作します。 このセクションでは、いくつかの一般的な SaaS アプリを Azure AD B2B と共に使用できるように構成する手順について説明します。

アプリ固有の手順を説明する前に、いくつかの一般的な原則を示しておきます。

* ほとんどのアプリでは、ユーザー セットアップを手動で起動する必要があります。 つまり、アプリでユーザーも手動で作成する必要があります。

* Dropbox のように、自動セットアップをサポートしているアプリでは、アプリから個別の招待が作成されます。 ユーザーは各招待に確実に応じる必要があります。

* ゲスト ユーザーの壊れたユーザー プロファイル ディスク (UPD) の問題を緩和するには、常にユーザー属性の **[ユーザー識別子]** を **[user.mail]** に設定します。


## <a name="dropbox-business"></a>Dropbox Business

ユーザーが自分の組織のアカウントを使用してサインインできるようにするには、Azure AD を Security Assertion Markup Language (SAML) ID プロバイダーとして使用するように Dropbox Business を手動で構成する必要があります。 Dropbox Business は、このように構成されていない場合、ユーザーにメッセージを表示するなどの方法で、ユーザーが Azure AD を使用してサインインできるようにすることができません。

1. Dropbox Business アプリを Azure AD に追加するには、左側のウィンドウで **[エンタープライズ アプリケーション]** を選択し、 **[追加]** をクリックします。

   ![[エンタープライズ アプリケーション] ページの [追加] ボタン](media/configure-saas-apps/add-dropbox.png)

2. **[アプリケーションの追加]** ウィンドウで検索ボックスに「**dropbox**」と入力し、結果の一覧で **[Dropbox for Business]** を選択します。

   ![[アプリケーションの追加] ページで "dropbox" を検索する](media/configure-saas-apps/add-app-dialog.png)

3. **[シングル サインオン]** ページの左側のウィンドウで **[シングル サインオン]** を選択し、 **[ユーザー識別子]** ボックスに「**user.mail**」と入力します (既定では、UPN に設定されます)。

   ![アプリのシングル サインオンの構成](media/configure-saas-apps/configure-app-sso.png)

4. Dropbox の構成に使用する証明書をダウンロードするには、 **[DropBox の構成]** を選択し、一覧の **[SAML Single Sign On Service URL (SAML シングル サインオン サービス URL)]** を選択します。

   ![Dropbox 構成の証明書のダウンロード](media/configure-saas-apps/download-certificate.png)

5. **[シングル サインオン]** ページのサインオン URL を使用して Dropbox にサインインします。

   ![Dropbox のサインイン ページを示すスクリーンショット](media/configure-saas-apps/sign-in-to-dropbox.png)

6. メニューで **[管理コンソール]** を選択します。

   ![Dropbox メニューの [管理コンソール] リンク](media/configure-saas-apps/dropbox-menu.png)

7. **[Authentication (認証)]** ダイアログ ボックスで **[More (詳細)]** を選択し、証明書をアップロードしてから、 **[Sign in URL (サインイン URL)]** ボックスに SAML シングル サインオン URL を入力します。

   ![折りたたまれた [Authentication (認証)] ダイアログ ボックスの [More (詳細)] リンク](media/configure-saas-apps/dropbox-auth-01.png)

   ![展開された [Authentication (認証)] ダイアログ ボックスの [Sign in URL (サインイン URL)] リンク](media/configure-saas-apps/paste-single-sign-on-URL.png)

8. Azure Portal で自動ユーザー セットアップを構成するには、左側のウィンドウで **[プロビジョニング]** を選択し、 **[プロビジョニング モード]** ボックスで **[自動]** を選択して、 **[承認]** を選択します。

   ![Azure Portal での自動ユーザー プロビジョニングの構成](media/configure-saas-apps/set-up-automatic-provisioning.png)

Dropbox アプリでゲストまたはメンバー ユーザーがセットアップされると、ユーザーは Dropbox から個別の招待を受け取ります。 Dropbox のシングル サインオンを使用するには、招待されたユーザーが招待のリンクをクリックして、招待に応じる必要があります。

## <a name="box"></a>ボックス
SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Box ゲスト ユーザーを Azure AD アカウントで認証できるようにすることができます。 この手順では、Box.com にメタデータをアップロードします。

1. エンタープライズ アプリから Box アプリを追加します。

2. 次の順序で、シングル サインオンを構成します。

   ![シングル サインオンの構成設定を示すスクリーンショット](media/configure-saas-apps/configure-box-sso.png)

   a. **[サインオン URL]** ボックスで、サインオン URL が Azure Portal で Box 用に適切に設定されていることを確認します。 この URL は、Box.com テナントの URL です。 名前付け規則 ( *https://.box.com* ) に従う必要があります。  
   **[識別子]** はこのアプリには該当しませんが、必須フィールドとして表示されます。

   b. **[ユーザー識別子]** ボックスに、「**user.mail**」と入力します (ゲスト アカウントの SSO 用)。

   c. **[SAML 署名証明書]** で、 **[新しい証明書の作成]** をクリックします。

   d. Box.com テナントが Azure AD を ID プロバイダーとして使用するようにする構成を開始するには、メタデータ ファイルをダウンロードし、ローカル ドライブに保存します。

   e. メタデータ ファイルを Box サポート チームに転送します。このチームが、シングル サインオンの構成を行ってくれます。

3. Azure AD 自動ユーザー セットアップの場合は、左側のウィンドウで **[プロビジョニング]** を選択し、 **[承認する]** を選択します。

   ![Box への接続を Azure AD に承認する](media/configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Dropbox での招待されるユーザーのように、Box での招待されるユーザーも、Box アプリからの招待に応じる必要があります。

## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する以下の記事を参照してください。

- [Azure AD B2B コラボレーションとは](what-is-b2b.md)
- [動的グループと B2B コラボレーション](use-dynamic-groups.md)
- [B2B コラボレーション ユーザーの要求マッピング](claims-mapping.md)
- [Microsoft 365 の外部共有](o365-external-user.md)

