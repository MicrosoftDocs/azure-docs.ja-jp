---
title: Azure portal の URL をセーフリストに追加する | Microsoft Docs
description: これらの URL をプロキシ サーバー バイパスに追加して、Azure portal やそのサービスと通信を行います
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 09/13/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 9618d99495e65231de22eb719355e2ca75a17600
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71218042"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>ファイアウォールまたはプロキシ サーバーのセーフリストに Azure portal の URL を追加する

ローカルエリア ネットワークまたはワイドエリア ネットワークと Azure クラウドとの間に良好なパフォーマンスと接続性を確保するために、Azure portal の URL に対するセキュリティ制限をバイパスするようにオンプレミス セキュリティ デバイスを構成します。 ユーザーのインターネット アクセスに対するセキュリティと制御を促すために、プロキシ サーバーやファイアウォールなどのデバイスがネットワーク管理者によってデプロイされていることが少なくありません。 ところが、ユーザーの保護を意図したルールによって、ビジネスに関連した正当なインターネット トラフィック、たとえばユーザーと Azure との間の通信がブロックされたり、低速化したりすることがあります。 ご利用のネットワークと Azure portal およびそのサービスとの間の接続性を最適化するために、Azure portal の URL をセーフリストに追加することをお勧めします。

## <a name="azure-portal-urls-for-proxy-bypass"></a>プロキシのバイパス対象となる Azure portal の URL

Azure portal 用にセーフリストに追加する URL エンドポイントは、組織がデプロイされている Azure クラウドに固有のものです。 ご利用のクラウドを選択し、ご利用のプロキシ サーバーまたはファイアウォールに次の一連の URL を追加して、それらのエンドポイントへのネットワーク トラフィックを許可し、制限をバイパスします。

#### <a name="public-cloudtabpublic-cloud"></a>[パブリック クラウド](#tab/public-cloud)
```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azureafd.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloudtabus-government-cloud"></a>[米国政府のクラウド](#tab/us-government-cloud)
```
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloudtabchina-government-cloud"></a>[中国政府のクラウド](#tab/china-government-cloud)
```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> これらのエンドポイントへのトラフィックには、標準的な TCP ポートが使用されます (HTTP では 80、HTTPS では 443)。
>
>
## <a name="next-steps"></a>次の手順

IP アドレスをセーフリストに追加するには、 ご利用のクラウドの Microsoft Azure データセンターの IP 範囲のリストをダウンロードします。

* [世界全域](https://www.microsoft.com/download/details.aspx?id=56519)
* [米国政府](https://www.microsoft.com/download/details.aspx?id=57063)
* [ドイツ](https://www.microsoft.com/download/details.aspx?id=57064)
* [中国](https://www.microsoft.com/download/details.aspx?id=57062)

その他の Microsoft サービスでは、さらに別の URL と IP アドレスが接続に使用されます。 Microsoft 365 サービスのネットワーク接続を最適化するには、「[Office 365 のネットワークをセットアップする](/office365/enterprise/set-up-network-for-office-365)」を参照してください。
