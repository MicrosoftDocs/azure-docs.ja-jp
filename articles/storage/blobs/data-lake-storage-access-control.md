---
title: Azure Data Lake Storage Gen2 のアクセス制御リスト | Microsoft Docs
description: Azure Data Lake Storage Gen2 で POSIX のような ACL アクセス制御リストがどのように機能するかを理解する。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 50c6b4f309eb78acee0cfa59d1b540adba65cab2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104774815"
---
# <a name="access-control-lists-acls-in-azure-data-lake-storage-gen2"></a>AzureData Lake Storage Gen2 のアクセス制御リスト (ACL)

Azure Data Lake Storage Gen2 では、Azure ロールベースのアクセス制御 (Azure RBAC) と POSIX のようなアクセス制御リスト (ACL) の両方をサポートするアクセス制御モデルが実装されています。 この記事では、Data Lake Storage Gen2 のアクセス制御リストについて説明します。 Azure RBAC を ACL に一緒に組み込む方法と、それらをシステムで評価して認可の決定を行う方法の詳細については、「[Azure Data Lake Storage Gen2 でのアクセス制御モデル](data-lake-storage-access-control-model.md)」を参照してください。

<a id="access-control-lists-on-files-and-directories"></a>

## <a name="about-acls"></a>ACL について

