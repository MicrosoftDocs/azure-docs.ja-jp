---
title: Azure portal でキュー データへのアクセスの承認方法を選択する
titleSuffix: Azure Storage
description: Azure portal を使用してキュー データにアクセスするときに、ポータルでは内部的に Azure Storage への要求が発行されます。 Azure Storage へのこれらの要求は、Azure AD アカウントまたはストレージ アカウント アクセス キーのいずれかを使用して認証および許可できます。
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozguns
ms.date: 02/10/2021
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: contperf-fy21q1
ms.openlocfilehash: fbb96fc1d2cb12e1aede07295357abfaa6d6b67f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100385015"
---
# <a name="choose-how-to-authorize-access-to-queue-data-in-the-azure-portal"></a>Azure portal でキュー データへのアクセスの承認方法を選択する

[Azure portal](https://portal.azure.com) を使用してキュー データにアクセスするときに、ポータルでは内部的に Azure Storage への要求が発行されます。 Azure Storage への要求は、Azure AD アカウントまたはストレージ アカウント アクセス キーのいずれかを使用して承認できます。 ポータルでは、どの方法を使用しているかを示し、適切なアクセス許可がある場合は、それら 2 つを切り替えることができます。

## <a name="permissions-needed-to-access-queue-data"></a>キュー データにアクセスするために必要なアクセス許可

Azure portal でキュー データへのアクセスを承認する方法に応じて、特定のアクセス許可が必要になります。 ほとんどの場合、これらのアクセス許可は Azure ロールベースのアクセス制御 (Azure RBAC) を使用して提供されます。 Azure RBAC の詳細については、「[Azure ロールベースのアクセス制御 (Azure RBAC) とは](../../role-based-access-control/overview.md)」を参照してください。

### <a name="use-the-account-access-key"></a>アカウント アクセス キーを使用する

アカウント アクセス キーを使用してキュー データにアクセスするには、Azure RBAC アクション **Microsoft.Storage/storageAccounts/listkeys/action** を含む Azure ロールが割り当てられている必要があります。 この Azure ロールは、組み込みロールまたはカスタム ロールのどちらでも構いません。 **Microsoft.Storage/storageAccounts/listkeys/action** をサポートする組み込みロールには、次が含まれます。

- Azure Resource Manager の[所有者ロール](../../role-based-access-control/built-in-roles.md#owner)
- Azure Resource Manager の[共同作成者ロール](../../role-based-access-control/built-in-roles.md#contributor)
- [Storage Account の共同作成者ロール](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Azure portal でキュー データにアクセスしようとすると、ポータルではまず **Microsoft.Storage/storageAccounts/listkeys/action** を含むロールがお客様に割り当てられているかどうかが確認されます。 このアクションを持つロールが割り当てられている場合、ポータルではキュー データにアクセスするためにアカウント キーが使用されます。 このアクションを持つロールが割り当てられていない場合、ポータルは、Azure AD アカウントを使用してデータへのアクセスを試みます。

> [!IMPORTANT]
> ストレージ アカウントが Azure Resource Manager の **ReadOnly** ロックでロックされている場合、そのストレージ アカウントに対して [キーの一覧表示](/rest/api/storagerp/storageaccounts/listkeys)操作は許可されません。 **キーの一覧表示** は POST 操作であり、アカウントに対して **ReadOnly** ロックが構成されている場合、すべての POST 操作が禁止されます。 このため、アカウントが **ReadOnly** ロックでロックされている場合、ユーザーは Azure AD 資格情報を使用してポータル内のキュー データにアクセスする必要があります。 Azure AD を使用したポータル内のキュー データへのアクセスの詳細については、「[自分の Azure AD アカウントを使用する](#use-your-azure-ad-account)」を参照してください。

> [!NOTE]
> 従来のサブスクリプション管理者ロールである **サービス管理者** と **共同管理者** には、Azure Resource Manager の [`Owner`](../../role-based-access-control/built-in-roles.md#owner) ロールと同等のものが含まれています。 **所有者** ロールには、**Microsoft.Storage/storageAccounts/listkeys/action** を含むすべてのアクションが含まれているので、これらの管理者ロールのいずれかを持つユーザーは、アカウント キーを持つキュー データにもアクセスできます。 詳細については、「[従来のサブスクリプション管理者ロール、Azure ロール、および Azure AD 管理者ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)」を参照してください。

### <a name="use-your-azure-ad-account"></a>自分の Azure AD アカウントを使用する

Azure AD アカウントを使用して、Azure portal からキュー データにアクセスするには、次のステートメントが両方とも自分に当てはまる必要があります。

- Azure Resource Manager の [`Reader`](../../role-based-access-control/built-in-roles.md#reader) ロールが、少なくとも、ストレージ アカウント以上のレベルを範囲として割り当てられている。 **リーダー** 役割は最も制限の厳しいアクセス許可を付与しますが、ストレージ アカウントの管理リソースへのアクセス権を付与する別の Azure Resource Manager ロールも受け入れることができます。
- キュー データへのアクセスを提供する組み込みロールまたはカスタム ロールのいずれかが割り当てられている。

**リーダー** ロールの割り当てまたは別の Azure Resource Manager ロールの割り当てが必要であるため、ユーザーは Azure portal でストレージ アカウントの管理リソースを表示したり移動したりできます。 キュー データへのアクセスを許可する Azure ロールでは、ストレージ アカウントの管理リソースへのアクセスは許可されません。 ポータルでキュー データにアクセスするためには、ユーザーにはストレージ アカウント リソースを移動する許可が必要です。 この要件に関する詳細については、「[ポータルへのアクセス用の閲覧者ロールの割り当て](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access)」を参照してください。

キュー データへのアクセスをサポートする組み込みロールには、次が含まれます。

- [ストレージ キュー データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor):キューに対する読み取り/書き込み/削除アクセス許可。
- [ストレージ キュー データ閲覧者](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader):キューに対する読み取り専用アクセス許可。

カスタム ロールは、組み込みロールによって提供される同じアクセス許可のさまざまな組み合わせをサポートできます。 Azure カスタム ロールを作成する方法の詳細については、「[Azure のカスタム ロール](../../role-based-access-control/custom-roles.md)」と「[Azure リソースのロール定義の概要](../../role-based-access-control/role-definitions.md)」を参照してください。

従来のサブスクリプション管理者ロールでキューを一覧表示することはサポートされていません。 キューを一覧表示するには、ユーザーには Azure Resource Manager **リーダー** ロール、**ストレージ キュー データ閲覧者** ロール、または **ストレージ キュー データ共同作成者** ロールが割り当てられている必要があります。

> [!IMPORTANT]
> Azure portal の Storage Explorer のプレビュー バージョンでは、キュー データを表示および変更するための Azure AD 資格情報の使用はサポートされていません。 Azure portal の Storage Explorer では、データは常にアカウント キーを使用してアクセスされます。 Azure portal で Storage Explorer を使用するには、**Microsoft. Storage/storageAccounts/listkeys/action** を含むロールが割り当てられている必要があります。

## <a name="navigate-to-queues-in-the-azure-portal"></a>Azure portal でキューに移動する

ポータルでキュー データを表示するには、ストレージ アカウントの **[概要]** に移動し、**キュー** のリンクをクリックします。 または、メニューの **[Queue サービス]** セクションに移動することもできます。

:::image type="content" source="media/authorize-data-operations-portal/queue-access-portal.png" alt-text="Azure portal でキュー データに移動する方法を示すスクリーンショット":::

## <a name="determine-the-current-authentication-method"></a>現在の認証方法の判別

キューに移動すると、認証のためにアカウント アクセス キーまたは Azure AD アカウントのどちらを現在使用しているかが Azure portal に示されます。

### <a name="authenticate-with-the-account-access-key"></a>アカウント アクセス キーを使用して認証を行う

アカウント アクセス キーを使用して認証を行う場合、ポータルには認証方法として **アクセス キー** が指定されていることが次のように示されます。

:::image type="content" source="media/authorize-data-operations-portal/auth-method-access-key.png" alt-text="アカウント キーを使用して現在キューにアクセスしているユーザーを示すスクリーンショット":::

Azure AD アカウントの使用に切り替えるには、図内で強調表示されているリンクをクリックします。 割り当てられている Azure ロールを通じて適切なアクセス許可が付与されている場合は、続行できます。 ただし、適切なアクセス許可を持っていない場合は、次のようなエラー メッセージが表示されます。

:::image type="content" source="media/authorize-data-operations-portal/auth-error-azure-ad.png" alt-text="Azure AD アカウントがアクセスをサポートしていないかどうかを示すエラー":::

キューは、Azure AD アカウントにそれらを表示するアクセス許可がない場合には表示されないことに注意してください。 認証に再度アクセス キーを使用するには、 **[Switch to access key]\(アクセス キーへの切り替え\)** リンクをクリックします。

### <a name="authenticate-with-your-azure-ad-account"></a>Azure AD アカウントを使用して認証を行う

Azure AD アカウントを使用して認証を行う場合は、ポータルに認証方法として **Azure AD ユーザー アカウント** が指定されていることが次のように示されます。

:::image type="content" source="media/authorize-data-operations-portal/auth-method-azure-ad.png" alt-text="Azure AD アカウントを使用して現在キューにアクセスしているユーザーを示すスクリーンショット":::

アカウント アクセス キーの使用に切り替えるには、図内で強調表示されているリンクをクリックします。 アカウント キーへのアクセスがある場合は、続行できます。 ただし、アカウント キーにアクセスできない場合は、Azure portal にエラー メッセージが表示されます。

アカウント キーへのアクセス権がない場合、キューはポータルに一覧表示されません。 認証に再度 Azure AD アカウントを使用するには、 **[Switch to Azure AD User Account]\(Azure AD ユーザー アカウントへの切り替え\)** リンクをクリックします。

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory を使用して Azure BLOB およびキューへのアクセスを認証する](../common/storage-auth-aad.md)
- [Azure portal を使用して BLOB とキュー データへのアクセスのための Azure ロールを割り当てる](../common/storage-auth-aad-rbac-portal.md)
- [Azure CLI を使用して BLOB およびキュー データにアクセスするための Azure ロールを割り当てる](../common/storage-auth-aad-rbac-cli.md)
- [Azure PowerShell モジュールを使用して BLOB およびキュー データにアクセスするための Azure ロールを割り当てる](../common/storage-auth-aad-rbac-powershell.md)
