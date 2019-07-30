---
title: Azure portal の URL をセーフリストに追加する | Microsoft Docs
description: これらの URL をプロキシ サーバー バイパスに追加して、Azure portal やそのサービスと通信を行います
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 06/13/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 87ec600a2f6c4a560ec7cbb064b561fa76e2b615
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304831"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>ファイアウォールまたはプロキシ サーバーのセーフリストに Azure portal の URL を追加する

ローカルエリア ネットワークまたはワイドエリア ネットワークと Azure クラウドとの間に良好なパフォーマンスと接続性を確保するために、Azure portal の URL に対するセキュリティ制限をバイパスするようにオンプレミス セキュリティ デバイスを構成します。 ユーザーのインターネット アクセスに対するセキュリティと制御を促すために、プロキシ サーバーやファイアウォールなどのデバイスがネットワーク管理者によってデプロイされていることが少なくありません。 ところが、ユーザーの保護を意図したルールによって、ビジネスに関連した正当なインターネット トラフィック、たとえばユーザーと Azure との間の通信がブロックされたり、低速化したりすることがあります。 ご利用のネットワークと Azure portal およびそのサービスとの間の接続性を最適化するために、Azure portal の URL をセーフリストに追加することをお勧めします。

## <a name="azure-portal-urls-for-proxy-bypass"></a>プロキシのバイパス対象となる Azure portal の URL

ご利用のプロキシ サーバーまたはファイアウォールに次の一連の URL を追加して、それらのエンドポイントへのネットワーク トラフィックを許可し、制限をバイパスします。

* *.aadcdn.microsoftonline-p.com
* *.aimon.applicationinsights.io
* *.azure.com
* *.azuredatalakestore.net
* *.azureedge.net
* *.exp.azure.com
* *.ext.azure.com
* *.gfx.ms
* *.account.microsoft.com
* *.hosting.portal.azure.net
* *.marketplaceapi.microsoft.com
* *.microsoftonline.com
* *.msauth.net
* *.msftauth.net
* *.portal.azure.com
* *.portalext.visualstudio.com
* *.sts.microsoft.com
* *.vortex.data.microsoft.com
* *.vscommerce.visualstudio.com
* *.vssps.visualstudio.com
* *.wpc.azureedge.net

> [!NOTE]
> これらのエンドポイントへのトラフィックには、標準的な TCP ポートが使用されます (HTTP では 80、HTTPS では 443)。
>
>
## <a name="next-steps"></a>次の手順

* IP アドレスをセーフリストに追加するには、 [Microsoft Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)のリストをダウンロードします。
* その他の Microsoft サービスでは、さらに別の URL と IP アドレスが接続に使用されます。 Microsoft 365 サービスのネットワーク接続を最適化するには、「[Office 365 のネットワークをセットアップする](/office365/enterprise/set-up-network-for-office-365)」を参照してください。
