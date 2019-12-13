---
title: Azure Change Tracking の問題を解決する
description: この記事では、Change Tracking のトラブルシューティングに関する情報を提供します
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 482b263b7c8427276d4fdb8681372c2be1ae69ee
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849396"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Change Tracking とインベントリのトラブルシューティング

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>シナリオ:Windows マシンの Change Tracking レコードが表示されません

#### <a name="issue"></a>問題

Change Tracking 用にオンボーディングされている Windows マシンのインベントリまたは Change Tracking の結果が表示されません。

#### <a name="cause"></a>原因

このエラーは次の理由で発生する可能性があります。

1. **Microsoft Monitoring Agent** が動作していません。
2. Automation アカウントに戻る通信がブロックされています。
3. Management Packs for Change Tracking がダウンロードされていません。
4. オンボードしている VM の複製元が、Microsoft Monitoring Agent がインストールされた状態で sysprep されなかった複製マシンであった可能性があります。

#### <a name="resolution"></a>解決策

1. **Microsoft Monitoring Agent** (HealthService.exe) がコンピューター上で実行されていることを確認します。
1. マシン上で**イベント ビューアー**をチェックして、`changetracking` という単語が含まれているイベントを探します。
1. [ネットワーク計画](../automation-hybrid-runbook-worker.md#network-planning)に関するセクションで、Change Tracking を動作させるために許可する必要があるアドレスとポートを確認します。
1. Change Tracking とインベントリの次の管理パックがローカルに存在することを確認します。
    * Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
    * Microsoft.IntelligencePacks.InventoryChangeTracking.*
    * Microsoft.IntelligencePacks.SingletonInventoryCollection.*
1. 複製イメージを使用する場合は、そのイメージを先に sysprep してから、Microsoft Monitoring Agent エージェントをインストールします。

以上の解決策で問題が解決されず、サポートに連絡する場合、次のコマンドを実行し、エージェントで診断データを収集します。

エージェント コンピューター上で `C:\Program Files\Microsoft Monitoring Agent\Agent\Tools` に移動し、次のコマンドを実行します。

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> 既定では、エラー トレースは有効になっています。前の例のような詳しいエラー メッセージを有効にするには、`VER` パラメーターを使用します。 情報トレースが必要であれば、`StartTracing.cmd` を呼び出すときに `INF` を使用します。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上します。
* さらにヘルプが必要であれば、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。
