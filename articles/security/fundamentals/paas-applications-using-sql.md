---
title: Azure で Paas データベースをセキュリティで保護する | Microsoft Docs
description: 'PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティ保護するための、Azure SQL Database と Azure Synapse Analytics のセキュリティ ベスト プラクティスについて説明します。 '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: 278812754c636d434bf579c0408832f1e99d3445
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94408075"
---
# <a name="best-practices-for-securing-paas-databases-in-azure"></a>Azure で PaaS データベースを保護するベスト プラクティス

この記事では、PaaS (platform-as-a-service) の Web アプリケーションとモバイル アプリケーションをセキュリティ保護するための、[Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md) と [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) の一連のセキュリティ ベスト プラクティスについて説明します。 このベスト プラクティスは、Azure に関して Microsoft が蓄積してきたノウハウと、ユーザーの皆様の経験に基づいています。

Azure SQL Database と Azure Synapse Analytics は、インターネット ベースのアプリケーション用のリレーショナル データベース サービスを提供します。 PaaS デプロイで Azure SQL Database と Azure Synapse Analytics を使用するときに、アプリケーションとデータの保護に役立つサービスを見ていきましょう。

- Azure Active Directory 認証 (SQL Server 認証ではない)
- Azure SQL ファイアウォール
- Transparent Data Encryption (TDE)

## <a name="use-a-centralized-identity-repository"></a>一元化された ID レポジトリを使用

Azure SQL Database は、次の 2 種類の認証のいずれかを使用するように構成できます。

- **SQL 認証** では、ユーザー名とパスワードを使用します。 データベースのサーバーを作成したときに、ユーザー名とパスワードによる "サーバー管理" ログインを指定しています。 これらの資格情報を使用すると、データベース所有者として、そのサーバーにある任意のデータベースを認証できます。

- **Azure Active Directory 認証** では、Azure Active Directory で管理されている ID を使用し、管理、統合されたドメインをサポートしています。 Azure Active Directory 認証を使用するには、"Azure AD 管理者" という、Azure AD ユーザーとグループを管理できるサーバー管理者を別に作成する必要があります。 この管理者は、通常のサーバー管理者が実行できるすべての操作も実行できます。

[Azure Active Directory 認証](../../active-directory/develop/authentication-vs-authorization.md)は、Azure Active Directory (AD) の ID を使用して Azure SQL Database と Azure Synapse Analytics に接続するメカニズムです。 Azure AD では、SQL Server 認証とは別の認証が提供されるため、データベース サーバー間でユーザー ID が拡散されるのを防ぐことができます。 Azure AD 認証を使用すると、データベース ユーザーの ID や他の Microsoft サービスを一元管理できます。 ID の一元管理では、1 か所でデータベース ユーザーを管理できるようになるため、アクセス許可の管理が容易になります。  

### <a name="benefits-of-using-azure-ad-instead-of-sql-authentication"></a>SQL 認証ではなく Azure AD を使用する利点

- 1 か所でのパスワードのローテーションを許可します。
- 外部の Azure AD グループを使用してデータベースのアクセス許可を管理できます。
- 統合 Windows 認証や、Azure AD でサポートされる他の認証形式を有効にすると、パスワードが保存されません。
- 包含データベース ユーザーを使用して、データベース レベルで ID を認証します。
- SQL Database に接続するアプリケーション向けにトークンベース認証をサポートしています。
- Azure AD 認証は、ADFS (Active Directory フェデレーション サービス) またはドメインを同期しないローカル Azure AD のためのネイティブ ユーザー/パスワード認証でドメインフェデレーションをサポートします。
- [Multi-Factor Authentication (MFA)](../../active-directory/authentication/concept-mfa-howitworks.md) を含む Active Directory ユニバーサル認証を使用する SQL Server Management Studio からの接続をサポートします。 MFA には、電話、テキスト メッセージ、スマート カードと暗証番号 (PIN)、モバイル アプリ通知など、簡単な各種確認オプションによる強力な認証が含まれます。 詳細については、[SQL Database と Azure Synapse Analytics でのユニバーサル認証](../../azure-sql/database/authentication-mfa-ssms-overview.md)に関するページを参照してください。

Azure AD 認証の詳細については、次を参照してください。

