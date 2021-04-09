---
title: Data Lake Storage Gen1 のアクセス制御の概要 | Microsoft Docs
description: HDFS から派生した、Azure Data Lake Storage Gen1 のアクセス制御モデルの基本について説明します。
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: d16f8c09-c954-40d3-afab-c86ffa8c353d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: aa0da5721c577957b101ac8a2d9346c0536f0a88
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102424140"
---
# <a name="access-control-in-azure-data-lake-storage-gen1"></a>Azure Data Lake Store Gen1 のアクセス制御

Azure Data Lake Store Gen1 は、POSIX アクセス制御モデルから派生した HDFS からさらに派生したアクセス制御モデルを実装します。 この記事では、Data Lake Store Gen1 のアクセス制御モデルの基本について説明します。 

## <a name="access-control-lists-on-files-and-folders"></a>ファイルとフォルダーのアクセス制御リスト

アクセス制御リスト (ACL) には、**アクセス ACL** と **既定の ACL** の 2 種類があります。

* **アクセス ACL**: オブジェクトへのアクセスを制御します。 ファイルとフォルダーの両方にアクセス ACL があります。

* **既定の ACL**: フォルダーに関連付けられた ACL の "テンプレート" です。この ACL によって、そのフォルダーの下に作成されるすべての子項目のアクセス ACL が決まります。 ファイルには既定の ACL がありません。


アクセス ACL と既定の ACL は両方とも同じ構造です。

> [!NOTE]
> 親の既定の ACL を変更しても、既存の子項目のアクセス ACL または既定の ACL には影響しません。
>
>

## <a name="permissions"></a>アクセス許可

ファイル システム オブジェクトに対するアクセス許可は、**読み取り**、**書き込み**、**実行** であり、次の表に示すように、ファイルとフォルダーに対して使用できます。

|            |    ファイル     |   Folder |
|------------|-------------|----------|
| **読み取り (R)** | ファイルの内容を読み取ることができる | フォルダーの内容を一覧表示するには、**読み取り** と **実行** が必要です。|
| **書き込み (W)** | ファイルへの書き込みまたは追加を実行できる | フォルダーに子項目を作成するには、**書き込み** と **実行** が必要です。 |
| **実行 (X)** | Data Lake Store Gen1 のコンテキストでは、何も意味しない | フォルダーの子項目をスキャンするために必要です。 |

### <a name="short-forms-for-permissions"></a>アクセス許可の短い形式

**RWX** は、**読み取り + 書き込み + 実行** を示すために使用されます。 さらに縮約された数値形式もあります。**読み取り = 4**、**書き込み = 2**、**実行 = 1** で、その合計でアクセス許可を表します。 次は一部の例です。

| 数値形式 | 短縮形式 |      意味     |
|--------------|------------|------------------------|
| 7            | `RWX`        | 読み取り + 書き込み + 実行 |
| 5            | `R-X`        | 読み取り + 実行         |
| 4            | `R--`        | Read                   |
| 0            | `---`        | アクセス許可なし         |


### <a name="permissions-do-not-inherit"></a>アクセス許可が継承されない

Data Lake Store Gen1 で使用されている POSIX 形式のモデルでは、項目のアクセス許可は項目自体に格納されます。 つまり、項目のアクセス許可は親項目から継承できません。

## <a name="common-scenarios-related-to-permissions"></a>アクセス許可に関連する一般的なシナリオ

Data Lake Store Gen1 アカウントに対する特定の操作の実行に必要なアクセス許可について理解できるように、一般的なシナリオを次に示します。

| 操作 | Object              |    /      | Seattle/   | Portland/   | Data.txt       |
|-----------|---------------------|-----------|------------|-------------|----------------|
| Read      | Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| 追加 | Data.txt            |   `--X`   |   `--X`    |  `--X`      | `-W-`          |
| 削除    | Data.txt            |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| 作成    | Data.txt            |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| List      | /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| List      | /Seattle/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| List      | /Seattle/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |


> [!NOTE]
> 上の 2 つの条件が満たされていれば、ファイルを削除するために、ファイルに対する書き込みアクセス許可は必要ありません。
>
>


## <a name="users-and-identities"></a>ユーザーと ID

すべてのファイルとフォルダーは、以下の ID の個別のアクセス許可を持っています。

* 所有ユーザー
* 所有グループ
* 名前付きユーザー
* 名前付きグループ
* その他のすべてのユーザー

ユーザーとグループの ID は、Azure Active Directory (Azure AD) の ID です。 そのため、注記がない限り、Data Lake Store Gen1 のコンテキストでの "ユーザー" は、Azure AD ユーザーまたは Azure AD セキュリティ グループを意味します。

### <a name="the-super-user"></a>スーパー ユーザー

スーパー ユーザーは、Data Lake Storage Gen1 アカウント 内のすべてのユーザーの中で最高の権限を持ちます。 スーパー ユーザーには、次の特長があります。

