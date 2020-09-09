---
title: Azure Arc の概要
description: Azure Arc とは何か、そしてお客様が Azure の他のサービスや機能を使用してハイブリッド リソースの管理とガバナンスを実現するうえでどのように役立つかについて説明します。
ms.date: 08/25/2020
ms.topic: overview
ms.openlocfilehash: 46bd2089dbc2794d0fb98ceb9a5e97011e36d6ea
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89052046"
---
# <a name="azure-arc-overview"></a>Azure Arc の概要

今日、企業は、しだいに複雑化する環境の管理と統制に苦慮しています。 こうした環境は、複数のデータ センターや複数のクラウド、エッジをまたいで広がっています。 使用される一連の管理ツールは環境とクラウドによって異なり、個別に習得して運用しなければなりません。

同時に、DevOps と ITOps の新しい運用モデルの導入も簡単ではありません。既存のツールでは、新しいクラウド ネイティブのパターンに対応できないためです。

Azure Arc は、マルチクラウドとオンプレミスの管理プラットフォームに一貫性をもたらすことでガバナンスと管理を簡素化します。 Azure Arc では、既存のリソースを Azure Resource Manager に投影することで自分の環境全体を一元的に管理できます。 あたかも Azure 内で実行されているかのように、仮想マシンや Kubernetes クラスター、データベースを管理できるようになります。 それらがどこにあっても、使い慣れた Azure のサービスや管理機能を使用することが可能です。 Azure Arc なら、従来の ITOps を引き続き使用しながら、DevOps プラクティスを導入し、自分の環境で新しいクラウド ネイティブ パターンをサポートすることができます。

:::image type="content" source="./media/overview/azure-arc-control-plane.png" alt-text="Azure Arc の管理コントロール プレーンの図" border="false":::

現在、Azure の外でホストされた次の種類のリソースを Azure Arc で管理することができます。

* サーバー - Windows または Linux を実行するマシン (物理マシンと仮想マシンの両方)。
* Kubernetes クラスター - 複数の Kubernetes ディストリビューションをサポート。
* Azure データ サービス - Azure SQL Database および PostgreSQL Hyperscale サービス。

## <a name="what-does-azure-arc-deliver"></a>Azure Arc で実現できること

Azure Arc の主な機能は次のとおりです。

* 自分のサーバー用に、環境全体で一貫したインベントリ、管理、ガバナンス、セキュリティを導入する。

* Azure 管理サービスを使用してサーバーの監視、保護、更新を行うように [Azure VM の拡張機能](./servers/manage-vm-extensions.md)を構成する。

* Kubernetes クラスターを大規模に管理、統制する。 

* GitOps ベースの構成をコード管理に使用して、GitHub などのソース管理から直接、アプリケーションと構成を 1 つまたは複数のクラスターにデプロイする。

* Azure Policy を使用して、Kubernetes クラスターのコンプライアンスと構成をゼロ タッチで実行する。

* あたかも Azure で実行しているかのように、Kubernetes 環境で Azure データ サービス (具体的には Azure SQL Managed Instance と Azure Database for PostgreSQL Hyperscale) を実行し、アップグレードと更新、セキュリティ、監視などのメリットを活かす。 エラスティック スケールを活用し、Azure への継続的な接続が得られなくても、アプリケーションのダウンタイムを発生させずに更新プログラムを適用することができます。

* Azure portal、Azure CLI、Azure PowerShell、Azure REST API のどれを使用していても、統合されたエクスペリエンスで Azure Arc 対応リソースを表示する。

## <a name="how-much-does-azure-arc-cost"></a>Azure Arc のコスト

以降、Azure Arc で現在利用できる機能の価格について詳しく取り上げます。

### <a name="arc-enabled-servers"></a>Arc 対応サーバー

現在のプレビュー フェーズでは、追加費用なしで Azure Arc 対応サーバーをご利用いただけます。

Arc 対応サーバーで使用されるすべての Azure サービス (Azure Security Center、Azure Monitor など) は、そのサービスの価格で請求されます。 詳細については、[Azure の価格のページ](https://azure.microsoft.com/pricing/)を参照してください。

### <a name="azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes

現在のプレビュー フェーズでは、追加費用なしで Azure Arc 対応 Kubernetes をご利用いただけます。

### <a name="azure-arc-enabled-data-services"></a>Azure Arc 対応データ サービス

現在のプレビュー フェーズでは、追加費用なしで Azure Arc 対応データ サービスをご利用いただけます。

## <a name="next-steps"></a>次のステップ

* Arc 対応サーバーの詳細については、次の[概要](./servers/overview.md)を参照してください

* Arc 対応 Kubernetes の詳細については、次の[概要](./kubernetes/overview.md)を参照してください

* Arc 対応データ サービスの詳細については、次の[概要](https://azure.microsoft.com/services/azure-arc/hybrid-data-services/)を参照してください
