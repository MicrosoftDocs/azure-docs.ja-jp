---
title: Azure Kubernetes Service (AKS) での機密ノードのサポートに関してよく寄せられる質問
description: Azure Kubernetes Service (AKS) および Azure Confidential Computing (ACC) ノードのサポートについてよく寄せられる質問の一部に対する回答が見つかります。
author: agowdamsft
ms.service: container-service
ms.topic: conceptual
ms.date: 02/09/2020
ms.author: amgowda
ms.openlocfilehash: 550995f0be3d634e7e9f24a8bf6826916003308e
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653399"
---
# <a name="frequently-asked-questions-about-confidential-computing-nodes-on-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 上の Confidential Computing ノードについてよく寄せられる質問

この記事では、Azure Kubernetes Service (AKS) 上の Intel SGX ベースの Confidential Computing ノードについてよく寄せられる質問にお答えします。 他に質問がある場合は、 **acconaks@microsoft.com** にメールを送信してください。

<a name="1"></a>
### <a name="are-the-confidential-computing-nodes-on-aks-in-ga"></a>AKS の Confidential Computing ノードは GA ですか。 ###
はい

<a name="2"></a>
### <a name="what-is-attestation-and-how-can-we-do-attestation-of-apps-running-in-enclaves"></a>構成証明とは何ですか。エンクレーブで実行されているアプリの構成証明を実行するにはどうすればよいですか。 ###
構成証明は、ソフトウェアの一部が特定のハードウェア プラットフォームで適切にインスタンス化されていることを実証および検証するプロセスです。 これにより、セキュリティで保護されたプラットフォームで実行されており、改ざんされていないことを保証する証拠が検証可能であることも確保されます。 エンクレーブ アプリの構成証明の実行方法の[詳細について参照してください](attestation.md)。

<a name="3"></a>
### <a name="can-i-enable-accelerated-networking-with-azure-confidential-computing-aks-clusters"></a>Azure Confidential Computing AKS クラスターで高速ネットワークを有効にできますか。 ###
いいえ。 高速ネットワークは、AKS の Confidential Computing ノードを構成する DCSv2 仮想マシンではサポートされていません。 

<a name="4"></a>
### <a name="can-i-bring-my-existing-containerized-applications-and-run-it-on-aks-with-azure-confidential-computing"></a>既存のコンテナー化されたアプリケーションを持ち込み、Azure Confidential Computing を使用する AKS 上で実行できますか。 ###
はい。プラットフォーム イネーブラーの詳細については、[「機密コンテナー」ページ](confidential-containers.md)を参照してください。

<a name="5"></a>
### <a name="what-version-of-intel-sgx-driver-version-is-on-the-aks-image-for-confidential-nodes"></a>Confidential ノードの AKS イメージには、どのバージョンの Intel SGX ドライバーがありますか。 ### 
現在、Azure Confidential Computing DCSv2 VM は、Intel SGX DCAP 1.33 と共にインストールされています。 

<a name="6"></a>
### <a name="can-i-inject-post-install-scriptscustomize-drivers-to-the-nodes-provisioned-by-aks"></a>AKS によってプロビジョニングされたノードに対してインストール後のスクリプトを挿入することや、ドライバーをカスタマイズすることはできますか。 ###
いいえ。 [AKS エンジン ベースの Confidential Computing ノード](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md)では、カスタム インストールを実行でき、かつ Kubernetes コントロール プレーンをフル制御できる Confidential Computing ノードがサポートされています。
<a name="7"></a>

### <a name="should-i-be-using-a-docker-base-image-to-get-started-on-enclave-applications"></a>エンクレーブ アプリケーションを開始するには、Docker ベース イメージを使用する必要がありますか。 ###
さまざまなイネーブラー (ISV および OSS プロジェクト) には、機密コンテナーを有効にする方法が用意されています。 詳細と実装への個々の参照については、[「機密コンテナー」ページ](confidential-containers.md)を参照してください。

<a name="8"></a>
### <a name="can-i-run-acc-nodes-with-other-standard-aks-skus-build-a-heterogenous-node-pool-cluster"></a>他の Standard AKS SKU で ACC ノードを実行する (異種ノード プール クラスターを構築する) ことはできますか。 ###

はい。ACC ノードを含む同じ AKS クラスター内で異なるノード プールを実行できます。 特定のノード プール上のエンクレーブ アプリケーションをターゲットにするには、ノード セレクターを追加するか、EPC 制限を適用することを検討してください。 機密ノードのクイック スタートの詳細については、[こちら](confidential-nodes-aks-get-started.md)を参照してください。

<a name="9"></a>
### <a name="can-i-run-windows-nodes-and-windows-containers-with-acc"></a>ACC で Windows ノードと Windows コンテナーを実行できますか。 ###
現時点ではありません。 Windows ノードまたはコンテナーを必要とする場合は、製品チーム ( *acconaks@microsoft.com* ) にお問い合わせください。 

<a name="10"></a>
### <a name="what-if-my-container-size-is-more-than-available-epc-memory"></a>コンテナー サイズが使用可能な EPC メモリを超えている場合はどうすればよいですか。 ###
EPC メモリは、エンクレーブで実行するようにプログラムされているアプリケーションの部分に適用されます。 コンテナーの合計サイズは、使用可能な最大 EPC メモリと比較する場合に適した方法ではありません。 実際、SGX が搭載された DCSv2 マシンでは、アプリケーションの信頼されていない部分で使用される最大 32GB の VM メモリが許可されています。 ただし、コンテナーに使用可能な EPC メモリを超える量が消費される場合、エンクレーブで実行されているプログラムの部分のパフォーマンスが影響を受ける可能性があります。

