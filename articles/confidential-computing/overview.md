---
title: Azure Confidential Computing の概要
description: Azure Confidential Computing (ACC) の概要
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: c3ef6a764123f52583f081a3b152651b9bb8b8b7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554192"
---
# <a name="confidential-computing-on-azure"></a>Azure におけるコンフィデンシャル コンピューティング

Azure Confidential Computing を使用すると、機密データがクラウドで処理されている間も、そのデータを隔離することができます。 多くの業界で、そのデータを保護するために、コンフィデンシャル コンピューティングを使用し、次のようなことが行われています。

- 財務データをセキュリティで保護する
- 患者の情報を保護する
- 機密情報に対する機械学習処理を実行する
- 複数のソースからの暗号化されたデータ セットに対してアルゴリズムを実行する


## <a name="overview"></a>概要
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

Microsoft は、お客様のクラウド データをセキュリティで保護することの重要性を認識しています。 お客様からの不安の声も届いています。 機密性の高いワークロードをクラウドに移すにあたってお客様が抱く可能性のある疑問を何点かだけ挙げてみます。 

- 暗号化されていないデータに Microsoft がアクセスできないことを確かめるには、どうすればよいか。
- 自社内の特権管理者からのセキュリティ上の脅威を防ぐにはどうすればよいか。
- 機密性の高い顧客データにサードパーティがアクセスできないようにするために、他にどのような方法があるか。

Microsoft Azure なら、攻撃面を最小限に抑え、より強固なデータ保護を実現するのに役立ちます。 Azure には、クライアント側暗号化やサーバー側暗号化などのモデルを使用して [**保存データ**](../security/fundamentals/encryption-atrest.md)を保護するさまざまなツールが既に用意されています。 さらに、TLS や HTTPS といった安全なプロトコルを通じて、[**転送中のデータ**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit)を暗号化するメカニズムも備わっています。 このページで紹介するのは、第 3 のデータ暗号化、つまり **使用中のデータ** の暗号化です。

## <a name="introduction-to-confidential-computing"></a>コンフィデンシャル コンピューティングの概要 

コンフィデンシャル コンピューティングは、コンフィデンシャル コンピューティングを定義し、その導入を促進することに特化した団体である [Confidential Computing Consortium](https://confidentialcomputing.io/) (CCC) によって定義された業界用語です。 CCC では、コンフィデンシャル コンピューティングを次のように定義しています。ハードウェアベースの信頼できる実行環境 (TEE) で計算を実行することによる、使用中のデータの保護。

TEE は、承認されたコードのみが実行されるよう強制する環境です。 TEE 内のデータを、その環境外のコードで読み取ったり改ざんしたりすることはできません。 

### <a name="lessen-the-need-for-trust"></a>信頼の必要性を軽減する
クラウドでワークロードを実行するには、信頼が必要です。 アプリケーションの各種のコンポーネントを有効にするさまざまなプロバイダーに、この信頼を付与します。


**アプリ ソフトウェア ベンダー**: オンプレミスにデプロイするか、オープンソースを使用するか、社内アプリケーション ソフトウェアを構築することで、ソフトウェアを信頼します。

**ハードウェア ベンダー**: オンプレミスのハードウェアまたは社内ハードウェアを使用することで、ハードウェアを信頼します。 

**インフラストラクチャ プロバイダー**: クラウド プロバイダーを信頼するか、独自のオンプレミスのデータ センターを管理します。


Azure のコンフィデンシャル コンピューティングを使用すると、コンピューティング クラウド インフラストラクチャのさまざまな側面にわたる信頼の必要性が軽減され、クラウド プロバイダーを信頼しやすくなります。 Azure のコンフィデンシャル コンピューティングにより、ホスト OS カーネル、ハイパーバイザー、VM 管理者、およびホスト管理者への信頼が最小限に抑えられます。

### <a name="reducing-the-attack-surface"></a>攻撃対象領域を縮小する
信頼済みのコンピューティング ベース (TCB) とは、セキュリティで保護された環境を提供する、システムのハードウェア、ファームウェア、およびソフトウェア コンポーネントのすべてのことです。 TCB 内のコンポーネントは、"クリティカル" と見なされます。 TCB 内の 1 つのコンポーネントが侵害された場合、システム全体のセキュリティが脅かされる可能性があります。 

TCB が低いほど、セキュリティが高いことを意味します。 さまざまな脆弱性、マルウェア、攻撃、および悪意のある人物にさらされるリスクが低下します。 Azure のコンフィデンシャル コンピューティングは、TEE を提供することで、クラウド ワークロードの TCB を低くすることを目的としています。 TEE を使用すると、信頼されているランタイム バイナリ、コード、およびライブラリに対する TCB を減らすことができます。 コンフィデンシャル コンピューティングに Azure のインフラストラクチャとサービスを使用する場合、すべての Microsoft を TCB から削除できます。


## <a name="using-azure-for-cloud-based-confidential-computing"></a>クラウドベースのコンフィデンシャル コンピューティングに Azure を使用する<a id="cc-on-azure"></a>

Azure Confidential Computing を使用すると、コンフィデンシャル コンピューティングの機能を仮想環境で活用することができます。 安全なハードウェア上で、ツール、ソフトウェア、クラウド インフラストラクチャを使用してビルドを行うことができます。  

**未承認のアクセスを禁止する**: 機密データをクラウドで実行します。 Azure が可能な限り最高のデータ保護を提供し、現在行われているものとほとんどまたはまったく変わらないことを信頼してください。

**法令遵守**:個人情報を保護する政府の規制を満たし、組織 IP をセキュリティで保護するために、クラウドに移行して、データの完全な制御を維持します。

**セキュリティで保護されるものと信頼されていないもののコラボレーション**: 広範なデータ分析と詳細な分析情報を可能にするために、競合他社も含む複数の組織にわたるデータを組み合わせることで、業界全体の作業規模の問題に取り組みます。

**分離された処理**: ブラインド処理によるプライベート データへの負担を除去する新しい機能の製品を提供します。 サービス プロバイダーさえ、ユーザー データを取得できません。 

## <a name="get-started"></a>はじめに
### <a name="azure-compute"></a>Azure コンピューティング
Azure のコンフィデンシャル コンピューティング IaaS サービス上にアプリケーションを構築します。
- 仮想マシン (VM): [DCsv2-Series](confidential-computing-enclaves.md)
- Azure Kubernetes (AKS): [コンフィデンシャル コンテナーの調整](confidential-nodes-aks-overview.md)

### <a name="azure-security"></a>Azure Security 
複数の検証方法とハードウェアバインド キー管理によってワークロードがセキュリティで保護されていることを確認します。 
- 構成証明: [Microsoft Azure Attestation (プレビュー)](../attestation/overview.md)
- キー管理: Managed-HSM (プレビュー)

### <a name="develop"></a>開発
エンクレーブ対応アプリケーションの開発を開始し、機密推論フレームワークを使用する機密アルゴリズムをデプロイします。
- DCsv2 VM 上で実行するアプリケーションを記述する: [Open-enclave SDK](https://github.com/openenclave/openenclave)
- ONNX ランタイムでの機密 ML モデル: [機密推論 (ベータ)](https://aka.ms/confidentialinference)

## <a name="next-steps"></a>次のステップ

DCsv2 シリーズの仮想マシンをデプロイし、そこに OE SDK をインストールします。

> [!div class="nextstepaction"]
> [Azure Marketplace でコンフィデンシャル コンピューティング VM をデプロイする](quick-create-marketplace.md)