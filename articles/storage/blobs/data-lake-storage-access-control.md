---
title: Azure Data Lake Storage Gen2 のアクセス制御の概要 | Microsoft Docs
description: Azure Data Lake Storage Gen2 のアクセス制御のしくみを理解する
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 112d3b18df8205aac173eafb8f8e30ed6c32e048
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249089"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 のアクセス制御

Azure Data Lake Storage Gen2 では、Azure のロールベースのアクセス制御 (RBAC) と POSIX のようなアクセス制御リスト (ACL) の両方をサポートするアクセス制御モデルが実装されています。 この記事では、Data Lake Storage Gen2 のアクセス制御モデルの基本について説明します。

<a id="azure-role-based-access-control-rbac" />

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

RBAC では、ロールの割り当てを使用して、"*セキュリティ プリンシパル*" にアクセス許可のセットが効果的に適用されます。 "*セキュリティ プリンシパル*" は、Azure リソースへのアクセスを要求している、Azure Active Directory (AD) で定義されたユーザー、グループ、サービス プリンシパル、またはマネージド ID を表すオブジェクトです。

通常、それらの Azure リソースは、最上位のリソースに制約されます (例: Azure ストレージ アカウント)。 Azure Storage およびその結果の Azure Data Lake Storage Gen2 の場合、このメカニズムは、コンテナー (ファイル システム) のリソースに拡張されています。

