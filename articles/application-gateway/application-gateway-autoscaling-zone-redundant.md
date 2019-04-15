---
title: Azure での自動スケーリングとゾーン冗長 Application Gateway (パブリック プレビュー)
description: この記事では、自動スケーリング機能とゾーン冗長機能が含まれる Azure アプリケーション v2 SKU について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 3/6/2019
ms.author: victorh
ms.openlocfilehash: f7d1c5bc54d909d1a948123839d95e1ee1158a5c
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58444816"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>自動スケールとゾーン冗長 Application Gateway (パブリック プレビュー)

Application Gateway と Web アプリケーション ファイアウォール (WAF) は、現在、新しい v2 SKU でパブリック プレビューとして使用できます。この SKU では、パフォーマンスの向上が提供され、自動スケール、ゾーン冗長性、静的 VIP のサポートなどの重要な新機能のサポートが追加されます。 一般公開された SKU の既存機能は、既知の制限のセクションで示されているいくつかの例外を除き、新しい v2 SKU でも引き続きサポートされます。 新しい v2 SKU には、次の拡張機能が含まれます。

- **自動スケール**:自動スケーリング SKU の下での Application Gateway または WAF のデプロイは、トラフィック負荷パターンの変化に基づいてスケールアップまたはスケールダウンできます。 また、自動スケールにより、プロビジョニングの間にデプロイのサイズまたはインスタンスの数を選択する必要がなくなります。 この SKU では真のエラスティック性が提供されます。 新しい SKU では、Application Gateway は固定容量モード (自動スケーリング無効) と自動スケーリング有効モードの両方で動作できます。 固定容量モードは、ワークロードが一定で予測可能なシナリオに便利です。 自動スケール モードは、アプリケーション トラフィックの変動が大きいアプリケーションで役に立ちます。

- **ゾーン冗長性**: Application Gateway または WAF のデプロイは複数の可用性ゾーンを対象にできるため、Traffic Manager を使ってゾーンごとに個別に Application Gateway のインスタンスをプロビジョニングして稼働させる必要はありません。 Application Gateway のインスタンスをデプロイする単一のゾーンまたは複数のゾーンを選択できるので、ゾーンの障害の回復性が保証されます。 アプリケーションのバックエンド プールも、複数の可用性ゾーンに同様に分散できます。
- **パフォーマンスの向上**: 自動スケーリング SKU では、一般に利用可能な SKU と比較して、SSL オフロードのパフォーマンスが最大で 5 倍になります。
- **デプロイと更新の時間の短縮**: 自動スケール SKU では、一般に使用可能な SKU と比較して、デプロイと更新の時間が短縮されます。
- **静的 VIP**: アプリケーション ゲートウェイの VIP は、静的な VIP の種類だけをサポートするようになります。 これにより、アプリケーション ゲートウェイに関連付けられた VIP は、再起動後でも変化しません。

> [!IMPORTANT]
> 自動スケールおよびゾーン冗長アプリケーション ゲートウェイの SKU は、現在、パブリック プレビュー段階です。 このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>v1 SKU と v2 SKU の機能比較

次の表では、各 SKU で使用できる機能を比較しています。

|                                                   | v1 SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| 自動スケール                                       |          | &#x2713; |
| ゾーン冗長性                                   |          | &#x2713; |
| &nbsp;静的 VIP&nbsp;&nbsp;                      |          | &#x2713; |
| URL ベースのルーティング                                 | &#x2713; | &#x2713; |
| 複数サイトのホスティング                             | &#x2713; | &#x2713; |
| トラフィック リダイレクト                               | &#x2713; | &#x2713; |
| Web アプリケーション ファイアウォール (WAF)                    | &#x2713; | &#x2713; |
| Secure Sockets Layer (SSL) の終了            | &#x2713; | &#x2713; |
| エンドツーエンド SSL 暗号化                         | &#x2713; | &#x2713; |
| セッション アフィニティ                                  | &#x2713; | &#x2713; |
| カスタム エラー ページ                                | &#x2713; | &#x2713; |
| HTTP ヘッダーを書き換える                           |          | &#x2713; |
| WebSocket のサポート                                 | &#x2713; | &#x2713; |
| HTTP/2 のサポート                                    | &#x2713; | &#x2713; |
| 接続のドレイン                               | &#x2713; | &#x2713; |
| Azure Kubernetes Service (AKS) のイングレス コントローラー |          | &#x2713; |

## <a name="supported-regions"></a>サポートされているリージョン

自動スケーリング SKU は、次のリージョンで利用できます:米国中北部、米国中南部、米国西部、米国西部 2、米国東部、米国東部 2、米国中部、北ヨーロッパ、西ヨーロッパ、東南アジア、フランス中部、英国西部、東日本、西日本。

## <a name="pricing"></a>価格

プレビューの期間中は、料金はかかりません。 キー コンテナーや仮想マシンなど、アプリケーション ゲートウェイ以外のリソースに対しては課金されます。

## <a name="known-issues-and-limitations"></a>既知の問題と制限

|問題|詳細|
|--|--|
|認証証明書|サポートされていません。<br>詳細については、「[Application Gateway でのエンド ツー エンド SSL の概要](ssl-overview.md#end-to-end-ssl-with-the-v2-sku)」を参照してください。|
|同じサブネット上の Standard_v2 と Standard Application Gateway の混在|サポートされていません|
|Application Gateway サブネット上のユーザー定義ルート (UDR)|サポートされていません|
|受信ポート範囲の NSG| - Standard_v2 SKU では 65200 ～ 65535<br>- Standard SKU では 65503 ～ 65534<br>詳細については、[FAQ](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet) をご覧ください。|
|Azure 診断でのパフォーマンス ログ|サポートされていません。<br>Azure メトリックを使用する必要があります。|
|課金|現在は請求されません。|
|FIPS モード|現在はサポートされていません。|
|ILB のみモード|現在これはサポートされていません。 パブリック モードと ILB モードがまとめてサポートされます。|
|Netwatcher 統合|パブリック プレビューではサポートされていません。|

## <a name="next-steps"></a>次の手順
- [Azure PowerShell を使用して、自動スケーリングの予約済み IP アドレスを持つゾーン冗長アプリケーション ゲートウェイを作成します](tutorial-autoscale-ps.md)
- [Application Gateway](overview.md) の詳細を参照します。
- [Azure Firewall](../firewall/overview.md) の詳細を参照します。
