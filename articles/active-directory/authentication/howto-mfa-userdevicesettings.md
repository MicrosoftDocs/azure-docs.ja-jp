---
title: Azure Multi-Factor Authentication のユーザー設定を管理する - Azure Active Directory
description: Azure Multi-Factor Authentication の Azure Active Directory ユーザー設定を構成する方法について説明します。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49c389170e9d586a2001009226c6542d4d152f7f
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87418221"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>Azure Multi-factor Authentication のユーザー設定の管理

Azure Multi-Factor Authentication のユーザーを管理しやすくするために、パスワードのリセット、MFA の再登録、または既存の MFA セッションの取り消しをユーザーに要求することができます。 アプリ パスワードを定義したユーザーの場合、これらのパスワードを削除して、これらのアプリケーションのレガシ認証が失敗するようにすることも選択できます。 これらの操作は、ユーザーを支援する必要がある場合やセキュリティ ステータスをリセットする場合に必要になることがあります。

## <a name="manage-user-authentication-options"></a>ユーザー認証オプションを管理する

*認証管理者*ロールが割り当てられている場合、パスワードのリセット、MFA の再登録、またはユーザー オブジェクトからの既存の MFA セッションの取り消しをユーザーに要求できます。 ユーザー設定を管理するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側の **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. 操作の実行対象のユーザーを選択し、 **[認証方法]** を選択します。 ウィンドウの上部で、ユーザーに対して次のいずれかのオプションを選択します。
   - **[パスワードのリセット]** では、ユーザーのパスワードがリセットされ、次のサインイン時に変更する必要がある一時パスワードが割り当てられます。
   - **[MFA の再登録が必要]** では、ユーザーが次回サインインするときに、新しい MFA 認証方法を設定するように要求されます。
   
      > [!NOTE]
      > ユーザーの現在登録されている認証方法は、管理者が MFA を再登録する必要がある場合は削除されません。 ユーザーが MFA を再登録した後、セキュリティ情報を確認し、使用できなくなった以前に登録された認証方法を削除することをお勧めします。
   
   - **[MFA セッションの取り消し]** では、ユーザーの記憶済み MFA セッションがクリアされ、デバイス上のポリシーによって次回要求されたときに MFA を実行するように要求されます。

   ![Azure portal から認証方法を管理する](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

## <a name="delete-users-existing-app-passwords"></a>ユーザーの既存のアプリ パスワードを削除する

必要に応じて、ユーザーが作成したすべてのアプリ パスワードを削除できます。 これらのアプリ パスワードに関連付けられているブラウザー以外のアプリは、新しいアプリ パスワードが作成されるまで機能しなくなります。 この操作を実行するには、*グローバル管理者*のアクセス許可が必要です。

ユーザーのアプリ パスワードを削除するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側の **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. **[Multi-Factor Authentication]** を選択します。 このメニュー オプションを表示するには、必要に応じて右にスクロールします。 次のスクリーンショット例を選択すると、完全な Azure portal ウィンドウとメニューの場所が表示されます。[![Azure AD の [ユーザー] ウィンドウから [Multi-Factor Authentication] を選択する](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. ユーザーまたは管理するユーザーの横にあるチェック ボックスをオンにします。 クイック ステップのオプションの一覧が右側に表示されます。
1. 次の例に示すように、 **[ユーザー設定の管理]** を選択し、 **[選択したユーザーが生成したすべての既存のアプリケーション パスワードを削除する]** チェックボックスをオンにします。![すべての既存のアプリ パスワードを削除する](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. **[保存]** 、 **[閉じる]** の順に選択します。

## <a name="next-steps"></a>次のステップ

この記事では、個々のユーザー設定を構成する方法について説明しました。 Azure Multi-Factor Authentication サービスの全体的な設定を構成するには、「[Azure Multi-Factor Authentication の設定を構成する](howto-mfa-mfasettings.md)」を参照してください。

ユーザーがヘルプを必要とする場合は、[Azure Multi-Factor Authentication のユーザー ガイド](../user-help/multi-factor-authentication-end-user-first-time.md)を参照してください。
