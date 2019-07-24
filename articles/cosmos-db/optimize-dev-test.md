---
title: Azure Cosmos DB での開発とテストのための最適化
description: この記事では、Azure Cosmos DB でサービスの開発およびテスト用に無料で提供されている複数のオプションについて説明します。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: rimman
ms.openlocfilehash: 3bb39fe99e612fb19f12fd049ebb8f600e48373e
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457872"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Azure Cosmos DB での開発とテストのコストを最適化する

この記事では、開発およびテスト用に Azure Cosmos DB を無料で使用するさまざまなオプションについて説明します。

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Azure Cosmos DB エミュレーター (ローカルにダウンロード可能なバージョン)

[Azure Cosmos DB エミュレーター](local-emulator.md)は Azure Cosmos DB クラウド サービスによく似ており、ローカルにダウンロードできます。 ネットワークに接続していなくても、コストをかけずに、Azure Cosmos DB API を使用してコードの記述とテストを行うことができます。 Azure Cosmos DB エミュレーターでは、クラウド サービスとほとんど同じローカル環境が開発用に提供されます。 Azure サブスクリプションを作成することなく、アプリケーションの開発とテストをローカルに行うことができます。 アプリケーションをクラウドにデプロイする準備ができたら、クラウドの Azure Cosmos DB エンドポイントに接続するように接続文字列を更新します。それ以外の変更は必要ありません。 Azure DevOps で [Azure Cosmos DB エミュレーター ビルド タスクを使用して CI/CD パイプラインを設定](tutorial-setup-ci-cd.md)し、テストを実行することもできます。 始めるには、[Azure Cosmos DB エミュレーター](local-emulator.md)に関する記事を参照してください。

## <a name="try-azure-cosmos-db-for-free"></a>Azure Cosmos DB を無料で試す

「[Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)」は、データベースとコレクションを作成し、クラウドの Azure Cosmos DB で実験することができる、無料のエクスペリエンスです。 Azure にサインアップしたり、料金を払ったりする必要はありません。 Azure Cosmos DB 試用アカウントは、限られた期間だけ (現在は 30 日) 利用できます。 いつでも更新できます。 Azure Cosmos DB 試用アカウントを利用すると、Azure Cosmos DB を簡単に評価し、クイック スタートまたはチュートリアルを使用してアプリケーションを構築してテストできます。 コストをかけずに、デモの作成または単体テストの実行を行うことができます。 Azure Cosmos DB 無料試用アカウントを使用することで、ターンキー グローバル配信、SLA、整合性モデルなどの Azure Cosmos DB のプレミアムな機能を無料で評価できます。 最大 25 個の Azure Cosmos コンテナーと 10,000 RU/秒のスループットを備えたデータベースを作成できます。 Azure アカウントをサブスクライブしたり、クレジット カードを使用したりせずに、サンプル アプリケーションを実行できます。 Azure Cosmos DB の無料試用では、数分でマルチリージョンの Azure Cosmos アカウントを作成して、そこでアプリを実行できます。 始めるには、「[Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)」ページをご覧ください。

## <a name="azure-free-account"></a>Azure 無料アカウント

Azure Cosmos DB が含まれる [Azure 無料アカウント](https://azure.microsoft.com/free)では、Azure クレジットとリソースが一定の期間無料で提供されています。 具体的には、Azure Cosmos DB の場合、この無料アカウントでは、5 GB のストレージと 400 RU のプロビジョニング済みスループットが 1 年間提供されます。 このエクスペリエンスでは、すべての開発者が簡単に無料で、Azure Cosmos DB の機能をテストしたり、他の Azure サービスと統合したりできます。 Azure の無料アカウントでは、最初の 30 日間使用できる 200 ドルのクレジットが手に入ります。 サービスの利用を開始した後でも、アップグレードするまでは無料で利用できます。 始めるには、[Azure 無料アカウント](https://azure.microsoft.com/free)のページをご覧ください。

## <a name="next-steps"></a>次の手順

以下の記事を参考にして、エミュレーターまたは無料の Azure Cosmos DB アカウントを使い始めることができます。

* [開発とテストのための最適化](optimize-dev-test.md)の詳細について学習します
* [Azure Cosmos DB の課金内容の確認](understand-your-bill.md)の詳細について学習します
* [スループット コストの最適化](optimize-cost-throughput.md)の詳細について学習します
* [ストレージ コストの最適化](optimize-cost-storage.md)の詳細について学習します
* [読み取りと書き込みのコストの最適化](optimize-cost-reads-writes.md)の詳細について学習します
* [クエリ コストの最適化](optimize-cost-queries.md)の詳細について学習します
* [複数リージョンの Azure Cosmos アカウント コストの最適化](optimize-cost-regions.md)の詳細について学習します

