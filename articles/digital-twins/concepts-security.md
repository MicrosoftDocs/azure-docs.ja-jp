---
title: Azure Digital Twins ソリューションのセキュリティ
titleSuffix: Azure Digital Twins
description: Azure Digital Twins によるセキュリティのベスト プラクティスについて説明します。
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 81a44d4d0025c841cf56e19d6afee5e95bd44a55
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101730509"
---
# <a name="secure-azure-digital-twins"></a>Azure Digital Twins をセキュリティで保護する

Azure Digital Twins では、セキュリティのために、デプロイ内の特定のデータ、リソース、アクションへのアクセスを正確に制御できます。 この処理には、**Azure ロールベースのアクセス制御 (Azure RBAC)** と呼ばれるきめ細かいロールおよびアクセス許可の管理戦略が使用されています。 Azure RBAC の一般的な原則については、[こちら](../role-based-access-control/overview.md)を参照してください。

Azure Digital Twins では、保存データの暗号化もサポートされています。

## <a name="roles-and-permissions-with-azure-rbac"></a>Azure RBAC を使用したロールとアクセス許可

Azure RBAC は、[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) との統合を介して Azure Digital Twins に提供されます。

Azure RBAC を使用して、"*セキュリティ プリンシパル*" (ユーザー、グループ、またはアプリケーションのサービス プリンシパルである可能性があります) にアクセス許可を付与できます。 セキュリティ プリンシパルは Azure AD によって認証され、応答として OAuth 2.0 トークンを受け取ります。 このトークンは、Azure Digital Twins インスタンスへのアクセス要求を承認するために使用できます。

### <a name="authentication-and-authorization"></a>認証と権限承認

Azure AD では、アクセスは 2 段階のプロセスです。 セキュリティ プリンシパル (ユーザー、グループ、またはアプリケーション) が Azure Digital Twins にアクセスしようとすると、要求は必ず "*認証*" され、"*承認*" されます。 

1. まず、セキュリティ プリンシパルの ID が "*認証*" され、OAuth 2.0 トークンが返されます。
2. 次に、指定されたリソースへのアクセスを "*承認*" するために、トークンが要求の一部として Azure Digital Twins サービスに渡されます。

この認証手順では、実行時のアプリケーション要求に OAuth 2.0 アクセス トークンが含まれている必要があります。 アプリケーションが Azure エンティティ ([Azure Functions](../azure-functions/functions-overview.md) アプリなど) 内で実行されている場合、**マネージド ID** を使用してリソースにアクセスできます。 マネージド ID の詳細については、次のセクションを参照してください。

承認の手順では、セキュリティ プリンシパルに Azure ロールを割り当てる必要があります。 セキュリティ プリンシパルに割り当てられたロールによって、そのプリンシパルが持つアクセス許可が決定されます。 Azure Digital Twins には、Azure Digital Twins リソースの一連のアクセス許可を含む Azure ロールが用意されています。 これらのロールについては、この記事の後半で説明します。

Azure でサポートされているロールとロールの割り当ての詳細については、Azure RBAC ドキュメントの [*各種ロールの概要*](../role-based-access-control/rbac-and-directory-admin-roles.md)に関するページを参照してください。

#### <a name="authentication-with-managed-identities"></a>マネージド ID による認証

[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) は、アプリケーション コードが実行されるデプロイに関連付けられた、セキュリティで保護された ID を作成できる Azure 間機能です。 この ID は、アプリケーションに必要な特定の Azure リソースにアクセスするためのカスタム アクセス許可を付与するアクセス制御ロールに関連付けることができます。

マネージド ID では、Azure プラットフォームがこのランタイム ID を管理します。 ID 自体やアクセスする必要のあるリソース用に、アクセス キーをアプリケーション コードや構成に保存して保護する必要はありません。 Azure App Service アプリケーション内で実行される Azure Digital Twins クライアント アプリで、SAS ルールとキー、またはその他のアクセス トークンを処理する必要はありません。 クライアント アプリには、Azure Digital Twins 名前空間のエンドポイント アドレスのみが必要です。 アプリが接続されると、Azure Digital Twins によってマネージド エンティティのコンテキストがクライアントにバインドされます。 マネージド ID に関連付けられると、Azure Digital Twins クライアントは承認済みのすべての操作を実行できます。 次に、マネージド エンティティを Azure Digital Twins Azure ロールに関連付けることにより、承認が付与されます (説明は後述します)。

