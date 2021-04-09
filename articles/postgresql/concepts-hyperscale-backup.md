---
title: バックアップと復元 – Hyperscale (Citus) - Azure Database for PostgreSQL
description: 不慮の破損または削除からのデータの保護
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 9656e6ab608665e325ac5f772e06df9ac296c521
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92485377"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus) でのバックアップと復元

Azure Database for PostgreSQL – Hyperscale (Citus) では、各ノードのバックアップを自動的に作成し、ローカル冗長ストレージに格納します。 バックアップを使用して、指定した時間に Hyperscale (Citus) クラスターを復元することができます。 不慮の破損または削除からデータを保護するバックアップと復元は、ビジネス継続性戦略の最も重要な部分です。

## <a name="backups"></a>バックアップ

少なくとも 1 日に 1 回、Azure Database for PostgreSQL では、データ ファイルとデータベース トランザクション ログのスナップショット バックアップが作成されます。 バックアップを使用すると、サーバーを、保持期間内の任意の時点に復元できます (保持期間は現在、すべてのクラスターで 35 日です)。すべてのバックアップが、AES 256 ビット暗号化を使用して暗号化されます。

可用性ゾーンをサポートする Azure リージョンでは、バックアップ スナップショットは 3 つの可用性ゾーンに格納されます。 少なくとも 1 つの可用性ゾーンがオンラインになっている限り、Hyperscale (Citus) クラスターは復元可能です。

バックアップ ファイルをエクスポートすることはできません。 Azure Database for PostgreSQL の復元操作にのみ使用できます。

### <a name="backup-storage-cost"></a>バックアップ ストレージのコスト

現在のバックアップ ストレージの価格については、Azure Database for PostgreSQL - Hyperscale (Citus) の[価格に関するページ](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)を参照してください。

## <a name="restore"></a>復元

Azure Database for PostgreSQL では、Hyperscale (Citus) クラスターを復元すると、元のノードのバックアップから新しいクラスターが作成されます。 

> [!IMPORTANT]
>Hyperscale (Citus) クラスターは、別のクラスター名を使用して、同じサブスクリプションとリソース グループ内でのみ復元できます。


> [!IMPORTANT]
> 削除された Hyperscale (Citus) クラスターを復元することはできません。 クラスターを削除すると、そのクラスターに属するすべてのノードが削除され、復旧できなくなります。 管理者は、デプロイ後の誤削除や予期せぬ変更からクラスターのリソースを保護するために、[管理ロック](../azure-resource-manager/management/lock-resources.md)を利用できます。

### <a name="point-in-time-restore-pitr"></a>ポイントインタイム リストア (PITR)

過去 35 日以内の任意の時点にクラスターを復元することができます。
ポイントインタイム リストアは複数のシナリオで役に立ちます。 たとえば、ユーザーが誤ってデータを削除したとき、重要なテーブルやデータベースを削除したとき、またはアプリケーションで適切なデータが不適切なデータで誤って上書きされた場合などです。

復元プロセスでは、元のものと同じ Azure リージョン、サブスクリプション、およびリソース グループに新しいクラスターが作成されます。 クラスターの構成は元のものです。つまり、ノード数、仮想コア数、ストレージ サイズ、ユーザー ロール、PostgreSQL バージョン、Citus 拡張機能のバージョンは同じです。

ファイアウォール設定と PostgreSQL サーバーのパラメーターは元のサーバー グループから保持されず、既定値にリセットされます。 ファイアウォールによって、すべての接続が阻止されます。 復元後にこれらの設定を手動で調整する必要があります。

> [!IMPORTANT]
> Hyperscale (Citus) クラスターのポイントインタイム リストアを実行するには、サポート要求を開く必要があります。

### <a name="post-restore-tasks"></a>復元後のタスク

いずれかの復旧メカニズムで復元した後、ユーザーとアプリケーションを元に戻して実行するには、次のようにする必要があります。

* 元のサーバーを新しいサーバーで置き換える場合は、クライアントとクライアント アプリケーションを新しいサーバーにリダイレクトする
* ユーザーが接続できるように、適切なサーバー レベルのファイアウォールと VNet ルールが適用されていることを確認します。 これらのルールは元のサーバー グループからはコピーされません。
* 必要に応じて、PostgreSQL サーバー パラメーターを調整します。 パラメーターは元のサーバー グループからはコピーされません。
* 適切なログインとデータベース レベルのアクセス許可が適切に指定されていることを確認する
* 必要に応じて、アラートを構成する

## <a name="next-steps"></a>次のステップ

*  [Azure 可用性ゾーン](../availability-zones/az-overview.md)について学習します。
* Hyperscale (Citus) サーバー グループで [推奨されるアラート](./howto-hyperscale-alert-on-metric.md#suggested-alerts)を設定します。