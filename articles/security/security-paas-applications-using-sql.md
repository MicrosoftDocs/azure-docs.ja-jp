---
title: "SQL Database と SQL Data Warehouse を使用して PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティで保護する | Microsoft Docs"
description: " PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティ保護するための、Azure SQL Database と SQL Data Warehouse のセキュリティ ベスト プラクティスについて説明します。 "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: be00c1427d57b96506ec8b0ac881b7c1bd09e4de
ms.lasthandoff: 03/22/2017


---
# <a name="securing-paas-web-and-mobile-applications-using-sql-database-and-sql-data-warehouse"></a>SQL Database と SQL Data Warehouse を使用して PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティで保護する

この記事では、PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティ保護するための、[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) と [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/) の一連のセキュリティ ベスト プラクティスについて説明します。 このベスト プラクティスは、Azure に関して Microsoft が蓄積してきたノウハウと、ユーザーの皆様の経験に基づいています。

## <a name="azure-sql-database-and-sql-data-warehouse"></a>Azure SQL Database と SQL Data Warehouse
[Azure SQL Database](../sql-database/sql-database-technical-overview.md) と [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) は、インターネット ベースのアプリケーション用のリレーショナル データベース サービスを提供します。 PaaS デプロイで Azure SQL Database と SQL Data Warehouse を使用するときに、アプリケーションとデータの保護に役立つサービスを見ていきましょう。

- Azure Active Directory 認証 (SQL Server 認証ではない)
- Azure SQL ファイアウォール
- 透過的なデータ暗号化 (TDE)

## <a name="best-practices"></a>ベスト プラクティス

### <a name="use-a-centralized-identity-repository-for-authentication-and-authorization"></a>認証と承認に一元化された ID レポジトリを使用する

Azure SQL Database は、次の 2 種類の認証のいずれかを使用するように構成できます。

- **SQL 認証**では、ユーザー名とパスワードを使用します。 データベースの論理サーバーを作成したときに、ユーザー名とパスワードによる "サーバー管理" ログインを指定したとします。 これらの資格情報を使用すると、データベース所有者として、そのサーバーにある任意のデータベースを認証できます。

- **Azure Active Directory 認証**では、Azure Active Directory で管理されている ID を使用し、管理、統合されたドメインをサポートしています。 Azure Active Directory 認証を使用するには、"Azure AD 管理者" という、Azure AD ユーザーとグループを管理できるサーバー管理者を別に作成する必要があります。 この管理者は、通常のサーバー管理者が実行できるすべての操作も実行できます。

[Azure Active Directory 認証](../active-directory/develop/active-directory-authentication-scenarios.md)は、Azure Active Directory (AD) の ID を使用して Azure SQL Database と SQL Data Warehouse に接続するメカニズムです。 Azure AD では、SQL Server 認証とは別の認証が提供されるため、データベース サーバー間でユーザー ID が拡散されるのを防ぐことができます。 Azure AD 認証を使用すると、データベース ユーザーの ID や他の Microsoft サービスを一元管理できます。 ID の一元管理では、1 か所でデータベース ユーザーを管理できるようになるため、アクセス許可の管理が容易になります。  

SQL 認証ではなく Azure AD 認証を使用する利点は次のとおりです。

- 1 か所でのパスワードのローテーションを許可します。
- 外部の Azure AD グループを使用してデータベースのアクセス許可を管理できます。
- 統合 Windows 認証や、Azure AD でサポートされる他の認証形式を有効にすると、パスワードが保存されません。
- 包含データベース ユーザーを使用して、データベース レベルで ID を認証します。
- SQL Database に接続するアプリケーション向けにトークンベース認証をサポートしています。
- ADFS (ドメイン フェデレーション) またはドメインを同期しないローカル Azure AD のネイティブ ユーザー/パスワード認証をサポートします。
- [Multi-Factor Authentication (MFA)](../multi-factor-authentication/multi-factor-authentication.md) を含む Active Directory ユニバーサル認証を使用する SQL Server Management Studio からの接続をサポートします。 MFA には、電話、テキスト メッセージ、スマート カードと暗証番号 (PIN)、モバイル アプリ通知など、簡単な各種確認オプションによる強力な認証が含まれます。 詳細については、「 [SQL Database と SQL Data Warehouse での Azure AD MFA のための SSMS のサポート](../sql-database/sql-database-ssms-mfa-authentication.md)」を参照してください。

Azure AD 認証の詳細については、次を参照してください。

- [Azure Active Directory 認証を使用して SQL Database または SQL Data Warehouse に接続する](../sql-database/sql-database-aad-authentication.md)
- [Azure SQL Data Warehouse への認証](../sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Azure AD 認証を使用して Azure SQL DB のトークンベース認証をサポート](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/)

