---
title: Azure サービスのセキュリティ属性
description: Azure Service Fabric を評価するための一般的なセキュリティ属性のチェックリスト
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: mbaldwin
ms.openlocfilehash: d45e28175412b574432adb59cf700568c9a7fb39
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304251"
---
# <a name="security-attributes-for-azure-services"></a>Azure サービスのセキュリティ属性

この記事では、特定の Azure サービスについて、その一般的なセキュリティ属性をまとめています。 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="api-managementapi-managementapi-management-security-attributesmd"></a>[API Management](../api-management/api-management-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化 (サーバー側暗号化、お客様が管理するキーによるサーバー側暗号化、およびその他の暗号化機能) | 証明書、キー、およびシークレットという名前付きの値などの機密データは、サービスで管理される、サービス インスタンスごとのキーで暗号化されます。 |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など) | はい | [Express Route](../expressroute/index.yml) と VNet 暗号化は、[Azure ネットワーク](../virtual-network/index.yml)によって提供されます。 |
| 暗号化キーの処理 (CMK や BYOK など)| いいえ | すべての暗号化キーはサービス インスタンスごとに存在し、サービスで管理されます。 |
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい | 管理プレーン呼び出しは、TLS 経由で [Azure Resource Manager](../azure-resource-manager/index.yml) によって行われます。 有効な JSON Web トークン (JWT) が必要です。  データ プレーン呼び出しは、TLS と、サポートされる認証メカニズムのいずれか (例: クライアント証明書や JWT など) で保護できます。
 |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| いいえ | |
| VNet インジェクションのサポート| はい | |
| ネットワークの分離とファイアウォールのサポート| はい | それぞれ、ネットワーク セキュリティ グループ (NSG) と Azure Application Gateway (またはその他のソフトウェア アプライアンス) を使用します。 |
| 強制トンネリングのサポート| はい | Azure ネットワークでは、強制トンネリングを提供します。 |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | |

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | |
| Authorization| はい | |


### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | [Azure Monitor のアクティビティ ログ](../azure-monitor/platform/activity-logs-overview.md) |
| データ プレーンのログ記録と監査| はい | [Azure Monitor 診断ログ](../azure-monitor/platform/diagnostic-logs-overview.md)と (必要に応じて) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md)。  |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | [Azure API Management DevOps リソース キット](https://aka.ms/apimdevops)を使用 |

### <a name="vulnerability-scans-false-positives"></a>脆弱性スキャンの偽陽性

このセクションでは一般的な脆弱性について説明します。これらは Azure API Management には影響しません。

| 脆弱性               | 説明                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed は、一部の F5 製品で検出された TLS SessionTicket 拡張機能の実装に含まれる脆弱性です。 初期化されていないメモリから最大 31 バイトのデータがリーク ("流出") する可能性があります。 これは、クライアントから渡される TLS スタックをセッション ID でパディングして、データを 32 ビットの長さにすることが原因で発生します。 |


## <a name="app-serviceapp-serviceapp-service-security-attributesmd"></a>[App Service](../app-service/app-service-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化 (サーバー側暗号化、お客様が管理するキーによるサーバー側暗号化、およびその他の暗号化機能) | はい | Web サイト ファイルのコンテンツは、Azure Storage に格納されます。これにより、コンテンツは保存時に自動的に暗号化されます。 「[保存データに対する Azure Storage 暗号化](../storage/common/storage-service-encryption.md)」を参照してください。<br><br>お客様が提供するシークレットは保存時に暗号化されます。 シークレットは、App Service 構成データベースに格納されている間、保存時に暗号化されます。<br><br>ローカルに接続されたディスクは、必要に応じて、Web サイトによって一時ストレージとして使用できます (D:\local および %TMP%)。 ローカルに接続されたディスクでは、保存時の暗号化は行われません。 |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など) | はい | お客様は、インバウンド トラフィックで HTTPS を必要とし、使用するように Web サイトを構成することができます。 ブログ記事の「[How to make an Azure App Service HTTPS only (Azure App Service を HTTPS のみにする方法)](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/)」を参照してください。 |
| 暗号化キーの処理 (CMK や BYOK など)| はい | お客様はアプリケーション シークレットを Key Vault に格納し、それらを実行時に取得することを選択できます。 「[App Service と Azure Functions の Key Vault 参照を使用する (プレビュー)](../app-service/app-service-key-vault-references.md)」を参照してください。|
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい | App Service を構成するための管理呼び出しは、HTTPS 経由の [Azure Resource Manager](../azure-resource-manager/index.yml) 呼び出しで行われます。 |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい | 現在、App Service のプレビューで利用可能です。 [Azure App Service のアクセス制限](../app-service/app-service-ip-restrictions.md)に関するページを参照してください。 |
| VNet インジェクションのサポート| はい | App Service Environment は、お客様の仮想ネットワークに挿入される 1 人のお客様専用の App Service のプライベート実装です。 「[App Service 環境の概要](../app-service/environment/intro.md)」を参照してください。 |
| ネットワークの分離とファイアウォールのサポート| はい | App Service のパブリック マルチテナント バリエーションでは、お客様は、許可されたインバウンド トラフィックをロック ダウンするようにネットワーク ACL (IP 制限) を構成することができます。  [Azure App Service のアクセス制限](../app-service/app-service-ip-restrictions.md)に関するページを参照してください。  App Service Environment は仮想ネットワークに直接デプロイされるため、NSG で保護することができます。 |
| 強制トンネリングのサポート| はい | App Service Environment は、強制トンネルが構成された、お客様の仮想ネットワークにデプロイすることができます。 お客様は、「[強制トンネリングを使用した App Service Environment の構成](../app-service/environment/forced-tunnel-support.md)」の指示に従う必要があります。 |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | App Service は、Application Insights をサポートする言語用の Application Insights と統合されます (完全な .NET Framework、.NET Core、Java および Node.JS)。  「[Azure App Service のパフォーマンスの監視](../azure-monitor/app/azure-web-apps.md)」を参照してください。 App Service では、アプリケーション メトリックを Azure Monitor に送信することもできます。 「[Azure App Service のアプリの監視](../app-service/web-sites-monitor.md)」を参照してください。 |

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | お客様は、[Azure Active Directory (Azure AD)](../active-directory/index.yml) および他の OAuth と互換性のある ID プロバイダーと自動的に統合される、App Service にアプリケーションをビルドすることができます (「[Azure App Service での認証および許可](../app-service/overview-authentication-authorization.md)」を参照)。 App Service 資産へのアクセス管理では、Azure AD で認証されたプリンシパルと Azure Resource Manager の RBAC ロールの組み合わせによって、すべてのアクセスが制御されます。 |
| Authorization| はい | App Service 資産へのアクセス管理では、Azure AD で認証されたプリンシパルと Azure Resource Manager の RBAC ロールの組み合わせによって、すべてのアクセスが制御されます。  |


### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | App Service オブジェクトで実行されるすべての管理操作は、[Azure Resource Manager](../azure-resource-manager/index.yml) 経由で実行されます。 これらの操作の履歴ログはポータルと CLI の両方を使用して入手できます (「[Azure Resource Manager リソース プロバイダー操作](../role-based-access-control/resource-provider-operations.md#microsoftweb)」と「[az monitor activity-log](/cli/azure/monitor/activity-log)」を参照)。 |
| データ プレーンのログ記録と監査 | いいえ | App Service のデータ プレーンは、ユーザーのデプロイされたWeb サイトのコンテンツを含む、リモート ファイル共有です。  リモート ファイル共有の監査はありません。 |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | 管理操作では、App Service 構成の状態を Azure Resource Manager テンプレートとしてエクスポートし、経時的にバージョン管理できます。 ランタイム操作では、お客様は App Service デプロイ スロット機能を使用して、複数の異なるライブ バージョンのアプリケーションを維持できます。 | 



## <a name="azure-resource-managerazure-resource-managerazure-resource-manager-security-attributesmd"></a>[Azure Resource Manager](../azure-resource-manager/azure-resource-manager-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化 (サーバー側暗号化、お客様が管理するキーによるサーバー側暗号化、およびその他の暗号化機能) | はい |  |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など) | はい | HTTPS/TLS。 |
| 暗号化キーの処理 (CMK や BYOK など)| 該当なし | Azure Resource Manager では、制御データのみが格納され、お客様のコンテンツは格納されません。 |
| 列レベルの暗号化 (Azure Data Services)| はい | |
| API 呼び出しの暗号化| はい | |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| いいえ | |
| VNet インジェクションのサポート| はい | |
| ネットワークの分離とファイアウォールのサポート| いいえ |  |
| 強制トンネリングのサポート| いいえ |  |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| いいえ | |

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | [Azure Active Directory](/azure/active-directory) ベース。|
| Authorization| はい | |


### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | アクティビティ ログでは、リソースに対して実行された書き込み操作 (PUT、POST、DELETE) がすべて公開されます。「[リソースのアクションを監査するアクティビティ ログの表示](../azure-resource-manager/resource-group-audit.md)」を参照してください。 |
| データ プレーンのログ記録と監査| 該当なし | |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい |  |


## <a name="azure-backupbackupbackup-security-attributesmd"></a>[Azure Backup](../backup/backup-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化 (サーバー側暗号化、お客様が管理するキーによるサーバー側暗号化、およびその他の暗号化機能)| はい | ストレージ アカウントに対して Storage Service Encryption を使用します。 |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など) | いいえ | HTTPS を使用します。 |
| 暗号化キーの処理 (CMK や BYOK など)| いいえ |  |
| 列レベルの暗号化 (Azure Data Services)| いいえ |  |
| API 呼び出しの暗号化| はい |  |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| いいえ |  |
| VNet インジェクションのサポート| いいえ |  |
| ネットワークの分離とファイアウォールのサポート| はい | VM バックアップでは、強制トンネリングがサポートされます。 VM 内で実行されるワークロードでは、強制トンネリングはサポートされません。 |
| 強制トンネリングのサポート| いいえ |  |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | Log Analytics は、診断ログを通じてサポートされます。 詳細については、[Log Analytics を使用した Azure Backup で保護されているワークロードの監視](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/)に関するページを参照してください。 |

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | 認証は Azure Active Directory を介して行われます。 |
| Authorization| はい | カスタマーが作成した RBAC ロールとビルトインの RBAC ロールが使用されます。 詳細については、[ロールベースのアクセス制御を使用した Azure Backup の回復ポイントの管理](/azure/backup/backup-rbac-rs-vault)に関するページを参照してください。 |


### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | Azure Portal からカスタマーがトリガーしたすべてのアクションがアクティビティ ログに記録されます。 |
| データ プレーンのログ記録と監査| いいえ | Azure Backup データ プレーンには、直接アクセスできません。  |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい|  |

## <a name="cosmos-dbcosmos-dbcosmos-db-security-attributesmd"></a>[Cosmos DB](../cosmos-db/cosmos-db-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化 (サーバー側暗号化、お客様が管理するキーによるサーバー側暗号化、およびその他の暗号化機能) | はい | すべての Cosmos DB データベースとバックアップは、既定で暗号化されます。「[Azure Cosmos DB でのデータの暗号化](../cosmos-db/database-encryption-at-rest.md)」を参照してください。 カスタマー マネージド キーによるサーバー側での暗号化はサポートされていません。 |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | Azure Cosmos DB のすべてのデータは、転送中に暗号化されます。 |
| 暗号化キーの処理 (CMK や BYOK など)| いいえ |  |
| 列レベルの暗号化 (Azure Data Services)| はい | Tables API Premium でのみ。 この機能は、すべての API でサポートされるわけではありません。 「[Azure Cosmos DB の概要:Table API](../cosmos-db/table-introduction.md)」を参照してください。 |
| API 呼び出しの暗号化| はい | Azure Cosmos DB へのすべての接続で HTTPS がサポートされます。 Azure Cosmos DB では、TLS 1.2 接続もサポートされますが、これはまだ実施されていません。 お客様が自分の側で下位レベルの TLS をオフにすると、Cosmos DB に確実に接続できます。  |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい |  |
| VNet インジェクションのサポート| はい | VNet サービス エンドポイントを使用して、仮想ネットワーク (VNet) の特定のサブネットからのアクセスのみを許可するように Azure Cosmos DB アカウントを構成できます。 VNet へのアクセスとファイアウォール規則を組み合わせることもできます。  「[仮想ネットワークから Azure Cosmos DB にアクセスする](../cosmos-db/vnet-service-endpoint.md)」を参照してください。 |
| ネットワークの分離とファイアウォールのサポート| はい | ファイアウォールのサポートを使用して、承認された IP アドレスのセット、IP アドレスの範囲、およびクラウド サービスからのアクセスのみを許可するように Azure Cosmos アカウントを構成できます。 「[Azure Cosmos DB で IP ファイアウォールを構成する](../cosmos-db/how-to-configure-firewall.md)」を参照してください。|
| 強制トンネリングのサポート | はい | 仮想マシンが配置されている VNET のクライアント側で構成できます。   |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | Azure Cosmos DB に送信されたすべての要求が記録されます。 [Azure 監視](../azure-monitor/overview.md)、Azure メトリック、Azure 監査ログがサポートされています。  データ プレーン要求、クエリのランタイム統計、クエリ テキスト、MongoDB 要求に対応する情報を記録することができます。 アラートを設定することもできます。 |

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | データベース アカウント レベルでは「はい」です。データ プレーン レベルでは、Cosmos DB では、リソース トークンとキー アクセスが使用されます。 |
| Authorization| はい | マスター キー (プライマリおよびセカンダリ) とリソース トークンを持つ Azure Cosmos アカウントでサポートされます。 マスター キーを使用して、データに対して、読み取り/書き込みアクセスまたは読み取り専用アクセスを実行できます。 リソース トークンを使用して、ドキュメントやコンテナーなどのリソースに、限られた時間だけアクセスできます。 |

### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロール/管理プレーンのログ記録と監査| はい | ファイアウォール、VNet、キー アクセス、IAM などのアカウント レベルの操作の Azure アクティビティ ログ。 |
| データ プレーンのログ記録と監査 | はい | コンテナーの作成、スループットのプロビジョニング、インデックス作成ポリシー、ドキュメントの CRUD 操作などのコンテナー レベルの診断監視のログ記録。 |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| いいえ  | | 

### <a name="additional-security-attributes-for-cosmos-db"></a>Cosmos DB の追加のセキュリティ属性

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| クロスオリジン リソース共有 (CORS) | はい | 「[クロスオリジン リソース共有 (CORS) の構成](../cosmos-db/how-to-configure-cross-origin-resource-sharing.md)」を参照してください。 |


## <a name="event-hubsevent-hubsevent-hubs-security-attributesmd"></a>[Event Hubs](../event-hubs/event-hubs-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化 (サーバー側暗号化、お客様が管理するキーによるサーバー側暗号化、およびその他の暗号化機能) |  はい | |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など) | はい | |
| 暗号化キーの処理 (CMK や BYOK など)| いいえ |  |
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい |  |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい |  |
| VNet インジェクションのサポート| いいえ | |
| ネットワークの分離とファイアウォールのサポート| はい |  |
| 強制トンネリングのサポート| いいえ |  |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | |

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | |
| Authorization|  はい | |


### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい |  |
| データ プレーンのログ記録と監査| はい |   |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | |


## <a name="expressrouteexpressrouteexpressroute-security-attributesmd"></a>[ExpressRoute](../expressroute/expressroute-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化 (サーバー側暗号化、お客様が管理するキーによるサーバー側暗号化、およびその他の暗号化機能) |  該当なし | ExpressRoute は顧客データを格納しません。 |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など) | いいえ | |
| 暗号化キーの処理 (CMK や BYOK など)| 該当なし |  |
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい | [Azure Resource Manager](../azure-resource-manager/index.yml) と HTTPS を使用します。 |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| 該当なし |  |
| VNet インジェクションのサポート| 該当なし | |
| ネットワークの分離とファイアウォールのサポート| はい | 各顧客は独自のルーティング ドメインに含まれ、独自の VNet にトンネリングされます |
| 強制トンネリングのサポート| 該当なし | Border Gateway Protocol (BGP) を使用。 |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | 「[ExpressRoute の監視、メトリック、およびアラート](../expressroute/expressroute-monitoring-metrics-alerts.md)」を参照してください。|

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | Gateway for Microsoft (GWM) (コントローラー) 用のサービス アカウント。開発と運用のための Just-In-Time (JIT) アクセス。 |
| Authorization|  はい |Gateway for Microsoft (GWM) (コントローラー) 用のサービス アカウント。開発と運用のための Just-In-Time (JIT) アクセス。 |


### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ| 
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい |  |
| データ プレーンのログ記録と監査| いいえ |   |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | ネットワーク リソース プロバイダー (NRP) を使用。 |


## <a name="key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Key Vault](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化 (サーバー側暗号化、お客様が管理するキーによるサーバー側暗号化、およびその他の暗号化機能) | はい | すべてのオブジェクトが暗号化されます。 |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など) | はい | すべての通信が暗号化された API 呼び出しを介して行われます。 |
| 暗号化キーの処理 (CMK や BYOK など)| はい | ユーザーが Key Vault 内のすべてのキーを制御します。 ハードウェア セキュリティ モジュール (HSM) バックアップ キーが指定されている場合、FIPS レベル 2 HSM により、キー、証明書、またはシークレットが保護されます。 |
| 列レベルの暗号化 (Azure Data Services)| 該当なし |  |
| API 呼び出しの暗号化| はい | HTTPS を使用します。 |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい | Virtual Network (VNet) サービス エンドポイントを使用。 |
| VNet インジェクションのサポート| いいえ |  |
| ネットワークの分離とファイアウォールのサポート| はい | VNet ファイアウォール規則を使用。 |
| 強制トンネリングのサポート| いいえ |  |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | Log Analytics を使用します。 |

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | 認証は Azure Active Directory を介して行われます。 |
| Authorization| はい | Key Vault アクセス ポリシーを使用します。 |

### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロール/管理プレーンのログ記録と監査| はい | Log Analytics を使用します。 |
| データ プレーンのログ記録と監査| はい | Log Analytics を使用します。 |

### <a name="access-controls"></a>アクセス制御

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 制御/管理プレーンのアクセス制御 | はい | Azure Resource Manager のロールベースのアクセス制御 (RBAC) |
| データ プレーンのアクセス制御 (すべてのサービス レベル) | はい | Key Vault アクセス ポリシー |

## <a name="load-balancerload-balancerload-balancer-security-attributesmd"></a>[Load Balancer](../load-balancer/load-balancer-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化 (サーバー側暗号化、お客様が管理するキーによるサーバー側暗号化、およびその他の暗号化機能) | 該当なし | |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| 該当なし | |
| 暗号化キーの処理 (CMK や BYOK など)| 該当なし | |
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい | [Azure Resource Manager](../azure-resource-manager/index.yml) を使用。 |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| 該当なし | |
| VNet インジェクションのサポート| 該当なし | 。 |
| ネットワークの分離とファイアウォールのサポート| 該当なし |  |
| 強制トンネリングのサポート| 該当なし | |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | 「[パブリック Basic ロード バランサーの Azure Monitor ログ](../load-balancer/load-balancer-monitor-log.md)」を参照してください。 |

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| 該当なし |  |
| Authorization| 該当なし |  |

### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | 「[パブリック Basic ロード バランサーの Azure Monitor ログ](../load-balancer/load-balancer-monitor-log.md)」を参照してください。 |
| データ プレーンのログ記録と監査 | 該当なし |  |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| 該当なし |  | 

## <a name="service-bus-messagingservice-bus-messagingservice-bus-messaging-security-attributesmd"></a>[Service Bus メッセージング](../service-bus-messaging/service-bus-messaging-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化 (サーバー側暗号化、お客様が管理するキーによるサーバー側暗号化、およびその他の暗号化機能) |  はい (サーバー側の保存時の暗号化では既定で)。 | 顧客管理キーと BYOK はまだサポートされていません。 クライアント側の暗号化はクライアントの責任です |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など) | はい | 標準の HTTPS/TLS メカニズムがサポートされます。 |
| 暗号化キーの処理 (CMK や BYOK など)| いいえ |   |
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい | API 呼び出しは、[Azure Resource Manager](../azure-resource-manager/index.yml) と HTTPS を使用して行われます。 |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい (Premium レベルのみ) | VNet サービス エンドポイントは、[Service Bus Premium レベル](../service-bus-messaging/service-bus-premium-messaging.md)でのみサポートされます。 |
| VNet インジェクションのサポート| いいえ | |
| ネットワークの分離とファイアウォールのサポート| はい (Premium レベルのみ) |  |
| 強制トンネリングのサポート| いいえ |  |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | [Azure Monitor とアラート](../service-bus-messaging/service-bus-metrics-azure-monitor.md)によってサポートされます。 |

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | [Azure Active Directory マネージド サービス ID](../service-bus-messaging/service-bus-managed-service-identity.md)を使用して管理されます (「[Service Bus の認証と承認](../service-bus-messaging/service-bus-authentication-and-authorization.md)」を参照)。|
| Authorization| はい | [RBAC](../service-bus-messaging/service-bus-role-based-access-control.md) (プレビュー) と SAS トークンによる承認がサポートされます (「[Service Bus の認証と承認](../service-bus-messaging/service-bus-authentication-and-authorization.md)」を参照)。 |


### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | 操作ログを使用できます (「[Service Bus 診断ログ](../service-bus-messaging/service-bus-diagnostic-logs.md)」を参照)。  |
| データ プレーンのログ記録と監査| いいえ |  |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | [Azure Resource Manager API](/rest/api/resources/) によるリソース プロバイダーのバージョン管理がサポートされます。|


## <a name="service-bus-relayservice-bus-relayservice-bus-relay-security-attributesmd"></a>[Service Bus Relay](../service-bus-relay/service-bus-relay-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化 (サーバー側暗号化、お客様が管理するキーによるサーバー側暗号化、およびその他の暗号化機能) |  該当なし | Relay は Web ソケットであり、データは保持されません。 |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など) | はい | Service には TLS が必要です。 |
| 暗号化キーの処理 (CMK や BYOK など)| いいえ | Microsoft の TLS 証明書のみを使用します。  |
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい | HTTPS。 |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| いいえ |  |
| ネットワークの分離とファイアウォールのサポート| いいえ |  |
| 強制トンネリングのサポート| 該当なし | Relay は TLS トンネルです  |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | |

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | SAS を使用。 |
| Authorization|  はい | SAS を使用。 |

### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | [Azure Resource Manager](../azure-resource-manager/index.yml) を使用。 |
| データ プレーンのログ記録と監査| はい | 接続の成功/失敗およびエラーはログに記録されます。  |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | [Azure Resource Manager](../azure-resource-manager/index.yml) を使用。|

## <a name="service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化 (サーバー側暗号化、お客様が管理するキーによるサーバー側暗号化、およびその他の暗号化機能) | はい | ユーザーは、クラスターと、クラスター構築の土台となる仮想マシン スケール セットを所有します。 Azure Disk Encryption は仮想マシン スケール セットで有効にできます。 |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など) | はい | ユーザーは、クラスターと、クラスター構築の土台となる仮想マシン スケール セットを所有します。 Azure Disk Encryption は仮想マシン スケール セットで有効にできます。 |
| 列レベルの暗号化 (Azure Data Services)| 該当なし |  |
| API 呼び出しの暗号化| はい | Service Fabric API は Azure Resource Manager によって呼び出されます。 有効な JSON Web トークン (JWT) が必要です。 |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい |  |
| VNet インジェクションのサポート| はい |  |
| ネットワークの分離とファイアウォールのサポート| はい | ネットワーク セキュリティ グループ (NSG) の使用。 |
| 強制トンネリングのサポート| はい | Azure ネットワークでは、強制トンネリングを提供します。 |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | Azure 監視サポートとサード パーティ サポートの使用。 |

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | 認証は Azure Active Directory を介して行われます。 |
| Authorization| はい | SFRP 経由の呼び出しのための ID およびアクセス管理 (IAM)。 クラスター エンド ポイントは、ユーザーと管理者という 2 つのロールで直接呼び出すことができます。ユーザーは API をいずれかのロールにマッピングできます。 |

### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | コントロール プレーン操作はすべて、監査と承認のプロセスを経由して実行されます。 |
| データ プレーンのログ記録と監査| 該当なし | ユーザーはクラスターを所有します。  |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | |

## <a name="sql-databasesql-databasesql-database-security-attributesmd"></a>[SQL Database](../sql-database/sql-database-security-attributes.md)

SQL Database には、[単一データベース](../sql-database/sql-database-single-index.yml)と[マネージド インスタンス](../sql-database/sql-database-managed-instance.md)の両方が含まれます。 次のエントリは、明記されている場合を除き、両方のオファリングに適用されます。

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化 (サーバー側暗号化、お客様が管理するキーによるサーバー側暗号化、およびその他の暗号化機能) | はい | [Always Encrypted](../sql-database/sql-database-always-encrypted.md) に関する記事で説明されているように、"使用時の暗号化" と呼ばれます。 サーバー側暗号化では、[Transparent Data Encryption](../sql-database/transparent-data-encryption-azure-sql.md) が使われます。|
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など) | はい | HTTPS を使用します。 |
| 暗号化キーの処理 (CMK や BYOK など)| はい | サービス マネージド キーとカスタマー マネージド キーの処理がどちらも提供されます。 後者は [Azure Key Vault](../key-vault/index.yml) を通じて提供されます。 |
| Azure のデータ サービスによって提供される列レベルの暗号化| はい | [Always Encrypted](../sql-database/sql-database-always-encrypted.md) により。 |
| 暗号化された API 呼び出し| はい | HTTPS/SSL を使用。 |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい | [単一データベース](../sql-database/sql-database-single-index.yml)のみに適用。 |
| Azure Virtual Network の挿入のサポート| はい | [マネージド インスタンス](../sql-database/sql-database-managed-instance.md)のみに適用。 |
| ネットワークの分離とファイアウォールのサポート| はい | データベースとサーバーの両方のレベルでのファイアウォール。 ネットワークの分離は、[マネージド インスタンス](../sql-database/sql-database-managed-instance.md)のみです。 |
| 強制トンネリングのサポート| はい | [ExpressRoute](../expressroute/index.yml) VPN による[マネージド インスタンス](../sql-database/sql-database-managed-instance.md)。 |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure Monitoring のサポート (Log Analytics や Application Insights など)| はい | [SQL 監査](../sql-database/sql-database-auditing.md)を使用した [Azure Event Hubs](../event-hubs/index.yml) の統合を介して、Imperva の SecureSphere による SIEM ソリューションもサポートされます。 |

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | Azure Active Directory (Azure AD) |
| Authorization| はい | なし |

### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールプレーンと管理プレーンのログ記録および監査| はい | 一部のイベントについてのみ、はい |
| データ プレーンのログ記録と監査 | はい | [SQL 監査](../sql-database/sql-database-auditing.md)による |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| いいえ  | なし |

### <a name="additional-security-attributes-for-sql-database"></a>SQL Database の追加セキュリティ属性

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 予防: 脆弱性評価 | はい | 「[SQL の脆弱性評価サービスは、データベースの脆弱性を特定するのに役に立ちます](../sql-database/sql-vulnerability-assessment.md)」をご覧ください。 |
| 予防: データの検出と分類  | はい | 「[Azure SQL Database および SQL Data Warehouse のデータの検出と分類](../sql-database/sql-database-data-discovery-and-classification.md)」をご覧ください。 |
| 検出: 脅威の検出 | はい | 「[Azure SQL Database の Advanced Threat Protection](../sql-database/sql-database-threat-detection-overview.md)」をご覧ください。 |


## <a name="storagestoragecommonstorage-security-attributesmd"></a>[Storage](../storage/common/storage-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化 (サーバー側暗号化、お客様が管理するキーによるサーバー側暗号化、およびその他の暗号化機能) | はい |  |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など) | はい | 標準の HTTPS/TLS 機構をサポートします。  サービスに転送するデータは、ユーザーが事前に暗号化することもできます。 |
| 暗号化キーの処理 (CMK や BYOK など)| はい | 「[Azure Key Vault で顧客が管理するキーを Storage Service Encryption に使用する](../storage/common/storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」を参照してください。|
| 列レベルの暗号化 (Azure Data Services)| 該当なし |  |
| API 呼び出しの暗号化| はい |  |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい |  |
| VNet インジェクションのサポート| 該当なし |  |
| ネットワークの分離とファイアウォールのサポート| はい | |
| 強制トンネリングのサポート| 該当なし |  |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | Azure Monitor のメトリックが利用できるようになり、ログのプレビューが開始されました |

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | Azure Active Directory、共有キー、共有アクセス トークン。 |
| Authorization| はい | RBAC、POSIX ACL、SAS トークンを使用した承認をサポートします |

### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査 | はい | Azure Resource Manager アクティビティ ログ |
| データ プレーンのログ記録と監査| はい | サービスの診断ログおよび Azure Monitor のログのプレビューが開始されました  |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | Azure Resource Manager API によりリソース プロバイダーのバージョン管理をサポートします |

## <a name="virtual-machines-and-virtual-machine-scale-sets"></a>Virtual Machines と Virtual Machine Scale Sets

[Linux VM](../virtual-machines/windows/virtual-machines-windows-security-attributes.md) | [Windows VM](../virtual-machines/windows/virtual-machines-windows-security-attributes.md) | [仮想マシン スケール セット](../virtual-machine-scale-sets/virtual-machine-scale-sets-security-attributes.md)


### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化 (サーバー側暗号化、お客様が管理するキーによるサーバー側暗号化、およびその他の暗号化機能) | はい | 「[Azure で Linux 仮想マシンを暗号化する方法](/azure/virtual-machines/linux/encrypt-disks)」および「[Windows VM の仮想ディスクを暗号化する](/azure/virtual-machines/windows/encrypt-disks)」を参照してください。 |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | Azure Virtual Machines では、[ExpressRoute](/azure/expressroute) と VNET の暗号化がサポートされています。 「[VM での転送中の暗号化](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)」を参照してください。 |
| 暗号化キーの処理 (CMK や BYOK など)| はい | カスタマー マネージド キーは、サポートされている Azure の暗号化シナリオです。「[Azure の暗号化の概要](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)」を参照してください。|
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい | HTTPS および SSL 経由。 |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい | |
| VNet インジェクションのサポート| はい | 。 |
| ネットワークの分離とファイアウォールのサポート| はい |  |
| 強制トンネリングのサポート| はい | 「[Azure Resource Manager デプロイ モデルを使用した強制トンネリングの構成](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm)」を参照してください。 |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | 「[Azure で Linux 仮想マシンの監視と更新を行う](/azure/virtual-machines/linux/tutorial-monitoring)」および「[Azure で Windows 仮想マシンの監視と更新を行う](/azure/virtual-machines/windows/tutorial-monitoring)」を参照してください。 |

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい |  |
| Authorization| はい |  |


### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい |  |
| データ プレーンのログ記録と監査 | いいえ |  |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい |  | 


## <a name="vpn-gatewayvpn-gatewayvpn-gateway-security-attributesmd"></a>[VPN Gateway](../vpn-gateway/vpn-gateway-security-attributes.md)

### <a name="preventative"></a>予防

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| 保存時の暗号化 (サーバー側暗号化、お客様が管理するキーによるサーバー側暗号化、およびその他の暗号化機能) | 該当なし | VPN ゲートウェイは顧客データを格納するのではなく、顧客データを転送します。 |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | VPN ゲートウェイは、Azure VPN ゲートウェイと顧客のオンプレミスの VPN デバイス (S2S) または VPN クライアント (P2S) の間の顧客パケットを暗号化します。 VPN ゲートウェイはまた、VNet 間の暗号化もサポートします。 |
| 暗号化キーの処理 (CMK や BYOK など)| いいえ | 顧客が指定した仮共有キーは保存時に暗号化されますが、まだ CMK には統合されていません。 |
| 列レベルの暗号化 (Azure Data Services)| 該当なし | |
| API 呼び出しの暗号化| はい | [Azure Resource Manager](../azure-resource-manager/index.yml) と HTTPS を使用します。  |

### <a name="network-segmentation"></a>ネットワークのセグメント化

| セキュリティ属性 | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| 該当なし | |
| VNet インジェクションのサポート| 該当なし | 。 |
| ネットワークの分離とファイアウォールのサポート| はい | VPN ゲートウェイは、顧客の仮想ネットワークごとの専用の VM インスタンスです。  |
| 強制トンネリングのサポート| はい |  |

### <a name="detection"></a>検出

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | [Azure Monitor の診断ログ/アラート](../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure Monitor のメトリック/アラート](../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)に関するページを参照してください。  |

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| Authentication| はい | サービスを管理し、Azure VPN ゲートウェイを構成するための [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)。 |
| Authorization| はい | [RBAC](../role-based-access-control/overview.md) 経由で承認をサポートします。 |

### <a name="audit-trail"></a>監査証跡

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| コントロールと管理プレーンのログ記録と監査| はい | Azure Resource Manager アクティビティ ログ。 |
| データ プレーンのログ記録と監査 | はい | VPN 接続のログ記録と監査のための [Azure Monitor の診断ログ](../azure-resource-manager/resource-group-audit.md)。 |

### <a name="configuration-management"></a>構成管理

| セキュリティ属性 | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| はい | 管理操作では、Azure VPN ゲートウェイ構成の状態を Azure Resource Manager テンプレートとしてエクスポートしたり、一定期間にわたってバージョン管理したりできます。 | 

