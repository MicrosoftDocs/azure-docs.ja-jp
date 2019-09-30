---
title: Azure SQL Database 機能の制限 | Microsoft Docs
description: Azure SQL Database 機能の制限により、攻撃者がデータベース内の情報にアクセスできるようにするデータベースの機能を制限することで、データベースのセキュリティを向上します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 03/22/2019
ms.openlocfilehash: f2fd6cb73428c69fbb27cb93377f851a4e06221d
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70959133"
---
# <a name="azure-sql-database-feature-restrictions"></a>Azure SQL Database 機能の制限

SQL Server の攻撃の一般的な原因の 1 つは、データベースにアクセスする Web アプリケーションによるものです。そこでは、データベースに関する情報を収集するために、さまざまな形式の SQL インジェクション攻撃が使われます。  理想的には、SQL インジェクションを許可しないようにアプリケーション コードを開発します。  しかし、レガシおよび外部のコードを含む大規模なコードベースでは、すべてのケースが対処されているかわからないため、SQL インジェクションを現実のものとして、防ぐ必要があります。  機能の制限の目標は、一部の形式の SQL インジェクションで、SQL インジェクションが成功した場合でも、データベースに関する情報の漏えいを防ぐことです。

## <a name="enabling-feature-restrictions"></a>機能の制限の有効化

機能の制限の有効化は、次のように`sp_add_feature_restriction`ストアド プロシージャを使用して行います。

```sql
EXEC sp_add_feature_restriction <feature>, <object_class>, <object_name>
```

次の機能を制限できます。

| 機能          | 説明 |
|------------------|-------------|
| N'ErrorMessages' | 制限すると、エラー メッセージ内のすべてのユーザー データがマスクされます。 「[エラー メッセージ機能の制限](#error-messages-feature-restriction) 」を参照してください|
| N'Waitfor'       | 制限すると、コマンドは遅延なく、すぐに返されます。 「[WAITFOR 機能の制限](#waitfor-feature-restriction)」を参照してください |

`object_class` の値は、`object_name` がデータベース内のユーザー名であるか、またはロール名であるかを示す `N'User'` または `N'Role'` のいずれかになります。

次の例では、ユーザー `MyUser` のすべてのエラー メッセージがマスクされます。

```sql
EXEC sp_add_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="disabling-feature-restrictions"></a>機能の制限の無効化

機能の制限の無効化は、次のように `sp_drop_feature_restriction` ストアド プロシージャを使用して行います。

```sql
EXEC sp_drop_feature_restriction <feature>, <object_class>, <object_name>
```

次の例では、ユーザー `MyUser` のエラー メッセージのマスクを無効にしています。

```sql
EXEC sp_drop_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="viewing-feature-restrictions"></a>機能の制限の表示

`sys.sql_feature_restrictions` ビューには、データベースに対して現在定義されているすべての機能の制限が表示されます。 次の列があります。

| 列名 | データ型 | 説明 |
|-------------|-----------|-------------|
| class       | nvarchar(128) | 制限が適用されるオブジェクトのクラス |
| object      | nvarchar(256) | 制限が適用されるオブジェクトの名前 |
| feature     | nvarchar(128) | 制限されている機能 |

## <a name="feature-restrictions"></a>機能の制限

### <a name="error-messages-feature-restriction"></a>エラー メッセージ機能の制限

一般的な SQL インジェクション攻撃方法の 1 つは、エラーを発生させるコードを挿入することです。  エラー メッセージを調査することによって、攻撃者はシステムに関する情報を学習して、さらに対象を絞った攻撃を追加できるようになる可能性があります。  この攻撃は、アプリケーションでクエリの結果が表示されず、エラー メッセージが表示される場合に特に有効な場合があります。

次の形式の要求を含む Web アプリケーションがあるとします。

```html
http://www.contoso.com/employee.php?id=1
```

これは、次のようなデータベース クエリを実行します。

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

`id` パラメーターとして、Web アプリケーション要求に渡された値がコピーされて、データベース クエリ内の $EmpId が置換される場合、攻撃者は、次の要求を作成できる可能性があります。

```html
http://www.contoso.com/employee.php?id=1 AND CAST(DB_NAME() AS INT)=0
```

さらに次のエラーが返されるため、攻撃者がデータベースの名前を知ることができる場合があります。

```sql
Conversion failed when converting the nvarchar value 'HR_Data' to data type int.
```

データベースでアプリケーション ユーザーのエラー メッセージ機能の制限を有効にすると、返されるエラー メッセージがマスクされるため、データベースに関する内部情報が漏えいしません。

```sql
Conversion failed when converting the ****** value '******' to data type ******.
```

同様に、攻撃者は次の要求を作成できる可能性があります。

```html
http://www.contoso.com/employee.php?id=1 AND CAST(Salary AS TINYINT)=0
```

さらに次のエラーが返されるため、攻撃者が従業員の給与を知ることができる場合があります。

```sql
Arithmetic overflow error for data type tinyint, value = 140000.
```

エラー メッセージ機能の制限を使用すると、データベースから次が返されます。

```sql
Arithmetic overflow error for data type ******, value = ******.
```

### <a name="waitfor-feature-restriction"></a>WAITFOR 機能の制限

ブラインド SQL インジェクションでは、アプリケーションが、挿入された SQL の結果とエラー メッセージを攻撃者に提供することはありませんが、攻撃者が、実行にかかる時間が異なる 2 つの条件付き分岐を含む条件付きクエリを構築することによって、データベースから情報を推論できます。 応答時間を比較することによって、攻撃者はどちらの分岐が実行されたかを知ることができ、それによってシステムに関する情報を知ることができます。 この攻撃の最もシンプルなバリエーションは、遅延を導入する `WAITFOR` ステートメントを使用することです。

次の形式の要求を含む Web アプリケーションがあるとします。

```html
http://www.contoso.com/employee.php?id=1
```

これは、次のようなデータベース クエリを実行します。

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Web アプリケーション要求に ID パラメーターとして渡される値がコピーされ、データベース クエリ内の $EmpId を置換する場合、攻撃者は、次の要求を行うことができます。

```html
http://www.contoso.com/employee.php?id=1; IF SYSTEM_USER='sa' WAITFOR DELAY '00:00:05'
```

`sa` アカウントが使用されていた場合、クエリは追加で 5 秒かかることがあります。 `WAITFOR` 機能制限が、データベースで無効にされている場合、`WAITFOR` ステートメントが無視され、この攻撃を使用して情報が漏えいされません。