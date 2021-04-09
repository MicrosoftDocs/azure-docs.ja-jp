---
title: Azure の Intel SGX クォート ヘルパー デーモンセットによるアウトプロセス構成証明のサポート (プレビュー)
description: SGX アプリケーションのプロセス外でクォートを生成するためのデーモンセット。 この記事では、コンテナー内で実行される機密性の高いワークロードに対し、アウトプロセス構成証明機能がどのように提供されるかについて説明します。
ms.service: container-service
author: agowdamsft
ms.topic: overview
ms.date: 2/12/2021
ms.author: amgowda
ms.openlocfilehash: 5d872032ea5b4e08c3f436dd3bfc202786d8514d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553257"
---
# <a name="platform-software-management-with-sgx-quote-helper-daemon-set-preview"></a>SGX クォート ヘルパー デーモン セットによるプラットフォーム ソフトウェア管理 (プレビュー)

リモート構成証明を実行する[エンクレーブ アプリケーション](confidential-computing-enclaves.md)は、生成されたクォートを必要とします。 このクォートによって、アプリケーションの ID と状態、そしてエンクレーブが実行されている環境の暗号による証明が得られます。 クォートを生成するためには、Intel の Platform Software Components (PSW) に含まれる信頼のおけるソフトウェア コンポーネントが必要です。

## <a name="overview"></a>概要
 
クォートの生成に関して、Intel は 2 つの構成証明モードをサポートしています。
- **インプロセス**: 信頼のおけるソフトウェア コンポーネントをエンクレーブ アプリケーションのプロセス内でホストします。

- **アウトプロセス**: 信頼のおけるソフトウェア コンポーネントをエンクレーブ アプリケーションの外でホストします。
 
Open Enclave SDK を使用して作成された SGX アプリケーションには、既定でインプロセス構成証明モードが使用されます。 SGX ベースのアプリケーションはアウトプロセスに対応していますが、そのためには、アプリケーションの外部で、Architectural Enclave Service Manager (AESM) などの必須コンポーネントを別途ホストして公開する必要があります。

この機能を利用することを **強くお勧め** します。Intel プラットフォームの更新時や DCAP ドライバーの更新時のエンクレーブ アプリのアップタイムが向上します。

AKS クラスターでこの機能を有効にするには、コンフィデンシャル コンピューティング アドオンを有効にするときに、CLI に対する add --enable-sgxquotehelper コマンドを変更してください。 CLI の詳細な手順は[こちら](confidential-nodes-aks-get-started.md)にあります。 

```azurecli-interactive
# Create a new AKS cluster with system node pool with Confidential Computing addon enabled and SGX Quote Helper
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom --enable-sgxquotehelper
```

## <a name="why-and-what-are-the-benefits-of-out-of-proc"></a>アウトプロセスを使用する理由と利点

-   コンテナー化されたアプリケーションごとに PSW のクォート生成コンポーネントを更新する必要がない。アウトプロセスでは、コンテナーの所有者がそのコンテナー内の更新を管理する必要がありません。 コンテナーの所有者は、プロバイダーから提供されたインターフェイスを利用することができます。これにより、コンテナーの外部にある一元化されたサービスが呼び出され、その更新と管理がプロバイダーによって行われます。

-   古い PSW コンポーネントに起因する構成証明の失敗を懸念する必要がない。クォートの生成には、信頼のおけるソフトウェア コンポーネント、Quoting Enclave (QE) と Provisioning Certificate Enclave (PCE) が関係します。どちらも信頼済みのコンピューティング ベース (TCB) に含まれています。 これらのソフトウェア コンポーネントは、構成証明の要件を維持するために最新の状態になっている必要があります。 これらのコンポーネントに対する更新はプロバイダーが管理するため、コンテナー内の信頼のおけるソフトウェア コンポーネントが古くなっていることが原因で発生する構成証明のエラーをユーザーが解決する必要はありません。

-   EPC メモリをより効率的に使用できる。インプロセス構成証明モードでリモート構成証明を行う場合、各エンクレーブ アプリケーションが、QE と PCE のコピーをインスタンス化する必要があります。 アウトプロセスでは、それらのエンクレーブをコンテナーでホストする必要がないため、コンテナーのクォータからはエンクレーブのメモリが消費されません。

-   カーネル強制から保護することができる。SGX ドライバーが起動して Linux カーネルに読み込まれるとき、エンクレーブの特権が強制的に引き上げられます。 この特権によってエンクレーブは PCE を呼び出すことができ、インプロセス モードで実行されているエンクレーブ アプリケーションが中断されます。 既定では、エンクレーブは、この権限を受け取りません。 この特権をエンクレーブ アプリケーションに与えるためには、アプリケーションのインストール プロセスに変更を加える必要があります。 これは、アウトプロセス モデルであれば簡単に対処できます。アウトプロセス要求を処理するサービスのプロバイダーによって確実に、この特権と共にサービスがインストールされるためです。

