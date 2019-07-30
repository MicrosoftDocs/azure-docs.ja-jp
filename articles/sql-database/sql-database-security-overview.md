---
title: Azure SQL Database のセキュリティの概要 | Microsoft Docs
description: Azure SQL Database と SQL Server のセキュリティについて説明します。クラウドとオンプレミス SQL Server の違いなどが含まれます。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto, carlrab, emlisa
manager: craigg
ms.date: 05/14/2019
ms.openlocfilehash: 6b9a5ca350377777beebda24e52bc678c976ad19
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070197"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Azure SQL Database のセキュリティ機能の概要

この記事では、Azure SQL Database を使用して、アプリケーションのデータ層をセキュリティで保護するための基本の概要を示します。 説明されているセキュリティ戦略では、下の図に示すように多層防御アプローチに従い、外側から内側に移動します。

![sql-security-layer.png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>ネットワークのセキュリティ

Microsoft Azure SQL Database では、クラウドおよびエンタープライズ アプリケーション用のリレーショナル データベース サービスを提供します。 顧客データを保護できるように、IP アドレスまたは Azure 仮想ネットワーク トラフィック元に基づいてアクセス権が明示的に付与されるまで、ファイアウォールによってデータベース サーバーへのネットワーク アクセスが遮断されます。

### <a name="ip-firewall-rules"></a>IP ファイアウォール規則

IP ファイアウォール規則では、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。 詳細については、[Azure SQL Database および SQL Data Warehouse ファイアウォール規則の概要](sql-database-firewall-configure.md)に関するページを参照してください。

### <a name="virtual-network-firewall-rules"></a>仮想ネットワーク ファイアウォールの規則

[仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)では、Azure バックボーン経由で仮想ネットワーク接続を拡張し、Azure SQL Database でトラフィック元の仮想ネットワーク サブネットを識別できるようにします。 Azure SQL Database にトラフィックが到達できるようにするには、SQL の[サービス タグ](../virtual-network/security-overview.md)を使用して、ネットワーク セキュリティ グループを介したアウトバウンド トラフィックを許可します。

[仮想ネットワーク規則](sql-database-vnet-service-endpoint-rule-overview.md)を使用すると、Azure SQL Database は、仮想ネットワーク内の選択されたサブネットから送信される通信のみを受け入れることができます。

> [!NOTE]
> ファイアウォール規則でのアクセスの制御は、**マネージド インスタンス**には適用され*ません*。 必要なネットワーク構成について詳しくは、「[マネージド インスタンスへの接続](sql-database-managed-instance-connect-app.md)」を参照してください

## <a name="access-management"></a>アクセス管理

> [!IMPORTANT]
> Azure 内でのデータベースとデータベース サーバーの管理は、ポータル ユーザー アカウントのロール割り当てによって制御されます。 この記事の詳細については、「[Azure Portal でのロール ベースのアクセス制御](../role-based-access-control/overview.md)」を参照してください。

### <a name="authentication"></a>Authentication

認証は、ユーザーが本人の主張どおりの人物であることを証明するプロセスです。 Azure SQL Database では、次の 2 種類の認証がサポートされます。

- **SQL 認証**:

    SQL データベース認証とは、ユーザーがユーザー名とパスワードを使用して [Azure SQL Database](sql-database-technical-overview.md) に接続するときに、そのユーザーを認証することです。 データベース用のデータベース サーバーの作成時に、ユーザー名とパスワードでの "サーバー管理者" ログインを指定する必要があります。 これらの資格情報を使用することで、"サーバー管理者" はデータベース所有者として、そのデータベース サーバーにある任意のデータベースを認証できます。 その後、サーバー管理者は、追加の SQL ログインとユーザーを作成できます。これにより、ユーザーはユーザー名とパスワードを使用して接続できるようになります。

- **Azure Active Directory 認証**:

    Azure Active Directory 認証は、Azure Active Directory (Azure AD) の ID を使用して [Azure SQL Database](sql-database-technical-overview.md) と [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) に接続するメカニズムです。 Azure AD 認証では、管理者は、データベース ユーザーの ID とアクセス許可を他の Microsoft サービスと共に一元管理できます。 これにはパスワード ストレージの最小化が含まれます。また、これにより一元化されたパスワードのローテーション ポリシーが有効になります。

     SQL Database で Azure AD 認証を使用するには、**Active Directory 管理者**と呼ばれるサーバー管理者を作成する必要があります。 詳細については、[Azure Active Directory 認証を使用した SQL Database への接続](sql-database-aad-authentication.md)に関するページを参照してください。 Azure AD 認証では、マネージド アカウントとフェデレーション アカウントの両方がサポートされます。 フェデレーション アカウントでは、Azure AD とフェデレーションされた顧客ドメインの Windows ユーザーとグループがサポートされます。

    使用できる追加の Azure AD 認証オプションには、[多要素認証](../active-directory/authentication/concept-mfa-howitworks.md)と[条件付きアクセス](sql-database-conditional-access.md)を含む、[SQL Server Management Studio 用の Active Directory ユニバーサル認証](sql-database-ssms-mfa-authentication.md)接続があります。

> [!IMPORTANT]
> Azure 内でのデータベースとサーバーの管理は、ポータル ユーザー アカウントのロール割り当てによって制御されます。 この記事の詳細については、「[Azure Portal でのロール ベースのアクセス制御](../role-based-access-control/overview.md)」を参照してください。 ファイアウォール規則でのアクセスの制御は、**マネージド インスタンス**には適用され*ません*。 必要なネットワーク構成について詳しくは、[マネージド インスタンスへの接続](sql-database-managed-instance-connect-app.md)に関する次の記事をご覧ください。

## <a name="authorization"></a>Authorization

承認は、Azure SQL Database 内のユーザーに割り当てられるアクセス許可を指し、ユーザーが実行できる操作を決定するものです。 アクセス許可を制御するには、[データベース ロール](/sql/relational-databases/security/authentication-access/database-level-roles)にユーザー アカウントを追加してデータベース レベルのアクセス許可をこれらのロールに割り当てるか、特定の[オブジェクト レベルのアクセス許可](/sql/relational-databases/security/permissions-database-engine)をユーザーに付与することができます。 詳細については、[ログインとユーザー](sql-database-manage-logins.md)に関するページを参照してください

ベスト プラクティスとして、必要なときにカスタム ロールを作成することをお勧めします。 職務に必要な最小限の特権を持つロールにユーザーを追加してください。 ユーザーにアクセス許可を直接割り当てないでください。 サーバー管理者アカウントは、広範なアクセス許可を持つ組み込み db_owner ロールのメンバーです。管理業務を行うごく少数のユーザーにのみ、これを付与してください。 Azure SQL Database アプリケーションでは、呼び出されるモジュールの実行コンテキストを指定するために [EXECUTE AS](/sql/t-sql/statements/execute-as-clause-transact-sql) を使用するか、限定的なアクセス許可を持つ[アプリケーション ロール](/sql/relational-databases/security/authentication-access/application-roles)を使用します。 こうすると、データベースに接続するアプリケーションに、そのアプリケーションで必要な最小限の特権が確実に付与されます。 また、このようなベスト プラクティスに従うことで、職務の分離も促進されます。

### <a name="row-level-security"></a>行レベルのセキュリティ

行レベルのセキュリティを使用して、クエリを実行しているユーザーの特性 (グループのメンバーシップや実行コンテキストなど) に基づいて、データベース テーブル内の行へのアクセスを制御できます。 また、行レベル セキュリティを使用して、ラベルに基づくカスタム セキュリティ概念を実装することもできます。 詳細については、「[行レベルのセキュリティ](/sql/relational-databases/security/row-level-security)」を参照してください。

![azure-database-rls.png](media/sql-database-security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>脅威の防止

SQL Database では、監査と脅威検出機能を提供することで、顧客データをセキュリティで保護します。

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>Azure Monitor ログと Event Hubs での SQL 監査

SQL Database Auditing はデータベース アクティビティを追跡し、顧客が所有する Azure ストレージ アカウントの監査ログにデータベース イベントを記録することによって、セキュリティ標準のコンプライアンスを維持できるようにします。 Auditing を使用することで、ユーザーは進行中のデータベース アクティビティを監視し、過去のアクティビティを分析および調査して、潜在的な脅威や疑わしい不正使用、およびセキュリティ違反を特定することができます。 詳細については、「[SQL Database 監査の使用](sql-database-auditing.md)」を参照してください。  

### <a name="advanced-threat-protection"></a>高度な脅威保護

Advanced Threat Protection では、SQL Server ログを分析し、通常とは異なる動作と、データベースへのアクセスまたは悪用を試みる有害な可能性がある試行を検出します。 SQL インジェクション、潜在的なデータ侵入、ブルートフォース攻撃などの疑わしいアクティビティや、特権の昇格と不正な資格情報の使用を検出するアクセス パターンの異常に対してアラートが作成されます。 アラートは、[Azure Security Center](https://azure.microsoft.com/services/security-center/) で表示されます。ここでは、疑わしいアクティビティの詳細が提供され、また、脅威を軽減するためのアクションと共に、より詳しい調査のためのレコメンデーションが示されます。 Advanced Threat Protection は追加料金でサーバーごとに有効にすることができます。 詳細については、[SQL Database の Advanced Threat Protection の概要](sql-database-threat-detection.md)に関する記事を参照してください。

![azure-database-td.jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>情報の保護と暗号化

### <a name="transport-layer-security-tls-encryption-in-transit"></a>トランスポート層セキュリティ TLS (転送中の暗号化)

SQL Database では、[トランスポート層セキュリティ](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)を使用して、移動中のデータを暗号化することで、顧客データをセキュリティで保護します。

SQL Server では、すべての接続に対して常に暗号化 (SSL/TLS) が適用されます。 これにより、接続文字列での **Encrypt** または **TrustServerCertificate** の設定に関係なく、すべてのデータがクライアントとサーバー間の "移動中" に暗号化されることが保証されます。

ベスト プラクティスとして、アプリケーションの接続文字列に、暗号化接続を指定し、サーバー証明書を信頼 _**しない**_ ことをお勧めします。 これにより、アプリケーションはサーバー証明書を強制的に検証するため、アプリケーションの man in the middle 攻撃に対する脆弱性を防ぎます。

たとえば、ADO.NET ドライバーを使用している場合、これは、**Encrypt=True** と **TrustServerCertificate=False** によって達成されます。 Azure portal から接続文字列を取得する場合は、正しい設定になっています。

> [!IMPORTANT]
> Microsoft 以外のドライバーは、機能するために、既定で TLS を使用しないか、または TLS の以前のバージョン (1.2 未満) に依存している場合があります。 この場合も、SQL Server は引き続きデータベースへの接続を許可します。 ただし、特に機密データを格納する場合に、そのようなドライバーとアプリケーションの SQL Database への接続を許可することのセキュリティ上のリスクを評価することをお勧めします。 
>
> TLS と接続の詳細については、[TLS に関する考慮事項](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)に関するセクションを参照してください

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transparent Data Encryption (保存時の暗号化)

[Azure SQL Database の Transparent Data Encryption (TDE)](transparent-data-encryption-azure-sql.md) ではセキュリティ層が追加されており、生ファイルまたはバックアップへの未承認またはオフライン アクセスから保存データを保護するのに役立ちます。 一般的なシナリオには、ディスク ドライブやバックアップ テープなどのハードウェアまたはメディアの、データセンターでの盗用や非セキュアな破棄が含まれます。 TDE では、アプリケーション開発者が既存のアプリケーションを変更する必要がない、AES 暗号化アルゴリズムを使用して、データベース全体を暗号化します。

Azure では、新しく作成されたすべての SQL データベースが既定で暗号化され、データベース暗号化キーは組み込まれているサーバー証明書によって保護されます。  証明書のメンテナンスとローテーションはサービスによって管理され、ユーザーによる入力は必要ありません。 暗号化キーの制御を望まれるお客様は、[Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) でキーを管理できます。

### <a name="key-management-with-azure-key-vault"></a>Azure Key Vault のキー管理

 [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) の [Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md) (BYOK) サポートを利用することで、お客様は、 [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) (Azure のクラウドベースの外部キー管理システム) を使用して、キーの管理とローテーションの所有権を取得できます。 キー コンテナーへのデータベースのアクセスが取り消された場合、データベースを暗号化解除して、メモリに読み込むことはできません。 Azure Key Vault ではキーの一元管理プラットフォームを提供し、厳しく監視されたハードウェア セキュリティ モジュール (HSM) を利用します。また、キー管理とデータ管理の職務の分離を可能にすることで、セキュリティ コンプライアンス要件の遵守を支援します。

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (使用時の暗号化)

![azure-database-ae.png](media/sql-database-security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) は、特定のデータベース列に格納された機微なデータ (クレジット カード番号、国民識別番号、_知る必要性_ に基づくデータなど) がアクセスされないようにすることを目的とした機能です。 これには、データベース管理者や、管理タスクを実行するためにデータベースへのアクセスを許可されているものの、暗号化された列の特定のデータには業務上、アクセスする必要がない他の特権ユーザーが含まれます。 データは常に暗号化されます。つまり、暗号化されたデータは、暗号化キーにアクセスできるクライアント アプリケーションによって処理される場合にのみ、暗号化解除されます。  暗号化キーは SQL に公開されることはなく、[Windows 証明書ストア](sql-database-always-encrypted.md)または [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) に格納できます。

### <a name="dynamic-data-masking"></a>動的データ マスク

![azure-database-ddm.png](media/sql-database-security-overview/azure-database-ddm.png)

SQL Database 動的データ マスクは、特権のないユーザーに対してデリケートなデータをマスクし、データの公開を制限します。 動的データ マスクは、Azure SQL Database 内で機密の可能性があるデータを自動的に検出し、アプリケーション層への影響を最小限に抑えながらそれらのフィールドをマスクするための具体的な推奨事項を提示します。 指定されたデータベース フィールドに対するクエリの結果セットに含まれる機密データを難読化しますが、データベース内のデータは変更しません。 詳細については、「[SQL Database の動的データ マスク](sql-database-dynamic-data-masking-get-started.md)」を参照してください。

## <a name="security-management"></a>セキュリティ管理

### <a name="vulnerability-assessment"></a>脆弱性評価

[脆弱性評価](sql-vulnerability-assessment.md)では、全体的なデータベース セキュリティを積極的に向上させる目的で、データベースの潜在的な脆弱性を検出、追跡、修復できるサービスを簡単に構成できます。 脆弱性評価 (VA) は、高度な SQL セキュリティ機能の統合パッケージである、Advanced Data Security (ADS) オファリングの一部です。 脆弱性評価は、SQL ADS ポータルを使って一元的にアクセスおよび管理できます。

### <a name="data-discovery--classification"></a>データの検出と分類

データの検出と分類 (現在プレビュー段階) には、データベース内の機密データの検出、分類、ラベル付け、および保護を行うための、Azure SQL Database に組み込まれた高度な機能が用意されています。 最も機微なデータ (ビジネス/金融、医療、個人データなど) の検出と分類は、組織の情報保護水準において極めて重要な役割を果たします。 これは、以下のケースのインフラストラクチャとして機能します。

- さまざまなセキュリティ シナリオ (機微なデータに対する異常なアクセスの監視 (監査) とアラートなど)。
- 非常に機微なデータを含むデータベースへのアクセスの制御と、セキュリティの強化。
- データのプライバシー基準および規制のコンプライアンス要件を満たす支援。

詳細については、「[データの検出と分類の概要](sql-database-data-discovery-and-classification.md)」を参照してください。

### <a name="compliance"></a>コンプライアンス

アプリケーションでさまざまなセキュリティ要件を満たすのに役立つ上記の機能以外にも、Azure SQL Database では定期的な監査に参加し、さまざまなコンプライアンス基準に認定されています。 詳細については、[Microsoft Azure セキュリティ センター](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)に関するページを参照してください。ここから最新の SQL Database コンプライアンス証明書の一覧を入手できます。

### <a name="feature-restrictions"></a>機能の制限

機能の制限により、一部の形式の SQL インジェクションで、SQL インジェクションが成功した場合でも、データベースに関する情報の漏えいを防ぐことができます。 詳細については、「[Azure SQL Database 機能の制限](sql-database-feature-restrictions.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- SQL Database でのアクセス制御機能の使用については、[アクセス制御](sql-database-control-access.md)に関するページを参照してください。
- データベース監査については、[SQL Database の監査に関する記事](sql-database-auditing.md)をご覧ください。
- 脅威の検出については、「[SQL Database Threat Detection](sql-database-threat-detection.md)」(SQL Database の脅威の検出) をご覧ください。
