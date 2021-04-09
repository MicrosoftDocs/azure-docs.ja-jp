---
title: Azure Data Catalog へのアクセスをセキュリティで保護する方法
description: この記事では、Azure Data Catalog でデータ カタログとそのデータ資産をセキュリティで保護する方法について説明します。
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 537254d7fb153e182be8ece13675a357a9a3e24e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674820"
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>データ カタログとデータ資産へのアクセスをセキュリティで保護する方法

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

> [!IMPORTANT]
> この機能は、Azure Data Catalog の Standard Edition でのみ使用できます。

Azure Data Catalog では、データ カタログにアクセスできるユーザーと、そのユーザーがカタログのメタデータで実行できる操作 (登録する、注釈を付ける、所有権を取得する) を指定できます。 

## <a name="catalog-users-and-permissions"></a>カタログのユーザーとアクセス許可

ユーザーまたはグループにデータ カタログへのアクセスを提供し、アクセス許可を設定するには、次のようにします。

1. [データ カタログのホーム ページ](https://www.azuredatacatalog.com)のツールバーで、**[設定]** をクリックします。

   ![Azure Data Catalog ホーム ページの [設定] ボタン](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)

2. [設定] ページで、**[カタログ ユーザー]** セクションを展開します。

   ![Azure Data Catalog ユーザーの [追加] ボタン](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)

3. **[追加]** をクリックします。

4. 完全修飾 **ユーザー名** または **セキュリティ グループ** の名前を、カタログに関連付けられている Azure Active Directory (AAD) に入力します。 複数のユーザーまたはグループを追加する場合は、区切り記号としてカンマ (`,’) を使用します。

   ![Azure Data Catalog ユーザー - ユーザーまたはグループ](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)

5. テキスト ボックス外で **Enter** キーまたは **Tab** キーを押します。 

6. すべてのアクセス許可 (**[注釈]**、**[登録]**、および **[所有権の取得]**) がこれらのユーザーまたはグループに既定で割り当てられていることを確認します。 これはつまり、ユーザーまたはグループが[データ資産を登録する]( data-catalog-how-to-register.md)、[データ資産に注釈を付ける]( data-catalog-how-to-annotate.md)、および[データ資産の所有権を取得する]( data-catalog-how-to-manage.md)ことができることを意味します。 

   ![Azure Data Catalog ユーザー - 既定のアクセス許可](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)

7. ユーザーまたはグループにカタログに対する読み取りアクセスのみを与えるには、そのユーザーまたはグループの **[注釈]** オプションをオフにします。 オフにすると、ユーザーおよびグループはカタログ内のデータ資産に注釈を付けられなくなりますが、表示は可能です。 

8. ユーザーまたはグループによるデータ資産の登録を拒否するには、そのユーザーまたはグループの **[登録]** オプションをオフにします。

9. ユーザーがデータ資産の所有権を取得することを拒否するには、そのユーザーまたはグループの **[所有権の取得]** オプションをオフにします。 

10. ユーザーまたはグループをカタログ ユーザーから削除するには、一覧の下部でそのユーザーまたはグループの **x** をクリックします。 

   ![Azure Data Catalog カタログ ユーザー - ユーザーの削除の [X] アイコン](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

   > [!IMPORTANT]
   > ユーザーを直接追加してアクセス許可を割り当てるより、カタログ ユーザーにセキュリティ グループを追加することをお勧めします。 その後、役割やカタログに対する必要なアクセスが一致するセキュリティ グループにユーザーを追加します。

## <a name="special-considerations"></a>特別な考慮事項

- セキュリティ グループに割り当てられるアクセス許可は付加的な要素です。 2 つのグループに 1 人のユーザーがいるとします。 一方のグループには注釈を付けるアクセス許可がある一方で、もう一方のグループにはありません。 その場合、ユーザーには注釈を付けるアクセス許可が付与されます。 
- ユーザーに明示的に割り当てられたアクセス許可は、そのユーザーが属するグループに割り当てられているアクセス許可をオーバーライドします。 前の例で、ユーザーを明示的にカタログ ユーザーに追加し、注釈を付けるアクセス許可を割り当てていないとします。 そのユーザーは注釈を付けるアクセス許可が付与されているグループのメンバーであっても、データ資産に注釈を付けることができません。

## <a name="next-steps"></a>次の手順

- [Azure Data Catalog の概要](data-catalog-get-started.md)
