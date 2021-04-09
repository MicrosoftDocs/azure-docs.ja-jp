---
title: Azure Kubernetes Service (AKS) 診断の概要
description: Azure Kubernetes Service の自己診断クラスターについて説明します。
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: yunjchoi
ms.openlocfilehash: e8921152177d3e4534ca9fb48cf209aed6e1b27c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96183364"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Azure Kubernetes Service 診断 (プレビュー) の概要

Azure Kubernetes Service (AKS) クラスターの問題のトラブルシューティングは、クラスターのメンテナンスの重要な部分であり、クラスターでミッションクリティカルなワークロードが実行されている場合は特に重要です。 AKS 診断はインテリジェントな自己診断エクスペリエンスであり、クラスターの問題を特定して解決するために役立ちます。 AKS 診断はクラウドネイティブであり、追加の構成や請求コストなしで使用できます。

この機能は現在パブリック プレビュー段階にあります。

## <a name="open-aks-diagnostics"></a>AKS 診断を開く

AKS 診断にアクセスするには:

- [Azure portal](https://portal.azure.com) で、対象の Kubernetes クラスターに移動します。
- 左側のナビゲーションで **[問題の診断と解決]** をクリックすると、AKS 診断が開きます。
- ホームページ タイル内のキーワードを使用して、クラスターの問題を最もよく説明するカテゴリを選択します。または、問題を最もよく説明するキーワードを検索バーに入力します (例: _Cluster Node Issues_)。

![ホームページ](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>診断レポートの表示

カテゴリをクリックした後、対象のクラスターに固有の診断レポートを表示できます。 クラスターに問題がある場合、状態アイコンを使用して診断レポートをインテリジェントに呼び出すことができます。 **[More Info]\(詳細情報\)** をクリックして各トピックをドリルダウンし、問題の詳細な説明、推奨されるアクション、役に立つドキュメントへのリンク、関連するメトリック、ログ データを表示することができます。 診断レポートは、さまざまなチェックを実行した後に、クラスターの現在の状態に基づいてインテリジェントに生成されます。 診断レポートは、クラスターの問題を特定し、問題解決に向けた次の手順を調べるために役立つツールです。

![診断レポート](./media/concepts-diagnostics/diagnostic-report.png)

![展開された診断レポート](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>クラスター分析情報

**[Cluster Insights]\(クラスター分析情報\)** では、次の診断チェックを利用できます。

### <a name="cluster-node-issues"></a>クラスター ノードの問題

[Cluster Node Issues] (クラスター ノードの問題) では、クラスターの予期しない動作の原因になる可能性があるノード関連の問題をチェックします。

- ノード対応性の問題
- ノードの障害
- リソース不足
- ノードの IP 構成の欠落
- ノード CNI の障害
- ノードが見つからない
- ノードの電源がオフ
- ノード認証エラー
- ノードの kube-proxy が古い

### <a name="create-read-update--delete-operations"></a>作成、読み取り、更新、削除操作

[CRUD Operations] (CRUD 操作) では、クラスターで問題の原因になる可能性がある CRUD 操作をチェックします。

- 使用中サブネットの削除操作エラー
- ネットワーク セキュリティ グループの削除操作エラー
- 使用中ルート テーブルの削除操作エラー
- 被参照リソースのプロビジョニング エラー
- パブリック IP アドレスの削除操作エラー
- デプロイ クォータが原因のデプロイ失敗
- 組織のポリシーが原因の操作エラー
- サブスクリプション未登録
- VM 拡張機能のプロビジョニング エラー
- サブネット容量
- クォータ超過エラー

### <a name="identity-and-security-management"></a>ID とセキュリティ管理

ID とセキュリティ管理では、クラスターとの通信を妨げる可能性がある認証と承認のエラーを検出します。

- ノード承認エラー
- 401 エラー
- 403 エラー

## <a name="next-steps"></a>次のステップ

[AKS Periscope](https://aka.ms/aksperiscope) を使用してログを収集すると、クラスターの問題のさらなるトラブルシューティングに役立ちます。

AKS Day-2 Operations ガイドの[「トリアージのプラクティス」セクション](/azure/architecture/operator-guides/aks/aks-triage-practices)を参照してください。

質問またはフィードバックは、[UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) でタイトルに "[Diag]" を付けて投稿してください。