* **すべて** のファイルとフォルダーに対して、RWX アクセス許可を持ちます。
* 任意のファイルまたはフォルダーのアクセス許可を変更できます。
* 任意のファイルまたはフォルダーの所有ユーザーまたは所有グループを変更できます。

Data Lake Storage Gen1 アカウントの **所有者** ロールに含まれるすべてのユーザーは、自動的にスーパーユーザーになります。

### <a name="the-owning-user"></a>所有ユーザー

項目を作成したユーザーは、自動的に項目の所有ユーザーになります。 所有ユーザーは、次のことができます。

* 所有しているファイルのアクセス許可を変更します。
* 所有しているファイルの所有グループを変更します。ただし、所有ユーザーが変更後のグループのメンバーでもある必要があります。

> [!NOTE]
> 所有ユーザーが、ファイルやフォルダーの所有ユーザーを変更することは "*できません*"。 ファイルまたはフォルダーの所有ユーザーを変更できるのは、スーパー ユーザーだけです。
>
>

### <a name="the-owning-group"></a>所有グループ

**バックグラウンド**

POSIX ACL では、すべてのユーザーが "プライマリ グループ" に関連付けられています。 たとえば、ユーザー "alice" が "finance" グループに属しているとします。 Alice は複数のグループに属している可能性がありますが、1 つのグループが常にプライマリ グループとして指定されています。 POSIX では、Alice がファイルを作成すると、そのファイルの所有グループが彼女のプライマリ グループに設定されます。ここでは、"finance" グループです。 その他の点では、所有グループの動作は、他のユーザー/グループに割り当てられているアクセス許可と同様です。

Data Lake Storage Gen1 でユーザーに "プライマリ グループ" が関連付けられていないため、所有グループは次のように割り当てられます。

**新しいファイルまたはフォルダーに対する所有グループの割り当て**

* **ケース 1**: ルート フォルダー "/"。 このフォルダーは、Data Lake Store Gen1 アカウントの作成時に作成されます。 この場合、所有グループはすべてゼロの GUID に設定されます。  この値では、どのようなアクセスも許可されません。  グループが割り当てられるまでのプレースホルダーです。
* **ケース 2** (その他すべての場合): 新しい項目が作成されると、所有グループが親フォルダーからコピーされます。

**所有グループの変更**

所有グループを変更できるユーザーは次のとおりです。
* すべてのスーパー ユーザー
* 所有ユーザー (所有ユーザーが変更後のグループのメンバーでもある場合)

> [!NOTE]
> 所有グループが、ファイルやフォルダーの ACL を変更することは "*できません*"。
>
> 2018 年 9 月以前に作成されたアカウントの場合、所有グループは上の **ケース 1** のルート フォルダーの場合にアカウントを作成したユーザーに設定されました。  所有グループを介してアクセス許可を提供する場合は単一のユーザー アカウントは有効でないため、この既定の設定ではアクセス許可は与えられません。 このアクセス許可は、有効なユーザー グループに対して割り当てることができます。


## <a name="access-check-algorithm"></a>アクセス確認アルゴリズム

次の擬似コードは、Data Lake Storage Gen1 アカウントのアクセス確認アルゴリズムを示しています。

