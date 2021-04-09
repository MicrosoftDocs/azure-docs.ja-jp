---
title: セキュリティの概要
titleSuffix: Azure Cognitive Search
description: エンドポイント、コンテンツ、操作を保護する Azure Cognitive Search のセキュリティ機能について説明します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/04/2021
ms.custom: references_regions
ms.openlocfilehash: 46f2035e5f8409cd38faeb9c327b88b06fc7d7a0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100097638"
---
# <a name="security-overview-for-azure-cognitive-search"></a>Azure Cognitive Search のセキュリティの概要

この記事では、コンテンツと運用を保護する Azure Cognitive Search のセキュリティ機能について説明します。

Search サービスに対して行われる受信要求については、検索サービス エンドポイントを保護するためのセキュリティ対策の進化があります。これは、要求の API キーからファイアウォールの受信ルールまでの、パブリック インターネットからサービスを完全に保護するプライベート エンドポイントへの移行です。

他のサービスに対して行われた送信要求については、外部ソースからコンテンツを読み取るインデクサーによって主要な要求が行われます。 接続文字列で資格情報を提供できます。 または、Azure Storage、Azure SQL、Cosmos DB、またはその他の Azure データ ソースからのデータにアクセスするときに、信頼されたサービスを検索するために、マネージド ID を設定できます。 マネージド ID は、接続の資格情報またはアクセス キーの代わりに使用されます。 この機能の詳細については、[マネージド ID を使用したデータ ソースへの接続](search-howto-managed-identities-data-sources.md)に関するページを参照してください。

外部サービスへの書き込み操作はほとんどありません。検索サービスはログ ファイルに書き込み、ナレッジ ストアの作成時、キャッシュされたエンリッチメントの保持時、デバッグセッションの保持時に Azure Storage に書き込みます。 その他のサービス間の呼び出し (Azure Cognitive Services など) は、内部ネットワーク上で行われます。

セキュリティ アーキテクチャと各機能カテゴリの概要については、こちらのビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

## <a name="network-security"></a>ネットワークのセキュリティ

<a name="service-access-and-authentication"></a>

受信セキュリティ機能では、高度化するセキュリティと複雑さによって検索サービス エンドポイントが保護されます。 まず、すべての要求に認証済みアクセスの API キーが必要です。 次に、必要に応じて、特定の IP アドレスへのアクセスを制限するファイアウォール規則を設定できます。 高度な保護では、3 つ目のオプションとして、Azure プライベート リンクを有効にして、すべてのインターネット トラフィックからサービス エンドポイントを保護します。

### <a name="public-access-using-api-keys"></a>API キーを使用したパブリック アクセス

既定で検索サービスは、検索サービス エンドポイントへの管理者アクセスまたはクエリ アクセス用のキーベースの認証を使用して、パブリック クラウドを介してアクセスされます。 有効なキーの送信は、要求が信頼されたエンティティのものであることの証明と見なされます。 キーベースの認証については、次のセクションで説明します。

### <a name="configure-ip-firewalls"></a>IP ファイアウォールの構成

検索サービスへのアクセスをさらに制御するために、特定の IP アドレスまたは IP アドレス範囲へのアクセスを許可する受信ファイアウォール規則を作成できます。 すべてのクライアント接続は、許可された IP アドレスを使用して行う必要があります。それ以外の場合、接続は拒否されます。

:::image type="content" source="media/search-security-overview/inbound-firewall-ip-restrictions.png" alt-text="IP 制限アクセスのサンプル アーキテクチャの図":::

[受信アクセスを構成](service-configure-firewall.md)するには、ポータルを使用します。

