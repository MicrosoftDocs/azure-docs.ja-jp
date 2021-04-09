---
title: レジストリのパフォーマンスのトラブルシューティング
description: レジストリのパフォーマンスに関する一般的な問題の現象、原因、および対処法
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 15129ebe1da2e52fac106a34863f609c440549ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92148414"
---
# <a name="troubleshoot-registry-performance"></a>レジストリのパフォーマンスのトラブルシューティング

この記事は、Azure コンテナー レジストリのパフォーマンスで発生する可能性のある問題のトラブルシューティングに役立ちます。 

## <a name="symptoms"></a>現象

次のうち 1 つ以上が含まれる場合があります。

* Docker CLI でイメージをプルまたはプッシュすると予想以上に時間がかかる
* Azure Kubernetes Service などのサービスへのイメージのデプロイに予想以上に時間がかかる
* 多数の同時プルまたはプッシュ操作を予想される時間内に完了できない
* Geo レプリケートされたレジストリでのプルまたはプッシュ操作の実行が、予想以上の時間がかかるか、`Error writing blob` または `Error writing manifest` のエラーでプッシュが失敗する

## <a name="causes"></a>原因

* ネットワーク接続の速度により、レジストリ操作が遅くなる場合がある - [対処法](#check-expected-network-speed)
* クライアントでイメージ レイヤーの圧縮または抽出が遅い場合がある - [対処法](#check-client-hardware)  
* レジストリ サービス階層または環境で構成されている制限に達している - [対処法](#review-configured-limits)
* Geo レプリケートされたレジストリのレプリカが隣接するリージョンにある - [対処法](#configure-geo-replicated-registry)
* 地理的に離れたレジストリ レプリカからプルしている - [対処法](#configure-dns-for-geo-replicated-registry)

ここで問題が解決できない場合は、「[高度なトラブルシューティング](#advanced-troubleshooting)」と[次のステップ](#next-steps)でその他のオプションを参照してください。

## <a name="potential-solutions"></a>対処法

### <a name="check-expected-network-speed"></a>予想されるネットワーク速度を確認する

インターネットのアップロードとダウンロードの速度を確認するか、AzureSpeed などのツールを使用して、[アップロード](https://www.azurespeed.com/Azure/Uploadß)、またはレジストリ イメージ レイヤーをホストする Azure BLOB ストレージからの[ダウンロード](https://www.azurespeed.com/Azure/Download)をテストします。

イメージのサイズを、サポートされている最大サイズと、レジストリ サービス レベルでサポートされているダウンロードまたはアップロードの帯域幅と比較して確認します。 レジストリが Basic または Standard レベルの場合は、パフォーマンスを向上させるためにアップグレードを検討してください。 

イメージを他のサービスにデプロイする場合は、レジストリとターゲットが配置されているリージョンを確認します。 パフォーマンスを向上させるには、レジストリとデプロイ ターゲットを同じリージョンまたはネットワークで近いリージョンに配置することを検討してください。

関連リンク:

* [Azure Container Registry サービス階層](container-registry-skus.md)    
* [Container Registry に関する FAQ](container-registry-faq.md)
* [Azure Blob Storage のパフォーマンスとスケーラビリティのターゲット](../storage/blobs/scalability-targets.md)

### <a name="check-client-hardware"></a>クライアント ハードウェアを確認する

Docker クライアント上のディスクの種類と CPU は、プルまたはプッシュ操作の一部としてクライアント上のイメージ レイヤーを抽出または圧縮する速度に影響を与える可能性があります。 たとえば、ハード ディスク ドライブ上のレイヤー抽出は、ソリッドステート ディスクよりも時間がかかります。 Azure コンテナー レジストリと、Docker Hub などのパブリック レジストリから、同等のイメージのプル操作を比較します。

### <a name="review-configured-limits"></a>構成されている制限を確認する

複数の多層イメージをレジストリに同時にプッシュまたはプルする場合は、レジストリ サービス レベルでサポートされている ReadOps と WriteOps の制限を確認します。 レジストリが Basic または Standard レベルの場合は、制限を引き上がるためにアップグレードを検討してください。 また、多くの同時操作で発生する可能性があるネットワーク帯域幅の調整についても、ネットワーク プロバイダーに確認します。 

最大同時アップロードまたはダウンロードについて、クライアントでのプッシュ操作またはプル操作ごとに、Docker デーモン構成を確認します。 必要に応じて、制限を引き上げて構成します。

各イメージ レイヤーには個別のレジストリ読み取りまたは書き込み操作が必要であるため、イメージ内のレイヤーの数を確認します。 イメージ レイヤーの数を減らす方法を検討してください。

関連リンク:

* [Azure Container Registry サービス階層](container-registry-skus.md)
* [dockerd](https://docs.docker.com/engine/reference/commandline/dockerd/)

### <a name="configure-geo-replicated-registry"></a>Geo レプリケートされたレジストリを構成する

Geo レプリケートされたレジストリにイメージをプッシュする Docker クライアントにより、イメージ レイヤーとそのマニフェストの一部が、単一のレプリカ リージョンにプッシュされないことがあります。 この状況は、Azure Traffic Manager により、レジストリ要求が、ネットワークで一番近いレプリカ レジストリにルーティングされることが原因で発生する可能性があります。 レジストリに近くのレプリケーション リージョンが 2 つある場合、イメージ層とマニフェストはその 2 つのサイトに分配されることがあり、マニフェストの有効性が検証されると、プッシュ操作に失敗します。

イメージをプッシュするとき、DNS 解決を最も近くのレプリカに求めることで最適化するには、プッシュ操作のソースと同じ Azure リージョンか、Azure 外での作業となる場合、最も近くのリージョンで geo レプリカ レジストリを構成します。

Geo レプリケートされたレジストリの操作をトラブルシューティングする場合、Traffic Manager の 1 つまたは複数のレプリケーションへのルーティングを一時的に無効にすることもできます。

関連リンク:

* [Azure Container Registry の geo レプリケーション](container-registry-geo-replication.md)

### <a name="configure-dns-for-geo-replicated-registry"></a>Geo レプリケートされたレジストリの DNS を構成する

Geo レプリケートされたレジストリからのプル操作に時間がかかる場合、クライアントの DNS 構成が地理的に離れた DNS サーバーに解決されている可能性があります。 この場合、要求が、ネットワークで DNS サーバーに近いが、クライアントからは離れているレプリカに Traffic Manager によってルーティングされている可能性があります。 `nslookup` や `dig` (Linux の場合) などのツールを実行して、レジストリ要求が、Traffic Manager によってルーティングされるレプリカを決定します。 次に例を示します。

```console
nslookup myregistry.azurecr.io
```

対処法は、より近い DNS サーバーを構成することです。

関連リンク:

* [Azure Container Registry の geo レプリケーション](container-registry-geo-replication.md)
* [geo レプリカ レジストリでプッシュ操作の問題を解決する](container-registry-geo-replication.md#troubleshoot-push-operations-with-geo-replicated-registries)
* [レプリケーションへのルーティングを一時的に無効にする](container-registry-geo-replication.md#temporarily-disable-routing-to-replication)
* [Traffic Manager に関する FAQ](../traffic-manager/traffic-manager-faqs.md)

### <a name="advanced-troubleshooting"></a>高度なトラブルシューティング

レジストリ リソースへのアクセス許可がある場合は、[レジストリ環境の正常性を確認](container-registry-check-health.md)します。 エラーが報告された場合は、対処法について[エラー リファレンス](container-registry-health-error-reference.md)を確認してください。

レジストリで[リソース ログの収集](container-registry-diagnostics-audit-logs.md)が有効になっている場合は、ContainterRegistryRepositoryEvents ログを確認します。 このログには、プッシュ イベントやプル イベントなどの操作に関する情報が格納されています。 ログで[リポジトリレベルの操作エラー](container-registry-diagnostics-audit-logs.md#repository-level-operation-failures)のクエリを実行します。 

関連リンク:

* [診断の評価と監査のためのログ](container-registry-diagnostics-audit-logs.md)
* [Container Registry に関する FAQ](container-registry-faq.md)
* [Azure Container Registry のベスト プラクティス](container-registry-best-practices.md)

## <a name="next-steps"></a>次のステップ

ここで問題を解決できない場合は、次のオプションを参照してください。

* レジストリのその他のトラブルシューティングのトピックには、次のものがあります。
  * [レジストリ ログインのトラブルシューティング](container-registry-troubleshoot-login.md)
  * [レジストリに関するネットワークの問題のトラブルシューティング](container-registry-troubleshoot-access.md)
* [コミュニティ サポート](https://azure.microsoft.com/support/community/) オプション
* [Microsoft Q&A](/answers/products/)
* [サポート チケットを開く](https://azure.microsoft.com/support/create-ticket/)