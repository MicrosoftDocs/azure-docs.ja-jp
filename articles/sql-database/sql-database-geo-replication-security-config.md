---
title: ディザスター リカバリーのセキュリティを構成する
description: データベースの復元やセカンダリ サーバーへのフェールオーバーを行った後の、セキュリティ構成やセキュリティ管理に関する考慮事項について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 12/18/2018
ms.openlocfilehash: 9d628583168883276e67d9e2f2fcafdce292769e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2019
ms.locfileid: "73807494"
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Azure SQL Database のセキュリティを geo リストアやフェールオーバー用に構成し、管理する

この記事では、[アクティブ geo レプリケーション](sql-database-active-geo-replication.md)と[自動フェールオーバー グループ](sql-database-auto-failover-group.md)を構成し制御するための認証要件について説明します。 セカンダリ データベースへのユーザー アクセスを設定するために必要な手順についても説明します。 最後に、[geo リストア](sql-database-recovery-using-backups.md#geo-restore)の使用後に復旧されたデータベースへのアクセスを有効にする方法についても説明します。 復旧オプションの詳細については、 [ビジネス継続性の概要](sql-database-business-continuity.md)に関する記事を参照してください。

## <a name="disaster-recovery-with-contained-users"></a>包含ユーザーによる障害復旧

従来のユーザーは master データベース内のログインにマップする必要がありましたが、包含ユーザーは、データベース自体で完全に管理されます。 これには 2 つ利点があります。 ディザスター リカバリーのシナリオでは、データベースがユーザーを管理するため、ユーザーは追加の構成なしで、新しいプライマリ データベースまたは geo リストアを使用して復旧されたデータベースに引き続き接続できます。 また、ログインの観点からは、この構成を使用することで、スケーラビリティとパフォーマンスを向上できる可能性があります。 詳細については、「 [包含データベース ユーザー - データベースの可搬性を確保する](https://msdn.microsoft.com/library/ff929188.aspx)」を参照してください。

主なトレードオフは、大規模なディザスター リカバリー プロセスの管理が困難になることです。 同じログインを使用するデータベースが複数ある場合、複数のデータベースで包含ユーザーを使用して資格情報を維持すると、包含ユーザーの利点が損なわれる場合があります。 たとえば、パスワード ローテーション ポリシーでは、マスター データベースで 1 回だけログイン用パスワードを変更するのではなく、複数のデータベースで一貫して変更を行う必要があります。 このような理由から、同じユーザー名とパスワードを使用するデータベースが複数ある場合、包含ユーザーの使用は推奨されません。

## <a name="how-to-configure-logins-and-users"></a>ログインとユーザーを構成する方法

包含ユーザーではなくログインとユーザーを使用している場合は、追加の手順を実行して、マスター データベースに同じログインが存在することを確認する必要があります。 次のセクションでは、関連する手順とその他の考慮事項について概要を説明します。

  >[!NOTE]
  > Azure Active Directory (AAD) ログインを使用してデータベースを管理することもできます。 詳細については、[Azure SQL のログインとユーザー](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)に関する記事を参照してください。

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>セカンダリ データベースまたは復旧されたデータベースへのユーザー アクセスの設定

セカンダリ データベースを読み取り専用のセカンダリ データベースとして使用できるようにし、新しいプライマリ データベースまたは geo リストアを使用して復旧されたデータベースへの適切なアクセスを保証するには、復旧の前に、ターゲット サーバーのマスター データベースに適切なセキュリティ構成を設定する必要があります。

各手順の詳細なアクセス許可については、後ほど詳しく説明します。

geo レプリケーション セカンダリに対するユーザー アクセスの準備は、geo レプリケーションの構成の一部として実行する必要があります。 geo リストアされたデータベースへのユーザー アクセスの準備は、元のサーバーがオンラインになっているときに実行する必要があります (例: 障害復旧訓練の一部として)。

> [!NOTE]
> ログイン アクセスが適切に構成されていないサーバーにフェールオーバーまたは geo リストアする場合は、サーバー管理者アカウントに制限されます。

ターゲット サーバーでのログインの設定には、以下に示す 3 つの手順が含まれます。

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1.プライマリ データベースにアクセスできるログインを特定する

このプロセスの最初の手順は、ターゲット サーバー上に複製するログインを特定することです。 そのために、ソース サーバーの論理 master データベースとプライマリ データベースでそれぞれ 1 回ずつ SELECT ステートメントを実行します。

サーバー管理者または **LoginManager** サーバー ロールのメンバーのみが、次の SELECT ステートメントを使用してソース サーバーのログインを特定できます。

    SELECT [name], [sid]
    FROM [sys].[sql_logins]
    WHERE [type_desc] = 'SQL_Login'

また、db_owner データベース ロールのメンバー、dbo ユーザー、またはサーバー管理者のみが、プライマリ データベース内にあるすべてのデータベース ユーザー プリンシパルを特定できます。

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2.手順 1 で特定したログインの SID を検索する

前のセクションで実行したクエリの出力を比較して SID を一致させることで、サーバー ログインをデータベース ユーザーに対応付けることができます。 一致する SID を持つデータベース ユーザーが対応付けられたログインは、そのデータベースのユーザー プリンシパルとしてデータベースに対するユーザー アクセス権が付与されます。

次のクエリを使用すると、データベース内のすべてのユーザー プリンシパルとその SID を表示できます。 このクエリを実行できるのは、db_owner データベース ロールのメンバーまたはサーバー管理者のみです。

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

> [!NOTE]
> **INFORMATION_SCHEMA** ユーザーと **sys** ユーザーの SID は *NULL* で、**ゲスト** SID は **0x00** です。 データベースの作成者が **DbManager** のメンバーではなくサーバー管理者だった場合、**dbo** SID は *0x01060000000001648000000000048454* で始まることがあります。

#### <a name="3-create-the-logins-on-the-target-server"></a>3.ターゲット サーバーへのログインを作成する

最後の手順では、1 つ以上のターゲット サーバーにアクセスし、適切な SID を使用してログインを生成します。 基本的な構文は次のとおりです。

    CREATE LOGIN [<login name>]
    WITH PASSWORD = <login password>,
    SID = <desired login SID>

> [!NOTE]
> セカンダリへのユーザー アクセスを許可し、プライマリへのアクセスは許可しない場合は、次の構文を使用してプライマリ サーバーでユーザー ログインを変更します。
>
> ```sql
> ALTER LOGIN <login name> DISABLE
> ```
>
> DISABLE を実行してもパスワードは変更されないため、必要な場合にいつでも有効にできます。

## <a name="next-steps"></a>次の手順

* データベースへのアクセスとログインの管理の詳細については、[SQL Database のセキュリティ:データベースのアクセスとログインのセキュリティの管理](sql-database-manage-logins.md)を参照してください。
* 包含データベース ユーザーの詳細については、「 [包含データベース ユーザー - データベースの可搬性を確保する](https://msdn.microsoft.com/library/ff929188.aspx)」を参照してください。
* アクティブ geo レプリケーションについては、[アクティブ geo レプリケーション](sql-database-active-geo-replication.md)に関するページを参照してください。
* 自動フェールオーバー グループについては、[自動フェールオーバー グループ](sql-database-auto-failover-group.md)に関するページを参照してください。
* geo リストアの使用方法については、[geo リストア](sql-database-recovery-using-backups.md#geo-restore)を参照してください。
