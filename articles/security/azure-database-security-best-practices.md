---
title: Azure のデータベース セキュリティに関するベスト プラクティス | Microsoft Docs
description: この記事では、Azure のデータベース セキュリティに関するベスト プラクティス一式を提供します。
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: tomsh
ms.openlocfilehash: 3e244f89904ce9aca161ed1ea435f4137e42bc5d
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56117971"
---
# <a name="azure-database-security-best-practices"></a>Azure のデータベース セキュリティに関するベスト プラクティス
データベースを管理するうえでセキュリティは最大の懸念事項であり、 [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/) では常に優先事項として扱われてきました。 データベースは、大部分の規制要件やセキュリティ要件 (HIPAA、ISO 27001/27002、PCI DSS レベル 1 など) を満たすように厳密なセキュリティで保護することができます。 現在のセキュリティ コンプライアンス認定のリストは、[Microsoft セキュリティ センターのサイト](https://azure.microsoft.com/support/trust-center/services/)で確認できます。 規制の要件に基づいて特定の Azure データセンターにデータベースを配置することも選択できます。

この記事では、Azure のデータベース セキュリティに関するベスト プラクティスについて説明します。 このベスト プラクティスは、Azure のデータベース セキュリティに関して Microsoft が蓄積してきたノウハウと、ユーザーの皆様の経験に基づいています。

それぞれのベスト プラクティスについて、次の点を説明します。

-   ベスト プラクティスの内容
-   そのベスト プラクティスをお勧めする理由
-   そのベスト プラクティスを実践しなかった場合に発生する可能性がある事態
-   そのベスト プラクティスを実践する方法

この「Azure のデータベース セキュリティに関するベスト プラクティス」の記事は、この記事の執筆時点における共通認識と、Azure プラットフォームの機能および機能セットに基づいています。 共通認識とテクノロジは時間が経つにつれて変化するため、そのような変化に対応するために、この記事は定期的に更新されます。

## <a name="use-firewall-rules-to-restrict-database-access"></a>ファイアウォール規則を使用してデータベースへのアクセスを制限する
Microsoft Azure SQL Database は、Azure およびその他のインターネット ベースのアプリケーション用のリレーショナル データベース サービスを提供します。 アクセス セキュリティを提供するために、SQL Database は以下を使用してアクセスを制御します。

- IP アドレスによって接続を制限するファイアウォール規則。
- ユーザーに ID の証明を求める認証メカニズム。
- ユーザーによる特定のアクションとデータを制限する認証メカニズム。

ファイアウォールは、どのコンピューターに権限を持たせるかを指定するまで、データベース サーバーへのすべてのアクセスを遮断します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。

次の図は、SQL database でサーバーのファイアウォールを設定する場所を示しています。

![ファイアウォール規則](./media/azure-database-security-best-practices/azure-database-security-best-practices-Fig1.png)

Azure SQL Database サービスは TCP ポート 1433 経由でのみ利用できます。 コンピューターから SQL Database にアクセスするには、クライアント コンピューターのファイアウォールで、TCP ポート 1433 での TCP 通信の発信を許可する必要があります。 他のアプリケーションでこれらの接続を必要としない場合は、ファイアウォール規則を使用して TCP ポート 1433 で着信接続をブロックします。

接続処理の一部として、Azure 仮想マシンからの接続は worker ロールごとに IP アドレスとポートにリダイレクトされます。 ポート番号の範囲は、11000 ～ 11999 です。 TCP ポートの詳細については、「[ADO.NET 4.5 用の 1433 以外のポート](../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md)」を参照してください。

SQL Database のファイアウォール規則の詳細については、[SQL Database のファイアウォール規則](../sql-database/sql-database-firewall-configure.md)に関するページを参照してください。

> [!Note]
> IP 規則に加えて、ファイアウォールは仮想ネットワーク規則を管理します。 仮想ネットワーク規則は、Virtual Network サービスのエンドポイントに基づいています。 仮想ネットワーク規則は、場合によっては IP 規則より望ましい場合があります。 詳細については、[Azure SQL Database の Virtual Network サービス エンドポイントと規則](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md)に関する記事をご覧ください。

## <a name="enable-database-authentication"></a>データベース認証を有効にする
SQL Database は SQL サーバー認証と Azure AD Authentication の 2 種類の認証をサポートしています。

### <a name="sql-server-authentication"></a>*SQL Server 認証*

次のような利点があります。

- Windows ドメインで認証されていないユーザーがいるオペレーティング システム混在環境を SQL Database がサポートできる。
- SQL Server 認証を必要とする、古いアプリケーションやパートナーが提供するアプリケーションを SQL Database がサポートできる。
- ユーザーが不明または信頼されていないドメインから接続できる場合。 たとえば、既存の顧客が割り当てられた SQL Server ログインで接続して注文の状態を受信するアプリケーションです。
- ユーザーが独自の ID を作成する Web ベースのアプリケーションを SQL Database がサポートできる。
- ソフトウェア開発者が、事前設定された既知の SQL Server ログインに基づく複雑なアクセス許可階層を使用してアプリケーションを配布できる場合。

> [!NOTE]
> SQL Server 認証は Kerberos セキュリティ プロトコルを使用できません。

SQL Server 認証を使用する場合は、次のことを行う必要があります。

- 強力な資格情報を自分で管理します。
- 接続文字列で資格情報を保護します。
- (場合により) Web サーバーからデータベースにネットワーク経由で渡される資格情報を保護します。 詳細については、[方法:ASP.NET 2.0 で SQL 認証を使用して SQL Server へ接続する方法](/previous-versions/msp-n-p/ff648340(v=pandp.10))をご覧ください。

### <a name="azure-active-directory-ad-authentication"></a>*Azure Active Directory (AD) 認証*
Azure AD 認証は、Azure AD の ID を使用して Azure SQL Database と [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) に接続するメカニズムです。 Azure AD 認証を使用すると、データベース ユーザーの ID や他の Microsoft サービスを一元管理できます。 ID の一元管理では、1 か所でデータベース ユーザーを管理できるようになるため、アクセス許可の管理が容易になります。

> [!NOTE]
> SQL Server 認証を使用して Azure AD 認証を使用することをお勧めします。

次のような利点があります。

- SQL Server 認証の代替方法が用意されています。
- データベース サーバー全体でユーザー ID が急増するのを防ぎます。
- 1 か所でのパスワードのローテーションを許可します。
- 顧客は外部の (Azure AD) グループを使用してデータベースのアクセス許可を管理できます。
- 統合 Windows 認証や、Azure Active Directory でサポートされる他の認証形式を有効にすることで、パスワードが保存されないようにすることができます。
- 包含データベース ユーザーを使用して、データベース レベルで ID を認証します。
- SQL Database に接続するアプリケーション向けにトークンベース認証をサポートしています。
- ADFS (ドメイン フェデレーション) またはドメインを同期しないローカル Azure Active Directory インスタンスのネイティブ ユーザー/パスワード認証をサポートします。
- Azure AD 認証は、Multi-Factor Authentication を含む Active Directory ユニバーサル認証を使用する SQL Server Management Studio からの接続をサポートします。 Multi-Factor Authentication には、電話、テキスト メッセージ、スマート カードと PIN、モバイル アプリ通知など、各種確認オプションによる強力な認証が用意されています。 詳細については、「 [SQL Database と SQL Data Warehouse での Azure AD Multi-Factor Authentication のための SSMS のサポート](../sql-database/sql-database-ssms-mfa-authentication.md)」を参照してください。

構成の手順には、Azure AD 認証を構成して使用する次の手順が含まれます。

- Azure AD を作成して設定します。
- 省略可能:Active Directory インスタンスを関連付けるか、現在 Azure サブスクリプションに関連付けられている Active Directory インスタンスを変更します。
- Azure SQL Database または [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/) の Azure Active Directory 管理者を作成します。
- クライアント コンピューターを構成します。
- Azure AD の ID にマップされている包含データベース ユーザーをデータベースに作成します。
- Azure AD の ID を使用してデータベースに接続します。

詳細については、「[Use Azure Active Directory Authentication for authentication with SQL Database, Managed Instance, or SQL Data Warehouse (SQL Database、マネージド インスタンス、または SQL Data Warehouse での認証に Azure Active Directory Authentication を使用する)](../sql-database/sql-database-aad-authentication.md)」をご覧ください。

## <a name="protect-your-data-by-using-encryption-and-row-level-security"></a>暗号化と行レベルのセキュリティを使用してデータを保護する
[Azure SQL Database の Transparent Data Encryption](../sql-database/transparent-data-encryption-azure-sql.md) は、ディスク上のデータを保護し、ハードウェアへの不正アクセスから保護できます。 データベース、関連付けられているバックアップ、保管されているトランザクション ログ ファイルの暗号化と暗号化解除をリアルタイムで実行することにより、アプリケーションに変更を加えずに暗号化を行うことができます。 Transparent Data Encryption は、データベース暗号化キーと呼ばれる対称キーを使用してデータベース全体のストレージを暗号化します。

ストレージ全体を暗号化している場合でも、データベース自体も暗号化することが重要です。 これは、多層防御型のデータ保護手法の 1 つです。 Azure SQL Database を使用していて、クレジット カードや社会保障番号などの機密データを保護する必要がある場合、FIPS 140-2 検証済み 256 ビット AES 暗号化を使ってデータベースを暗号化できます。 この暗号化は、さまざまな業界標準 (HIPAA、PCI など) の要件を満たしています。

Transparent Data Encryption を使用してデータベースを暗号化するときには、[バッファー プール拡張機能 (BPE)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) に関連するファイルは暗号化されません。 BPE 関連ファイルについては、[BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) や[暗号化ファイル システム (EFS)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749610(v%3dws.10)) などのファイル システム レベルの暗号化ツールを使用する必要があります。

セキュリティ管理者やデータベース管理者などの特権を持つユーザーは、Transparent Data Encryption で暗号化されたデータベースのデータにもアクセスできるため、次の推奨事項にも従う必要があります。

- データベース レベルでの SQL Server 認証を有効にします。
- [RBAC ロール](../role-based-access-control/overview.md)を使用して Azure AD Authentication を使用します。
- ユーザーとアプリケーションは、別々のアカウントを使って認証する必要があります。 そうすることで、ユーザーとアプリケーションに付与されるアクセス許可を制限し、悪意のあるアクティビティのリスクを減らすことができます。
- 固定データベース ロール (db_datareader、db_datawriter など) を使用してデータベース レベルのセキュリティを実装します。 または、アプリケーションにカスタム ロールを作成し、選択したデータベース オブジェクトに明示的なアクセス許可を付与できます。

その他の方法でデータをセキュリティ保護するには、次を検討してください。

- [セルレベルの暗号化](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) により、暗号化キーが異なるデータの特定の列またはセルを暗号化できます。
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) を使用すると、クライアントはクライアント アプリケーション内の機密データを暗号化することができます。暗号化キーがデータベース エンジン (SQL Database や SQL Server) に公開されることはありません。 これにより、Always Encrypted では、データの所有者 (データを表示できるユーザー) とデータの管理者 (アクセス権は付与しないユーザー) を分離できます。
- [行レベルのセキュリティ](/sql/relational-databases/security/row-level-security)を使用すると、クエリを実行しているユーザーの特性に基づいて、データベース テーブル内の行へのアクセスを制御できます。 (例の特性は、グループ メンバーシップと実行コンテキストです)。

データベース レベルの暗号化を使用しない場合、SQL データベース内にあるデータを損なうような攻撃を受けやすくなる可能性があります。

SQL Database の Transparent Data Encryption の詳細については、「[Azure SQL Database での Transparent Data Encryption](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx)」を参照してください。

## <a name="enable-database-auditing"></a>データベース監査を有効にする
SQL Server データベース エンジンまたは個々のデータベースのインスタンスを監査するには、イベントを追跡し、ログに記録します。 SQL Server では、サーバー レベルのイベントの仕様とデータベース レベルのイベントの仕様が含まれている監査を作成できます。 監査されるイベントは、イベント ログまたは監査ファイルに書き込むことができます。

インストールに対する政府や標準の要件に応じて、SQL Server には複数のレベルの監査があります。 SQL Server 監査では、さまざまなサーバーおよびデータベース オブジェクトで監査を有効化、格納、表示するためのツールとプロセスが提供されます。

[Azure SQL Database Auditing](../sql-database/sql-database-auditing.md) では、データベース イベントを追跡し、Azure Storage アカウントの監査ログにイベントを書き込みます。

監査により、規定遵守の維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違反を示す矛盾や異常に対する洞察が容易になります。 監査は、標準準拠を促進しますが、準拠を保証するものではありません。

データベースの監査とこれを有効にする方法の詳細については、「[SQL Database 監査の使用](../sql-database/sql-database-auditing.md)」を参照してください。

## <a name="enable-database-threat-detection"></a>データベースの脅威の検出を有効にする
脅威の防止は検出だけではありません。 データベースの脅威の防止は次のとおりです。

- データを保護できるように、最も機密性の高いデータを検出して分類します。
- データベースを保護できるように、データベースにセキュリティで保護された構成を実装します。
- 潜在的な脅威が発生すると同時に検出することによって、迅速に対応して修復できるようにします。

**ベスト プラクティス**: データベースの機密データを検出、分類、およびラベル付けする。   
**詳細**: Azure SQL Database の [[Data Discovery and Classification]](../sql-database/sql-database-data-discovery-and-classification.md) (データの検出と分類) を有効にすることによって、SQL Database のデータを分類します。 Azure ダッシュ ボードで機密データへのアクセスを監視することも、レポートをダウンロードすることもできます。

**ベスト プラクティス**: データベースのセキュリティを予防的に向上できるように、データベースの脆弱性を追跡する。   
**詳細**: Azure SQL Database の[脆弱性評価](../sql-database/sql-vulnerability-assessment.md)サービスを使用して、データベースの潜在的な脆弱性をスキャンします。 このサービスでは、構成の不備、過剰な権限、保護されていない機密データなどセキュリティの脆弱性にフラグを設定し、ベスト プラクティスからの逸脱を示す、ルールのナレッジ ベースが利用されます。

ルールは Microsoft のベスト プラクティスに基づき、データベースとその貴重なデータに最も大きなリスクとなるセキュリティの問題に注目します。 これらのルールは、データベース レベルの問題およびサーバーのファイアウォール設定やサーバー レベルの権限など、サーバー レベルのセキュリティ問題もカバーしたものとなっています。 また、これらの規則は、規制機関によるコンプライアンス基準を満たすための多くの要件も表しています。

**ベスト プラクティス**: 脅威の検出を有効にします。  
**詳細**: Azure SQL Database の[脅威の検出](../sql-database/sql-database-threat-detection.md)を有効にし、セキュリティ アラートと、脅威を調査して軽減する方法の推奨事項を取得します。 不審なデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃や、異常なデータベース アクセスやクエリのパターンが見つかった場合に、アラートを受信します。

[Advanced Threat Protection](../sql-database/sql-advanced-threat-protection.md) は、高度な SQL セキュリティ機能のための統合パッケージです。 これには前に説明したサービス (データの検出と分類、脆弱性の評価、脅威の検出) が含まれています。 これらの機能を 1 つの場所で有効にして管理できます。

これらの機能を有効にすると次のことが可能になります。

- データのプライバシー基準および規制のコンプライアンス要件を満たします。
- データベースへのアクセスを制御し、セキュリティを強化します。
- 変更の追跡が困難な動的データベース環境を監視します。
- 潜在的な脅威を検出し、これに対応します。

さらに、脅威の検出は、アラートを、すべての Azure リソースのセキュリティ状態の一元的に表示するための Azure Security Center と統合します。

## <a name="next-steps"></a>次の手順
Azure を使用してクラウド ソリューションを設計、デプロイ、管理するときに使用するセキュリティのベスト プラクティスの詳細については、「[Azure セキュリティのベスト プラクティスとパターン](security-best-practices-and-patterns.md)」を参照してください。

Azure のセキュリティとそれに関連する Microsoft サービスの一般情報については、以下のリソースを参照してください。
* [Azure セキュリティ チーム ブログ](https://blogs.msdn.microsoft.com/azuresecurity/) – Azure のセキュリティに関する最新情報を提供しています。
* [Microsoft セキュリティ レスポンス センター](https://technet.microsoft.com/library/dn440717.aspx) - このサイトでは、Azure に関する問題を含め、マイクロソフトのセキュリティの脆弱性を報告できます。メールの場合は、secure@microsoft.com 宛に報告してください。
