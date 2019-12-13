---
title: Azure MFA を使用してユーザーとデバイスを管理する - Azure Active Directory
description: 追加のセキュリティ確認を強制するなど、管理者がユーザー設定を変更する方法。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a1e6dc3ed1c1f9af6f6b935cdd3a6ceaedbe7a4
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848257"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>クラウドでの Azure Multi-factor Authentication によるユーザー設定の管理

管理者は、ユーザーとデバイスの次の設定を管理できます。

* 連絡方法をもう一度提供するようユーザーに要求する
* アプリ パスワードを削除する
* 信頼されているすべてのデバイスについて MFA を要求する

## <a name="manage-authentication-methods"></a>認証方法を管理する

管理者が認証管理者ロールを割り当てると、パスワードのリセット、MFA の再登録、またはユーザー オブジェクトからの既存の MFA セッションの取り消しをユーザーに要求できます。

![Azure portal から認証方法を管理する](./media/howto-mfa-userdevicesettings/manage-authentication-methods.png)

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 左側の **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. 操作の実行対象のユーザーを選択し、 **[認証方法]** を選択します。
   - **[パスワードのリセット]** では、ユーザーのパスワードがリセットされ、次のサインイン時に変更する必要がある一時パスワードが割り当てられます。
   - **[MFA の再登録が必要]** では、ユーザーが次回サインインするときに、新しい MFA 認証方法を設定するように要求されます。
   - **[MFA セッションの取り消し]** では、ユーザーの記憶済み MFA セッションがクリアされ、デバイス上のポリシーによって次回要求されたときに MFA を実行するように要求されます。

## <a name="delete-users-existing-app-passwords"></a>ユーザーの既存のアプリ パスワードを削除する

この設定では、ユーザーが作成したすべてのアプリ パスワードが削除されます。 これらのアプリ パスワードに関連付けられているブラウザー以外のアプリは、新しいアプリ パスワードが作成されるまで機能しなくなります。 この操作を実行するには、グローバル管理者のアクセス許可が必要です。

### <a name="how-to-delete-users-existing-app-passwords"></a>ユーザーの既存のアプリ パスワードを削除する方法

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側の **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
3. 右側のツール バーで **[Multi-Factor Authentication]** を選択します。 多要素認証ページが開きます。
4. ユーザーまたは管理するユーザーの横にあるチェック ボックスをオンにします。 クイック ステップのオプションの一覧が右側に表示されます。
5. **[Manage user settings]** (ユーザー設定の管理) を選択します。
6. **[選択したユーザーが生成したすべての既存のアプリ パスワードを削除する]** チェック ボックスをオンにします。
   ![すべての既存のアプリ パスワードを削除する](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. **[Save]** をクリックします。
8. **[閉じる]** をクリックします。

## <a name="next-steps"></a>次の手順

- 詳細については、「[Azure Multi-Factor Authentication の設定を構成する](howto-mfa-mfasettings.md)」をご覧ください。
- ヘルプが必要な場合、「[2 段階認証のユーザー ガイド](../user-help/multi-factor-authentication-end-user.md)」を参照してください。
