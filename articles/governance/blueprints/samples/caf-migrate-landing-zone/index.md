---
title: サンプル - CAF 移行ランディング ゾーン ブループリント - 概要
description: CAF 移行ランディング ゾーン ブループリント サンプルの概要とアーキテクチャ。
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/20/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.custom: fasttrack-new
ms.openlocfilehash: 81bde54c65cf3ed25da634c38b7b99e72a187e00
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002459"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-migration-landing-zone-blueprint-sample"></a>Microsoft Cloud Adoption Framework for Azure 移行ランディング ゾーン ブループリント サンプルの概要

Microsoft Cloud Adoption Framework for Azure (CAF) 移行ランディング ゾーン ブループリントは、最初のワークロードの移行の設定とクラウド資産の管理に役立つ、CAF と連携した一連のインフラストラクチャです。

[CAF 基盤](../caf-foundation/index.md)ブループリント サンプルによりこのサンプルは拡張されます。

## <a name="architecture"></a>アーキテクチャ

CAF 移行ランディング ゾーン ブループリント サンプルは、仮想マシンを移行するためのサブスクリプションを準備する際に組織で使用できる基本インフラストラクチャ リソースを Azure にデプロイします。 また、クラウド資産を管理するために必要なガバナンス コントロールの導入にも役立ちます。 このサンプルは、組織が自信を持って Azure を使い始めるために役立つリソース、ポリシー、テンプレートをデプロイして適用します。

![CAF 移行ランディング ゾーン、インストール内容の説明図 (初期のランディング ゾーンに関する CAF ガイダンスの一部) ](../../media/caf-blueprints/caf-migration-landing-zone-architecture.png)

この環境は、セキュリティで保護され、完全に監視されたエンタープライズ対応のガバナンスを提供するために使用される複数の Azure サービスで構成されます。 この環境は、以下で構成されます。

- [Azure Key Vault](../../../../key-vault/key-vault-overview.md) インスタンス。共有サービス環境にデプロイされた証明書、キー、シークレット用に使用されるシークレットをホストします。
- [Log Analytics](../../../../azure-monitor/overview.md)。移行の開始以降、すべてのアクションとサービス ログが中心的な場所に確保されるようにデプロイされます。
- [Azure Security Center](../../../../security-center/security-center-intro.md) (標準バージョン)。移行されたワークロードを脅威から保護します。
- [Azure Virtual Network](../../../../virtual-network/virtual-networks-overview.md)。仮想マシン用の分離されたネットワークとサブネットを提供します。
- [Azure Migrate プロジェクト](../../../..//migrate/migrate-overview.md)。検出と評価のために使用されます。 サーバーの評価、サーバーの移行、データベースの評価、データベースの移行のためのツールが今後追加される予定です。  


これらの要素はすべて、「[Azure アーキテクチャ センター - 参照アーキテクチャ](/azure/architecture/reference-architectures/)」で公開されている実証済みのプラクティスに従っています。

> [!NOTE]
> CAF 移行ブループリントは、ワークロードのランディング ゾーンを示します。 この基本アーキテクチャの上で仮想マシンまたはデータベースの評価と移行を優先的に行う必要もあります。

詳細については、[Microsoft Cloud Adoption Framework for Azure の移行](/azure/architecture/cloud-adoption/migrate/)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

CAF 移行ランディング ゾーン ブループリント サンプルの概要とアーキテクチャを確認しました。

> [!div class="nextstepaction"]
>  [CAF 移行ランディング ゾーン ブループリント - デプロイ手順](./deploy.md)

ブループリントとその使用方法に関するその他の記事:

- [ブループリントのライフサイクル](../../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../../how-to/update-existing-assignments.md)方法を参照する。