---
title: セキュリティの概要
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search は、SOC 2、HIPAA、およびその他の認定に準拠しています。 接続とデータの暗号化、認証、および ID は、フィルター式のユーザーとグループのセキュリティ識別子を介してアクセスします。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.custom: references_regions
ms.openlocfilehash: 4bf8f5d7bb8fd262fefc7cbf2f8ca906136509d5
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88225276"
---
# <a name="security-in-azure-cognitive-search---overview"></a>Azure Cognitive Search のセキュリティ - 概要

この記事では、コンテンツと運用を保護できる Azure Cognitive Search の主要なセキュリティ機能について説明します。

+ ストレージ層に、インデックス、シノニム マップ、インデクサー、データ ソース、およびスキルセットの定義を含む、ディスクに保存されるすべてのサービス マネージド コンテンツに対する保存時の暗号化が組み込まれています。 Azure Cognitive Search によって、インデックス付きコンテンツを追加で暗号化するカスタマー マネージド キー (CMK) も追加サポートされています。 インデックス付きコンテンツを完全に二重に暗号化する CMK での暗号化は、2020 年 8 月 1 日以降に作成されたサービスでは、一時ディスク上のデータにも拡張されています。

+ 受信セキュリティでは、要求に対する API キーから、ファイアウォールの受信ルール、さらにはパブリック インターネットからサービスを完全に保護するプライベート エンドポイントに至るまで、高度化するセキュリティ レベルで検索サービス エンドポイントが保護されます。

+ 送信セキュリティは、外部ソースからコンテンツをプルするインデクサーに適用されます。 送信要求の場合は、Azure Storage、Azure SQL、Cosmos DB、またはその他の Azure データ ソースからのデータにアクセスするときに、信頼されたサービスを検索するために、マネージド ID を設定します。 マネージド ID は、接続の資格情報またはアクセス キーの代わりに使用されます。 この記事では、送信セキュリティについては説明しません。 この機能の詳細については、[マネージド ID を使用したデータ ソースへの接続](search-howto-managed-identities-data-sources.md)に関するページを参照してください。

セキュリティ アーキテクチャと各機能カテゴリの概要については、こちらのビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

<a name="encryption"></a>

## <a name="encrypted-transmissions-and-storage"></a>伝送とストレージの暗号化

Azure Cognitive Search での暗号化は、接続時および転送時に開始され、ディスクに格納されたコンテンツにまでおよびます。 パブリック インターネット上の検索サービスでは、Azure Cognitive Search によって HTTPS ポート 443 がリッスンされます。 すべてのクライアントとサービスの間の接続では、TLS 1.2 暗号化が使用されます。 これより前のバージョン (1.0 または 1.1) はサポートされていません。

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

<a name="service-access-and-authentication"></a>

## <a name="inbound-security-and-endpoint-protection"></a>受信セキュリティとエンドポイント保護

受信セキュリティ機能では、高度化するセキュリティと複雑さによって検索サービス エンドポイントが保護されます。 まず、すべての要求に認証済みアクセスの API キーが必要です。 次に、必要に応じて、特定の IP アドレスへのアクセスを制限するファイアウォール規則を設定できます。 高度な保護では、3 つ目のオプションとして、Azure プライベート リンクを有効にして、すべてのインターネット トラフィックからサービス エンドポイントを保護します。

### <a name="public-access-using-api-keys"></a>API キーを使用したパブリック アクセス

既定で検索サービスは、検索サービス エンドポイントへの管理者アクセスまたはクエリ アクセス用のキーベースの認証を使用して、パブリック クラウドを介してアクセスされます。 API キーは、ランダムに生成された数字と文字から成る文字列です。 キーの種類 (管理者またはクエリ) によって、アクセスのレベルが決まります。 有効なキーの送信は、要求が信頼されたエンティティのものであることの証明と見なされます。

検索サービスへのアクセスには 2 つのレベルがあり、次の API キーによって有効になります。

+ 管理者キー (検索サービスでの[作成、読み取り、更新、削除](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete)操作用に読み取り/書き込みアクセスを許可)

+ クエリ キー (インデックスのドキュメント コレクションへの読み取り専用アクセスを許可)

