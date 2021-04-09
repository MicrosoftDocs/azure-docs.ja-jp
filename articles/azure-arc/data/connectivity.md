---
title: 接続モードと要件
description: ご利用の環境から Azure への Azure Arc 対応データ サービス接続オプションについて説明します。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: d148509af45b93dce8dbd99b9afc674276b149b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493974"
---
# <a name="connectivity-modes-and-requirements"></a>接続モードと要件

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connectivity-modes"></a>接続モード

Azure Arc 対応データ サービス環境から Azure への接続の程度には、複数のオプションがあります。 ビジネス ポリシー、政府の規制、または Azure へのネットワーク接続の可用性によって要件が異なりますが、次の接続モードから選択できます。

Azure Arc 対応データ サービスでは、2 つの異なる *接続モード* で Azure に接続するためのオプションが提供されます。 

- 直接接続 
- 間接接続

この接続モードにより、Azure に送信されるデータの量や、ユーザーが Arc Data Controller とやり取りする方式を柔軟に選択できます。 選択した接続モードによっては、Azure Arc 対応データ サービスの一部の機能が利用できる場合と利用できない場合があります。

重要な点として、Azure Arc 対応データ サービスが Azure に直接接続される場合、ユーザーは [Azure Resource Manager API](/rest/api/resources/)、Azure CLI、および Azure portal を使用して Azure Arc データ サービスを操作できます。 直接接続モードのエクスペリエンスは、プロビジョニング/プロビジョニング解除、スケーリング、構成などをすべて Azure portal で行う形で他の Azure サービスを使用する方法とほぼ同じです。  Azure Arc 対応データ サービスが Azure に間接的に接続される場合、Azure portal は読み取り専用ビューです。 デプロイした SQL マネージド インスタンスや Postgres Hyperscale インスタンスのインベントリとそれらの詳細を表示できますが、Azure portal でそれらに対してアクションを実行することはできません。  間接接続モードでは、すべてのアクションは、Azure Data Studio、[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]、または kubectl などの Kubernetes ネイティブ ツールを使用してローカルで実行する必要があります。

また、Azure Active Directory と Azure ロールベースのアクセス制御は、Azure への継続的な直接接続に依存して提供される機能であるため、直接接続モードでしか使用できません。

Azure Defender セキュリティ サービス、Container Insights、BLOB ストレージへの Azure Backup など、一部の Azure 接続型サービスは、これらに直接到達できる場合にのみ利用可能です。

||**間接接続**|**直接接続**|**非接続**|
|---|---|---|---|
|**説明**|間接接続モードでは、ほとんどの管理サービスは環境内でローカルに提供され、Azure に直接接続することはありません。  インベントリと請求の目的に _限って_、最小限のデータを Azure に送信する必要があります。 ファイルにエクスポートされて、少なくとも月に 1 回 Azure にアップロードされます。  Azure への直接または継続的な接続は必要ありません。  Azure への接続が必要な一部の機能とサービスは利用できなくなります。|直接接続モードでは、Azure との直接接続が確立できるときに利用可能なすべてのサービスが提供されます。 接続は常に、ご利用の環境 _から_ Azure への方向で開始され、HTTPS/443 などの標準のポートとプロトコルを使用します。|どのような方法でも、Azure との間でデータを送受信することはできません。|
|**現在の可用性**| プレビューで利用可能。|プレビューで利用可能。|現在サポートされていません。|
|**一般的なユース ケース**|ビジネスまたは法令順守のポリシーにより、または外部からの攻撃やデータ流出の懸念により、データ センターのデータ領域との間で接続が許可されないオンプレミスのデータセンター。  一般的な例:金融機関、医療、政府。 <br/><br/>エッジ サイトから通常はインターネットに接続できないエッジ サイトの場所。  一般的な例: 石油/ガスまたは軍事分野のアプリケーション。  <br/><br/>接続が断続的で、長期間の停止があるエッジ サイトの場所。  一般的な例: スタジアム、クルーズ船。 | パブリック クラウドを使用している組織。  一般的な例:Azure、AWS、または Google Cloud。<br/><br/>通常はインターネット接続が存在し、許可されるエッジ サイトの場所。  一般的な例: 小売店、製造業。<br/><br/>データ センターのデータ領域とインターネットの間の接続に関して、比較的制限が緩いポリシーを持つ企業のデータ センター。  一般的な例:規制のない業種、中小企業|どのような状況でも一切のデータがデータ環境を出入りできない、真の "エアギャップ" 環境。 一般的な例: 最高機密の政府機関。|
|**Azure にデータが送信されるしくみ**|請求およびインベントリ データを Azure に送信する方法には、次の 3 つのオプションがあります。<br><br> 1) セキュリティで保護されたデータ領域と Azure の両方に接続できる自動プロセスによって、データ領域からデータがエクスポートされます。<br><br>2) データ領域内の自動プロセスによってデータ領域からデータがエクスポートされ、セキュリティが低い方の領域に自動的にコピーされ、セキュリティが低い方の領域内の自動プロセスによってデータが Azure にアップロードされます。<br><br>3) セキュリティで保護された領域内のユーザーによってデータが手動でエクスポートされ、セキュリティで保護された領域から手動で持ち出されて、Azure に手動でアップロードされます。 <br><br>最初の 2 つのオプションは、頻繁な実行をスケジュールできる自動化された継続的プロセスであるため、Azure への接続が利用可能であれば、最小限の遅延で Azure にデータが送信されます。|データは自動的かつ継続的に Azure に送信されます。|データが Azure に送信されることはありません。|

