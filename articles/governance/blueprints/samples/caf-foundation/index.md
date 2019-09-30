---
title: サンプル - CAF 基盤ブループリント - 概要
description: CAF 基盤ブループリント サンプルの概要とアーキテクチャ。
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/20/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.custom: fasttrack-new
ms.openlocfilehash: aa25bbb54893fa84bb345a317850df4125faed25
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003301"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Microsoft Cloud Adoption Framework for Azure 基盤ブループリント サンプルの概要

Microsoft Cloud Adoption Framework for Azure (CAF) 基盤ブループリントは、最初の運用環境グレードの Azure アプリケーションに必要な一連のコア インフラストラクチャ リソースとポリシー コントロールをデプロイします。 この基盤ブループリントは、CAF の推奨パターンに基づいています。

## <a name="architecture"></a>アーキテクチャ

CAF 基盤ブループリント サンプルは、クラウド資産を管理するために必要な基本コントロールを作成する際に組織で使用することが推奨されているインフラストラクチャ リソースを Azure にデプロイします。 このサンプルは、組織が自信を持って Azure を使い始めるために役立つリソース、ポリシー、テンプレートをデプロイして適用します。

![CAF 基盤、インストール内容の説明図 (Azure を使い始めるための基盤の作成に関する CAF ガイダンスの一部)](../../media/caf-blueprints/caf-foundation-architecture.png)

この実装には、セキュリティで保護され、完全に監視されたエンタープライズ対応の基盤を提供するための、複数の Azure サービスが組み込まれています。 この環境は、以下で構成されます。

- [Azure Key Vault](../../../../key-vault/key-vault-overview.md) インスタンス。共有サービス環境にデプロイされた VM 用に使用されるシークレットをホストします。
- [Log Analytics](../../../../azure-monitor/overview.md)。診断ログに使用する [Storage アカウント](../../../../storage/common/storage-introduction.md)への安全なデプロイを開始した時点から、すべてのアクションとサービス ログが中心的な場所に確保されるようにデプロイされます。
- [Azure Security Center](../../../../security-center/security-center-intro.md) (標準バージョン)。移行されたワークロードを脅威から保護します。
- また、このブループリントでは、以下に関する [Azure ポリシー](../../../policy/overview.md)の定義とデプロイも行われます。 
  - リソース グループに適用されるタグ付け (CostCenter)
  - リソースを CostCenter タグと共にリソース グループ内に追加する
  - リソースとリソース グループに許可されている Azure リージョン
  - 許可されている Storage アカウントの SKU (デプロイ時に選択)
  - 許可されている Azure VM SKU (デプロイ時に選択)
  - Network Watch のデプロイを要求する 
  - Azure Storage アカウントのセキュリティで保護された転送の暗号化を要求する
  - リソースを種類ごとに拒否する (デプロイ時に選択)  
- イニシアティブ
  - Azure Security Center での監視を有効にする (89 ポリシー)

これらの要素はすべて、「[Azure アーキテクチャ センター - 参照アーキテクチャ](/azure/architecture/reference-architectures/)」で公開されている実証済みのプラクティスに従っています。

> [!NOTE]
> CAF 基盤は、ワークロードの基本アーキテクチャを示します。
> この基本アーキテクチャに従ってワークロードをデプロイする必要があります。

詳細については、[Microsoft Cloud Adoption Framework for Azure の準備](/azure/architecture/cloud-adoption/ready/azure-readiness-guide/govern-org-compliance?tabs=AzurePolicy)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

CAF 基盤ブループリント サンプルの概要とアーキテクチャを確認しました。

> [!div class="nextstepaction"]
>  [CAF 基盤ブループリント - デプロイ手順](./deploy.md)

ブループリントとその使用方法に関するその他の記事:

- [ブループリントのライフサイクル](../../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../../how-to/update-existing-assignments.md)方法を参照する。