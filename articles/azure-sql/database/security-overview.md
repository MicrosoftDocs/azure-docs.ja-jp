---
title: セキュリティの概要
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database と Azure SQL Managed Instance のセキュリティについて、SQL Server との違いを含めて説明します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, emlisa
ms.date: 10/26/2020
ms.openlocfilehash: 39119f62fa938f5f4f6529539d4ca9a84bdf8fd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94989192"
---
# <a name="an-overview-of-azure-sql-database-and-sql-managed-instance-security-capabilities"></a>Azure SQL Database と SQL Managed Instance のセキュリティ機能の概要
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

この記事では、[Azure SQL Database](sql-database-paas-overview.md)、[Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)、[Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) を使用して、アプリケーションのデータ層をセキュリティで保護するための基本の概要を示します。 説明されているセキュリティ戦略では、下の図に示すように多層防御アプローチに従い、外側から内側に移動します。

![多層防御の図。 顧客データは、ネットワーク セキュリティ、アクセス管理、および脅威の防止と情報の保護のレイヤーに入れられています。](./media/security-overview/sql-security-layer.png)

## <a name="network-security"></a>ネットワークのセキュリティ

Microsoft Azure SQL Database、SQL Managed Instance、Azure Synapse Analytics では、クラウドおよびエンタープライズ アプリケーション用のリレーショナル データベース サービスを提供します。 顧客データを保護できるように、IP アドレスまたは Azure 仮想ネットワーク トラフィック元に基づいてアクセス権が明示的に付与されるまで、ファイアウォールによってサーバーへのネットワーク アクセスが遮断されます。

### <a name="ip-firewall-rules"></a>IP ファイアウォール規則

IP ファイアウォール規則では、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。 詳細については、[Azure SQL Database と Azure Synapse Analytics のファイアウォール規則](firewall-configure.md)に関するページを参照してください。

### <a name="virtual-network-firewall-rules"></a>仮想ネットワーク ファイアウォールの規則

[仮想ネットワーク サービス エンドポイント](../../virtual-network/virtual-network-service-endpoints-overview.md)では、Azure バックボーン経由で仮想ネットワーク接続を拡張し、Azure SQL Database でトラフィック元の仮想ネットワーク サブネットを識別できるようにします。 Azure SQL Database にトラフィックが到達できるようにするには、SQL の[サービス タグ](../../virtual-network/network-security-groups-overview.md)を使用して、ネットワーク セキュリティ グループを介したアウトバウンド トラフィックを許可します。

[仮想ネットワーク規則](vnet-service-endpoint-rule-overview.md)を使用すると、Azure SQL Database は、仮想ネットワーク内の選択されたサブネットから送信される通信のみを受け入れるようにすることができます。

> [!NOTE]
> ファイアウォール規則でのアクセスの制御は、**SQL Managed Instance** には適用され "*ません*"。 必要なネットワーク構成について詳しくは、「[マネージド インスタンスへの接続](../managed-instance/connect-application-instance.md)」を参照してください

## <a name="access-management"></a>アクセス管理

> [!IMPORTANT]
> Azure 内でのデータベースとサーバーの管理は、ポータル ユーザー アカウントのロール割り当てによって制御されます。 この記事の詳細については、[Azure portal での Azure ロールベースのアクセス制御](../../role-based-access-control/overview.md)に関する記事を参照してください。

### <a name="authentication"></a>認証

認証は、ユーザーが本人の主張どおりの人物であることを証明するプロセスです。 Azure SQL Database と SQL Managed Instance では、次の 2 種類の認証がサポートされています。

- **SQL 認証**:

    SQL 認証とは、ユーザーがユーザー名とパスワードを使用して Azure SQL Database または Azure SQL Managed Instance に接続するときに、そのユーザーを認証することを指します。 サーバーの作成時には、ユーザー名とパスワードを使用して **サーバー管理者** ログインを指定する必要があります。 これらの資格情報を使用することで、**サーバー管理者** はデータベース所有者として、そのサーバーまたはインスタンスにある任意のデータベースを認証できます。 その後、サーバー管理者は、追加の SQL ログインとユーザーを作成できます。これにより、ユーザーはユーザー名とパスワードを使用して接続できるようになります。

