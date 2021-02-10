---
title: ローカル セッション マネージャー サービスの待機中には VM が応答しない
description: この記事では、Azure VM の起動中に、ローカル セッション マネージャーが処理を完了するのを待ってゲスト OS がスタックする問題を解決する手順について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/22/2020
ms.author: v-mibufo
ms.openlocfilehash: fc3bd5d2590e969db07e9dffa61b4902ea4604c3
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632548"
---
# <a name="vm-is-unresponsive-while-waiting-for-the-local-session-manager-service"></a>ローカル セッション マネージャー サービスの待機中には VM が応答しない

この記事では、Azure 仮想マシン (VM) の起動中に、ローカル セッション マネージャーが処理を完了するのを待ってゲスト オペレーティング システム (ゲスト OS) がスタックする問題を解決する手順について説明します。

## <a name="symptoms"></a>現象

[ブート診断](./boot-diagnostics.md)を使用して VM の出力のスクリーンショットを表示すると、スクリーンショットのプロンプトに、"ローカル セッション マネージャーを待機してください" というメッセージが表示されることがわかります。

!["ローカル セッション マネージャーを待機してください" というメッセージでスタックした、Windows Server 2012 R2 でのゲスト OS を示すスクリーンショット。](media/vm-unresponsive-wait-local-session-manager/vm-unresponsive-wait-local-session-manager-1.png)

## <a name="cause"></a>原因

ローカル セッション マネージャーを待機して VM がスタックする理由は複数考えられます。 この問題が引き続き発生する場合は、分析のためにメモリ ダンプを収集する必要があります。

## <a name="solution"></a>解決策

> [!TIP]
> VM の最新のバックアップがある場合は、[そのバックアップから VM の復元](../../backup/backup-azure-arm-restore-vms.md)を試みて、起動の問題を修正できます。

プロセスが終了するのを待つだけで問題が解消する場合もあります。 VM が応答せず、1 時間を超えても待機画面のままである場合は、メモリ ダンプを収集した後、Microsoft サポートに連絡してください。

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>新しい修復 VM に OS ディスクを接続する

1. 修復 VM を準備するには、[VM 修復コマンド](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)のステップ 1 から 3 に従います。
1. リモート デスクトップ接続を使用して修復 VM に接続します。

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>ダンプ ファイルを探してサポート チケットを送信する

1. 修復 VM で、接続されている OS ディスクの Windows フォルダーに移動します。 たとえば、接続されている OS ディスクに割り当てられたドライブ文字のラベルが *F* である場合は、`F:\Windows` に移動します。
1. *memory.dmp* ファイルを見つけてから、メモリ ダンプ ファイルを添付して [サポート チケットを送信](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)します。
1. *memory.dmp* ファイルが見つからない場合は、ガイドに従って、[マスク不可能割り込み (NMI) 呼び出しを使用してクラッシュ ダンプ ファイルを生成](/windows/client-management/generate-kernel-or-complete-crash-dump)します。

NMI 呼び出しの詳細については、[Azure シリアル コンソールでの NMI 呼び出し](./serial-console-windows.md#use-the-serial-console-for-nmi-calls)に関するユーザー ガイドを参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure 仮想マシンのブート エラーのトラブルシューティング](boot-error-troubleshoot.md)