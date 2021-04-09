---
title: Azure Kubernetes Service (AKS) 上のコンフィデンシャル コンピューティング ノード
description: AKS のコンフィデンシャル コンピューティング ノード
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.topic: overview
ms.date: 2/08/2021
ms.author: amgowda
ms.openlocfilehash: 9205513c4eb7e377fee0c5d18577d76a82476cf2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553393"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service"></a>Azure Kubernetes Service 上のコンフィデンシャル コンピューティング ノード

[Azure Confidential Computing](overview.md) を使用すると、機密データをその使用中に保護することができます。 このデータは、基になるコンフィデンシャル コンピューティング インフラストラクチャと、ハードウェアを基盤にした高信頼実行コンテナー環境によって、他のアプリケーションや管理者、クラウド プロバイダーから保護されます。 コンフィデンシャル コンピューティング ノードを追加することにより、コンテナー アプリケーションを、ハードウェアで保護され、構成証明可能な隔離された環境で実行するターゲットに設定できます。

## <a name="overview"></a>概要

Azure Kubernetes Service (AKS) は、Intel SGX を基盤とする [DCsv2 コンフィデンシャル コンピューティング ノード](confidential-computing-enclaves.md)の追加をサポートします。 これらのノードにより、ハードウェアベースの信頼できる実行環境 (TEE: Trusted Execution Environment ) 内で機密のワークロードを実行できます。 TEE では、コンテナーのユーザーレベルのコードでメモリのプライベート領域を割り当てて、CPU でコードを直接実行できます。 CPU で直接実行されるこれらのプライベート メモリ領域は、エンクレーブと呼ばれます。 エンクレーブは、同じノードで実行されている他のプロセスから、データの機密性、データの整合性、およびコードの整合性を保護するのに役立ちます。 Intel SGX 実行モデルでは、ゲスト OS、ホスト OS、ハイパーバイザーの中間層も削除されるため、攻撃対象領域が小さくなります。 ノード内の "*ハードウェア ベースのコンテナーごとに分離された実行*" モデルによって、コンテナーごとに特殊なメモリ ブロックを暗号化したまま、アプリケーションを CPU で直接実行することができます。 機密コンテナーを使用したコンフィデンシャル コンピューティング ノードは、ゼロ トラスト セキュリティ計画と多層防御コンテナー戦略に対する優れた追加機能です。

![SGX ノードの概要](./media/confidential-nodes-aks-overview/sgxaksnode.jpg)

## <a name="aks-confidential-nodes-features"></a>AKS の機密ノードの機能

- Intel SGX の信頼できる実行環境 (TEE) によってハードウェア ベース、プロセス レベルでコンテナーを分離 
- 異種ノード プール クラスター (機密ノード プールと非機密ノード プールの混在)
- 暗号化されたページ キャッシュ (EPC) メモリベースのポッド スケジューリング (アドオンが必要)
- プレインストールされた Intel SGX DCAP ドライバー
- CPU 消費量に基づくポッドの水平自動スケーリングとクラスターの自動スケーリング
- Ubuntu 18.04 Gen 2 VM ワーカー ノードによる Linux コンテナーのサポート

## <a name="confidential-computing-add-on-for-aks"></a>AKS 用のコンフィデンシャル コンピューティング アドオン
このアドオン機能を使用すると、クラスターでコンフィデンシャル コンピューティング ノード プールを実行するときに、AKS の追加機能が有効になります。 このアドオンにより、以下の機能が有効になります。

#### <a name="azure-device-plugin-for-intel-sgx"></a>Intel SGX 用 Azure デバイス プラグイン<a id="sgx-plugin"></a>

デバイス プラグインは、暗号化されたページ キャッシュ (EPC) メモリ用の Kubernetes デバイス プラグイン インターフェイスを実装し、ノードからデバイス ドライバーを公開します。 EPC メモリは、実質上、このプラグインによって、Kubernetes におけるもう 1 つのリソース タイプとなります。 ユーザーは、他のリソースと同様、このリソースに対する制限を指定できます。 このデバイス プラグインは、スケジューリング機能以外に、機密ワークロード コンテナーへのアクセス許可を Intel SGX デバイス ドライバーに割り当てるのに役立ちます。 開発者は、このプラグインを使用して、デプロイ ファイル内の Intel SGX ドライバー ボリュームがマウントされないようにすることができます。 EPC メモリベース デプロイの実装サンプル (`kubernetes.azure.com/sgx_epc_mem_in_MiB`) は、[こちら](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml)でご覧いただけます。


## <a name="programming-models"></a>プログラミング モデル

### <a name="confidential-containers"></a>機密コンテナー

[機密コンテナー](confidential-containers.md)は、ほとんどの **一般的なプログラミング言語** ランタイム (Python、ノード、Java など) の既存の変更されていないコンテナー アプリケーションを機密性を保った状態で実行するのに役立ちます。 このパッケージ モデルでは、ソースコードの変更や再コンパイルは必要ありません。 これは、お使いの標準 Docker コンテナーをオープンソース プロジェクトまたは Azure パートナー ソリューションでパッケージ化することで実現できる、機密性への最速の方法です。 このパッケージおよび実行モデルでは、コンテナー アプリケーションのすべての部分が信頼できる境界 (エンクレーブ) に読み込まれます。 このモデルは、市場で入手可能な既成のコンテナー アプリケーションや、現在汎用ノードで実行されているカスタム アプリに適しています。

### <a name="enclave-aware-containers"></a>エンクレーブ対応コンテナー
AKS 上のコンフィデンシャル コンピューティング ノードでは、CPU の **特殊な命令セット** を利用するためにエンクレーブで実行するようにプログラムされたコンテナーもサポートします。 このプログラミング モデルでは、実行フローをより厳密に制御でき、特別な SDK とフレームワークを使用する必要があります。 このプログラミング モデルは、アプリケーション フローのほとんどの制御に最小の信頼できるコンピューティング ベース (TCB) を提供します。 エンクレーブ対応のコンテナー開発には、コンテナー アプリケーションの信頼できない部分と信頼できる部分が含まれるため、通常のメモリと、エンクレーブが実行される暗号化されたページ キャッシュ (EPC) メモリを管理できます。 エンクレーブ対応コンテナーについての[詳細情報](enclave-aware-containers.md)をご覧ください。

## <a name="next-steps"></a>次の手順

[コンフィデンシャル コンピューティング ノードを使用して AKS クラスターをデプロイする](./confidential-nodes-aks-get-started.md)

[機密コンテナーのクイック スタート サンプル](https://github.com/Azure-Samples/confidential-container-samples)

[DCsv2 SKU リスト](../virtual-machines/dcv2-series.md)

[機密コンテナーを使用した多層防御に関するウェビナー セッション](https://www.youtube.com/watch?reload=9&v=FYZxtHI_Or0&feature=youtu.be)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions
