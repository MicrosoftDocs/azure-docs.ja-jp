---
title: "管理者パスワードのリセットなどの管理者タスクの実行方法 | Microsoft Docs"
description: "SQL Database で一般的な管理者タスクを実行する方法について説明します。 たとえば、管理者パスワードのリセット、アクセスの許可と削除などです。"
services: sql-database
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
keywords: "管理者パスワードのリセット"
ms.assetid: 9803fdcf-6501-4ac4-8cd0-f80071e052e1
ms.service: sql-database
ms.custom: troubleshoot
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2016
ms.author: v-shysun
translationtype: Human Translation
ms.sourcegitcommit: e5b5751facb68ae4a62e3071fe4dfefc02434a9f
ms.openlocfilehash: 5b717afe388d9ad6d4d37fa07d351e55ac8dbbb8


---
# <a name="how-to-perform-common-administrative-tasks-such-as-resetting-admin-password-in-azure-sql-database"></a>Azure SQL Database で管理者パスワードのリセットなどの一般的な管理タスクを実行する方法
Azure SQL Database へのアクセスの付与および削除を行う簡単な手順については、このトピックをご覧ください。 より包括的な情報については、次を参照してください。

* [Azure SQL Database におけるデータベースとログインの管理](sql-database-manage-logins.md)
* [SQL Database の保護](sql-database-security-overview.md)
* [SQL Server Database エンジンと Azure SQL Database のセキュリティ センター](https://msdn.microsoft.com/library/bb510589)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="to-reset-admin-password-for-a-logical-server"></a>論理サーバーの管理者パスワードをリセットするには
* [Azure Portal](https://portal.azure.com) で **[SQL Server]** をクリックし、一覧からサーバーを選択して、**[パスワードのリセット]** をクリックします。

## <a name="to-help-make-sure-only-authorized-ip-addresses-are-allowed-to-access-the-server"></a>権限のある IP アドレスのみがサーバーへのアクセスを許可されるようにするには
* 「 [方法: ファイアウォール設定を構成する (SQL Database)](sql-database-configure-firewall-settings.md)」を参照してください。

## <a name="to-create-contained-database-users-in-the-user-database"></a>ユーザー データベースに包含データベース ユーザーを作成するには
* [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) ステートメントを使用し、「[包含データベース ユーザー - データベースの可搬性を確保する](https://msdn.microsoft.com/library/ff929188.aspx)」をご覧ください。

## <a name="to-authenticate-contained-database-users-by-using-your-azure-active-directory"></a>Azure Active Directory を使用して包含データベース ユーザーを認証するには
* 「 [Azure Active Directory 認証を使用して SQL Database または SQL Data Warehouse に接続する](sql-database-aad-authentication.md)」をご覧ください。

## <a name="to-create-additional-logins-for-high-privileged-users-in-the-virtual-master-database"></a>仮想マスター データベースで高い特権を持つユーザーの追加ログインを作成するには
* [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx) ステートメントを使用し、[Azure SQL Database でのデータベースとログインの管理](sql-database-manage-logins.md)に関するページのログイン管理セクションで詳細を確認してください。




<!--HONumber=Dec16_HO4-->