- **Azure Active Directory 認証**:

    Azure Active Directory 認証は、Azure Active Directory (Azure AD) の ID を使用して [Azure SQL Database](sql-database-paas-overview.md)、[Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)、[Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) に接続するメカニズムです。 Azure AD 認証では、管理者は、データベース ユーザーの ID とアクセス許可を他の Azure サービスと共に一元管理できます。 これにはパスワード ストレージの最小化が含まれます。また、これにより一元化されたパスワードのローテーション ポリシーが有効になります。

     SQL Database で Azure AD 認証を使用するには、**Active Directory 管理者** と呼ばれるサーバー管理者を作成する必要があります。 詳細については、[Azure Active Directory 認証を使用した SQL Database への接続](authentication-aad-overview.md)に関するページを参照してください。 Azure AD 認証では、マネージド アカウントとフェデレーション アカウントの両方がサポートされます。 フェデレーション アカウントでは、Azure AD とフェデレーションされた顧客ドメインの Windows ユーザーとグループがサポートされます。

    使用できる追加の Azure AD 認証オプションには、[多要素認証](../../active-directory/authentication/concept-mfa-howitworks.md)と[条件付きアクセス](conditional-access-configure.md)を含む、[SQL Server Management Studio 用の Active Directory ユニバーサル認証](authentication-mfa-ssms-overview.md)接続があります。

> [!IMPORTANT]
> Azure 内でのデータベースとサーバーの管理は、ポータル ユーザー アカウントのロール割り当てによって制御されます。 この記事の詳細については、[Azure portal での Azure ロールベースのアクセス制御](../../role-based-access-control/overview.md)に関する記事を参照してください。 ファイアウォール規則でのアクセスの制御は、**SQL Managed Instance** には適用され "*ません*"。 必要なネットワーク構成の詳細については、[マネージド インスタンスへの接続](../managed-instance/connect-application-instance.md)に関する次の記事をご覧ください。

## <a name="authorization"></a>承認

承認は、Azure SQL Database のデータベースまたは Azure SQL Managed Instance 内のユーザーに割り当てられるアクセス許可を指し、ユーザーが実行できる操作を決定するものです。 アクセス許可を制御するには、[データベース ロール](/sql/relational-databases/security/authentication-access/database-level-roles)にユーザー アカウントを追加してデータベース レベルのアクセス許可をこれらのロールに割り当てるか、特定の[オブジェクト レベルのアクセス許可](/sql/relational-databases/security/permissions-database-engine)をユーザーに付与することができます。 詳細については、[ログインとユーザー](logins-create-manage.md)に関するページを参照してください

ベスト プラクティスとして、必要なときにカスタム ロールを作成することをお勧めします。 職務に必要な最小限の特権を持つロールにユーザーを追加してください。 ユーザーにアクセス許可を直接割り当てないでください。 サーバー管理者アカウントは、広範なアクセス許可を持つ組み込み db_owner ロールのメンバーです。管理業務を行うごく少数のユーザーにのみ、これを付与してください。 アプリケーションでは、呼び出されるモジュールの実行コンテキストを指定するために [EXECUTE AS](/sql/t-sql/statements/execute-as-clause-transact-sql) を使用するか、限定的なアクセス許可を持つ[アプリケーション ロール](/sql/relational-databases/security/authentication-access/application-roles)を使用します。 こうすると、データベースに接続するアプリケーションに、そのアプリケーションで必要な最小限の特権が確実に付与されます。 また、このようなベスト プラクティスに従うことで、職務の分離も促進されます。

### <a name="row-level-security"></a>行レベルのセキュリティ

行レベル セキュリティにより、顧客はクエリを実行するユーザーの特性に基づき、データベース テーブルの行へのアクセスを制御できます (たとえば、グループのメンバーシップや実行コンテキストなど)。 また、行レベル セキュリティを使用して、ラベルに基づくカスタム セキュリティ概念を実装することもできます。 詳細については、「[行レベルのセキュリティ](/sql/relational-databases/security/row-level-security)」を参照してください。

