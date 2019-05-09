---
title: Azure Active Directory を使用して Azure BLOB およびキューへのアクセスを認証する | Microsoft Docs
description: Azure Active Directory を使用して Azure BLOB およびキューへのアクセスを認証します。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/28/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e72400a759b976b1a2a6864b2fa7d7d91e16c62f
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619290"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Azure Active Directory を使用して Azure BLOB およびキューへのアクセスを認証する

Azure Storage は、Azure Active Directory (AD) を使用して BLOB サービスと Queue サービスの認証と承認をサポートします。 Azure AD では、ロールベースのアクセス制御 (RBAC) を使用して、ユーザー、グループ、またはアプリケーションのサービス プリンシパルにアクセスを許可します。 

Azure AD の資格情報を使用したユーザーまたはアプリケーションの認証は、セキュリティと使いやすさで他の承認手段よりも優れています。 アプリケーションで共有キー承認を引き続き使うことはできますが、Azure AD を使うと、コードでアカウント アクセス キーを保存する必要がなくなります。 Shared Access Signature (SAS) を使ってストレージ アカウント内のリソースに対するきめ細かいアクセスの許可を続けることもできますが、Azure AD は、SAS トークンを管理したり侵害された SAS の取り消しを心配したりする必要なしに、同様の機能を提供します。 Azure Storage アプリケーションでは、できる限り Azure AD 認証を使用することをお勧めします。

Azure AD 資格情報での認証と認可は、すべてのパブリック リージョンのすべての汎用 v2、汎用 v1、および BLOB ストレージ アカウントに対して使用できます。 Azure AD の認可は、Azure Resource Manager デプロイ モデルで作成されたストレージ アカウントにおいてのみサポートされます。

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>BLOB とキューに対する Azure AD の概要

Azure Storage で Azure AD の統合を使用する最初のステップは、ストレージ データに対する RBAC のロールを、サービス プリンシパル (ユーザー、グループ、またはアプリケーションのサービス プリンシパル) または Azure リソースに対するマネージド ID に割り当てることです。 RBAC のロールには、コンテナーとキューに対する共通のアクセス許可セットが含まれます。 Azure Storage 用の RBAC ロールの割り当てについては、[RBAC で Azure Storage データへのアクセス許可を管理する](storage-auth-aad-rbac.md)方法に関するページを参照してください。

Azure AD を使ってアプリケーションのストレージ リソースへのアクセスを承認するには、コードから OAuth 2.0 アクセス トークンを要求する必要があります。 アクセス トークンを要求し、それを使用して Azure Storage への要求を認可する方法については、[Azure Storage アプリケーションからの Azure AD による認証](storage-auth-aad-app.md)に関する記事をご覧ください。 マネージド ID を使用している場合は、「[Azure リソース (プレビュー) の Azure 管理 ID を使用した blob およびキューへのアクセス認証](storage-auth-aad-msi.md)」をご覧ください。

現在、Azure CLI と PowerShell は、Azure AD ID でのログインをサポートしています。 Azure AD ID を使用してサインインすると、セッションはその ID で実行されるようになります。 詳しくは、[CLI または PowerShell での Azure AD ID を使用した Azure Storage へのアクセス](storage-auth-aad-script.md)に関する記事をご覧ください。

## <a name="rbac-roles-for-blobs-and-queues"></a>BLOB とキューの RBAC ロール

Azure Active Directory (Azure AD) では、[ロールベースのアクセス制御 (RBAC)](../../role-based-access-control/overview.md) を通じて、セキュリティで保護されたリソースへのアクセス権が承認されます。 BLOB およびキューのデータへのアクセスに使用される一般的なアクセス許可セットを含む一連の組み込み RBAC ロールは、Azure Storage によって定義されます。 BLOB およびキューのデータにアクセスするためのカスタム ロールを定義することもできます。

RBAC ロールが Azure AD セキュリティ プリンシパルに割り当てられると、Azure によりそのセキュリティ プリンシパルのリソースへのアクセス権が付与されます。 アクセスのスコープは、サブスクリプション、リソース グループ、ストレージ アカウント、あるいは個別のコンテナーまたはキューのレベルで指定できます。 Azure AD セキュリティ プリンシパルは、Azure リソースのユーザー、グループ、アプリケーション サービス プリンシパル、または[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) の場合があります。

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Azure Storage リソースに組み込みの RBAC を割り当てる方法については、次のトピックのいずれかをご覧ください。

- [Azure portal で RBAC を使用して Azure BLOB とキューのデータへのアクセスを付与する](storage-auth-aad-rbac-portal.md)
- [RBAC と Azure CLI を使用して Azure BLOB とキューのデータへのアクセスを付与する](storage-auth-aad-rbac-cli.md)
- [RBAC と PowerShell を使用して Azure BLOB とキューのデータへのアクセスを付与する](storage-auth-aad-rbac-powershell.md)

### <a name="access-permissions-granted-by-rbac-roles"></a>RBAC ロールによって付与されるアクセス許可 

Azure Storage 操作を呼び出すために必要なアクセス許可の詳細については、「[Permissions for calling REST operations (REST 操作を呼び出すためのアクセス許可)](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations)」を参照してください。

## <a name="resource-scope"></a>リソースのスコープ

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="next-steps"></a>次の手順

- [Azure Active Directory ベースのアクセス制御の Azure Storage によるサポートの一般提供](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
- [BLOB やキューにアクセスするためにアプリケーションから Azure Active Directory で認証を行う](storage-auth-aad-app.md)
- [Azure リソースに対するマネージド ID を使用して BLOB およびキューへのアクセスを認証する](storage-auth-aad-msi.md)
- Azure Files はドメイン参加 VM に関してのみ SMB 経由での Azure AD 認証をサポートしています (プレビュー)。 Azure Files での SMB を使用した Azure AD 認証の詳細については、「[SMB を使用した Azure Files への Azure Active Directory 認証 (プレビュー) の概要](../files/storage-files-active-directory-overview.md)」を参照してください。