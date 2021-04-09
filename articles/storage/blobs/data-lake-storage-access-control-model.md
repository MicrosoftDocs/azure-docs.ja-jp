---
title: Azure Data Lake Storage Gen2 のアクセス制御モデル | Microsoft Docs
description: 階層型名前空間を持つアカウントでコンテナー、ディレクトリ、およびファイルレベルのアクセスを構成する方法について説明します。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: normesta
ms.openlocfilehash: 9fa7f586db5a32640c16db5802b56d6481e8889d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439291"
---
# <a name="access-control-model-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 のアクセス制御モデル

Data Lake Storage Gen2 では、次の認可メカニズムがサポートされています。

- 共有キー認可
- Shared Access Signature (SAS) 認可
- ロールベースのアクセス制御 (Azure RBAC)
- アクセス制御リスト (ACL)

[共有キーと SAS 認可](#shared-key-and-shared-access-signature-sas-authorization)では、Azure Active Directory (Azure AD) で ID を取得するように要求せずに、ユーザー (またはアプリケーション) へのアクセスが許可されます。 これらの 2 つの形式の認証を使用しても、Azure RBAC と ACL は影響を受けません。

Azure RBAC と ACL では両方とも、ユーザー (またはアプリケーション) が Azure AD で ID を取得する必要があります。  Azure RBAC を使用すると、ストレージ アカウント データへの "粒度の粗い" アクセス (ストレージ アカウント内の **すべての** データへの読み取りアクセスまたは書き込みアクセスなど) を許可できます。一方、ACL を使用すると、"粒度の細かい" アクセス (特定のディレクトリやファイルへの書き込みアクセスなど) を許可できます。  

この記事では、システムで Azure RBAC と ACL をまとめて評価し、ストレージ アカウント リソースに対する認可の決定を行う方法について説明します。

<a id="role-based-access-control"></a>

## <a name="role-based-access-control-azure-rbac"></a>ロールベースのアクセス制御 (Azure RBAC)

Azure RBAC を使用すると、ロールの割り当てを使用して、"[セキュリティ プリンシパル](../../role-based-access-control/overview.md#security-principal)" にアクセス許可セットが適用されます。 セキュリティ プリンシパルは、Azure Active Directory (AD) で定義されたユーザー、グループ、サービス プリンシパル、またはマネージド ID を表すオブジェクトです。 アクセス許可セットを使用すると、セキュリティ プリンシパルに "粒度の粗い" アクセス レベル (ストレージ アカウント内の **すべての** データやコンテナー内の **すべての** データへの読み取りアクセスまたは書き込みアクセスなど) を付与できます。 

次のロールでは、セキュリティ プリンシパルがストレージ アカウント内のデータにアクセスすることが許可されます。

|Role|説明|
|--|--|
| [ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) | BLOB コンテナーおよびデータへのフル アクセス。 このアクセスでは、セキュリティ プリンシパルが所有者に項目を設定し、すべての項目の ACL を変更することが許可されます。 |
| [ストレージ BLOB データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) | BLOB ストレージ コンテナーと BLOB への読み取り、書き込み、削除アクセス。 このアクセスでは、セキュリティ プリンシパルが項目の所有権を設定することが許可されませんが、セキュリティ プリンシパルが所有している項目の ACL を変更することはできます。 |
| [ストレージ BLOB データ閲覧者](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) | BLOB ストレージ コンテナーと BLOB を読み取り、一覧表示します。 |

[所有者](../../role-based-access-control/built-in-roles.md#owner)、[共同作成者](../../role-based-access-control/built-in-roles.md#contributor)、[閲覧者](../../role-based-access-control/built-in-roles.md#reader)、[ストレージ アカウント共同作成者](../../role-based-access-control/built-in-roles.md#storage-account-contributor)などのロールでは、セキュリティ プリンシパルがストレージ アカウントを管理することが許可されますが、そのアカウント内のデータにアクセスすることはできません。 ただし、これらのロール (**閲覧者** を除く) では、データにアクセスするためにさまざまなクライアント ツールで使用できるストレージ キーへのアクセス権を取得できます。

## <a name="access-control-lists-acls"></a>アクセス制御リスト (ACL)

ACL を使用すると、ディレクトリやファイルに "粒度の細かい" アクセス レベルを適用できます。 "*ACL*" は、一連の "*ACL エントリ*" を含むアクセス許可の構成体です。 各 ACL エントリでは、セキュリティ プリンシパルとアクセス レベルが関連付けられます。  詳細については、[Azure Data Lake Storage Gen2 でのアクセス制御リスト (ACL)](data-lake-storage-access-control.md) に関するページを参照してください。

## <a name="how-permissions-are-evaluated"></a>権限を評価する方法

セキュリティ プリンシパルベースの認可を行う際に、次の順序でアクセス許可が評価されます。

:one:&nbsp;&nbsp;Azure ロールの割り当てが最初に評価され、ACL の割り当てよりも優先されます。

:two:&nbsp;&nbsp;Azure ロールの割り当てに基づいて操作が完全に認可されている場合は、ACL がまったく評価されません。

:three:&nbsp;&nbsp;操作が完全に認可されていない場合は、ACL が評価されます。

> [!div class="mx-imgBorder"]
> ![Data Lake Storage のアクセス許可フロー](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-flow.png)

アクセス許可がシステムで評価される方法が原因で、ACL を使用して、ロールの割り当てによって既に付与されているアクセスを **制限する** ことが **できません**。 これは、Azure ロールの割り当てが最初に評価され、その割り当てで十分なアクセス許可が付与されている場合は、ACL が無視されるためです。 

次の図は、ディレクトリの内容の一覧表示、ファイルの読み取り、ファイルの書き込みという 3 つの一般的な操作に対するアクセス許可フローを示しています。

> [!div class="mx-imgBorder"]
> ![Data Lake Storage のアクセス許可フローの例](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-example.png)

## <a name="permissions-table-combining-azure-rbac-and-acl"></a>アクセス許可の表:Azure RBAC と ACL の組み合わせ

次の表は、セキュリティ プリンシパルが **[操作]** 列に示されている操作を実行できるように、Azure ロールと ACL エントリを組み合わせる方法を示しています。 この表は、架空のディレクトリ階層の各レベルを表す列を示しています。 コンテナーのルート ディレクトリ (`/`)、**Oregon** という名前のサブディレクトリ、**Portland** という名前の Oregon ディレクトリのサブディレクトリ、および **Data.txt** という名前の Portland ディレクトリのテキスト ファイルの列があります。 これらの列には、アクセス許可を付与するために必要な ACL エントリが[短い形式](data-lake-storage-access-control.md#short-forms-for-permissions)で表示されます。 操作を実行するために ACL エントリが必要ない場合は、列に **該当なし** (_適用できません_) と表示されます。

|    操作             | 割り当てられた Azure ロール               |    /        | Oregon/     | Portland/ | Data.txt |             
|--------------------------|----------------------------------|-------------|-------------|-----------|----------|
| Read Data.txt            |   ストレージ BLOB データ所有者        | 該当なし      | なし      | なし       | 該当なし    |  
|                          |   ストレージ BLOB データ共同作成者  | 該当なし      | なし      | なし       | 該当なし    |
|                          |   ストレージ BLOB データ閲覧者       | 該当なし      | なし      | なし       | N/A    |
|                          |   None                           | `--X`    | `--X`    | `--X`     | `R--`  |
| Append to Data.txt       |   ストレージ BLOB データ所有者        | 該当なし      | なし      | なし       | 該当なし    |
|                          |   ストレージ BLOB データ共同作成者  | 該当なし      | なし      | なし       | 該当なし    |
|                          |   ストレージ BLOB データ閲覧者       | `--X`    | `--X`    | `--X`     | `-W-`  |
|                          |   なし                           | `--X`    | `--X`    | `--X`     | `RW-`  |
| Delete Data.txt          |   ストレージ BLOB データ所有者        | 該当なし      | なし      | なし       | 該当なし    |
|                          |   ストレージ BLOB データ共同作成者  | 該当なし      | なし      | なし       | 該当なし    |
|                          |   ストレージ BLOB データ閲覧者       | `--X`    | `--X`    | `-WX`     | N/A    |
|                          |   None                           | `--X`    | `--X`    | `-WX`     | 該当なし    |
| Create Data.txt          |   ストレージ BLOB データ所有者        | 該当なし      | なし      | なし       | 該当なし    |
|                          |   ストレージ BLOB データ共同作成者  | 該当なし      | なし      | なし       | 該当なし    |
|                          |   ストレージ BLOB データ閲覧者       | `--X`    | `--X`    | `-WX`     | N/A    |
|                          |   None                           | `--X`    | `--X`    | `-WX`     | 該当なし    |
| List /                   |   ストレージ BLOB データ所有者        | 該当なし      | なし      | なし       | 該当なし    |
|                          |   ストレージ BLOB データ共同作成者  | 該当なし      | なし      | なし       | 該当なし    |
|                          |   ストレージ BLOB データ閲覧者       | 該当なし      | なし      | なし       | N/A    |
|                          |   None                           | `R-X`    | 該当なし      | なし       | 該当なし    |
| List /Oregon/            |   ストレージ BLOB データ所有者        | 該当なし      | なし      | なし       | 該当なし    |
|                          |   ストレージ BLOB データ共同作成者  | 該当なし      | なし      | なし       | 該当なし    |
|                          |   ストレージ BLOB データ閲覧者       | 該当なし      | なし      | なし       | N/A    |
|                          |   None                           | `--X`    | `R-X`    | 該当なし       | 該当なし    |
| List /Oregon/Portland/   |   ストレージ BLOB データ所有者        | 該当なし      | なし      | なし       | 該当なし    |
|                          |   ストレージ BLOB データ共同作成者  | 該当なし      | なし      | なし       | 該当なし    |
|                          |   ストレージ BLOB データ閲覧者       | 該当なし      | なし      | なし       | N/A    |
|                          |   None                           | `--X`    | `--X`    | `R-X`     | 該当なし    |


> [!NOTE] 
> Azure Storage Explorer でコンテナーの内容を表示するには、セキュリティ プリンシパルが [Azure AD を使用して Storage Explorer にサインインし](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#attach-to-an-individual-resource)、(少なくとも) コンテナーのルート フォルダー (`\`) への読み取りアクセス (R--) を持っている必要があります。 このアクセス許可レベルでは、ルート フォルダーの内容を一覧表示できます。 ルート フォルダーの内容が表示されないようにするには、[[閲覧者]](../../role-based-access-control/built-in-roles.md#reader) ロールを割り当てます。 このロールでは、アカウント内のコンテナーを一覧表示できますが、コンテナーの内容を表示することはできません。 その後、ACL を使用して、特定のディレクトリやファイルへのアクセスを許可できます。   

## <a name="security-groups"></a>セキュリティ グループ

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-groups.md)]

## <a name="limits-on-azure-role-assignments-and-acl-entries"></a>Azure ロールの割り当てと ACL エントリに対する制限

グループを使用すると、サブスクリプションごとのロール割り当ての最大数と、ファイルやディレクトリごとの ACL エントリの最大数を超える可能性が低くなります。 次の表に制限事項を示します。

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-rbac-acl-limits.md)] 

## <a name="shared-key-and-shared-access-signature-sas-authorization"></a>共有キーと Shared Access Signature (SAS) 認可

Azure Data Lake Storage Gen2 では、認証に[共有キー](/rest/api/storageservices/authorize-with-shared-key)と [SAS](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) の方法がサポートされています。 これらの認証方法の特徴は、呼び出し元に ID が関連付けられていないため、セキュリティ プリンシパルのアクセス許可ベースの承認を実行できないことです。

共有キーの場合、呼び出し元では効果的に 'スーパー ユーザー' のアクセス権が取得されます。つまり、データ、所有者の設定、ACL の変更を含む、すべてのリソースですべての操作に対してフル アクセス権を持つことになります。

SAS トークンには、トークンの一部として許可されるアクセス許可が含まれます。 SAS トークンに含まれるアクセス許可は、すべての承認の決定に効果的に適用されますが、追加の ACL チェックは行われません。

## <a name="next-steps"></a>次の手順

アクセス制御リストの詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御リスト (ACL)](data-lake-storage-access-control.md)」を参照してください。