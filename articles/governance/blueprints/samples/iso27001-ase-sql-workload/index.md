---
title: ISO 27001 ASE/SQL ワークロード ブループリント サンプルの概要
description: ISO 27001 App Service Environment/SQL Database ワークロード ブループリント サンプルの概要とアーキテクチャ
ms.date: 07/13/2020
ms.topic: sample
ms.openlocfilehash: 76177efcac8b32907c60cecac41404a3834d0fb8
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926095"
---
# <a name="overview-of-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>ISO 27001 App Service Environment/SQL Database ワークロード ブループリント サンプルの概要。

ISO 27001 App Service Environment/SQL Database ワークロード ブループリント サンプルでは、[ISO 27001 共有サービス](../iso27001-shared/index.md) ブループリント サンプルへの追加のインフラストラクチャが提供されます。
このブループリントにより、お客様は、認定またはコンプライアンスの要件があるシナリオにソリューションを提供するクラウド ベースのアーキテクチャをデプロイできます。

このサンプルと [ISO 27001 共有サービス](../iso27001-shared/index.md) ブループリント サンプルの 2 つの ISO 27001 ブループリント サンプルがあります。

> [!IMPORTANT]
> このサンプルは、[ISO 27001 共有サービス](../iso27001-shared/index.md) ブループリント サンプルによってデプロイされるインフラストラクチャに依存します。 これは、最初にデプロイする必要があります。

## <a name="architecture"></a>Architecture

ISO 27001 App Service Environment/SQL Database ワークロード ブループリント サンプルにより、サービスとしてのプラットフォーム ベースの Web 環境がデプロイされます。 環境は、複数の Web アプリケーション、Web API、および ISO 27001 標準に準拠した SQL Database インスタンスをホストするために使用できます。 このブルー プリントのサンプルは、[ISO 27001 共有サービス](../iso27001-shared/index.md) ブルー プリント サンプルに依存します。

:::image type="content" source="../../media/sample-iso27001-ase-sql-workload/iso27001-ase-sql-workload-blueprint-sample-design.png" alt-text="ISO 27001 ASE/SQL ワークロード ブループリント サンプルの設計" border="false":::

この環境は、ISO 27001 標準に基づいたセキュリティで保護された、完全に監視されたエンタープライズ対応のワークロード インフラストラクチャを提供するために使用される複数の Azure サービスで構成されます。 この環境は、以下で構成されます。

- ブループリント サンプルによってデプロイされる [Azure App Service Environment](../../../../app-service/environment/intro.md) 内でリソースをデプロイおよび管理する権限を持つ DevOps という名前の [Azure ロール](../../../../role-based-access-control/overview.md)
- 環境にデプロイできるサービスをロックダウンする [Azure ポリシー](../../../policy/overview.md)。任意のパブリック IP アドレス (PIP) リソースの作成を拒否します。
- 1 つのサブネットを含む仮想ネットワーク。既存の[共有サービス](../iso27001-shared/index.md)環境にピアリングし直され、[共有サービス](../iso27001-shared/index.md)のファイアウォールによってすべてのトラフィックの通過が強制されます。 仮想ネットワークは、次のリソースをホストします。
  - [Azure App Service Environment](../../../../app-service/environment/intro.md)。1 つ以上の Web アプリケーション、Web API、または関数のホストに使用できます。
  - VNet サービス エンドポイントを使用する [Azure Key Vault](../../../../key-vault/general/overview.md) インスタンス。ワークロード環境で実行されるアプリケーションによって使用されるシークレットを格納します。
  - VNet サービス エンドポイントを使用する [Azure SQL Database](../../../../azure-sql/database/sql-database-paas-overview.md) サーバー インスタンス。ワークロード環境内のアプリケーション用に使用されるデータベースをホストします。

## <a name="next-steps"></a>次のステップ

ISO 27001 App Service Environment/SQL Database ワークロード ブループリント サンプルの概要とアーキテクチャを確認しました。 次に、コントロール マッピングと、このサンプルをデプロイする方法を確認するには、次の記事を参照してください。

> [!div class="nextstepaction"]
> [ISO 27001 App Service Environment/SQL Database ワークロード ブループリント - コントロール マッピング](./control-mapping.md)
> [ISO 27001 App Service Environment/SQL Database ワークロード ブループリント - デプロイ手順](./deploy.md)

ブループリントとその使用方法に関するその他の記事:

- [ブループリントのライフサイクル](../../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../../how-to/update-existing-assignments.md)方法を参照する。