## <a name="feature-availability-by-connectivity-mode"></a>接続モード別の機能の可用性

|**機能**|**間接接続**|**直接接続**|
|---|---|---|
|**自動高可用性**|サポートされています|サポートされています|
|**セルフサービス プロビジョニング**|サポートされています<br/>作成を行うには、Azure Data Studio、[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]、Kubernetes ネイティブ ツール (helm、kubectl、oc など)、または Azure Arc 対応の Kubernetes GitOps プロビジョニングを使用します。|サポートされています<br/>間接接続モードの作成オプションに加えて、Azure portal、Azure Resource Manager API、Azure CLI、または ARM テンプレートを使用して作成することもできます。 **直接接続モードの保留中の可用性**
|**柔軟なスケーラビリティ**|サポートされています|サポートされています<br/>**直接接続モードの保留中の可用性**|
|**Billing**|サポートされています<br/>請求データは定期的にエクスポートされ、Azure に送信されます。|サポートされています<br/>請求データは自動的かつ継続的に Azure に送信され、ほぼリアルタイムで反映されます。 **直接接続モードの保留中の可用性**|
|**在庫管理**|サポートされています<br/>インベントリ データは定期的にエクスポートされ、Azure に送信されます。<br/><br/>Azure Data Studio、Azure Data CLI、 `kubectl` などのクライアント ツールを使用して、インベントリをローカルで表示および管理します。|サポートされています<br/>インベントリ データは自動的かつ継続的に Azure に送信され、ほぼリアルタイムで反映されます。 そのため、Azure portal から直接インベントリを管理できます。 **直接接続モードの保留中の可用性**|
|**自動アップグレードとパッチ適用**|サポートされています<br/>データ コントローラーから Microsoft Container Registry (MCR) に直接アクセスできる必要があります。または、コンテナー イメージを MCR からプルして、データ コントローラーからアクセスできるローカルのプライベート コンテナー レジストリにプッシュする必要があります。|サポートされています<br/>**直接接続モードの保留中の可用性**|
|**自動バックアップと復元**|サポートされています<br/>自動ローカル バックアップと復元。|サポートされています<br/>自動化されたローカルバックアップと復元に加えて、_必要に応じて_、長期間のオフサイト保持のためにバックアップを Azure Backup に送信することができます。 **直接接続モードの保留中の可用性**|
|**監視**|サポートされています<br/>Grafana と Kibana のダッシュボードを使用したローカル監視。|サポートされています<br/>ローカルの監視ダッシュボードに加えて、_必要に応じて_、複数のサイトを 1 か所でまとめて監視するために、監視データとログを Azure Monitor に送信することができます。 **直接接続モードの保留中の可用性**|
|**認証**|データ コントローラーとダッシュボードの認証には、ローカルのユーザー名/パスワードを使用します。 データベース インスタンスへの接続には、SQL および Postgres ログインまたは Active Directory を使用します。  Kubernetes API に対する認証には、K8s 認証プロバイダーを使用します。|間接接続モードの認証方法に加えて、またはその代わりに、_必要に応じて_ Azure Active Directory を使用できます。 **直接接続モードの保留中の可用性**|
|**ロールベースのアクセス制御 (RBAC)**|Kubernetes API では Kubernetes RBAC を使用します。 データベース インスタンスには SQL および Postgres RBAC を使用します。|必要に応じて、Azure Active Directory および Azure RBAC と統合できます。 **直接接続モードの保留中の可用性**|
|**Azure Defender**|サポートされていません|将来的に予定されています|

## <a name="connectivity-requirements"></a>接続の要件

