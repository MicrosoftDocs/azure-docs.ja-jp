---
title: リモートでの作業：リモート作業でのネットワーク仮想アプライアンス (NVA) に関する考慮事項 | Azure VPN Gateway
description: この記事は、COVID-19 の世界流行中に Azure でネットワーク仮想アプライアンス (NVA) を使用する際に考慮すべき事項を理解するのに役立ちます。
services: vpn-gateway
author: scottnap
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: scottnap
ms.openlocfilehash: 70b5732e1293e35127a19fbe736d8562056a870b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96499681"
---
# <a name="working-remotely-network-virtual-appliance-nva-considerations-for-remote-work"></a>リモートでの作業：リモート作業でのネットワーク仮想アプライアンス (NVA) に関する考慮事項

>[!NOTE]
>この記事では、ネットワーク仮想アプライアンス、Azure、Microsoft ネットワーク、Azure パートナーのエコシステムを活用してリモートで作業し、COVID-19 危機がもたらすネットワークの問題を軽減する方法について説明します。
>

一部の Azure カスタマーは、Azure Marketplace からサードパーティのネットワーク仮想アプライアンス (NVA) を利用して、COVID-19 の世界流行中に在宅勤務を行う従業員に向けてポイント対サイト VPN などの重要なサービスを提供しています。 この記事では、Azure で NVA を活用してリモートアクセス ソリューションを提供する際に考慮すべき高水準ガイダンスの概要を説明します。

## <a name="nva-performance-considerations"></a>NVA パフォーマンスに関する考慮事項

Azure Marketplace のすべての主要な NVA ベンダーは、ソリューションをデプロイする際に使用する VM のサイズとインスタンスの数に関するレコメンデーションを用意している必要があります。  ほぼすべての NVA ベンダーにおいて、特定リージョンで利用可能な任意のサイズの選択ができますが、その際には、Azure VM インスタンスのサイズに関するベンダーのレコメンデーションに従うことが非常に重要です。その理由は、これらのレコメンデーションが、ベンダーが Azure でパフォーマンス テストを実施した際の VM サイズだからです。  

### <a name="consider-the-following"></a>以下の点を考慮してください。

- **容量および同時接続ユーザー数** - 各接続ユーザーが1つの暗号化された (IPSec または SSL VPN) トンネルを作成するため、ポイント対サイト VPN のユーザーにとって、この数字は特に重要です。  
- **集計スループット** - リモートアクセスの提供を受けるユーザー数に対応するために必要となる合計帯域幅です。
- **必要な VM サイズ** - NVA ベンダーの推奨する VM サイズを常に使用する必要があります。  ポイント対サイト VPN に関しては、多数のユーザーが接続同時を行う場合には、[Dv2 や DSv2 シリーズ](../virtual-machines/dv2-dsv2-series.md "Dv2 および Dsv2 シリーズ") VM など、より大きな VM サイズを使用する必要があります。 より多くの vCPU を備えたこれらの VM は、より多くの VPN セッションを同時に処理できます。  仮想コアの数が増えるだけでなく、Azure の VM サイズが大きいほど、VM のサイズが小さい場合よりも多くの集計帯域幅容量を使用できます。
    > **重要:** 各ベンダーは、異なる方法でリソースを利用します。  想定されるユーザー負荷に対応するために必要なインスタンスサイズが不明な場合には、ソフトウェア ベンダーに直接連絡してレコメンデーションを確認してください。
- **インスタンスの数** - 多数のユーザー数と接続数が想定される場合、実行可能な NVA インスタンスサイズのスケールアップに関する制限があります。  複数の VM インスタンスをデプロイすることを検討してください。
- **IPSec vpn と SSL VPN の比較** - 一般的に、IPSec VPN 実装の方が、SSL VPN 実装よりもパフォーマンスが優れています。  
- **ライセンス** - NVA ソリューション用に購入したソフトウェア ライセンスが、COVID-19 パンデミック中に発生しうる急激な増加に対応していることを確認します。  NVA ライセンス プログラムの多くは、ソリューションが対応できる接続または帯域幅の数を制限しています。
- **高速ネットワーク** - 高速ネットワークをサポートする NVA ソリューションを検討してください。  高速ネットワークによって、VM との間でシングル ルート I/O 仮想化 (SR-IOV) が可能になり、ネットワークのパフォーマンスが大幅に向上します。 この高パフォーマンスのパスによってデータパスからホストがバイパスされ、サポートされている VM タイプのうち最も要件の厳しいネットワーク ワークロードで使用した場合でも、待ち時間、ジッター、CPU 使用率が軽減されます。 高速ネットワークは、2つ以上の vCPUs を備えた、汎用目的およびコンピューティングに最適化されたインスタンスサイズのほとんどでサポートされています。

## <a name="monitoring-resources"></a>リソースの監視

各 NVA ソリューションには、NVA のパフォーマンスをモニターするための独自のツールとリソースが用意されています。  パフォーマンスの制限について理解し、NVA が容量の上限に近づいている、あるいは上限に達していることを確実に検出できるようにするには、ベンダーのドキュメントを参照してください。  これに加えて、Azure Monitor Network Insights を参照し、次のような Network Virtual Appliances に関する基本パフォーマンス情報を確認することもできます。

- CPU 使用率
- Network In
- Network Out
- 受信フロー数
- 送信フロー数

## <a name="next-steps"></a>次の手順

主要な NVA パートナーの多くは、COVID-19 パンデミック中における予期せぬ急激な増加に対応したスケーリングに関するガイダンスをすでに投稿しました。 パートナー リソースへの便利なリンクをいくつか紹介します。

[Barracuda で、COVID-19 パンデミック中にデータを保護しながら自宅からの作業が可能に](https://www.barracuda.com/covid-19/work-from-home "COVID-19 パンデミック中にデータを保護しながら自宅からの作業が可能に")

[コロナウイルスが蔓延している間、Check Point によってリモート ワークフォースをセキュリティで保護する](https://www.checkpoint.com/solutions/secure-remote-workforce-during-coronavirus/ "コロナウイルスが蔓延している間、リモート ワークフォースをセキュリティで保護する")

[COVID-19 への備えとしての Cisco AnyConnect 実装とパフォーマンス/スケーリングの参考情報](https://www.cisco.com/c/en/us/support/docs/security/anyconnect-secure-mobility-client/215331-anyconnect-implementation-and-performanc.html "COVID-19 への備えとしての Cisco AnyConnect 実装とパフォーマンス/スケーリングの参考情報")

[Citrix COVID-19 対応サポートセンター](https://www.citrix.com/support/covid-19-coronavirus.html "Citrix COVID-19 対応サポートセンター")

[リモートワーカーの大幅な増加に対応する F5 ガイダンス](https://www.f5.com/business-continuity "リモートワーカーの大幅な増加に対応する F5 ガイダンス")

[Fortinet COVID-19 に関するカスタマーおよびパートナーへの更新情報](https://www.fortinet.com/covid-19.html "COVID-19 に関するカスタマーおよびパートナーへの更新情報")

[Palo Alto Networks COVID-19 対応センター](https://live.paloaltonetworks.com/t5/COVID-19-Response-Center/ct-p/COVID-19_Response_Center "Palo Alto Networks COVID-19 対応センター")

[Kemp で、ビジネス継続性のためにリモート作業とアプリの常時接続のエクスペリエンスが実現](https://kemptechnologies.com/remote-work-always-on-application-experience-business-continuity/ "Kemp で、ビジネス継続性のためにリモート作業とアプリの常時接続のエクスペリエンスが実現")

