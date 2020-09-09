---
title: Azure Firewall とは
description: Azure Firewall は、Azure Virtual Network リソースを保護するクラウドベースのマネージド ネットワーク セキュリティ サービスです。
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc, contperfq1
ms.date: 08/25/2020
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: 914f267edd5a8168fc11af7186e322c306718a4a
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88852634"
---
# <a name="what-is-azure-firewall"></a>Azure Firewall とは

![ICSA 認定資格](media/overview/icsa-cert-firewall-small.png)

Azure Firewall は、Azure Virtual Network リソースを保護するクラウドベースのマネージド ネットワーク セキュリティ サービスです。 組み込みの高可用性とクラウドの無制限のスケーラビリティを備えた、完全にステートフルなサービスとしてのファイアウォールです。

![ファイアウォールの概要](media/overview/firewall-threat.png)

サブスクリプションと仮想ネットワークをまたいでアプリケーションとネットワークの接続ポリシーを一元的に作成、適用、記録できます。 Azure Firewall では、外部のファイアウォールが仮想ネットワークからのトラフィックを識別できるよう、仮想ネットワーク リソースに静的パブリック IP アドレスが使用されます。  サービスはログ記録と分析を行うために Azure Monitor と完全に統合されます。

## <a name="features"></a>特徴

Azure Firewall の機能の詳細については、「[Azure Firewall の機能](features.md)」を参照してください。

## <a name="known-issues"></a>既知の問題

Azure Firewall には、次の既知の問題があります。