```
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or folder
  # Note: the "sticky bit" is not illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

  # Handle the owning user. Note that mask IS NOT used.
  entry = get_acl_entry( path, OWNER )
  if (user == entry.identity)
      return ( (desired_perms & entry.permissions) == desired_perms )

  # Handle the named users. Note that mask IS used.
  entries = get_acl_entries( path, NAMED_USER )
  for entry in entries:
      if (user == entry.identity ) :
          mask = get_mask( path )
          return ( (desired_perms & entry.permmissions & mask) == desired_perms)

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

### <a name="the-mask"></a>マスク

アクセス確認アルゴリズムに示されているように、マスクによって、**名前付きユーザー**、**所有グループ**、および **名前付きグループ** のアクセスが制限されます。  

> [!NOTE]
> 新しい Data Lake Store Gen1 アカウントでは、ルート フォルダー ("/") のアクセス ACL のマスクが既定で RWX に設定されています。
>
>

### <a name="the-sticky-bit"></a>スティッキー ビット

スティッキー ビットは、POSIX ファイル システムのより高度な機能です。 Data Lake Storage Gen1 のコンテキストでは、スティッキー ビットが必要になることはあまりありません。 つまり、フォルダーでスティッキー ビットが有効になっている場合、子項目を削除または名前変更できるのは、子項目を所有しているユーザーのみです。

スティッキー ビットは、Azure Portal には表示されません。

## <a name="default-permissions-on-new-files-and-folders"></a>新しいファイルとフォルダーの既定のアクセス許可

既存のフォルダーの下に新しいファイルまたはフォルダーが作成されると、親フォルダーの既定の ACL によって、次のものが決定します。

- 子フォルダーの既定の ACL とアクセス ACL。
- 子ファイルのアクセス ACL (ファイルには既定の ACL がありません)。

### <a name="umask"></a>umask

ファイルまたはフォルダーを作成するときに、umask を使用して、子項目に既定の ACL がどのように設定されるかを変更します。 umask は親フォルダーの 9 ビットの値であり、**所有ユーザー**、**所有グループ**、**その他** に対する RWX 値が含まれています。

Azure Data Lake Storage Gen1 に対する umask は、007 に設定される定数値です。 この値の変換値

| umask コンポーネント     | 数値形式 | 短縮形式 | 意味 |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | 所有ユーザーの場合、親の既定 ACL を子の Access ACL にコピーします | 
| umask.owning_group  |    0         |   `---`      | 所有グループの場合、親の既定 ACL を子の Access ACL にコピーします | 
| umask.other         |    7         |   `RWX`      | その他の場合、子の Access ACL 上のすべてのアクセス許可を削除します |

Azure Data Lake Storage Gen1 で umask の値が使用されると、実際上は、既定の ACL が何を示しているかに関係なく、既定では、other に対する値は、新しい子では決して送信されないことを意味します。 

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

## <a name="common-questions-about-acls-in-data-lake-storage-gen1"></a>Data Lake Storage Gen1 の ACL に関する一般的な質問

### <a name="do-i-have-to-enable-support-for-acls"></a>ACL のサポートを有効にする必要はありますか

いいえ。 ACL によるアクセス制御は、Data Lake Storage Gen1 アカウントでは常に有効になっています。

### <a name="which-permissions-are-required-to-recursively-delete-a-folder-and-its-contents"></a>フォルダーとその内容を再帰的に削除するのに必要なアクセス許可を教えてください

* 親フォルダーには、**書き込み + 実行** アクセス許可が必要です。
* 削除対象のフォルダーとその中のすべてのフォルダーには、**読み取り + 書き込み + 実行** アクセス許可が必要です。

> [!NOTE]
> フォルダー内のファイルを削除するには、書き込みアクセス許可は必要ありません。 また、ルート フォルダー "/" を削除することは **できません**。
>
>

### <a name="who-is-the-owner-of-a-file-or-folder"></a>ファイルまたはフォルダーの所有者として設定されるのはだれですか

ファイルまたはフォルダーの作成者が所有者になります。

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-folder-at-creation"></a>ファイルまたはフォルダーの作成時に、所有グループとして設定されるのはどのグループですか

所有グループは、新しいファイルまたはフォルダーが作成される親フォルダーの所有グループからコピーされます。

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>ファイルの所有ユーザーですが、必要な RWX アクセス許可を持っていません。 どうすればよいですか。

所有ユーザーは、ファイルのアクセス許可を変更して、必要な任意の RWX アクセス許可を自分に与えることができます。

### <a name="when-i-look-at-acls-in-the-azure-portal-i-see-user-names-but-through-apis-i-see-guids-why-is-that"></a>Azure Portal で ACL を確認するとユーザー名が表示されますが、API では GUID が表示されるのはなぜですか

ACL のエントリは、ユーザーに対応する GUID として Azure AD に格納されます。 API では GUID がそのまま返されます。 Azure Portal では、可能であれば GUID をわかりやすい名前に変換することで、ACL をより使いやすくします。

### <a name="why-do-i-sometimes-see-guids-in-the-acls-when-im-using-the-azure-portal"></a>Azure Portal の使用時に ACL に GUID が表示されることがある理由を教えてください

GUID が表示されるのは、そのユーザーがもう Azure AD に存在しない場合です。 通常、ユーザーが会社を辞めた場合や Azure AD でそのアカウントが削除された場合に、この現象が発生します。 また、適切な ID を使用して ACL を設定していることを確認してください (以下で詳しく説明します)。

### <a name="when-using-service-principal-what-id-should-i-use-to-set-acls"></a>サービス プリンシパルを使用する場合、ACL を設定するには、どのような ID を使用する必要がありますか。

Azure portal 内で、 **[Azure Active Directory] > [エンタープライズ アプリケーション]** の順に選択し、アプリケーションを選択します。 **[概要]** タブにオブジェクト ID が表示されます。これは、(アプリケーション ID ではなく) データ アクセスの ACL を追加するときに使用するものです。

### <a name="does-data-lake-storage-gen1-support-inheritance-of-acls"></a>Data Lake Storage Gen1 は ACL の継承をサポートしていますか。

いいえ。ただし、親フォルダーに新たに作成される子ファイルや子フォルダーについては、既定の ACL を使用して ACL を設定できます。

### <a name="what-are-the-limits-for-acl-entries-on-files-and-folders"></a>ACL エントリでは、ファイルとフォルダーにどのような上限がありますか?

ファイルあたり、かつ、ディレクトリあたり 32 ACL を設定できます。 アクセス ACL と既定の ACL それぞれに、独自の 32 個の ACL エントリの制限があります。 可能であれば、ACL 割り当て用にセキュリティ グループを使用します。 グループを使用すると、ファイルやディレクトリごとの ACL エントリの最大数を超える可能性が低くなります。

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

* [Azure Data Lake Storage Gen1 の概要](data-lake-store-overview.md)
