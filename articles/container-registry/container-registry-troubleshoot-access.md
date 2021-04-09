---
title: レジストリに関するネットワークの問題のトラブルシューティング
description: 仮想ネットワークまたはファイアウォールの内側で Azure コンテナー レジストリにアクセスするときの一般的な問題の現象、原因、および解決策
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: 75c94d40663a7058dab7ed691183dd578964edcc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101699608"
---
# <a name="troubleshoot-network-issues-with-registry"></a>レジストリに関するネットワークの問題のトラブルシューティング

この記事は、仮想ネットワークまたはファイアウォールの内側で Azure コンテナー レジストリにアクセスするときに発生する可能性のある問題のトラブルシューティングに役立ちます。 

## <a name="symptoms"></a>現象

次のうち 1 つ以上が含まれる場合があります。

* イメージをプッシュまたはプルできず、エラー `dial tcp: lookup myregistry.azurecr.io` が発生する
* イメージをプッシュまたはプルできず、Azure CLI エラー `Could not connect to the registry login server` が発生する
* レジストリから Azure Kubernetes Service または別の Azure サービスにイメージをプルできない
* HTTPS プロキシの背後にあるレジストリにアクセスできず、エラー `Error response from daemon: login attempt failed with status: 403 Forbidden` が発生する
* 仮想ネットワーク設定を構成できず、エラー `Failed to save firewall and virtual network settings for container registry` が発生する
* Azure portal でレジストリ設定にアクセスまたは表示できないか、Azure CLI を使用してレジストリを管理できない
* 仮想ネットワークの設定またはパブリック アクセス規則を追加または変更できない
* ACR タスクによるイメージのプッシュまたはプルができない
* Azure Security Center によるレジストリ内のイメージのスキャンができない。または、スキャン結果が Azure Security Center に表示されない

## <a name="causes"></a>原因

