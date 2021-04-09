---
title: Azure Defender for Resource Manager - 利点と機能
description: Azure Defender for Resource Manager の利点と機能について学習します。
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 903853f9f37921a68c918d09a94087548b5c562c
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100663"
---
# <a name="introduction-to-azure-defender-for-resource-manager"></a>Azure Defender for Resource Manager の概要

[Azure Resource Manager](../azure-resource-manager/management/overview.md) は、Azure のデプロイおよび管理サービスです。 お使いの Azure アカウント内のリソースを作成、更新、および削除できる管理レイヤーを提供します。 アクセス制御、ロック、タグなどの管理機能を使用して、デプロイ後にリソースを保護および整理します。

このクラウド管理レイヤーは、すべてのクラウド リソースに接続される非常に重要なサービスです。 それだけに攻撃者の標的になるリスクもはらんでいます。 したがって、セキュリティ運用チームでリソース管理レイヤーを厳しく監視することをお勧めします。 

組織内のリソース管理操作は、その手段が Azure portal や Azure REST API、Azure CLI であれ、その他 Azure のプログラマティック クライアントであれ、Azure Defender for Resource Manager によって自動的に監視されます。 Azure Defender は高度なセキュリティ分析を実行して脅威を検出し、不審なアクティビティについてのアラートを生成します。

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|プレビュー<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|価格:|**Azure Defender for Resource Manager** の課金については、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」をご覧ください|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![No](./media/icons/no-icon.png) ナショナル/ソブリン (US Gov、China Gov、その他の Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-resource-manager"></a>Azure Defender for Resource Manager の利点

Azure Defender for Resource Manager は、次のような問題からの保護対策となります。

- **不審なリソース管理操作**: 不審な IP アドレスからの操作、マルウェア対策ソフトの無効化、VM の拡張機能で実行される不審なスクリプトなど
- **悪用ツールキット**: Microburst、PowerZure など
- **侵入拡大**: Azure 管理レイヤーから Azure リソースのデータ プレーンへの侵入

:::image type="content" source="media/defender-for-resource-manager-introduction/consistent-management-layer-with-defender.png" alt-text="Azure Resource Manager の概要図":::

Azure Defender for Resource Manager によって提供されるアラートの全一覧は、[アラートのリファレンス ページ](alerts-reference.md#alerts-resourcemanager)に掲載されています。


 ## <a name="how-to-investigate-alerts-from-azure-defender-for-resource-manager"></a>Azure Defender for Resource Manager からアラートを調査する方法

Azure Defender for Resource Manager からのセキュリティ アラートの基になっているのは、Azure Resource Manager の操作を監視することによって検出された脅威です。 Azure Defender では、Azure Resource Manager の内部ログ ソースに加え、Azure のアクティビティ ログが使用されます。アクティビティ ログは Azure のプラットフォーム ログであり、サブスクリプションレベルのイベントについての分析情報が得られます。

詳細については、「[Azure アクティビティ ログ](../azure-monitor/essentials/activity-log.md)」を参照してください。

Azure Defender for Resource Manager からのセキュリティ アラートを調査するには、次の手順に従います。

1. Azure のアクティビティ ログを開きます。

    :::image type="content" source="media/defender-for-resource-manager-introduction/opening-azure-activity-log.png" alt-text="Azure のアクティビティ ログを開く方法":::

1. イベントに次のフィルターを適用します。
    - アラート内で言及されているサブスクリプション
    - 検出されたアクティビティの期間
    - 関連するユーザー アカウント (該当する場合)

1. 不審なアクティビティを探します。

> [!TIP]
> より高度で多彩な調査環境を確立したければ、「[Azure アクティビティ ログからデータを接続する](../sentinel/connect-azure-activity.md)」の説明に従って、Azure のアクティビティ ログを Azure Sentinel にストリーム配信してください。



## <a name="next-steps"></a>次のステップ

この記事では、Azure Defender for Resource Manager について学習しました。 関連資料については、次の記事をご覧ください。 

- セキュリティ アラートは、Security Center によって生成される場合もあれば、Security Center がさまざまなセキュリティ製品から受信する場合もあります。 それらすべてのアラートを Azure Sentinel、サードパーティの SIEM、またはその他の外部ツールにエクスポートする場合は、[SIEM へのアラートのエクスポート](continuous-export.md)に関するページの手順に従ってください。

- > [!div class="nextstepaction"]
    > [Azure Defender を有効にする](enable-azure-defender.md)