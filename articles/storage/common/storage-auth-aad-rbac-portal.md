---
title: Azure portal を使用して、RBAC で BLOB およびキュー データへの Azure AD アクセス権を管理する - Azure Storage | Microsoft Docs
description: Azure portal からロールベースのアクセス制御 (RBAC) を使用して、キューとコンテナーにセキュリティ プリンシパルへのアクセス権を割り当てます。 Azure Storage では、Azure AD を使用した認証用の組み込みとカスタムの RBAC ロールがサポートされています。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 8214ff821bad8a46eb710c8b9506d337715db103
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449846"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-in-the-azure-portal"></a>Azure portal で RBAC を使用して Azure BLOB とキューのデータへのアクセスを付与する

Azure Active Directory (Azure AD) では、[ロールベースのアクセス制御 (RBAC)](../../role-based-access-control/overview.md) を通じて、セキュリティで保護されたリソースへのアクセス権が承認されます。 BLOB またはキュー データへのアクセスに使用される一般的なアクセス許可セットを含む一連の組み込み RBAC ロールは、Azure Storage によって定義されます。 

RBAC ロールが Azure AD セキュリティ プリンシパルに割り当てられると、Azure によりそのセキュリティ プリンシパルのリソースへのアクセス権が付与されます。 アクセスのスコープは、サブスクリプション、リソース グループ、ストレージ アカウント、あるいは個別のコンテナーまたはキューのレベルで指定できます。 Azure AD ID セキュリティ プリンシパルは、Azure リソースのユーザー、グループ、アプリケーション サービス プリンシパル、または[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) の場合があります。

この記事では、Azure portal を使用して RBAC ロールを割り当てる方法を説明します。 Azure portal には、RBAC ロールの割り当てとストレージ リソースへのアクセスを管理するためのシンプルなインターフェイスがあります。 また、Azure コマンドライン ツールまたは Azure Storage 管理 API を使用して、BLOB とキューのリソースに RBAC ロールを割り当てることもできます。 ストレージ リソースの RBAC ロールに関する詳細については、「[Azure Active Directory を利用して Azure BLOB およびキューへのアクセスを認証する](storage-auth-aad.md)」を参照してください。 

## <a name="rbac-roles-for-blobs-and-queues"></a>BLOB とキューの RBAC ロール

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>リソースのスコープを決定する 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Azure portal を使用した RBAC ロールの割り当て

ロールの割り当ての適切なスコープを決定したら、Azure portal でそのリソースに移動します。 リソースの **[アクセス制御 (IAM)]** 設定を表示し、次の指示に従ってロールの割り当てを管理します。

1. Azure AD セキュリティ プリンシパルにアクセス権を付与するために、適切な Azure Storage の RBAC ロールを割り当てます。