-   PSW および DCAP との下位互換性のチェックが不要。 PSW のクォート生成コンポーネントに対する更新は、あらかじめプロバイダーによって下位互換性が確認されます。 そのため、前もって互換性の問題に対処し、解決したうえで、機密性の高いワークロードを対象に更新をデプロイすることができます。

## <a name="how-does-the-out-of-proc-attestation-mode-work-for-confidential-workloads-scenario"></a>機密性の高いワークロードのシナリオにおけるアウトプロセス構成証明モードの動作

全体的な設計は、クォートの要求側とクォートの生成側が同じ物理マシン上で別々に実行されるモデルとなっています。 クォートの生成は一元的に行われ、そこですべてのエンティティからのクォート要求が処理されます。 インターフェイスが適切に定義され、また、クォートを要求するどのエンティティからも検出可能であることが必要です。

![SGX クォート ヘルパー AESM](./media/confidential-nodes-out-of-proc-attestation/aesmmanager.png)

上の抽象モデルは、機密性の高いワークロードのシナリオを対象としたものです。あらかじめ用意された AESM サービスを利用しています。 AESM はコンテナー化され、Kubernetes クラスター全体にデーモンセットとしてデプロイされます。 AESM サービス コンテナーの 1 つのインスタンスがポッドにラップされ、各エージェント ノードにデプロイされることが Kubernetes によって保証されます。 新しい SGX クォート デーモンセットは、sgx-device-plugin デーモンセットに依存します。AESM サービス コンテナーは、QE と PCE のエンクレーブを起動するための EPC メモリを sgx-device-plugin に要求するためです。

アウトプロセスのクォート生成を使用するには、作成時に環境変数 **SGX_AESM_ADDR=1** を設定して、それぞれのコンテナーでオプトインする必要があります。 また、コンテナーには、既定の UNIX ドメイン ソケットに要求を誘導する役割を果たす libsgx-quote-ex パッケージが含まれている必要があります。

アプリケーションでは従来どおり、インプロセスの構成証明を使用することもできますが、あるアプリケーション内でインプロセスとアウトプロセスの両方を同時に使用することはできません。 アウトプロセス インフラストラクチャは既定で利用でき、リソースを消費します。

## <a name="sample-implementation"></a>実装例

以下の Docker ファイルは、Open Enclave ベースのアプリケーションのサンプルです。 環境変数 SGX_AESM_ADDR=1 を Docker ファイルで設定するか、デプロイ ファイルで設定します。 Docker ファイルとデプロイ yaml の詳細については、以下のサンプルに従ってください。 

  > [!Note] 
  > アウトプロセス構成証明を正しく機能させるためには、アプリケーション コンテナーに Intel の **libsgx-quote-ex** を含める必要があります。
    
```yaml
# Refer to Intel_SGX_Installation_Guide_Linux for detail
FROM ubuntu:18.04 as sgx_base
RUN apt-get update && apt-get install -y \
    wget \
    gnupg

# Add the repository to sources, and add the key to the list of
# trusted keys used by the apt to authenticate packages
RUN echo "deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main" | tee /etc/apt/sources.list.d/intel-sgx.list \
    && wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | apt-key add -
# Add Microsoft repo for az-dcap-client
RUN echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | tee /etc/apt/sources.list.d/msprod.list \
    && wget -qO - https://packages.microsoft.com/keys/microsoft.asc | apt-key add -

FROM sgx_base as sgx_sample
RUN apt-get update && apt-get install -y \
    clang-7 \
    libssl-dev \
    gdb \
    libprotobuf10 \
    libsgx-dcap-ql \
    libsgx-quote-ex \
    az-dcap-client \
    open-enclave
WORKDIR /opt/openenclave/share/openenclave/samples/remote_attestation
RUN . /opt/openenclave/share/openenclave/openenclaverc \
    && make build
# this sets the flag for out of proc attestation mode. alternatively you can set this flag on the deployment files
ENV SGX_AESM_ADDR=1 

CMD make run
```
また、以下のとおり、アウトプロセス構成証明モードをデプロイ yaml ファイルで設定することもできます。

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
spec:
  template:
    spec:
      containers:
      - name: sgxtest
        image: <registry>/<repository>:<version>
        env:
        - name: SGX_AESM_ADDR
          value: 1
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10
        volumeMounts:
        - name: var-run-aesmd
          mountPath: /var/run/aesmd
      restartPolicy: "Never"
      volumes:
      - name: var-run-aesmd
        hostPath:
          path: /var/run/aesmd
```

## <a name="next-steps"></a>次の手順
[AKS に機密ノード (DCsv2 シリーズ) をプロビジョニングする](./confidential-nodes-aks-get-started.md)

[機密コンテナーのクイック スタート サンプル](https://github.com/Azure-Samples/confidential-container-samples)

[DCsv2 SKU リスト](../virtual-machines/dcv2-series.md)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions
