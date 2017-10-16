---
title: "Azure のデータベース セキュリティに関するベスト プラクティス | Microsoft Docs"
description: "この記事では、Azure のデータベース セキュリティに関するベスト プラクティス一式を提供します。"
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2017
ms.author: tomsh
ms.openlocfilehash: 5bd6fe0dd369b3bbc7ca0d697c964badda557cb8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-security-best-practices"></a>Azure のデータベース セキュリティに関するベスト プラクティス

データベースを管理するうえでセキュリティは最大の懸念事項であり、Azure SQL Database では常に優先事項として扱われてきました。 データベースは、大部分の規制要件やセキュリティ要件 (特に HIPAA、ISO 27001/27002、PCI DSS レベル 1 など) を満たすように厳密なセキュリティで保護することができます。 現在のセキュリティ コンプライアンス認定のリストは、[Microsoft セキュリティ センターのサイト](http://azure.microsoft.com/support/trust-center/services/)で確認できます。 規制の要件に基づいて特定の Azure データセンターにデータベースを配置することも選択できます。

この記事では、Azure のデータベース セキュリティに関するベスト プラクティスのコレクションについて説明します。 このベスト プラクティスは、Azure のデータベース セキュリティに関して Microsoft が蓄積してきたノウハウと、ユーザーの皆様の経験に基づいています。

それぞれのベスト プラクティスについて、次の点を説明します。

-   ベスト プラクティスの内容
-   そのベスト プラクティスをお勧めする理由
-   そのベスト プラクティスを実践しなかった場合に発生する可能性がある事態
-   そのベスト プラクティスを実践する方法

この「Azure のデータベース セキュリティに関するベスト プラクティス」の記事は、この記事の執筆時点における共通認識と、Azure プラットフォームの機能および機能セットに基づいています。 共通認識とテクノロジは時間が経つにつれて変化するため、そのような変化に対応するために、この記事は定期的に更新されます。

この記事で説明する Azure のデータベース セキュリティに関するベスト プラクティスは、次のとおりです。

-   ファイアウォール規則を使用してデータベースへのアクセスを制限する
-   データベース認証を有効にする
-   暗号化を使用してデータを保護する
-   転送中のデータを保護する
-   データベース監査を有効にする
-   データベースの脅威の検出を有効にする


## <a name="use-firewall-rules-to-restrict-database-access"></a>ファイアウォール規則を使用してデータベースへのアクセスを制限する

Microsoft Azure SQL Database は、Azure およびその他のインターネット ベースのアプリケーション用のリレーショナル データベース サービスを提供します。 SQL Database では、アクセスのセキュリティを提供するために、IP アドレスで接続を制限するファイアウォール規則、ユーザーに ID の指定を要求する認証メカニズム、およびユーザーを特定のアクションとデータに限定する承認メカニズムによって、アクセスを制御します。 ファイアウォールは、どのコンピューターに権限を持たせるかを指定するまで、データベース サーバーへのすべてのアクセスを遮断します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。

![ファイアウォール規則](./media/azure-database-security-best-practices/azure-database-security-best-practices-Fig1.png)

Azure SQL Database サービスは TCP ポート 1433 経由でのみ利用できます。 コンピューターから SQL Database にアクセスするには、クライアント コンピューターのファイアウォールで、TCP ポート 1433 での TCP 通信の発信を許可する必要があります。 他のアプリケーションで必要ない場合は、ファイアウォール規則を使用して TCP ポート 1433 での着信接続をブロックします。

接続処理の一部として、Azure 仮想マシンからの接続は worker ロールごとに異なる別の IP アドレスとポートにリダイレクトされます。 ポート番号の範囲は、11000 ～ 11999 です。 TCP ポートの詳細については、「[ADO.NET 4.5 用の 1433 以外のポート](https://docs.microsoft.com/azure/sql-database/sql-database-develop-direct-route-ports-adonet-v12)」を参照してください。

> [!Note]
> SQL Database のファイアウォール規則の詳細については、[SQL Database のファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)に関するページを参照してください。

## <a name="enable-database-authentication"></a>データベース認証を有効にする
SQL Database は SQL 認証と Azure Active Directory 認証 (Azure AD Authentication) の 2 種類の認証をサポートしています。

**SQL 認証**は次の場合にお勧めします。

-   Windows ドメインで認証されていないユーザーがいるオペレーティング システム混在環境を SQL Azure がサポートできる場合。
-   SQL Server 認証を必要とする、古いアプリケーションやサード パーティが提供するアプリケーションを SQL Azure でサポートできる場合。
-   ユーザーが不明または信頼されていないドメインから接続できる場合。 たとえば、既存の顧客が割り当てられた SQL Server ログインで接続して注文の状態を受信するアプリケーションなどです。
-   ユーザーが独自の ID を作成する Web ベースのアプリケーションを SQL Azure でサポートできる場合。
-   ソフトウェア開発者が、事前設定された既知の SQL Server ログインに基づく複雑なアクセス許可階層を使用してアプリケーションを配布できる場合。

> [!Note]
> ただし、SQL Server 認証は Kerberos セキュリティ プロトコルを使用できません。

**SQL 認証**を使用する場合は、次のことを行う必要があります。

-   強力な資格情報を自分で管理します。
-   接続文字列で資格情報を保護します。
-   (場合により) Web サーバーからデータベースにネットワーク経由で渡される資格情報を保護します。 詳しくは、「[How To: ASP.NET 2.0 で SQL 認証を使用して SQL Server へ接続する方法](https://msdn.microsoft.com/library/ms998300.aspx)」をご覧ください。

**Azure Active Directory 認証**は、Azure Active Directory (Azure AD) の ID を使用して Microsoft Azure SQL Database と [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) に接続するメカニズムです。 Azure AD 認証を使用すると、データベース ユーザーの ID や他の Microsoft サービスを一元管理できます。 ID の一元管理では、1 か所でデータベース ユーザーを管理できるようになるため、アクセス許可の管理が容易になります。 次のような利点があります。

-   SQL Server 認証の代替方法が用意されています。
-   データベース サーバー全体でユーザー ID が急増するのを防ぎます。
-   1 か所でのパスワードのローテーションを許可します。
-   顧客は外部の (AAD) グループを使用してデータベースのアクセス許可を管理できます。
-   統合 Windows 認証や、Azure Active Directory でサポートされる他の認証形式を有効にすることで、パスワードが保存されないようにすることができます。
-   Azure AD 認証では、包含データベース ユーザーを使用して、データベース レベルで ID を認証します。
-   Azure AD は、SQL Database に接続するアプリケーション向けにトークンベース認証をサポートしています。
-   Azure AD 認証は、ADFS (ドメイン フェデレーション) またはドメインを同期しないローカル Azure Active Directory のネイティブ ユーザー/パスワード認証をサポートします。
-   Azure AD 認証は、Multi-Factor Authentication (MFA) を含む Active Directory ユニバーサル認証を使用する SQL Server Management Studio からの接続をサポートします。 MFA には、電話、テキスト メッセージ、スマート カードと暗証番号 (PIN)、モバイル アプリ通知など、簡単な各種確認オプションによる強力な認証が含まれます。 詳細については、「 [SQL Database と SQL Data Warehouse での Azure AD MFA のための SSMS のサポート](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication)」を参照してください。

構成の手順には、Azure Active Directory 認証を構成して使用する次の手順が含まれます。

-   Azure AD を作成して設定します。
-   省略可能: Active Directory を関連付けるか、現在 Azure サブスクリプションに関連付けられている Active Directory を変更します。
-   Azure SQL Server または [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/) の Azure Active Directory 管理者を作成します。
- クライアント コンピューターを構成します。
-   Azure AD の ID にマップされている包含データベース ユーザーをデータベースに作成します。
-   Azure AD の ID を使用してデータベースに接続します。

詳しくは、[こちら](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)をご覧ください。

## <a name="protect-your-data-using-encryption"></a>暗号化を使用してデータを保護する

[Azure SQL Database Transparent Data Encryption (TDE)](https://msdn.microsoft.com/library/dn948096.aspx) を使用すると、データベース、関連付けられているバックアップ、保管されているトランザクション ログ ファイルの暗号化と解読をリアルタイムで実行することにより、悪意のあるアクティビティの脅威からデータを保護できます。アプリケーションを変更する必要はありません。 TDE は、データベース暗号化キーと呼ばれる対称キーを使用してデータベース全体のストレージを暗号化します。

ストレージ全体を暗号化している場合でも、データベース自体を暗号化することは非常に重要です。 これは、多層防御型のデータ保護手法の 1 つです。 Azure SQL Database を使用している場合、クレジット カードや社会保障番号などの機微なデータを保護する必要があるときには、さまざまな業界標準 (HIPAA、PCI など) の要件を満たす FIPS 140-2 検証済み 256 ビット AES 暗号化を使ってデータベースを暗号化できます。

データベースを TDE で暗号化する場合、[バッファー プール拡張機能 (BPE)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) に関連するファイルは暗号化されないことを理解しておく必要があります。 BPE 関連ファイルについては、[BitLocker](https://technet.microsoft.com/library/cc732774) や[暗号化ファイル システム (EFS)](https://technet.microsoft.com/library/cc700811.aspx) などのファイル システム レベルの暗号化ツールを使用する必要があります。

セキュリティ管理者やデータベース管理者などの特権を持つユーザーは、TDE で暗号化されたデータベースのデータにもアクセスできるため、次の推奨事項にも従う必要があります。

-   データベース レベルでの SQL 認証を有効にします。
-   [RBAC ロール](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is)を使用して Azure AD Authentication を使用します。
-   ユーザーとアプリケーションは、別々のアカウントを使って認証する必要があります。 そうすることで、ユーザーとアプリケーションに付与されるアクセス許可を制限し、悪意のあるアクティビティのリスクを減らすことができます。
-   固定データベース ロール (db_datareader、db_datawriter など) を使用してデータベース レベルのセキュリティを実装しますが、アプリケーション用のカスタム ロールを作成して、選択したデータベース オブジェクトに明示的なアクセス許可を付与することもできます。

その他の方法でデータを暗号化するには、次を検討してください。

-   [セルレベルの暗号化](https://msdn.microsoft.com/library/ms179331.aspx) により、暗号化キーが異なるデータの特定の列またはセルを暗号化できます。
-   Always Encrypted を使用した使用時の暗号化: [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) を使用すると、クライアントはクライアント アプリケーション内の機微なデータを暗号化することができます。暗号化キーがデータベース エンジン (SQL Database や SQL Server) に公開されることはありません。 これにより、Always Encrypted では、データの所有者 (データを表示できるユーザー) とデータの管理者 (アクセス権は付与しないユーザー) を分離できます。
-   行レベルのセキュリティの使用: 行レベルのセキュリティを使用すると、クエリを実行しているユーザーの特性 (たとえば、グループ メンバーシップや実行コンテキストなど) に基づいて、データベース テーブル内の行へのアクセスを制御できます。 詳細については、「[行レベルのセキュリティ](https://msdn.microsoft.com/library/dn765131)」を参照してください。

## <a name="protect-data-in-transit"></a>転送中のデータを保護する
転送中のデータの保護は、データ保護戦略に欠かせない要素です。 データはさまざまな場所を経由して転送されるため、一般的には常時 SSL/TLS プロトコルを使用してデータをやり取りすることが推奨されています。 状況によっては、オンプレミスとクラウド インフラストラクチャ間の通信チャネル全体を、仮想プライベート ネットワーク (VPN) を使用して隔離する必要があります。

オンプレミス インフラストラクチャと Azure 間のデータ移動については、HTTPS や VPN などの適切なセキュリティ対策を検討してください。

オンプレミスにある複数のワークステーションから Azure へのアクセスをセキュリティ保護する必要がある場合には、[Azure のサイト間 VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create) を使用します。

オンプレミスまたはオフプレミスにある個々のワークステーションから Azure へのアクセスをセキュリティ保護する必要がある組織は、[ポイント対サイト VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create) の使用を検討してください。

大規模なデータ セットは、[ExpressRoute](https://azure.microsoft.com/services/expressroute/) などの専用高速 WAN リンクを利用して移動できます。 ExpressRoute を使用する場合、[SSL/TLS](https://support.microsoft.com/kb/257591) などのプロトコルを使用してアプリケーション レベルでデータを暗号化することで、さらにセキュリティを強化できます。

Azure Portal で Azure Storage を操作する場合、すべてのトランザクションは HTTPS 経由で行われます。 HTTPS 経由の [Storage REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) も、[Azure Storage](https://azure.microsoft.com/services/storage/) と [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) の操作に使用できます。

転送中のデータを保護しない場合、[man-in-the-middle 攻撃](https://technet.microsoft.com/library/gg195821.aspx)、[盗聴](https://technet.microsoft.com/library/gg195641.aspx)、セッション ハイジャックに対して脆弱になります。 このような攻撃は、機密データへのアクセスを取得するための最初の手順として実行される場合があります。

Azure VPN オプションについて詳しくは、「[VPN Gateway の計画と設計](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-plan-design)」をご覧ください。

## <a name="enable-database-auditing"></a>データベース監査を有効にする
SQL Server データベース エンジンまたは個々のデータベースのインスタンスを監査するには、データベース エンジンで発生するイベントを追跡し、ログに記録します。 SQL Server 監査では、サーバー監査を作成できます。これには、サーバー レベルのイベント用のサーバー監査の仕様と、データベース レベルのイベント用のデータベース監査仕様を含めることができます。 監査されるイベントは、イベント ログまたは監査ファイルに書き込むことができます。

インストールに対する政府や標準の要件に応じて、SQL Server には複数のレベルの監査があります。 SQL Server 監査では、さまざまなサーバーおよびデータベース オブジェクトで監査を有効化、格納、表示するために必要なツールとプロセスが提供されます。

[Azure SQL Database Auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) では、データベース イベントを追跡し、Azure Storage アカウントの監査ログにイベントを書き込みます。

監査により、規定遵守の維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違犯の疑いを示す差異や異常に対する洞察が容易になります。

監査は、標準準拠を強化し促進しますが、準拠を保証するものではありません。

データベースの監査とその有効化の方法について詳しくは、「[Azure Security Center で SQL サーバーの監査と脅威検出の有効化](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-servers)」の記事をご覧ください。

## <a name="enable-database-threat-detection"></a>データベースの脅威の検出を有効にする
SQL の脅威の検出では、異常なアクティビティに対するセキュリティ アラートを提供することによって、潜在的な脅威が発生したときにユーザーがそれを検出して対応できます。 不審なデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃や、異常なデータベース アクセス パターンが見つかった場合に、アラートを受信します。 SQL の脅威の検出によるアラートは、不審なアクティビティの詳細と、脅威の調査や危険性の軽減のために推奨される対処方法を提供します。

たとえば、SQL インジェクションはインターネットにおける Web アプリケーションの一般的なセキュリティ問題の 1 つであり、データ駆動型アプリケーションの攻撃に使用されます。 攻撃者は、アプリケーションの脆弱性を利用してアプリケーションの入力フィールドに悪意のある SQL ステートメントを挿入し、データベースのデータを侵害または変更します。

Azure Portal でデータベースの脅威の検出を設定する方法について詳しくは、「[SQL Database の脅威の検出](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)」をご覧ください。

また、SQL の脅威の検出では、アラートが [Azure Security Center](https://azure.microsoft.com/services/security-center/) と統合されています。 60 日間は無料で試用版をご利用いただけます。

データベースの脅威の検出とその有効化の方法について詳しくは、「[Azure Security Center で SQL サーバーの監査と脅威検出の有効化](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-servers)」の記事をご覧ください。

## <a name="conclusion"></a>まとめ
Azure データベースは、多くの組織や規制のコンプライアンス要件を満たすセキュリティ機能を豊富に備えた堅牢なデータベース プラットフォームです。 データへの物理的アクセスの制御と、Transparent Data Encryption、セルレベルの暗号化、行レベルのセキュリティによるファイル、列、または行レベルのデータ セキュリティの各種オプションの使用によって、データを保護することができます。 Always Encrypted によって、暗号化されたデータに対する操作も可能になり、アプリケーションの更新プロセスが簡略化されます。 そして、SQL Database のアクティビティの監査ログにアクセスして必要な情報を入手し、データがいつどのようにアクセスされているかを把握することができます。

## <a name="next-steps"></a>次のステップ
- ファイアウォール規則の詳細については、[ファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)に関するページを参照してください。
- ユーザーとログインの詳細については、[ログインの管理](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)に関するページを参照してください。
- チュートリアルについては、「[Azure SQL データベースのセキュリティ保護](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial)」を参照してください。
