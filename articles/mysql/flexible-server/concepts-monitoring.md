---
title: 監視する - Azure Database for MySQL - フレキシブル サーバー
description: この記事では、Azure Database for MySQL フレキシブル サーバーでの監視およびアラート生成のためのメトリック (CPU、ストレージ、および接続の統計を含む) について説明します。
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: c2883a2ddba9a322c09a318aa9f232de5f4704b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "100595157"
---
# <a name="monitor-azure-database-for-mysql-flexible-servers-with-built-in-metrics"></a>組み込みのメトリックを使用して Azure Database for MySQL フレキシブル サーバーを監視する

> [!IMPORTANT] 
> Azure Database for MySQL - フレキシブル サーバーは現在、パブリック プレビュー段階にあります。

Azure Database for MySQL フレキシブル サーバーでは、Azure Monitor を通してサーバーを監視できます。 メトリックは、サーバーのリソースの特定の時点における何らかの側面を表す数値です。 サーバーのリソースを監視すると、ユーザーにとって最も重要なものを監視できるため、ワークロードのトラブルシューティングと最適化に役立ちます。 適切なメトリックを監視すると、サーバーとアプリケーションのパフォーマンス、信頼性、可用性を維持するのに役立ちます。

この記事では、フレキシブル サーバーで使用できる、サーバーの動作についての分析情報を提供するさまざまなメトリックについて説明します。

## <a name="available-metrics"></a>使用可能なメトリック

Azure Database for MySQL フレキシブル サーバーには、ワークロードがどのように実行されているかを把握し、そのデータに基づいてサーバーとアプリケーションへの影響を理解するための、さまざまなメトリックが用意されています。 たとえば、フレキシブル サーバーでは、**ホストの CPU 使用率**、**アクティブな接続**、**IO の割合**、および **ホストのメモリ使用率** を監視して、パフォーマンスに影響があるタイミングを特定できます。 そこから、ワークロードを最適化したり、コンピューティング レベルを変更して垂直方向にスケーリングしたり、読み取りレプリカを使用して水平方向にスケーリングしたりすることが必要になる場合があります。

すべての Azure メトリックは 1 分間隔で、各メトリックの 30 日間の履歴が保持されます。 メトリックにアラートを構成できます。 詳細な手順については、[アラートの設定方法](./how-to-alert-on-metric.md)に関する記事をご覧ください。 その他のタスクとして、自動化されたアクションの設定、高度な分析の実行、履歴のアーカイブなどがあります。 詳細については、[Azure のメトリックの概要](../../azure-monitor/data-platform.md)に関する記事をご覧ください。

### <a name="list-of-metrics"></a>メトリックの一覧
これらのメトリックは、Azure Database for MySQL に使用できます。

|メトリックの表示名|メトリック|ユニット|説明|
|---|---|---|---|
|ホストの CPU 使用率|cpu_percent|Percent|サーバーの CPU 使用率 (顧客のワークロードと Azure MySQL プロセスの両方からの CPU 使用率を含む)|
|ホストのネットワーク受信 |network_bytes_ingress|バイト|サーバー上の受信ネットワーク トラフィック (顧客のデータベースと、レプリケーション、監視、ログなどの Azure MySQL の機能からのトラフィックを含む)|
|ホストのネットワーク送信|network_bytes_egress|バイト|サーバー上の送信ネットワーク トラフィック (顧客のデータベースと、レプリケーション、監視、ログなどの Azure MySQL の機能からのトラフィックを含む)|
|レプリケーションのラグ|replication_lag|Seconds|最後に再生されたトランザクションからの時間。 このメトリックは、レプリカ サーバーのみで使用できます。|
|アクティブな接続|active_connection|Count|サーバーへのアクティブな接続の数|
|使用済みバックアップ ストレージ|backup_storage_used|バイト|使用されているバックアップ ストレージの量。|
|IO の割合|io_consumption_percent|Percent|使用されている IO の割合|
|ホストのメモリ使用率|memory_percent|Percent|サーバーで使用中のメモリ使用率 (顧客のワークロードと Azure MySQL プロセスの両方からのメモリ使用率を含む)|
|ストレージの制限|storage_limit|バイト|このサーバーの最大のストレージ|
|ストレージの割合|storage_percent|Percent|サーバーの最大数のうち使用されているストレージの割合|
|使用されているストレージ|storage_used|バイト|使用されているストレージの量。 サービスで使用されるストレージには、データベース ファイル、トランザクション ログ、サーバー ログが含まれることがあります。|
|合計接続数|total_connections|Count|サーバーへの合計接続数|
|中止された接続|aborted_connections|Count|MySQL への接続を試みて失敗した (たとえば、資格情報が正しくないために接続できなかった場合などの) 回数。|
|クエリ|Query|Count|1 秒あたりのクエリ数|

## <a name="next-steps"></a>次のステップ
- メトリックに対するアラートの作成のガイダンスについては、[アラートを設定する方法](./how-to-alert-on-metric.md)に関するページをご覧ください。
- パフォーマンスを向上させるための [IOPS のスケーリング](./concepts/../concepts-compute-storage.md#iops)の詳細を確認します。