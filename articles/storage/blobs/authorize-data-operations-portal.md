---
title: Azure portal で BLOB データへのアクセスの承認方法を選択する
titleSuffix: Azure Storage
description: Azure portal を使用して BLOB データにアクセスするときに、ポータルでは内部的に Azure Storage への要求が発行されます。 Azure Storage へのこれらの要求は、Azure AD アカウントまたはストレージ アカウント アクセス キーのいずれかを使用して認証および許可できます。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.custom: contperf-fy21q1
ms.openlocfilehash: a12936f8f9f84dacfab4850253df665ae7758be1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102613250"
---
# <a name="choose-how-to-authorize-access-to-blob-data-in-the-azure-portal"></a>Azure portal で BLOB データへのアクセスの承認方法を選択する

[Azure portal](https://portal.azure.com) を使用して BLOB データにアクセスするときに、ポータルでは内部的に Azure Storage への要求が発行されます。 Azure Storage への要求は、Azure AD アカウントまたはストレージ アカウント アクセス キーのいずれかを使用して承認できます。 ポータルでは、どの方法を使用しているかを示し、適切なアクセス許可がある場合は、それら 2 つを切り替えることができます。  

また、Azure portal で個々の BLOB アップロード操作を承認する方法を指定することもできます。 既定では、ユーザーが BLOB アップロード操作を承認するために既に使用している任意の方法がポータルによって使用されますが、BLOB をアップロードするときにこの設定を変更できます。

## <a name="permissions-needed-to-access-blob-data"></a>BLOB データにアクセスするために必要なアクセス許可

Azure portal で BLOB データへのアクセスを承認する方法に応じて、特定のアクセス許可が必要になります。 ほとんどの場合、これらのアクセス許可は Azure ロールベースのアクセス制御 (Azure RBAC) を使用して提供されます。 Azure RBAC の詳細については、「[Azure ロールベースのアクセス制御 (Azure RBAC) とは](../../role-based-access-control/overview.md)」を参照してください。

### <a name="use-the-account-access-key"></a>アカウント アクセス キーを使用する

アカウント アクセス キーを使用して BLOB データにアクセスするには、Azure RBAC アクション **Microsoft.Storage/storageAccounts/listkeys/action** を含む Azure ロールが割り当てられている必要があります。 この Azure ロールは、組み込みロールまたはカスタム ロールのどちらでも構いません。 **Microsoft.Storage/storageAccounts/listkeys/action** をサポートする組み込みロールには、次が含まれます。

- Azure Resource Manager の[所有者](../../role-based-access-control/built-in-roles.md#owner)ロール
- Azure Resource Manager の[共同作成者](../../role-based-access-control/built-in-roles.md#contributor)ロール
- [Storage Account の共同作成者](../../role-based-access-control/built-in-roles.md#storage-account-contributor)ロール

Azure portal で BLOB データにアクセスしようとすると、ポータルではまず **Microsoft.Storage/storageAccounts/listkeys/action** を含むロールがお客様に割り当てられているかどうかが確認されます。 このアクションを持つロールが割り当てられている場合、ポータルでは BLOB データにアクセスするためにアカウント キーが使用されます。 このアクションを持つロールが割り当てられていない場合、ポータルは、Azure AD アカウントを使用してデータへのアクセスを試みます。

> [!IMPORTANT]
> ストレージ アカウントが Azure Resource Manager の **ReadOnly** ロックでロックされている場合、そのストレージ アカウントに対して [キーの一覧表示](/rest/api/storagerp/storageaccounts/listkeys)操作は許可されません。 **キーの一覧表示** は POST 操作であり、アカウントに対して **ReadOnly** ロックが設定されている場合、すべての POST 操作が禁止されます。 このため、アカウントが **ReadOnly** ロックでロックされている場合、ユーザーは Azure AD 資格情報を使用してポータル内の BLOB データにアクセスする必要があります。 Azure AD を使用したポータル内の BLOB データへのアクセスの詳細については、「[自分の Azure AD アカウントを使用する](#use-your-azure-ad-account)」を参照してください。

> [!NOTE]
> 従来のサブスクリプション管理者ロールであるサービス管理者と共同管理者には、Azure Resource Manager の[所有者](../../role-based-access-control/built-in-roles.md#owner)ロールと同等のものが含まれています。 **所有者** ロールには、**Microsoft.Storage/storageAccounts/listkeys/action** を含むすべてのアクションが含まれているので、これらの管理者ロールのいずれかを持つユーザーは、アカウント キーを持つ BLOB データにもアクセスできます。 詳細については、「[従来のサブスクリプション管理者ロール、Azure ロール、および Azure AD 管理者ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)」を参照してください。

### <a name="use-your-azure-ad-account"></a>自分の Azure AD アカウントを使用する

Azure AD アカウントを使用して、Azure portal から BLOB データにアクセスするには、次のステートメントが両方とも自分に当てはまる必要があります。

- Azure Resource Manager の[リーダー](../../role-based-access-control/built-in-roles.md#reader)ロールが、少なくとも、ストレージ アカウント以上のレベルを範囲として割り当てられている。 **リーダー** 役割は最も制限の厳しいアクセス許可を付与しますが、ストレージ アカウントの管理リソースへのアクセス権を付与する別の Azure Resource Manager ロールも受け入れることができます。
- BLOB データへのアクセスを提供する組み込みロールまたはカスタム ロールのいずれかが割り当てられている。

**リーダー** ロールの割り当てまたは別の Azure Resource Manager ロールの割り当てが必要であるため、ユーザーは Azure portal でストレージ アカウントの管理リソースを表示したり移動したりできます。 BLOB データへのアクセスを許可する Azure ロールでは、ストレージ アカウントの管理リソースへのアクセスは許可されません。 ポータルで BLOB データにアクセスするためには、ユーザーにはストレージ アカウント リソースを移動する許可が必要です。 この要件に関する詳細については、「[ポータルへのアクセス用の閲覧者ロールの割り当て](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access)」を参照してください。

BLOB データへのアクセスをサポートする組み込みロールには、次が含まれます。

- [ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner):Azure Data Lake Storage Gen2 の POSIX アクセス制御の管理。
- [ストレージ BLOB データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor):blob に対する読み取り/書き込み/削除アクセス許可。
- [ストレージ BLOB データ閲覧者](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader):blob の読み取り専用アクセス許可。

カスタム ロールは、組み込みロールによって提供される同じアクセス許可のさまざまな組み合わせをサポートできます。 Azure カスタム ロールを作成する方法の詳細については、「[Azure のカスタム ロール](../../role-based-access-control/custom-roles.md)」と「[Azure リソースのロール定義の概要](../../role-based-access-control/role-definitions.md)」を参照してください。

> [!IMPORTANT]
> Azure portal の Storage Explorer のプレビュー バージョンでは、BLOB データを表示および変更するための Azure AD 資格情報の使用はサポートされていません。 Azure portal の Storage Explorer では、データは常にアカウント キーを使用してアクセスされます。 Azure portal で Storage Explorer を使用するには、**Microsoft. Storage/storageAccounts/listkeys/action** を含むロールが割り当てられている必要があります。

## <a name="navigate-to-blobs-in-the-azure-portal"></a>Azure portal で BLOB に移動する

ポータルで BLOB データを表示するには、ストレージ アカウントの **[概要]** に移動し、 **[BLOB]** のリンクをクリックします。 または、メニューの **[コンテナー]** セクションに移動することもできます。

:::image type="content" source="media/authorize-data-operations-portal/blob-access-portal.png" alt-text="Azure portal で BLOB データに移動する方法を示すスクリーンショット":::

## <a name="determine-the-current-authentication-method"></a>現在の認証方法の判別

コンテナーに移動すると、認証のためにアカウント アクセス キーまたは Azure AD アカウントのどちらを現在使用しているかが Azure portal に示されます。

### <a name="authenticate-with-the-account-access-key"></a>アカウント アクセス キーを使用して認証を行う

アカウント アクセス キーを使用して認証を行う場合、ポータルには認証方法として **アクセス キー** が指定されていることが次のように示されます。

:::image type="content" source="media/authorize-data-operations-portal/auth-method-access-key.png" alt-text="アカウント キーを使用して現在コンテナーにアクセスしているユーザーを示すスクリーンショット":::

Azure AD アカウントの使用に切り替えるには、図内で強調表示されているリンクをクリックします。 割り当てられている Azure ロールを通じて適切なアクセス許可が付与されている場合は、続行できます。 ただし、適切なアクセス許可を持っていない場合は、次のようなエラー メッセージが表示されます。

:::image type="content" source="media/authorize-data-operations-portal/auth-error-azure-ad.png" alt-text="Azure AD アカウントがアクセスをサポートしていないかどうかを示すエラー":::

BLOB は Azure AD アカウントにそれらを表示するアクセス許可がない場合には表示されないことに注意してください。 認証に再度アクセス キーを使用するには、 **[Switch to access key]\(アクセス キーへの切り替え\)** リンクをクリックします。

### <a name="authenticate-with-your-azure-ad-account"></a>Azure AD アカウントを使用して認証を行う

Azure AD アカウントを使用して認証を行う場合は、ポータルに認証方法として **Azure AD ユーザー アカウント** が指定されていることが次のように示されます。

:::image type="content" source="media/authorize-data-operations-portal/auth-method-azure-ad.png" alt-text="Azure AD アカウントを使用して現在コンテナーにアクセスしているユーザーを示すスクリーンショット":::

アカウント アクセス キーの使用に切り替えるには、図内で強調表示されているリンクをクリックします。 アカウント キーへのアクセスがある場合は、続行できます。 ただし、アカウント キーへのアクセスがない場合は、次のようなエラー メッセージが表示されます。

:::image type="content" source="media/authorize-data-operations-portal/auth-error-access-key.png" alt-text="アカウント キーへのアクセスがない場合に表示されるエラー":::

アカウント キーへのアクセスがない場合は、BLOB は一覧表示されないことに注意してください。 認証に再度 Azure AD アカウントを使用するには、 **[Switch to Azure AD User Account]\(Azure AD ユーザー アカウントへの切り替え\)** リンクをクリックします。

## <a name="specify-how-to-authorize-a-blob-upload-operation"></a>BLOB アップロード操作を承認する方法を指定する

ユーザーは、Azure portal から BLOB をアップロードするときに、その操作の認証と承認を、アカウント アクセス キーまたは Azure AD の資格情報のどちらで行うかを指定できます。 既定では、「[現在の認証方法の判別](#determine-the-current-authentication-method)」で示されているように、現在の認証方法がポータルによって使用されます。

BLOB アップロード操作を承認する方法を指定するには、次の手順のようにします。

1. Azure portal で、BLOB をアップロードするコンテナーに移動します。
1. **[アップロード]** ボタンを選択します。
1. **[詳細]** セクションを展開して、BLOB の詳細プロパティを表示します。
1. 次の図に示すように、 **[認証の種類]** フィールドで、Azure AD アカウントとアカウント アクセス キーのどちらを使用してアップロード操作を承認するかを指定します。

    :::image type="content" source="media/authorize-data-operations-portal/auth-blob-upload.png" alt-text="BLOB アップロードでの承認方法を変更する方法を示すスクリーンショット":::

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory を使用して Azure BLOB およびキューへのアクセスを認証する](../common/storage-auth-aad.md)
- [Azure portal を使用して BLOB とキュー データへのアクセスのための Azure ロールを割り当てる](../common/storage-auth-aad-rbac-portal.md)
- [Azure CLI を使用して BLOB およびキュー データにアクセスするための Azure ロールを割り当てる](../common/storage-auth-aad-rbac-cli.md)
- [Azure PowerShell モジュールを使用して BLOB およびキュー データにアクセスするための Azure ロールを割り当てる](../common/storage-auth-aad-rbac-powershell.md)