[セキュリティ プリンシパル](../../role-based-access-control/overview.md#security-principal)をファイルおよびディレクトリに対するアクセス レベルと関連付けることができます。 これらの関連付けは、"*アクセス制御リスト (ACL)* " でキャプチャされます。 ストレージ アカウント内の各ファイルおよびディレクトリは、アクセス制御リストを持っています。 セキュリティ プリンシパルがファイルまたはディレクトリに対して操作を実行しようとすると、ACL チェックによって、そのセキュリティ プリンシパル (ユーザー、グループ、サービス プリンシパル、またはマネージド ID) が、その操作を実行するための適切なアクセス許可レベルを持っているかどうかが判断されます。

> [!NOTE]
> ACL は同じテナント内のセキュリティ プリンシパルにのみ適用され、共有キーまたは Shared Access Signature (SAS) トークン認証を使用するユーザーには適用されません。 これは、呼び出し元に ID が関連付けられていないため、セキュリティ プリンシパルのアクセス許可ベースの認可を実行できないことが原因です。  

<a id="set-access-control-lists"></a>

## <a name="how-to-set-acls"></a>ACL を設定する方法

ファイルおよびディレクトリ レベルのアクセス許可を設定するには、次のいずれかの記事をご覧ください。

| 環境 | [アーティクル] |
|--------|-----------|
|Azure ストレージ エクスプローラー |[Azure Storage Explorer を使用して Azure Data Lake Storage Gen2 での ACL を設定する](data-lake-storage-explorer-acl.md)|
|.NET |[Azure Data Lake Storage Gen2 で .NET を使用して ACL を設定する](data-lake-storage-acl-dotnet.md)|
|Java|[Azure Data Lake Storage Gen2 で Java を使用して ACL を設定する](data-lake-storage-acl-java.md)|
|Python|[Azure Data Lake Storage Gen2 で Python を使用して ACL を設定する](data-lake-storage-acl-python.md)|
|JavaScript (Node.js)JavaScript (Node.js)|[Node.js の JavaScript SDK を使用して Azure Data Lake Storage Gen2 で ACL を設定する](data-lake-storage-directory-file-acl-javascript.md)|
|PowerShell|[Azure Data Lake Storage Gen2 で PowerShell を使用して ACL を設定する](data-lake-storage-acl-powershell.md)|
|Azure CLI|[Azure Data Lake Storage Gen2 で Azure CLI を使用して ACL を設定する](data-lake-storage-acl-cli.md)|
|REST API |[Path - Update (パス - 更新)](/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> セキュリティ プリンシパルが "*サービス*" プリンシパルである場合は、関連するアプリ登録のオブジェクト ID ではなく、サービス プリンシパルのオブジェクト ID を使うことが重要です。 サービス プリンシパルのオブジェクト ID を取得するには、Azure CLI を開き、`az ad sp show --id <Your App ID> --query objectId` コマンドを使います。 `<Your App ID>` プレースホルダーは、アプリ登録のアプリ ID に置き換えます。

## <a name="types-of-acls"></a>ACL の種類

アクセス制御リストには、"*アクセス ACL*" と "*既定の ACL*" の 2 種類があります。

アクセス ACL はオブジェクトへのアクセスを制御します。 ファイルとディレクトリの両方がアクセス ACL を持っています。

既定の ACL は、ディレクトリに関連付けられた ACL のテンプレートです。この ACL によって、そのディレクトリの下に作成されるすべての子項目のアクセス ACL が決まります。 ファイルには既定の ACL がありません。

アクセス ACL と既定の ACL はどちらも同じ構造です。

> [!NOTE]
> 親の既定の ACL を変更しても、既存の子項目のアクセス ACL または既定の ACL には影響しません。

## <a name="levels-of-permission"></a>アクセス許可のレベル

コンテナー内のディレクトリとファイルに対するアクセス許可は、**読み取り**、**書き込み**、**実行** です。これらは、次の表に示すようにファイルとディレクトリに対して使用できます。

|            |    ファイル     |   ディレクトリ |
|------------|-------------|----------|
| **読み取り (R)** | ファイルの内容を読み取ることができる | ディレクトリの内容を一覧表示するには、**読み取り** と **実行** が必要です。 |
| **書き込み (W)** | ファイルへの書き込みまたは追加を実行できる | ディレクトリに子項目を作成するには、**書き込み** と **実行** が必要です。 |
| **実行 (X)** | Data Lake Storage Gen2 のコンテキストでは、何も意味しない | ディレクトリの子項目をスキャンするために必要です。 |

> [!NOTE]
> ACL のみを使用して (Azure RBAC なしで) アクセス許可を付与する場合、セキュリティ プリンシパルにファイルの読み取りまたは書き込みアクセス権を付与するには、コンテナーのルート フォルダー、およびそのファイルに至るフォルダー階層内の各フォルダーに対する **実行** アクセス許可をセキュリティ プリンシパルに付与する必要があります。

### <a name="short-forms-for-permissions"></a>アクセス許可の短い形式

**RWX** は、**読み取り + 書き込み + 実行** を示すために使用されます。 さらに縮約された数値形式もあります。**読み取り = 4**、**書き込み = 2**、**実行 = 1** で、その合計でアクセス許可を表します。 次は一部の例です。

| 数値形式 | 短縮形式 |      意味     |
|--------------|------------|------------------------|
| 7            | `RWX`        | 読み取り + 書き込み + 実行 |
| 5            | `R-X`        | 読み取り + 実行         |
| 4            | `R--`        | Read                   |
| 0            | `---`        | アクセス許可なし         |

### <a name="permissions-inheritance"></a>アクセス許可の継承

Data Lake Storage Gen2 で使用されている POSIX 形式のモデルでは、項目のアクセス許可は項目自体に格納されます。 つまり、子項目が既に作成された後にアクセス許可を設定すると、項目のアクセス許可を親項目から継承できません。 子項目が作成される前に、親項目で既定のアクセス許可が設定されている場合にのみ、アクセス許可が継承されます。

## <a name="common-scenarios-related-to-acl-permissions"></a>ACL アクセス許可に関連する一般的なシナリオ

次の表は、**Operation** 列に示されている操作を実行するためにセキュリティ プリンシパルを有効にするのに必要な ACL エントリを示します。 

この表は、架空のディレクトリ階層の各レベルを表す列を示しています。 コンテナーのルート ディレクトリ (`\`)、**Oregon** という名前のサブディレクトリ、**Portland** という名前の Oregon ディレクトリのサブディレクトリ、および **Data.txt** という名前の Portland ディレクトリのテキスト ファイルの列があります。 

> [!IMPORTANT]
> この表は、Azure ロールの割り当てを使用せずに ACL **のみ** を使用していることを前提としています。 Azure RBAC と ACL を組み合わせた同様の表を確認するには、「[アクセス許可の表: Azure RBAC と ACL の組み合わせ](data-lake-storage-access-control-model.md#permissions-table-combining-azure-rbac-and-acl)」を参照してください。

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

## <a name="users-and-identities"></a>ユーザーと ID

すべてのファイルとディレクトリは、以下の ID の個別のアクセス許可を持っています。

- 所有ユーザー
- 所有グループ
- 名前付きユーザー
- 名前付きグループ
- 名前付きサービス プリンシパル
- 名前付きマネージド ID
- その他のすべてのユーザー

ユーザーとグループの ID は、Azure Active Directory (Azure AD) の ID です。 そのため、注記がない限り、Data Lake Store Gen2 のコンテキストでの "*ユーザー*" は、Azure AD ユーザー、サービス プリンシパル、マネージド ID、またはセキュリティ グループを意味します。

### <a name="the-owning-user"></a>所有ユーザー

項目を作成したユーザーは、自動的に項目の所有ユーザーになります。 所有ユーザーは、次のことができます。

* 所有しているファイルのアクセス許可を変更します。
* 所有しているファイルの所有グループを変更します。ただし、所有ユーザーが変更後のグループのメンバーでもある必要があります。

> [!NOTE]
> 所有ユーザーが、ファイルやディレクトリの所有ユーザーを変更することは *できません*。 ファイルまたはディレクトリの所有ユーザーを変更できるのは、スーパー ユーザーだけです。

### <a name="the-owning-group"></a>所有グループ

POSIX ACL では、すべてのユーザーが *プライマリ グループ* に関連付けられています。 たとえば、ユーザー "Alice" が "finance" グループに属しているとします。 Alice は複数のグループに属している可能性がありますが、1 つのグループが常にプライマリ グループとして指定されています。 POSIX では、Alice がファイルを作成すると、そのファイルの所有グループが彼女のプライマリ グループに設定されます。ここでは、"finance" グループです。 その他の点では、所有グループの動作は、他のユーザー/グループに割り当てられているアクセス許可と同様です。

#### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>新しいファイルまたはディレクトリに対する所有グループの割り当て

* **ケース 1**:ルート ディレクトリ "/"。 このディレクトリは、Data Lake Storage Gen2 コンテナーの作成時に作成されます。 この場合、OAuth を使用してコンテナーが作成された場合は、所有グループはそれを作成したユーザーに設定されます。 共有キー、アカウント SAS、またはサービス SAS を使用してコンテナーを作成した場合は、所有者と所有グループは **$superuser** に設定されます。
* **ケース 2** (その他すべての場合):新しい項目が作成されると、所有グループが親ディレクトリからコピーされます。

#### <a name="changing-the-owning-group"></a>所有グループの変更

所有グループを変更できるユーザーは次のとおりです。
* すべてのスーパー ユーザー
* 所有ユーザー (所有ユーザーが変更後のグループのメンバーでもある場合)

> [!NOTE]
> 所有グループが、ファイルやディレクトリの ACL を変更することはできません。  ルート ディレクトリの場合 (上記の **ケース 1**)、所有グループはアカウントを作成したユーザーに設定されますが、所有グループを介したアクセス許可の付与に関して、単一ユーザー アカウントは有効ではありません。 この権限は、有効なユーザー グループに対して、該当する場合に割り当てることができます。

## <a name="access-check-algorithm"></a>アクセス確認アルゴリズム

次の擬似コードは、ストレージ アカウントのアクセス確認アルゴリズムを示しています。

```console
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
  mask = get_mask( path )
  for entry in entries:
    if (user_is_member_of_group(user, entry.identity)) :
        if ((desired_perms & entry.permissions & mask) == desired_perms)
            return True 
        
  # Handle other
  perms = get_perms_for_other(path)
  mask = get_mask( path )
  return ( (desired_perms & perms & mask ) == desired_perms)
```

### <a name="the-mask"></a>マスク

アクセス確認アルゴリズムに示されているように、マスクによって、名前付きユーザー、所有グループ、および名前付きグループのアクセスが制限されます。  

新しい Data Lake Storage Gen2 コンテナーでは、ルート ディレクトリ ("/") のアクセス ACL のマスクは、ディレクトリの場合は **750**、ファイルの場合は **640** に既定で設定されています。 次の表は、これらのアクセス許可レベルのシンボリック表記を示しています。

|Entity|ディレクトリ|ファイル|
|--|--|--|
|所有ユーザー|`rwx`|`r-w`|
|所有グループ|`r-x`|`r--`|
|その他|`---`|`---`|

ファイルは格納専用のシステム内のファイルとは無関係であるため、X ビットを受け取りません。 

マスクは、呼び出しごとに指定できます。 これにより、クラスターなどのさまざまな使用システムが、ファイル操作に対して異なる有効なマスクを持つことができます。 特定の要求でマスクが指定されると、既定のマスクが完全にオーバーライドされます。

### <a name="the-sticky-bit"></a>スティッキー ビット

スティッキー ビットは、POSIX コンテナーのより高度な機能です。 Data Lake Storage Gen2 のコンテキストでは、スティッキー ビットが必要になることはあまりありません。 つまり、ディレクトリでスティッキー ビットが有効になっている場合、子項目を削除または名前変更できるのは、子項目を所有しているユーザーのみです。

スティッキー ビットは、Azure portal には表示されません。

## <a name="default-permissions-on-new-files-and-directories"></a>新しいファイルとディレクトリの既定のアクセス許可

既存のディレクトリの下に新しいファイルまたはディレクトリが作成されると、親ディレクトリの既定の ACL によって、次のものが決定します。

- 子ディレクトリの既定の ACL とアクセス ACL。
- 子ファイルのアクセス ACL (ファイルには既定の ACL がありません)。

### <a name="umask"></a>umask

ファイルまたはディレクトリを作成するときに、umask を使用して、子項目に既定の ACL がどのように設定されるかを変更します。 umask は親ディレクトリに設定される 9 ビットの値であり、**所有ユーザー**、**所有グループ**、および **その他** に対する RWX 値が含まれています。

Azure Data Lake Storage Gen2 に対する umask は、007 に設定される定数値です。 この値の変換値:

| umask コンポーネント     | 数値形式 | 短縮形式 | 意味 |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | 所有ユーザーの場合、親の既定の ACL を子のアクセス ACL にコピーします | 
| umask.owning_group  |    0         |   `---`      | 所有グループの場合、親の既定の ACL を子のアクセス ACL にコピーします | 
| umask.other         |    7         |   `RWX`      | その他の場合、子のアクセス ACL 上のすべてのアクセス許可を削除します |

Azure Data Lake Storage Gen2 で umask 値の値が使用されると、実質的に、既定の ACL が親ディレクトリに定義されていない限り、**other** の値は新しい子では既定で送信されないことを意味してます。 その場合、umask は実質的に無視され、既定の ACL によって定義されたアクセス許可が子項目に適用されます。 

次の疑似コードは、子項目に ACL を作成するときに、unmask がどのように適用されるかを示しています。

```console
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

## <a name="faq"></a>よく寄せられる質問

### <a name="do-i-have-to-enable-support-for-acls"></a>ACL のサポートを有効にする必要はありますか

いいえ。 階層型名前空間 (HNS) 機能がオンになっている限り、ストレージ アカウントの ACL によるアクセス制御は有効です。

HNS がオフになっている場合、Azure RBAC の承認規則が引き続き適用されます。

### <a name="what-is-the-best-way-to-apply-acls"></a>ACL を適用する最善の方法

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-groups.md)] 

### <a name="how-are-azure-rbac-and-acl-permissions-evaluated"></a>Azure RBAC と ACL のアクセス許可はどのように評価されますか?

システムが Azure RBAC と ACL をまとめて評価し、ストレージ アカウント リソースに対する認可の決定を行う方法については、「[アクセス許可の評価方法](data-lake-storage-access-control-model.md#how-permissions-are-evaluated)」を参照してください。

### <a name="what-are-the-limits-for-azure-role-assignments-and-acl-entries"></a>Azure ロールの割り当てと ACL エントリにはどのような制限がありますか。

次の表に、Azure RBAC を使用して "粒度の粗い" アクセス許可 (ストレージ アカウントまたはコンテナーに適用されるアクセス許可) を管理し、ACL を使用して "粒度の細かい" アクセス許可 (ファイルとディレクトリに適用されるアクセス許可) を管理する際に考慮する制限の概要ビューを示します。 ACL 割り当て用のセキュリティ グループを使用します。 グループを使用すると、サブスクリプションごとのロール割り当ての最大数と、ファイルやディレクトリごとの ACL エントリの最大数を超える可能性が低くなります。 

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-rbac-acl-limits.md)] 

