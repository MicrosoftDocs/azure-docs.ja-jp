---
title: Azure DDoS Protection の参照アーキテクチャ
description: Azure DDoS Protection の参照アーキテクチャについて説明します。
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: e5472620fe9b07d152a5325b0654044cb1505fd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94992439"
---
# <a name="ddos-protection-reference-architectures"></a>DDoS Protection の参照アーキテクチャ

DDoS Protection Standard は、[仮想ネットワークにデプロイされるサービス用](../virtual-network/virtual-network-for-azure-services.md)に設計されています。 他のサービスには、既定の DDoS Protection Basic が適用されます。 以下の参照アーキテクチャは、アーキテクチャ パターンでグループ化されたシナリオで調整されています。

## <a name="virtual-machine-windowslinux-workloads"></a>仮想マシン (Windows/Linux) のワークロード

### <a name="application-running-on-load-balanced-vms"></a>負荷分散された VM 上で実行しているアプリケーション

この参照アーキテクチャは、可用性とスケーラビリティを向上させるために、ロード バランサーの背後にあるスケール セット内で複数の Windows VM を実行するための一連の実証済みの手法を示します。 このアーキテクチャは、Web サーバーなど、任意のステートレス ワークロードで使用できます。

![負荷分散された VM で実行されているアプリケーションの参照アーキテクチャの図](./media/ddos-best-practices/image-9.png)

このアーキテクチャでは、ワークロードが複数の VM インスタンスにわたって分散されます。 単一のパブリック IP アドレスが存在し、ロード バランサーを通じてインターネット トラフィックが VM に分散されます。 パブリック IP が関連付けられた Azure (インターネット) ロード バランサーの仮想ネットワークでは、DDoS Protection Standard が有効になります。

ロード バランサーは、受信インターネット要求を各 VM インスタンスに分散します。 仮想マシン スケール セットにより、VM の数を手動でスケールインまたはスケールアウトしたり、定義済みの規則に基づいて自動的に設定したりできるようになります。 これは、リソースが DDoS 攻撃を受けている場合に重要です。 この参照アーキテクチャについて詳しくは、[こちらの記事](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)をご覧ください。

### <a name="application-running-on-windows-n-tier"></a>Windows N 層で実行しているアプリケーション

N 層アーキテクチャを実装する方法は多数あります。 次の図は、典型的な 3 層の Web アプリケーションを示したものです。 このアーキテクチャは「[スケーラビリティと可用性のために負荷分散された VM を実行する](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)」の記事に基づいて作成されています。 Web 層とビジネス層では、負荷分散された VM が使用されます。

![Windows N 層で実行されているアプリケーションの参照アーキテクチャの図](./media/ddos-best-practices/image-10.png)

このアーキテクチャでは、DDoS Protection Standard が仮想ネットワークで有効になっています。 仮想ネットワーク内のすべてのパブリック IP には、レイヤー 3/レイヤー 4 の DDoS 保護が適用されます。 レイヤー 7 を保護するには、WAF SKU の Application Gateway をデプロイしてください。 この参照アーキテクチャについて詳しくは、[こちらの記事](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier)をご覧ください。

> [!NOTE]
> 1 つの VM がパブリック IP の背後で実行されているシナリオはサポートされていません。

### <a name="paas-web-application"></a>PaaS Web アプリケーション

この参照アーキテクチャでは、単一リージョンでの Azure App Service アプリケーションの実行を示します。 このアーキテクチャは、[Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) と [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/) を使用する Web アプリケーションを対象とした一連の実証済みプラクティスを示しています。
フェールオーバー シナリオのためにスタンバイ リージョンを設定します。

![PaaS Web アプリケーションの参照アーキテクチャの図](./media/ddos-best-practices/image-11.png)

Azure Traffic Manager は、着信要求をいずれかのリージョンの Application Gateway にルーティングします。 通常の運用中は、アクティブなリージョンの Application Gateway に要求をルーティングします。 そのリージョンが使用できなくなった場合、Traffic Manager はスタンバイ リージョンの Application Gateway にフェールオーバーします。

インターネットから Web アプリケーションに宛てられたすべてのトラフィックは、Traffic Manager によって [Application Gateway のパブリック IP アドレス](../application-gateway/application-gateway-web-app-overview.md)にルーティングされます。 このシナリオでは、アプリ サービス (Web アプリ) 自体は外部に対して直接公開されておらず、Application Gateway によって保護されています。 

Application Gateway WAF SKU (禁止モード) を構成して、レイヤー 7 (HTTP/HTTPS/Web ソケット) の攻撃を防ぐことをお勧めします。 さらに、Web アプリは、ָ[Application Gateway の IP アドレスからのトラフィックのみを受け入れる](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)ように構成されます。

この参照アーキテクチャについて詳しくは、[こちらの記事](/azure/architecture/reference-architectures/app-service-web-app/multi-region)をご覧ください。

## <a name="mitigation-for-non-web-paas-services"></a>Web PaaS 以外のサービスに対するリスク軽減

### <a name="hdinsight-on-azure"></a>Azure 上の HDInsight

この参照アーキテクチャは、[Azure HDInsight クラスター](../hdinsight/index.yml)に対する DDoS Protection Standard の構成を示したものです。 HDInsight クラスターが仮想ネットワークにリンクされていること、および DDoS Protection がその仮想ネットワークで有効になっていることを確認してください。

![[HDInsight] および [詳細設定] ウィンドウと、仮想ネットワークの設定](./media/ddos-best-practices/image-12.png)

![DDoS Protection を有効にするための選択](./media/ddos-best-practices/image-13.png)

このアーキテクチャでは、インターネットから HDInsight クラスター宛のトラフィックは、HDInsight ゲートウェイのロード バランサーに関連付けられているパブリック IP にルーティングされます。 ゲートウェイのロード バランサーは、トラフィックをヘッド ノードまたはワーカー ノードに直接送信します。 HDInsight の仮想ネットワークで DDoS Protection Standard が有効になっているので、仮想ネットワーク内のすべてのパブリック IP アドレスにはレイヤー 3/レイヤー 4 の DDoS Protection が適用されます。 この参照アーキテクチャは、N 層およびマルチリージョンの参照アーキテクチャと組み合わせることができます。

この参照アーキテクチャについて詳しくは、「[Azure Virtual Network を使用した Azure HDInsight の拡張](../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」をご覧ください。


> [!NOTE]
> パブリック IP を使用する仮想ネットワーク内での PowerApps 用 Azure App Service 環境または API 管理は、どちらもネイティブにはサポートされていません。

## <a name="next-steps"></a>次のステップ

- [DDoS 保護プランの作成](manage-ddos-protection.md)方法について説明します。