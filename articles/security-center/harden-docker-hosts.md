---
title: Azure Security Center を使用して Docker ホストをセキュリティ強化し、コンテナーを保護する
description: Docker ホストを保護し、CIS Docker ベンチマークに準拠していることを確認する方法
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: b30e08a2739000d2a7ec14a95742f2654e1d2ea1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98916236"
---
# <a name="harden-your-docker-hosts"></a>Docker ホストのセキュリティを強化する

Azure Security Center では、IaaS Linux VM 上、または Docker コンテナーを実行している他の Linux マシン上でホストされているアンマネージド コンテナーが識別されます。 Security Center によって、こうしたコンテナーの構成が継続的に評価されます。 その後、[Center for Internet Security (CIS) Docker Benchmark](https://www.cisecurity.org/benchmark/docker/) と比較されます。

Security Center には CIS Docker Benchmark のルールセット全体が含まれており、コンテナーがいずれかのコントロールを満たしていない場合は警告が表示されます。 不適切な構成が検出されると、Security Center によってセキュリティの推奨事項が生成されます。 Security Center の **推奨事項ページ** を使用して、推奨事項を表示したり、問題を修復したりします。

脆弱性が見つかった場合、1 つの推奨事項内にグループ化されます。

>[!NOTE]
> これらの CIS ベンチマーク チェックは、AKS マネージド インスタンスまたは Databricks マネージド VM では実行されません。

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般提供 (GA)|
|価格:|[Azure Defender for servers](defender-for-servers-introduction.md) が必要|
|必要なロールとアクセス許可:|ホストが接続するワークスペースの **閲覧者**|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![Yes](./media/icons/yes-icon.png) ナショナル/ソブリン (US Gov、China Gov、その他の Gov)|
|||

## <a name="identify-and-remediate-security-vulnerabilities-in-your-docker-configuration"></a>Docker 構成のセキュリティの脆弱性を特定して修復する

1. Azure Security Center メニューから、 **[推奨事項]** ページを開きます。

1. 推奨事項を **[コンテナーのセキュリティ構成の脆弱性を修復する必要があります]** でフィルター処理して、推奨事項を選択します。

    [推奨事項] ページには、影響を受けるリソース (Docker ホスト) が表示されます。 

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-found.png" alt-text="コンテナーのセキュリティ構成の脆弱性を修復するための推奨事項":::

1. 特定のホストで障害が発生した CIS コントロールを表示して修復するには、調査するホストを選択します。 

    > [!TIP]
    > [資産インベントリ] ページで開始し、そこからこの推奨事項に到達した場合は、[推奨事項] ページの **[アクションの実行]** ボタンを選択します。
    >
    > :::image type="content" source="./media/monitor-container-security/host-security-take-action-button.png" alt-text="Log Analytics を起動する [アクションの実行] ボタン":::

    実行の準備が整ったカスタム操作を示した Log Analytics が開きます。 既定のカスタム クエリには、問題を解決するためのガイドラインと共に評価済みの失敗したルールの全一覧が含まれています。

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-in-query.png" alt-text="失敗したすべての CIS コントロールを表示するクエリを含む Log Analytics ページ":::

1. 必要に応じて、クエリ パラメーターを調整します。

1. コマンドが適切であり、ホストの準備ができていることを確認したら、 **[実行]** を選択します。


## <a name="next-steps"></a>次のステップ

Docker のセキュリティ強化は、Security Center のコンテナーのセキュリティ機能の 1 つの側面にすぎません。 

[Security Center のコンテナーのセキュリティ](container-security.md)について詳しく確認します。