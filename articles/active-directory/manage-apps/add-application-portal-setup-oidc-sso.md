---
title: 'クイックスタート: Azure Active Directory (Azure AD) テナント内のアプリケーションへの OIDC ベースのシングル サインオン (SSO) を設定する'
description: このクイックスタートでは、Azure Active Directory (Azure AD) テナント内のアプリケーションへの OIDC ベースのシングル サインオン (SSO) を設定するプロセスについて説明します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.openlocfilehash: e5695d5ded92e9bb41d39a082790cfbb456dbc38
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258356"
---
# <a name="quickstart-set-up-oidc-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>クイックスタート: Azure Active Directory (Azure AD) テナント内のアプリケーションへの OIDC ベースのシングル サインオン (SSO) を設定する

Azure Active Directory (Azure AD) テナントに追加したアプリケーションへのシングル サインオン (SSO) を設定することで、簡略化されたユーザー ログインの利用を開始します。 SSO を設定すると、ユーザーが自分の Azure AD の資格情報を使用してアプリケーションにサインインできるようになります。 SSO は Azure AD の Free Edition に含まれています。

OIDC ベースの SSO の詳細については、「[OIDC ベースのシングル サインオンについて理解する](configure-oidc-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure AD テナントに追加したアプリケーションへの SSO を設定するには、以下が必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 次のいずれかのロール: グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者、またはサービス プリンシパルの所有者。
- 既に事前構成済みで Azure AD ギャラリーに追加されている、SSO をサポートするアプリケーション。 ほとんどのアプリでは、SSO に Azure AD を使用できます。 Azure AD ギャラリー内のアプリは事前構成されています。 ご自分のアプリが一覧に表示されていない場合、またはカスタム開発されたアプリの場合でも、Azure AD で使用できます。 目次にあるチュートリアルやその他のドキュメントを確認してください。 このクイックスタートは、SSO 用に事前構成され、アプリ開発者によって Azure AD ギャラリーに追加されたアプリに重点を置いています。
- 省略可能:[アプリの表示](view-applications-portal.md)の完了。
- 省略可能:[アプリの追加](add-application-portal.md)の完了。
- 省略可能:[アプリの構成](add-application-portal-configure.md)の完了。
- 省略可能:[アプリへのユーザーの割り当て](add-application-portal-assign-users.md)の完了。

>[!IMPORTANT]
>このクイックスタートの手順をテストする場合は、非運用環境を使用してください。

## <a name="enable-single-sign-on-for-an-app"></a>アプリへのシングル サインオンを有効にする

SSO に OIDC 標準を使用するアプリを追加すると、セットアップ ボタンが表示されます。 このボタンを選択すると、アプリケーションのサイトに移動するので、アプリに対するサインアップ プロセスを完了させます。 アプリを追加するプロセスについては、このシリーズで前に説明したアプリの追加に関するクイックスタートを参照してください。 既に追加されているアプリケーションを構成する場合は、最初のクイックスタートを参照してください。 テナント内に既に存在するアプリケーションを表示する手順が説明されています。 

アプリケーションへのシングル サインオンを設定するには、次を行います。

1. このシリーズの前のクイックスタートでは、ID 管理に Azure AD テナントを使用するアプリを追加する方法を学習しました。 アプリ開発者が OIDC 標準を使用して SSO を実装してある場合は、アプリを追加するときにサインアップ ボタンが表示されます。 

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png" alt-text="[シングルサインオン] オプションと [サインアップ] ボタンが表示されているスクリーンショット。" lightbox="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png":::


2. **[サインアップ]** を選択すると、アプリ開発者のサインオン ページが表示されます。 Azure Active Directory サインイン資格情報を使用してサインインします。 

   > [!IMPORTANT]
    > アプリケーションのサブスクリプションを既に持っている場合は、ユーザーの詳細およびテナントまたはディレクトリ情報の検証が行われます。 アプリケーションがユーザーを確認できない場合は、アプリケーション サービスへのサインアップまたはエラー ページにリダイレクトされます。

3. 認証が成功すると、管理者の同意を求めるダイアログが表示されます。 **[組織の代理として同意する]** を選択して、 **[同意する]** を選択します。 ユーザーおよび管理者の同意の詳細については、「[ユーザーおよび管理者の同意について](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)」を参照してください。

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/consent.png" alt-text="アプリの同意画面が表示されているスクリーンショット。" lightbox="media/add-application-portal-setup-oidc-sso/consent.png":::

4. アプリケーションがテナントに追加され、アプリケーションのホーム ページが表示されます。


> [!TIP]
> Graph API を使用してアプリの管理を自動化できます。[Microsoft Graph API によるアプリ管理の自動化](/graph/application-saml-sso-configure-api)に関するページを参照してください。

これは、OIDC ベースのアプリを Azure AD に追加する方法の詳細を説明するビデオです。

Azure Active Directory で OIDC ベースのアプリを追加する

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタート シリーズを完了したら、アプリを削除してテスト テナントをクリーンアップすることを検討してください。 アプリの削除については、このシリーズの最後のクイックスタートである[アプリの削除](delete-application-portal.md)に関する記事で説明されています。

## <a name="next-steps"></a>次のステップ

次の記事に進み、アプリを削除する方法を学習してください。
> [!div class="nextstepaction"]
> [アプリを削除する](delete-application-portal.md)