ワーカー ノードの EPC メモリをより適切に管理するには、Kubernetes を介した EPC メモリベースの制限管理を検討してください。 以下の例を参考にしてください。

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10 # This limit will automatically place the job into confidential computing node. Alternatively, you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
```
<a name="11"></a>
### <a name="what-happens-if-my-enclave-consumes-more-than-maximum-available-epc-memory"></a>エンクレーブに使用可能な最大 EPC メモリを超える量が消費されるとどうなりますか。 ###

使用可能な EPC メモリの合計は、同じ VM またはワーカー ノード内のエンクレーブ アプリケーション間で共有されます。 アプリケーションに使用可能な EPC メモリを超える量が使用されている場合、アプリケーションのパフォーマンスが影響を受ける可能性があります。 このため、上の例で示すように、デプロイの yaml ファイルでアプリケーションごとの許容値を設定して、ワーカー ノードごとに使用可能な EPC メモリをより適切に管理することをお勧めします。 または、ワーカー ノード プールの VM サイズの上限を引き上げるか、ノードを追加することをいつでも選択できます。 

<a name="12"></a>
### <a name="why-cant-i-do-forks--and-exec-to-run-multiple-processes-in-my-enclave-application"></a>エンクレーブ アプリケーションで複数のプロセスを実行するために forks () と exec を実行できないのはなぜですか。 ###

現在、Azure Confidential Computing DCsv2 SKU VM は、エンクレーブで実行されているプログラムに対して 1 つのアドレス空間をサポートしています。 1 つのプロセスは、高度なセキュリティを中心に設計された現在の制限です。 ただし、機密コンテナー イネーブラーには、この制限を克服できる代替実装が存在する場合があります。
<a name="13"></a>
### <a name="do-you-automatically-install-any-additional-daemonset-to-expose-the-sgx-drivers"></a>SGX ドライバーを公開するために、追加の daemonset が自動的にインストールされますか。 ###

はい。 daemonset の名前は、sgx-device-plugin です。 それぞれの目的の詳細については、[こちら](confidential-nodes-aks-overview.md)を参照してください。  

<a name="14"></a>
### <a name="what-is-the-vm-sku-i-should-be-choosing-for-confidential-computing-nodes"></a>Confidential Computing ノードには、どのような VM SKU を選択する必要がありますか。 ###

DCSv2 SKUs。 [DCSv2 SKU](../virtual-machines/dcv2-series.md) は、[サポートされているリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all)で使用できます。

<a name="15"></a>
### <a name="can-i-still-schedule-and-run-non-enclave-containers-on-confidential-computing-nodes"></a>Confidential Computing ノード上で非エンクレーブ コンテナーをスケジュールして実行することはできますか。 ###

はい。 VM には、標準のコンテナー ワークロードを実行できる通常のメモリもあります。 デプロイ モデルを決定する前に、アプリケーションのセキュリティと脅威モデルを検討してください。
<a name="16"></a>

### <a name="can-i-provision-aks-with-dcsv2-node-pools-through-azure-portal"></a>Azure portal を使用して DCSv2 ノード プールで AKS をプロビジョニングすることはできますか。 ###

はい。 Azure CLI は、[こちら](confidential-nodes-aks-get-started.md)に記載されている代替手段として使用することもできます。

<a name="17"></a>
### <a name="what-ubuntu-version-and-vm-generation-is-supported"></a>どの Ubuntu バージョンと VM 生成がサポートされていますか。 ###
Gen 2 上の 18.04。 

<a name="18"></a>
### <a name="can-we-change-the-current-intel-sgx-dcap-diver-version-on-aks"></a>AKS 上で現在の Intel SGX DCAP ダイバーのバージョンを変更できますか。 ###

いいえ。 カスタム インストールを実行するには、[AKS-Engine Confidential Computing ワーカー ノード](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md)のデプロイを選択することをお勧めします。 

<a name="19"></a>

### <a name="what-version-of-kubernetes-do-you-support-and-recommend"></a>サポートおよび推奨されている Kubernetes のバージョンを教えてください。 ###

Kubernetes バージョン 1.16 以降がサポートされ、推奨されています。 

<a name="20"></a>
### <a name="what-are-the-known-current-limitations-of-the-product"></a>製品の現在における既知の制限は何ですか。 ###

- Ubuntu 18.04 Gen 2 VM ノードのみをサポートします 
- Windows ノードのサポートまたは Windows コンテナーのサポートがありません
- EPC メモリ ベースの水平ポッド自動スケーリングはサポートされていません。 CPU と通常のメモリベースのスケーリングはサポートされています。
- 機密アプリ用の AKS 上の Dev Spaces は現在サポートされていません

<a name="21"></a>
### <a name="will-only-signed-and-trusted-images-be-loaded-in-the-enclave-for-confidential-computing"></a>Confidential Computing では、署名され、信頼されたイメージのみがエンクレーブに読み込まれますか。 ###
エンクレーブの初期化中はネイティブで行われませんが、構成証明プロセスを通じて署名を検証できるので、「はい」です。 [こちらを](../attestation/basic-concepts.md#benefits-of-policy-signing)参照してください。 

### <a name="next-steps"></a>次の手順
機密コンテナーの詳細について、[「機密コンテナー」ページ](confidential-containers.md)を確認します。