または、管理 REST API を使用します。 API バージョン 2020-03-13 以降では、[IpRule](/rest/api/searchmanagement/services/createorupdate#iprule) パラメーターを指定することで、検索サービスへのアクセスを付与する IP アドレスを個別に、あるいは範囲で特定することで、サービスへのアクセスを制限できます。

### <a name="network-isolation-through-a-private-endpoint-no-internet-traffic"></a>プライベート エンドポイント経由のネットワーク分離 (インターネット トラフィックなし)

Azure Cognitive Search の[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を確立し、[仮想ネットワーク](../virtual-network/virtual-networks-overview.md)上のクライアントが[プライベート リンク](../private-link/private-link-overview.md)を介して、検索インデックス内のデータに安全にアクセスできます。

プライベート エンドポイントでは、検索サービスに接続するために仮想ネットワークのアドレス空間の IP アドレスが使用されます。 クライアントと検索サービス間のネットワーク　トラフィックは、仮想ネットワークおよび Microsoft バックボーン ネットワーク上のプライベートリンクを経由することで、パブリック インターネット上での露出を排除します。 VNET によって、オンプレミス ネットワークやインターネットを利用したリソース間の安全な通信が可能になります。

:::image type="content" source="media/search-security-overview/inbound-private-link-azure-cog-search.png" alt-text="プライベート エンドポイント アクセスのサンプル アーキテクチャの図":::

このソリューションは最も安全ですが、追加のサービスを使用すると、追加のコストがかかります。そのため、使用の前に利点の詳細を明確に理解しておく必要があります。 コストの詳細については、[価格](https://azure.microsoft.com/pricing/details/private-link/)に関するページを参照してください。 これらのコンポーネントを連携させる方法の詳細については、この記事の上部にあるビデオをご覧ください。 プライベート エンドポイント オプションの説明は、ビデオの 5:48 から始まります。 エンドポイントを設定する方法については、[Azure Cognitive Search でのプライベート エンドポイントの作成](service-create-private-endpoint.md)に関するページを参照してください。

## <a name="authentication"></a>認証

Search サービスへの受信要求の場合、認証は[必須の API キー](search-security-api-keys.md) (ランダムに生成された数字と文字で構成される文字列) を介して、要求が信頼できるソースからのものであることを証明します。 Azure Cognitive Search は、現在、受信要求の Azure Active Directory 認証をサポートしていません。

インデクサーによって行われた送信要求は、外部サービスによる認証の対象となります。 Azure Cognitive Search のインデクサー サブサービスは、マネージド ID を使用して他のサービスに接続する、Azure 上の信頼されたサービスにできます。 詳細については、「[マネージド ID を使用してデータ ソースへのインデクサー接続を設定する](search-howto-managed-identities-data-sources.md)」を参照してください。

## <a name="authorization"></a>承認

Azure Cognitive Search には、コンテンツ管理とサービス管理のためのさまざまな承認モデルが用意されています。 

### <a name="authorization-for-content-management"></a>コンテンツ管理の承認

コンテンツおよびコンテンツに関連する操作への承認は、書き込みアクセスか、要求で提供される [API キー](search-security-api-keys.md)の特権のいずれかで与えられます。 API キーは認証メカニズムですが、API キーの種類に応じてアクセスを承認することもできます。

+ サービスのプロビジョニング時に作成される管理者キー (検索サービスでの作成、読み取り、更新、削除操作の読み取りおよび書き込みアクセスを許可)

+ 必要に応じて作成され、クエリを発行するクライアント アプリケーション用に設計されたクエリ キー (インデックスのドキュメント コレクションへの読み取り専用アクセスを許可)

アプリケーション コードでは、コンテンツとオプションへのアクセスを許可するエンドポイントと API キーを指定します。 エンドポイントはサービス自体、インデックス コレクション、特定のインデックス、ドキュメント コレクション、特定のドキュメントなどである場合があります。 エンドポイント、操作 (作成要求または更新要求など) とアクセス許可レベル (キーに基づく完全または読み取り専用の権限) が組み合わさることで、コンテンツと操作を保護するセキュリティ保護方法が構成されます。

### <a name="controlling-access-to-indexes"></a>インデックスへのアクセスの制御

Azure Cognitive Search では、個別のインデックスはセキュリティ保護可能なオブジェクトでありません。 その代わり、インデックスへのアクセスは操作のコンテキストと共にサービス層 (提供する API キーに基づく読み取りまたは書き込みアクセス) で決定されます。

読み取り専用アクセスの場合は、クエリ要求を構造化して、[クエリ キー](search-security-rbac.md)を使用して接続し、アプリで使用される特定のインデックスを含めることができます。 クエリ要求では、インデックスの結合または複数のインデックスへの同時アクセスといった概念がないので、当然すべての要求のターゲットは単一のインデックスです。 そのため、クエリ要求自体 (キーと単一のターゲット インデックス) の構築でセキュリティ境界を定義します。

インデックスに対する管理者と開発者のアクセスは区別されていません。サービスによって管理されているオブジェクトの作成、削除、更新を行うには、どちらも書き込みアクセスが必要です。 サービスに対する[管理者キー](search-security-rbac.md)を持っている人はだれでも、そのサービスのインデックスの読み取り、変更、削除を行えます。 インデックスが誤って削除されたり、悪意によって削除されたりすることを防止するうえで、コード資産の社内ソース管理は、望ましくないインデックスの削除または変更を修復する対応策になります。 Azure Cognitive Search は可用性を確保するためにクラスター内のフェールオーバーを備えていますが、インデックスの作成または読み込みに使用される専用コードを格納したり実行したりしません。

インデックス レベルでセキュリティ境界が必要なマルチテナントのソリューションでは、通常、そのソリューションに顧客がインデックス分離を処理するための中間層が含まれています。 マルチテナントのユース ケースの詳細については、[マルチテナント SaaS アプリケーションと Azure Cognitive Search の設計パターン](search-modeling-multitenant-saas-applications.md)に関する記事を参照してください。

### <a name="controlling-access-to-documents"></a>ドキュメントへのアクセスの制御

検索結果をユーザーごとにきめ細かく制御する必要がある場合は、クエリに対するセキュリティ フィルターを作成し、特定のセキュリティ ID に関連付けられているドキュメントを返すことができます。 

概念的には "行レベルのセキュリティ" と同じですが、インデックス内のコンテンツに対する承認は、Azure Active Directory のエンティティにマップされる定義済みのロールまたはロールの割り当てを使用したネイティブではサポートされません。 Cosmos DB など、外部システムのデータに対するユーザーのアクセス許可は、Azure Cognitive Search によってインデックスが作成されるときに、そのデータと共に転送されることはありません。

"行レベルのセキュリティ" を必要とするソリューションのための回避策としては、セキュリティ グループまたはユーザー ID を表すデータソース内にフィールドを作成し、Azure Cognitive Search のフィルターを使用して、ID に基づいてドキュメントとコンテンツの検索結果を選択的にトリミングする方法があります。 次の表では、承認されていないコンテンツの検索結果をトリミングする 2 つのアプローチについて説明しています。

| アプローチ | 説明 |
|----------|-------------|
|[ID フィルターに基づいたセキュリティによるトリミング](search-security-trimming-for-azure-search.md)  | ユーザー ID アクセス制御を実装する基本的なワークフローについて記載しています。 また、インデックスへのセキュリティ ID の追加について取り上げているほか、そのフィールドに対してフィルター処理を行い、禁止されているコンテンツの結果をトリミングする方法について説明しています。 |
|[Azure Active Directory ID に基づいたセキュリティによるトリミング](search-security-trimming-for-azure-search-with-aad.md)  | この記事は前の記事を拡張したものであり、Azure クラウド プラットフォームの[無料サービス](https://azure.microsoft.com/free/)の 1 つである Azure Active Directory (Azure AD) から ID を取得する手順について説明しています。 |

### <a name="authorization-for-service-management"></a>サービス管理の承認

サービス管理操作は、[Azure のロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md) によって承認されます。 Azure RBAC は、Azure リソースをプロビジョニングするために [Azure Resource Manager](../azure-resource-manager/management/overview.md) 上に構築された承認システムです。 

Azure Cognitive Search では、Resource Manager を使用して、サービスの作成または削除、API キーの管理、サービスのスケーリングが行われます。 そのため、[ポータル](search-manage.md)、[PowerShell](search-manage-powershell.md)、[管理 REST API](/rest/api/searchmanagement/search-howto-management-rest-api) のどれを使用しているかにかかわらず、Azure で割り当てられているロールによって、これらのタスクを実行できるユーザーが決定されます。

検索サービスの管理には、[3 つの基本的なロール](search-security-rbac.md#management-tasks-by-role)が定義されます。 ロールの割り当ては、サポートされている任意の方法 (ポータル、PowerShell など) を使用して行うことができ、サービス全体に適用されます。 所有者ロールと共同作成者ロールは、さまざまな管理機能を実行できます。 重要な情報のみを表示するユーザーに閲覧者ロールを割り当てることができます。

> [!Note]
> Azure 全体のメカニズムを使用して、サブスクリプションまたはリソースをロックし、管理者権限を持つユーザーが検索サービスを誤って、または許可なく削除しないようにすることができます。 詳細については、[リソースのロックによる予期せぬ削除の防止](../azure-resource-manager/management/lock-resources.md)に関するページを参照してください。

<a name="encryption"></a>

## <a name="data-protection"></a>データの保護

ストレージ層には、インデックスやシノニム マップ、およびインデクサー、データ ソース、スキルセットの定義など、ディスクに保存されるすべてのサービス マネージド コンテンツに対するデータ暗号化が組み込まれています。 必要に応じて、カスタマー マネージド キー (CMK) を追加して、インデックス付きコンテンツの暗号化を追加で行うことができます。 インデックス付きコンテンツを完全に "二重暗号化" する CMK での暗号化は、2020 年 8 月 1 日以降に作成されたサービスでは、一時ディスク上のデータにも拡張されています。

### <a name="data-in-transit"></a>転送中のデータ

Azure Cognitive Search での暗号化は、接続時および転送時に開始され、ディスクに格納されたコンテンツにまでおよびます。 パブリック インターネット上の検索サービスでは、Azure Cognitive Search によって HTTPS ポート 443 がリッスンされます。 すべてのクライアントとサービスの間の接続では、TLS 1.2 暗号化が使用されます。 これより前のバージョン (1.0 または 1.1) はサポートされていません。

### <a name="encrypted-data-at-rest"></a>保存データの暗号化

検索サービスによって内部で処理されるデータについて、次の表で[データ暗号化モデル](../security/fundamentals/encryption-models.md)を説明しています。 ナレッジ ストア、インクリメンタル エンリッチメント、インデクサー ベースのインデックス作成などの一部の機能は、他の Azure サービスのデータ構造から読み書きされます。 これらのサービスには、Azure Cognitive Search とは別の独自レベルの暗号化があります。

| モデル | キー&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 必要条件&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 制限 | 適用対象 |
|------------------|-------|-------------|--------------|------------|
| サーバー側暗号化 | Microsoft のマネージド キー | なし (組み込み) | なし。2018 年 1 月 24 日以降に作成されたコンテンツに対し、すべてのリージョンのすべてのレベルで使用できます。 | コンテンツ (インデックスとシノニム マップ) と定義 (インデクサー、データ ソース、スキルセット) |
| サーバー側暗号化 | カスタマー マネージド キー | Azure Key Vault | 2019 年 1 月以降に作成されたコンテンツに対し、すべてのリージョンのすべての請求対象レベルで使用できます。 | データ ディスク上のコンテンツ (インデックスとシノニム マップ) |
| サーバー側二重暗号化 | カスタマー マネージド キー | Azure Key Vault | 2020 年 8 月 1 日以降の検索サービスに対し、選択したリージョンの請求対象レベルで使用できます。 | データ ディスクおよび一時ディスク上のコンテンツ (インデックスとシノニム マップ) |

### <a name="service-managed-keys"></a>サービス マネージド キー

サービス マネージド暗号化とは、256 ビットの [AES 暗号化](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)を使用する、[Azure Storage Service Encryption](../storage/common/storage-service-encryption.md) に基づいた、Microsoft の内部処理です。 (2018 年 1 月より前に作成された) 完全に暗号化されていないインデックスに対する増分更新を含む、すべてのインデックス作成で自動的に行われます。

### <a name="customer-managed-keys-cmk"></a>カスタマー マネージド キー (CMK)

カスタマー マネージド キーには、Azure Key Vault という請求対象のサービスが追加で必要です。これのリージョンは別であってもかまいませんが、Azure Cognitive Search と同じサブスクリプションのものである必要があります。 CMK での暗号化を有効にすると、インデックスのサイズが増加し、クエリのパフォーマンスが低下します。 日付を見ると、クエリ時間が 30 ～ 60% 増加することが予想されますが、実際のパフォーマンスは、インデックスの定義やクエリの種類によって変化します。 こうしたパフォーマンスの影響のため、この機能をインデックスに対して有効にするのは、実際に必要な場合のみにすることをお勧めします。 詳細については、[Azure Cognitive Search でのカスタマー マネージド暗号化キーの構成](search-security-manage-encryption-keys.md)に関するページを参照してください。

<a name="double-encryption"></a>

### <a name="double-encryption"></a>二重暗号化

Azure Cognitive Search における二重暗号化は、CMK の拡張機能です。 これは、(1 回目は CMK、次はサービス マネージド キーによって) 二重に暗号化される、データ ディスクに書き込まれる長期的な保存と一時ディスクに書き込まれる短期的な保存におよぶ、広範囲にわたる暗号化です。 2020 年 8 月 1 日より前と後の CMK では、さらに一時ディスクに保存されているデータも暗号化されるようになったことが異なり、これが Azure Cognitive Search の二重暗号化機能となっています。

二重暗号化は、8 月 1 日より後に作成された、次のリージョンの新しいサービスで使用できます。

+ 米国西部 2
+ 米国東部
+ 米国中南部
+ US Gov バージニア州
+ US Gov アリゾナ

## <a name="security-management"></a>セキュリティ管理

### <a name="api-keys"></a>API キー

API キーベースの認証に依存するということは、Azure のセキュリティのベスト プラクティスに従って、定期的に管理者キーを再生成するための計画を立てる必要があることを意味します。 Search サービスごとに最大 2 個の管理キーがあります。 API キーのセキュリティと管理の詳細については、[API キーの作成と管理](search-security-api-keys.md)に関する記事を参照してください。

#### <a name="activity-and-diagnostic-logs"></a>アクティビティ ログと診断ログ

Azure Cognitive Search では、ユーザー ID はログに記録されないため、特定のユーザーに関する情報のログを参照することはできません。 ただし、このサービスでは、ログの作成、読み取り、更新、削除の各操作がログに記録されるため、これらのログを他のログと関連付けて、特定のアクションの機関を理解できる場合があります。

Azure でアラートとログ記録インフラストラクチャを使用すると、クエリ ボリュームの急増や、予想されるワークロードから逸脱したその他のアクションを検出できます。 ログの設定の詳細については、[ログ データの収集と分析](search-monitor-logs.md)および[クエリ要求の監視](search-monitor-queries.md)に関する記事を参照してください。

### <a name="certifications-and-compliance"></a>認定資格とコンプライアンス

Azure Cognitive Search は通常の監査に参加し、パブリック クラウドと Azure Government の両方について、グローバル、リージョン、および業界固有のさまざまな標準に対して認定を受けています。 完全な一覧については、公式の監査レポート ページから [**Microsoft Azure Compliance Offerings** ホワイトペーパー](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/)をダウンロードしてください。

コンプライアンスのため、[Azure セキュリティ ベンチマーク](../security/benchmarks/introduction.md)の安全性の高いベスト プラクティスを、[Azure Policy](../governance/policy/overview.md) を使用して実装できます。 Azure セキュリティ ベンチマークは、サービスやデータに対する脅威を軽減するために実行する必要のある主要なアクションにマップされる、セキュリティ コントロールに体系化された、セキュリティに関する推奨事項を集めたものです。 現在は、[ネットワーク セキュリティ](../security/benchmarks/security-control-network-security.md)、[ログ記録および監視](../security/benchmarks/security-control-logging-monitoring.md)および[データ保護](../security/benchmarks/security-control-data-protection.md)などの数例を含む 11 のセキュリティ コントロールがあります。

Azure Policy は、Azure セキュリティ ベンチマークの標準を含む複数の標準に対するコンプライアンスの管理に役立つ、Azure に組み込まれた機能です。 広く知られたベンチマークについては、コンプライアンス非対応の場合に使用できる、基準と実施可能な対応の両方の組み込みの定義が、Azure Policy によって提供されています。

Azure Cognitive Search には、現在 1 つの定義が組み込まれています。 それは診断ログ用です。 これの組み込みにより、診断ログが欠落している検索サービスを識別するポリシーを割り当てて、有効にすることができます。 詳細については、「[Azure Cognitive Search 用の Azure Policy 規制コンプライアンス コントロール](security-controls-policy.md)」を参照してください。

## <a name="see-also"></a>関連項目

+ [Azure セキュリティの基礎](../security/fundamentals/index.yml)
+ [Azure Security](https://azure.microsoft.com/overview/security)
+ [Azure Security Center](../security-center/index.yml)