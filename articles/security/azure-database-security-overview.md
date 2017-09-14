---
title: "Azure のデータベース セキュリティの概要 | Microsoft Docs"
description: "この記事では、Azure のデータベース セキュリティ機能の概要について説明します。"
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/19/2017
ms.author: TomSh
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 036ce3dce28e7951bb39754c4351661fae85f06c
ms.contentlocale: ja-jp
ms.lasthandoff: 08/09/2017

---

# <a name="azure-database-security-overview"></a>Azure のデータベース セキュリティの概要

データベースを管理するうえでセキュリティは最大の懸念事項であり、Azure SQL Database では常に優先事項として扱われてきました。 Azure SQL Database は、ファイアウォール規則と接続の暗号化によって接続のセキュリティをサポートしています。 認証はユーザー名とパスワード、および Azure Active Directory で管理される ID を使用した Azure Active Directory 認証によってサポートされています。 承認には、ロールベースのアクセス制御が使用されています。

Azure SQL Database では、データベース、関連付けられているバックアップ、保管されているトランザクション ログ ファイルの暗号化と暗号化解除をリアルタイムで実行することにより、アプリケーションに変更を加えずに暗号化を行うことができます。

Microsoft では、エンタープライズ データの暗号化方法として、他にも以下のような方法を提供しています。

-   セルレベルの暗号化により、暗号化キーが異なるデータの特定の列またはセルを暗号化します。
-   ハードウェア セキュリティ モジュールか、暗号化キー階層の中央管理が必要な場合は、Azure VM で Azure Key Vault と SQL Server を併用することを検討してください。
-   Always Encrypted (現在プレビュー段階) は、暗号化をアプリケーションに対して透過的にし、クライアントが SQL Database と暗号化キーを共有することなくクライアント アプリケーション内で機微なデータを暗号化できるようにします。

