---
title: Azure Container Instances とは
description: Azure Container Instances サービスには、仮想マシンを管理したり、より高度なオーケストレーターを採用したりせずに、Azure で最も高速かつ簡単に別個のコンテナーを実行する方法が用意されています。
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: overview
ms.date: 04/25/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: a07eda709c813e68bc3e4f08b6aab3f93dec65a6
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325739"
---
# <a name="what-is-azure-container-instances"></a>Azure Container Instances とは

コンテナーは、クラウド アプリケーションのパッケージ化、デプロイ、管理を行う優れた方法としてしだいに普及しつつあります。 Azure Container Instances には、仮想マシンを管理したり、より高度なサービスを採用したりせずに、Azure で最も高速かつ簡単にコンテナーを実行する方法が用意されています。

Azure Container Instances は、単純なアプリケーション、タスク自動化、ジョブ作成など、分離されたコンテナーで操作できるあらゆるシナリオ向けの、優れたソリューションです。 複数コンテナー間でのサービスの検出、自動スケーリング、およびアプリケーションの調整されたアップグレードなど、コンテナーの完全なオーケストレーションが必要なシナリオには、[Azure Kubernetes Service (AKS)](../aks/index.yml) をお勧めします。

## <a name="fast-startup-times"></a>高速なスタートアップ時間

コンテナーは、スタートアップにおいて、仮想マシン (VM) よりもはるかに優れています。 Azure Container Instances を使用すると、VM をプロビジョニングして管理する必要なく、数秒で Azure でコンテナーを開始できます。

## <a name="public-ip-connectivity-and-dns-name"></a>パブリック IP 接続と DNS 名

Azure Container Instances を使用すると、IP アドレスと完全修飾ドメイン名 (FQDN) を使ってコンテナーをインターネットに直接公開できます。 コンテナー インスタンスを作成するとき、カスタム DNS 名ラベルを指定できるので、*customlabel*.*azureregion*.azurecontainer.io でアプリケーションに到達できます。

## <a name="hypervisor-level-security"></a>ハイパーバイザーレベルのセキュリティ

従来、コンテナーは、アプリケーション依存関係の分離とリソース ガバナンスを実現してきましたが、悪意のあるマルチテナント使用に対する十分なセキュリティ強化については考慮されていません。 Azure Container Instances を使用すると、アプリケーションは、VM 内であるかのように、コンテナー内で確実に分離されます。

## <a name="custom-sizes"></a>カスタム サイズ

コンテナーは、通常、1 つのアプリケーションだけを実行するために最適化されますが、そのようなアプリケーションの正確なニーズはさまざまに異なることがあります。 Azure Container Instances で、CPU のコア数とメモリを厳密に指定することによって最適な稼働率を確保することが可能です。 支払額は、その要求内容に基づいて秒単位で課金されるので、実際のニーズに応じて支出をきめ細やかに調整することができます。

機械学習などの計算集中型ジョブの場合、Azure Container Instances では、NVIDIA Tesla [GPU リソース](container-instances-gpu.md) (プレビュー) を使用するように Linux コンテナーをスケジュールできます。

## <a name="persistent-storage"></a>永続的ストレージ

Azure Container Instances を使用して状態を取得および保持できるように、Microsoft では [Azure Files 共有を直接マウント](container-instances-mounting-azure-files-volume.md)しています。

## <a name="linux-and-windows-containers"></a>Linux コンテナーと Windows コンテナー

Azure Container Instances では、同じ API で、Windows と Linux の両方のコンテナーをスケジュールできます。 [コンテナー グループ](container-instances-container-groups.md)を作成するときに、OS の種類を指定するだけです。

一部の機能は、現在のところ Linux コンテナーに限定されています。

* コンテナー グループあたりの複数のコンテナー
* ボリューム マウント ([Azure Files](container-instances-volume-azure-files.md)、[emptyDir](container-instances-volume-emptydir.md)、[GitRepo](container-instances-volume-gitrepo.md)、[シークレット](container-instances-volume-secret.md))
* [リソース使用量メトリック](container-instances-monitor.md)と Azure Monitor
* [仮想ネットワークのデプロイ](container-instances-vnet.md) (プレビュー)
* [GPU リソース](container-instances-gpu.md) (プレビュー)

Windows コンテナーのデプロイでは、一般的な [Windows ベースのイメージ](container-instances-faq.md#what-windows-base-os-images-are-supported)に基づくイメージを使用します。

> [!NOTE]
> Azure Container Instances での Windows Server 2019 ベースのイメージの使用は、プレビュー段階です。

## <a name="co-scheduled-groups"></a>共同スケジュール グループ

Azure Container Instances は、ホスト コンピューター、ローカル ネットワーク、ストレージ、およびライフサイクルを共有する[複数コンテナー グループ](container-instances-container-groups.md)のスケジュール設定をサポートします。 これにより、メイン アプリケーション コンテナーを、サイドカーのように主要な機能と併走しながら補助的な役割をつかさどるコンテナー (ログ記録など) と結合することができます。

## <a name="virtual-network-deployment-preview"></a>仮想ネットワークのデプロイ (プレビュー)

これは [Azure 仮想ネットワークにコンテナー インスタンスをデプロイ](container-instances-vnet.md)する機能です。Azure Container Instances の機能であり、現在はプレビュー段階となっています。 コンテナー インスタンスを仮想ネットワーク内のサブネットにデプロイすることで、オンプレミス上のリソースとの ([VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) または [ExpressRoute](../expressroute/expressroute-introduction.md) 経由での) 通信も含め、仮想ネットワークに存在する他のリソースとの通信を安全に行うことができます。

> [!IMPORTANT]
> Azure Container Instances の一部の機能はプレビュー段階にあり、一部の[制限が適用されます](container-instances-vnet.md#preview-limitations)。 プレビュー版は、[追加使用条件][terms-of-use]に同意することを条件に使用できます。 このような機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。

## <a name="next-steps"></a>次の手順

クイックスタート ガイドを使用して、1 つのコマンドでコンテナーを Azure にデプロイしてみてください。

> [!div class="nextstepaction"]
> [Azure Container Instances のクイックスタート](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
