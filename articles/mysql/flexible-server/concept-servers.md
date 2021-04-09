---
title: サーバーの概念 - Azure Database for MySQL フレキシブル サーバー
description: このトピックでは、Azure Database for MySQL フレキシブル サーバーを使用するための考慮事項とガイドラインを示します
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: b664dd406a1ab90b4ea5e85005a69935f345c609
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034661"
---
# <a name="server-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Azure Database for MySQL フレキシブル サーバー (プレビュー) でのサーバーの概念

> [!IMPORTANT] 
> Azure Database for MySQL フレキシブル サーバーは、現在パブリック プレビュー段階にあります。

この記事では、Azure Database for MySQL フレキシブル サーバーの使用に関する考慮事項とガイドラインを示します。

## <a name="what-is-an-azure-database-for-mysql-flexible-server"></a>Azure Database for MySQL フレキシブル サーバーとは

Azure Database for MySQL フレキシブル サーバーは、MySQL のコミュニティ バージョンが実行されるフル マネージドのデータベース サービスです。 一般に、ユーザーの要件に基づいて、柔軟性と構成のカスタマイズを提供するように設計されています。 これは、オンプレミスで一般的な MySQL サーバー コンストラクトと同じです。 具体的には、フレキシブル サーバーは管理されており、フレキシブル サーバーでは何もしなくても優れたパフォーマンスとサーバーの管理および制御が提供され、サーバー レベルでアクセスと機能が公開されます。

Azure Database for MySQL フレキシブル サーバーとは次のようなものです。

- Azure サブスクリプション内で作成されます。
- データベースの親リソースです。
- サーバー パラメーターを通じて MySQL の構成を公開できます (サーバー パラメーターの概念へのリンク)。
- 自動バックアップが実行され、ポイントインタイム リストアがサポートされます。
- データベースに名前空間を提供します。
- 強力な有効期間のセマンティクスが含まれるコンテナーです。サーバーを削除すると、包含データベースが削除されます。
- リージョンにリソースを併置します。
- ユーザー指定のサーバー メンテナンス スケジュールがサポートされます
- 高可用性向上のためゾーン冗長セットアップでフレキシブル サーバーをデプロイできます
- データベース サーバー アクセスのために仮想ネットワーク統合が提供されます
- 使用されていないときにフレキシブル サーバーを一時停止してコストを節約する手段が提供されます
- データベースに適用される管理ポリシーのスコープ (ログイン、ファイアウォール、ユーザー、ロール、構成など) を提供します。
- メジャー バージョン MySQL 5.7 および MySQL 8.0 をサポートしています。 詳細については、[サポートされる Azure Database for MySQL エンジンのバージョン](./../concepts-supported-versions.md)に関するページを参照してください。

Azure Database for MySQL フレキシブル サーバー内には、1 つまたは複数のデータベースを作成できます。 サーバーごとに 1 つのデータベースを作成してすべてのリソースを使用するか、複数のデータベースを作成してリソースを共有することができます。 価格は、コンピューティング レベル、仮想コア数、ストレージ (GB) の構成に基づいて、サーバーごとに構成されています。 詳細については、[コンピューティングとストレージ](./concepts-compute-storage.md)に関するページを参照してください。

## <a name="stopstart-an-azure-database-for-mysql-flexible-server"></a>Azure Database for MySQL フレキシブル サーバーを停止または開始する

Azure Database for MySQL フレキシブル サーバーを使用すると、使用されていないときにサーバーを **停止** し、アクティビティを再開したらサーバーを **開始** することができます。 これは基本的に、データベース サーバーのコストを節約し、使用したリソースに対してのみ課金されるようにするために行われます。 これは、開発テスト ワークロードの場合、および 1 日の一部でしかサーバーを使用しない場合に、いっそう重要になります。 サーバーを停止すると、すべてのアクティブな接続が切断されます。 後で、サーバーをオンラインに戻すときは、[Azure portal](how-to-stop-start-server-portal.md) または CLI のいずれかを使用できます。

サーバーが **停止** 状態にある場合、サーバーのコンピューティングは課金されません。 ただし、サーバーが再び起動されたときにデータ ファイルを使用できるようにするため、サーバーのストレージは維持されているため、ストレージは引き続き課金されます。

> [!IMPORTANT]
> サーバーを **停止** すると、それ以降連続して 7 日間は、その状態のままになります。 この期間内に手動で **開始** しないと、サーバーは 7 日の終わりに自動的に開始されます。 サーバーを使用していない場合は、再び **停止** することができます。

サーバーが停止されている間、サーバーで管理操作を実行することはできません。 サーバーの構成設定を変更するには、[サーバーを起動する](how-to-stop-start-server-portal.md)必要があります。 [停止と開始の制限事項](./concepts-limitations.md#stopstart-operation)に関するページを参照してください。

## <a name="how-do-i-manage-a-server"></a>サーバーの管理方法

Azure Database for MySQL フレキシブル サーバーを管理するには、[Azure portal](./quickstart-create-server-portal.md) または [Azure CLI](./quickstart-create-server-cli.md) を使用できます。

## <a name="next-steps"></a>次のステップ

-   [サーバーの作成](./quickstart-create-server-portal.md)について確認する
-   [監視とアラート](./how-to-alert-on-metric.md)について確認する