*管理者キー*は、サービスのプロビジョニング時に作成されます。 2 つの管理キーがあり、区別するために "*プライマリ*" と "*セカンダリ*" と呼ばれますが、これらは実際には交換可能です。 各サービスが 2 つの管理キーを持つため、サービスにアクセスしたままで一方のキーをロールオーバーできます。 Azure のセキュリティのベスト プラクティスに従って[管理キーを再生成](search-security-api-keys.md#regenerate-admin-keys)できますが、管理キーの合計数は追加できません。 Search サービスごとに最大 2 個の管理キーがあります。

*クエリ キー*は必要に応じて作成され、クエリを発行するクライアント アプリケーション向けに設計されています。 クエリ キーは最大 50 個まで作成できます。 アプリケーション コードでは、特定のインデックスのドキュメント コレクションへの読み取り専用アクセスを許可するために検索の URL とクエリ API キーを指定します。 エンドポイント、読み取り専用アクセスのための API キー、およびターゲット インデックスの組み合わせにより、クライアント アプリケーションからの接続のスコープとアクセス レベルが定義されます。

要求ごとに認証が必要です。この各要求は必須のキー、操作、およびオブジェクトで構成されています。 2 つのアクセス許可レベル (完全または読み取り専用) とコンテキスト (インデックスでのクエリ操作など) を組み合わせれば、サービス操作に対して全範囲のセキュリティを実現できます。 キーの詳細については、[API キーの作成と管理](search-security-api-keys.md)に関するページを参照してください。

### <a name="ip-restricted-access"></a>IP 制限アクセス

検索サービスへのアクセスをさらに制御するために、特定の IP アドレスまたは IP アドレス範囲へのアクセスを許可する受信ファイアウォール規則を作成できます。 すべてのクライアント接続は、許可された IP アドレスを使用して行う必要があります。それ以外の場合、接続は拒否されます。

[受信アクセスを構成](service-configure-firewall.md)するには、ポータルを使用します。

または、管理 REST API を使用します。 [IpRule](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service#IpRule) パラメーターを指定した API バージョン 2020-03-13 では、検索サービスへのアクセスを付与する IP アドレスを個別に、あるいは範囲で特定することで、サービスへのアクセスを制限できます。

### <a name="private-endpoint-no-internet-traffic"></a>プライベート エンドポイント (インターネット トラフィックなし)

Azure Cognitive Search の[プライベート エンドポイント](../private-link/private-endpoint-overview.md)では、[仮想ネットワーク](../virtual-network/virtual-networks-overview.md)上のクライアントが[プライベート リンク](../private-link/private-link-overview.md)を介して、検索インデックス内のデータに安全にアクセスできます。

プライベート エンドポイントでは、検索サービスに接続するために仮想ネットワークのアドレス空間の IP アドレスが使用されます。 クライアントと検索サービス間のネットワーク　トラフィックは、仮想ネットワークおよび Microsoft バックボーン ネットワーク上のプライベートリンクを経由することで、パブリック インターネット上での露出を排除します。 VNET によって、オンプレミス ネットワークやインターネットを利用したリソース間の安全な通信が可能になります。

このソリューションは最も安全ですが、追加のサービスを使用すると、追加のコストがかかります。そのため、使用の前に利点の詳細を明確に理解しておく必要があります。 コストの詳細については、[価格](https://azure.microsoft.com/pricing/details/private-link/)に関するページを参照してください。 これらのコンポーネントを連携させる方法の詳細については、この記事の上部にあるビデオをご覧ください。 プライベート エンドポイント オプションの説明は、ビデオの 5:48 から始まります。 エンドポイントを設定する方法については、[Azure Cognitive Search でのプライベート エンドポイントの作成](service-create-private-endpoint.md)に関するページを参照してください。

## <a name="index-access"></a>インデックへのアクセス

Azure Cognitive Search では、個別のインデックスはセキュリティ保護可能なオブジェクトでありません。 その代わり、インデックスへのアクセスは操作のコンテキストと共にサービス層 (サービスへの読み取りまたは書き込みアクセス) で決定されます。

エンドユーザーのアクセスの場合、クエリ キーを使用して接続するようにクエリ要求を構成できます (これにより、すべての要求が読み取り専用になります)。また、アプリで使用される特定のインデックスを含めることができます。 クエリ要求では、インデックスの結合または複数のインデックスへの同時アクセスといった概念がないので、当然すべての要求のターゲットは単一のインデックスです。 そのため、クエリ要求自体 (キーと単一のターゲット インデックス) の構築でセキュリティ境界を定義します。

インデックスに対する管理者と開発者のアクセスは区別されていません。サービスによって管理されているオブジェクトの作成、削除、更新を行うには、どちらも書き込みアクセスが必要です。 サービスに対する管理者キーを持っている人はだれでも、そのサービスのインデックスの読み取り、変更、削除を行えます。 インデックスが誤って削除されたり、悪意によって削除されたりすることを防止するうえで、コード資産の社内ソース管理は、望ましくないインデックスの削除または変更を修復する対応策になります。 Azure Cognitive Search は可用性を確保するためにクラスター内のフェールオーバーを備えていますが、インデックスの作成または読み込みに使用される専用コードを格納したり実行したりしません。

インデックス レベルでセキュリティ境界が必要なマルチテナントのソリューションでは、通常、そのソリューションに顧客がインデックス分離を処理するための中間層が含まれています。 マルチテナントのユース ケースの詳細については、[マルチテナント SaaS アプリケーションと Azure Cognitive Search の設計パターン](search-modeling-multitenant-saas-applications.md)に関する記事を参照してください。

## <a name="user-access"></a>ユーザー アクセス

ユーザーがインデックスおよびその他のオブジェクトにアクセスする方法は、要求の API キーの種類によって決まります。 ほとんどの開発者は、クライアント側の検索要求に対して "[*クエリ キー*](search-security-api-keys.md)" を作成して割り当てます。 クエリ キーにより、インデックス内の検索可能なコンテンツに対する読み取り専用アクセスが付与されます。

検索結果をユーザーごとにきめ細かく制御する必要がある場合は、クエリに対するセキュリティ フィルターを作成し、特定のセキュリティ ID に関連付けられているドキュメントを返すことができます。 事前定義されたロールとロールの割り当ては使用されず、ID に基づいてドキュメントとコンテンツの検索結果をトリミングする "*フィルター*" として ID ベースのアクセス制御が実装されます。 次の表では、承認されていないコンテンツの検索結果をトリミングする 2 つのアプローチについて説明しています。

| アプローチ | 説明 |
|----------|-------------|
|[ID フィルターに基づいたセキュリティによるトリミング](search-security-trimming-for-azure-search.md)  | ユーザー ID アクセス制御を実装する基本的なワークフローについて記載しています。 また、インデックスへのセキュリティ ID の追加について取り上げているほか、そのフィールドに対してフィルター処理を行い、禁止されているコンテンツの結果をトリミングする方法について説明しています。 |
|[Azure Active Directory ID に基づいたセキュリティによるトリミング](search-security-trimming-for-azure-search-with-aad.md)  | この記事は前の記事を拡張したものであり、Azure クラウド プラットフォームの[無料サービス](https://azure.microsoft.com/free/)の 1 つである Azure Active Directory (AAD) から ID を取得する手順について説明しています。 |

## <a name="administrative-rights"></a>管理者権限

[Azure ロール ベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md) は、Azure リソースをプロビジョニングするために [Azure Resource Manager](../azure-resource-manager/management/overview.md) 上に構築された承認システムです。 Azure Cognitive Search では、Resource Manager を使用して、サービスの作成または削除、API キーの管理、サービスのスケーリングが行われます。 そのため、[ポータル](search-manage.md)、[PowerShell](search-manage-powershell.md)、[管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api) のどれを使用しているかにかかわらず、Azure で割り当てられているロールによって、これらのタスクを実行できるユーザーが決定されます。

これに対して、サービスでホストされているコンテンツに対する管理者権限 (インデックスを作成または削除する機能など) は、[前のセクション](#index-access)で説明されているように、API キーによって付与されます。

> [!TIP]
> Azure 全体のメカニズムを使用して、サブスクリプションまたはリソースをロックし、管理者権限を持つユーザーが検索サービスを誤って、または許可なく削除しないようにすることができます。 詳細については、[リソースのロックによる予期せぬ削除の防止](../azure-resource-manager/management/lock-resources.md)に関するページを参照してください。

## <a name="certifications-and-compliance"></a>認定資格とコンプライアンス

Azure Cognitive Search は、パブリック クラウドと Azure Government の両方について、グローバル、地域、および業界固有の複数の標準への準拠が認定されています。 完全な一覧については、公式の監査レポート ページから [**Microsoft Azure Compliance Offerings** ホワイトペーパー](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/)をダウンロードしてください。

コンプライアンスのため、[Azure セキュリティ ベンチマーク](../security/benchmarks/introduction.md)の安全性の高いベスト プラクティスを、[Azure Policy](../governance/policy/overview.md) を使用して実装できます。 Azure セキュリティ ベンチマークは、サービスやデータに対する脅威を軽減するために実行する必要のある主要なアクションにマップされる、セキュリティ コントロールに体系化された、セキュリティに関する推奨事項を集めたものです。 現在は、[ネットワーク セキュリティ](../security/benchmarks/security-control-network-security.md)、[ログ記録および監視](../security/benchmarks/security-control-logging-monitoring.md)および[データ保護](../security/benchmarks/security-control-data-protection.md)などの数例を含む 11 のセキュリティ コントロールがあります。

Azure Policy は、Azure セキュリティ ベンチマークの標準を含む複数の標準に対するコンプライアンスの管理に役立つ、Azure に組み込まれた機能です。 広く知られたベンチマークについては、コンプライアンス非対応の場合に使用できる、基準と実施可能な対応の両方の組み込みの定義が、Azure Policy によって提供されています。

Azure Cognitive Search には、現在 1 つの定義が組み込まれています。 それは診断ログ用です。 これの組み込みにより、診断ログが欠落している検索サービスを識別するポリシーを割り当てて、有効にすることができます。 詳細については、「[Azure Cognitive Search 用の Azure Policy 規制コンプライアンス コントロール](security-controls-policy.md)」を参照してください。

## <a name="see-also"></a>関連項目

+ [Azure セキュリティの基礎](../security/fundamentals/index.yml)
+ [Azure Security](https://azure.microsoft.com/overview/security)
+ [Azure Security Center](../security-center/index.yml)
