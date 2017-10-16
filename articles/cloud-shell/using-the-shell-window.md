---
title: "Azure Cloud Shell (プレビュー) の使用 | Microsoft Docs"
description: "Azure Cloud Shell ウィンドウの使用方法の概要について説明します。"
services: azure
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: fb242abfbea79bc8c242a7a89b3d775cf74a0617
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-azure-cloud-shell-window"></a>Azure Cloud Shell ウィンドウの使用

ここでは、Cloud Shell ウィンドウの使用方法について説明します。

## <a name="swap-between-bash-and-powershell-environments"></a>Bash 環境と PowerShell 環境のスワップ
![](media/using-the-shell-window/env-selector.png)

Bash 環境と PowerShell 環境をスワップするには、Cloud Shell ツール バーの環境セレクターを使います。

## <a name="restart-cloud-shell"></a>Cloud Shell の再起動
![](media/using-the-shell-window/restart.png)
> [!WARNING]
> Cloud Shell を再起動すると、マシンの状態がリセットされ、ファイル共有に保持されていないファイルはすべて失われます。

* マシンの状態をリセットするには、Cloud Shell ツール バーの再起動アイコンをクリックします。

## <a name="minimize--maximize-cloud-shell-window"></a>Cloud Shell ウィンドウの最小化と最大化
![](media/using-the-shell-window/minmax.png)
* ウィンドウの右上の最小化アイコンをクリックすると、ウィンドウが非表示になります。 Cloud Shell アイコンをクリックすると再び表示されます。
* 最大化アイコンをクリックすると、ウィンドウの高さが最大に設定されます。 ウィンドウを元のサイズに戻すには、[復元] をクリックします。

## <a name="concurrent-sessions"></a>同時セッション数
Cloud Shell では、各セッションが別の Bash プロセスとして存在できるようにすることで、ブラウザー タブで複数の同時セッションを行うことができます。
セッションを終了する場合は、必ず各セッション ウィンドウを終了してください。各プロセスは同一マシン上で実行していますが、それぞれが独立しているためです。

## <a name="copy-and-paste"></a>コピーと貼り付け
[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Cloud Shell ウィンドウのサイズ変更
* ツールバーの上端をクリックしてから、上下にドラッグして Cloud Shell ウィンドウのサイズを変更します。

## <a name="scrolling-text-display"></a>テキスト表示のスクロール
* マウスまたはタッチパッドを使用してスクロールし、端末のテキストを移動します。

## <a name="changing-the-text-size"></a>テキスト サイズの変更
![](media/using-the-shell-window/text-size.png)
* ウィンドウの左上の設定アイコンをクリックし、[文字のサイズ] オプションをポイントして、目的のテキスト サイズを選びます。

## <a name="exit-command"></a>exit コマンド
`exit` を実行すると、アクティブなセッションが終了します。 この動作は既定では 10 分後に発生します (操作が行われない場合)。

## <a name="next-steps"></a>次のステップ

[Cloud Shell の Bash のクイックスタート](quickstart.md)
[Cloud Shell の PowerShell のクイックスタート](quickstart-powershell.md)