- [SQL Database、Managed Instance、または Azure Synapse Analytics での認証に Azure Active Directory Authentication を使用する](../../azure-sql/database/authentication-aad-overview.md)
- [Azure Synapse Analytics への認証](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Azure AD 認証を使用して Azure SQL Database のトークン ベース認証をサポート](../../azure-sql/database/authentication-aad-overview.md)

> [!NOTE]
> Azure Active Directory を環境に確実に適合させるには、「[Azure AD の機能と制限事項](../../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations)」を参照してください。

## <a name="restrict-access-based-on-ip-address"></a>IP アドレスに基づいてアクセスを制限する

受け入れ可能な IP アドレスの範囲を指定するファイアウォール規則を作成できます。 これらの規則は、サーバー レベルとデータベース レベルの両方を対象にすることができます。 ただし、セキュリティを強化しデータベースの移植性を高めるため、可能な限りデータベース レベルのファイアウォール規則を使用することをお勧めします。 アクセス要件が同じデータベースが多数存在し、それぞれのデータベースの設定に時間を費やしたくない場合は、管理者向けのサーバー レベルのファイアウォール規則を使用します。

SQL Database 既定ソース IP アドレス制限では、他のサブスクリプションやテナントを含む、どの Azure アドレスからでもアクセスできます。 これを、自分の IP アドレスのみがインスタンスへのアクセスを許可されるように制限することができます。 SQL ファイアウォールと IP アドレス制限を使用していても、強力な認証が必要です。 この記事で前述した推奨事項を参照してください。

Azure SQL ファイアウォールと IP の制限については、次を参照してください。

- [Azure SQL Database と Azure Synapse Analytics のアクセス制御](../../azure-sql/database/logins-create-manage.md)
- [Azure SQL Database と Azure Synapse Analytics のファイアウォール規則](../../azure-sql/database/firewall-configure.md)

## <a name="encrypt-data-at-rest"></a>保存データを暗号化

[Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) は既定で有効になっています。 TDE は SQL Server、Azure SQL Database、および Azure Synapse Analytics のデータ ファイルとログ ファイルを透過的に暗号化します。 TDE はファイルやそのバックアップへの直接アクセスによる侵害から保護します。 これにより、既存のアプリケーションを変更することなく保存データを暗号化できます。 TDE は常に有効にしておく必要があります。ただし、通常のアクセス パスを使用する攻撃者を阻止することはできません。 TDE は多数の法律、規制、さまざまな業界で制定されたガイドラインに準拠する機能を提供します。

Azure SQL は TDE のキーに関連する問題を管理します。 TDE については、データベースを移動する際には復旧可能性を確保するために、オンプレミスで特別な注意が必要です。 より高度なシナリオでは、Azure Key Vault で拡張可能なキー管理を通じてキーを明示的に管理できます。 [EKM を使用して SQL Server で TDE を有効にする](/sql/relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm)を参照してください。 またこれにより、Bring Your Own Key (BYOK) が Azure Key Vault の BYOK 機能を通じて有効になります。

Azure SQL では [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) を通じて列を暗号化できます。 これにより、許可されているアプリケーションのみが重要な列にアクセスできます。 このような暗号化を使用することで、暗号化された列に対する SQL クエリを等値ベースの値に制限します。

アプリケーション レベルの暗号化は、選択したデータにも使用する必要があります。 データ主権の問題は、適切な国または地域に保管されているキーを使用してデータを暗号化することにより緩和されることがあります。 これにより、偶発的なデータ転送が問題を引き起こすことが防止されます。その理由は、強力なアルゴリズム (AES 256 など) が使用されていると仮定した場合、キーがなければデータの暗号化を解除することは不可能なためです。

セキュリティで保護されたシステムの設計、機密資産の暗号化、データベース サーバーに対するファイアウォールの構築などの、データベースを保護する追加の対策を講じることができます。

## <a name="next-steps"></a>次のステップ

この記事では、PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティ保護するための、SQL Database と Azure Synapse Analytics の一連のセキュリティ ベスト プラクティスについて説明しました。 PaaS デプロイのセキュリティ保護の詳細については、次のリンク先をご覧ください。

- [PaaS デプロイをセキュリティで保護する](paas-deployments.md)
- [Azure App Services を使用して PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティで保護する](paas-applications-using-app-services.md)