---
title: ユーザーのパスワードをリセットする - Azure Active Directory | Microsoft Docs
description: Azure Active Directory を使用してユーザーのパスワードをリセットする方法について説明します。
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 09/05/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8809f8c168e7095f05587c7a572e08287637dc5a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034593"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>Azure Active Directory を使用してユーザーのパスワードをリセットする

パスワードを忘れた場合、ユーザーがデバイスからロックアウトされた場合、またはユーザーがパスワードを受け取っていない場合、管理者はユーザーのパスワードをリセットできます。

>[!Note]
>ご利用の Azure AD テナントがユーザーのホーム ディレクトリでない場合、管理者はユーザーのパスワードをリセットすることができません。 つまり、ユーザーが別の組織からのアカウント、Microsoft アカウント、または Google アカウントを使用して組織にサインインしている場合、管理者はそれらのパスワードをリセットすることはできません。<br><br>ユーザーが権限ソースを Windows Server Active Directory としている場合、管理者はパスワード ライトバックを有効にした場合にのみパスワードをリセットすることができます。<br><br>ユーザーが権限ソースを外部の Azure AD としている場合、管理者はパスワードをリセットすることができません。 パスワードをリセットできるのは、該当するユーザーまたは外部の Azure AD の管理者のみとなります。

>[!Note]
>管理者ではないユーザーが、自分の職場または学校のパスワードをリセットする方法については、「[職場または学校のパスワードをリセットする](../user-help/active-directory-passwords-update-your-own-password.md)」をご覧ください。

## <a name="to-reset-a-password"></a>パスワードをリセットするには

1. [Azure portal](https://portal.azure.com/) にユーザー管理者またはパスワード管理者としてサインインします。 利用できるロールの詳細については、「[Azure AD の組み込みロール](../roles/permissions-reference.md)」を参照してください。

2. **[Azure Active Directory]** 、 **[ユーザー]** の順に選択し、リセットを必要としているユーザーを検索して選択し、 **[パスワードのリセット]** を選択します。

    **[パスワードのリセット]** オプションを含む **[Alain Charon - プロファイル]** ページが表示されます。

    ![[パスワードのリセット] オプションが強調表示されているユーザーのプロファイル ページ](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. **[パスワードのリセット]** ページで、 **[パスワードのリセット]** を選択します。

    > [!Note]
    > Azure Active Directory を使用している場合、ユーザー用に一時パスワードが自動生成されます。 オンプレミスで Active Directory を使用している場合は、自分でユーザー用のパスワードを作成します。

4. そのパスワードをコピーして、ユーザーに付与します。 ユーザーは次のサインイン プロセス中にパスワードを変更するように求められます。

    >[!Note]
    >一時パスワードに期限はありません。 次回ユーザーがサインインすると、一時パスワードが生成されてから経過している時間にかかわらず、パスワードは引き続き機能します。

> [!IMPORTANT]
> 管理者がユーザーのパスワードをリセットできず、Azure AD Connect サーバーのアプリケーション イベント ログにエラー コード hr=80231367 が表示される場合、Active Directory でユーザーの属性を確認してください。  属性 **AdminCount** が 1 に設定されている場合、管理者はユーザーのパスワードをリセットできません。  管理者がユーザーのパスワードをリセットするには、属性 **AdminCount** を 0 に設定する必要があります。


## <a name="next-steps"></a>次のステップ

ユーザーのパスワードをリセットしたら、次の基本的な手順を行うことができます。

- [ユーザーを追加または削除する](add-users-azure-active-directory.md)

- [ユーザーにロールを割り当てる](active-directory-users-assign-role-azure-portal.md)

- [プロファイル情報を追加または変更する](active-directory-users-profile-azure-portal.md)

- [基本的なグループを作成し、メンバーを追加する](active-directory-groups-create-azure-portal.md)

または、委任の割り当て、ポリシーの使用、ユーザー アカウントの共有など、より複雑なユーザー シナリオを実行することもできます。 他の実行可能なアクションの詳細については、「[Azure Active Directory のユーザー管理のドキュメント](../enterprise-users/index.yml)」を参照してください。
