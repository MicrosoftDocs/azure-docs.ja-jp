---
title: Azure のサーバーレス コンテナー
description: Azure Container Instances サービスには、仮想マシンを管理したり、より高度なオーケストレーターを採用したりせずに、Azure で最も高速かつ簡単に別個のコンテナーを実行する方法が用意されています。
ms.topic: overview
ms.date: 03/22/2021
ms.custom: seodec18, mvc
ms.openlocfilehash: c445687db7a154b6fc86e962d2c2340ad6297431
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799073"
---
# <a name="what-is-azure-container-instances"></a>Azure Container Instances とは

コンテナーは、クラウド アプリケーションのパッケージ化、デプロイ、管理を行う優れた方法としてしだいに普及しつつあります。 Azure Container Instances には、仮想マシンを管理したり、より高度なサービスを採用したりせずに、Azure で最も高速かつ簡単にコンテナーを実行する方法が用意されています。

Azure Container Instances は、単純なアプリケーション、タスク自動化、ジョブ作成など、分離されたコンテナーで操作できるあらゆるシナリオ向けの、優れたソリューションです。 複数コンテナー間でのサービスの検出、自動スケーリング、およびアプリケーションの調整されたアップグレードなど、コンテナーの完全なオーケストレーションが必要なシナリオには、[Azure Kubernetes Service (AKS)](../aks/index.yml) をお勧めします。

## <a name="fast-startup-times"></a>高速なスタートアップ時間

コンテナーは、スタートアップにおいて、仮想マシン (VM) よりもはるかに優れています。 Azure Container Instances を使用すると、VM をプロビジョニングして管理する必要なく、数秒で Azure でコンテナーを開始できます。

Docker Hub、プライベート [Azure コンテナー レジストリ](../container-registry/index.yml)、または別のクラウドベースの Docker レジストリから、Linux または Windows のコンテナー イメージを取り込みます。 ACI でサポートされているレジストリについては、[FAQ](container-instances-faq.md) を参照してください。 Azure Container Instances によって複数の一般的な基本 OS イメージがキャッシュされるため、カスタム アプリケーション イメージのデプロイを高速化するのに役立ちます。

## <a name="container-access"></a>コンテナー アクセス

Azure Container Instances を使用すると、IP アドレスと完全修飾ドメイン名 (FQDN) を使用してコンテナー グループをインターネットに直接公開できます。 コンテナー インスタンスを作成するとき、カスタム DNS 名ラベルを指定できるので、*customlabel*.*azureregion*.azurecontainer.io でアプリケーションに到達できます。

Azure Container Instances では、アプリケーションの開発とトラブルシューティングを支援するために、対話型シェルを提供することで、実行中のコンテナーでのコマンドの実行もサポートしています。 アクセスは HTTPS 経由で行われ、TLS を使用してクライアント接続がセキュリティで保護されます。

> [!IMPORTANT]
> 2020 年 1 月 13 日以降、Azure Container Instances では、サーバーとアプリケーションからのセキュリティで保護されたすべての接続で TLS 1.2 を使用する必要があります。 TLS 1.0 と 1.1 のサポートは、廃止される予定です。

## <a name="compliant-deployments"></a>準拠しているデプロイ

### <a name="hypervisor-level-security"></a>ハイパーバイザーレベルのセキュリティ

従来、コンテナーは、アプリケーション依存関係の分離とリソース ガバナンスを実現してきましたが、悪意のあるマルチテナント使用に対する十分なセキュリティ強化については考慮されていません。 Azure Container Instances を使用すると、アプリケーションは、VM 内であるかのように、コンテナー内で確実に分離されます。

### <a name="customer-data"></a>顧客データ

ACI サービスは、コンテナー グループが想定どおりに実行されていることを保証するために必要な最小限の顧客データを格納します。 現在、顧客データを 1 つのリージョンに格納することは、アジア太平洋地域の東南アジア リージョン (シンガポール) と、ブラジル地域のブラジル南部リージョン (サンパウロ州) でのみ使用できます。 その他のすべてのリージョンでは、顧客データは[地域](https://azure.microsoft.com/global-infrastructure/geographies/)内に格納されます。 詳細については、Azure サポートまでお問い合わせください。

## <a name="custom-sizes"></a>カスタム サイズ

コンテナーは、通常、1 つのアプリケーションだけを実行するために最適化されますが、そのようなアプリケーションの正確なニーズはさまざまに異なることがあります。 Azure Container Instances で、CPU のコア数とメモリを厳密に指定することによって最適な稼働率を確保することが可能です。 支払額は、その要求内容に基づいて秒単位で課金されるので、実際のニーズに応じて支出をきめ細やかに調整することができます。

機械学習などの計算集中型ジョブの場合、Azure Container Instances では、NVIDIA Tesla [GPU リソース](container-instances-gpu.md) (プレビュー) を使用するように Linux コンテナーをスケジュールできます。

## <a name="persistent-storage"></a>永続的ストレージ

Azure Container Instances を使用して状態を取得および保持できるように、Azure Storage によってサポートされる [Azure Files 共有の直接マウント](./container-instances-volume-azure-files.md)が提供されます。

## <a name="linux-and-windows-containers"></a>Linux コンテナーと Windows コンテナー

Azure Container Instances では、同じ API で、Windows と Linux の両方のコンテナーをスケジュールできます。 [コンテナー グループ](container-instances-container-groups.md)を作成するときに、OS の種類を指定するだけです。

一部の機能は、現在のところ Linux コンテナーに限定されています。

* コンテナー グループあたりの複数のコンテナー
* ボリューム マウント ([Azure Files](container-instances-volume-azure-files.md)、[emptyDir](container-instances-volume-emptydir.md)、[GitRepo](container-instances-volume-gitrepo.md)、[シークレット](container-instances-volume-secret.md))
* [リソース使用量メトリック](container-instances-monitor.md)と Azure Monitor
* [仮想ネットワークのデプロイ](container-instances-vnet.md)
* [GPU リソース](container-instances-gpu.md) (プレビュー)

Windows コンテナーのデプロイでは、一般的な [Windows ベースのイメージ](container-instances-faq.md#what-windows-base-os-images-are-supported)に基づくイメージを使用します。

## <a name="co-scheduled-groups"></a>共同スケジュール グループ

Azure Container Instances は、ホスト コンピューター、ローカル ネットワーク、ストレージ、およびライフサイクルを共有する[複数コンテナー グループ](container-instances-container-groups.md)のスケジュール設定をサポートします。 これにより、メイン アプリケーション コンテナーを、サイドカーのように主要な機能と併走しながら補助的な役割をつかさどるコンテナー (ログ記録など) と結合することができます。

## <a name="virtual-network-deployment"></a>仮想ネットワークのデプロイ

Azure Container Instances を使用すると、[Azure 仮想ネットワークにコンテナー インスタンスをデプロイする](container-instances-vnet.md)ことができます。 仮想ネットワーク内のサブネットにデプロイするとき、コンテナー インスタンスでは、オンプレミス上にあるものなど ([VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) または [ExpressRoute](../expressroute/expressroute-introduction.md) 経由で)、仮想ネットワークに存在する他のリソースと安全に通信することができます。

## <a name="next-steps"></a>次のステップ

クイックスタート ガイドを使用して、1 つのコマンドでコンテナーを Azure にデプロイしてみてください。

> [!div class="nextstepaction"]
> [Azure Container Instances のクイックスタート](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
