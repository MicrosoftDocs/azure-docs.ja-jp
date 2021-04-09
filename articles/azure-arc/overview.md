---
title: Azure Arc の概要
description: Azure Arc とは何か、そしてお客様が Azure の他のサービスや機能を使用してハイブリッド リソースの管理とガバナンスを実現するうえでどのように役立つかについて説明します。
ms.date: 03/02/2021
ms.topic: overview
ms.openlocfilehash: 33c9d6ca87c3d8d2d8920ff429902f5876bbdc59
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101650194"
---
# <a name="azure-arc-overview"></a>Azure Arc の概要

現在、企業はますます複雑化する環境の制御と管理に苦労しています。 こうした環境は、複数のデータ センターや複数のクラウド、エッジをまたいで広がっています。 環境とクラウドごとにまとまりのない管理ツールの独自のセットを所有しており、それらを習得して運用しなければなりません。

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

* GitOps を使用して、Git リポジトリから 1 つ以上のクラスターに構成をデプロイする。

*  Azure Policy を使用して、Kubernetes クラスターのコンプライアンスと構成をゼロ タッチで実行する。

* あたかも Azure で実行しているかのように、Kubernetes 環境で Azure データ サービス (具体的には Azure SQL Managed Instance と Azure Database for PostgreSQL Hyperscale) を実行し、アップグレードと更新、セキュリティ、監視などのメリットを活かす。 エラスティック スケールを使用し、Azure への継続的な接続が得られなくても、アプリケーションのダウンタイムを発生させずに更新プログラムを適用する。

* Azure portal、Azure CLI、Azure PowerShell、Azure REST API のどれを使用していても、統合されたエクスペリエンスで Azure Arc 対応リソースを表示する。

## <a name="how-much-does-azure-arc-cost"></a>Azure Arc のコスト

以降、Azure Arc で現在利用できる機能の価格について詳しく取り上げます。

### <a name="arc-enabled-servers"></a>Arc 対応サーバー

次の Azure Arc コントロール プレーンの機能は、追加コストなしで提供されます。

* Azure 管理グループと Azure タグを使用したリソース組織。

* Azure Resource Graph を使用した検索とインデックス作成。

* Azure RBAC とサブスクリプションを使用したアクセスとセキュリティ。

* テンプレートと拡張機能を使用した環境と自動化。

* 更新管理

Arc 対応サーバーで使用されるすべての Azure サービス (Azure Security Center、Azure Monitor など) は、そのサービスの価格で請求されます。 詳細については、[Azure の価格ページ](https://azure.microsoft.com/pricing/)をご覧ください。

### <a name="azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes

Arc 対応 Kubernetes で使用されるすべての Azure サービス (Azure Security Center、Azure Monitor など) は、そのサービスの価格で請求されます。 Azure Arc 対応 Kubernetes 上の構成に対する料金の詳細については、[Azure の価格ページ](https://azure.microsoft.com/pricing/)をご覧ください。

### <a name="azure-arc-enabled-data-services"></a>Azure Arc 対応データ サービス

現在のプレビュー フェーズでは、追加費用なしで Azure Arc 対応データ サービスをご利用いただけます。

## <a name="next-steps"></a>次のステップ

* Arc 対応サーバーの詳細については、次の[概要](./servers/overview.md)を参照してください

* Arc 対応 Kubernetes の詳細については、次の[概要](./kubernetes/overview.md)を参照してください

* Arc 対応データ サービスの詳細については、次の[概要](https://azure.microsoft.com/services/azure-arc/hybrid-data-services/)を参照してください

* [概念実証のジャンプスタート](https://azurearcjumpstart.io/azure_arc_jumpstart/)に関するページから Arc 対応サービスを体験します