お使いのストレージ アカウントのスコープでセキュリティ プリンシパルにロールを割り当てる方法については、「[Azure portal で RBAC を使用して Azure BLOB とキューのデータへのアクセスを付与する](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」をご覧ください。

### <a name="the-impact-of-role-assignments-on-file-and-directory-level-access-control-lists"></a>ファイルおよびディレクトリ レベルのアクセス制御リストでのロール割り当ての影響

RBAC のロール割り当ての使用は、アクセス許可を制御する強力なメカニズムですが、ACL と比較すると、非常にきめの粗いメカニズムです。 RBAC の最小粒度は、ファイル システム レベルで、これは ACL よりも高い優先順位で評価されます。 したがって、ファイル システムのスコープでセキュリティ プリンシパルにロールを割り当てた場合、ACL の割り当てに関係なく、そのセキュリティ プリンシパルには、そのファイル システム内の "すべて" のディレクトリとファイルに対して、そのロールに関連付けられている承認レベルが適用されます。

[組み込みロール](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues)またはカスタム ロールを通じて、セキュリティ プリンシパルに RBAC データのアクセス許可が付与されると、要求の承認時に、これらのアクセス許可がまず評価されます。 要求された操作がセキュリティ プリンシパルの RBAC の割り当てによって承認されている場合は、承認はすぐに解決され、追加の ACL チェックは実行されません。 または、セキュリティ プリンシパルに RBAC の割り当てがない場合、または要求の操作が割り当てられたアクセス許可と一致しない場合は、セキュリティ プリンシパルが要求された操作を実行することを承認されているかどうかを判断するため、ACL チェックが実行されます。

> [!NOTE]
> セキュリティ プリンシパルにストレージ BLOB データ所有者の組み込みロールが割り当てられている場合、そのセキュリティ プリンシパルは "*スーパー ユーザー*" と見なされ、すべての変更操作 (ディレクトリやファイルの所有権の設定や、自分が所有者ではないディレクトリやファイルへの ACL の設定など) へのフル アクセス権が付与されます。 スーパー ユーザーのアクセス権は、リソースの所有者を変更するために承認された唯一の方法です。

## <a name="shared-key-and-shared-access-signature-sas-authentication"></a>共有キーと Shared Access Signature (SAS) 認証

Azure Data Lake Storage Gen2 では、認証に共有キーと SAS の方法がサポートされています。 これらの認証方法の特徴は、呼び出し元に ID が関連付けられていないため、セキュリティ プリンシパルのアクセス許可ベースの承認を実行できないことです。

共有キーの場合、呼び出し元は効果的に 'スーパー ユーザー' のアクセス権を得られます。つまり、所有者の設定や ACL の変更を含む、すべてのリソースですべての操作に対してフル アクセス権を持つことになります。

SAS トークンには、トークンの一部として許可されるアクセス許可が含まれます。 SAS トークンに含まれるアクセス許可は、すべての承認の決定に効果的に適用されますが、追加の ACL チェックは行われません。

## <a name="access-control-lists-on-files-and-directories"></a>ファイルとディレクトリのアクセス制御リスト

セキュリティ プリンシパルをファイルおよびディレクトリに対するアクセス レベルと関連付けることができます。 これらの関連付けは、"*アクセス制御リスト (ACL)* " でキャプチャされます。 ストレージ アカウント内の各ファイルおよびディレクトリは、アクセス制御リストを持っています。

ストレージ アカウント レベルでセキュリティ プリンシパルにロールを割り当てた場合、アクセス制御リストを使って、そのセキュリティ プリンシパルに、特定のファイルおよびディレクトリに対する昇格されたアクセス権を付与することができます。

アクセス制御リストを使って、ロール割り当てによって許可されるレベルより低いアクセスのレベルを提供することはできません。 たとえば、[ストレージ BLOB データ共同作成者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)ロールをセキュリティ プリンシパルに割り当てた場合、アクセス制御リストを使って、そのセキュリティ プリンシパルによるディレクトリへの書き込みを禁止することはできません。

### <a name="set-file-and-directory-level-permissions-by-using-access-control-lists"></a>アクセス制御リストを使ってファイルおよびディレクトリ レベルのアクセス許可を設定する

ファイルおよびディレクトリ レベルのアクセス許可を設定するには、次のいずれかの記事をご覧ください。

|使うツール:    |参照する記事:    |
|--------|-----------|
|Azure ストレージ エクスプローラー    |[Azure Data Lake Storage Gen2 で Azure Storage Explorer を使用してファイルとディレクトリ レベルのアクセス許可を設定する](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)|
|REST API    |[Path - Update (パス - 更新)](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> セキュリティ プリンシパルが "*サービス*" プリンシパルである場合は、関連するアプリ登録のオブジェクト ID ではなく、サービス プリンシパルのオブジェクト ID を使うことが重要です。 サービス プリンシパルのオブジェクト ID を取得するには、Azure CLI を開き、`az ad sp show --id <Your App ID> --query objectId` コマンドを使います。 `<Your App ID>` プレースホルダーは、アプリ登録のアプリ ID に置き換えます。

### <a name="types-of-access-control-lists"></a>アクセス制御リストの種類

アクセス制御リストには、"*アクセス ACL*" と "*既定の ACL*" の 2 種類があります。

アクセス ACL はオブジェクトへのアクセスを制御します。 ファイルとディレクトリの両方がアクセス ACL を持っています。

既定の ACL は、ディレクトリに関連付けられた ACL のテンプレートです。この ACL によって、そのディレクトリの下に作成されるすべての子項目のアクセス ACL が決まります。 ファイルには既定の ACL がありません。

アクセス ACL と既定の ACL はどちらも同じ構造です。

> [!NOTE]
> 親の既定の ACL を変更しても、既存の子項目のアクセス ACL または既定の ACL には影響しません。

### <a name="levels-of-permission"></a>アクセス許可のレベル

ファイル システム オブジェクトに対するアクセス許可は、**読み取り**、**書き込み**、**実行**であり、次の表に示すように、ファイルとディレクトリに対して使用できます。

|            |    ファイル     |   Directory |
|------------|-------------|----------|
| **読み取り (R)** | ファイルの内容を読み取ることができる | ディレクトリの内容を一覧表示するには、**読み取り**と**実行**が必要です。 |
| **書き込み (W)** | ファイルへの書き込みまたは追加を実行できる | ディレクトリに子項目を作成するには、**書き込み**と**実行**が必要です。 |
| **実行 (X)** | Data Lake Storage Gen2 のコンテキストでは、何も意味しない | ディレクトリの子項目をスキャンするために必要です。 |

> [!NOTE]
> (RBAC は使用せず) ACL のみを使用してアクセス許可を付与するときに、ファイルにサービス プリンシパルの読み取りまたは書き込みアクセスを付与するには、ファイル システムおよびそのファイルにつながるフォルダー階層の各フォルダーにそのサービス プリンシパルの**実行**のアクセス許可を付与する必要があります。

#### <a name="short-forms-for-permissions"></a>アクセス許可の短い形式

**RWX** は、**読み取り + 書き込み + 実行**を示すために使用されます。 さらに縮約された数値形式もあります。**読み取り = 4**、**書き込み = 2**、**実行 = 1** で、その合計でアクセス許可を表します。 次は一部の例です。

| 数値形式 | 短縮形式 |      意味     |
|--------------|------------|------------------------|
| 7            | `RWX`        | 読み取り + 書き込み + 実行 |
| 5            | `R-X`        | 読み取り + 実行         |
| 4            | `R--`        | 読み取り                   |
| 0            | `---`        | アクセス許可なし         |

#### <a name="permissions-inheritance"></a>アクセス許可の継承

Data Lake Storage Gen2 で使用されている POSIX 形式のモデルでは、項目のアクセス許可は項目自体に格納されます。 つまり、子項目が既に作成された後にアクセス許可を設定すると、項目のアクセス許可を親項目から継承できません。 子項目が作成される前に、親項目で既定のアクセス許可が設定されている場合にのみ、アクセス許可が継承されます。

### <a name="common-scenarios-related-to-permissions"></a>アクセス許可に関連する一般的なシナリオ

ストレージ アカウントに対する特定の操作の実行に必要なアクセス許可について理解できるように、一般的なシナリオを次の表に示します。

|    Operation             |    /    | Oregon/ | Portland/ | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Read Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Append to Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Delete Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Create Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| List /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| List /Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| List /Oregon/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> 上の 2 つの条件が満たされていれば、ファイルを削除するために、ファイルに対する書き込みアクセス許可は必要ありません。

### <a name="users-and-identities"></a>ユーザーと ID

すべてのファイルとディレクトリは、以下の ID の個別のアクセス許可を持っています。

- 所有ユーザー
- 所有グループ
- 名前付きユーザー
- 名前付きグループ
- 名前付きサービス プリンシパル
- 名前付きマネージド ID
- その他のすべてのユーザー

ユーザーとグループの ID は、Azure Active Directory (Azure AD) の ID です。 そのため、注記がない限り、Data Lake Store Gen2 のコンテキストでの "*ユーザー*" は、Azure AD ユーザー、サービス プリンシパル、マネージド ID、またはセキュリティ グループを意味します。

#### <a name="the-owning-user"></a>所有ユーザー

項目を作成したユーザーは、自動的に項目の所有ユーザーになります。 所有ユーザーは、次のことができます。

* 所有しているファイルのアクセス許可を変更します。
* 所有しているファイルの所有グループを変更します。ただし、所有ユーザーが変更後のグループのメンバーでもある必要があります。

> [!NOTE]
> 所有ユーザーが、ファイルやディレクトリの所有ユーザーを変更することは*できません*。 ファイルまたはディレクトリの所有ユーザーを変更できるのは、スーパー ユーザーだけです。

#### <a name="the-owning-group"></a>所有グループ

POSIX ACL では、すべてのユーザーが*プライマリ グループ*に関連付けられています。 たとえば、ユーザー "Alice" が "finance" グループに属しているとします。 Alice は複数のグループに属している可能性がありますが、1 つのグループが常にプライマリ グループとして指定されています。 POSIX では、Alice がファイルを作成すると、そのファイルの所有グループが彼女のプライマリ グループに設定されます。ここでは、"finance" グループです。 その他の点では、所有グループの動作は、他のユーザー/グループに割り当てられているアクセス許可と同様です。

##### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>新しいファイルまたはディレクトリに対する所有グループの割り当て

* **ケース 1**:ルート ディレクトリ "/"。 このディレクトリは、Data Lake Storage Gen2 ファイル システムの作成時に作成されます。 この場合、OAuth を使用してファイル システムが作成された場合は、所有グループはそれを作成したユーザーに設定されます。 共有キー、アカウント SAS、またはサービス SAS を使用してファイル システムを作成した場合は、所有者と所有グループは **$superuser** に設定されます。
* **ケース 2** (その他すべての場合):新しい項目が作成されると、所有グループが親ディレクトリからコピーされます。

##### <a name="changing-the-owning-group"></a>所有グループの変更

所有グループを変更できるユーザーは次のとおりです。
* すべてのスーパー ユーザー
* 所有ユーザー (所有ユーザーが変更後のグループのメンバーでもある場合)

> [!NOTE]
> 所有グループが、ファイルやディレクトリの ACL を変更することはできません。  ルート ディレクトリの場合 (上記の**ケース 1**)、所有グループはアカウントを作成したユーザーに設定されますが、所有グループを介したアクセス許可の付与に関して、単一ユーザー アカウントは有効ではありません。 この権限は、有効なユーザー グループに対して、該当する場合に割り当てることができます。

### <a name="access-check-algorithm"></a>アクセス確認アルゴリズム

次の擬似コードは、ストレージ アカウントのアクセス確認アルゴリズムを示しています。

```
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or directory
  # Note: the "sticky bit" isn't illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

# Handle the owning user. Note that mask isn't used.
entry = get_acl_entry( path, OWNER )
if (user == entry.identity)
    return ( (desired_perms & entry.permissions) == desired_perms )

# Handle the named users. Note that mask IS used.
entries = get_acl_entries( path, NAMED_USER )
for entry in entries:
    if (user == entry.identity ) :
        mask = get_mask( path )
        return ( (desired_perms & entry.permissions & mask) == desired_perms)

# Handle named groups and owning group
member_count = 0
perms = 0
entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
for entry in entries:
if (user_is_member_of_group(user, entry.identity)) :
    member_count += 1
    perms | =  entry.permissions
if (member_count>0) :
return ((desired_perms & perms & mask ) == desired_perms)

# Handle other
perms = get_perms_for_other(path)
mask = get_mask( path )
return ( (desired_perms & perms & mask ) == desired_perms)
```

#### <a name="the-mask"></a>マスク

アクセス確認アルゴリズムに示されているように、マスクによって、名前付きユーザー、所有グループ、および名前付きグループのアクセスが制限されます。  

> [!NOTE]
> 新しい Data Lake Storage Gen2 ファイル システムでは、ルート ディレクトリ ("/") のアクセス ACL のマスクは、ディレクトリの場合は 750、ファイルの場合は 640 に既定で設定されています。 ファイルは格納専用のシステム内のファイルとは無関係であるため、X ビットを受け取りません。
>
> マスクは、呼び出しごとに指定できます。 これにより、クラスターなどのさまざまな使用システムが、ファイル操作に対して異なる有効なマスクを持つことができます。 特定の要求でマスクが指定されると、既定のマスクが完全にオーバーライドされます。

#### <a name="the-sticky-bit"></a>スティッキー ビット

スティッキー ビットは、POSIX ファイル システムのより高度な機能です。 Data Lake Storage Gen2 のコンテキストでは、スティッキー ビットが必要になることはあまりありません。 つまり、ディレクトリでスティッキー ビットが有効になっている場合、子項目を削除または名前変更できるのは、子項目を所有しているユーザーのみです。

スティッキー ビットは、Azure portal には表示されません。

### <a name="default-permissions-on-new-files-and-directories"></a>新しいファイルとディレクトリの既定のアクセス許可

既存のディレクトリの下に新しいファイルまたはディレクトリが作成されると、親ディレクトリの既定の ACL によって、次のものが決定します。

- 子ディレクトリの既定の ACL とアクセス ACL。
- 子ファイルのアクセス ACL (ファイルには既定の ACL がありません)。

#### <a name="umask"></a>umask

ファイルまたはディレクトリを作成するときに、umask を使用して、子項目に既定の ACL がどのように設定されるかを変更します。 umask は親ディレクトリに設定される 9 ビットの値であり、**所有ユーザー**、**所有グループ**、および**その他**に対する RWX 値が含まれています。

Azure Data Lake Storage Gen2 に対する umask は、007 に設定される定数値です。 この値の変換値:

| umask コンポーネント     | 数値形式 | 短縮形式 | 意味 |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | 所有ユーザーの場合、親の既定の ACL を子のアクセス ACL にコピーします | 
| umask.owning_group  |    0         |   `---`      | 所有グループの場合、親の既定の ACL を子のアクセス ACL にコピーします | 
| umask.other         |    7         |   `RWX`      | その他の場合、子のアクセス ACL 上のすべてのアクセス許可を削除します |

Azure Data Lake Storage Gen2 で umask の値が使用されると、実際上は、既定の ACL が何を示しているかに関係なく、既定では、**その他**に対する値は、新しい子では決して送信されないことを意味します。 

次の疑似コードは、子項目に ACL を作成するときに、unmask がどのように適用されるかを示しています。

```
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    for entry in parent.acls :
        new_entry = None
        if (entry.type == OWNING_USER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_user))
        elif (entry.type == OWNING_GROUP) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_group))
        elif (entry.type == OTHER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.other))
        else :
            new_entry = entry.clone(perms = entry.perms )
        child_acls.add( new_entry )
```

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>Data Lake Storage Gen2 の ACL に関する一般的な質問

### <a name="do-i-have-to-enable-support-for-acls"></a>ACL のサポートを有効にする必要はありますか

いいえ。 階層型名前空間 (HNS) 機能がオンになっている限り、ストレージ アカウントの ACL によるアクセス制御は有効です。

HNS がオフになっている場合、Azure RBAC の承認規則が引き続き適用されます。

### <a name="what-is-the-best-way-to-apply-acls"></a>ACL を適用する最善の方法

ACL で割り当て済みのプリンシパルとして常に Azure AD セキュリティ グループを使用します。 個々のユーザーまたはサービス プリンシパルを直接割り当てることを抑止します。 この構造体を使用すると、ユーザーまたはサービス プリンシパルを追加したり削除したりすることができます。ACL をディレクトリ構造全体に再適用する必要はありません。 ) 代わりに、適切な Azure AD セキュリティ グループからユーザーまたはサービス プリンシパルを追加または削除するだけです。 ACL は継承されないため、ACL の再適用には、すべてのファイルとサブディレクトリで ACL を更新する必要があることに注意してください。 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>ディレクトリとその内容を再帰的に削除するのに必要なアクセス許可を教えてください

- 呼び出し元に 'スーパー ユーザー' アクセス許可がある

または

- 親ディレクトリには、書き込み + 実行アクセス許可が必要
- 削除対象のディレクトリとその中のすべてのディレクトリには、読み取り + 書き込み + 実行アクセス許可が必要

> [!NOTE]
> ディレクトリ内のファイルを削除するには、書き込みアクセス許可は必要ありません。 また、ルート ディレクトリ "/" を削除することはできません。

### <a name="who-is-the-owner-of-a-file-or-directory"></a>ファイルまたはディレクトリの所有者として設定されるのはだれですか

ファイルまたはディレクトリの作成者が所有者になります。 ルート ディレクトリの場合、これはファイル システムを作成したユーザーの ID です。

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>ファイルまたはディレクトリの作成時に、所有グループとして設定されるのはどのグループですか

所有グループは、新しいファイルまたはディレクトリが作成される親ディレクトリの所有グループからコピーされます。

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>ファイルの所有ユーザーですが、必要な RWX アクセス許可を持っていません。 どうすればよいですか。

所有ユーザーは、ファイルのアクセス許可を変更して、必要な任意の RWX アクセス許可を自分に与えることができます。

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>ACL に GUID が表示されることがあるのはなぜですか

エントリがユーザーを表し、そのユーザーがもう Azure AD に存在しなくなった場合に、GUID が表示されます。 通常、ユーザーが会社を辞めた場合や Azure AD でそのアカウントが削除された場合に、この現象が発生します。 さらに、サービス プリンシパルおよびセキュリティ グループには、それらを識別するためのユーザー プリンシパル名 (UPN) がないため、これらは自身の OID 属性 (guid) で表されます。

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>サービス プリンシパル用 ACL を正しく設定するにはどうすればよいですか。

サービス プリンシパル用 ACL を定義するときは、作成したアプリ登録に対応する "*サービス プリンシパル*" のオブジェクト ID (OID) を使用することが重要です。 登録済みアプリについては、特定の Azure AD テナントに個別のサービス プリンシパルがあることに注意してください。 登録済みアプリの OID は Azure portal に表示されていますが、その "*サービス プリンシパル*" には別の (異なる) OID があります。

アプリ登録に対応するサービス プリンシパルの OID を取得するには、`az ad sp show` コマンドを使用し、 パラメーターとしてアプリケーション ID を指定します。 アプリ ID が 18218b12-1895-43e9-ad80-6e8fc1ea88ce のアプリ登録に対応するサービス プリンシパルの OID を取得する例を次に示します。 Azure CLI で、次のコマンドを実行します。

```
$ az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
<<OID will be displayed>>
```

サービス プリンシパルの正しい OID を取得したら、Storage Explorer で **[アクセスの管理]** ページに移動して OID を追加し、その OID に対する適切なアクセス許可を割り当てます。 その後、必ず **[保存]** を選択してください。

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Data Lake Storage Gen2 は ACL の継承をサポートしていますか。

Azure RBAC の割り当ては継承されます。 割り当ては、サブスクリプション、リソース グループ、ストレージ アカウント リソースからファイル システム リソースに渡されます。

ACL は継承されません。 ただし、親ディレクトリの下に作成される子サブディレクトリやファイルについては、既定の ACL を使用して ACL を設定できます。 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>POSIX アクセス制御モデルの詳細はどこで確認できますか

* [POSIX Access Control Lists on Linux (Linux での POSIX アクセス制御リスト)](https://www.linux.com/news/posix-acls-linux)
* [HDFS Permission Guide (HDFS アクセス許可ガイド)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [POSIX FAQ (POSIX のよく寄せられる質問)](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [Ubuntu での POSIX ACL](https://help.ubuntu.com/community/FilePermissionsACLs)
* [ACL using access control lists on Linux (Linux でのアクセス制御リストを使用した ACL)](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>関連項目

* [Azure Data Lake Storage Gen2 の概要](../blobs/data-lake-storage-introduction.md)
