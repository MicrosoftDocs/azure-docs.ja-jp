---
title: Azure Application Gateway の新着情報
description: 最新のリリース ノート、既知の問題、バグの修正、非推奨の機能、予定されている変更点など、Azure Application Gateway の新着情報について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/1/2019
ms.author: victorh
ms.openlocfilehash: f686c8ac53db2d128cf5bb20f252c547348e5ac7
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2019
ms.locfileid: "58863102"
---
# <a name="whats-new-in-azure-application-gateway"></a>Azure Application Gateway の新着情報

Azure Application Gateway は、継続的に更新されます。 常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

- 最新のリリース
- 既知の問題
- バグの修正
- 非推奨の機能

## <a name="new-features"></a>新機能

|機能  |説明  |追加日  |
|---------|---------|---------|
|ヘッダーの CRUD/書き換え     |HTTP ヘッダーを書き換えることができるようになりました。 手順については、「[チュートリアル: アプリケーション ゲートウェイを作成して HTTP ヘッダーを書き換える](tutorial-http-header-rewrite-powershell.md)」を参照してください。|2018 年 12 月|
|WAF 構成と除外リスト     |WAF の構成と誤検知の削減に役立つオプションが追加されました。 詳細については、「[Web アプリケーション ファイアウォール要求サイズ制限と除外リスト](application-gateway-waf-configuration.md)」を参照してください。|2018 年 12 月|
|自動スケーリング、ゾーン冗長性、静的 VIP のサポートのプレビュー     |v2 の SKU には、自動スケーリング、パフォーマンスの向上など、多くの機能強化があります。 詳細については、「[Azure Application Gateway とは](overview.md#autoscaling-public-preview)」を参照してください。|2018 年 9 月|
|接続のドレイン     |接続のドレインにより、バックエンド プールからメンバーを適切に削除できます。 詳細については、「[接続のドレイン](overview.md#connection-draining)」を参照してください。|2018 年 9 月|
|カスタム エラー ページ     |カスタム エラー ページを使用すると、Web サイトの他のページと同じ形式でエラー ページを作成できます。 この機能を有効にするには、「[Application Gateway のカスタム エラー ページを作成する](custom-error.md)」を参照してください。|2018 年 9 月|
|メトリックの機能強化     |強化されたメトリックを使用して、Application Gateway の状態をよりよく把握することができます。 Application Gateway でメトリックを有効にするには、「[Application Gateway のバックエンドの正常性、診断ログ、およびメトリック](application-gateway-diagnostics.md)」を参照してください。|2018 年 6 月|

## <a name="known-issues"></a>既知の問題

- [v2 SKU の既知の問題](application-gateway-autoscaling-zone-redundant.md#known-issues-and-limitations)

## <a name="next-steps"></a>次の手順

Azure Application Gateway の詳細については、「[Azure Application Gateway とは](overview.md)」を参照してください。