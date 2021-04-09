---
title: Azure CDN リソースの正常性の監視| Microsoft Docs
description: Azure Resource Health を使用して、Azure CDN リソースの正常性を監視する方法について説明します。
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: bf23bd89-35b2-4aca-ac7f-68ee02953f31
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 81c92b6ef5bdf89391364bc238f55200eb3679f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92778159"
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Azure CDN リソースの正常性の監視
  
Azure CDN リソース正常性は、[Azure Resource Health](../service-health/resource-health-overview.md) のサブセットです。  Azure Resource Health を使用すると、CDN リソースの正常性を監視し、問題のトラブルシューティングを行うための実践的なガイダンスを得ることができます。

>[!IMPORTANT] 
>現在 CDN リソース正常性で考慮されるのは、グローバルな CDN 配信および API 機能の正常性だけです。  Azure CDN リソース正常性では、個別の CDN エンドポイントは検証されません。
>
>Azure CDN リソース正常性に情報を提供する信号には、最大 15 分の遅れが生じる可能性があります。

## <a name="how-to-find-azure-cdn-resource-health"></a>Azure CDN リソース正常性を検索する方法

1. [Azure Portal](https://portal.azure.com) で、CDN プロファイルに移動します。

2. **[Settings]** をクリックします。

    ![[設定] ボタン](./media/cdn-resource-health/cdn-profile-settings.png)

3. *[サポート + トラブルシューティング]* で、**[リソース正常性]** をクリックします。

    ![CDN リソース正常性](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>*[ヘルプとサポート]* ブレードの *[リソース正常性]* タイルでも CDN リソースを確認できます。  *[ヘルプとサポート]* にすばやくアクセスするには、ポータルの右上にある、丸で囲まれた **?** をクリックします。
>
> ![ヘルプとサポート](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Azure CDN 固有のメッセージ

Azure CDN リソース正常性に関連する状態を次に示します。

|Message | 推奨される操作 |
|---|---|
|1 つまたは複数の CDN エンドポイントが停止または削除されたか、正しく構成されなかった可能性があります | 1 つまたは複数の CDN エンドポイントが停止または削除されたか、正しく構成されなかった可能性があります。|
|申し訳ありません。CDN 管理サービスは現在利用できません | ここで状態の更新をもう一度確認してください。想定された時間が経過しても問題が解決しない場合は、サポートにお問い合わせください。|
|申し訳ありません。CDN プロバイダーの継続的な問題によって、CDN エンドポイントが影響を受けている可能性があります | ここで状態の更新をもう一度確認してください。発信元および CDN エンドポイントをテストする方法を確認するには、トラブルシューティング ツールを使用します。想定された時間が経過しても問題が解決しない場合は、サポートにお問い合わせください。 |
|申し訳ありません。CDN エンドポイントの構成変更の反映が遅れています | ここで状態の更新をもう一度確認してください。想定された時間で一部の構成変更が反映されない場合は、サポートにお問い合わせください。|
|申し訳ありません。補助ポータルの読み込みで問題が発生しています | ここで状態の更新をもう一度確認してください。想定された時間が経過しても問題が解決しない場合は、サポートにお問い合わせください。|
申し訳ありません。CDN プロバイダーの一部で問題が発生しています | ここで状態の更新をもう一度確認してください。想定された時間が経過しても問題が解決しない場合は、サポートにお問い合わせください。 |

## <a name="next-steps"></a>次のステップ

- [Azure Resource Health の概要を確認します](../service-health/resource-health-overview.md)
- [CDN 圧縮に関する問題のトラブルシューティングを行います](./cdn-troubleshoot-compression.md)
- [404 エラーに関する問題のトラブルシューティングを行います](./cdn-troubleshoot-endpoint.md)