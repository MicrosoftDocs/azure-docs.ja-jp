---
title: Azure portal で Azure AD B2C コンシューマー ユーザー アカウントを作成および削除する
description: Azure portal を使用して Azure AD B2C ディレクトリ内のコンシューマー ユーザーを作成および削除する方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ace0ccb8372ff21a2d3e8721baf09bab539846c2
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033668"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>Azure portal を使用して Azure AD B2C 内のコンシューマー ユーザーを作成および削除する

Azure Active Directory B2C (Azure AD B2C) ディレクトリにコンシューマー アカウントを手動で作成することが必要になるシナリオも考えられます。 Azure AD B2C ディレクトリ内のコンシューマー アカウントは、ユーザーがいずれかのアプリケーションを使用するためにサインアップしたときに作成されるのが最も一般的ですが、Azure portal を使用してプログラムで作成することもできます。 この記事では、Azure portal のユーザーの作成と削除方法に焦点を当てています。

ユーザーを追加または削除するには、アカウントに *ユーザー管理者* または *全体管理者* のロールが割り当てられている必要があります。

## <a name="types-of-user-accounts"></a>ユーザー アカウントの種類

[Azure AD B2C のユーザー アカウントの概要](user-overview.md)に関する記事で述べられているように、Azure AD B2C ディレクトリに作成できるユーザー アカウントには次の 3 種類があります。

* Work
* ゲスト
* コンシューマー

この記事では、Azure portal での **コンシューマー アカウント** の操作に焦点を当てています。 職場アカウントとゲスト アカウントの作成と削除については、「[Azure Active Directory を使用してユーザーを追加または削除する](../active-directory/fundamentals/add-users-azure-active-directory.md)」を参照してください。

## <a name="create-a-consumer-user"></a>コンシューマー ユーザーを作成する

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[管理]** にある **[ユーザー]** を選択します。
1. **[ 新規ユーザー]** を選択します。
1. **[Azure AD B2C ユーザーの作成]** を選択します。
1. **サインイン方法** を選択し、新しいユーザーの **メール** アドレスまたは **ユーザー名** を入力します。 ここで選択するサインイン方法は、Azure AD B2C テナントの *ローカル アカウント* の ID プロバイダーに指定した設定と一致している必要があります (Azure AD B2C テナントで **[管理]**  >  **[ID プロバイダー]** を参照してください)。
1. ユーザーの **名前** を入力します。 通常、これはユーザーのフル ネーム (名と姓) です。
1. (省略可能) ユーザーがサインインできるようにするのを遅らせたい場合は、**サインインをブロック** することができます。 後でサインインを有効にするには、Azure portal でユーザーの **プロファイル** を編集します。
1. **[パスワードの自動生成]** または **[自分でパスワードを作成する]** を選択します。
1. ユーザーの **名** と **姓** を入力します。
1. **［作成］** を選択します

**[サインインのブロック]** を選択していない限り、ユーザーは、指定されたサインイン方法 (メールまたはユーザー名) を使用してサインインすることができます。

## <a name="reset-a-users-password"></a>ユーザーのパスワードのリセット

管理者は、ユーザーが自分のパスワードを忘れた場合に、ユーザーのパスワードをリセットできます。 ユーザーのパスワードをリセットすると、そのユーザーに対して一時パスワードが自動生成されます。 一時パスワードに期限はありません。 次回ユーザーがサインインすると、一時パスワードが生成されてから経過している時間にかかわらず、パスワードは引き続き機能します。 その後、ユーザーはパスワードを永続的なパスワードに再設定する必要があります。 

> [!IMPORTANT]
> ユーザーのパスワードをリセットする前に、[Azure Active Directory B2C で、パスワード リセットを強制するフローを設定してください](force-password-reset.md)。それを行わないと、ユーザーはサインインできません。

ユーザーのパスワードをリセットするには:

1. Azure AD B2C ディレクトリで、 **[ユーザー]** を選択し、次に、パスワードをリセットするユーザーを選択します。
1. リセットを必要としているユーザーを検索して選択し、 **[パスワードのリセット]** を選択します。

    **[パスワードのリセット]** オプションを含む **[Alain Charon - プロファイル]** ページが表示されます。

    ![[パスワードのリセット] オプションが強調表示されているユーザーのプロファイル ページ](media/manage-users-portal/user-profile-reset-password-link.png)

1. **[パスワードのリセット]** ページで、 **[パスワードのリセット]** を選択します。
1. そのパスワードをコピーして、ユーザーに付与します。 ユーザーは次のサインイン プロセス中にパスワードを変更するように求められます。


## <a name="delete-a-consumer-user"></a>コンシューマー ユーザーを削除する

1. Azure AD B2C ディレクトリで、 **[ユーザー]** を選択し、次に、削除するユーザーを選択します。
1. **[削除]** を選択し、 **[はい]** を選択して削除を確定します。

削除後 30 日以内にユーザーを復元する方法、またはユーザーを完全に削除する方法の詳細については、「[Azure Active Directory を使用して最近削除されたユーザーを復元または削除する](../active-directory/fundamentals/active-directory-users-restore.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

自動化されたユーザー管理シナリオ (たとえば、別の ID プロバイダーから Azure AD B2C ディレクトリにユーザーを移行するなど) については、[Azure AD B2C:ユーザーの移行](user-migration.md)に関するページを参照してください。