#### <a name="authorization-azure-roles-for-azure-digital-twins"></a>Authorization:Azure Digital Twins の Azure ロール

Azure には、Azure Digital Twins [データ プレーン API](how-to-use-apis-sdks.md#overview-data-plane-apis) へのアクセスを承認するための **2 つの Azure 組み込みロール** が用意されています。 名前または ID を使用してロールを参照できます。

| 組み込みのロール | 説明 | id | 
| --- | --- | --- |
| Azure Digital Twins Data Owner (Azure Digital Twins データ所有者) | Azure Digital Twins リソースへのフル アクセス権が付与されます | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
| Azure Digital Twins Data Reader (Azure Digital Twins データ閲覧者) | Azure Digital Twins リソースへの読み取り専用アクセス権が付与されます | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |

ロールを割り当てるには、次の 2 つの方法があります。
* Azure portal で Azure Digital Twins のアクセス制御 (IAM) ペインを使用する (「[*Azure portal を使用して Azure ロールを割り当てる*](../role-based-access-control/role-assignments-portal.md)」を参照してください)
* CLI コマンドを使用してロールを追加または削除する

この詳細な実行手順については、[*Azure Digital Twins チュートリアルのエンド ツー エンドのソリューションの接続*](tutorial-end-to-end.md)に関するページを参照してください。

組み込みロールの定義方法の詳細については、Azure RBAC のドキュメントの [*ロール定義の概要*](../role-based-access-control/role-definitions.md)に関するページを参照してください。 Azure カスタム ロールの作成については、「[*Azure カスタム ロール*](../role-based-access-control/custom-roles.md)」を参照してください。

##### <a name="automating-roles"></a>ロールの自動化

自動化されたシナリオでロールを参照する場合は、名前ではなく **ID** を使用して参照することをお勧めします。 名前はリリースによって変わる可能性がありますが、ID は変わらないため、自動化の場合はより安定した参照になります。

> [!TIP]
> `New-AzRoleAssignment` ([ reference](/powershell/module/az.resources/new-azroleassignment)) などのコマンドレットを使用してロールを割り当てる場合は、`-RoleDefinitionName` ではなく `-RoleDefinitionId` パラメーターを使用して、ロールの名前ではなく ID を渡すことができます。

### <a name="permission-scopes"></a>アクセス許可のスコープ

セキュリティ プリンシパルに Azure ロールを割り当てる前に、セキュリティ プリンシパルに必要なアクセスのスコープを決定します。 ベスト プラクティスとしては、できるだけ狭いスコープのみを付与するのが最善の方法です。

次の一覧は、Azure Digital Twins リソースへのアクセスのスコープを指定できるレベルについて説明したものです。
* モデル:このリソースのアクションを使用すると、Azure Digital Twins にアップロードされた[モデル](concepts-models.md)の制御を指示できます。
* Digital Twins グラフのクエリこのリソースのアクションによって、Azure Digital Twins グラフ内のデジタル ツインに対して[クエリ操作](concepts-query-language.md)を実行する機能が決まります。
* デジタル ツイン:このリソースのアクションを使用すると、ツイン グラフ内の[デジタル ツイン](concepts-twins-graph.md)に対する CRUD 操作を制御できます。
* デジタル ツインの関係:このリソースのアクションによって、ツイン グラフ内のデジタル ツイン間の[関係](concepts-twins-graph.md)に対する CRUD 操作の制御が定義されます。
* イベント ルート:このリソースのアクションによって、Azure Digital Twins から [Event Hub](../event-hubs/event-hubs-about.md)、[Event Grid](../event-grid/overview.md)、または [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) などのエンドポイント サービスに[イベントをルーティング](concepts-route-events.md)するアクセス許可が決まります。

### <a name="troubleshooting-permissions"></a>アクセス許可のトラブルシューティング

ユーザーが自分のロールで許可されていないアクションの実行を試みた場合、`403 (Forbidden)` と書かれた、サービス要求からのエラーが表示されることがあります。 詳細とトラブルシューティングの手順については、"[*Azure Digital Twins 要求が失敗しました: 状態 403 (許可されていません)*](troubleshoot-error-403.md)" といったエラーのトラブルシューティングに関するページを参照してください。

## <a name="managed-identity-for-accessing-other-resources-preview"></a>他のリソースにアクセスするためのマネージド ID (プレビュー)

Azure Digital Twins インスタンスの [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) **マネージド ID** を設定することで、Azure AD で保護されている他のリソース ([Azure Key Vault](../key-vault/general/overview.md) など) に簡単にアクセスできます。 ID は Azure プラットフォームによって管理され、ユーザーがシークレットをプロビジョニングまたはローテーションする必要はありません。 Azure AD のマネージド ID について詳しくは、「 [*Azure リソースのマネージド ID*](../active-directory/managed-identities-azure-resources/overview.md)」をご覧ください。 

Azure では、システム割り当てとユーザー割り当てという 2 種類のマネージド ID がサポートされています。 現時点では、Azure Digital Twins では **システム割り当て ID** のみがサポートされています。 

Azure Digital インスタンスのシステム割り当てマネージド ID を使用して、[カスタム定義エンドポイント](concepts-route-events.md#create-an-endpoint)に対する認証を行うことができます。 Azure Digital Twins では、[Event Hub](../event-hubs/event-hubs-about.md) と  [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)  の送信先用のエンドポイントと[配信不能イベント](concepts-route-events.md#dead-letter-events)用の [Azure Storage コンテナー](../storage/blobs/storage-blobs-introduction.md)  エンドポイントに対する、システム割り当て ID ベースの認証がサポートされています。 [Event Grid](../event-grid/overview.md)  エンドポイントでは、現時点ではマネージド ID はサポートされていません。

Azure Digital Twins のシステムマネージド ID を有効にし、それを使用してイベントをルーティングする方法については、「[*方法: イベントをルーティングするためにマネージド ID を有効にする (プレビュー)*](./how-to-enable-managed-identities-portal.md)」を参照してください。

## <a name="private-network-access-with-azure-private-link-preview"></a>Azure Private Link を使用したプライベート ネットワーク アクセス (プレビュー)

[Azure Private Link](../private-link/private-link-overview.md) は、[Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md) 内のプライベート エンドポイント経由で、Azure リソース ([Azure Event Hubs](../event-hubs/event-hubs-about.md)、[Azure Storage](../storage/common/storage-introduction.md)、[Azure Cosmos DB](../cosmos-db/introduction.md) など) と、Azure でホストされている顧客とパートナー サービスにアクセスできるようにするサービスです。 

同様に、Azure Digital Twin インスタンス用のプライベート エンドポイントを使用して、仮想ネットワークに配置されたクライアントから Private Link 経由でインスタンスに安全にアクセスできます。 

プライベート エンドポイントでは、Azure VNet アドレス空間の IP アドレスが使用されます。 プライベート ネットワーク上のクライアントと Azure Digital Twins インスタンス間のネットワーク トラフィックは、VNet と Microsoft バックボーン ネットワーク上のプライベート リンクを経由します。これにより、パブリック インターネットに露出されることがなくなります。 このシステムを視覚化すると、次のようになります。

:::image type="content" source="media/concepts-security/private-link.png" alt-text="PowerGrid 社のネットワークを示している図。インターネット/パブリック クラウドへのアクセスがない保護された VNET であり、Private Link 経由で CityOfTwins と呼ばれる Azure Digital Twins インスタンスに接続している。":::

Azure Digital Twins インスタンス用のプライベート エンドポイントを構成することで、Azure Digital Twins インスタンスをセキュリティで保護してパブリックに露出されないようにするだけでなく、VNet からのデータの流出を回避できます。

Azure Digital Twins 用の Private Link を設定する方法については、「[*方法: Private Link を使用してプライベート アクセスを有効にする (プレビュー)*](./how-to-enable-private-link-portal.md)」を参照してください。

### <a name="design-considerations"></a>設計上の考慮事項 

Azure Digital Twins に対して Private Link を使用する場合は、次の点を考慮する必要があります。
* **価格**: 価格の詳細については、「 [Azure Private Link の価格](https://azure.microsoft.com/pricing/details/private-link)」をご覧ください。 
* **リージョン別の提供状況**:Azure Digital Twins に対しては、Azure Digital Twins を利用できるすべての Azure リージョンでこの機能を利用できます。 
* **Azure Digital Twins インスタンスあたりのプライベート エンドポイントの最大数**: 10

Private Link の制限事項については、 [Azure Private Link のドキュメントの制限に関する記事](../private-link/private-link-service-overview.md#limitations)を参照してください。

## <a name="service-tags"></a>サービス タグ

**サービス タグ** は、指定された Azure サービスからの IP アドレス プレフィックスのグループを表します。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。これにより、ネットワーク セキュリティ規則に対する頻繁な更新の複雑さを最小限に抑えられます。 サービス タグの詳細については、" [*仮想ネットワーク タグ*"](../virtual-network/service-tags-overview.md)に関するページを参照してください。 

サービス タグを使用して、 [ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md#security-rules) または  [Azure Firewall](../firewall/service-tags.md) に対してネットワーク アクセス制御を定義するには、セキュリティ規則を作成するときに特定の IP アドレスの代わりにサービス タグを使用します。 規則の適切な " *ソース*"  または " *宛先*"  フィールドにサービス タグ名 (この場合は  **AzureDigitalTwins**) を指定することにより、対応するサービスのトラフィックを許可または拒否することができます。 

**AzureDigitalTwins** サービス タグの詳細を以下に示します。

| タグ | 目的 | 受信または送信で使用できるか | リージョン別か | Azure Firewall と共に使用できるか |
| --- | --- | --- | --- | --- |
| AzureDigitalTwins | Azure Digital Twins<br>注:このタグ、またはこのタグによってカバーされる IP アドレスを使用すれば、[イベント ルート](concepts-route-events.md)用に構成されたエンドポイントへのアクセスを制限することができます。 | 受信 | いいえ | はい |

### <a name="using-service-tags-for-accessing-event-route-endpoints"></a>サービス タグを使用したイベント ルート エンドポイントへのアクセス 

Azure Digital Twins でサービス タグを使用して [イベント ルート](concepts-route-events.md) エンドポイントにアクセスする手順を次に示します。

1. 最初に、Azure の IP 範囲とサービス タグを示す次の JSON ファイル参照をダウンロードします。["*Azure の IP 範囲とサービス タグ*"](https://www.microsoft.com/download/details.aspx?id=56519)。 

2. JSON ファイル内で "AzureDigitalTwins" IP 範囲を検索します。  

3. エンドポイントに接続されている外部リソース (たとえば、[Event Grid](../event-grid/overview.md)、[Event Hub](../event-hubs/event-hubs-about.md)、[Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)、または[配信不能イベント](concepts-route-events.md#dead-letter-events)用の [Azure Storage](../storage/blobs/storage-blobs-overview.md)) のドキュメントを参照して、そのリソースに対して IP フィルターを設定する方法を確認します。

4. "*手順 2*" からの IP 範囲を使用して、外部リソースに対して IP フィルターを設定します。  

5. 必要に応じて、IP 範囲を定期的に更新します。 範囲は時間の経過と共に変化する可能性があるため、これらを定期的にチェックし、必要に応じて更新することをお勧めします。 これらの更新の頻度は異なる場合がありますが、1 週間に 1 回チェックすることをお勧めします。

## <a name="encryption-of-data-at-rest"></a>保存データの暗号化

Azure Digital Twins によって、データ センターに書き込まれる場合の保存データおよび転送中データが暗号化され、アクセス時に復号化されます。 この暗号化は、Microsoft のマネージド暗号化キーを使用して行われます。

## <a name="cross-origin-resource-sharing-cors"></a>クロスオリジン リソース共有 (CORS)

現在、Azure Digital Twins では **クロスオリジン リソース共有 (CORS)** がサポートされていません。 このため、ブラウザー アプリ、[API Management (APIM)](../api-management/api-management-key-concepts.md) インターフェイス、または [Power Apps](/powerapps/powerapps-overview) コネクタから REST API を呼び出すと、ポリシー エラーが表示される場合があります。

このエラーを解決するために、次のいずれかのアクションを実行できます。
* メッセージから CORS ヘッダー `Access-Control-Allow-Origin` を削除します。 このヘッダーは、応答を共有できるかどうかを示します。 
* または、CORS プロキシを作成し、それを通じて Azure Digital Twins REST API 要求を行います。 

## <a name="next-steps"></a>次のステップ

* [*インスタンスと認証の設定方法*](how-to-set-up-instance-portal.md)に関するページで取り上げられている、動作中の概念について確認します。

* クライアント アプリケーション コードからこれらの概念を操作する方法を、[*アプリ認証コードを作成する方法*](how-to-authenticate-client.md)に関するページで確認します。

* [Azure RBAC](../role-based-access-control/overview.md) の詳細を確認します。