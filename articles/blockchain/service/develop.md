---
title: Azure Blockchain Service の開発の概要
description: Azure Blockchain Service でのソリューションの開発の概要です。
services: azure-blockchain
keywords: ''
author: pataltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 388a5d8c80c3e2462602959e9d5cbc1452974d1f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027617"
---
# <a name="azure-blockchain-service-development-overview"></a>Azure Blockchain Service の開発の概要

Azure Blockchain Service では、コンソーシアム ブロックチェーン ネットワークを作成して、資産の追跡、デジタル トークン、ロイヤルティと報酬、サプライ チェーン ファイナンス、来歴などのエンタープライズ シナリオを有効にすることができます。 この記事では、Azure Blockchain Service の開発の概要と、企業向けのブロックチェーンの実装に関する主要なトピックについて説明します。

## <a name="client-connection-to-azure-blockchain-service"></a>Azure Blockchain Service へのクライアント接続

ブロックチェーン ネットワークには、フル ノード、ライト ノード、リモート クライアントなど、さまざまな種類のクライアントがあります。 Azure Blockchain Service では、ノードを含むブロックチェーン ネットワークを構築します。 ブロックチェーン開発のための Azure Blockchain Service に対するゲートウェイとして、さまざなまなクライアントを使用できます。 Azure Blockchain Service では、開発エンドポイントとして基本認証またはアクセス キーが提供されます。 接続を使用できる一般的なクライアントは次のとおりです。

### <a name="metamask"></a>MetaMask

MetaMask は、ブラウザー ベースのウォレット (リモート クライアント)、RPC クライアント、および基本的なコントラクト エクスプローラーです。 他のブラウザー ウォレットとは異なり、MetaMask では web3 インスタンスがブラウザーの JavaScript コンテキストに挿入されて、さまざまな Ethereum ブロックチェーンに接続する RPC クライアントとして機能します (*mainnet*、*Ropsten testnet*、*Kovan testnet*、ローカル RPC ノードなど)。 簡単にカスタム RPC をセットアップして、Azure Blockchain Service に接続し、Remix を使用したブロックチェーン開発を始めることができます。

### <a name="geth"></a>Geth

Geth は、Go で実装された完全な Ethereum ノードを実行するためのコマンド ライン インターフェイスです。 フル ノードを実行する必要はありませんが、Azure Blockchain Service とやり取りするための JavaScript API が公開される JavaScript ランタイム環境を提供する、対話型コンソールを起動できます。

## <a name="development-framework-configuration"></a>開発フレームワークの構成

高度なエンタープライズ ブロックチェーン ソリューションを開発するには、さまざまなブロックチェーン ネットワークに接続し、スマート コントラクトのライフサイクルを管理し、テストを自動化し、スクリプトを使用してスマート コントラクトをデプロイし、対話型コンソールを装備する、開発フレームワークが必要です。

Truffle は、Ethereum ブロックチェーンで分散アプリケーションを記述、コンパイル、デプロイ、テストするための人気のあるブロックチェーン開発フレームワークです。 Truffle は、スマート コントラクトの開発と従来の Web 開発をシームレスに統合するフレームワークと考えることもできます。

最も小規模なプロジェクトでも、少なくとも 2 つのブロックチェーン ノードとやり取りします。1 つは開発者のコンピューター上にあり、もう 1 つは開発者がそのアプリケーションをデプロイするネットワークを表します。 たとえば、メイン パブリック Ethereum ネットワークや Azure Blockchain Service などです。 Truffle では、各ネットワークのコンパイルとデプロイの成果物を、最終的なアプリケーションのデプロイが簡単になる方法で管理するためのシステムが提供されます。 詳細については、「[クイック スタート: Use Truffle to connect to a an Azure Blockchain Service network (Truffle を使用して Azure Blockchain Service ネットワークに接続する)](connect-truffle.md)」をご覧ください。

## <a name="ethereum-quorum-private-transaction"></a>Ethereum Quorum のプライベート トランザクション

Quorum は、トランザクションに加えてコントラクトのプライバシーと新しいコンセンサス メカニズムを備えた、Ethereum ベースの分散型台帳プロトコルです。 Go-Ethereum に対する重要な機能強化としては次のものがあります。

* プライバシー - Quorum では、パブリックとプライベートの状態の分離によってプライベート トランザクションとプライベート コントラクトがサポートされ、ネットワーク参加者へのプライベート データの転送にはピア ツー ピア暗号化メッセージ交換が使用されます。
* 代替コンセンサス メカニズム - 許可されたネットワークでは proof-of-work または proof-of-stake のコンセンサスは必要ありません。 Quorum では、RAFT や IBFT などのコンソーシアム チェーン用に設計された複数のコンセンサス メカニズムが提供されます。  Azure Blockchain Service では IBFT コンセンサス メカニズムが使用されます。

* ピア アクセス許可 - 既知のパーティだけがネットワークに参加できることが保証される、スマート コントラクトを使用したノードとピアのアクセス許可です
* より高いパフォーマンス - Quorum では、パブリック Geth より高いパフォーマンスが提供されます

手順については、「[チュートリアル: Send a transaction using Azure Blockchain Service (チュートリアル: Azure Blockchain Service を使用してトランザクションを送信する)](send-transaction.md)」でプライベート トランザクションの例をご覧ください。

## <a name="block-explorers"></a>ブロック エクスプローラー

ブロック エクスプローラーはオンライン ブロックチェーン ブラウザーであり、個々のブロックの内容、トランザクション アドレス データ、履歴が表示されます。 ブロックの基本的な情報は Azure Blockchain Service 内の Azure Monitor によって入手できますが、開発時にさらに詳しい情報が必要な場合は、ブロック エクスプローラーが役に立ちます。  人気のあるオープンソースのブロック エクスプローラーを使用できます。 Azure Blockchain Service で動くブロック エクスプローラーの一覧を次に示します。

* [Azure Blockchain Service Explorer](https://web3labs.com/azure-offer) (Web3 Labs)
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

## <a name="tps-measurement"></a>TPS の測定

エンタープライズ シナリオでのブロックチェーンの使用が増えており、ボトルネックとシステムの非効率性を回避するには、1 秒あたりのトランザクション数 (TPS) の速度が重要です。 分散型ブロックチェーン内では、高いトランザクション レートを維持するのが難しい場合があります。 正確な TPS の測定は、サーバー スレッド、トランザクション キューのサイズ、ネットワーク待機時間、セキュリティなどのさまざまな要因によって影響を受ける可能性があります。 開発中に TPS の速度を測定する必要がある場合は、オープンソース ツールの [ChainHammer](https://github.com/drandreaskrueger/chainhammer) がよく使用されます。

## <a name="next-steps"></a>次の手順

[クイック スタート:Truffle を使用して Azure Blockchain Service ネットワークに接続する](connect-truffle.md)