1. Azure Resource Manager の[閲覧者](../../role-based-access-control/built-in-roles.md#reader)ロールを、Azure AD 資格情報を使用して Azure portal でコンテナーまたはキューにアクセスする必要があるユーザーに割り当てます。 

以降のセクションで、これらの手順のそれぞれについて詳しく説明します。

### <a name="assign-a-built-in-rbac-role"></a>組み込み RBAC ロールを割り当てる

セキュリティ プリンシパルにロールを割り当てる前に、付与するアクセス許可のスコープを必ず検討してください。 適切なスコープの決定については、「[リソースのスコープを決定する](#determine-resource-scope)」セクションを参照してください。

ここに示す手順では、コンテナーに対してスコープが指定されたロールを割り当てます。しかし、同じ手順に従って、キューに対してスコープが指定されたロールを割り当てることができます。 

1. [Azure portal](https://portal.azure.com) でストレージ アカウントに移動し、そのアカウントの **[概要]** を表示します。
1. [サービス] で **[BLOB]** を選択します。 
1. 割り当てるロールの対象にするコンテナーを見つけて、コンテナーの設定を表示します。 
1. **[アクセス制御 (IAM)]** を選択して、コンテナーのアクセス制御設定を表示します。 **[ロールの割り当て]** タブを選択して、ロールの割り当ての一覧を表示します。

    ![コンテナーのアクセス制御設定を示すスクリーンショット](media/storage-auth-aad-rbac-portal/portal-access-control-container.png)

1. **[ロールの割り当ての追加]** ボタンをクリックして新しいロールを追加します。
1. **[ロールの割り当ての追加]** ウィンドウで、割り当てる Azure Storage ロールを選択します。 次に、そのロールの割り当て先となるセキュリティ プリンシパルを検索して見つけます。

    ![RBAC ロールの割り当て方法を示すスクリーンショット](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. **[Save]** をクリックします。 ロールの割り当て先となった ID が、そのロールに一覧表示されます。 たとえば次の図は、今追加されたユーザーに、*sample-container* という名前のコンテナーのデータに対する読み取りアクセス許可があることを示しています。

    ![ロールに割り当てられたユーザーの一覧を示すスクリーンショット](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

同じ手順を実行して、ストレージ アカウント、リソース グループ、またはサブスクリプションに対してスコープが指定されたロールを割り当てることができます。

> [!NOTE]
> Azure Storage アカウントの所有者であっても、データへのアクセス許可が自動的に割り当てられるわけではありません。 Azure Storage の RBAC ロールを自分自身に明示的に割り当てる必要があります。 これは、サブスクリプション、リソース グループ、ストレージ アカウント、あるいはコンテナーまたはキューのレベルで割り当てることができます。
> 
> ストレージ アカウントで階層型名前空間が有効になっている場合、コンテナーまたはキューに対してスコープが指定されたロールを割り当てることはできません。

### <a name="assign-the-reader-role-for-portal-access"></a>ポータルへのアクセス用の閲覧者ロールの割り当て

セキュリティ プリンシパルに Azure Storage の組み込みまたはカスタム ロールを割り当てると、そのセキュリティ プリンシパルに、ストレージ アカウント内のデータに対する操作を実行するためのアクセス許可を付与することになります。 組み込みの**データ閲覧者**ロールがコンテナーまたはキュー内のデータの読み取りアクセス許可を提供する一方で、組み込みの**データ共同作成者**ロールは、コンテナーまたはキューに対する読み取り、書き込み、および削除のアクセス許可を提供します。 アクセス許可のスコープは指定されたリソースに指定されます。  

たとえば、**ストレージ BLOB データ共同作成者**ロールを **sample-container** という名前のコンテナーのレベルで Mary というユーザーに割り当てると、Mary にはそのコンテナー内のすべての BLOB に対する読み取り、書き込み、および削除のアクセス権が付与されます。

ただし、Mary が Azure portal で BLOB を表示する場合、**ストレージ BLOB データ共同作成者**ロール自体は、BLOB を表示するためにポータル内でその BLOB に移動するための十分なアクセス許可を提供しません。 ポータル内で移動し、そこに表示されているその他のリソースを確認するには、Azure AD の追加のアクセス許可が必要です。

ユーザーが Azure portal で BLOB にアクセスできる必要がある場合は、それらのユーザーに、ストレージ アカウント以上のレベルで追加の RBAC ロール、[閲覧者](../../role-based-access-control/built-in-roles.md#reader) ロールを割り当てます。 **閲覧者**ロールは Azure Resource Manager のロールであり、ユーザーにストレージ アカウントのリソースの表示を許可しますが、変更は許可しません。 これは Azure Storage 内のデータに読み取りアクセス許可を提供しませんが、アカウント管理リソースに対してのみです。

ユーザーが Azure portal から BLOB にアクセスできるように**閲覧者**ロールを割り当てるには、次の手順に従います。 この例では、割り当てのスコープはストレージ アカウントに指定されています。

1. [Azure portal](https://portal.azure.com) のストレージ アカウントに移動します。
1. **[アクセス制御 (IAM)]** を選択して、ストレージ アカウントのアクセス制御設定を表示します。 **[ロールの割り当て]** タブを選択して、ロールの割り当ての一覧を表示します。
1. **[ロールの割り当ての追加]** ウィンドウで、**[閲覧者]** ロールを選択します。 
1. **[アクセスの割り当て先]** フィールドで **[Azure AD のユーザー、グループ、サービス プリンシパル]** を選択します。
1. ロールの割り当て先となるセキュリティ プリンシパルを検索して見つけます。
1. ロールの割り当てを保存します。

> [!NOTE]
> 閲覧者ロールを割り当てる必要があるのは、Azure portal を使用して BLOB またはキューにアクセスする必要があるユーザーに対してのみです。 

## <a name="next-steps"></a>次の手順

- ストレージ リソースの RBAC ロールに関する詳細については、「[Azure Active Directory を利用して Azure BLOB およびキューへのアクセスを認証する](storage-auth-aad.md)」を参照してください。 
- RBAC の詳細については、「[ロールベースのアクセス制御 (RBAC) とは](../../role-based-access-control/overview.md)」を参照してください。
- Azure PowerShell、Azure CLI、または REST API で RBAC ロールを割り当てて管理する方法については、次の記事を参照してください。
    - [Azure PowerShell を使用してロールベースのアクセス制御 (RBAC) を管理する](../../role-based-access-control/role-assignments-powershell.md)
    - [Azure CLI を使用してロールベースのアクセス制御 (RBAC) を管理する](../../role-based-access-control/role-assignments-cli.md)
    - [REST API を使用してロールベースのアクセス制御 (RBAC) を管理する](../../role-based-access-control/role-assignments-rest.md)
- ストレージ アプリケーション内からコンテナーおよびキューへのアクセスを承認する方法については、[Azure Storage アプリケーションでの Azure AD の使用](storage-auth-aad-app.md)に関するページを参照してください。