> [!NOTE]
> Azure Active Directory を環境に確実に適合させるには、「[Azure AD の機能と制限事項](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations)」の、特に「追加の考慮事項」を参照してください。
>
>

### <a name="restrict-access-based-on-ip-address"></a>IP アドレスに基づいてアクセスを制限する
受け入れ可能な IP アドレスの範囲を指定するファイアウォール規則を作成できます。 これらの規則は、サーバー レベルとデータベース レベルの両方を対象にすることができます。 ただし、セキュリティを強化しデータベースの移植性を高めるため、可能な限りデータベース レベルのファイアウォール規則を使用することをお勧めします。 アクセス要件が同じデータベースが多数存在し、それぞれのデータベースの設定に時間を費やしたくない場合は、管理者向けのサーバー レベルのファイアウォール規則を使用します。

SQL Database の既定のソース IP アドレスの制限では、どの Azure アドレス (他のサブスクリプションやテナントを含む) からのアクセスも許可されます。 これを、自分の IP アドレスのみがインスタンスへのアクセスを許可されるように制限することができます。 SQL ファイアウォールと IP アドレス制限を使用していても、強力な認証が必要です。 この記事で前述した推奨事項を参照してください。

Azure SQL ファイアウォールと IP の制限については、次を参照してください。

- [Azure SQL Database のアクセス制御](../sql-database/sql-database-control-access.md)
- [Azure SQL Database ファイアウォール規則の構成 - 概要](../sql-database/sql-database-firewall-configure.md)
- [Azure Portal を使用して Azure SQL Database のサーバー レベルのファイアウォール規則を作成する](../sql-database/sql-database-configure-firewall-settings.md)

### <a name="encryption-of-data-at-rest"></a>保存データの暗号化
[Transparent Data Encryption (TDE)](https://msdn.microsoft.com/library/azure/bb934049) は、SQL Server、Azure SQL Database、Azure SQL Data Warehouse などのデータ ファイルを暗号化します。これは、保存データの暗号化として知られています。 セキュリティで保護されたシステムの設計、機密資産の暗号化、データベース サーバーに対するファイアウォールの構築などの、データベースを保護するいくつかの対策を講じることができます。 ただし、物理メディア (ドライブやバックアップ テープなど) が盗まれたシナリオでは、悪意のある第三者がデータベースを復元したりデータベースを乗っ取って、データを閲覧する可能性があります。 ソリューションの 1 つとして、データベース内の機密データを暗号化し、証明書を使用してデータを暗号化するために使用するキーを保護することが挙げられます。 これにより、キーを持たないユーザーによるデータの使用を防ぐことができます。ただし、このような保護は事前に計画する必要があります。

TDE は、保存データ、つまりデータとログ ファイルを保護します。 多数の法律、規制、さまざまな業界で制定されたガイドラインに準拠する機能を提供します。 これにより、ソフトウェア開発者は既存のアプリケーションを変更することなく、業界標準の暗号化アルゴリズムを使用してデータを暗号化できます。

規制で明示的にこのような暗号化が指定されている場合は、TDE を使用してください。 ただし、通常のアクセス パスを使用する攻撃者を阻止することはできませんので注意してください。 TDE は、行や列に対する Azure SQL の提供する暗号化、またはアプリケーション レベルの暗号化のいずれかを通じて、追加のアプリケーション レベルの暗号化を使用する必要があるような、まれなケースに対する保護に使用されます。

アプリケーション レベルの暗号化は、選択したデータにも使用する必要があります。 データ主権の問題は、適切な国に保管されているキーを使ってデータを暗号化することにより緩和されます。 これにより、偶発的なデータ転送が問題を引き起こすことが防止されます。その理由は、強力なアルゴリズム (AES 256 など) が使用されていると仮定した場合、キーがなければデータの暗号化を解除することは不可能なためです。

Azure SQL が提供する行や列に対する暗号化は、権限のある ([RBAC](../active-directory/role-based-access-built-in-roles.md)) ユーザーのみにアクセスを許可し、権限の低いユーザーによって列や行が閲覧されることを防ぎます。

## <a name="next-steps"></a>次のステップ
この記事では、PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティ保護するための、SQL Database と SQL Data Warehouse の一連のセキュリティ ベスト プラクティスについて説明しました。 PaaS デプロイのセキュリティ保護の詳細については、次のリンク先をご覧ください。

- [PaaS デプロイをセキュリティで保護する](security-paas-deployments.md)
- [Azure App Services を使用して PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティで保護する](security-paas-applications-using-app-services.md)