|問題  |説明  |対応策  |
|---------|---------|---------|
TCP/UDP 以外のプロトコル (ICMP など) に関するネットワーク フィルタリング規則が、インターネットへのトラフィックで機能しない|TCP/UDP 以外のプロトコルに関するネットワーク フィルタリング規則は、パブリック IP アドレスへの SNAT で機能しません。 TCP/UDP 以外のプロトコルは、スポーク サブネットと VNet との間でサポートされます。|Azure Firewall では Standard Load Balancer が使用されます。[現在 Standard Load Balancer では、IP プロトコルの SNAT はサポートされていません](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)。 Microsoft は、将来のリリースでこのシナリオに対応できるよう方法を模索しています。|
|PowerShell と CLI では ICMP がサポートされない|Azure PowerShell と CLI は、ネットワーク ルールの有効なプロトコルとして ICMP をサポートしていません。|それでも、ポータルと REST API を介して ICMP をプロトコルとして使用することが可能です。 近いうちに PowerShell と CLI に ICMP を追加するよう取り組んでいます。|
|FQDN タグで port:protocol を設定する必要がある|FQDN タグを使用するアプリケーション ルールには、port:protocol の定義が必要です。|port:protocol 値として、**https** を使用できます。 FQDN タグの使用時にこのフィールドを省略可能にするため、取り組みを進めています。|
|ファイアウォールを別のリソース グループまたはサブスクリプションへ移動することはサポートされていません|ファイアウォールを別のリソース グループまたはサブスクリプションへ移動することはサポートされていません。|この機能は今後サポートされる予定です。 ファイアウォールを別のリソース グループまたはサブスクリプションに移動するには、現在のインスタンスを削除して、新しいリソース グループまたはサブスクリプション内に再作成する必要があります。|
|脅威インテリジェンス アラートがマスクされることがある|アラートのみのモードに構成されている場合、送信フィルター処理用の宛先 80/443 のネットワーク ルールによって脅威インテリジェンス アラートがマスクされます。|アプリケーション ルールを使用して 80/443 の送信フィルター処理を作成します。 または、脅威インテリジェンス モードを **[Alert and Deny]\(アラートと拒否\)** に変更します。|
|Azure Firewall DNAT がプライベート IP 送信先で機能しない|Azure Firewall DNAT のサポートは、インターネット エグレスまたはイングレスに制限されています。 現在、DNAT はプライベート IP 送信先で機能しません。 たとえば、スポークからスポークです。|これは現在の制限です。|
|最初のパブリック IP の構成を削除できない|Azure Firewall の各パブリック IP アドレスは、"*IP 構成*" に割り当てられています。  最初の IP 構成はファイアウォールのデプロイ中に割り当てられ、通常、それにはファイアウォールのサブネットへの参照も含まれます (テンプレートのデプロイによって別に明示的に構成されていない場合)。 この IP 構成を削除するとファイアウォールの割り当てが解除されるため、削除できません。 ただし、ファイアウォールで使用できるパブリック IP アドレスが他に 1 つ以上ある場合は、この IP 構成に関連付けられているパブリック IP アドレスを変更または削除できます。|これは仕様です。|
|Availability Zones は、デプロイ時にのみ構成できます。|Availability Zones は、デプロイ時にのみ構成できます。 ファイアウォールがデプロイされた後、Availability Zones を構成することはできません。|これは仕様です。|
|受信接続での SNAT|DNAT に加えて、ファイアウォールのパブリック IP アドレスを使用した (受信) 接続は SNAT によっていずれかのファイアウォールのプライベート IP に変換されます。 対称的なルーティングを実現するために、現在このような要件が (アクティブ/アクティブ NVA に対しても) 適用されます。|HTTP/S の元の送信元を保持するには、[XFF](https://en.wikipedia.org/wiki/X-Forwarded-For) ヘッダーを使用することを検討します。 たとえば、ファイアウォールの直前に [Azure Front Door](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) や [Azure Application Gateway](../application-gateway/rewrite-http-headers.md) などのサービスを使用します。 Azure Front Door とチェーンの一部としてファイアウォールに WAF を追加することもできます。
|SQL の FQDN のフィルター処理がプロキシ モードでのみサポートされる (ポート 1433)|Azure SQL Database、Azure SQL Data Warehouse、Azure SQL Managed Instance の場合:<br><br>プレビュー期間中、SQL の FQDN のフィルター処理は、プロキシ モードでのみサポートされます (ポート 1433)。<br><br>Azure SQL IaaS の場合:<br><br>標準以外のポートを使っている場合は、アプリケーション ルールでそれらのポートを指定できます。|リダイレクト モードの SQL (Azure 内から接続する場合の既定) では、代わりに Azure Firewall ネットワーク ルールの一部として SQL サービス タグを使ってアクセスをフィルター処理できます。
|TCP ポート 25 でアウトバウンド トラフィックが許可されない| TCP ポート 25 を使用するアウトバウンド SMTP 接続はブロックされます。 ポート 25 は主に、認証されていないメール配信で使用されます。 仮想マシンでは、これがプラットフォームの既定の動作となります。 詳細については、「[Azure でのアウトバウンド SMTP 接続に関する問題のトラブルシューティング](../virtual-network/troubleshoot-outbound-smtp-connectivity.md)」を参照してください。 ただし、仮想マシンとは異なり、Azure Firewall でこの機能を有効にすることは現在できません。 注: 認証済み SMTP (ポート 587) または 25 以外のポートでの SMTP を許可するには、アプリケーション ルールではなく、必ずネットワーク ルールを構成してください。現時点では、SMTP の検査がサポートされません。|SMTP のトラブルシューティング記事に記載されている推奨される方法に従ってメールを送信してください。 または、送信 SMTP アクセスを必要とする仮想マシンを、ファイアウォールへの既定のルートから除外します。 代わりに、インターネットへの直接のアウトバウンド アクセスを構成します。
|アクティブ FTP がサポートされていない|Azure Firewall では、FTP ポート コマンドを使用した FTP バウンス攻撃から保護するために、アクティブ FTP が無効になっています。|代わりに、パッシブ FTP を使用できます。 その場合も、ファイアウォールで TCP ポート 20 と 21 を明示的に開く必要があります。
|SNAT ポート使用率メトリックに 0% が表示される|Azure Firewall SNAT ポート使用率メトリックに、SNAT ポートが使用されているときでも 0% が表示されることがあります。 この場合、このメトリックがファイアウォールの正常性メトリックの一部として使用されていることで結果が不正確になります。|この問題は修正されており、2020 年 5 月に運用環境に配布される予定です。 ファイアウォールの配置を換えることで問題が解決される場合もありますが、それには一貫性がありません。 修正プログラムが配布されるまでの回避策として、*status=unhealthy* ではなく、*status=degraded* を探す目的でのみ、ファイアウォールの正常性状態を利用してください。 ポート枯渇には *[低下]* と表示されます。 *[異常]* は、ファイアウォールの正常性に影響を与えるメトリックが増える将来のために予約されています。
|強制トンネリングが有効になっている場合、DNAT はサポートされない|強制トンネリングが有効になった状態でデプロイされているファイアウォールは、非対称ルーティングのため、インターネットからの受信アクセスをサポートできません。|これは、非対称ルーティングのための仕様です。 受信接続のリターン パスは、確立された接続が検出されていないオンプレミスのファイアウォールを経由します。
|送信パッシブ FTP が複数のパブリック IP アドレスを持つファイアウォールで機能しない|パッシブ FTP は、コントロールとデータのチャネルに対して異なる接続を確立します。 複数のパブリック IP アドレスを持つファイアウォールは、送信データを送信するときに、ソース IP アドレスとしてパブリック IP アドレスの 1 つをランダムに選択します。 データとコントロールのチャネルで異なるソース IP アドレスが使用されていると、FTP は失敗します。|明示的な SNAT 構成が計画されています。 現時点では、このような場合は、1 つの IP アドレスを使用することを検討してください。|
|NetworkRuleHit メトリックにプロトコル ディメンションがない|ApplicationRuleHit メトリックでは、プロトコルに基づいたフィルター処理が許可されていますが、対応する NetworkRuleHit メトリックにこの機能がありません。|解決策を調査中です。|
|64000 から 65535 の範囲のポートを使用した NAT ルールはサポートされません。|Azure Firewall では、ネットワーク ルールとアプリケーション ルールで 1 から 65535 の範囲の任意のポートを使用できますが、NAT ルールでサポートされるのは、1 から 63999 の範囲のポートのみです。|これは現在の制限です。
|構成の更新に平均 5 分かかる|Azure Firewall 構成の更新は平均で 3 から 5 分かかる場合があり、並列更新はサポートされていません。|解決策を調査中です。|
|Azure Firewall では、HTTPS トラフィックと MSSQL トラフィックのフィルター処理に SNI TLS ヘッダーが使用される|ブラウザーまたはサーバー ソフトウェアが Server Name Indicator (SNI) 拡張機能をサポートしていない場合は、Azure Firewall 経由で接続することがはできません。|ブラウザーまたはサーバー ソフトウェアが SNI をサポートしていない場合は、アプリケーション ルールではなくネットワーク ルールを使用して接続を制御できます。 SNI をサポートするソフトウェアについては、「[Server Name Indication](https://wikipedia.org/wiki/Server_Name_Indication)」を参照してください。|
|カスタム DNS (プレビュー) が強制トンネリングで正しく機能しない|強制トンネリングが有効になっている場合、カスタム DNS (プレビュー) は正しく機能しません。|解決策を調査中です。

## <a name="next-steps"></a>次のステップ

- [チュートリアル:Azure portal を使用して Azure Firewall をデプロイして構成する](tutorial-firewall-deploy-portal.md)
- [テンプレートを使用して Azure Firewall をデプロイする](deploy-template.md)
- [Azure Firewall のテスト環境を作成する](scripts/sample-create-firewall-test.md)
