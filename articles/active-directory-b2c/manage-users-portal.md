---
title: Azure portal で Azure AD B2C コンシューマー ユーザー アカウントを作成および削除する
description: Azure portal を使用して Azure AD B2C ディレクトリ内のコンシューマー ユーザーを作成および削除する方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/09/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5d6d6169f8662c9b973fb7f624a590322f62b0b5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85387526"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>Azure portal を使用して Azure AD B2C 内のコンシューマー ユーザーを作成および削除する

Azure Active Directory B2C (Azure AD B2C) ディレクトリにコンシューマー アカウントを手動で作成することが必要になるシナリオも考えられます。 Azure AD B2C ディレクトリ内のコンシューマー アカウントは、ユーザーがいずれかのアプリケーションを使用するためにサインアップしたときに作成されるのが最も一般的ですが、Azure portal を使用してプログラムで作成することもできます。 この記事では、Azure portal のユーザーの作成と削除方法に焦点を当てています。

ユーザーを追加または削除するには、アカウントに*ユーザー管理者*または*全体管理者*のロールが割り当てられている必要があります。

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="types-of-user-accounts"></a>ユーザー アカウントの種類

[Azure AD B2C のユーザー アカウントの概要](user-overview.md)に関する記事で述べられているように、Azure AD B2C ディレクトリに作成できるユーザー アカウントには次の 3 種類があります。

* Work
* ゲスト
* コンシューマー

この記事では、Azure portal での**コンシューマー アカウント**の操作に焦点を当てています。 職場アカウントとゲスト アカウントの作成と削除については、「[Azure Active Directory を使用してユーザーを追加または削除する](../active-directory/fundamentals/add-users-azure-active-directory.md)」を参照してください。

## <a name="create-a-consumer-user"></a>コンシューマー ユーザーを作成する

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[管理]** にある **[ユーザー]** を選択します。
1. **[ 新規ユーザー]** を選択します。
1. **[Azure AD B2C ユーザーの作成]** を選択します。
1. **サインイン方法**を選択し、新しいユーザーの**メール** アドレスまたは**ユーザー名**を入力します。 ここで選択するサインイン方法は、Azure AD B2C テナントの*ローカル アカウント*の ID プロバイダーに指定した設定と一致している必要があります (Azure AD B2C テナントで **[管理]**  >  **[ID プロバイダー]** を参照してください)。
1. ユーザーの**名前**を入力します。 通常、これはユーザーのフル ネーム (名と姓) です。
1. (省略可能) ユーザーがサインインできるようにするのを遅らせたい場合は、**サインインをブロック**することができます。 後でサインインを有効にするには、Azure portal でユーザーの**プロファイル**を編集します。
1. **[パスワードの自動生成]** または **[自分でパスワードを作成する]** を選択します。
1. ユーザーの**名**と**姓**を入力します。
1. **［作成］** を選択します

**[サインインのブロック]** を選択していない限り、ユーザーは、指定されたサインイン方法 (メールまたはユーザー名) を使用してサインインすることができます。

## <a name="delete-a-consumer-user"></a>コンシューマー ユーザーを削除する

1. Azure AD B2C ディレクトリで、 **[ユーザー]** を選択し、次に、削除するユーザーを選択します。
1. **[削除]** を選択し、 **[はい]** を選択して削除を確定します。

削除後 30 日以内にユーザーを復元する方法、またはユーザーを完全に削除する方法の詳細については、「[Azure Active Directory を使用して最近削除されたユーザーを復元または削除する](../active-directory/fundamentals/active-directory-users-restore.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

自動化されたユーザー管理シナリオ (たとえば、別の ID プロバイダーから Azure AD B2C ディレクトリにユーザーを移行するなど) については、[Azure AD B2C:ユーザーの移行](user-migration.md)に関するページを参照してください。
