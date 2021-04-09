---
title: 一般的なセキュリティ要件を解決するためのプレイブック
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: この記事では、Azure SQL Database および Azure SQL Managed Instance の一般的なセキュリティ要件とベスト プラクティスについて説明します。
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 09/21/2020
ms.reviewer: ''
ms.openlocfilehash: 1217d3af855e96b6d6a0f403c2ff351a6b957d9a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96459667"
---
# <a name="playbook-for-addressing-common-security-requirements-with-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database と Azure SQL Managed Instance で一般的なセキュリティ要件を解決するためのプレイブック
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

この記事では、一般的なセキュリティ要件を解決する方法に関するベスト プラクティスを提供します。 一部の要件はすべての環境には適用されないため、どの機能を実装するかについて、ご自分のデータベースおよびセキュリティ チームにご相談ください。

## <a name="solving-common-security-requirements"></a>一般的なセキュリティ要件の解決

このドキュメントでは、Azure SQL Database および Azure SQL Managed Instance を使用する新規または既存のアプリケーションの一般的なセキュリティ要件を解決する方法に関するガイダンスを提供します。 これは、高レベルのセキュリティ領域別に整理されています。 特定の脅威の対処については、「[一般的なセキュリティ上の脅威と考えられる軽減策](#common-security-threats-and-potential-mitigations)」セクションを参照してください。 提示されているいくつかの推奨事項は、アプリケーションをオンプレミスから Azure に移行する際に適用できますが、このドキュメントでは移行シナリオには焦点を当てていません。

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>このガイドで扱っている Azure SQL Database デプロイのオファー

- [Azure SQL Database](./index.yml): [サーバー](logical-servers.md)内の[単一データベース](single-database-overview.md)と[エラスティック プール](elastic-pool-overview.md)
- [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)

### <a name="deployment-offers-not-covered-in-this-guide"></a>このガイドで扱っていないデプロイのオファー

- Azure Synapse Analytics
- Azure SQL VM (IaaS)
- SQL Server

### <a name="audience"></a>対象ユーザー

このガイドの対象読者は、Azure SQL Database をセキュリティで保護する方法に関する問題に直面しているユーザーです。 このベスト プラクティスの記事が役に立つロールとして、次が挙げられます (ただし、これらに限定されません)。

- セキュリティ アーキテクト
- セキュリティ マネージャー
- コンプライアンス責任者
- プライバシー責任者
- セキュリティ エンジニア

### <a name="using-this-guide"></a><a id="using"></a> このガイドの使用法

このドキュメントは、既存の [Azure SQL Database セキュリティ](security-overview.md)に関するドキュメントとの併用を意図しています。

特に明記されていない限り、各セクションに記載されているすべてのベスト プラクティスに従って、それぞれの目標や要件を達成することをお勧めします。 特定のセキュリティに関するコンプライアンス基準やベスト プラクティスを満たすために、該当する場合は必ず、要件または目標セクションに重要な規制コンプライアンス制御が一覧表示されています。 この記事で言及されているセキュリティ基準と規制は、次のとおりです。

- [FedRAMP](https://www.fedramp.gov/documents/):AC-04、AC-06
- [SOC](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html):CM-3、SDL-3
- [ISO/IEC 27001](https://www.iso27001security.com/html/27001.html):アクセス制御、暗号
- [Microsoft Operational Security Assurance (OSA) のプラクティス](https://www.microsoft.com/securityengineering/osa/practices):プラクティス #1-6 と #9
- [NIST Special Publication 800-53 セキュリティ制御](https://nvd.nist.gov/800-53):AC-5、AC-6
- [PCI DSS](https://www.pcisecuritystandards.org/document_library):6.3.2、6.4.2

こちらに記載されている推奨事項とベスト プラクティスは、継続的に更新予定です。 このドキュメントに関してご意見または訂正事項などがございましたら、この記事の下部にある **フィードバック** のリンクを利用してお寄せください。

## <a name="authentication"></a>認証

認証は、ユーザーが本人の主張どおりの人物であることを証明するプロセスです。 Azure SQL Database と SQL Managed Instance では、次の 2 種類の認証がサポートされています。

- SQL 認証
- Azure Active Directory 認証

> [!NOTE]
> 一部のツールとサードパーティ アプリケーションでは、Azure Active Directory 認証がサポートされない場合があります。

### <a name="central-management-for-identities"></a>ID の中央管理

ID の中央管理には、次のような利点があります。

- サーバー、データベース、マネージド インスタンス間でログインを重複させずに、グループ アカウントを管理してユーザーのアクセス許可を制御します。
- 簡素化された柔軟なアクセス許可の管理。
- 大規模なアプリケーションの管理。

**実装方法**:

- Azure Active Directory (Azure AD) 認証を使用して、ID の集中管理を行います。

**ベスト プラクティス**:

- Azure AD テナントを作成し、人間のユーザーを表す[ユーザーを作成](../../active-directory/fundamentals/add-users-azure-active-directory.md)し、アプリ、サービス、自動化ツールを表す[サービス プリンシパル](../../active-directory/develop/app-objects-and-service-principals.md)を作成します。 サービス プリンシパルは、Windows および Linux でのサービス アカウントに相当します。

- グループ割り当てを使用して Azure AD プリンシパルにリソースへのアクセス権を割り当てます。Azure AD グループを作成し、グループにアクセス権を付与し、個々のメンバーをグループに追加します。 データベースに、Azure AD グループをマップする包含データベース ユーザーを作成します。 データベース内のアクセス許可を割り当てるには、適切なアクセス許可があるデータベース ロールを、Azure AD グループに関連付けられているユーザーに付与します。
  - 「[SQL による Azure Active Directory 認証の構成と管理](authentication-aad-configure.md)」と[Azure AD を使用した SQL の認証](authentication-aad-overview.md)に関する記事を参照してください。
  > [!NOTE]
  > SQL Managed Instance では、マスター データベースの Azure AD プリンシパルにマップされるログインを作成することもできます。 「[CREATE LOGIN (Transact-SQL)](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)」を参照してください。

- Azure AD グループを使用するとアクセス許可の管理が簡素化され、グループ所有者とリソース所有者の両方が、グループへのメンバーの追加またはグループからのメンバーの削除を行うことができます。

- サーバーまたはマネージド インスタンスごとに Azure AD 管理者用の個別のグループを作成します。

  - [サーバーの Azure Active Directory 管理者のプロビジョニング](authentication-aad-configure.md#provision-azure-ad-admin-sql-database)に関する記事を参照してください。

- Azure AD 監査アクティビティ レポートを使用して Azure AD グループ メンバーシップの変更を監視します。

- マネージド インスタンスの場合、Azure AD 管理者を作成するための別の手順が必要です。
  - 「[マネージド インスタンスの Azure Active Directory 管理者をプロビジョニングする](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)」という記事を参照してください。

> [!NOTE]
>
> - Azure AD 認証は Azure SQL 監査ログに記録されますが、Azure AD サインイン ログには記録されません。
> - Azure で付与された Azure RBAC アクセス許可は、Azure SQL Database または SQL Managed Instance のアクセス許可には適用されません。 このようなアクセス許可は、既存の SQL アクセス許可を使用して手動で作成またはマップする必要があります。
> - クライアント側では、Azure AD 認証にはインターネットへのアクセス、またはユーザー定義ルート (UDR) 経由の仮想ネットワークへのアクセスが必要です。
> - Azure AD アクセス トークンはクライアント側にキャッシュされ、その有効期間はトークンの構成によって異なります。 「[Azure Active Directory における構成可能なトークンの有効期間](../../active-directory/develop/active-directory-configurable-token-lifetimes.md)」という記事を参照してください。
> - Azure AD Authentication の問題のトラブルシューティングのガイダンスについては、次のブログを参照してください。[Azure AD のトラブルシューティング](https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991)。

### <a name="azure-ad-multi-factor-authentication"></a>Azure AD の Multi-Factor Authentication

> 次で言及されています: OSA プラクティス #2、ISO アクセス制御 (AC)

Azure AD Multi-Factor Authentication では、複数の形式の認証が要求されるので、セキュリティが強化されます。

**実装方法**:

- 条件付きアクセスを使用して Azure AD で [Multi-Factor Authentication を有効](../../active-directory/authentication/concept-mfa-howitworks.md)にし、対話型認証を使用します。

- 別の方法として、Azure AD または AD ドメイン全体で Multi-Factor Authentication を有効にすることもできます。

**ベスト プラクティス**:

- Azure AD で条件付きアクセスをアクティブにします (Premium サブスクリプションが必要)。
  - [Azure AD での条件付きアクセス](../../active-directory/conditional-access/overview.md)に関する記事を参照してください。  

- Azure AD グループを作成し、Azure AD 条件付きアクセスを使用して、選択したグループに対して Multi-Factor Authentication ポリシーを有効にします。
  - [条件付きアクセスの展開の計画](../../active-directory/conditional-access/plan-conditional-access.md)に関する記事を参照してください。

- Multi-Factor Authentication は、Azure AD 全体、または Azure AD とフェデレーションされた Active Directory 全体で有効にすることができます。

- パスワードが対話形式で要求され、その後に Multi-Factor Authentication 認証が適用される Azure SQL Database と Azure SQL Managed Instance には Azure AD 対話型認証モードを使用します。
  - SSMS でユニバーサル認証を使用します。 [Azure SQL Database、SQL Managed Instance、Azure Synapse で多要素 Azure AD 認証を使用する (Multi-Factor Authentication の SSMS サポート)](authentication-mfa-ssms-overview.md) に関する記事を参照してください。
  - SQL Server Data Tools (SSDT) でサポートされている対話型認証を使用します。 「[SQL Server Data Tools (SSDT) での Azure Active Directory のサポート](/sql/ssdt/azure-active-directory?view=azuresqldb-current)」という記事を参照してください。
  - Multi-Factor Authentication をサポートする他の SQL ツールを使用します。
    - データベースをエクスポート/抽出/デプロイするための SSMS ウィザードのサポート  
    - [sqlpackage.exe](/sql/tools/sqlpackage): オプション "/ua"
    - [sqlcmd ユーティリティ](/sql/tools/sqlcmd-utility): オプション -G (対話型)
    - [bcp ユーティリティ](/sql/tools/bcp-utility): オプション -G (対話型)

- Multi-Factor Authentication をサポートする対話型認証を使用して、Azure SQL Database または Azure SQL Managed Instance に接続するアプリケーションを実装します。
  - [Azure AD Multi-Factor Authentication を使用した Azure SQL Database への接続](active-directory-interactive-connect-azure-sql-db.md)に関する記事を参照してください。
  > [!NOTE]
  > この認証モードには、ユーザーベースの ID が必要です。 個々の Azure AD ユーザー認証をバイパスする信頼できる ID モデルが使用されている場合 (たとえば、Azure リソースにマネージド ID を使用)、Multi-Factor Authentication は適用されません。

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>ユーザーに対するパスワードベースの認証の使用を最小限にする

> 次で言及されています: OSA プラクティス #4、ISO アクセス制御 (AC)

パスワードベースの認証方法は、強度が低い認証形式です。 資格情報が侵害されたり、誤って漏洩したりする可能性があります。

**実装方法**:

- パスワードの使用を排除する Azure AD 統合認証を使用します。

**ベスト プラクティス**:

- Windows の資格情報を使用したシングル サインオン認証を使用します。 オンプレミスの AD ドメインを Azure AD とフェデレーションし、(Azure AD を使用するドメインに参加しているコンピューターに対して) 統合 Windows 認証を使用します。
  - [Azure AD 統合認証に対する SSMS のサポート](authentication-aad-configure.md#active-directory-integrated-authentication)に関する記事を参照してください。

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>アプリケーションに対するパスワードベースの認証の使用を最小限にする

> 次で言及されています: OSA プラクティス #4、ISO アクセス制御 (AC)

**実装方法**:

- Azure マネージド ID を有効にします。 また、統合認証や証明書ベースの認証を使用することもできます。

**ベスト プラクティス**:

- [Azure リソースに対してマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を使用します。
  - [システム割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)
  - [ユーザー割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [マネージド ID を使って Azure App Service から Azure SQL Database を使用します (コード変更なし)](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- アプリケーションに対して、証明書ベースの認証を使用します。
  - こちらの[コード サンプル](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token)を参照してください。

- 統合されたフェデレーション ドメインと、ドメインに参加しているコンピューターには Azure AD 認証を使用します (上記のセクションを参照)。
  - [統合認証のサンプル アプリケーション](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated)を参照してください。

### <a name="protect-passwords-and-secrets"></a>パスワードとシークレットを保護する

パスワードの使用を避けられない場合は、それらがセキュリティで保護されていることを確認します。

**実装方法**:

- Azure Key Vault を使用してパスワードとシークレットを格納します。 適用できる場合は必ず、Azure AD ユーザーを含む Azure SQL Database に対して Multi-Factor Authentication を使用します。

**ベスト プラクティス**:

- パスワードやシークレットを回避できない場合は、ユーザー パスワードとアプリケーション シークレットを Azure Key Vault に保存し、Key Vault のアクセス ポリシーを使用してアクセスを管理します。

- アプリ開発フレームワークによっては、アプリ内のシークレットを保護するための、フレームワーク固有のメカニズムが提供されている場合もあります。 次に例を示します。[ASP.NET Core アプリ](/aspnet/core/security/app-secrets?tabs=windows&view=aspnetcore-2.1)。

### <a name="use-sql-authentication-for-legacy-applications"></a>レガシ アプリケーションに対して SQL 認証を使用する

SQL 認証とは、ユーザーがユーザー名とパスワードを使用して Azure SQL Database または SQL Managed Instance に接続するときに、そのユーザーを認証することを指します。 ログインは、サーバーまたはマネージド インスタンスごとに作成し、ユーザーはデータベースごとに作成する必要があります。

**実装方法**:

- SQL 認証を使用します。

**ベスト プラクティス**:

- サーバーまたはインスタンス管理者として、ログインとユーザーを作成します。 パスワードを持つ包含データベース ユーザーを使用する場合を除いて、すべてのパスワードはマスター データベースに格納されます。
  - [SQL Database、SQL Managed Instance、Azure Synapse Analytics へのデータベース アクセスの制御と許可](logins-create-manage.md)に関する記事を参照してください。

## <a name="access-management"></a>アクセス管理

アクセス管理 (承認とも呼ばれます) とは、承認されたユーザーの Azure SQL Database または SQL Managed Instance へのアクセス権と特権を制御および管理するプロセスです。

### <a name="implement-principle-of-least-privilege"></a>最小特権の原則を実装する

> 次で言及されています: FedRamp コントロール AC-06、NIST:AC-6、OSA プラクティス #3

最小特権の原則には、ユーザーは自分のタスクを完了するのに必要である以上の特権を持つべきではないと記載されています。 詳細については、「[Just Enough Administration](/powershell/scripting/learn/remoting/jea/overview)」という記事を参照してください。

**実装方法**:

必要なタスクを完了するのに必要な[アクセス許可](/sql/relational-databases/security/permissions-database-engine)のみを割り当てます。

- SQL データベースの場合:
  - 粒度の細かいアクセス許可とユーザー定義データベース ロール (Managed Instance ではサーバー ロール) を使用します。
    1. 必要なロールを作成します
       - [CREATE ROLE](/sql/t-sql/statements/create-role-transact-sql)
       - [CREATE SERVER ROLE](/sql/t-sql/statements/create-server-role-transact-sql)
    1. 必要なユーザーを作成します
       - [CREATE USER](/sql/t-sql/statements/create-user-transact-sql)
    1. ユーザーをメンバーとしてロールに追加します
       - [ALTER ROLE](/sql/t-sql/statements/alter-role-transact-sql)
       - [ALTER SERVER ROLE](/sql/t-sql/statements/alter-server-role-transact-sql)
    1. 次に、アクセス許可をロールに割り当てます。
       - [GRANT](/sql/t-sql/statements/grant-transact-sql)
  - 不要なロールにユーザーを割り当てないようにしてください。

- Azure Resource Manager の場合:
  - 組み込みロール (使用可能な場合) または Azure カスタム ロールを使用し、必要なアクセス許可を割り当てます。
    - [Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)
    - [Azure カスタム ロール](../../role-based-access-control/custom-roles.md)

**ベスト プラクティス**:

次のベスト プラクティスは省略可能ですが、セキュリティ戦略の管理容易性とサポート容易性が向上します。

- 可能ならば、最も可能性の低いアクセス許可セットから開始し、真の必要性 (および正当性) がある場合に 1 つずつアクセス許可の追加を開始します。これは、アクセス許可を 1 つずつ除去していくという反対のアプローチとは逆になります。

- 個々のユーザーにアクセス許可を割り当てないようにします。 代わりに、一貫してロール (データベースまたはサーバーのロール) を使用します。 ロールは、アクセス許可のレポートとトラブルシューティングに非常に役立ちます。 (Azure RBAC では、ロールによるアクセス許可の割り当てのみがサポートされます)。

- 正確に必要な分だけのアクセス許可を備えたカスタム ロールを作成して使用します。 実際に使用される一般的なロールは次のとおりです。
  - セキュリティ展開
  - 管理者
  - Developer
  - サポート担当者
  - 監査担当者
  - 自動プロセス
  - エンド ユーザー
  
- ロールのアクセス許可がユーザーに必要なアクセス許可と完全に一致する場合にのみ、組み込みロールを使用します。 ユーザーは複数のロールに割り当てることができます。

- データベース エンジンのアクセス許可は、次のスコープ内で適用できることを覚えておいてください (スコープが小さいほど、付与されるアクセス許可の影響も小さくなります)。
  - Azure のサーバー (マスター データベースの特別なロール)
  - データベース
  - スキーマ
    - データベース内のアクセス許可の付与にはスキーマを使用するのがベスト プラクティスです。 (関連トピック: 「[Schema-design:Recommendations for Schema design with security in mind](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/)」(スキーマ設計: セキュリティに配慮したスキーマ設計の推奨事項))
  - オブジェクト (テーブル、ビュー、プロシージャなど)

  > [!NOTE]
  > オブジェクト レベルでアクセス許可を適用することは推奨されません。このレベルでは、不必要な複雑さが実装全体に追加されるからです。 オブジェクト レベルのアクセス許可を使用する場合は、それらを明確に文書化する必要があります。 列レベルのアクセス許可も同様です。それらは、同じ理由により、さらにお勧めできません。 また、既定ではテーブルレベルの[拒否](/sql/t-sql/statements/deny-object-permissions-transact-sql)によって列レベルの許可がオーバーライドされないことにも注意してください。 このため、[情報セキュリティ国際評価基準に準拠したサーバー構成](/sql/database-engine/configure-windows/common-criteria-compliance-enabled-server-configuration-option)をアクティブにする必要があります。

- [脆弱性評価 (VA)](/sql/relational-databases/security/sql-vulnerability-assessment) を使用して通常のチェックを実行し、アクセス許可が多すぎるかどうかをテストします。

### <a name="implement-separation-of-duties"></a>職務の分離を実装する

> 次で言及されています: FedRamp:AC-04、NIST:AC-5、ISO:6.1.2、PCI 6.4.2、SOC:CM-3、SDL-3

職務の分離では、機密性の高いタスクを、別々のユーザーに割り当てられる複数のタスクに分割する必要性が述べられています。 職務を分離することで、データの侵害を防ぐことができます。

**実装方法**:

- 必要な職務の分離レベルを特定します。 例 :
  - 開発/テスト環境と運用環境の間
  - セキュリティ上機密性の高いタスクと、データベース管理者 (DBA) の管理レベル タスクと、開発者のタスク。
    - 例 :監査者、ロール レベル セキュリティ (RLS) のセキュリティ ポリシーの作成、DDL アクセス許可を持つ SQL Database オブジェクトの実装。

- システムにアクセスするユーザーの包括的階層 (および自動化されたプロセス) を特定します。

- 必要なユーザー グループにしたがってロールを作成し、ロールにアクセス許可を割り当てます。
  - Azure portal での、または PowerShell 自動化を使用した管理レベルのタスクには、Azure ロールを使用します。 要件に一致する組み込みロールを見つけるか、使用可能なアクセス許可を使用して Azure カスタム ロールを作成します。
  - サーバー全体のタスク (新しいログイン、データベースの作成) のサーバー ロールをマネージド インスタンスに作成します。
  - データベース レベルのタスクに対するデータベース ロールを作成します。

- 特定の機密性の高いタスクについては、ユーザーに代わってタスクを実行するために、証明書によって署名された特殊なストアド プロシージャを作成することを検討してください。 デジタル署名されたストアド プロシージャの重要な利点の 1 つは、プロシージャが変更された場合に、以前のバージョンのプロシージャに与えられたアクセス許可が直ちに削除されることです。
  - 例:[チュートリアル:証明書を使用したストアド プロシージャへの署名](/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate)

- Azure Key Vault のカスタマー マネージド キーを使用して Transparent Data Encryption (TDE) を実装し、データ所有者とセキュリティ所有者の間での職務の分離を可能にします。
  - [Azure portal からの Azure Storage 暗号化用カスタマー マネージド キーの構成](../../storage/common/customer-managed-keys-configure-key-vault.md)に関する記事を参照してください。

- 非常に機密性が高いと見なされるデータを DBA が表示できないようにしながらも、DBA のタスクを実行できるようにするには、ロール分離と共に Always Encrypted を使用することができます。
  - 「[Always Encrypted のキー管理の概要](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted)」、「[役割の分離を指定してキーを与える](/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles)」、および「[役割の分離ありの列マスター キーの交換](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation)」の記事を参照してください。

- Always Encrypted の使用が不可能な場合や、少なくとも大きなコストと努力を払わなくては実現できないようなケースでは (それにより、システムがほぼ使用不可になる可能性すらある場合)、次のような補完コントロールの使用により妥協し、侵害を軽減できます。
  - プロセスへの人間の介入。
  - 監査証跡 - 監査の詳細については、「[重要なセキュリティ イベントを監査する](#audit-critical-security-events)」を参照してください。

**ベスト プラクティス**:

- 開発/テスト環境と運用環境で、別々のアカウントが使用されていることを確認します。 別々のアカウントを使用すると、テストと運用のシステムの分離に従うことができます。

- 個々のユーザーにアクセス許可を割り当てないようにします。 代わりに、一貫してロール (データベースまたはサーバーのロール) を使用します。 ロールを使用すると、アクセス許可のレポートとトラブルシューティングに非常に役立ちます。

- アクセス許可が、必要なアクセス許可に完全に一致する場合は組み込みロールを使用します。複数の組み込みロールのすべてのアクセス許可を合わせると 100% 一致する場合は、複数のロールを同時に割り当てることもできます。

- 組み込みロールで付与されるアクセス許可が多すぎる場合や不十分な場合は、ユーザー定義ロールを作成して使用します。

- ロールの割り当ては、T-SQL の SQL エージェント ジョブ ステップ内で、または Azure ロール用の Azure PIM を使用して一時的に行うこともできます。これは、動的な職務の分離 (DSD) とも呼ばれます。

- DBA が暗号化キーやキー ストアにアクセスできないことを確認し、次に、キーにアクセスできるセキュリティ管理者がデータベースにアクセスできないことを確認します。 [拡張キー管理 (EKM)](/sql/relational-databases/security/encryption/extensible-key-management-ekm) を使用すると、このような分離を簡単に実現できます。 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) を使用して EKM を実装できます。

- セキュリティに関連した操作については、常に監査証跡を取るようにします。

- Azure 組み込みロールの定義を取得して、使用されているアクセス許可を確認し、PowerShell を使用してそれらの抜粋と蓄積に基づいてカスタム ロールを作成することができます。

- db_owner データベース ロールのメンバーは、Transparent Data Encryption (TDE) のようなセキュリティ設定を変更したり、SLO を変更したりできるため、このメンバーシップの付与は慎重に行う必要があります。 ただし、db_owner 特権を必要とする多くのタスクがあります。 DB オプションの変更など、データベース設定の変更のようなタスク。 どのソリューションでも監査が重要な役割を果たします。

- db_owner のアクセス許可を制限することはできません。そのため、管理者アカウントがユーザー データを表示できないようにします。 データベース内に非常に機密性の高いデータがある場合は、Always Encrypted を使用すると、db_owner や他の DBA がそれを表示するのを安全に防止できます。

> [!NOTE]
> セキュリティ関連またはトラブルシューティングのタスクでは、職務の分離 (SoD) を実現することは困難です。 開発やエンドユーザーのロールのようなその他の領域では、分離が容易になります。 多くのコンプライアンスに関連した制御では、他のソリューションが実用的でない場合、監査などの代替制御機能を使用できます。

SoD についてより深く知りたい読者には、次のリソースをお勧めします。

- Azure SQL Database と SQL Managed Instance の場合:  
  - [データベース アクセスの制御と許可](logins-create-manage.md)
  - [アプリケーション開発者のための職務の分離](/previous-versions/sql/sql-server-2008/cc974525(v=sql.100))
  - [職務の分離](https://www.microsoft.com/download/details.aspx?id=39269)
  - [ストアド プロシージャへの署名](/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- Azure Resource Management の場合:
  - [Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)
  - [Azure カスタム ロール](../../role-based-access-control/custom-roles.md)
  - [昇格されたアクセス権に Azure AD Privileged Identity Management を使用する](https://www.microsoft.com/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>通常のコード レビューを実行する

> 次で言及されています: PCI:6.3.2、SOC:SDL-3

職務の分離は、データベース内のデータに限定されず、アプリケーション コードも含まれます。 悪意のあるコードが、セキュリティ制御をくぐり抜ける可能性があります。 カスタム コードを運用環境にデプロイする前に、デプロイされる内容を確認する必要があります。

**実装方法**:

- ソース管理をサポートする Azure Data Studio のようなデータベース ツールを使用します。

- 分離されたコード デプロイ プロセスを実装します。

- メイン ブランチにコミットする前に、(コード自体の作成者以外の) ユーザーが、特権の昇格の可能性に関するリスクや悪意のあるデータ変更がないかコードを検査して、不正および悪意のあるアクセスから保護する必要があります。 これは、ソース管理メカニズムを使用して実行できます。

**ベスト プラクティス**:

- 標準化: すべてのコード更新で従うべき標準的手順を実装すると役立ちます。

- 脆弱性評価には、過剰なアクセス許可、古い暗号化アルゴリズムの使用、およびデータベース スキーマ内のその他のセキュリティ問題についてチェックする規則が含まれています。

- SQL インジェクションに対して脆弱なコードがないかスキャンする Advanced Threat Protection を使用して、QA やテスト環境でさらなるチェックを行うことができます。

- 注意すべき内容の例を次に示します。
  - 自動化された SQL コード更新デプロイ内からのユーザーの作成またはセキュリティ設定の変更。
  - 指定されたパラメーターに応じて、規則に従わない方法でセル内の通貨値を更新するストアド プロシージャ。

- レビューを行うユーザーが、元のコード作成者以外の個人であり、コード レビューと安全なコーディングに関する知識を持っていることを確認します。

- コード変更のすべてのソースを必ず把握するようにします。 コードは T-SQL スクリプトに記述できます。 ビュー、関数、トリガー、ストアド プロシージャの形式で実行または配置されるアドホック コマンドです。 SQL Agent ジョブ定義 (ステップ) に含めることができます。 また、SSIS パッケージ、Azure Data Factory、およびその他のサービス内から実行することもできます。

## <a name="data-protection"></a>データ保護

データ保護は、暗号化や難読化によって、重要な情報が侵害されないように保護するための一連の機能です。

> [!NOTE]
> Microsoft は、Azure SQL Database、SQL Managed Instance が FIPS 140-2 レベル 1 に準拠していることを証明します。 これは、FIPS 140-2 レベル 1 の許容されるアルゴリズムと、それらのアルゴリズムの FIPS 140-2 レベル 1 検証済みインスタンスの厳密な使用 (必要なキー長、キー管理、キー生成、およびキーの格納に関する整合性など) を検証した後に行われます。 この証明は、データの処理またはシステムやアプリケーションの提供において、FIPS 140-2 レベル 1 検証済みインスタンスの使用の必要性または要件にお客様が対応できるようにすることを目的としています。 Microsoft では、米国およびカナダ政府で使用されている別の用語 "FIPS 140-2 レベル 1 検証済み" に対する意図された適用性を示すために、上記のステートメントで使用されている "FIPS 140-2 レベル 1 準拠" と "FIPS 140-2 レベル 1 コンプライアンス" という用語を定義しています。

### <a name="encrypt-data-in-transit"></a>転送中のデータを暗号化する

> 次で言及されています: OSA プラクティス #6、ISO コントロール ファミリCryptography

クライアントとサーバーの間でデータが移動している間、データを保護します。 「[ネットワークのセキュリティ](#network-security)」を参照してください。

### <a name="encrypt-data-at-rest"></a>保存データを暗号化

> 次で言及されています: OSA プラクティス #6、ISO コントロール ファミリCryptography

保存時の暗号化は、データベース、ログ、およびバックアップ ファイルに保存されているときのデータの暗号化保護です。

**実装方法**:

- サービス マネージド キーを使用した [Transparent Database Encryption (TDE)](transparent-data-encryption-tde-overview.md) は、2017 年より後に Azure SQL Database および SQL Managed Instance で作成されたすべてのデータベースに対して既定で有効になっています。
- マネージド インスタンスで、オンプレミス サーバーを使用した復元操作からデータベースが作成された場合は、元のデータベースの TDE 設定が使用されます。 元のデータベースで TDE が有効になっていない場合は、マネージド インスタンスに対して手動で TDE を有効にすることをお勧めします。

**ベスト プラクティス**:

- 保存時の暗号化を必要とするデータをマスター データベースに格納しないでください。 マスター データベースは、TDE を使用して暗号化できません。

- TDE 保護に対して透過性を高め、きめ細かい制御を行う必要がある場合は、Azure Key Vault 内のカスタマー マネージド キーを使用します。 Azure Key Vault では、いつでもアクセス許可を取り消して、データベースをアクセス不可にすることができます。 TDE 保護機能を他のキーと共に中央で管理したり、Azure Key Vault を使用して独自のスケジュールで TDE 保護機能をローテーションしたりすることができます。

- Azure Key Vault でカスタマー マネージド キーを使用する場合は、[Azure Key Vault で TDE を構成するためのガイドライン](transparent-data-encryption-byok-overview.md#recommendations-when-configuring-akv)と [Azure Key Vault で Geo-DR を構成する方法](transparent-data-encryption-byok-overview.md#geo-dr-and-customer-managed-tde)に関する記事に従ってください。

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>高い特権を持つ承認されていないユーザーから、使用中の機密データを保護する

使用中のデータとは、SQL クエリの実行中にデータベース システムのメモリに格納されたデータです。 データベースに機密データが格納されている場合、組織は、特権の高いユーザーがデータベース内の機密データを表示できないようにすることが必要な場合があります。 組織の Microsoft オペレーターや DBA などの高い特権を持つユーザーは、データベースを管理できる必要がありますが、SQL プロセスのメモリからの機密データの表示や流出の可能性、データベースに対するクエリの実行を防止する必要があります。

どのデータが機密であるかや、機密データをメモリ内で暗号化して管理者がプレーンテキストで使用できないようにするかどうかを判定するポリシーは、お客様の組織とお客様が準拠する必要がある法令遵守規定に固有です。 関連する要件を確認してください （「[機密データを特定してタグを付ける](#identify-and-tag-sensitive-data)」）。

**実装方法**:

- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) を使用して、メモリ内にある場合や使用中であっても、機密データが Azure SQL Database または SQL Managed Instance にプレーンテキストで確実に公開されないようにします。 Always Encrypted により、データベース管理者 (DBA) とクラウド管理者 (または、高い特権を持つが未承認のユーザーを偽装できる悪意のあるアクター) からデータを保護し、データにアクセスできるユーザーをより細かく制御できます。

**ベスト プラクティス**:

- Always Encrypted は、保存データ (TDE) または転送中のデータ (SSL/TLS) の暗号化の代替ではありません。 パフォーマンスと機能への影響を最小限に抑えるため、機密データ以外のデータには Always Encrypted を使用しないでください。 保存時、転送中、使用中のデータを包括的に保護するには、TDE およびトランスポート層セキュリティ (TLS) と組み合わせて Always Encrypted を使用することをお勧めします。

- Always Encrypted を運用データベースにデプロイする前に、特定された機密データ列の暗号化による影響を評価します。 Always Encrypted では通常、暗号化された列でクエリの機能性が低下します。また、「[Always Encrypted」の「機能の詳細](/sql/relational-databases/security/encryption/always-encrypted-database-engine#feature-details)」に記載されているその他の制限事項があります。 そのため場合によっては、クエリでサポートされていない機能をクライアント側に再実装するためにアプリケーションを再設計したり、ストアド プロシージャ、関数、ビュー、トリガーなど、データベース スキーマをリファクターしたりする必要があります。 Always Encrypted の制約と制限に準拠していない既存のアプリケーションでは、暗号化された列を使用できない場合があります。 Always Encrypted がサポートされる Microsoft のツール、製品、サービスのエコシステムは拡大しつつありますが、それらの一部では暗号化された列を使用できません。 ワークロードの特性によっては、列の暗号化がクエリのパフォーマンスに影響を及ぼす場合もあります。

- Always Encrypted を使用して悪意のある DBA からデータを保護する場合は、役割の分離を使って Always Encrypted キーを管理します。 役割の分離を使用する場合、セキュリティ管理者が物理キーを作成します。 DBA は、データベースに物理キーを記述する列マスター キーと列暗号化キー メタデータ オブジェクトを作成します。 この処理中、セキュリティ管理者はデータベースにアクセスする必要はなく、DBA はプレーンテキストの物理キーにアクセスする必要はありません。
  - 詳細については、「[役割の分離を使用したキー管理](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation)」という記事を参照してください。

- 管理を容易にするために、列マスター キーを Azure Key Vault に格納します。 キー管理を難しくする Windows 証明書ストア (および一般的に、中央キー管理ソリューションとは対照的な分散キー ストア ソリューション) は使用しないようにしてください。

- 複数のキー (列マスター キーまたは列暗号化キー) を使用した場合のトレードオフについて、慎重に検討してください。 キー管理コストを削減するために、キーの数は少なく保つようにしてください。 通常、データベースごとに 1 つの列マスター キーと 1 つの列暗号化キーがあれば、(キー ローテーションの途中でなければ) 安定状態の環境では十分です。 複数のユーザー グループがあり、それぞれ異なるキーを使用し、異なるデータにアクセスする場合は、追加のキーが必要になる場合があります。  

- 列マスター キーは、コンプライアンス要件に従ってローテーションさせます。 列暗号化キーもローテーションする必要がある場合は、アプリケーションのダウンタイムを最小限に抑えるためにオンライン暗号化を使用することを検討してください。
  - 「[パフォーマンスと可用性に関する考慮事項](/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations)」という記事を参照してください。

- データの計算 (等値) をサポートする必要がある場合は、決定論的な暗号化を使用します。 それ以外の場合は、ランダム化された暗号化を使用します。 低エントロピのデータ セットや、公に知られているディストリビューションがあるデータ セットには、決定論的な暗号化を使用しないようにしてください。

- 第三者が同意なしにデータに合法的にアクセスすることが懸念される場合は、プレーンテキストのキーとデータにアクセスできるすべてのアプリケーションとツールが Microsoft Azure Cloud の外部で実行されるようにします。 キーにアクセスできなければ、暗号化をバイパスしない限り、第三者がデータの暗号化を解除することはできません。

- Always Encrypted では、キー (および保護されたデータ) への一時的アクセスの付与は容易にはサポートされません。 たとえば、キーを DBA と共有して、DBA が機密データおよび暗号化されたデータに対して何らかのクレンジング操作を実行できるようにする必要がある場合があります。 DBA からのデータへのアクセスを確実に取り消す唯一の方法は、データを保護する列暗号化キーと列マスター キーの両方を回転することですが、これはコストのかかる操作です。

- 暗号化された列のプレーンテキスト値にアクセスするには、ユーザーは、列を保護する列マスター キー (CMK) にアクセスできる必要があります。これは、CMK が保持されているキー ストアに構成されています。 また、ユーザーには、 **[列マスター キー定義を表示する]** と **[列暗号化キー定義を表示する]** のデータベース権限が必要です。

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>暗号化によってアプリケーション ユーザーの機密データへのアクセスを制御する

暗号化は、暗号化キーにアクセスできる特定のアプリケーション ユーザーのみがデータを表示また更新できるようにするための方法として使用できます。

**実装方法**:

- セル レベルの暗号化 (CLE) を使用します。 詳細については、「[データの列の暗号化](/sql/relational-databases/security/encryption/encrypt-a-column-of-data)」という記事を参照してください。
- Always Encrypted を使用してください。ただし、制限事項には注意してください。 制限事項を次に示します。

**ベスト プラクティス**

CLE を使用する場合:

- SQL のアクセス許可とロールを使用して、キーへのアクセスを制御します。

- データの暗号化には AES (AES 256 を推奨) を使用します。 RC4、DES、TripleDES などのアルゴリズムは非推奨であり、既知の脆弱性があるため、使用しないでください。

- 3DES の使用を回避するため、非対称キー/証明書 (パスワードではない) を使用して対称キーを保護します。

- エクスポート/インポート (bacpac ファイル) でセル レベルの暗号化を使用してデータベースを移行するときは注意してください。
  - データ移行時のキーの消失を回避する方法、およびその他のベスト プラクティスのガイダンスについては、「[Azure SQL Database でセルレベルの暗号化を使用するための推奨事項](/archive/blogs/sqlsecurity/recommendations-for-using-cell-level-encryption-in-azure-sql-database)」という記事を参照してください。

Always Encrypted が、基本的に、使用中の機密データを Azure SQL Database の高い特権を持つユーザー (クラウド オペレーター、DBA) から保護するように設計されていることに留意してください。「[高い特権を持つ承認されていないユーザーから、使用中の機密データを保護する](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users)」を参照してください。 Always Encrypted を使用してアプリケーション ユーザーからデータを保護する場合は、次の課題に注意してください。

- 既定で、Always Encrypted をサポートするすべての Microsoft クライアント ドライバーは、列暗号化キーのグローバル (アプリケーションごとに 1 つ) のキャッシュを保持しています。 クライアント ドライバーが列マスター キーを保持しているキー ストアに接続して、プレーンテキスト列の暗号化キーを取得すると、そのプレーンテキスト列の暗号化キーはキャッシュされます。 そのため、マルチユーザー アプリケーションのユーザーからデータを分離することは困難になります。 アプリケーションがキー ストア (Azure Key Vault など) と対話するときにエンド ユーザーを偽装すると、ユーザーのクエリによって列暗号化キーがキャッシュに取り込まれた後、同じキーを必要とするが、別のユーザーによってトリガーされる後続のクエリでは、キャッシュされたキーが使用されます。 ドライバーはキー ストアを呼び出さず、2 番目のユーザーが列暗号化キーにアクセスする権限を持っているかどうかはチェックされません。 その結果、ユーザーがキーへのアクセス権を持っていない場合でも、そのユーザーは暗号化されたデータを表示できます。 マルチユーザー アプリケーション内のユーザーを分離するには、列暗号化キーのキャッシュを無効にします。 キャッシュを無効にすると、パフォーマンスのオーバーヘッドが増えます。これは、データの暗号化または復号化の操作ごとに、ドライバーがキーストアに接続する必要があるためです。

### <a name="protect-data-against-unauthorized-viewing-by-application-users-while-preserving-data-format"></a>データ形式を維持しながら、アプリケーション ユーザーによる承認されていない表示からデータを保護する

承認されていないユーザーがデータを表示するのを防ぐもう 1 つの方法は、ユーザー アプリケーションが引き続きデータを処理および表示できるようにデータの型と形式を維持しながら、データを難読化またはマスクすることです。

**実装方法**:

- テーブルの列を難読化するには、[動的データ マスク](/sql/relational-databases/security/dynamic-data-masking)を使用します。

> [!NOTE]
> Always Encrypted は、動的データ マスクでは機能しません。 同じ列を暗号化してマスクすることはできません。つまり、使用中のデータを保護することと、動的データ マスクによってアプリ ユーザーのデータをマスクすることのどちらを優先するかを決める必要があります。

**ベスト プラクティス**:

> [!NOTE]
> 動的データ マスクを使用して、高い特権を持つユーザーからデータを保護することはできません。 マスク ポリシーは、db_owner のような管理者アクセス権を持つユーザーには適用されません。

- アプリ ユーザーにアドホック クエリの実行を許可しないでください (動的データ マスクを回避できる可能性があるからです)。  
  - 詳細については、「[推論またはブルートフォース手法を使用してマスクをバイパスする](/sql/relational-databases/security/dynamic-data-masking#security-note-bypassing-masking-using-inference-or-brute-force-techniques)」という記事を参照してください。  

- (SQL アクセス許可、ロール、RLS を介した) 適切なアクセス制御ポリシーを使用して、マスクされた列で更新を行うユーザー アクセス許可を制限します。 列にマスクを作成しても、その列への更新は防止されません。 マスクされた列のクエリを実行するときにマスクされたデータを受け取るユーザーは、書き込みアクセス許可を持っている場合はデータを更新できます。

- 動的データ マスクは、マスクされた値の統計的特性を保持しません。 これは、クエリ結果に影響する場合があります (たとえば、マスクされたデータのフィルター述語や結合を含むクエリ)。

## <a name="network-security"></a>ネットワークのセキュリティ

ネットワークのセキュリティとは、Azure SQL Database に転送中のデータをセキュリティで保護するためのアクセス制御とベスト プラクティスを指します。

### <a name="configure-my-client-to-connect-securely-to-sql-databasesql-managed-instance"></a>SQL Database または SQL Managed Instance に安全に接続するようにクライアントを構成する

既知の脆弱性 (たとえば、古い TLS プロトコルと暗号スイートを使用するなど) があるクライアント マシンとアプリケーションが Azure SQL Database および SQL Managed Instance に接続できないようにする方法のベスト プラクティス。

**実装方法**:

- Azure SQL Database および SQL Managed Instance に接続しているクライアント コンピューターでは、確実に [トランスポート層セキュリティ (TLS)](security-overview.md#transport-layer-security-encryption-in-transit) を使用します。

**ベスト プラクティス**:

- 暗号化を有効にしてすべてのアプリとツールを SQL Database に接続するように構成します
  - Encrypt = On、TrustServerCertificate = Off (または Microsoft 以外のドライバーでの同等のもの)。

- アプリが、TLS をサポートしていないドライバーを使用している場合や古いバージョンの TLS をサポートしている場合は、可能であればドライバーを置き換えます。 可能でない場合は、セキュリティ上のリスクを慎重に評価してください。

- [トランスポート層セキュリティ (TLS) レジストリ設定](/windows-server/security/tls/tls-registry-settings#tls-10)に従って、Azure SQL Database に接続しているクライアント コンピューターで SSL 2.0、SSL 3.0、TLS 1.0、および TLS 1.1 を無効にすることにより、それらの脆弱性による攻撃ベクトルを減らします。

- クライアント上で使用できる暗号スイートを確認します。[TLS/SSL (Schannel SSP) の暗号スイート](/windows/desktop/SecAuthN/cipher-suites-in-schannel)。 具体的には、[TLS の暗号スイートの順序の構成](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order)に関する記事に従って 3DES を無効にします。

- Azure SQL Database および SQL Managed Instance の場合、暗号化はプロキシとリダイレクトの両方の接続の種類に適用されます。 Azure SQL Managed Instance の場合は、 **[プロキシ]** の接続の種類 (既定) を使用します。これにより、サーバー側から暗号化が実施されます。 **[リダイレクト]** の接続の種類は、現在、暗号化の実施をサポートしておらず、プライベート IP 接続でのみ使用可能です。

- 詳細については、[Azure SQL Database の接続アーキテクチャに関するページの「接続ポリシー」](connectivity-architecture.md#connection-policy)を参照してください。

### <a name="minimize-attack-surface"></a>攻撃対象領域を最小限にする

悪意のあるユーザーから攻撃される可能性のある機能の数を最小限にします。 Azure SQL Database のネットワーク アクセス制御を実装します。

> 次で言及されています: OSA プラクティス #5

**実装方法**:

SQL Database:

- サーバー レベルで [Azure サービスへのアクセスを許可] を [オフ] に設定します。
- VNet サービス エンドポイントと VNet ファイアウォール規則を使用します。
- Private Link (プレビュー) を使用します。

SQL Managed Instance の場合:

- 「[ネットワークの要件](../managed-instance/connectivity-architecture-overview.md#network-requirements)」のガイドラインに従います。

**ベスト プラクティス**:

- (たとえば、プライベート データ パスを使用して) プライベート エンドポイントで接続することにより、Azure SQL Database および SQL Managed Instance へのアクセスを制限します。
  - マネージド インスタンスは、外部アクセスを防ぐために、仮想ネットワーク内で分離することができます。 同じリージョン内の同じまたはピアリングされた仮想ネットワークにあるアプリケーションとツールは、直接アクセスできます。 異なるリージョンにあるアプリケーションとツールは、仮想ネットワーク間接続や ExpressRoute 回線のピアリングを使用して接続を確立できます。 ユーザーは、ネットワーク セキュリティ グループ (NSG) を使用して、ポート 1433 でのアクセスを、マネージド インスタンスへのアクセスを必要とするリソースのみに制限する必要があります。
  - SQL Database については、仮想ネットワーク内のサーバーに専用のプライベート IP を提供する [Private Link](../../private-link/private-endpoint-overview.md) 機能を使用します。 また、[仮想ネットワークのファイアウォール規則と共に仮想ネットワーク サービス エンドポイント](vnet-service-endpoint-rule-overview.md)を使用して、ご使用のサーバーへのアクセスを制限することもできます。
  - モバイル ユーザーは、ポイント対サイト VPN 接続を使用して、データ パス経由で接続する必要があります。
  - オンプレミス ネットワークに接続されているユーザーは、サイト間 VPN 接続または ExpressRoute を使用して、データ パス経由で接続する必要があります。

- パブリック エンドポイントに接続することで (たとえば、パブリック データ パスを使用して) Azure SQL Database および SQL Managed Instance にアクセスできます。 次のベスト プラクティスを検討してください。
  - SQL Database のサーバーの場合は、[IP ファイアウォール規則](firewall-configure.md)を使用して、許可された IP アドレスのみにアクセスを制限します。
  - SQL Managed Instance の場合、ネットワーク セキュリティ グループ (NSG) を使用して、ポート 3342 でのアクセスを、必要なリソースのみに制限します。 詳細については、「[パブリック エンドポイントで安全にマネージド インスタンスを使用する](../managed-instance/public-endpoint-overview.md)」を参照してください。

> [!NOTE]
> SQL Managed Instance のパブリック エンドポイントは、既定では有効になっておらず、明示的に有効にする必要があります。 会社のポリシーでパブリック エンドポイントの使用が禁止されている場合は、最初に [Azure Policy](../../governance/policy/overview.md) を使用して、パブリック エンドポイントを有効にしないようにします。

- Azure のネットワーク コンポーネントを設定します。
  - 「[Azure のネットワーク セキュリティのベスト プラクティス](../../security/fundamentals/network-best-practices.md)」に従います。
  - 「[Azure Virtual Network のよく寄せられる質問 (FAQ)](../../virtual-network/virtual-networks-faq.md)」で説明されているベスト プラクティスと計画に従って、Virtual Network の構成を計画します。
  - 仮想ネットワークを複数のサブネットにセグメント化し、同様のロールのリソースを同じサブネット (たとえば、フロントエンドとバックエンドのリソース) に割り当てます。
  - [ネットワーク セキュリティ グループ (NSG)](../../virtual-network/network-security-groups-overview.md) を使用して、Azure 仮想ネットワーク境界内のサブネット間のトラフィックを制御します。
  - サブスクリプションで [Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) を有効にして、ネットワークの送受信トラフィックを監視します。

### <a name="configure-power-bi-for-secure-connections-to-sql-databasesql-managed-instance"></a>SQL Database または SQL Managed Instance へのセキュリティで保護された接続用に Power BI を構成する

**ベスト プラクティス**:

- Power BI Desktop の場合、可能な限りプライベート データ パスを使用します。

- [トランスポート層セキュリティ (TLS)](/windows-server/security/tls/tls-registry-settings) のレジストリ設定に従ってクライアント コンピューターのレジストリ キーを設定して、Power BI Desktop が TLS 1.2 を使用して接続していることを確認します。

- [Power BI での行レベル セキュリティ (RLS)](/power-bi/service-admin-rls) を使用して、特定のユーザーのデータ アクセスを制限します。

- Power BI サービスについては、[制限事項と考慮事項](/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway)に留意して、[オンプレミス データ ゲートウェイ](/power-bi/service-gateway-onprem)を使用します。

### <a name="configure-app-service-for-secure-connections-to-sql-databasesql-managed-instance"></a>SQL Database または SQL Managed Instance へのセキュリティで保護された接続用に App Service を構成する

**ベスト プラクティス**:

- 単純な Web アプリの場合、パブリック エンドポイント経由で接続するには、 **[Azure サービスを許可する]** を [オン] に設定する必要があります。

- マネージド インスタンスへのプライベート データ パスの接続のために、[アプリを Azure Virtual Network に統合](../../app-service/web-sites-integrate-with-vnet.md)します。 必要に応じて、[App Service Environment (ASE)](../../app-service/environment/intro.md) を使用して Web アプリをデプロイすることもできます。

- ASE を使用する Web アプリ、または SQL Database 内のデータベースに接続する仮想ネットワークに統合された Web アプリの場合、[仮想ネットワーク サービス エンドポイントと仮想ネットワーク ファイアウォール規則](vnet-service-endpoint-rule-overview.md)を使用して特定の仮想ネットワークとサブネットからのアクセスを制限することができます。 次に、 **[Azure サービスを許可する]** を [オフ] に設定します。 また、プライベート データ パス経由で ASE を SQL Managed Instance のマネージド インスタンスに接続することもできます。  

- 「[Azure App Service を使用してサービスとしてのプラットフォーム (PaaS) の Web アプリケーションを保護するベスト プラクティス](../../security/fundamentals/paas-applications-using-app-services.md)」の記事に従って Web アプリが構成されていることを確認します。

- Web アプリを一般的な悪用と脆弱性から保護するために、[Web アプリケーション ファイアウォール (WAF)](../../web-application-firewall/ag/ag-overview.md) をインストールします。

### <a name="configure-azure-virtual-machine-hosting-for-secure-connections-to-sql-databasesql-managed-instance"></a>SQL Database または SQL Managed Instance へのセキュリティで保護された接続用に Azure 仮想マシンのホスティングを構成する

**ベスト プラクティス**:

- Azure 仮想マシンの NSG で許可と拒否の規則を組み合わせて使用して、VM からアクセスできるリージョンを制御します。

- 「[Azure における IaaS ワークロードのセキュリティに関するベスト プラクティス](../../security/fundamentals/iaas.md)」という記事に従って VM が構成されていることを確認します。

- すべての VM が特定の仮想ネットワークおよびサブネットに関連付けられていることを確認します。

- 「[強制トンネリングについて](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling)」のガイダンスに従って、既定のルート 0.0.0.0/インターネットが必要かどうかを評価します。
  - 必要な場合 (たとえば、フロントエンド サブネット) は、既定のルートを保持します。
  - 不要な場合 (たとえば、中間層やバックエンド サブネット) は、強制トンネリングを有効にします。これにより、トラフィックがインターネットを経由してオンプレミスに到達する (つまり、クロスプレミスになる) ことはありません。

- ピアリングを使用しているか、オンプレミスに接続している場合は、[省略可能な既定のルート](../../virtual-network/virtual-networks-udr-overview.md#optional-default-routes)を実装します。

- パケット検査のために仮想ネットワーク内のすべてのトラフィックをネットワーク仮想アプライアンスに送信する必要がある場合は、[ユーザー定義ルート](../../virtual-network/virtual-networks-udr-overview.md#user-defined)を実装します。

- Azure のバックボーン ネットワーク経由で Azure Storage などの PaaS サービスに安全にアクセスするには、[仮想ネットワーク サービス エンドポイント](vnet-service-endpoint-rule-overview.md)を使用します。

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>分散型サービス拒否 (DDoS) 攻撃から保護する

分散型サービス拒否 (DDoS) 攻撃とは、悪意のあるユーザーが、Azure インフラストラクチャを過負荷にし、有効なログインおよびワークロードを拒否させる目的で、Azure SQL Database に大量のネットワーク トラフィックを送信しようとすることです。

> 次で言及されています: OSA プラクティス #9

**実装方法**:

DDoS 保護は、Azure プラットフォームの一部として自動的に有効になります。 これには、パブリック エンドポイントでのネットワーク レベル攻撃に対する常時オンのトラフィック監視とリアルタイム軽減策が含まれます。

- [Azure DDoS Protection](../../ddos-protection/ddos-protection-overview.md) を使用して、仮想ネットワークにデプロイされたリソースに関連付けられているパブリック IP アドレスを監視します。

- [SQL Database の Advanced Threat Protection](threat-detection-overview.md) を使用して、データベースに対するサービス拒否 (DoS) 攻撃を検出します。

**ベスト プラクティス**:

- 「[攻撃対象領域を最小限にする](#minimize-attack-surface)」で説明されているプラクティスに従うことで、DDoS 攻撃の脅威を最小限にすることができます。

- Advanced Threat Protection の **Brute force SQL credentials (SQL 資格情報に対するブルート フォース攻撃)** アラートは、ブルート フォース攻撃の検出に役立ちます。 場合によって、アラートは侵入テストのワークロードを区別することもできます。

- SQL Database に接続する Azure VM ホスティング アプリケーションの場合:
  - Azure Security Center でインターネットに接続するエンドポイント経由のアクセスを制限するための推奨事項に従ってください。
  - Azure VM でアプリケーションの複数インスタンスを実行するには、仮想マシン スケール セットを使用します。
  - ブルート フォース攻撃を防ぐために、インターネットからの RDP と SSH を無効にします。

## <a name="monitoring-logging-and-auditing"></a>監視、ログ、監査

このセクションでは、データベースへのアクセスや悪用を試行する、通常と異なる、害を及ぼす可能性のある試みを示唆する異常なアクティビティを検出するのに役立つ機能について説明します。 また、データベース イベントを追跡およびキャプチャするようにデータベース監査を構成するためのベスト プラクティスについても説明します。

### <a name="protect-databases-against-attacks"></a>データベースを攻撃から保護する

Advanced Threat Protection を使用すると、異常なアクティビティに対するセキュリティ アラートを提供することで、潜在的な脅威が発生したときにそれを検出して対応することができます。

**実装方法**:

- [Advanced Threat Protection for SQL](threat-detection-overview.md#alerts) を使用して、通常と異なる、害を及ぼす可能性のあるデータベースへのアクセスや悪用の試行を検出します。たとえば、次のようなものがあります。
  - SQL インジェクション攻撃。
  - 資格情報の盗難または漏洩。
  - 特権の悪用。
  - データ窃盗。

**ベスト プラクティス**:

- 特定のサーバーまたはマネージド インスタンス用に [Azure Defender for SQL](azure-defender-for-sql.md)  を構成します。 [Azure Security Center の Standard レベル](../../security-center/security-center-pricing.md)に切り替えて、サブスクリプション内のすべてのサーバーおよびマネージド インスタンス用に Azure Defender for SQL を構成することもできます。

- 完全な調査エクスペリエンスを実現するには、 [SQL Database Auditing](../../azure-sql/database/auditing-overview.md) を有効にすることをお勧めします。 監査を使用すると、データベース イベントを追跡し、Azure Storage アカウントまたは Azure Log Analytics ワークスペースの監査ログに書き込むことができます。

### <a name="audit-critical-security-events"></a>重要なセキュリティ イベントを監査する

データベース イベントを追跡すると、データベース アクティビティを理解するために役立ちます。 ビジネス上の懸念やセキュリティ違反の疑いを示す可能性のある不一致や異常について分析情報を得ることができます。 また、これにより、コンプライアンス基準への準拠を可能にし、促進します。

**実装方法**:

-  [SQL Database 監査](../../azure-sql/database/auditing-overview.md)または [Managed Instance の監査](../managed-instance/auditing-configure.md)を有効にしてデータベース イベントを追跡し、それらを Azure Storage アカウント、Log Analytics ワークスペース (プレビュー)、または Event Hubs (プレビュー) の監査ログに書き込みます。

- 監査ログは、Azure Storage アカウント、Log Analytics ワークスペース (Azure Monitor ログで使用)、またはイベント ハブ (イベント ハブで使用) に書き込むことができます。 これらのオプションは組み合わせて構成でき、それぞれの場所に監査ログが書き込まれます。

**ベスト プラクティス**:

- イベントを監査するためにサーバーまたは [Managed Instance Auditing](../managed-instance/auditing-configure.md) に [SQL Database 監査](../../azure-sql/database/auditing-overview.md)を構成すると、そのサーバー上の既存および新しく作成されたすべてのデータベースが監査されます。
- 既定で、監査ポリシーには、データベースに対するすべてのアクション (クエリ、ストアド プロシージャ、成功および失敗したログイン) が含まれます。その結果、大量の監査ログが生成される可能性があります。 [PowerShell を使用してさまざまな種類のアクションとアクション グループの監査を構成する](./auditing-overview.md#manage-auditing)ことをお勧めします。 これを構成すると、監査されるアクションの数を制御し、イベント損失のリスクを最小限に抑えることができます。 カスタムの監査構成を使うと、必要な監査データのみをキャプチャできます。
- 監査ログは、[Azure portal](https://portal.azure.com/) で、または構成されたストレージの場所から直接使用できます。

> [!NOTE]
> Log Analytics に対する監査を有効にすると、インジェストのレートに基づくコストが発生します。 この[オプション](https://azure.microsoft.com/pricing/details/monitor/)を使用した場合のコストを承知のうえで利用するか、または、監査ログを Azure ストレージ アカウントに格納することを検討してください。

**その他のリソース**:

- [SQL データベースの監査](../../azure-sql/database/auditing-overview.md)
- [SQL Server 監査](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="secure-audit-logs"></a>監査ログをセキュリティで保護する

ストレージ アカウントへのアクセスを制限して、職務の分離をサポートし、DBA と監査者を分離します。

**実装方法**:

- 監査ログを Azure Storage に保存するときは、ストレージ アカウントへのアクセスが最小限のセキュリティ原則に合わせて制限されていることを確認します。 ストレージ アカウントにアクセスできるユーザーを制御します。
- 詳細については、[Azure Storage へのアクセスの承認](../../storage/common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)に関する記事を参照してください。

**ベスト プラクティス**:

- 監査ターゲットへのアクセスを制御することは、DBA を監査者から分離する際の重要な概念です。

- 機密データへのアクセスを監査するときは、監査者への情報漏洩を避けるために、データの暗号化を使用してデータをセキュリティで保護することを検討してください。 詳細については、「[高い特権を持つ承認されていないユーザーから、使用中の機密データを保護する](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users)」セクションを参照してください。

## <a name="security-management"></a>セキュリティ管理

このセクションでは、データベースのセキュリティ体制を管理するためのさまざまな側面とベスト プラクティスについて説明します。 これには、データベースがセキュリティ基準を満たすよう構成されていることを確認するため、およびデータベース内の潜在的な機密データへのアクセスを検出、分類、および追跡するためのベスト プラクティスが含まれます。

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>セキュリティのベスト プラクティスに合うようにデータベースが構成されていることを確認する

潜在的なデータベースの脆弱性を検出して修正することにより、データベースのセキュリティを事前に強化します。

**実装方法**:

- [SQL 脆弱性評価](/sql/relational-databases/security/sql-vulnerability-assessment) (VA) を有効にしてデータベースのセキュリティ問題をスキャンし、データベースで定期的に自動実行されるようにします。

**ベスト プラクティス**:

- 最初に、データベース上で VA を実行し、セキュリティのベスト プラクティスに反する失敗したチェックを修正して繰り返します。 スキャンが "_クリーン_" になるか、すべてのチェックに合格するまで、許容される構成のベースラインを設定します。  

- 定期的な反復スキャンを 1 週間に 1 回実行するように構成し、関係者が概要のメールを受信するように構成します。

- 毎週スキャンするたびに、VA の概要を確認します。 見つかった脆弱性について、以前のスキャン結果からのドリフトを評価し、チェックを解決する必要があるかどうかを判断します。 構成の変更に正当な理由があるかどうかを確認します。

- チェックを解決し、関連するベースラインを更新します。 アクションを解決するためのチケット項目を作成し、それらが解決されるまで追跡します。

**その他のリソース**:

- [SQL 脆弱性評価](/sql/relational-databases/security/sql-vulnerability-assessment)
- [SQL 脆弱性評価サービスは、データベースの脆弱性を特定するのに役に立ちます](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>機密データを特定してタグを付ける

機密データが含まれている可能性がある列を検出します。 何が機密データとみなされるかは、お客様や遵守すべき規則などによって大きく異なり、そのデータを所管するユーザーによって評価される必要があります。 機密度に基づく高度な監査と保護のシナリオを使用するために、列を分類します。

**実装方法**:

- [SQL データの検出と分類](data-discovery-and-classification-overview.md)を使用して、データベース内の機密データを検出、分類、ラベル付け、および保護します。
  - [SQL Data Discovery and Classification]\(SQL データの検出と分類\) ダッシュボードで、自動検出によって作成された分類の推奨事項を確認します。 機密データに分類ラベルが永続的にタグ付けされるように、関連する分類を受け入れます。
  - 自動メカニズムによって検出されなかったその他の機密データ フィールドについては、手動で分類を追加します。
- 詳しくは、「[SQL データの検出と分類](/sql/relational-databases/security/sql-data-discovery-and-classification)」をご覧ください。

**ベスト プラクティス**:

- データベースの分類状態を正確に評価するために、分類ダッシュボードを定期的に監視します。 コンプライアンスと監査の目的で、データベースの分類状態に関するレポートをエクスポートまたは印刷して共有することができます。

- SQL 脆弱性評価で推奨される機密データの状態を継続的に監視します。 機密データの検出規則を追跡し、分類のための推奨列にドリフトがないか特定します。  

- 組織の特定のニーズに合った方法で分類を使用します。 Azure Security Center の [SQL Information Protection](../../security-center/security-center-info-protection-policy.md) ポリシーで Information Protection ポリシー (機密ラベル、情報の種類、検出ロジック) をカスタマイズします。

### <a name="track-access-to-sensitive-data"></a>機密データへのアクセスを追跡する

機密データにアクセスするユーザーを監視し、監査ログで機密データに対するクエリをキャプチャします。

**実装方法**:

- SQL 監査とデータ分類を組み合わせて使用します。
  - [SQL Database Audit](../../azure-sql/database/auditing-overview.md) ログで、特に機密データへのアクセスを追跡できます。 また、アクセスされたデータや機密ラベルなどの情報を表示することもできます。 詳細については、[データ検出と分類](data-discovery-and-classification-overview.md)に関するページと「[機密データへのアクセスの監査](data-discovery-and-classification-overview.md#audit-sensitive-data)」を参照してください。

**ベスト プラクティス**:

- 監査とデータ分類に関するセクションのベスト プラクティスを参照してください。
  - [重要なセキュリティ イベントを監査する](#audit-critical-security-events)
  - [機密データを特定してタグを付ける](#identify-and-tag-sensitive-data)

### <a name="visualize-security-and-compliance-status"></a>セキュリティとコンプライアンスの状態を視覚化する

データ センター (SQL Database のデータベースを含む) のセキュリティ体制を強化する、統一されたインフラストラクチャ セキュリティ管理システムを使用します。 データベースとコンプライアンス状態のセキュリティに関連する推奨事項の一覧を表示します。

**実装方法**:

- [Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) で、SQL に関連したセキュリティの推奨事項とアクティブな脅威を監視します。

## <a name="common-security-threats-and-potential-mitigations"></a>一般的なセキュリティ上の脅威と考えられる軽減策

このセクションは、特定の攻撃ベクトルから保護するためのセキュリティ対策を見つけるのに役立ちます。 多くの軽減策は、前述の 1 つ以上のセキュリティ ガイドラインに従うことによって実現できます。

### <a name="security-threat-data-exfiltration"></a>セキュリティの脅威:データ窃盗

データ窃盗とは、コンピューターやサーバーからの許可されていないデータのコピー、転送、または取得です。 Wikipedia にある「[データ窃盗](https://en.wikipedia.org/wiki/Data_exfiltration)」の定義を参照してください。

パブリック エンドポイント経由でサーバーに接続するとデータ窃盗のリスクが生じます。これは、ユーザーがパブリック IP に対してファイアウォールを開く必要があるからです。  

**シナリオ 1**: Azure VM 上のアプリケーションが Azure SQL Database 内のデータベースに接続します。 悪意のあるアクターが VM にアクセスして、不正に侵入します。 このシナリオにおけるデータ窃盗とは、承認されていない VM を使用する外部エンティティがデータベースに接続し、個人データをコピーし、それを BLOB ストレージや、別のサブスクリプションの別の SQL データベースに格納することを意味します。

**シナリオ 2**: 悪意のある DBA。 このシナリオは、規制対象業界のセキュリティに敏感なお客様によって提起されます。 このシナリオでは、高い特権を持つユーザーが、Azure SQL Database から、データ所有者によって制御されていない別のサブスクリプションにデータをコピーする可能性があります。

**考えられる軽減策**:

現在、Azure SQL Database および SQL Managed Instance では、データ窃盗の脅威を軽減するために次の方法が提供されています。

- Azure VM の NSG で許可と拒否の規則を組み合わせて使用して、VM からアクセスできるリージョンを制御します。
- SQL Database のサーバーを使用する場合は、次のオプションを設定します。
  - [Azure サービスを許可する] を [オフ]。
  - VNet ファイアウォール規則を設定して、Azure VM を含むサブネットからのトラフィックのみを許可します。
  - [Private Link](../../private-link/private-endpoint-overview.md) を使用します。
- SQL Managed Instance の場合、既定でプライベート IP アクセスを使用することで、承認されていない VM によるデータ窃盗の最大の懸念に対応します。 SQL Managed Instance のサブネットに最も制限の厳しいポリシーを自動的に設定するサブネットの委任機能をサブネットで有効にします。
- 悪意のある DBA の懸念は、SQL Managed Instance で、より顕在化します。これは、攻撃対象領域が広くなり、ネットワーク要件がユーザーにとって明白だからです。 これに対する最善の軽減策は、このセキュリティ ガイドに記載されているすべてのプラクティスを適用して、(データ窃盗のためだけでなく) 最初の段階で、悪意のある DBA のシナリオを防止することです。 Always Encrypted は、機密データを暗号化して、DBA がキーにアクセスできないようにして保護する 1 つの方法です。

## <a name="security-aspects-of-business-continuity-and-availability"></a>ビジネス継続性と可用性のセキュリティ面

多くのセキュリティ標準では、冗長性とフェールオーバーの機能を実装して単一障害点を回避することで実現される運用の継続性の観点からデータの可用性に対処しています。 災害シナリオでは、データおよびログ ファイルのバックアップを保持するのが一般的な方法です。 次のセクションでは、Azure に組み込まれている機能の概要を説明します。 また、特定のニーズに合わせて構成できるその他のオプションについても説明します。

- Azure には、次のような高可用性が組み込まれています。[SQL Database と SQL Managed Instance を使用した高可用性](high-availability-sla.md)

- Business Critical サービス レベルには、フェールオーバー グループ、完全および差分のログ バックアップ、および既定で有効になっているポイントインタイム リストアのバックアップが含まれます。  
  - [自動バックアップ](automated-backups-overview.md)
  - [データベースの自動バックアップを使用してデータベースを復旧する - ポイントインタイム リストア](recovery-using-backups.md#point-in-time-restore)

- さまざまな Azure geo 間でのゾーン冗長構成や自動フェールオーバー グループなど、追加のビジネス継続性機能を構成できます。 
    - [高可用性 - Premium および Business Critical のサービス レベル向けのゾーン冗長構成](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability)
    - [高可用性 - General Purpose サービス レベル向けのゾーン冗長構成](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
    - [ビジネス継続性の概要](business-continuity-high-availability-disaster-recover-hadr-overview.md)

## <a name="next-steps"></a>次のステップ

- 「[Azure SQL Database のセキュリティ機能の概要](security-overview.md)」を参照してください。