---
title: Azure SQL Data Warehouse の列レベルのセキュリティ | Microsoft Docs
description: 列レベルのセキュリティ (CLS) では、ユーザーの実行コンテキストまたはグループ メンバーシップに基づいて、データベース テーブルの列へのアクセスを制御できます。 CLS により、アプリケーションでのセキュリティの設計とコーディングが簡略化されます。 CLS を使用すると、列のアクセスに対して制限を実装できます。
services: sql-data-warehouse
author: kavithajonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: kavithaj
ms.reviewer: igorstan, carlrab
ms.openlocfilehash: aa91bd586e064239d0e05c754427947963c9ee3a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "61082812"
---
# <a name="column-level-security"></a>列レベルのセキュリティ
列レベルのセキュリティ (CLS) では、ユーザーの実行コンテキストまたはグループ メンバーシップに基づいて、データベース テーブルの列へのアクセスを制御できます。

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]

CLS により、アプリケーションでのセキュリティの設計とコーディングが簡略化されます。 CLS を使用すると、機密データを保護するために、列のアクセスに対して制限を実装できます。 たとえば、特定のユーザーが、所属する部門に関連するテーブルの一定の列にのみアクセスできるようにします。 アクセス制限のロジックは、別のアプリケーション層のデータから離れた場所ではなく、データベース層に配置されています。 任意の階層からデータ アクセスが試行されるたびに、データベースによってアクセス制限が適用されます。 この制限により、セキュリティ システム全体の外部からのアクセスが減り、そのシステムの信頼性と堅牢性が向上します。 さらに、CLS は、列を除外してユーザーにアクセス制限を課すためのビューの導入も不要にします。

CLS は [GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) T-SQL ステートメントで実装できます。 このメカニズムでは、SQL と Azure Active Directory (AAD) 認証がどちらもサポートされます。

![CLS](./media/column-level-security/cls.png)

## <a name="syntax"></a>構文

```sql
GRANT <permission> [ ,...n ] ON
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]
    TO <database_principal> [ ,...n ]
    [ WITH GRANT OPTION ]
    [ AS <database_principal> ]
<permission> ::=
    SELECT
  | UPDATE
<database_principal> ::=
      Database_user
    | Database_role
    | Database_user_mapped_to_Windows_User
    | Database_user_mapped_to_Windows_Group
```

## <a name="example"></a>例
次の例では、"TestUser" が "Membership" テーブルの "SSN" 列にアクセスするのを制限する方法を示します。

社会保障番号を格納するために使用される SSN 列が含まれた "Membership" テーブルを作成します。

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

機密データが含まれている SSN 列を除くすべての列に、"TestUser" がアクセスできるようにします。

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

"TestUser" として実行されたクエリは、SSN 列が含まれている場合に失敗します。

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>ユース ケース
今日使用されている CLS のユース ケースをいくつか紹介します。
- 金融サービス会社は、アカウント マネージャーだけが、顧客の社会保障番号 (SSN) や電話番号などの個人を特定できる情報 (PII) にアクセスできるようにします。
- 医療機関は、医師と看護師だけが機密の医療記録にアクセスできるようにする一方で、請求担当部門のメンバーがそのようなデータを表示できないようにします。
