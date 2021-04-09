---
title: 他の人と共同作業を行う - LUIS
titleSuffix: Azure Cognitive Services
description: アプリの所有者は、オーサリング リソースに共同作成者を追加できます。 これらの共同作業者は、アプリのモデル変更、トレーニング、および発行が可能です。
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/21/2021
ms.openlocfilehash: 5ca13784fe2f9a6a5b448bc838bf508f01b0a9fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101095195"
---
# <a name="add-contributors-to-your-app"></a>アプリに共同作成者を追加する

アプリの所有者は、アプリに共同作成者を追加できます。 これらの共同作業者は、アプリのモデル変更、トレーニング、および発行が可能です。 アカウントを [移行](luis-migration-authoring.md)すると、オーサリング リソースの "_共同作成者_" は、 **[アクセス制御 (IAM)]** ページを使用して、Azure portal で管理されます。 コラボレーターのメール アドレスと "_共同作成者_" ロールを使用して、ユーザーを追加します。

## <a name="add-contributor-to-azure-authoring-resource"></a>Azure オーサリング リソースに共同作成者を追加する

LUIS オーサリング エクスペリエンスが LUIS ポータルの **[管理] -> [Azure リソース]** ページのオーサリング リソースに関連付けられている場合は、移行済みです。

1. Azure portal で、Language Understanding (LUIS) オーサリング リソースを見つけます。 種類は `LUIS.Authoring` です。
1. このリソースの **[アクセス制御 (IAM)]** ページで、**[+ 追加]** を選択し、**[ロールの割り当ての追加]** を選択します。

    ![Azure portal で、オーサリング リソースにロールの割り当てを追加します。](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. **[ロールの割り当ての追加]** ウィンドウで、共同作成者の **[ロール]** を選択します。 **[アクセスの割り当て先]** オプションで **[Azure AD のユーザー、グループ、サービス プリンシパル]** を選択します。 **[選択]** オプションで、ユーザーのメール アドレスを入力します。 同じドメインに対してそのユーザーが複数のメール アドレスで認識されている場合は、"_主要_" なメール アカウントを入力してください。

    ![Azure AD の共同作成者ロールにユーザーのメール アドレスを追加する](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    ユーザーのメール アドレスが見つかったら、アカウントを選択し、**[保存]** を選択します。

    このロールの割り当てで問題が発生した場合は、[Azure のロールの割り当て](../../role-based-access-control/role-assignments-portal.md)と [Azure のアクセス制御のトラブルシューティング](../../role-based-access-control/troubleshooting.md#problems-with-azure-role-assignments)に関する記事を参照してください。

## <a name="view-the-app-as-a-contributor"></a>アプリに共同作成者として表示する

共同作成者として追加されたら、[LUIS ポータルにサインイン](sign-in-luis-portal.md)します。

[!INCLUDE [switch azure directories](includes/switch-azure-directories.md)]

### <a name="users-with-multiple-emails"></a>複数のメール アドレスを持つユーザー

LUIS アプリに共同作成者を追加する場合は、正確なメール アドレスを指定します。 Azure Active Directory (Azure AD) では、1 人のユーザーが複数のメール アカウントのいずれでも使用できますが、LUIS では、共同作成者を追加するときに指定したメール アドレスを使用してサインインする必要があります。

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Azure Active Directory リソース

組織で [Azure Active Directory](../../active-directory/index.yml) (Azure AD) を使用する場合、Language Understanding (LUIS) には、ユーザーが LUIS を使用するときのユーザー アクセスに関する情報に対するアクセス許可が必要です。 LUIS に必要なリソースはごくわずかです。

以下に関する管理者の同意など、管理者の同意があるか、管理者の同意が不要のアカウントでサインアップしようとしたときに、詳細な説明が表示されます。

* 組織のアカウントでアプリにサインインし、アプリにユーザーのプロファイルを読み取らせることを許可します。 基本的な会社情報の読み取りもアプリに許可します。 これにより、ユーザー ID、電子メール、名前などの基本的なプロファイル データを読み取るアクセス許可を LUIS に与えます
* ユーザーが現在アプリを使用していないときでも、ユーザーのデータの参照と更新をアプリに許可します。 このアクセス許可は、ユーザーのアクセス トークンを更新するために必要です。


### <a name="azure-active-directory-tenant-user"></a>Azure Active Directory テナント ユーザー

LUIS では、Azure Active Directory (Azure AD) の標準的な同意フローが使用されます。

テナント管理者は、Azure AD で LUIS を使用するためのアクセス許可を必要とするユーザーと直接的に協力する必要があります。

* まず、ユーザーが LUIS にサインインすると、管理者の承認を必要とするポップアップ ダイアログ ボックスが表示されます。 ユーザーは、続行する前にテナント管理者に問い合わせます。
* 次に、テナント管理者が LUIS にサインインすると、同意フロー ポップアップ ダイアログが表示されます。 これは、管理者がユーザーにアクセス許可を与えるために必要なダイアログです。 管理者がアクセス許可を承認すると、ユーザーは LUIS の使用を続行できます。 テナント管理者は、LUIS にサインインしなくても、LUIS のための[同意](https://account.activedirectory.windowsazure.com/r#/applications)にアクセスできます。 このページでは、一覧をフィルター処理して、名前 `LUIS` を含む項目だけを対象とすることができます。

テナント管理者が、特定のユーザーのみに LUIS を使わせたい場合は、いくつかの可能性のあるソリューションがあります。
* "管理者の同意" を付与します (Azure AD のすべてのユーザーに同意する) が、エンタープライズ アプリケーションのプロパティの下の [ユーザーの割り当てが必要ですか] を [はい] に設定し、最後に目的のユーザーのみをアプリケーションに割り当て/追加します。 この方法では、管理者はアプリに "管理者の同意" を与えますが、それにアクセスできるユーザーを制御できます。
* 2 つ目の解決策は、[Azure AD の ID を使用して Microsoft Graph で管理 API にアクセス](/graph/azuread-identity-access-management-concept-overview)し、特定のユーザーそれぞれに同意することです。

Azure Active Directory ユーザーと同意の詳細を確認します。
* [ご利用のアプリを特定のユーザー セットに制限する](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md)

## <a name="next-steps"></a>次のステップ

* [バージョンを使用](luis-how-to-manage-versions.md)してアプリのライフ サイクルを制御する方法を確認します。
* [オーサリング リソース](luis-how-to-azure-subscription.md#authoring-key)やそのリソースの[共同作成者](luis-how-to-azure-subscription.md#contributions-from-other-authors)などの概念を理解します。
* オーサリング リソースとランタイム リソースの[作成方法](luis-how-to-azure-subscription.md)を確認します
* 新しい[オーサリング リソース](luis-migration-authoring.md)に移行します