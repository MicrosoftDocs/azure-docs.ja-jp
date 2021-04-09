---
title: Azure Blockchain Service の台帳のバージョン、修正プログラム適用、アップグレード
description: Azure Blockchain Service でサポートされている台帳のバージョンの概要です。 システム修正プログラムの適用やアップグレードに関するポリシーなど、Azure Blockchain Service でサポートされている台帳のバージョンの概要です。
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: ea7c54e229178320329204a3199ab2b4c44058fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85807742"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>サポートされている Azure Blockchain Service の台帳のバージョン

Azure Blockchain Service では、既知の参加者のグループ (Azure Blockchain Service ではコンソーシアムと呼ばれます) 内のプライベート トランザクションの処理用に設計されている Ethereum ベースの [Quorum](https://www.goquorum.com/developers) 台帳が使用されます。

現在、Azure Blockchain Service は [Quorum バージョン 2.6.0](https://github.com/jpmorganchase/quorum/releases/tag/v2.6.0) と [Tessera トランザクション マネージャー](https://github.com/jpmorganchase/tessera)をサポートしています。

## <a name="managing-updates-and-upgrades"></a>更新プログラムとアップグレードの管理

Quorum でのバージョン管理は、メジャー リリース、マイナー リリース、パッチ リリースを通じて行われます。 たとえば、Quorum のバージョンが 2.0.1 の場合、リリースの種類は次のように分類されます。

|メジャー | Minor  | 修正プログラム  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Azure Blockchain Service は、既存の実行中メンバーに対して、Quorum から入手可能になってから 30 日以内に、Quorum のパッチ リリースを自動的に更新します。

## <a name="availability-of-new-ledger-versions"></a>台帳の新しいバージョンの使用可能性

Azure Blockchain Service では、Quorum 台帳の最新のメジャーおよびマイナー バージョンは、Quorum の製造元で使用可能になってから 60 日以内に提供されます。 新しいメンバーとコンソーシアムをプロビジョニングするとき、コンソーシアムでは最大 4 つのマイナー リリースから選択することができます。 メジャーまたはマイナー リリースからの、またはそれらへのアップグレードは現在サポートされていません。 たとえば、バージョン 2.x を実行している場合、バージョン 3.x へのアップグレードは現在サポートされていません。 同様に、バージョン 2.2 を実行している場合、バージョン 2.3 へのアップグレードは現在サポートされていません。

## <a name="how-to-check-quorum-ledger-version"></a>Quorum 台帳のバージョンを確認する方法

Azure Blockchain Service メンバーの Quorum バージョンを確認するには、geth を使用してノードに接続するか、診断ログを表示します。

### <a name="using-geth"></a>geth を使用する

geth を使用して Azure Blockchain Service ノードに接続します。 たとえば、「 `geth attach https://myblockchainmember.blockchain.azure.com:3200/<Access key>` 」のように入力します。

ノードが接続されると、次の出力のように Quorum バージョンが geth によって報告されます。

``` text
instance: Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12
```

geth の使用方法の詳細については、「[クイック スタート:Geth を使用して Azure Blockchain Service のトランザクション ノードに接続する](connect-geth.md)」を参照してください。

### <a name="using-diagnostic-logs"></a>診断ログを使用する

診断ログを有効にすると、トランザクション ノードの Quorum バージョンが報告されます。 たとえば、次のノード情報ログ メッセージに、Quorum バージョンが記されています。

``` text 
{"NodeName":"transaction-node","Message":"INFO [06-22|05:31:45.156] Starting peer-to-peer node instance=Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12\n"}
{"NodeName":"transaction-node","Message":"[*] Starting Quorum node with QUORUM_VERSION=2.6.0, TESSERA_VERSION=0.10.5 and PRIVATE_CONFIG=/working-dir/c/tm.ipc\n"}
111
```

診断ログの詳細については、「[Azure Monitor を使用して Azure Blockchain Service を監視する](monitor-azure-blockchain-service.md#diagnostic-settings)」をご覧ください。

## <a name="how-to-check-genesis-file-content"></a>ジェネシス ファイルの内容を確認する方法

ブロックチェーン ノードのジェネシス ファイルの内容を確認するには、次の Ethereum JavaScript API を使用できます。

``` bash
admin.nodeInfo.protocols
```
この API は、geth コンソールまたは web3 ライブラリを使用して呼び出せます。 geth の使用方法の詳細については、「[クイック スタート:Geth を使用して Azure Blockchain Service のトランザクション ノードに接続する](connect-geth.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

[Azure Blockchain Service での制限事項](limits.md)
