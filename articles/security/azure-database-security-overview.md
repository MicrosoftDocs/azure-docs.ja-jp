---
title: Azure のデータベース セキュリティの概要 | Microsoft Docs
description: この記事では、Azure のデータベース セキュリティ機能の概要について説明します。
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: tomsh
ms.openlocfilehash: 7e0e93c82279ec1a4fbecbbf27c7a1866286b2f8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60587656"
---
# <a name="azure-database-security-overview"></a>Azure のデータベース セキュリティの概要

データベースを管理するうえでセキュリティは最大の懸念事項であり、Azure SQL Database では常に優先事項として扱われてきました。 Azure SQL Database は、ファイアウォール規則と接続の暗号化によって接続のセキュリティをサポートしています。 認証はユーザー名とパスワード、および Azure Active Directory で管理される ID を使用した Azure Active Directory (Azure AD) 認証によってサポートされています。 承認には、ロールベースのアクセス制御が使用されています。

Azure SQL Database では、データベース、関連付けられているバックアップ、保管されているトランザクション ログ ファイルの暗号化と暗号化解除をリアルタイムで実行することにより、アプリケーションに変更を加えずに暗号化を行うことができます。

Microsoft では、エンタープライズ データの暗号化方法として、他にも以下のような方法を提供しています。

-   セルレベルの暗号化では、暗号化キーが異なるデータの特定の列またはセルの暗号化を使用できます。
-   ハードウェア セキュリティ モジュールか、暗号化キー階層の中央管理が必要な場合は、Azure 仮想マシン (VM) で Azure Key Vault と SQL Server を併用することを検討してください。
-   Always Encrypted (現在はプレビュー) では、暗号化がアプリケーションに対して透過的になります。 また、クライアントではこれを利用して、クライアント アプリケーション内の機密データを暗号化できます。このとき、SQL Database では暗号化キーは共有されません。

Azure SQL Database Auditing を利用することにより、企業ではイベントを Azure Storage 内の監査ログに記録できます。 また SQL Database の監査を Microsoft Power BI と統合することにより、詳細なレポートと分析が容易になります。

