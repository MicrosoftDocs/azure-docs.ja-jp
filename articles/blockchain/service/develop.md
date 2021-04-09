---
title: Azure Blockchain Service の開発の概要
description: Azure Blockchain Service でのソリューションの開発の概要です。
ms.date: 03/26/2020
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 4a98e5ea025894303bc5c77bba0c6154a08315f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "80348379"
---
# <a name="azure-blockchain-service-development-overview"></a>Azure Blockchain Service の開発の概要

Azure Blockchain Service では、コンソーシアム ブロックチェーン ネットワークを作成して、資産の追跡、デジタル トークン、ロイヤルティと報酬、サプライ チェーン ファイナンス、来歴などのエンタープライズ シナリオを有効にすることができます。 以下のセクションでは、エンタープライズ ブロックチェーン ソリューションを実装するための Azure Blockchain Service について紹介しています。

## <a name="connecting-to-azure-blockchain-service"></a>Azure Blockchain Service に接続する

ブロックチェーン ネットワークには、フル ノード、ライト ノード、リモート クライアントなど、さまざまな種類のクライアントがあります。 Azure Blockchain Service では、ノードを含むブロックチェーン ネットワークを構築します。 ブロックチェーン開発のための Azure Blockchain Service に対するゲートウェイとして、さまざなまなクライアントを使用できます。 Azure Blockchain Service では、開発エンドポイントとして基本認証またはアクセス キーが提供されます。 接続を使用できる一般的なクライアントは次のとおりです。

### <a name="visual-studio-code"></a>Visual Studio Code

Azure Blockchain Development Kit Visual Studio Code 拡張機能を使用して、コンソーシアム メンバーに接続できます。 コンソーシアムに接続したら、スマート コントラクトをコンパイル、ビルドし、Azure Blockchain Service コンソーシアム メンバーにデプロイできます。

高度なエンタープライズ ブロックチェーン ソリューションを開発するには、さまざまなブロックチェーン ネットワークに接続し、スマート コントラクトのライフサイクルを管理するための開発フレームワークが必要です。 ほとんどのプロジェクトで、少なくとも 2 つのブロックチェーン ノードとやり取りします。 開発者は開発中、ローカルのブロックチェーンを使用します。 アプリケーションのテストまたはリリースの準備ができたら、開発者はブロックチェーン ネットワークにデプロイします。 たとえば、メイン パブリック Ethereum ネットワークや Azure Blockchain Service などです。 Visual Studio Code の Azure Blockchain Development Kit for Ethereum 拡張機能では、Truffle を使用します。 Truffle は、Ethereum ブロックチェーンで分散アプリケーションを記述、コンパイル、デプロイ、テストするための人気のあるブロックチェーン開発フレームワークです。 Truffle は、スマート コントラクトの開発と従来の Web 開発をシームレスに統合するフレームワークと考えることもできます。

詳細については、「[クイック スタート: Visual Studio Code を使用して Azure Blockchain Service コンソーシアム ネットワークに接続する](connect-vscode.md)」を参照してください。

### <a name="metamask"></a>MetaMask

MetaMask は、ブラウザー ベースのウォレット (リモート クライアント)、RPC クライアント、および基本的なコントラクト エクスプローラーです。 他のブラウザー ウォレットとは異なり、MetaMask では web3 インスタンスがブラウザーの JavaScript コンテキストに挿入されて、さまざまな Ethereum ブロックチェーンに接続する RPC クライアントとして機能します (*mainnet*、*Ropsten testnet*、*Kovan testnet*、ローカル RPC ノードなど)。 簡単にカスタム RPC をセットアップして、Azure Blockchain Service に接続し、Remix を使用したブロックチェーン開発を始めることができます。

詳細については、「[MetaMask を使用してスマート コントラクトを接続およびデプロイする](connect-metamask.md)」を参照してください。

### <a name="geth"></a>Geth

Geth は、Go で実装された完全な Ethereum ノードを実行するためのコマンド ライン インターフェイスです。 フル ノードを実行する必要はありませんが、Azure Blockchain Service とやり取りするための JavaScript API が公開される JavaScript ランタイム環境を提供する、対話型コンソールを起動できます。

詳細については、「[クイック スタート:Geth を使用して Azure Blockchain Service のトランザクション ノードに接続する](connect-geth.md)」を参照してください。

## <a name="ethereum-quorum-private-transactions"></a>Ethereum Quorum のプライベート トランザクション

Quorum は、トランザクションに加えてコントラクトのプライバシーと新しいコンセンサス メカニズムを備えた、Ethereum ベースの分散型台帳プロトコルです。 Go-Ethereum に対する重要な機能強化としては次のものがあります。

* **プライバシー** - Quorum では、パブリックとプライベートの状態の分離によってプライベート トランザクションとプライベート コントラクトがサポートされ、ネットワーク参加者へのプライベート データの転送にはピア ツー ピア暗号化メッセージ交換が使用されます。
* **代替コンセンサス メカニズム** - 許可されたネットワークの場合、proof-of-work または proof-of-stake のコンセンサスは必要ありません。 Quorum では、RAFT や IBFT などのコンソーシアム チェーン用に設計された複数のコンセンサス メカニズムが提供されます。  Azure Blockchain Service では IBFT コンセンサス メカニズムが使用されます。
* **ピア アクセス許可** - スマート コントラクトを使用したノードとピアのアクセス許可により、既知のパーティだけがネットワークに参加できるようになります。
* **より高いパフォーマンス** - Quorum では、パブリック Geth より高いパフォーマンスが提供されます。

## <a name="block-explorers"></a>ブロック エクスプローラー

ブロック エクスプローラーはオンライン ブロックチェーン ブラウザーであり、個々のブロックの内容、トランザクション アドレス データ、履歴が表示されます。 基本的なブロック情報は、Azure Blockchain Service の Azure Monitor から入手できます。 しかしながら、開発中にもっと詳しい情報が必要になった場合、ブロック エクスプローラーが役立ちます。  次のブロック エクスプローラーは Azure Blockchain Service で動作します。

* [Epirus Azure Blockchain Service Explorer](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview) (Web3 Labs)
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

Blockchain Data Manager および Azure Cosmos DB を使用して独自のブロック エクスプローラーを構築することもできます。「[チュートリアル: Blockchain Data Manager を使用して Azure Cosmos DB にデータを送信する](data-manager-cosmosdb.md)」を参照してください。

## <a name="tps-measurement"></a>TPS の測定

エンタープライズ シナリオでのブロックチェーンの使用が増えており、ボトルネックとシステムの非効率性を回避するには、1 秒あたりのトランザクション数 (TPS) の速度が重要です。 分散型ブロックチェーン内では、高いトランザクション レートを維持するのが難しい場合があります。 正確な TPS の測定は、サーバー スレッド、トランザクション キューのサイズ、ネットワーク待機時間、セキュリティなどのさまざまな要因によって影響を受ける可能性があります。 開発中に TPS の速度を測定する必要がある場合は、オープンソース ツールの [ChainHammer](https://github.com/drandreaskrueger/chainhammer) がよく使用されます。

## <a name="next-steps"></a>次のステップ

Azure Blockchain Development Kit for Ethereum を使用して Azure Blockchain Service 上のコンソーシアムに接続するクイックスタートをお試しください。

> [!div class="nextstepaction"]
> [Visual Studio Code を使用して Azure Blockchain Service に接続する](connect-vscode.md)