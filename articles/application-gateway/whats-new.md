---
title: Azure Application Gateway の新着情報
description: 最新のリリース ノート、既知の問題、バグの修正、非推奨の機能、予定されている変更点など、Azure Application Gateway の新着情報について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/30/2019
ms.author: victorh
ms.openlocfilehash: 6755825aa123b3d7d1eed23291790ff3dc477ac5
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382483"
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
|その他のメトリック |ご利用のアプリケーション ゲートウェイ v2 SKU を監視するのに役立つ次の新しいメトリックを追加しました。[タイミング関連のメトリック](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics)、バックエンド応答の状態、受信バイト数、送信バイト数、クライアント TLS プロトコル、現在のコンピューティング ユニット。 「[Application Gateway V2 SKU でサポートされるメトリック](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku)」を参照してください。 |2019 年 8 月 |
|WAF カスタム規則 |Applicaiton Gateway WAF_v2 では、カスタム ルールの作成がサポートされるようになりました。 [Application Gateway のカスタム ルール](custom-waf-rules-overview.md)に関する記事を参照してください。 |2019 年 6 月 |
|自動スケーリング、ゾーン冗長性、静的 VIP のサポートの一般公開 |自動スケーリング、ゾーン冗長性、パフォーマンスの向上、静的 VIP、Key Vault、ヘッダーの書き換えをサポートする v2 SKU の一般公開。 [Application Gateway の自動スケーリングのドキュメント](application-gateway-autoscaling-zone-redundant.md)を参照してください。 |2019 年 4 月 |
|Key Vault の統合 |Application Gateway では、HTTPS が有効なリスナーにアタッチされているサーバー証明書用の Key Vault との統合 (パブリック プレビュー段階) がサポートされるようになりました。 「[Key Vault 証明書での SSL 終了](key-vault-certs.md)」を参照してください。 |2019 年 4 月 |
|ヘッダーの CRUD/書き換え     |HTTP ヘッダーを書き換えることができるようになりました。 手順については、「[チュートリアル: アプリケーション ゲートウェイを作成して HTTP ヘッダーを書き換える](tutorial-http-header-rewrite-powershell.md)」を参照してください。|2018 年 12 月|
|WAF 構成と除外リスト     |WAF の構成と誤検知の削減に役立つオプションが追加されました。 詳細については、「[Web アプリケーション ファイアウォール要求サイズ制限と除外リスト](application-gateway-waf-configuration.md)」を参照してください。|2018 年 12 月|
|自動スケーリング、ゾーン冗長性、静的 VIP のサポート      |v2 の SKU には、自動スケーリング、パフォーマンスの向上など、多くの機能強化があります。 詳細については、「[Azure Application Gateway とは](overview.md)」を参照してください。|2018 年 9 月|
|接続のドレイン     |接続のドレインにより、バックエンド プールからメンバーを適切に削除できます。 詳細については、「[接続のドレイン](overview.md#connection-draining)」を参照してください。|2018 年 9 月|
|カスタム エラー ページ     |カスタム エラー ページを使用すると、Web サイトの他のページと同じ形式でエラー ページを作成できます。 この機能を有効にするには、「[Application Gateway のカスタム エラー ページを作成する](custom-error.md)」を参照してください。|2018 年 9 月|
|メトリックの機能強化     |強化されたメトリックを使用して、Application Gateway の状態をよりよく把握することができます。 Application Gateway でメトリックを有効にするには、「[Application Gateway のバックエンドの正常性、診断ログ、およびメトリック](application-gateway-diagnostics.md)」を参照してください。|2018 年 6 月|

## <a name="next-steps"></a>次の手順

Azure Application Gateway の詳細については、「[Azure Application Gateway とは](overview.md)」を参照してください。
