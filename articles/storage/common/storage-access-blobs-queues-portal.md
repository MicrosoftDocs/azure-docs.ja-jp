---
title: Azure portal を使用した BLOB またはキュー データへのアクセス
titleSuffix: Azure Storage
description: Azure portal を使用して BLOB またはキュー データにアクセスするときに、ポータルは Azure Storage に要求を公開せずに行います。 Azure Storage へのこれらの要求は、Azure AD アカウントまたはストレージ アカウント アクセス キーのいずれかを使用して認証および許可できます。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 89816e3640c0afad6290e77faa3904c691df4318
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892399"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Azure portal を使用した BLOB またはキュー データへのアクセス

[Azure portal](https://portal.azure.com) を使用して BLOB またはキュー データにアクセスするときに、ポータルは Azure Storage に要求を公開せずに行います。 Azure Storage への要求は、Azure AD アカウントまたはストレージ アカウント アクセス キーのいずれかを使用して承認できます。 ポータルでは、どの方法を使用しているかを示し、適切なアクセス許可がある場合は、それら 2 つを切り替えることができます。  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>BLOB またはキューのデータにアクセスするために必要なアクセス許可

Azure portal で BLOB またはキュー データへのアクセスを承認する方法に応じて、特定のアクセス許可が必要になります。 ほとんどの場合、これらのアクセス許可はロールベースのアクセス制御 (RBAC) を使用して提供されます。 RBAC の詳細については、「[ロールベースのアクセス制御 (RBAC) とは](../../role-based-access-control/overview.md)」を参照してください。

### <a name="account-access-key"></a>アカウント アクセス キー

BLOB およびキューのデータをアカウント アクセス キーでアクセスするには、RBAC アクション **Microsoft.Storage/storageAccounts/listkeys/action** を含む割り当てられた RBAC ロールが必要です。 この RBAC ロールは、組み込みロールまたはカスタム ロールのどちらでも構いません。 **Microsoft.Storage/storageAccounts/listkeys/action** をサポートする組み込みロールには、次が含まれます。

- Azure Resource Manager の[所有者](../../role-based-access-control/built-in-roles.md#owner)ロール
- Azure Resource Manager の[共同作成者](../../role-based-access-control/built-in-roles.md#contributor)ロール
- [Storage Account の共同作成者](../../role-based-access-control/built-in-roles.md#storage-account-contributor)ロール

Azure portal で BLOB またはキューのデータにアクセスしようとすると、ポータルはまずユーザーが **Microsoft.Storage/storageAccounts/listkeys/action** で割り当てられているロールがあるかどうかを確認します。 このアクションを持つロールが割り当てられている場合、ポータルは BLOB およびキューのデータにアクセスするためにアカウント キーを使用します。 このアクションを持つロールが割り当てられていない場合、ポータルは、Azure AD アカウントを使用してデータへのアクセスを試みます。

> [!NOTE]
> 従来のサブスクリプション管理者ロールであるサービス管理者と共同管理者には、Azure Resource Manager の[所有者](../../role-based-access-control/built-in-roles.md#owner)ロールと同等のものが含まれています。 **所有者**ロールには、**Microsoft.Storage/storageAccounts/listkeys/action** を含むすべてのアクションが含まれているので、これらの管理者ロールのいずれかを持つユーザーは、アカウント キーを持つ BLOB およびキューのデータにもアクセスできます。 詳細については、「[従来のサブスクリプション管理者ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)」を参照してください。

### <a name="azure-ad-account"></a>Azure AD アカウント

Azure AD アカウントを使用して、Azure portal から BLOB またはキューのデータにアクセスするには、次のステートメントの両方が真である必要があります。

- Azure Resource Manager の[リーダー](../../role-based-access-control/built-in-roles.md#reader)ロールが、少なくとも、ストレージ アカウント以上のレベルを範囲として割り当てられている。 **リーダー**役割は最も制限の厳しいアクセス許可を付与しますが、ストレージ アカウントの管理リソースへのアクセス権を付与する別の Azure Resource Manager ロールも受け入れることができます。
- BLOB またはキューのデータへのアクセスを提供する組み込みロールまたはカスタム ロールのいずれかが割り当てられている。

**リーダー** ロールの割り当てまたは別の Azure Resource Manager ロールの割り当てが必要であるため、ユーザーは Azure portal でストレージ アカウントの管理リソースを表示したり移動したりできます。 BLOB またはキューのデータへのアクセスを付与する RBAC ロールは、ストレージ アカウントの管理リソースへのアクセスを付与しません。 ポータルで BLOB またはキューのデータにアクセスするためには、ユーザーにはストレージ アカウント リソースを移動する許可が必要です。 この要件に関する詳細については、「[ポータルへのアクセス用の閲覧者ロールの割り当て](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access)」を参照してください。

BLOB またはキュー データへのアクセスをサポートする組み込みロールには、次が含まれます。

- [ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner):Azure Data Lake Storage Gen2 の POSIX アクセス制御の管理。
- [ストレージ BLOB データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor):blob に対する読み取り/書き込み/削除アクセス許可。
- [ストレージ BLOB データ閲覧者](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader):blob の読み取り専用アクセス許可。
- [ストレージ キュー データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor):キューに対する読み取り/書き込み/削除アクセス許可。
- [ストレージ キュー データ閲覧者](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader):キューに対する読み取り専用アクセス許可。
    
カスタム ロールは、組み込みロールによって提供される同じアクセス許可のさまざまな組み合わせをサポートできます。 カスタム RBAC ロールを作成する方法の詳細については、「[Azure リソースのカスタム ロール](../../role-based-access-control/custom-roles.md)」と「[Understand role definitions for Azure resources](../../role-based-access-control/role-definitions.md)」(Azure リソースのロール定義を理解する) を参照してください。

> [!NOTE]
> 従来のサブスクリプション管理者ロールでキューを一覧表示することはサポートされていません。 キューを一覧表示するには、ユーザーには Azure Resource Manager **リーダー** ロール、**ストレージ キュー データ閲覧者**ロール、または**ストレージ キュー データ共同作成者**ロールが割り当てられている必要があります。

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>ポータルでの blob またはキューへの移動

ポータルで BLOB またはキューのデータを表示するには、ストレージ アカウントの **[概要]** に移動し、**Blob** または**キュー**のリンクをクリックします。 または、メニューの **[Blob service]** と **[Queue サービス]** セクションに移動します。 

![Azure portal での BLOB またはキューのデータへの移動](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>現在の認証方法の判別

コンテナーまたはキューに移動すると、Azure portal は、認証のためにアカウント アクセス キーまたは Azure AD アカウントのどちらを現在使用しているかを示します。

このセクションの例では、コンテナーとその BLOB へのアクセスを示していますが、キューおよびそのメッセージにアクセスしたりキューを一覧表示したりするときに、ポータルには同じメッセージが表示されます。

### <a name="account-access-key"></a>アカウント アクセス キー

アカウント アクセス キーを使用して認証を行う場合、ポータルには認証方法として**アクセス キー**が指定されていることが次のように示されます。

![Currently accessing container data with the account key (現在コンテナー データにアカウント キーを使用してアクセスしています)](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Azure AD アカウントの使用に切り替えるには、図内で強調表示されているリンクをクリックします。 割り当てられている RBAC ロールにより適切なアクセス許可を持っていれば、続行することができます。 ただし、適切なアクセス許可を持っていない場合は、次のようなエラー メッセージが表示されます。

![Azure AD アカウントがアクセスをサポートしていないかどうかを示すエラー](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

BLOB は Azure AD アカウントにそれらを表示するアクセス許可がない場合には表示されないことに注意してください。 認証に再度アクセス キーを使用するには、 **[Switch to access key]\(アクセス キーへの切り替え\)** リンクをクリックします。

### <a name="azure-ad-account"></a>Azure AD アカウント

Azure AD アカウントを使用して認証を行う場合は、ポータルに認証方法として **Azure AD ユーザー アカウント**が指定されていることが次のように示されます。

![Currently accessing container data with Azure AD account (現在コンテナーのデータに Azure AD アカウントでアクセスしています)](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

アカウント アクセス キーの使用に切り替えるには、図内で強調表示されているリンクをクリックします。 アカウント キーへのアクセスがある場合は、続行できます。 ただし、アカウント キーへのアクセスがない場合は、次のようなエラー メッセージが表示されます。

![アカウント キーへのアクセスがない場合に表示されるエラー](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

アカウント キーへのアクセスがない場合は、BLOB は一覧表示されないことに注意してください。 認証に再度 Azure AD アカウントを使用するには、 **[Switch to Azure AD User Account]\(Azure AD ユーザー アカウントへの切り替え\)** リンクをクリックします。

## <a name="next-steps"></a>次の手順

- [Azure Active Directory を使用して Azure BLOB およびキューへのアクセスを認証する](storage-auth-aad.md)
- [Azure portal での RBAC を使用して Azure コンテナーとキューへのアクセスを付与する](storage-auth-aad-rbac-portal.md)
- [RBAC と Azure CLI を使用して Azure BLOB とキューのデータへのアクセスを付与する](storage-auth-aad-rbac-cli.md)
- [RBAC と PowerShell を使用して Azure BLOB とキューのデータへのアクセスを付与する](storage-auth-aad-rbac-powershell.md)