Azure SQL データベースは、大部分の規制要件やセキュリティ要件 (特に HIPAA、ISO 27001/27002、PCI DSS レベル 1 など) を満たすように厳密なセキュリティで保護することができます。 現在のセキュリティ コンプライアンス認定のリストは、[Microsoft Azure セキュリティ センターのサイト](https://azure.microsoft.com/support/trust-center/services/)で確認できます。

この記事では、Microsoft Azure SQL データベースの構造化データ、表形式データ、リレーショナル データをセキュリティで保護する基本的な手順について紹介します。 特にこの記事では、データの保護、アクセスの制御、プロアクティブな監視を行うためのリソースの概要を説明します。

## <a name="protection-of-data"></a>データの保護

SQL Database は、次の暗号化を提供することでデータを保護します。

- 移動中のデータの場合、[Transport Layer Security (TLS)](https://support.microsoft.com/kb/3135244)。
- 保存データの場合、[Transparent Data Encryption](https://go.microsoft.com/fwlink/?LinkId=526242)。
- 使用中のデータの場合、[Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx)。

その他の方法でデータを暗号化するには、次を検討してください。

-   [セルレベルの暗号化](https://msdn.microsoft.com/library/ms179331.aspx) により、暗号化キーが異なるデータの特定の列またはセルを暗号化できます。
-   ハードウェア セキュリティ モジュールか、暗号化キー階層の中央管理が必要な場合は、[Azure VM で Azure Key Vault と SQL Server を併用](https://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx)します。

### <a name="encryption-in-motion"></a>移動時の暗号化

すべてのクライアント/サーバー アプリケーションに共通する問題は、パブリック ネットワークとプライベート ネットワークをまたいでデータが移動するため、プライバシーが必要になるという点です。 ネットワーク上を移動するデータが暗号化されていないと、承認されていないユーザーにより、そのデータが取得されたり盗まれたりする危険性があります。 データベース サービスを処理するときに、データベース クライアントとサーバー間で、確実にデータが暗号化されるようにします。 また、相互通信および中間層アプリケーションで通信を行うデータベース サーバー間でも、確実にデータが暗号化されるようにします。

ネットワークを管理するうえでの課題の 1 つが、信頼されていないネットワークを介してアプリケーション間で送信されるデータを保護することです。 [TLS/SSL](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol) を使用すると、サーバーとクライアントを認証することができ、その後は認証済みの関係者間で送受信されるメッセージを暗号化することもできます。

認証プロセスでは、TLS/SSL クライアントは、TLS/SSL サーバーにメッセージを送信します。 サーバーは、そのサーバーが自己認証する必要がある情報を返します。 クライアントとサーバーはさらにセッション キーの交換を行い、認証ダイアログが終了します。 認証が完了すると、認証プロセス中に設定された対称暗号化キーによって、サーバーとクライアントの間で SSL により保護された通信を開始することができます。

データがデータベースとの間で "送受信中" である間は、常に Azure SQL Database への接続をすべて (TLS/SSL を使用して) 暗号化する必要があります。 SQL Database では、TLS/SSL を使用してサーバーとクライアントを認証し、その後は認証済みの関係者間で送受信されるメッセージを暗号化します。 

アプリケーションの接続文字列では、パラメーターを指定して接続を暗号化し、サーバー証明書を信頼しないようにする必要があります (これは、Azure Portal 以外で接続文字列をコピーする場合に行います)。そうしないと、接続ではサーバーの ID が検証されず、"アクティブな中間者" の攻撃の影響を受けやすくなります。 たとえば ADO.NET ドライバーの場合、これらの接続文字列のパラメーターは、`Encrypt=True` および `TrustServerCertificate=False` です。

### <a name="encryption-at-rest"></a>保存時の暗号化

データベースを保護するために、いくつかの対策を講じることができます。 たとえば、セキュリティで保護されたシステムの設計、機密資産の暗号化、データベース サーバーに対するファイアウォールの構築を行います。 しかし、物理メディア (ドライブやバックアップ テープなど) が盗まれるシナリオでは、悪意のある第三者がデータベースを復元するかアタッチするだけでデータを閲覧することができます。

ソリューションの 1 つとして、データベース内の機密データを暗号化し、証明書を使用してデータを暗号化するために使用するキーを保護することが挙げられます。 このソリューションにより、キーを持たないユーザーによるデータの使用を防ぐことはできますが、このような保護方法は事前に計画しなければなりません。

この問題を解決するため、SQL Server と SQL Database では [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current&viewFallbackFrom=sql-server-2017) をサポートしています。 Transparent Data Encryption では、保存データの暗号化と呼ばれる SQL Server および SQL Database データ ファイルの暗号化が行われます。

Transparent Data Encryption は、悪意のあるアクティビティの脅威からの保護に役立ちます。 データベース、関連付けられているバックアップ、保管されているトランザクション ログ ファイルの暗号化と暗号化解除をリアルタイムで実行することにより、アプリケーションに変更を加えずに暗号化を行うことができます。  

Transparent Data Encryption は、データベース暗号化キーと呼ばれる対称キーを使用してデータベース全体のストレージを暗号化します。 SQL Database では、データベース暗号化キーは組み込まれているサーバー証明書によって保護されます。 組み込みのサーバー証明書は、SQL Database サーバーごとに一意です。

データベースが Geo DR リレーションシップに含まれる場合は、サーバーごとに異なるキーで保護されます。 2 つのデータベースが同じサーバーに接続されている場合は、同じ組み込み証明書が共有されます。 Microsoft は、少なくとも 90 日ごとにこれらの証明書を自動的にローテーションします。 

詳細については、「[透過的なデータ暗号化](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde)」を参照してください。

### <a name="encryption-in-use-client"></a>使用時の暗号化 (クライアント)

ほとんどのデータ侵害には、クレジット カード番号や個人を特定できる情報などの重要なデータの盗難が伴います。 データベースは、保存されているデータによっては機密情報の宝庫です。 データベースには顧客の個人データ (国民 ID 番号など)、機密性の高い競合情報、および知的財産が含まれていることがあります。 データの紛失や盗難は、特にそれが顧客データの場合、ブランド価値の低下や競争面での不利、高額の罰金につながる恐れがあり、訴訟を起こされる可能性さえあります。

![Always Encrypted 機能がロックとキーとして示されている](./media/azure-databse-security-overview/azure-database-fig1.png)

[Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) は、Azure SQL Database または SQL Server データベースに格納されている機密データを保護するために設計された機能です。 Always Encrypted を使用すると、クライアントはクライアント アプリケーション内の機密データを暗号化することができます。暗号化キーがデータベース エンジン (SQL Database や SQL Server) に公開されることはありません。

Always Encrypted では、データの所有者 (データを表示できるユーザー) とデータの管理者 (アクセス権は付与しないユーザー) を分離できます。 承認されていないユーザーを除く、オンプレミスのデータベース管理者、クラウド データベース オペレーター、またはその他の高い特権を持つユーザーに対して暗号化されたデータへのアクセスを禁止します。

また、Always Encrypted では、暗号化がアプリケーションに対して透過的になります。 Always Encrypted 対応のドライバーをクライアント コンピューターにインストールすることにより、クライアント アプリケーション内の機密データを自動的に暗号化および暗号化解除できます。 ドライバーは、データベース エンジンにデータを渡す前に機密性の高い列のデータを暗号化します。 ドライバーは、アプリケーションに対するセマンティクスが保持されるように、自動的にクエリを書き換えます。 また、暗号化されたデータベース列に格納され、クエリ結果に含まれているデータを、同じように透過的に暗号化解除します。

## <a name="access-control"></a>アクセス制御

セキュリティを提供するために、SQL Database は以下を使用してアクセスを制御します。

- IP アドレスによって接続を制限するファイアウォール規則。
- ユーザーに ID の証明を求める認証メカニズム。
- ユーザーによる特定のアクションとデータを制限する認証メカニズム。

### <a name="database-access"></a>データベース アクセス

データ保護は、データへのアクセスを制御することから始まります。 データをホストしているデータ センターでは、物理的なアクセスを管理します。 ネットワーク層でセキュリティを管理するために、ファイアウォールを構成できます。 また、認証用にログインを構成し、サーバーおよびデータベース ロール用のアクセス許可を定義することによるアクセス制御も行います。

#### <a name="firewall-and-firewall-rules"></a>ファイアウォールとファイアウォール規則

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) は、Azure およびその他のインターネット ベースのアプリケーション用のリレーショナル データベース サービスを提供します。 データを保護するため、ファイアウォールは、どのコンピューターに権限を持たせるかを指定するまで、データベース サーバーへのすべてのアクセスを遮断します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。 詳細については、「[Azure SQL Database ファイアウォール規則の概要](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)」を参照してください。

Azure SQL Database サービスは TCP ポート 1433 経由でのみ利用できます。 コンピューターから SQL Database にアクセスするには、クライアント コンピューターのファイアウォールで、TCP ポート 1433 での TCP 通信の発信を許可する必要があります。 他のアプリケーションで着信接続が必要ない場合は、TCP ポート 1433 でブロックします。

#### <a name="authentication"></a>Authentication

認証とは、データベースへの接続時に ID を証明する方法のことです。 SQL Database は、2 種類の認証をサポートしています。

-   **SQL Server 認証**:論理 SQL インスタンスを作成すると、SQL Database サブスクライバー アカウントと呼ばれる 1 つのログイン アカウントが作成されます。 このアカウントは、[SQL Server 認証](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview) (ユーザー名とパスワード) を使用して接続します。 このアカウントは、論理サーバー インスタンスと、そのインスタンスに接続されているすべてのユーザー データベースの管理者です。 サブスクライバー アカウントのアクセス許可を制限することはできません。 これらのアカウントのうち、存在できるのは 1 つだけです。
-   **Azure Active Directory 認証**:[Azure AD 認証](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)は、Azure AD の ID を使用して Azure SQL Database と SQL Data Warehouse に接続するメカニズムです。 これを使用して、データベース ユーザーの ID を一元的に管理できます。

![SQL Database による Azure AD 認証](./media/azure-databse-security-overview/azure-database-fig2.png)

 Azure AD 認証には、以下のような利点があります。
  - SQL Server 認証の代替方法が用意されています。
  - データベース サーバー全体でのユーザー ID の急増を阻止するために役立ち、1 か所でのパスワードのローテーションが可能になります。
  - 外部の (Azure AD) グループを使用してデータベースのアクセス許可を管理できます。
  - 統合 Windows 認証や、Azure AD でサポートされる他の認証形式を有効にすることで、パスワードが保存されないようにすることができます。

#### <a name="authorization"></a>Authorization

[承認](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)とは、Azure SQL データベース内でユーザーが実行できる操作を指します。 ユーザー アカウントのデータベースの[ロール メンバーシップ](https://msdn.microsoft.com/library/ms189121)と[オブジェクトレベル権限](https://msdn.microsoft.com/library/ms191291.aspx)によって制御されます。 承認とは、プリンシパルがアクセスできるセキュリティ保護可能なリソースと、それらのリソースに対して実行できる操作を決めるプロセスです。

### <a name="application-access"></a>アプリケーション アクセス

#### <a name="dynamic-data-masking"></a>動的データ マスク

コール センターのサービス担当者は、複数桁の社会保障番号やクレジット カード番号によって発信者を識別する場合があります。 しかし、それらのデータ項目がサービス担当者に完全に公開されてはいけません。

クエリの結果セットの社会保障番号やクレジット カード番号の末尾 4 桁を除くすべての数字をマスクするマスク ルールを定義できます。

![SQL データベースとビジネス アプリ間で送信される番号に対するマスキング](./media/azure-databse-security-overview/azure-database-fig3.png)

もう 1 つの例として、個人の識別が可能な情報を保護するために、適切なデータ マスクを定義できます。 開発者は、トラブルシューティングを目的として、法令遵守規定に違反せずに運用環境を照会できます。

[SQL Database の動的データ マスク](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)は、特権のないユーザーに対して重要なデータをマスクすることでデータの公開を制限します。 Azure SQL Database の V12 バージョンでは、動的データ マスクがサポートされています。

[動的データ マスク](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)では、公開する機微なデータの量を指定することで、機微なデータに対する未承認のアクセスを防ぐことができ、アプリケーション レイヤーへの影響は最小限に抑えられます。 これはポリシー ベースのセキュリティ機能であり、指定されたデータベース フィールドに対するクエリの結果セットに含まれるデリケートなデータが表示されないようにします。データベース内のデータは変更されません。

> [!Note]
> 動的データ マスクを構成できるのは、Azure Database 管理者、サーバー管理者、またはセキュリティ責任者の各ロールです。

#### <a name="row-level-security"></a>行レベルのセキュリティ

マルチテナント データベースに共通するもう 1 つのセキュリティ要件として、[行レベルのセキュリティ](https://msdn.microsoft.com/library/dn765131.aspx)があります。 この機能を使用して、クエリを実行しているユーザーの特性に基づいて、データベース テーブル内の行へのアクセスを制御できます (例の特性は、グループ メンバーシップと実行コンテキストです)。

![行レベルのセキュリティによって、ユーザーはクライアント アプリ経由でテーブル内の行にアクセスできる](./media/azure-databse-security-overview/azure-database-fig4.png)

アクセス制限のロジックは、別のアプリケーション層のデータから離れた場所ではなく、データベース層に配置されています。 任意の階層からデータ アクセスが試行されるたびに、データベース システムによってアクセス制限が適用されます。 これによりセキュリティ システムの外部からのアクセスが減り、そのシステムの信頼性と堅牢性が向上します。

行レベルのセキュリティには、述語ベースのアクセス制御が取り入れられています。 柔軟かつ一元化された評価機能により、メタデータや、管理者が必要に応じて決めるその他の条件を考慮に入れて評価することができます。 述語は、データへの適切なアクセス権がユーザーにあるかどうかをユーザー属性に基づき判定するための条件として使用されます。 述語ベースのアクセス制御を使用することで、ラベルベースのアクセス制御を実装できます。

## <a name="proactive-monitoring"></a>プロアクティブな監視

SQL Database には、データのセキュリティ保護に役立つ*監査*および*脅威検出*機能が用意されています。

### <a name="auditing"></a>監査

[Azure SQL Database の監査](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) を利用することで、データベース内で発生するイベントや変更についての情報が把握しやすくなります。 例としては、データに対する更新や照会があります。

SQL Database の監査では、データベース イベントを追跡し、Azure Storage アカウントの監査ログにイベントを書き込みます。 監査により、規定遵守の維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違犯の疑いを示す差異や異常に対する洞察が容易になります。 監査は、標準準拠を強化し促進しますが、準拠を保証するものではありません。

SQL Database 監査を使用して、以下を行うことができます。

- **保持** 。 監査するデータベース活動のカテゴリを定義できます。
- **レポート** 。 事前に構成したレポートとダッシュボードを使用して、活動とイベントのレポートをすぐに使用できます。
- **分析** 。 疑わしいイベント、異常な活動、および傾向を発見できます。

監査方法は 2 つあります。

-   **BLOB の監査**:ログは Azure Blob Storage に書き込まれます。 これは新しい監査方法です。 パフォーマンスが高く、細かなオブジェクト レベルの監査が可能であり、対費用効果が高くなります。
-   **テーブルの監査**:Azure Table Storage にログが書き込まれます。

### <a name="threat-detection"></a>脅威の検出

[Azure SQL Database の Advanced Threat Protection](https://docs.microsoft.com/azure/sql-database/sql-advanced-threat-protection)では、潜在的なセキュリティ上の脅威を示す疑わしいアクティビティが検出されます。 脅威の検出を使用して、SQL インジェクションなどの疑わしいイベントがデータベース上で発生したときに、そのイベントに対処できます。 アラート機能を備えているほか、Azure SQL Database 監査 を使用して疑わしいイベントを調査することもできます。

![外部からの攻撃者および悪意のある内部関係者による、SQL Database および Web アプリの脅威の検出](./media/azure-databse-security-overview/azure-database-fig5.jpg)

SQL Advanced Threat Protection (ATP) は、データの探索と分類、脆弱性の評価、脅威の検出などの高度な SQL セキュリティ機能のセットを提供します。 

- [データの検出と分類](../sql-database/sql-database-data-discovery-and-classification.md)
- [脆弱性評価](../sql-database/sql-vulnerability-assessment.md)  
- [脅威の検出](../sql-database/sql-database-threat-detection.md)

[Azure Database for PostgreSQL の Advanced Threat Protection](../postgresql/concepts-data-access-and-security-threat-protection.md) では、新しいセキュリティ レイヤーが提供されます。この利用によって、異常なアクティビティに関するセキュリティ アラートが提供されて、発生する潜在的脅威の検出と対応が可能になります。 不審なデータベース アクティビティ、潜在的な脆弱性、異常なデータベース アクセスやクエリのパターンが見つかった場合に、ユーザーはアラートを受信します。 Azure Database for PostgreSQL 用の Advanced Threat Protection では、アラートが Azure Security Center と統合されています。 アラートの種類には以下が含まれます。

- 通常とは異なる場所からのアクセス
- 通常とは異なる Azure データ センターからのアクセス 
- 通常とは異なるプリンシパルからのアクセス 
- 潜在的に有害なアプリケーションからのアクセス 
- Azure Database for PostgreSQL 資格情報のブルート フォース 

[Azure Database for MySQL の Advanced Threat Protection](../mysql/concepts-data-access-and-security-threat-protection.md) は、PostgreSQL の高度な保護のような保護を提供します。  

## <a name="centralized-security-management"></a>セキュリティの一元管理

[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) は、脅威の防御、検出、対応を可能にする機能です。 Azure サブスクリプション間のセキュリティ監視とポリシー管理を総合的に提供します。 Security Center は、見つけにくい脅威の検出を支援すると共に、さまざまなセキュリティ ソリューションをまとめた広範なエコシステムとして機能します。

すべてのサーバーとデータベースのセキュリティを [Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-database) で可視化することによって、SQL Database に格納されているデータを保護することができます。 Security Center では、次のことを実行できます。

- SQL Database の暗号化と監査のポリシーを定義する。
- 利用しているすべてのサブスクリプションを対象に SQL Database リソースのセキュリティを監視する。
- セキュリティの問題をすばやく特定して修復する。
- [Azure SQL Database 脅威検出](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)からのアラートを統合する。

セキュリティ センターは、ロール ベースのアクセスをサポートしています。

## <a name="sql-information-protection"></a>SQL Information Protection

[SQL Information Protection](../sql-database/sql-database-data-discovery-and-classification.md) では、潜在的な機密データが自動的に検出されて分類され、分類属性を使用して機密データを永続的にタグ付けするためのラベル付けメカニズムが提供されます。データベースの分類状態を示す詳細ダッシュボードも提供されます。  

さらに、SQL クエリの結果セットの機密性が計算されるので、機密データを抽出するクエリを明示的に監査し、データを保護できます。 SQL Information Protection について詳しくは、「Azure SQL Database のデータの検出と分類」をご覧ください。

Azure Security Center で [SQL Information Protection](../security-center/security-center-info-protection-policy.md) のポリシーを構成できます。

## <a name="azure-marketplace"></a>Azure Marketplace

Azure Marketplace は、新興企業および独立系ソフトウェア会社 (ISV) が独自のソリューションを世界中の Azure のお客様に提供できるようにするアプリケーションおよびサービスのオンライン マーケットプレースです。
Azure Marketplace では、お客様とパートナーにより良いサービスを提供するために、Microsoft Azure の複数のパートナー エコシステムを組み合わせて 1 つの統合プラットフォームとしています。 [検索を実行](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1)して、Azure Marketplace で利用可能なデータベース セキュリティ製品を表示できます。

## <a name="next-steps"></a>次の手順

- [Azure SQL データベースのセキュリティ保護](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial)
- [Azure Security Center と Azure SQL Database サービス](https://docs.microsoft.com/azure/security-center/security-center-sql-database)
- [SQL Database の脅威の検出](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [SQL Database のパフォーマンスを向上させる](https://docs.microsoft.com/azure/sql-database/sql-database-performance-tutorial)
