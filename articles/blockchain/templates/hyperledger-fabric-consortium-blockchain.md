---
title: Azure の Hyperledger Fabric コンソーシアム
description: Hyperledger Fabric コンソーシアム ネットワークをデプロイおよび構成するソリューション テンプレート
services: azure-blockchain
keywords: ''
author: pataltimore
ms.author: patricka
ms.date: 05/09/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: caleteet
manager: femila
ms.openlocfilehash: 80de4e1479fac7296889e45289a5f20e586e3f57
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65510751"
---
# <a name="hyperledger-fabric-consortium-network"></a>Hyperledger Fabric コンソーシアム ネットワーク

Hyperledger Fabric コンソーシアム ソリューション テンプレートを使用して、Azure で Hyperledger Fabric コンソーシアム ネットワークをデプロイして構成できます。

この記事を読むと、次のことができます。

- ブロックチェーン、Hyperledger Fabric、より複雑なコンソーシアム ネットワーク アーキテクチャの実用的知識を得る
- Azure portal 内から Hyperledger Fabric コンソーシアム ネットワークをデプロイし、構成する方法を学習する

## <a name="about-blockchain"></a>ブロックチェーンの概要

このソリューション テンプレートは、ブロックチェーン コミュニティに参加したばかりの方に最適です。ぜひこの機会にこの技術を学習してください。Azure で構成可能な方法で楽に学習できます。 ブロックチェーンは Bitcoin の背後にある基礎技術ですが、仮想通貨を可能にするだけの技術ではありません。 既存のデータベース、分散システム、暗号化技術を合わせたものであり、マルチパーティ秘密計算 (Secure Multi-party Computation) を可能にします。不変性、検証可能性、監査可能性、攻撃に対する回復力が約束されます。 さまざまなプロトコルでさまざまなメカニズムを採用することで、このような特性が与えられます。 [Hyperledger Fabric](https://github.com/hyperledger/fabric) はこのようなプロトコルの 1 つです。

## <a name="consortium-architecture-on-azure"></a>Azure のコンソーシアム アーキテクチャ

Azure で Hyperledger Fabric を有効にするため、サポートされている 2 種類のプライマリ デプロイがあります。 これらのデプロイは、目的のターゲットに基づき、さまざまなトポロジに対応するために設計されています。

- **単一の仮想マシン、開発者用サーバー**: このデプロイの種類は、Hyperledger Fabric でビルドされたソリューションをビルドおよびテストするために使用する開発環境として設計されています。
- **複数の仮想マシン、デプロイのスケール アウト**: このデプロイの種類は、共有環境を活用するさまざまな参加者のコンソーシアムをモデル化する環境のために設計されています。

どちらのデプロイでも、Hyperledger Fabric の中核を構成する構成要素は同じです。  デプロイの違いは、これらのコンポーネントのスケール アウト方法にあります。

- **CA ノード**:ネットワーク内で ID に使用される証明書の生成に使用される証明機関を実行するノード。
- **Orderer ノード**:トータル オーダー ブロードキャストやアトミック トランザクションなど、配信保証を実装する通信サービスを実行するノード。
- **ピア ノード**:トランザクションをコミットし、分散型台帳の状態とコピーを保守管理するノード。
- **CouchDB ノード**:状態データベースを保持でき、チェーンコード データの高度なクエリを提供できる CouchDB サービスを実行できるノード。単純なキー/値から JSON 型ストレージに展開します。

### <a name="single-virtual-machine-architecture"></a>単一の仮想マシン アーキテクチャ

前述したように、単一の仮想マシン アーキテクチャは、開発者がアプリケーションの開発にメモリ占有領域の低いサーバーを使用できるように構築されています。 表示されるすべてのコンテナーは、単一の仮想マシンで実行されています。 ordering サービスはこの構成に対して [SOLO](https://github.com/hyperledger/fabric/tree/master/orderer) を使用しています。 この構成はフォールト トレラントな ordering サービスでは*ありません*が、開発目的のため、軽量になるように設計されています。

![単一の仮想マシン アーキテクチャ](./media/hyperledger-fabric-consortium-blockchain/hlf-single-arch.png)

### <a name="multiple-virtual-machine-architecture"></a>複数の仮想マシン アーキテクチャ

複数の仮想マシンのスケール アウト アーキテクチャは、高可用性を持つように構築され、中心で各コンポーネントをスケーリングします。 このアーキテクチャは、運用環境グレードのデプロイにはるかに適しています。

![複数の仮想マシン アーキテクチャ](./media/hyperledger-fabric-consortium-blockchain/hlf-multi-arch.png)

## <a name="getting-started"></a>使用の開始

開始するには、複数の仮想マシンと標準のストレージ アカウントをデプロイできる Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合、[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成できます。

サブスクリプションを得たら、[Azure Portal](https://portal.azure.com) に進みます。 **[リソースの作成]、[ブロック チェーン]、[Hyperledger Fabric コンソーシアム]** の順に選択します。

![Hyperledger Fabric シングル メンバー ブロックチェーン Marketplace テンプレート](./media/hyperledger-fabric-consortium-blockchain/marketplace-template.png)

## <a name="deployment"></a>Deployment

**Hyperledger Fabric コンソーシアム** テンプレートで、 **[作成]** を選択します。

テンプレート デプロイでは、マルチノード [Hyperledger 1.3](https://hyperledger-fabric.readthedocs.io/en/release-1.3/) ネットワークを段階的に構成できます。 デプロイ フローは、基本、コンソーシアム ネットワーク設定、Fabric 構成、およびオプションのコンポーネントの 4 つの手順に分割されます。

### <a name="basics"></a>基本

**基本** では、任意のデプロイの標準パラメーターの値を指定します。 たとえば、サブスクリプション、リソース グループ、基本仮想マシンのプロパティを指定します。

![基本](./media/hyperledger-fabric-consortium-blockchain/basics.png)

| パラメーター名 | 説明 | 使用できる値 |
|---|---|---|
**[リソース プレフィックス]** | デプロイの一部としてプロビジョニングされたリソースにプレフィックスを指定します |6 文字以下 |
**ユーザー名** | このメンバーに対してデプロイされている各仮想マシンの管理者のユーザー名 |1 から 64 文字 |
**認証の種類** | 仮想マシンに対して認証する方法 |[パスワード] または [SSH 公開キー]|
**[パスワード] ([認証の種類] = [パスワード])** |デプロイされた各仮想マシンの管理者アカウントのパスワード。 パスワードには、小文字、大文字、数字、特殊文字の 4 種類のうち 3 種類を使用する必要があります<br /><br />VM にはすべて、最初の段階で同じパスワードが与えられます。プロビジョニング後にそのパスワードを変更できます|12 から 72 文字|
**SSH キー ([認証の種類] = [SSH 公開キー])** |リモート ログインに使用される Secure Shell キー ||
**サブスクリプション** |デプロイ対象のサブスクリプション ||
**リソース グループ** |コンソーシアム ネットワークをデプロイするリソース グループ ||
**場所** |最初のメンバーをデプロイする Azure リージョン ||

**[OK]** を選択します。

### <a name="consortium-network-settings"></a>コンソーシアム ネットワークの設定

**ネットワークの設定**で、コンソーシアム ネットワークを作成または既存のものに参加するための入力を指定し、組織の設定を構成します。

![コンソーシアム ネットワークの設定](./media/hyperledger-fabric-consortium-blockchain/network-settings.png)

| パラメーター名 | 説明 | 使用できる値 |
|---|---|---|
**ネットワーク構成** |新しいネットワークを作成することも、既存のネットワークに参加することもできます。 *[Join Existing]\(既存に参加\)* を選択した場合、追加の値を指定する必要があります。 |新しいネットワーク <br/> 既存に参加 |
**HLF CA パスワード** |デプロイの一部として作成される証明機関によって生成された証明書に使用するパスワード。 パスワードには、小文字、大文字、数字、特殊文字の 4 種類のうち 3 種類を使用する必要があります。<br /><br />仮想マシンにはすべて、最初の段階で同じパスワードが与えられます。プロビジョニング後にそのパスワードを変更できます。|1 から 25 文字 |
**組織のセットアップ** |組織の名前と証明書をカスタマイズしたり、使用する既定値を指定することができます。|既定値 <br/> 詳細 |
**VPN ネットワークの設定** | VM にアクセスするための VPN トンネル ゲートウェイをプロビジョニングします。 | はい <br/> いいえ |

**[OK]** を選択します。

### <a name="fabric-specific-settings"></a>Fabric 固有の設定

**Fabric 構成**では、ネットワークのサイズとパフォーマンスを構成し、ネットワークの可用性に対する入力を指定します。 これには、数 orderer ノードとピア ノード、各ノードで使用される永続化エンジン、VM のサイズなどが含まれます。

![Fabric 設定](./media/hyperledger-fabric-consortium-blockchain/fabric-specific-settings.png)

| パラメーター名 | 説明 | 使用できる値 |
|---|---|---|
**スケールの種類** |複数のコンテナーを使用した単一の仮想マシン、またはスケール アウト モデルで複数のコンテナーまたは複数の仮想マシンのいずれかのデプロイの種類。|単一の VM または複数の VM |
**VM ディスクの種類** |デプロイされた各ノードをバックアップするストレージの種類。 <br/> 使用できるディスクの種類の詳細については、[ディスクの種類の選択](../../virtual-machines/windows/disks-types.md)に関する記事を参照してください。|Standard SSD <br/> Premium SSD |

### <a name="multiple-vm-deployment-additional-settings"></a>複数の VM のデプロイ (追加の設定)

![複数の VM デプロイのファブリック設定](./media/hyperledger-fabric-consortium-blockchain/multiple-vm-deployment.png)

| パラメーター名 | 説明 | 使用できる値 |
|---|---|---|
**orderer ノードの数** |トランザクションを整理してブロックにするノードの数。 <br />ordering サービスの詳細については、Hyperledge の[ドキュメント](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html)をご覧ください。 |1-4 |
**orderer ノードの仮想マシンのサイズ** |ネットワーク内の orderer ノードで使用される仮想マシンのサイズ|Standard Bs、<br />Standard Ds、<br />Standard FS |
**ピア ノードの数** | トランザクションを実行し、台帳の状態とコピーを保守管理するノード。コンソーシアム メンバーが所有しています。<br />ordering サービスの詳細については、[Hyperledge 文書](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html)をご覧ください。|1-4 |
**ノード状態の永続性** |ピア ノードで使用される永続化エンジン。 ピア ノードごとにこのエンジンを構成することができます。 複数のピア ノードについては、以下の詳細を参照してください。|CouchDB <br />LevelDB |
**ピア ノードの仮想マシンのサイズ** |ネットワークの全ノードで使用される仮想マシンのサイズ|Standard Bs、<br />Standard Ds、<br />Standard FS |

### <a name="multiple-peer-node-configuration"></a>複数のピア ノード構成

このテンプレートでは、ピア ノードごとに永続化エンジンを選択できます。 たとえば、3 つのピア ノードがある場合、1 つのノードで CouchDB を使用して、他の 2 つのノードで LevelDB を使用することができます。

![複数のピア ノード構成](./media/hyperledger-fabric-consortium-blockchain/multiple-peer-nodes.png)

**[OK]** を選択します。

### <a name="deploy"></a>デプロイ

**[概要]** で、指定した入力を確認し、基本的なデプロイ前検証を実行します。

![まとめ](./media/hyperledger-fabric-consortium-blockchain/summary.png)

法律条項とプライバシー条項を確認し、 **[購入]** を選択してデプロイします。 デプロイ時間は、プロビジョニングされる VM の数に基づき、数分から数十分かかります。

## <a name="next-steps"></a>次の手順

これで、Hyperledger コンソーシアム ブロックチェーン ネットワークに対するアプリケーションとチェーンコードの開発に集中的に取り組むことができます。