Azure SQL Database Auditing を利用することにより、企業ではイベントを Azure Storage 内の監査ログに記録できます。 また SQL Database の監査を Microsoft Power BI と統合することにより、詳細なレポートと分析が容易になります。

 SQL Azure データベースは、大部分の規制要件やセキュリティ要件 (特に HIPAA、ISO 27001/27002、PCI DSS レベル 1 など) を満たすように厳密なセキュリティで保護することができます。 現在のセキュリティ コンプライアンス認定のリストは、[Microsoft Azure セキュリティ センターのサイト](http://azure.microsoft.com/support/trust-center/services/)で確認できます。

この記事では、Microsoft Azure SQL Database の構造化データ、表形式データ、リレーショナル データをセキュリティで保護する基本的な手順について紹介します。 特にこの記事では、データの保護、アクセスの制御、プロアクティブな監視を行うためのリソースの概要を説明します。

この記事、「Azure のデータベース セキュリティの概要」では、以下の領域を重点的に取り上げます。

-   データの保護
-   アクセス制御
-   プロアクティブな監視
-   セキュリティの一元管理
-   Azure Marketplace

## <a name="protect-data"></a>データの保護

SQL Database は、暗号化によってデータを保護します。移動中のデータには[トランスポート層セキュリティ](https://support.microsoft.com/kb/3135244)、保存データには [Transparent Data Encryption](http://go.microsoft.com/fwlink/?LinkId=526242)、使用中のデータには [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) を使用することで、データの暗号化を行います。

このセクションでは、以下の内容を説明します。

-   移動時の暗号化
-   保存時の暗号化
-   使用時の暗号化 (クライアント)

その他の方法でデータを暗号化するには、次を検討してください。

-   [セルレベルの暗号化](https://msdn.microsoft.com/library/ms179331.aspx) により、暗号化キーが異なるデータの特定の列またはセルを暗号化できます。
-   ハードウェア セキュリティ モジュールか、暗号化キー階層のサーバーの中央管理が必要な場合は、 [Azure VM で Azure Key Vault と SQL Server](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx)を併用することを検討してください。

### <a name="encryption-in-motion"></a>移動時の暗号化

すべてのクライアント/サーバー アプリケーションに共通する問題は、パブリック ネットワークとプライベート ネットワークをまたいでデータが移動するため、プライバシーが必要になるという点です。 ネットワーク上を移動するデータが暗号化されていないと、承認されていないユーザーにより、そのデータが取得されたり盗まれたりする危険性があります。 データベース サービスを扱う場合は、データベース クライアントとデータベース サーバーの間だけでなく、データベース サーバー間での相互の通信や、中間層アプリケーションとの通信においてもデータを確実に暗号化する必要があります。

ネットワークを管理するうえでの課題の 1 つが、信頼されていないネットワークを介してアプリケーション間で送信されるデータを保護することです。 [TLS/SSL](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol) を使用すると、サーバーとクライアントを認証することができ、その後は認証済みの関係者間で送受信されるメッセージを暗号化することもできます。

認証プロセスでは、TLS/SSL クライアントから TLS/SSL サーバーにメッセージが送信され、サーバーはサーバー自体の認証に必要な情報を使用してそれに応答します。 クライアントとサーバーはさらにセッション キーの交換を行い、認証ダイアログが終了します。 認証が完了すると、認証プロセス中に設定された対称暗号化キーを使用し、サーバーとクライアントの間で SSL により保護された通信を開始することができます。

データがデータベースとの間で "送受信中" である間は、常に Azure SQL Database への接続をすべて (SSL/TLS を使用して) 暗号化する必要があります。 SQL Azure では、TLS/SSL を使用してサーバーとクライアントを認証し、その後は認証済みの関係者間で送受信されるメッセージを暗号化します。 アプリケーションの接続文字列内に、接続を暗号化し、サーバーの証明書を信頼しないようにするパラメーターを指定する必要があります (Azure クラシック ポータルから接続文字列をコピーすると、この操作は自動的に実行されます)。この操作を実行しないと、接続時にサーバーの ID が検証されず、"man-in-the-middle" 攻撃を受けやすくなります。 たとえば ADO.NET ドライバーの場合、これらの接続文字列のパラメーターは、Encrypt=True および TrustServerCertificate=False です。

### <a name="encryption-at-rest"></a>保存時の暗号化
セキュリティで保護されたシステムの設計、機密資産の暗号化、データベース サーバーに対するファイアウォールの構築などの、データベースを保護するいくつかの対策を講じることができます。 ただし、物理メディア (ドライブやバックアップ テープなど) が盗まれるシナリオでは、悪意のある第三者がデータベースを復元するかアタッチするだけでデータを閲覧することができます。

解決策の 1 つとして、データベース内の機微なデータを暗号化し、データの暗号化に使用されるキーを証明書で保護するという方法が挙げられます。 これにより、キーを持たないユーザーによるデータの使用を防ぐことはできますが、このような保護方法は事前に計画しなければなりません。

この問題を解決するため、SQL Server と Azure SQL では [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/securityrecryption/transparent-data-encryption-tde) をサポートしています。 TDE では、保存データの暗号化と呼ばれる SQL Server および Azure SQL Database データ ファイルの暗号化が行われます。

Azure SQL Database の透過的なデータ暗号化を使用すると、データベース、関連付けられているバックアップ、保管されているトランザクション ログ ファイルの暗号化と暗号化解除をリアルタイムで実行することにより、アプリケーションに変更を加えずに悪意のあるアクティビティの脅威からデータを保護できます。  

TDE は、データベース暗号化キーと呼ばれる対称キーを使用してデータベース全体のストレージを暗号化します。 SQL Database では、データベース暗号化キーは組み込まれているサーバー証明書によって保護されます。 組み込みのサーバー証明書は、SQL Database サーバーごとに一意です。

データベースが GeoDR リレーションシップに含まれる場合は、サーバーごとに異なるキーで保護されます。 2 つのデータベースが同じサーバーに接続されている場合は、同じ組み込み証明書が共有されます。 Microsoft は、少なくとも 90 日ごとにこれらの証明書を自動的にローテーションします。 TDE の一般的な説明については、「 [透過的なデータ暗号化 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde)」を参照してください。

### <a name="encryption-in-use-client"></a>使用時の暗号化 (クライアント)

ほとんどのデータ侵害には、クレジット カード番号や個人を特定できる情報などの重要なデータの盗難が伴います。 データベースは、保存されているデータによっては機密情報の宝庫です。 データベースには顧客の個人データ、機密性の高い競合情報、および知的財産が含まれていることがあります。 データの紛失や盗難は、特にそれが顧客データの場合、ブランド価値の低下や競争面での不利、高額の罰金につながる恐れがあり、訴訟を起こされる可能性さえあります。

![Always Encrypted](./media/azure-databse-security-overview/azure-database-fig1.png)

[Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) は、Azure SQL Database や SQL Server データベースに格納された、クレジット カード番号や国民識別番号 (米国の社会保障番号など) のような機微なデータを保護することを目的とした機能です。 Always Encrypted を使用すると、クライアントはクライアント アプリケーション内の機密データを暗号化することができます。暗号化キーがデータベース エンジン (SQL Database や SQL Server) に公開されることはありません。

Always Encrypted では、データの所有者 (データを表示できるユーザー) とデータの管理者 (アクセス権は付与しないユーザー) を分離できます。 承認されていないユーザーを除く、オンプレミスのデータベース管理者、クラウド データベース オペレーター、またはその他の高い特権を持つユーザーに対して暗号化されたデータへのアクセスを禁止することにより、

また、Always Encrypted では、暗号化がアプリケーションに対して透過的になります。 Always Encrypted 対応のドライバーをクライアント コンピューターにインストールすることにより、クライアント アプリケーション内の機微なデータを自動的に暗号化および暗号化解除できます。 ドライバーは、機密列のデータを暗号化してからデータベース エンジンに渡すと、アプリケーションに対するセマンティクスが保持されるように自動的にクエリを書き換えます。 また、暗号化されたデータベース列に格納され、クエリ結果に含まれているデータを、同じように透過的に暗号化解除します。

## <a name="access-control"></a>アクセス制御
SQL Database では、セキュリティを提供するために、IP アドレスで接続を制限するファイアウォール規則、ユーザーに ID の指定を要求する認証メカニズム、およびユーザーを特定の操作とデータに限定する承認メカニズムによって、アクセスを制御します。

### <a name="database-access"></a>データベース アクセス

データ保護は、データへのアクセスを制御することから始まります。 データをホストしているデータセンターで物理アクセスの管理が行われる一方、ユーザー側ではネットワーク層でセキュリティを管理するようファイアウォールを構成できます。 また、認証用にログインを構成し、サーバーおよびデータベース ロール用のアクセス許可を定義することによるアクセス制御も行います。

このセクションでは、以下の内容を説明します。

-   ファイアウォールとファイアウォール規則
-   認証
-   承認

#### <a name="firewall-and-firewall-rules"></a>ファイアウォールとファイアウォール規則

Microsoft Azure SQL Database は、Azure およびその他のインターネット ベースのアプリケーション用のリレーショナル データベース サービスを提供します。 データを保護するため、ファイアウォールは、どのコンピューターに権限を持たせるかを指定するまで、データベース サーバーへのすべてのアクセスを遮断します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。 詳細については、「[Azure SQL Database ファイアウォール規則の概要](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)」を参照してください。

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) サービスは TCP ポート 1433 経由でのみ利用できます。 コンピューターから SQL Database にアクセスするには、クライアント コンピューターのファイアウォールで、TCP ポート 1433 での TCP 通信の発信を許可する必要があります。 他のアプリケーションで必要ない場合は、TCP ポート 1433 での着信接続をブロックします。

#### <a name="authentication"></a>認証

SQL データベース認証とは、データベースへの接続時に ID を証明する方法のことです。 SQL Database は、2 種類の認証をサポートしています。

-   **SQL 認証:** 論理 SQL インスタンスを作成すると、SQL Database サブスクライバー アカウントと呼ばれる 1 つのログイン アカウントが作成されます。 このアカウントは、[SQL Server 認証](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview) (ユーザー名とパスワード) を使用して接続します。 このアカウントは、論理サーバー インスタンスと、そのインスタンスに接続されているすべてのユーザー データベースの管理者です。 サブスクライバー アカウントのアクセス許可を制限することはできません。 これらのアカウントのうち、存在できるのは 1 つだけです。
-   **Azure Active Directory 認証:** [Azure Active Directory 認証](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) は、Azure Active Directory (Azure AD) の ID を使用して Microsoft Azure SQL Database と SQL Data Warehouse に接続するメカニズムです。 これにより、データベース ユーザーの ID を一元的に管理できます。

![認証](./media/azure-databse-security-overview/azure-database-fig2.png)

 Azure Active Directory 認証には、以下のような利点があります。
  - SQL Server 認証の代替方法が用意されています。
  - データベース サーバー全体でのユーザー ID の急増を阻止するために役立ち、1 か所でのパスワードのローテーションが可能になります。
  - 外部 (Azure Active Directory) のグループを使用してデータベースのアクセス許可を管理できます。
  - 統合 Windows 認証や、Azure Active Directory でサポートされる他の認証形式を有効にすることで、パスワードが保存されないようにすることができます。

#### <a name="authorization"></a>承認
[承認](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)とは、Azure SQL Database 内でユーザーにどのような操作が許可されるかを示すものであり、ユーザー アカウントの[データベース ロールのメンバーシップ](https://msdn.microsoft.com/library/ms189121)と[オブジェクト レベルのアクセス許可](https://msdn.microsoft.com/library/ms191291.aspx)によって制御されます。 承認とは、プリンシパルがアクセスできるセキュリティ保護可能なリソースと、それらのリソースに対して実行できる操作を決めるプロセスです。

### <a name="application-access"></a>アプリケーション アクセス

このセクションでは、以下の内容を説明します。

-   動的データ マスク
-   行レベルのセキュリティ

#### <a name="dynamic-data-masking"></a>動的データ マスク
コール センターのサポート担当者は、社会保障番号やクレジット カード番号の一部の数字から電話の相手を特定できますが、このようなデータ項目をサポート担当者にすべて公開すべきではありません。

クエリの結果セットの社会保障番号やクレジット カード番号の末尾 4 桁を除くすべての数字をマスクするマスク ルールを定義できます。

![動的データ マスク](./media/azure-databse-security-overview/azure-database-fig3.png)

別の例として、開発者は、適切なデータ マスクを定義し、個人を特定できる情報 (PII) データを保護し、法令遵守規定に違反することなくトラブルシューティングの目的で運用環境に対して照会を行うことができます。

[SQL Database 動的データ マスク](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)では、特権のないユーザーに対して機微なデータをマスクすることでデータの公開を制限します。 Azure SQL Database の V12 バージョンでは、動的データ マスクがサポートされています。

[動的データ マスク](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)では、公開する機微なデータの量を指定することで、機微なデータに対する未承認のアクセスを防ぐことができ、アプリケーション レイヤーへの影響は最小限に抑えられます。 これはポリシー ベースのセキュリティ機能であり、指定されたデータベース フィールドに対するクエリの結果セットに含まれるデリケートなデータが表示されないようにします。データベース内のデータは変更されません。


> [!Note]
> 動的データ マスクを構成できるのは、Azure Database 管理者、サーバー管理者、またはセキュリティ責任者の各ロールです。

#### <a name="row-level-security"></a>行レベルのセキュリティ
マルチテナント データベースに共通するもう 1 つのセキュリティ要件として、[行レベルのセキュリティ](https://msdn.microsoft.com/library/dn765131.aspx)があります。 この機能では、クエリを実行しているユーザーの特性 (グループ メンバーシップや実行コンテキストなど) に基づいて、データベース テーブル内の行へのアクセスを制御できます。

![行レベルのセキュリティ](./media/azure-databse-security-overview/azure-database-fig4.png)

アクセス制限のロジックは、別のアプリケーション層のデータから離れた場所ではなく、データベース層に配置されています。 任意の階層からデータ アクセスが試行されるたびに、データベース システムによってアクセス制限が適用されます。 これによりセキュリティ システムの外部からのアクセスが減り、そのシステムの信頼性と堅牢性が向上します。

行レベルのセキュリティには、述語ベースのアクセス制御が取り入れられています。 柔軟かつ一元化された述語ベースの評価機能により、メタデータや、管理者が必要に応じて決めるその他の条件を考慮に入れて評価することができます。 述語は、データへの適切なアクセス権がユーザーにあるかどうかをユーザー属性に基づき判定するための条件として使用されます。 述語ベースのアクセス制御を使用することで、ラベルベースのアクセス制御を実装できます。

## <a name="proactive-monitoring"></a>プロアクティブな監視
SQL Database には、データをセキュリティで保護するために、**監査**および**脅威検出**機能が用意されています。

### <a name="auditing"></a>監査
SQL Database Auditing を利用することで、データに対する更新やクエリなど、データベース内で発生するイベントや変更についての情報が把握しやすくなります。

[Azure SQL Database Auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) では、データベース イベントを追跡し、Azure Storage アカウントの監査ログにイベントを書き込みます。 監査により、規定遵守の維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違犯の疑いを示す差異や異常に対する洞察が容易になります。 監査は、標準準拠を強化し促進しますが、準拠を保証するものではありません。

SQL Database 監査により、以下のことが可能になります。

-   **保持** 。 監査するデータベース活動のカテゴリを定義できます。
-   **レポート** 。 事前に構成したレポートとダッシュボードを使用して、活動とイベントのレポートをすぐに使用できます。
-   **分析** 。 疑わしいイベント、異常な活動、および傾向を発見できます。

監査方法は 2 つあります。

-   **BLOB の監査** - Azure Blob ストレージにログが書き込まれます。 これは新しい監査方法であり、パフォーマンスが高く、粒度の高いオブジェクト レベルの監査をサポートし、コスト効率に優れています。
-   **テーブルの監査** - Azure Table Storage にログが書き込まれます。

### <a name="threat-detection"></a>脅威の検出
[Azure SQL Database の脅威の検出](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)では、潜在的なセキュリティ上の脅威を示す疑わしいアクティビティが検出されます。 脅威の検出を利用すると、SQL インジェクションなどの疑わしいイベントがデータベース上で発生したときに、そのイベントに対処できます。 アラート機能を備えているほか、Azure SQL Database Auditing を使用して疑わしいイベントを調査することもできます。

![脅威の検出](./media/azure-databse-security-overview/azure-database-fig5.jpg)

たとえば、SQL インジェクションはインターネットにおける Web アプリケーションの一般的なセキュリティ問題の 1 つであり、データ駆動型アプリケーションの攻撃に使用されます。 攻撃者は、アプリケーションの脆弱性を利用してアプリケーションの入力フィールドに悪意のある SQL ステートメントを挿入し、データベースのデータを侵害または変更します。

セキュリティ責任者や他の指定された管理者は、不審なデータベースのアクティビティが発生すると、直ちに通知を受け取ることができます。 それぞれの通知では、不審なアクティビティの詳細情報と、脅威に対して推奨されるさらなる調査方法や軽減策が記載されています。        

## <a name="centralized-security-management"></a>セキュリティの一元管理

[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) は、脅威の防御、検出、対応を可能にする機能です。 これにより、Azure サブスクリプション全体に統合セキュリティの監視とポリシーの管理を提供し、気付かない可能性がある脅威を検出し、セキュリティ ソリューションの広範なエコシステムと連動します。

すべてのサーバーとデータベースのセキュリティを [Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-database) で可視化することによって、SQL Database に格納されているデータを保護することができます。 Security Center では、次のことを実行できます。

-   SQL Database の暗号化と監査のポリシーを定義する。
-   利用しているすべてのサブスクリプションを対象に SQL Database リソースのセキュリティを監視する。
-   セキュリティの問題をすばやく特定して修復する。
-   [Azure SQL Database 脅威検出](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)からのアラートを統合する。
-   セキュリティ センターは、ロール ベースのアクセスをサポートしています。

## <a name="azure-marketplace"></a>Azure Marketplace

Azure Marketplace は、新興企業および独立系ソフトウェア会社 (ISV) が独自のソリューションを世界中の Azure のお客様に提供できるようにするアプリケーションおよびサービスのオンライン マーケットプレースです。
Azure Marketplace では、お客様とパートナーにより良いサービスを提供するために、Microsoft Azure の複数のパートナー エコシステムを組み合わせて 1 つの統合プラットフォームとしています。 [こちら](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1)をクリックし、Azure Marketplace で提供されているデータベース セキュリティ製品をご確認ください。

## <a name="next-steps"></a>次のステップ

- 詳細情報: 「[Azure SQL データベースのセキュリティ保護](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial)」
- 詳細情報: 「[Azure Security Center と Azure SQL Database サービス](https://docs.microsoft.com/azure/security-center/security-center-sql-database)」
- 脅威の検出の詳細については、「[SQL Database の脅威の検出](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)」を参照してください。
- 詳細については、[SQL データベースのパフォーマンスの改善](https://docs.microsoft.com/azure/sql-database/sql-database-performance-tutorial)に関するページを参照してください。 