### <a name="does-data-lake-storage-gen2-support-inheritance-of-azure-rbac"></a>Data Lake Storage Gen2 は Azure RBAC の継承をサポートしていますか。

Azure ロールの割り当ては継承されます。 割り当ては、サブスクリプション、リソース グループ、ストレージ アカウント リソースからコンテナー リソースに渡されます。

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Data Lake Storage Gen2 は ACL の継承をサポートしていますか。

親ディレクトリの下に作成される新しい子サブディレクトリやファイルについては、既定の ACL を使用して ACL を設定できます。 既存の子項目の ACL を更新するには、目的のディレクトリ階層に対して ACL を再帰的に追加、更新、または削除する必要があります。 ガイダンスについては、この記事の「[ACL を設定する方法](#set-access-control-lists)」セクションを参照してください。 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>ディレクトリとその内容を再帰的に削除するのに必要なアクセス許可を教えてください

- 呼び出し元に 'スーパー ユーザー' アクセス許可がある

または

- 親ディレクトリには、書き込み + 実行アクセス許可が必要
- 削除対象のディレクトリとその中のすべてのディレクトリには、読み取り + 書き込み + 実行アクセス許可が必要

> [!NOTE]
> ディレクトリ内のファイルを削除するには、書き込みアクセス許可は必要ありません。 また、ルート ディレクトリ "/" を削除することはできません。

### <a name="who-is-the-owner-of-a-file-or-directory"></a>ファイルまたはディレクトリの所有者として設定されるのはだれですか

ファイルまたはディレクトリの作成者が所有者になります。 ルート ディレクトリの場合、これはコンテナーを作成したユーザーの ID です。

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>ファイルまたはディレクトリの作成時に、所有グループとして設定されるのはどのグループですか

所有グループは、新しいファイルまたはディレクトリが作成される親ディレクトリの所有グループからコピーされます。

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>ファイルの所有ユーザーですが、必要な RWX アクセス許可を持っていません。 どうすればよいですか。

所有ユーザーは、ファイルのアクセス許可を変更して、必要な任意の RWX アクセス許可を自分に与えることができます。

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>ACL に GUID が表示されることがあるのはなぜですか

エントリがユーザーを表し、そのユーザーがもう Azure AD に存在しなくなった場合に、GUID が表示されます。 通常、ユーザーが会社を辞めた場合や Azure AD でそのアカウントが削除された場合に、この現象が発生します。 さらに、サービス プリンシパルおよびセキュリティ グループには、それらを識別するためのユーザー プリンシパル名 (UPN) がないため、これらは自身の OID 属性 (guid) で表されます。

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>サービス プリンシパル用 ACL を正しく設定するにはどうすればよいですか。

サービス プリンシパル用 ACL を定義するときは、作成したアプリ登録に対応する "*サービス プリンシパル*" のオブジェクト ID (OID) を使用することが重要です。 登録済みアプリについては、特定の Azure AD テナントに個別のサービス プリンシパルがあることに注意してください。 登録済みアプリの OID は Azure portal に表示されていますが、その "*サービス プリンシパル*" には別の (異なる) OID があります。

アプリ登録に対応するサービス プリンシパルの OID を取得するには、`az ad sp show` コマンドを使用し、 パラメーターとしてアプリケーション ID を指定します。 アプリ ID が 18218b12-1895-43e9-ad80-6e8fc1ea88ce のアプリ登録に対応するサービス プリンシパルの OID を取得する例を次に示します。 Azure CLI で、次のコマンドを実行します。

```azurecli
az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
```

OID が表示されます。

サービス プリンシパルの正しい OID を取得したら、Storage Explorer で **[アクセスの管理]** ページに移動して OID を追加し、その OID に対する適切なアクセス許可を割り当てます。 その後、必ず **[保存]** を選択してください。

### <a name="can-i-set-the-acl-of-a-container"></a>コンテナーの ACL を設定できますか。

いいえ。 コンテナーに ACL がありません。 ただし、コンテナーのルート ディレクトリの ACL を設定できます。 すべてのコンテナーにはルート ディレクトリがあり、コンテナーと同じ名前を共有します。 たとえば、コンテナーに `my-container` という名前が付けられている場合、ルート ディレクトリの名前は `myContainer/` になります。 

Azure Storage REST API には [Set Container ACL](/rest/api/storageservices/set-container-acl)という操作が含まれていますが、この操作を使用してコンテナーの ACL またはコンテナーのルート ディレクトリを設定することはできません。 その代わりに、その操作は、コンテナー内の BLOB が[パブリックにアクセス可能かどうか](anonymous-read-access-configure.md)を示します。 

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

- 「[Azure Data Lake Storage Gen2 のアクセス制御モデル](data-lake-storage-access-control-model.md)」
