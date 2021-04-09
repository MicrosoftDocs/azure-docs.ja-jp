---
title: クイック スタート:Azure Active Directory (Azure AD) テナントからアプリケーションを削除する
description: このクイックスタートでは、Azure portal を使用して、Azure Active Directory (Azure AD) テナントからアプリケーションを削除します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 1/5/2021
ms.author: kenwith
ms.openlocfilehash: 187f4a1d524e0343130808aa4b4c18222fa982c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99259272"
---
# <a name="quickstart-delete-an-application-from-your-azure-active-directory-azure-ad-tenant"></a>クイック スタート:Azure Active Directory (Azure AD) テナントからアプリケーションを削除する

このクイックスタートでは、Azure portal を使用して、Azure Active Directory (Azure AD) テナントに追加されたアプリケーションを削除します。

SSO と Azure の詳細については、「[シングル サインオン (SSO) とは](what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure AD テナントからアプリケーションを削除するには、次のものが必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 次のいずれかのロール: グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者、またはサービス プリンシパルの所有者。
- 省略可能:[アプリの表示](view-applications-portal.md)の完了。
- 省略可能:[アプリの追加](add-application-portal.md)の完了。
- 省略可能:[アプリの構成](add-application-portal-configure.md)の完了。
- 省略可能:[アプリへのユーザーの割り当て](add-application-portal-assign-users.md)の完了。
- 省略可能:[シングル サインオンの設定](add-application-portal-setup-sso.md)の完了。

>[!IMPORTANT]
>このクイックスタートの手順をテストする場合は、非運用環境を使用してください。

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Azure AD テナントからアプリケーションを削除する

Azure AD テナントからアプリケーションを削除するには:

1. Azure AD portal で、 **[エンタープライズ アプリケーション]** を選択します。 次に、削除するアプリケーションを探して選択します。 ここでは、前のクイックスタートで追加した **GitHub_test** アプリケーションを削除します。
1. 左側のペインの **[管理]** セクションで、 **[プロパティ]** を選択します。
1. **[削除]** を選択してから **[はい]** を選び、Azure AD テナントからアプリを削除することを確定します。

> [!TIP]
> Graph API を使用してアプリの管理を自動化できます。[Microsoft Graph API によるアプリ管理の自動化](/graph/application-saml-sso-configure-api)に関するページを参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタート シリーズを完了したら、アプリを削除してテスト テナントをクリーンアップすることを検討してください。 アプリの削除については、このクイックスタートで説明しました。

## <a name="next-steps"></a>次のステップ

クイックスタート シリーズを完了しました。 次に、シングル サインオン (SSO) について学習しましょう。[SSO の概要](what-is-single-sign-on.md)に関するページを参照してください または、アプリ管理でのベスト プラクティスについてお読みください。
> [!div class="nextstepaction"]
> [アプリケーション管理のベスト プラクティス](application-management-fundamentals.md)
