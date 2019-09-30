---
title: Azure Blockchain Workbench プレビューのトラブルシューティング
description: Azure Blockchain Workbench プレビュー アプリケーションのトラブルシューティング方法。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 8fec065b629f2f2b93e78a63521ea0ce4669dd4e
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844024"
---
# <a name="azure-blockchain-workbench-preview-troubleshooting"></a>Azure Blockchain Workbench プレビューのトラブルシューティング

PowerShell スクリプトを利用すると、開発時のデバッグやサポートに役立ちます。 トラブルシューティングのために、スクリプトで概要を生成し、詳細なログを収集することができます。 収集されるログの内容は次のとおりです。

* Ethereum などのブロックチェーン ネットワーク
* Blockchain Workbench マイクロサービス
* Application Insights
* Azure 監視 (Azure Monitor ログ)

この情報を利用して、次の手順を決定し、問題の根本原因を特定することができます。

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="troubleshooting-script"></a>トラブルシューティング スクリプト

PowerShell のトラブルシューティング スクリプトは、GitHub で入手できます。 [ZIP ファイルをダウンロード](https://github.com/Azure-Samples/blockchain/archive/master.zip)するか、GitHub からサンプルを複製します。

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>スクリプトを実行する
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

`collectBlockchainWorkbenchTroubleshooting.ps1` スクリプトを実行してログを収集し、トラブルシューティング情報のフォルダーを含む ZIP ファイルを作成します。 例:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
このスクリプトは、次のパラメーターを受け取ります。

| パラメーター  | 説明 | 必須 |
|---------|---------|----|
| SubscriptionID | すべてのリソースを作成または特定する サブスクリプション ID。 | はい |
| ResourceGroupName | Blockchain Workbench がデプロイされている Azure リソース グループの名前。 | はい |
| OutputDirectory | 出力 .ZIP ファイルを作成するパス。 指定しない場合、既定は現在のディレクトリになります。 | いいえ |
| LookbackHours | 利用統計情報を取得する際に使用する時間数。 既定値は 24 時間です。 最大値は 90 時間です | いいえ |
| OmsSubscriptionId | Azure Monitor ログのデプロイ先のサブスクリプション ID。 ブロックチェーン ネットワークの Azure Monitor ログが Blockchain Workbench のリソース グループ以外にデプロイされている場合にのみ、このパラメーターを渡します。| いいえ |
| OmsResourceGroup |Azure Monitor ログのデプロイ先のリソース グループ。 ブロックチェーン ネットワークの Azure Monitor ログが Blockchain Workbench のリソース グループ以外にデプロイされている場合にのみ、このパラメーターを渡します。| いいえ |
| OmsWorkspaceName | Log Analytics ワークスペース名。 ブロックチェーン ネットワークの Azure Monitor ログが Blockchain Workbench のリソース グループ以外にデプロイされている場合にのみ、このパラメーターを渡します | いいえ |

## <a name="what-is-collected"></a>収集される内容

出力 ZIP ファイルのフォルダー構造は次のとおりです。

| フォルダーまたはファイル | 説明  |
|---------|---------|
| \Summary.txt | システムの概要 |
| \Metrics\blockchain | ブロックチェーンに関するメトリック |
| \Metrics\Workbench | ワークベンチに関するメトリック |
| \Details\Blockchain | ブロックチェーンに関する詳細なログ |
| \Details\Workbench | ワークベンチに関する詳細なログ |

概要ファイルは、アプリケーションの全体的な状態とアプリケーションの正常性のスナップショットです。 この概要から、推奨されるアクション、主なエラー、実行中のサービスに関するメタデータがわかります。

**Metrics** フォルダーには、時間の経過に伴う各種システム コンポーネントのメトリックが格納されます。 たとえば、出力ファイル `\Details\Workbench\apiMetrics.txt` には、さまざまな応答コードと収集期間全体の応答時間の概要が格納されます。 **Details** フォルダーには、Workbench または基盤のブロックチェーン ネットワークの特定の問題のトラブルシューティングのための詳細なログが格納されます。 たとえば、`\Details\Workbench\Exceptions.csv` にはシステムで発生した最新の例外の一覧が格納されますが、これはスマート コントラクトやブロックチェーンとの相互作用のエラーのトラブルシューティングに役立ちます。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench Application Insights トラブルシューティング ガイド](https://aka.ms/workbenchtroubleshooting)