![行レベルのセキュリティにより、SQL データベースの個々の行が、クライアントアプリ経由でのユーザーによるアクセスから保護されていることを示す図。](./media/security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>脅威の防止

SQL Database と SQL Managed Instance では、監査と脅威検出機能を提供することで、顧客データをセキュリティで保護します。

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>Azure Monitor ログと Event Hubs での SQL 監査

SQL Database と SQL Managed Instance の監査はデータベース アクティビティを追跡し、顧客が所有する Azure ストレージ アカウントの監査ログにデータベース イベントを記録することによって、セキュリティ標準のコンプライアンスを維持できるようにします。 Auditing を使用することで、ユーザーは進行中のデータベース アクティビティを監視し、過去のアクティビティを分析および調査して、潜在的な脅威や疑わしい不正使用、およびセキュリティ違反を特定することができます。 詳細については、「[SQL Database 監査の使用](../../azure-sql/database/auditing-overview.md)」を参照してください。  

### <a name="advanced-threat-protection"></a>高度な脅威保護

Advanced Threat Protection では、ログを分析し、通常とは異なる動作と、データベースへのアクセスまたは悪用を試みる有害な可能性がある試行を検出します。 SQL インジェクション、潜在的なデータ侵入、ブルートフォース攻撃などの疑わしいアクティビティや、特権の昇格と不正な資格情報の使用を検出するアクセス パターンの異常に対してアラートが作成されます。 アラートは、[Azure Security Center](https://azure.microsoft.com/services/security-center/) で表示されます。ここでは、疑わしいアクティビティの詳細が提供され、また、脅威を軽減するためのアクションと共に、より詳しい調査のためのレコメンデーションが示されます。 Advanced Threat Protection は追加料金でサーバーごとに有効にすることができます。 詳細については、[SQL Database の Advanced Threat Protection の概要](threat-detection-configure.md)に関する記事を参照してください。

![外部の攻撃者や悪意のある内部関係者による Web アプリの SQL データベースへのアクセスを監視する SQL 脅威の検出を示す図。](./media/security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>情報の保護と暗号化

### <a name="transport-layer-security-encryption-in-transit"></a>トランスポート層セキュリティ (転送中の暗号化)

SQL Database、SQL Managed Instance、Azure Synapse Analytics では、[トランスポート層セキュリティ (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) を使用して、移動中のデータを暗号化することで、顧客データをセキュリティで保護します。

SQL Database、SQL Managed Instance、Azure Synapse Analytics では、すべての接続に対して常に暗号化 (SSL/TLS) が適用されます。 これにより、接続文字列での **Encrypt** または **TrustServerCertificate** の設定に関係なく、すべてのデータがクライアントとサーバー間の "移動中" に暗号化されることが保証されます。

ベスト プラクティスとして、アプリケーションで使用される接続文字列に、暗号化接続を指定し、サーバー証明書を信頼 _**しない**_ ことをお勧めします。 これにより、アプリケーションはサーバー証明書を強制的に検証するため、アプリケーションの man in the middle 攻撃に対する脆弱性を防ぎます。

たとえば、ADO.NET ドライバーを使用している場合、これは、**Encrypt=True** と **TrustServerCertificate=False** によって達成されます。 Azure portal から接続文字列を取得する場合は、正しい設定になっています。

> [!IMPORTANT]
> Microsoft 以外のドライバーは、機能するために、既定で TLS を使用しないか、または TLS の以前のバージョン (1.2 未満) に依存している場合があります。 この場合も、サーバーは引き続きデータベースへの接続を許可します。 ただし、特に機密データを格納する場合には、そのようなドライバーとアプリケーションによる SQL Database への接続許可について、セキュリティ上のリスクを評価することをお勧めします。
>
> TLS と接続の詳細については、[TLS に関する考慮事項](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)に関するセクションを参照してください

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transparent Data Encryption (保存時の暗号化)

[SQL Database、SQL Managed Instance、Azure Synapse Analytics の Transparent Data Encryption (TDE)](transparent-data-encryption-tde-overview.md) ではセキュリティ層が追加されており、生ファイルまたはバックアップへの未認可またはオフライン アクセスから保存データを保護するのに役立ちます。 一般的なシナリオには、ディスク ドライブやバックアップ テープなどのハードウェアまたはメディアの、データ センターでの盗用や非セキュアな破棄が含まれます。 TDE では、アプリケーション開発者が既存のアプリケーションを変更する必要がない、AES 暗号化アルゴリズムを使用して、データベース全体を暗号化します。

Azure では、新しく作成されたすべてのデータベースが既定で暗号化され、データベース暗号化キーは組み込まれているサーバー証明書によって保護されます。  証明書のメンテナンスとローテーションはサービスによって管理され、ユーザーによる入力は必要ありません。 暗号化キーの制御を望まれるお客様は、[Azure Key Vault](../../key-vault/general/secure-your-key-vault.md) でキーを管理できます。

### <a name="key-management-with-azure-key-vault"></a>Azure Key Vault のキー管理

 [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) の [Bring Your Own Key](transparent-data-encryption-byok-overview.md) (BYOK) サポートを利用することで、お客様は、 [Azure Key Vault](../../key-vault/general/secure-your-key-vault.md) (Azure のクラウドベースの外部キー管理システム) を使用して、キーの管理とローテーションの所有権を取得できます。 キー コンテナーへのデータベースのアクセスが取り消された場合、データベースを暗号化解除して、メモリに読み込むことはできません。 Azure Key Vault ではキーの一元管理プラットフォームを提供し、厳しく監視されたハードウェア セキュリティ モジュール (HSM) を利用します。また、キー管理とデータ管理の職務の分離を可能にすることで、セキュリティ コンプライアンス要件の遵守を支援します。

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (使用時の暗号化)

![Always Encrypted 機能の基本を示す図。 ロックを使用する SQL データベースへは、キーが含まれるアプリからのみアクセスできます。](./media/security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) は、特定のデータベース列に格納された機微なデータ (クレジット カード番号、国民識別番号、_知る必要性_ に基づくデータなど) がアクセスされないようにすることを目的とした機能です。 これには、データベース管理者や、管理タスクを実行するためにデータベースへのアクセスを許可されているものの、暗号化された列の特定のデータには業務上、アクセスする必要がない他の特権ユーザーが含まれます。 データは常に暗号化されます。つまり、暗号化されたデータは、暗号化キーにアクセスできるクライアント アプリケーションによって処理される場合にのみ、暗号化解除されます。 暗号化キーは SQL Database または SQL Managed Instance に公開されることはなく、[Windows 証明書ストア](always-encrypted-certificate-store-configure.md)または [Azure Key Vault](always-encrypted-azure-key-vault-configure.md) に格納できます。

### <a name="dynamic-data-masking"></a>動的データ マスク

![動的データ マスキングを示す図。 データは、ビジネス アプリによって SQL Database に送信され、そこでマスキングされてから、ビジネス アプリに戻されます。](./media/security-overview/azure-database-ddm.png)

動的データ マスクは、特権のないユーザーに対してデリケートなデータをマスクし、データの公開を制限します。 動的データ マスクは、Azure SQL Database と SQL Managed Instance の中で機密の可能性があるデータを自動的に検出し、アプリケーション層への影響を最小限に抑えながらそれらのフィールドをマスクするための具体的な推奨事項を提示します。 指定されたデータベース フィールドに対するクエリの結果セットに含まれる機密データを難読化しますが、データベース内のデータは変更しません。 詳細については、[SQL Database と SQL Managed Instance の動的データ マスク](dynamic-data-masking-overview.md)に関するページを参照してください。

## <a name="security-management"></a>セキュリティ管理

### <a name="vulnerability-assessment"></a>脆弱性評価

[脆弱性評価](sql-vulnerability-assessment.md)では、全体的なデータベース セキュリティを積極的に向上させる目的で、データベースの潜在的な脆弱性を検出、追跡、修復できるサービスを簡単に構成できます。 脆弱性評価 (VA) は、高度な SQL セキュリティ機能の統合パッケージである Azure Defender for SQL オファリングの一部です。 脆弱性評価は、Azure Defender for SQL ポータルを使用して、一元的にアクセスおよび管理できます。

### <a name="data-discovery-and-classification"></a>データの検出と分類

データの検出と分類 (現在プレビュー段階) には、データベース内の機密データの検出、分類、ラベル付け、および保護を行うための、Azure SQL Database と SQL Managed Instance に組み込まれた高度な機能が用意されています。 最も機微なデータ (ビジネス/金融、医療、個人データなど) の検出と分類は、組織の情報保護水準において極めて重要な役割を果たします。 次のような場合にインフラストラクチャとして使用できます。

- さまざまなセキュリティ シナリオ (機微なデータに対する異常なアクセスの監視 (監査) とアラートなど)。
- 非常に機微なデータを含むデータベースへのアクセスの制御と、セキュリティの強化。
- データのプライバシー基準および規制のコンプライアンス要件を満たす支援。

詳細については、「[データの検出と分類の概要](data-discovery-and-classification-overview.md)」を参照してください。

### <a name="compliance"></a>コンプライアンス

アプリケーションでさまざまなセキュリティ要件を満たすのに役立つ上記の機能以外にも、Azure SQL Database では定期的な監査に参加し、さまざまなコンプライアンス基準に認定されています。 詳細については、[Microsoft Azure Trust Center](https://www.microsoft.com/trust-center/compliance/compliance-overview)に関するページを参照してください。ここから最新の SQL Database コンプライアンス証明書の一覧を入手できます。

## <a name="next-steps"></a>次のステップ

- SQL Database と SQL Managed Instance でのログイン、ユーザー アカウント、データベース ロール、およびアクセス許可の使用については、[ログインとユーザー アカウントの管理](logins-create-manage.md)に関するページをご覧ください。
- データベース監査については、[監査](../../azure-sql/database/auditing-overview.md)に関するページをご覧ください。
- 脅威の検出については、[脅威の検出](threat-detection-configure.md)に関するページをご覧ください。