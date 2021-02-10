---
title: 既定のドメイン コントローラーのポリシーを適用中に VM が応答しない
titlesuffix: Azure Virtual Machines
description: この記事では、既定のドメイン コントローラー ポリシーによって Azure VM の起動が妨げられる問題を解決するための手順を示します。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 34e6b765-3496-46a1-b7d7-6def00884394
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/08/2020
ms.author: v-miegge
ms.openlocfilehash: 6c139398182ca9d875de0d3b21c58afe503bd8a5
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632276"
---
# <a name="vm-is-unresponsive-while-applying-default-domain-controllers-policy"></a>既定のドメイン コントローラーのポリシーを適用中に VM が応答しない

この記事では、既定のドメイン コントローラー ポリシーによって Azure 仮想マシン (VM) の起動が妨げられる問題を解決するための手順を示します。

## <a name="symptom"></a>症状

[ブート診断](./boot-diagnostics.md)を使用して VM のスクリーンショットを表示すると、そのスクリーンショットには、起動中に **[既定のドメイン コントローラー ポリシー]** というメッセージで OS が無応答になっていることが示されています。

  ![図 1 は [既定のドメイン コントローラー ポリシー] というメッセージで OS が停止していることを示している](./media/vm-unresponsive-domain-controllers-policy/1-default-domain-controllers-policy.png)

## <a name="cause"></a>原因

この問題は、既定のドメイン コントローラー ポリシーに加えられた最近の変更のためである可能性があります。 そうでない場合は、根本原因を特定するためにメモリ ダンプ ファイル分析を実行する必要があります。

## <a name="solution"></a>解決策

> [!TIP]
> VM の最新のバックアップがある場合は、[そのバックアップから VM の復元](../../backup/backup-azure-arm-restore-vms.md)を試みて、起動の問題を修正できます。

最近、既定のドメイン コントローラー ポリシーを変更した場合は、問題を解決するために、これらの変更を元に戻すことをお勧めします。 何が問題の原因であるかがわからない場合は、メモリ ダンプを収集してから、サポート チケットを送信します。

### <a name="collect-the-memory-dump-file"></a>メモリ ダンプ ファイルを収集する

この問題を解決するには、まずクラッシュに関するメモリ ダンプ ファイルを収集し、そのメモリ ダンプ ファイルを使用してサポートに問い合わせる必要があります。 ダンプ ファイルを収集するには、次の手順を行います。

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>新しい修復 VM に OS ディスクを接続する

1. [仮想マシンの修復コマンド](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)に関する説明の手順 1 から 3 に従い、修復 VM を準備します。

1. リモート デスクトップ接続を使用し、修復 VM に接続します。

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>ダンプ ファイルを探してサポート チケットを送信する

1. 修復 VM で、接続されている OS ディスク内の Windows フォルダーに移動します。 接続されている OS ディスクに割り当てられているドライブ文字が `F` の場合は、`F:\Windows` に移動する必要があります。

1. memory.dmp ファイルを検索し、メモリ ダンプ ファイルと共に[サポート チケットを送信](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)します。

1. memory.dmp ファイルが見つからない場合は、代わりに[シリアル コンソールでマスク不可能割り込み (NMI) 呼び出し](./serial-console-windows.md#use-the-serial-console-for-nmi-calls)を使用することをお勧めします。 [NMI 呼び出しを使用してクラッシュ ダンプ ファイルを生成する](/windows/client-management/generate-kernel-or-complete-crash-dump)ためのガイドに従います。