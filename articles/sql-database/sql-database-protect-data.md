---
title: "Azure SQL Database のデータ保護の概要 | Microsoft Docs"
description: "Azure SQL データベース内のデータの保護について説明します。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: secure and protect
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 12/21/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: f4712d70c0323e607ddcc021809f8097a621730d
ms.openlocfilehash: 67b32b224d86e8d0ed06d8248c3a2c5f5569c5cb


---
# <a name="protecting-data-within-your-sql-database"></a>SQL Database 内のデータの保護

SQL Database では、暗号化を使用してデータを保護します。  

## <a name="overview"></a>概要

SQL Database は、データを暗号化することでデータのセキュリティを保護します。移動中のデータには[トランスポート層セキュリティ](https://support.microsoft.com/en-us/kb/3135244)を使用し、保存データには [Transparent Data Encryption](http://go.microsoft.com/fwlink/?LinkId=526242) を使用し、使用中のデータには [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) を使用します。 SQL Database でのこれらのデータ保護機能の使用については、[データ保護とセキュリティ](sql-database-protect-data.md)に関するページを参照してください。

その他の方法でデータを暗号化するには、次を検討してください。

* [セルレベルの暗号化](https://msdn.microsoft.com/library/ms179331.aspx) により、暗号化キーが異なるデータの特定の列またはセルを暗号化できます。
* ハードウェア セキュリティ モジュールか、暗号化キー階層のサーバーの中央管理が必要な場合は、 [Azure VM で Azure Key Vault と SQL Server](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx)を併用することを検討してください。


SQL Database には、データのセキュリティを保護するために、監査および脅威検出機能が用意されています。 

### <a name="auditing"></a>監査
SQL Database Auditing は、データベース イベントを Azure Storage アカウントの監査ログに記録することによって、データベース アクティビティを追跡し、規制の遵守を維持できるようにします。 Auditing を使用すると、実行中のデータベース アクティビティを把握し、過去のアクティビティを分析および調査して、潜在的な脅威、不正使用の可能性、およびセキュリティ違反を特定することができます。 詳細については、「[SQL Database 監査の使用](sql-database-auditing-get-started.md)」を参照してください。  

### <a name="threat-detection"></a>脅威の検出
脅威の検出では、Azure SQL Database サービスに組み込まれたセキュリティ インテリジェンスの追加レイヤーを提供することにより、監査を補完します。 データベースの異常なアクティビティに関する学習、プロファイル、および検出が、常時実行されます。 不審なアクティビティ、潜在的な脆弱性、SQL インジェクション攻撃、および異常なデータベース アクセス パターンについては、アラートが送信されます。 提供された情報と具体的な手順に従って、アラートに対処することができます。 詳細については、「 [SQL Database 脅威の検出の概要](sql-database-threat-detection-get-started.md)」をご覧ください。  

## <a name="connection-security"></a>接続のセキュリティ
接続のセキュリティとは、ファイアウォール ルールと接続の暗号化を使用して、データベースへの接続を制限し、保護する方法のことです。

ファイアウォール ルールはサーバーとデータベースの両方で使用され、明示的にホワイト リストに登録されていない IP アドレスからの接続試行を拒否します。 新しいデータベースへの接続を試行するために、アプリケーションまたはクライアント コンピューターのパブリック IP アドレスを許可するには、まず Microsoft Azure クラシック ポータル、REST API、または PowerShell を使用して、サーバーレベルのファイアウォール ルールを作成する必要があります。 ベスト プラクティスとして、可能な限りサーバーのファイアウォールにより許可される IP アドレスの範囲を制限する必要があります。 詳細については、「 [Azure SQL Database ファイアウォール](https://msdn.microsoft.com/library/ee621782)」をご覧ください。

データがデータベースとの間で "送受信中" である間は、常に Azure SQL Database への接続をすべて (SSL/TLS を使用して) 暗号化する必要があります。 アプリケーションの接続文字列で、接続を暗号化するパラメーターを指定する必要があります。サーバーの証明書は、信頼 "*しないで*" ください。Azure クラシック ポータルから接続文字列をコピーすると、自動的にこのようになります。このようにしないと、サーバーの ID が確認されず、"man-in-the-middle" 攻撃を受けやすくなります。 たとえば ADO.NET ドライバーの場合、これらの接続文字列のパラメーターは、**Encrypt=True** と **TrustServerCertificate=False** です。 詳細については、[Azure SQL Database の接続の暗号化と証明書の検証](https://msdn.microsoft.com/library/azure/ff394108#encryption)に関するトピックをご覧ください。

## <a name="authentication"></a>認証
認証とは、データベースへの接続時に ID を証明する方法のことです。 SQL Database は、2 種類の認証をサポートしています。

* **SQL 認証**。ユーザー名とパスワードを使用します。
* **Azure Active Directory 認証**。Azure Active Directory で管理されている ID を使用します。管理および統合されたドメインの場合にサポートされます。

データベースの論理サーバーを作成したときに、ユーザー名とパスワードによる "サーバー管理" ログインを指定したとします。 これらの資格情報を使用すると、データベース所有者、つまり "dbo" として、そのサーバーにある任意のデータベースを認証できます。 Azure Active Directory 認証を使用する場合は、"Azure AD 管理者" という、Azure AD ユーザーとグループを管理できるサーバー管理者を別に作成する必要があります。 この管理者は、通常のサーバー管理者が実行できるすべての操作も実行できます。 Azure AD 管理者を作成して Azure Active Directory 認証を有効にする方法のチュートリアルについては、「 [Azure Active Directory の認証を使用して SQL Database に接続する](sql-database-aad-authentication.md) 」を参照してください。

ベスト プラクティスとしては、アプリケーションで別のアカウントを使用して認証することをお勧めします。この方法により、アプリケーションに付与されるアクセス許可を制限し、アプリケーション コードが SQL インジェクション攻撃に対して脆弱な場合に、悪意のあるアクティビティのリスクを軽減できます。 [包含データベース ユーザー](https://msdn.microsoft.com/library/ff929188)を作成する方法をお勧めします。この方法により、アプリがデータベースから直接認証を受けることができます。 サーバー管理者権限のログインでユーザー データベースに接続しているときに、次の T-SQL コマンドを実行することで、SQL 認証を使用する包含データベース ユーザーを作成できます。

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password'; -- SQL Authentication
```

Azure AD 管理者を作成した場合、Azure AD 管理者権限でユーザー データベースに接続しているときに、次の T-SQL コマンドを実行することで、Azure Active Directory 認証を使用する包含データベース ユーザーを作成できます。

```
CREATE USER [Azure_AD_principal_name | Azure_AD_group_display_name] FROM EXTERNAL PROVIDER; -- Azure Active Directory Authentication
```

いずれの場合でも、アプリケーションの接続文字列では、サーバー管理者のログインではなく、このユーザー資格情報を指定してデータベースに接続します。

SQL Database の認証の詳細については、 [Azure SQL Database におけるデータベースとログインの管理](sql-database-manage-logins.md)に関するページを参照してください。

## <a name="authorization"></a>承認
承認とは、Azure SQL Database で許可される操作を示すものであり、ユーザー アカウントのロールのメンバーシップとアクセス許可によって制御されます。 ベスト プラクティスとして、必要最低限の特権をユーザーに付与することをお勧めします。 Azure SQL Database では、T-SQL のロールを使用して承認を容易に管理できます。

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

接続しているサーバー管理者のアカウントは db_owner のメンバーであり、データベース内ですべての操作を実行する権限を持ちます。 スキーマのアップグレードやその他の管理操作をデプロイするために、このアカウントを保存します。 アクセス許可が限定された "ApplicationUser" アカウントを使用して、アプリケーションで必要な最小限の特権により、アプリケーションをデータベースに接続します。

ユーザーが Azure SQL Database で実行できる操作をさらに制限する方法がいくつかあります。

* db_datareader と db_datawriter 以外の[データベース ロール](https://msdn.microsoft.com/library/ms189121)を使用して、より権限の大きなアプリケーション ユーザー アカウント、またはより権限の小さな管理アカウントを作成できます。
* 詳細な [アクセス許可](https://msdn.microsoft.com/library/ms191291) により、個々の列、テーブル、ビュー、プロシージャ、その他のデータベース内のオブジェクトで実行できる操作を制御できます。
* [権限借用](https://msdn.microsoft.com/library/vstudio/bb669087)と[モジュール署名](https://msdn.microsoft.com/library/bb669102)を使用すると、安全にアクセス許可を一時的に昇格できます。
* [Row-Level Security](https://msdn.microsoft.com/library/dn765131) を使用すると、ユーザーがアクセスできる行を制限できます。
* [データのマスキング](sql-database-dynamic-data-masking-get-started.md) を使用すると、機密データの公開を制限できます。
* [ストアド プロシージャ](https://msdn.microsoft.com/library/ms190782) を使用すると、データベースで実行できるアクションを制限できます。

Azure クラシック ポータルまたは Azure リソース マネージャー API を使用したデータベースと論理サーバーの管理は、ポータル ユーザー アカウントのロールの割り当てによって制御されます。 このトピックの詳細については、「 [Azure ポータルでのロール ベースのアクセス制御](../active-directory/role-based-access-control-configure.md)」を参照してください。

## <a name="encryption"></a>暗号化
Azure SQL Database では、 [透過的なデータ暗号化](http://go.microsoft.com/fwlink/?LinkId=526242)を使用して、データが "静止" 状態のとき、またはデータベース ファイルやバックアップに格納されているときに、そのデータを暗号化することによりデータを保護できます。 データベースを暗号化するには、データベースの所有者として接続し、次のコマンドを実行します。

```
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

その他の方法で機密データを暗号化するには、次を検討してください。

* [セルレベルの暗号化](https://msdn.microsoft.com/library/ms179331.aspx) により、暗号化キーが異なるデータの特定の列またはセルを暗号化できます。
* ハードウェア セキュリティ モジュールか、暗号化キー階層のサーバーの中央管理が必要な場合は、 [Azure VM で Azure Key Vault と SQL Server](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx)を併用することを検討してください。
* [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) (プレビュー) は、暗号化をアプリケーションに対して透過的にし、クライアントが SQL Database と暗号化キーを共有することなくクライアント アプリケーション内で機密データを暗号化できるようにします。

## <a name="auditing"></a>監査
データベースの監査イベントと追跡イベントは、規制遵守の維持や、疑わしいアクティビティの特定に役立ちます。 SQL Database の監査により、Azure ストレージ アカウントの監査ログにデータベースのイベントを記録できます。 また SQL Database の監査を Microsoft Power BI と統合することにより、詳細なレポートと分析が容易になります。 詳細については、「 [SQL Database 監査の使用](sql-database-auditing-get-started.md)」を参照してください。

監査のほかに、SQL Database 脅威の検出機能によって、セキュリティを強化することもできます。 この機能を使用すると、異常なアクティビティに対してセキュリティ アラートを提供することで、発生した脅威に対応できます。 詳細については、「 [SQL Database 脅威の検出の概要](sql-database-threat-detection-get-started.md)」をご覧ください。  

## <a name="compliance"></a>コンプライアンス
アプリケーションがさまざまなセキュリティ コンプライアンスの要件を満たすのに役立つ上記の機能以外にも、Azure SQL Database は定期的な監査に参加し、さまざまなコンプライアンス基準に認定されています。 詳細については、「[Microsoft Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/)」をご覧ください。ここから最新の [SQL Database コンプライアンス証明書](https://azure.microsoft.com/support/trust-center/services/)の一覧を入手できます。


## <a name="next-steps"></a>次のステップ

- すべての SQL Database セキュリティ機能の概要については、[SQL セキュリティの概要](sql-database-security-overview.md)に関するページを参照してください。
- SQL Database でのアクセス制御機能の使用については、[アクセス制御](sql-database-control-access.md)に関するページを参照してください。
- プロアクティブな監視の詳細については、「[SQL Database 監査の使用](sql-database-auditing-get-started.md)」と「[SQL Database 脅威の検出の概要](sql-database-threat-detection-get-started.md)」を参照してください。



<!--HONumber=Dec16_HO4-->


