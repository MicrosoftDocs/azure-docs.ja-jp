---
title: Azure Web アプリケーション ファイアウォールの概要
description: この記事では、Azure Web アプリケーション ファイアウォール (WAF) の概要を説明します
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/18/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 49024c86d09f5cdd9e8b04d5a49f60021660b0c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "79475789"
---
# <a name="what-is-azure-web-application-firewall"></a>Azure Web アプリケーション ファイアウォールとは

Web アプリケーション ファイアウォール (WAF) では、一般的な悪用や脆弱性から Web アプリケーションを一元的に保護します。 Web アプリケーションが、一般的な既知の脆弱性を悪用した悪意のある攻撃の標的になるケースが増えています。 よくある攻撃の例として、SQL インジェクションやクロスサイト スクリプティングが挙げられます。

![WAF の概要](media/overview/wafoverview.png)

アプリケーション コードでのこのような攻撃の阻止は困難です。 アプリケーション トポロジの複数の層で厳格な保守、パッチ適用、監視が必要になる場合があります。 Web アプリケーション ファイアウォールを一元化することで、セキュリティの管理がはるかに簡単になります。 また、WAF を使用すると、アプリケーション管理者にとっては脅威や侵入に対する保護がより確実になります。

WAF のソリューションでは、個々の Web アプリケーションをセキュリティで保護するのではなく、一元的に既知の脆弱性の修正プログラムを適用することで、さらに迅速にセキュリティの脅威に対応できます。

## <a name="supported-service"></a>サポートされるサービス

WAF は Microsoft の Azure Application Gateway、Azure Front Door、Azure Content Delivery Network (CDN) サービスでデプロイすることができます。 Azure CDN の WAF は現在、パブリック プレビューの段階にあります。  WAF には、特定のサービスごとにカスタマイズされた機能があります。 各サービスの WAF 機能の詳細については、各サービスの概要に関するトピックを参照してください。

## <a name="next-steps"></a>次の手順

- Application Gateway 上の Web アプリケーション ファイアウォールの詳細については、「[Application Gateway 上の Web アプリケーション ファイアウォール](./ag/ag-overview.md)」を参照してください。
- Azure Front Door Service 上の Web アプリケーション ファイアウォールの詳細については、[Azure Front Door Service 上の Web アプリケーション ファイアウォール](./afds/afds-overview.md)に関するページを参照してください。
- Azure CDN Service 上の Web アプリケーション ファイアウォールの詳細については、[Azure CDN Service 上の Web アプリケーション ファイアウォール](./cdn/cdn-overview.md)に関するページを参照してください。
