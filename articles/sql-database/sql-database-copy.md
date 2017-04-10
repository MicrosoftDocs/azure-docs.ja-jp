---
title: "Azure SQL Database のコピーの作成 | Microsoft Docs"
description: "Azure SQL Database のコピーの作成"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 5aaf6bcd-3839-49b5-8c77-cbdf786e359b
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 10/24/2016
ms.author: sashan
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: e64abfd5581c02b609707f7fa712962c024b293b
ms.lasthandoff: 03/23/2017


---
# <a name="copy-an-azure-sql-database"></a>Azure SQL Database のコピー

同じサーバーまたは別のサーバーのいずれかで、データベースのコピーを作成できます。 データベースのコピーとは、コピーが要求された時点でのソース データベースのスナップショットのことです。 データベース コピーのサービス レベルとパフォーマンス レベル (価格レベル) は、既定でコピー元のデータベースと同じになります。 API を使用している場合は、同じサービス レベル (エディション) 内のさまざまなパフォーマンス レベルを選択できます。 コピーの完了後、コピーは完全に機能する独立したデータベースになります。 この時点では、任意のエディションにアップグレードまたはダウン グレードできます。 ログイン、ユーザー、アクセス許可は非依存で管理できます。  

データベースを同じ論理サーバーにコピーすると、両方のデータベースで同じログインを利用できます。 データベースをコピーするために使用するセキュリティ プリンシパルが、新しいデータベースのデータベース所有者 (DBO) になります。 すべてのデータベース ユーザー、アクセス許可、セキュリティ識別子 (SID) がデータベースのコピーにコピーされます。  

別の論理サーバーにデータベースをコピーする場合は、新しいサーバーのセキュリティ プリンシパルが新しいデータベースのデータベース所有者になります。 データのアクセスに[包含データベース ユーザー](sql-database-manage-logins.md)を使用する場合は、プライマリとセカンダリの両方のデータベースが常に同じユーザー資格情報を持つようにします。そうすることで、コピーの完了後すぐに同じ資格情報でアクセスできるようになります。 [Azure Active Directory](../active-directory/active-directory-whatis.md) を使用すると、コピーで資格情報を管理する必要がなくなります。 ただし、新しいサーバーにデータベースをコピーする場合、ログインに基づくアクセスは動作しないことがあります。これは、そのログインが新しいサーバーに存在しないためです。 別の論理サーバーにデータベースをコピーする場合のログイン管理の詳細については、「[障害復旧後にセキュリティを管理する方法](sql-database-geo-replication-security-config.md)」をご覧ください。 

[Azure Portal](sql-database-copy-portal.md)、[PowerShell](scripts/sql-database-copy-database-to-new-server-powershell.md)、 または [T-SQL](sql-database-copy-transact-sql.md) を使用して SQL データベースをコピーすることができます。 

## <a name="next-steps"></a>次のステップ

* 別の論理サーバーにデータベースをコピーする場合のユーザーおよびログインの管理の詳細については、「 [障害復旧後にセキュリティを管理する方法](sql-database-geo-replication-security-config.md) 」をご覧ください。
* 詳細については、[ログインの管理](sql-database-manage-logins.md)に関するページをご覧ください。
* データベースをエクスポートするには、[BACPAC へのデータベースのエクスポート](sql-database-export.md)に関するページをご覧ください。

