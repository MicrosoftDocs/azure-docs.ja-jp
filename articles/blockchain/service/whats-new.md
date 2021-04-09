---
title: 新機能 リリース ノート - Azure Blockchain Service
description: 最新のリリース ノート、バージョン、既知の問題、今後の変更点など、Azure Blockchain Service の新機能について説明します。
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 6a3113a2d28e704b188d701da13493ecd8263cab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94335028"
---
# <a name="whats-new-in-azure-blockchain-service"></a>Azure Blockchain Service の新機能

> URL `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Blockchain+Service%22&locale=en-us` をコピーして、ご利用の RSS フィード リーダー [![[RSS フィード リーダー] アイコン](./media/whats-new/feed-icon-16x16.png)](/api/search/rss?locale=en-us&search=%2522Release%2bnotes%2b-%2bAzure%2bBlockchain%2bService%2522) に貼り付け、更新内容を確認するためにこのページに再度アクセスするタイミングに関する通知を受け取るようにしてください。

Azure Blockchain Service は、継続的に改善されています。 常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

- 新機能
- バージョンのアップグレード
- 既知の問題

---

## <a name="june-2020"></a>2020 年 6 月

### <a name="version-upgrades"></a>バージョンのアップグレード

- Quorum のバージョンを 2.6.0 にアップグレード。 バージョン 2.6.0 では、署名されたプライベート トランザクションを送信できます。 プライベート トランザクションの送信方法の詳細については、[Quorum API のドキュメント](https://docs.goquorum.consensys.net/en/latest/Reference/APIs/ContractExtensionAPIs/#apis)を参照してください。
- Tessera のバージョンを 0.10.5 にアップグレード。

### <a name="contract-size-and-transaction-size-increased-to-128-kb"></a>コントラクトのサイズとトランザクションのサイズが 128 KB に増えました

型: 構成の変更

より大きなサイズのスマート コントラクトをデプロイできるように、コントラクトのサイズ (MaxCodeSize) が 128 KB に増えました。 また、トランザクション サイズ (txnSizeLimit) が 128 KB に増えました。 2020 年 6 月 19 日以降に Azure Blockchain Service 上で作成された新しいコンソーシアムに、構成の変更が適用されます。

### <a name="trietimeout-value-reduced"></a>TrieTimeout 値が縮小されました

型: 構成の変更

メモリ内の状態がディスクにより頻繁に書き込まれるように、TrieTimeout 値が小さくなりました。 値が小さいほど、ノードのクラッシュというまれなケースで、ノードの回復が確実に速くなります。

## <a name="may-2020"></a>2020 年 5 月

### <a name="version-upgrades"></a>バージョンのアップグレード

- Ubuntu のバージョンを 18.04 にアップグレード
- Quorum のバージョンを 2.5.0 にアップグレード
- Tessera のバージョンを 0.10.4 にアップグレード

### <a name="azure-blockchain-service-supports-sending-rawprivate-transactions"></a>Azure Blockchain Service では、rawPrivate トランザクションの送信がサポートされています

型: 特徴量

お客様はノードのアカウントの外部でプライベート トランザクションに署名できます。

### <a name="two-phase-member-provisioning"></a>2 フェーズ メンバー プロビジョニング

型: 拡張機能

2 フェーズでは、既存の長いコンソーシアムでメンバーを作成するというシナリオを容易に最適化できます。 最初のフェーズでは、メンバー インフラストラクチャがプロビジョニングされます。 2 番目のフェーズでは、メンバーがブロックチェーンと同期されます。 2 フェーズ プロビジョニングは、タイムアウトによるメンバー作成の失敗を回避するのに役立ちます。

## <a name="known-issues"></a>既知の問題

### <a name="ethestimategas-function-throws-exception-in-quorum-v260"></a>Quorum v2.6.0 で、eth.estimateGas 関数から例外がスローされます。

Quorum v2.6.0 では、追加の *value* パラメーターを指定せずに *eth.estimateGas* 関数を呼び出すと、例外 *method handler crashed* が発生します。 Quorum チームには通知が届いており、2020 年 7 月の終わりに修正プログラムが提供される予定です。 修正プログラムが利用可能になるまで、次の回避策を使用できます。

- パフォーマンスに影響を与える可能性があるため、*eth.estimateGas* の使用は避けてください。 eth.estimateGas のパフォーマンスに関する問題の詳細については、「[eth.estimateGas 関数を呼び出すとパフォーマンスが低下する](#calling-ethestimategas-function-reduces-performance)」を参照してください。 各トランザクションの gas 値を含めます。 gas 値が指定されていない場合、ほとんどのライブラリでは eth.estimateGas が呼び出され、それが原因で Quorum v2.6.0 がクラッシュします。
- *eth.estimateGas* を呼び出す必要がある場合、回避策として追加のパラメーター *value* を *0* として渡すことを Quorum チームはお勧めしています。

### <a name="mining-stops-if-fewer-than-four-validator-nodes"></a>検証ノードが 4 つ未満の場合、マイニングは停止する

実稼働ネットワークには、少なくとも 4 つの検証ノードを用意する必要があります。 IBFT クラッシュ フォールト トレランス (3F+1) を満たすためには、少なくとも 4 つの検証ノードが必要であると、Quorum は助言しています。 4 つの検証ノードを取得するには、Azure Blockchain Service の *Standard* レベル ノードを少なくとも 2 つ備える必要があります。 標準ノードは、2 つの検証ノードを使用してプロビジョニングされます。  

Azure Blockchain Service 上のブロック チェーン ネットワークに 4 つの検証ノードがない場合、ネットワーク上でマイニングが停止する可能性があります。 マイニングが停止したことを検出するには、処理するブロックに対してアラートを設定します。 正常なネットワークでは、5 分ごとにノードあたり 60 のブロックが処理されます。

軽減策として、Azure Blockchain Service チームはノードを再起動する必要があります。 お客様は、ノードを再起動するためのサポート リクエストを作成する必要があります。 Azure Blockchain Service チームは、マイニングの問題の自動的な検出および修正に取り組んでいます。

運用グレードのデプロイには、*Standard* レベルを使用します。 開発、テスト、概念実証には、*Basic* レベルを使用します。 メンバーの作成後に価格レベルを Basic と Standard の間で変更することはできません。

### <a name="blockchain-data-manager-requires-standard-tier-node"></a>Blockchain Data Manager には Standard レベル ノードが必要

Blockchain Data Manager を使用する場合は、*Standard* レベルを使用します。 *Basic* レベルには 4 GB のメモリしかありません。 そのため、Blockchain Data Manager およびこれで実行されているその他のサービスに必要な使用量にスケーリングすることはできません。

開発、テスト、概念実証には、*Basic* レベルを使用します。 メンバーの作成後に価格レベルを Basic と Standard の間で変更することはできません。

### <a name="large-volume-of-unlock-account-calls-causes-geth-to-crash"></a>アカウントのロック解除呼び出しを大量に行うと geth がクラッシュする

トランザクションの送信中にアカウントのロック解除呼び出しを大量に行うと、トランザクション ノード上で geth がクラッシュする可能性があります。 その結果、取り込みトランザクションを停止する必要があります。 そうしなければ、保留中のトランザクション キューが増大します。

geth は、1 分以内に自動的に再起動されます。 ノードによっては、同期に時間がかかることがあります。 Azure Blockchain Service チームは、同期の所要時間を短縮するアーカイブ機能を有効にしています。

geth のクラッシュを特定するには、アプリケーション ログの Blockchain メッセージ内でエラー メッセージのログを確認してください。 処理されるブロックが減少する一方で、保留中のトランザクションが増えていないかどうかも確認できます。

この問題を軽減するには、アカウントのロックを解除するコマンドで、署名なしのトランザクションを送信するのでなく、署名されたトランザクションを送信します。 既に外部で署名されているトランザクションの場合、アカウントのロックを解除する必要はありません。

署名なしのトランザクションを送信する場合は、unlock コマンド内の time パラメーターとして 0 を送信することで、無期限にアカウントのロックを解除します。 すべてのトランザクションが送信された後で、アカウントを再びロックすることができます。  

Azure Blockchain サービスが使用する geth パラメーターを次に示します。 これらのパラメーターを調整することはできません。

- イスタンブール ブロック期間: 5 秒
- フロア ガスの制限: 700000000
- シール ガスの制限: 800000000

### <a name="large-volume-of-private-transactions-reduces-performance"></a>大量のプライベート トランザクションによってパフォーマンスが低下する

Azure Blockchain Service の Basic レベルとプライベート トランザクションを使用している場合、Tessera がクラッシュすることがあります。

Tessera のクラッシュを検出するには、Blockchain アプリケーション ログを確認し、メッセージ `Tessera crashed. Restarting Tessera...` を検索します。

クラッシュが発生すると、Azure Blockchain Service によって Tessera が再起動されます。 再起動には約 1 分かかります。

大量のプライベート トランザクションを送信する場合は、*Standard* レベルを使用します。 開発、テスト、概念実証には、*Basic* レベルを使用します。 メンバーの作成後に価格レベルを Basic と Standard の間で変更することはできません。

### <a name="calling-ethestimategas-function-reduces-performance"></a>eth.estimateGas 関数を呼び出すとパフォーマンスが低下する

*eth.estimateGas* を複数回呼び出すと、1 秒あたりのトランザクションが大幅に削減されます。 各トランザクションの送信には、*eth.estimateGas* 関数を使用しないでください。 *eth.estimateGas* 関数は大量のメモリを使用します。

可能であれば、トランザクションの送信には控えめな gas 値を使用し、*eth.estimateGas* の使用を最小限に抑えてください。

### <a name="unbounded-loops-in-smart-contracts-reduces-performance"></a>スマート コントラクト内の境界のないループによってパフォーマンスが低下する

スマート コントラクト内では境界のないループを避けてください。パフォーマンスが低下する原因となるからです。 詳細については、次のリソースを参照してください。

- [境界のないループを避ける](https://blog.b9lab.com/getting-loopy-with-solidity-1d51794622ad )
- [スマート コントラクトのセキュリティに関するベスト プラクティス](https://github.com/ConsenSys/smart-contract-best-practices)
- [Quorum によって提供されるスマート コントラクトのガイドライン](https://docs.goquorum.consensys.net/en/stable/Concepts/Security/Framework/DecentralizedApplication/SmartContractsSecurity/)
- [Solidity によって提供される gas の制限とループに関するガイドライン](https://solidity.readthedocs.io/en/develop/security-considerations.html#gas-limit-and-loops)