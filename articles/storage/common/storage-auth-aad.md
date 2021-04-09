---
title: Active Directory を使用して BLOB とキューへのアクセスを承認する
titleSuffix: Azure Storage
description: Azure Active Directory (Azure AD) を使用して Azure の BLOB およびキューへのアクセスを承認する。 アクセス権に対して Azure ロールを割り当てる。 Azure AD アカウントでのアクセス データ。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 9d03496634c5d30d30b23a76b5b47b1e810af288
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94635400"
---
# <a name="authorize-access-to-blobs-and-queues-using-azure-active-directory"></a>Azure Active Directory を使用して BLOB とキューへのアクセスを承認する

Azure Storage では、Azure Active Directory (Azure AD) を使用して Blob Storage および Queue storage への要求を承認することがサポートされています。 Azure AD では、Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、セキュリティ プリンシパル (ユーザー、グループ、またはアプリケーションのサービス プリンシパルである可能性があります) にアクセス許可を付与します。 セキュリティ プリンシパルは、Azure AD によって認証されて、OAuth 2.0 トークンを返します。 その後、そのトークンを、Blob Storage または Queue storage に対する要求を承認するために使用できます。

Azure AD を使用して Azure Storage に対する要求を承認すると、共有キー承認よりも優れたセキュリティと使いやすさが実現されます。 Microsoft では、共有キーに固有の潜在的なセキュリティの脆弱性を最小限に抑えるために、可能な場合は、BLOB アプリケーションとキュー アプリケーションで Azure AD での承認を使用することをお勧めします。

Azure AD での承認は、すべてのパブリック リージョンおよび国内クラウド内のすべての汎用および BLOB ストレージ アカウントに対して使用できます。 Azure AD の認可は、Azure Resource Manager デプロイ モデルで作成されたストレージ アカウントにおいてのみサポートされます。

Blob Storage ではさらに、Azure AD 資格情報で署名された Shared Access Signature (SAS) を作成することもサポートされています。 詳細については、[Shared Access Signatures を使用したデータへの制限付きアクセスの許可](storage-sas-overview.md)に関するページを参照してください。

Azure Files はドメイン参加 VM に関してのみ SMB 経由の AD (プレビュー) または Azure AD DS (GA) での承認をサポートしています。 Azure Files での SMB 経由の AD (プレビュー) または Azure AD DS (GA) を使用する方法の詳細については、「[SMB アクセスに対する Azure Files ID ベース認証サポートの概要](../files/storage-files-active-directory-overview.md)」を参照してください。

Azure AD での承認は Azure Table Storage ではサポートされていません。 Table Storage への要求を承認するには、共有キーを使用します。

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>BLOB とキューに対する Azure AD の概要

セキュリティ プリンシパル (ユーザー、グループ、またはアプリケーション) が BLOB またはキューのリソースにアクセスしようとすると、BLOB が匿名アクセスに利用できる場合を除き、要求は承認される必要があります。 Azure AD では、リソースへのアクセスは 2 段階のプロセスです。 まず、セキュリティ プリンシパルの ID が認証され、OAuth 2.0 トークンが返されます。 次に、そのトークンが BLOB サービスまたは Queue サービスへの要求の一部として渡され、指定されたリソースへのアクセスを承認するためにサービスによって使用されます。

認証の手順では、アプリケーションが実行時に OAuth 2.0 アクセス トークンを要求する必要があります。 アプリケーションが Azure VM、仮想マシン スケール セット、または Azure Functions アプリなどの Azure エンティティ内から実行されている場合、[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を使用して BLOB またはキューにアクセスできます。 Azure BLOB サービスまたは Queue サービスに対するマネージド ID によって作成された要求を承認する方法については、「[Azure リソースに対するマネージド ID を使用して BLOB およびキューへのアクセスを認証する](storage-auth-aad-msi.md)」を参照してください。

承認の手順では、セキュリティ プリンシパルに 1 つまたは複数の Azure ロールを割り当てる必要があります。 BLOB およびキューのデータへの一般的なアクセス許可セットを含む Azure ロールは、Azure Storage によって提供されます。 セキュリティ プリンシパルに割り当てられたロールによって、そのプリンシパルが持つアクセス許可が決定されます。 Azure Storage 用の Azure ロールの割り当てについては、[Azure RBAC で Azure Storage データへのアクセス許可を管理する](./storage-auth-aad-rbac-portal.md)方法に関するページを参照してください。

Azure BLOB サービスまたは Queue サービスに対する要求を作成するネイティブ アプリケーションと Web アプリケーションは、Azure AD でアクセスを承認することもできます。 アクセス トークンを要求し、それを使用して BLOB またはキューのデータへの要求を認可する方法については、[Azure Storage アプリケーションからの Azure AD による Azure Storage へのアクセスの承認](storage-auth-aad-app.md)に関する記事をご覧ください。

## <a name="assign-azure-roles-for-access-rights"></a>アクセス権に対して Azure ロールを割り当てる

Azure Active Directory (Azure AD) では、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) を通じて、セキュリティで保護されたリソースへのアクセス権が承認されます。 BLOB およびキュー データへのアクセスに使用される一般的なアクセス許可セットを含む一連の Azure 組み込みロールは、Azure Storage によって定義されます。 BLOB およびキューのデータにアクセスするためのカスタム ロールを定義することもできます。

Azure ロールが Azure AD セキュリティ プリンシパルに割り当てられると、Azure によりそのセキュリティ プリンシパルのリソースへのアクセス権が付与されます。 アクセスのスコープは、サブスクリプション、リソース グループ、ストレージ アカウント、あるいは個別のコンテナーまたはキューのレベルで指定できます。 Azure AD セキュリティ プリンシパルは、Azure リソースのユーザー、グループ、アプリケーション サービス プリンシパル、または[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) の場合があります。

### <a name="azure-built-in-roles-for-blobs-and-queues"></a>BLOB とキューに対する Azure の組み込みロール

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Azure 組み込みロールをセキュリティ プリンシパルに割り当てる方法を学習するには、次のいずれかの記事を参照してください。

- [Azure portal を使用して BLOB とキュー データへのアクセスのための Azure ロールを割り当てる](storage-auth-aad-rbac-portal.md)
- [Azure CLI を使用して BLOB およびキュー データにアクセスするための Azure ロールを割り当てる](storage-auth-aad-rbac-cli.md)
- [Azure PowerShell モジュールを使用して BLOB およびキュー データにアクセスするための Azure ロールを割り当てる](storage-auth-aad-rbac-powershell.md)

Azure Storage の組み込みロールの定義方法については、「[ロール定義について](../../role-based-access-control/role-definitions.md#management-and-data-operations)」を参照してください。 Azure カスタム ロールの作成については、「[Azure カスタム ロール](../../role-based-access-control/custom-roles.md)」を参照してください。

### <a name="access-permissions-for-data-operations"></a>データ操作用のアクセス許可

特定の BLOB サービスまたは Queue サービスの操作を呼び出すために必要なアクセス許可の詳細については、「[Permissions for calling blob and queue data operations](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」 (BLOB とキューのデータ操作を呼び出すためのアクセス許可) を参照してください。

## <a name="resource-scope"></a>リソースのスコープ

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Azure AD アカウントでのアクセス データ

Azure portal、PowerShell、または Azure CLI 経由での BLOB またはキューのデータへのアクセスは、ユーザーの Azure AD アカウントを使用するか、アカウント アクセス キー (共有キーによる承認) を使用することによって承認できます。

### <a name="data-access-from-the-azure-portal"></a>Azure portal からのデータ アクセス

Azure portal では、Azure AD アカウントまたはアカウント アクセス キーのいずれかを使用して、Azure ストレージ アカウントの BLOB とキューのデータにアクセスできます。 Azure portal で使用する認証スキームは、お客様に割り当てられている Azure ロールに応じて異なります。

BLOB またはキューのデータにアクセスしようとすると、Azure portal でポータルはまずお客様に **Microsoft.Storage/storageAccounts/listkeys/action** で Azure ロールが割り当てられているかどうかを確認します。 このアクションを持つロールが割り当てられている場合、Azure portal は共有キー承認によって BLOB およびキューのデータにアクセスするためにアカウント キーを使用します。 このアクションを持つロールが割り当てられていない場合、Azure portal は Azure AD アカウントを使用してデータへのアクセスを試みます。

Azure AD アカウントを使用して Azure Portal から BLOB またはキュー データにアクセスするには、その BLOB やキュー データにアクセスするためのアクセス許可が必要です。また、Azure Portal でストレージ アカウント リソース内を移動するためのアクセス許可も必要です。 Azure Storage によって提供される組み込みロールでは BLOB やキュー リソースへのアクセス権が付与されますが、ストレージ アカウント リソースへのアクセス許可は付与されません。 このため、ポータルへのアクセスには、スコープがストレージ アカウント以上のレベルに設定された、[閲覧者](../../role-based-access-control/built-in-roles.md#reader)ロールなどの Azure Resource Manager ロールの割り当ても必要です。 **リーダー** 役割は最も制限の厳しいアクセス許可を付与しますが、ストレージ アカウントの管理リソースへのアクセス権を付与する別の Azure Resource Manager ロールも受け入れることができます。 Azure AD アカウントを使用した Azure portal 内のデータ アクセスのためにユーザーにアクセス許可を割り当てる方法の詳細については、「[Azure portal を使用して BLOB とキュー データへのアクセスのための Azure ロールを割り当てる](storage-auth-aad-rbac-portal.md)」を参照してください。

Azure Portal では、コンテナーまたはキューに移動すると、どの承認スキームが使用されているかが示されます。 ポータルでのデータ アクセスの詳細については、「[Azure portal で BLOB データへのアクセスの承認方法を選択する](../blobs/authorize-data-operations-portal.md)」と「[Azure portal でキュー データへのアクセスの承認方法を選択する](../queues/authorize-data-operations-portal.md)」を参照してください。

### <a name="data-access-from-powershell-or-azure-cli"></a>PowerShell または Azure CLI からのデータ アクセス

Azure CLI と PowerShell では、Azure AD の資格情報を使ったサインインをサポートします。 サインインした後、セッションはその資格情報で実行されます。 詳細については、「[Azure AD ID を使用し、CLI または PowerShell で BLOB とキューのデータにアクセスする](../blobs/authorize-data-operations-powershell.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory と Azure リソースのマネージド ID を使用して BLOB およびキューへのアクセスを承認する](storage-auth-aad-msi.md)
- [BLOB やキューにアクセスするためにアプリケーションから Azure Active Directory を使用して承認を行う](storage-auth-aad-app.md)
- [Azure Active Directory ベースのアクセス制御の Azure Storage によるサポートの一般提供](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)