* クライアントのファイアウォールまたはプロキシによってアクセスが妨げられている - [解決策](#configure-client-firewall-access)
* レジストリのパブリック ネットワーク アクセス規則によってアクセスが妨げられている - [解決策](#configure-public-access-to-registry)
* 仮想ネットワークの構成によってアクセスが妨げられている - [解決策](#configure-vnet-access)
* Azure Security Center または特定の他の Azure サービスをプライベート エンドポイント、サービス エンドポイント、またはパブリック IP アクセス規則を持つレジストリと統合しようとしている - [解決策](#configure-service-access)

## <a name="further-diagnosis"></a>詳しい診断 

[az acr check-health](/cli/azure/acr#az-acr-check-health) コマンドを実行して、レジストリ環境の正常性に関する詳細情報を取得し、必要に応じてターゲット レジストリにアクセスします。 たとえば、特定のネットワーク接続や構成の問題を診断します。 

コマンドの例については、「[Azure コンテナー レジストリの正常性のチェック](container-registry-check-health.md)」を参照してください。 エラーが報告された場合は、推奨される対処法について、[エラー リファレンス](container-registry-health-error-reference.md)と次のセクションを確認してください。

Azure Kubernetes Service でレジストリを使用しているときに問題が発生した場合は、[az aks check-acr](/cli/azure/aks#az_aks_check_acr) コマンドを実行して、AKS クラスターからレジストリにアクセスできることを確認します。

> [!NOTE]
> また、レジストリの認証または承認に問題がある場合にも、ネットワーク接続の現象が発生することがあります。 「[レジストリ ログインのトラブルシューティング](container-registry-troubleshoot-login.md)」を参照してください。

## <a name="potential-solutions"></a>対処法

### <a name="configure-client-firewall-access"></a>クライアント ファイアウォール アクセスを構成する

クライアント ファイアウォールまたはプロキシ サーバーの背後からレジストリにアクセスするには、レジストリのパブリック REST エンドポイントおよびデータ エンドポイントにアクセスするようにファイアウォール規則を構成します。 [専用データ エンドポイント](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints)が有効になっている場合は、次にアクセスするための規則が必要です。

* REST エンドポイント: `<registryname>.azurecr.io`
* データ エンドポイント: `<registry-name>.<region>.data.azurecr.io`

geo レプリケートされたレジストリの場合は、リージョンのレプリカごとにデータ エンドポイントへのアクセスを構成します。

HTTPS プロキシの背後で、Docker クライアントと Docker デーモンの両方をプロキシの動作用に確実に構成します。

ContainerRegistryLoginEvents テーブルのレジストリ リソース ログは、ブロックされている接続試行の診断に役立ちます。

関連リンク:

* [ファイアウォールの内側から Azure コンテナー レジストリにアクセスする規則を構成する](container-registry-firewall-access-rules.md)
* [HTTP/HTTPS プロキシの構成](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Azure Container Registry の geo レプリケーション](container-registry-geo-replication.md)
* [診断の評価と監査のための Azure Container Registry ログ](container-registry-diagnostics-audit-logs.md)

### <a name="configure-public-access-to-registry"></a>レジストリへのパブリック アクセスを構成する

インターネット経由でレジストリにアクセスしている場合は、クライアントからのパブリック ネットワーク アクセスがレジストリで許可されていることを確認します。 既定では、Azure コンテナー レジストリによって、すべてのネットワークからのパブリック レジストリエンド ポイントへのアクセスが許可されています。 レジストリでは、選択したネットワークまたは選択した IP アドレスへのアクセスを制限することができます。 

レジストリがサービス エンドポイントを持つ仮想ネットワーク用に構成されている場合、パブリック ネットワークアクセスを無効にすると、サービス エンドポイント経由のアクセスも無効になります。 レジストリが Private Link を使用する仮想ネットワーク用に構成されている場合、IP ネットワーク規則は、レジストリのプライベート エンドポイントには適用されません。 

関連リンク:

* [パブリック IP ネットワーク ルールを構成する](container-registry-access-selected-networks.md)
* [Azure Private Link を使用して Azure Container Registry にプライベートで接続する](container-registry-private-link.md)
* [Azure 仮想ネットワークのサービス エンドポイントを使用してコンテナー レジストリへのアクセスを制限する](container-registry-vnet.md)


### <a name="configure-vnet-access"></a>VNet アクセスを構成する

Private Link のプライベート エンドポイントまたはサービス エンドポイント (プレビュー) のいずれかを使用して、仮想ネットワークが構成されていることを確認します。 現在、Azure Bastion エンドポイントはサポートされていません。

ネットワーク内の他のリソースからレジストリへのトラフィックを制限するために使用される NSG ルールとサービス タグを確認します。 

レジストリへのサービス エンドポイントが構成されている場合は、そのネットワーク サブネットからのアクセスを許可するネットワーク規則がレジストリに追加されていることを確認します。 サービス エンドポイントによって、ネットワーク内の仮想マシンおよび AKS クラスターからのアクセスのみがサポートされます。

別の Azure サブスクリプションの仮想ネットワークを使用してレジストリ アクセスを制限する場合は、必ずそのサブスクリプションに `Microsoft.ContainerRegistry` リソース プロバイダーを登録してください。 Azure portal、Azure CLI、またはその他の Azure ツールを使用して Azure Container Registry の[リソース プロバイダーを登録します](../azure-resource-manager/management/resource-providers-and-types.md)。

Azure Firewall または同様のソリューションがネットワークに構成されている場合は、AKS クラスターなどの他のリソースからのエグレス トラフィックが、レジストリ エンドポイントに到達できるようになっていることを確認します。

プライベート エンドポイントが構成されている場合は、DNS によってレジストリのパブリック FQDN (*myregistry.azurecr.io* など) がレジストリのプライベート IP アドレスに解決されることを確認します。 DNS の参照には `dig` や `nslookup` などのネットワーク ユーティリティを使用します。

関連リンク:

* [Azure Private Link を使用して Azure Container Registry にプライベートで接続する](container-registry-private-link.md)
* [Azure 仮想ネットワークのサービス エンドポイントを使用してコンテナー レジストリへのアクセスを制限する](container-registry-vnet.md)
* [AKS クラスターに必要な送信ネットワーク規則と FQDN](../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
* [Kubernetes: DNS 解決のデバッグ](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
* [仮想ネットワーク サービス タグ](../virtual-network/service-tags-overview.md)

### <a name="configure-service-access"></a>サービス アクセスを構成する

現時点では、ネットワークが制限されたコンテナー レジストリへのアクセスは、次のいくつかの Azure サービスでは許可されていません。

* Azure Security Center では、プライベート エンドポイント、選択したサブネット、または IP アドレスへのアクセスを制限するレジストリで[イメージの脆弱性のスキャン](../security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json)を実行することはできません。 
* Azure App Service や Azure Container Instances を含む特定の Azure サービスのリソースは、ネットワークが制限されたコンテナー レジストリにアクセスすることはできません。

コンテナー レジストリに対するこれらの Azure サービスのアクセスや統合が必要な場合は、ネットワークの制限を解除します。 たとえば、レジストリのプライベート エンドポイントを削除するか、レジストリのパブリック アクセス規則を削除または変更します。

2021 年 1 月以降、選択した信頼されたサービスからの[アクセスを許可](allow-access-trusted-services.md)するように、ネットワーク制限付きレジストリを構成できます。

関連リンク:

* [Security Center による Azure Container Registry のイメージ スキャン](../security-center/defender-for-container-registries-introduction.md)
* [フィードバック](https://feedback.azure.com/forums/347535-azure-security-center/suggestions/41091577-enable-vulnerability-scanning-for-images-that-are)の提供
* [信頼されたサービスがネットワーク制限付きコンテナー レジストリに安全にアクセスできるようにする](allow-access-trusted-services.md)


## <a name="advanced-troubleshooting"></a>高度なトラブルシューティング

レジストリで[リソース ログの収集](container-registry-diagnostics-audit-logs.md)が有効になっている場合は、ContainterRegistryLoginEvents ログを確認します。 このログには、受信 ID や IP アドレスを含む、認証イベントと状態が格納されています。 ログで[レジストリ認証エラー](container-registry-diagnostics-audit-logs.md#registry-authentication-failures)のクエリを実行します。 

関連リンク:

* [診断の評価と監査のためのログ](container-registry-diagnostics-audit-logs.md)
* [Container Registry に関する FAQ](container-registry-faq.md)
* [Azure Container Registry 用の Azure セキュリティ ベースライン](security-baseline.md)
* [Azure Container Registry のベスト プラクティス](container-registry-best-practices.md)

## <a name="next-steps"></a>次のステップ

ここで問題を解決できない場合は、次のオプションを参照してください。

* レジストリのその他のトラブルシューティングのトピックには、次のものがあります。
  * [レジストリ ログインのトラブルシューティング](container-registry-troubleshoot-login.md) 
  * [レジストリのパフォーマンスのトラブルシューティング](container-registry-troubleshoot-performance.md)
* [コミュニティ サポート](https://azure.microsoft.com/support/community/) オプション
* [Microsoft Q&A](/answers/products/)
* [サポート チケットを開く](https://azure.microsoft.com/support/create-ticket/)