**一部の機能では Azure への接続が必要です。**

**Azure とのすべての通信は常に、ご利用の環境から開始されます。** これは、Azure portal でユーザーが開始する操作にも当てはまります。  その場合、Azure のキューに入れられるタスクが実質的に存在します。  環境内のエージェントによって、キュー内のタスクを確認するために Azure との通信が開始され、タスクが実行されて、ステータス/完了/失敗が Azure に報告されます。

|**データの種類**|**方向**|**必須/省略可能**|**追加コスト**|**必要なモード**|**ノート**|
|---|---|---|---|---|---|
|**コンテナー イメージ**|Microsoft Container Registry -> お客様|必須|いいえ|間接または直接|コンテナー イメージは、ソフトウェアを配布するための方法です。  インターネット経由で Microsoft Container Registry (MCR) に接続できる環境では、コンテナー イメージを MCR から直接プルできます。  デプロイ環境から直接接続できない場合、MCR からイメージをプルして、デプロイ環境内のプライベート コンテナー レジストリにプッシュすることができます。  作成時に、MCR の代わりにプライベート コンテナー レジストリからプルするように作成プロセスを構成できます。 これは自動更新にも適用されます。|
|**リソース インベントリ**|お客様の環境 -> Azure|必須|いいえ|間接または直接|データ コントローラー、データベース インスタンス (PostgreSQL および SQL) のインベントリは、請求目的で、またすべてのデータ コントローラーとデータベース インスタンスのインベントリを 1 か所に作成する目的で Azure に保持されます。後者は、Azure Arc データ サービスを使用する複数の環境がある場合に特に役立ちます。  インスタンスがプロビジョニング、プロビジョニング解除、スケールアウト/イン、スケールアップ/ダウンされると、Azure でインベントリが更新されます。|
|**請求テレメトリ データ**|お客様の環境 -> Azure|必須|いいえ|間接または直接|請求のために、データベース インスタンスの使用率を Azure に送信する必要があります。  Azure Arc 対応データ サービスのコストは、プレビュー期間中は発生しません。|
|**監視データとログ**|お客様の環境 -> Azure|Optional|データ量によって異なる場合があります (「[Azure Monitor の価格](https://azure.microsoft.com/en-us/pricing/details/monitor/)」を参照)|間接または直接|複数の環境にわたるデータを 1 か所に集約するために、ローカルで収集された監視データとログを Azure Monitor に送信することができます。また、Azure Machine Learning のデータを使用して、アラートなどの Azure Monitor サービスを使用することもできます。|
|**Azure ロールベースのアクセス制御 (Azure RBAC)**|お客様の環境 -> Azure -> お客様の環境|省略可能|×|直接のみ|Azure RBAC を使用する場合は、常に Azure との接続が確立されている必要があります。  Azure RBAC を使用しない場合は、ローカルの Kubernetes RBAC を使用できます。  **直接接続モードの保留中の可用性**|
|**Azure Active Directory (AD)**|お客様の環境 -> Azure -> お客様の環境|Optional|状況によりますが、Azure AD に対する支払いが既に発生している場合があります|直接のみ|認証に Azure AD を使用する場合は、常に Azure との接続が確立されている必要があります。 認証に Azure AD を使用しない場合は、Active Directory を介して Active Directory フェデレーション サービス (AD FS) を使用できます。 **直接接続モードの保留中の可用性**|
|**バックアップと復元**|お客様の環境 -> お客様の環境|必須|いいえ|直接または間接|バックアップと復元サービスは、ローカル ストレージ クラスをポイントするように構成できます。 |
|**Azure Backup - 長期保有期間**| お客様の環境 -> Azure | Optional| はい (Azure Storage の場合) | 直接のみ |バックアップをオフサイトで長期間保持するために、ローカルで取得したバックアップを Azure Backup に送信し、復元のためにローカル環境に戻すことができます。 **直接接続モードの保留中の可用性**|
|**Azure Defender セキュリティ サービス**|お客様の環境 -> Azure -> お客様の環境|Optional|はい|直接のみ|**直接接続モードの保留中の可用性**|
|**Azure portal からのプロビジョニングと構成の変更**|お客様の環境 -> Azure -> お客様の環境|省略可能|×|直接のみ|プロビジョニングと構成の変更は、Azure Data Studio または [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] を使用してローカルで行うことができます。  直接接続モードでは、Azure portal からプロビジョニングと構成変更を行うこともできます。 **直接接続モードの保留中の可用性**|


## <a name="details-on-internet-addresses-ports-encryption-and-proxy-server-support"></a>インターネット アドレス、ポート、暗号化、プロキシ サーバー サポートの詳細

現在、プレビュー フェーズでは、間接接続モードのみがサポートされています。 このモードでは、インターネット上で利用可能なサービスに必要な接続は 3 つだけです。 接続の種類:

- [Microsoft Container Registry (MCR)](#microsoft-container-registry-mcr)
- [Azure Resource Manager API](#azure-resource-manager-apis)
- [Azure Monitor API](#azure-monitor-apis)

Azure と Microsoft Container Registry へのすべての HTTPS 接続は、SSL/TLS と、正式に署名され検証可能な証明書を使用して暗号化されます。

以降のセクションではこれらの接続についてさらに詳しく説明します。 

### <a name="microsoft-container-registry-mcr"></a>Microsoft Container Registry (MCR)

Microsoft Container Registry では、Azure Arc 対応データ サービスのコンテナー イメージをホストします。  これらのイメージを MCR からプルしてプライベート コンテナー レジストリにプッシュし、そのプライベート コンテナー レジストリからコンテナー イメージをプルするようにデータ コントローラーのデプロイ プロセスを構成できます。

#### <a name="connection-source"></a>接続元

コンテナー イメージをプルする各 Kubernetes ノード上の Kubernetes kubelet。

#### <a name="connection-target"></a>接続先

`mcr.microsoft.com`

#### <a name="protocol"></a>Protocol

HTTPS

#### <a name="port"></a>Port

443

#### <a name="can-use-proxy"></a>プロキシを使用可能

はい

#### <a name="authentication"></a>認証

なし

### <a name="azure-resource-manager-apis"></a>Azure Resource Manager API
一部の機能では、Azure との間でデータを送受信するために、Azure Data Studio、[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] および Azure CLI が Azure Resource Manager API に接続します。

#### <a name="connection-source"></a>接続元

Azure に接続しようとしている Azure Data Studio、[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]、または Azure CLI を実行しているコンピューター。

#### <a name="connection-target"></a>接続先

- `login.microsoftonline.com`
- `management.azure.com`
- `san-af-eastus-prod.azurewebsites.net`
- `san-af-eastus2-prod.azurewebsites.net`
- `san-af-australiaeast-prod.azurewebsites.net`
- `san-af-centralus-prod.azurewebsites.net`
- `san-af-westus2-prod.azurewebsites.net`
- `san-af-westeurope-prod.azurewebsites.net`
- `san-af-southeastasia-prod.azurewebsites.net`
- `san-af-koreacentral-prod.azurewebsites.net`
- `san-af-northeurope-prod.azurewebsites.net`
- `san-af-westeurope-prod.azurewebsites.net`
- `san-af-uksouth-prod.azurewebsites.net`
- `san-af-francecentral-prod.azurewebsites.net`

#### <a name="protocol"></a>Protocol

HTTPS

#### <a name="port"></a>Port

443

#### <a name="can-use-proxy"></a>プロキシを使用可能

はい

#### <a name="authentication"></a>認証 

Azure Active Directory

### <a name="azure-monitor-apis"></a>Azure Monitor API

一部の機能では、Azure との間でデータを送受信するために、Azure Data Studio、[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] および Azure CLI が Azure Resource Manager API に接続します。

#### <a name="connection-source"></a>接続元

監視メトリックまたはログを Azure Monitor にアップロードしている [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] または Azure CLI を実行しているコンピューター。

#### <a name="connection-target"></a>接続先

- `login.microsoftonline.com`
- `management.azure.com`
- `*.ods.opinsights.azure.com`
- `*.oms.opinsights.azure.com`
- `*.monitoring.azure.com`

#### <a name="protocol"></a>Protocol

HTTPS

#### <a name="port"></a>Port

443

#### <a name="can-use-proxy"></a>プロキシを使用可能

はい

#### <a name="authentication"></a>認証 

Azure Active Directory

> [!NOTE]
> 現時点では、Grafana および Kibana ダッシュボードへの、また [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] からデータ コントローラー API へのブラウザー HTTPS/443 接続はすべて、自己署名証明書を使用して SSL で暗号化されます。  将来的には、これらの SSL 接続の暗号化用に独自の証明書を提供するための機能が利用可能になる予定です。

Azure Data Studio および [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] から Kubernetes API サーバーへの接続には、確立した Kubernetes 認証および暗号化が使用されます。  Azure Data Studio および [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] を使用している各ユーザーは、Azure Arc 対応データ サービスに関連した多くのアクションを実行するために、Kubernetes API への認証された接続を持っている必要があります。

