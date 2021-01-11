---
title: Azure Cloud Shell ウィンドウの使用 | Microsoft Docs
description: Azure Cloud Shell ウィンドウの使用方法の概要について説明します。
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: damaerte
ms.openlocfilehash: 3435d958049012750e775e2a17f43f463a0cc358
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "70860327"
---
# <a name="using-the-azure-cloud-shell-window"></a>Azure Cloud Shell ウィンドウの使用

ここでは、Cloud Shell ウィンドウの使用方法について説明します。

## <a name="swap-between-bash-and-powershell-environments"></a>Bash 環境と PowerShell 環境のスワップ

Bash 環境と PowerShell 環境をスワップするには、Cloud Shell ツール バーの環境セレクターを使います。  
![環境の選択](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>Cloud Shell の再起動
マシンの状態をリセットするには、Cloud Shell ツール バーの再起動アイコンをクリックします。  
![Cloud Shell の再起動](media/using-the-shell-window/restart.png)
> [!WARNING]
> Cloud Shell を再起動すると、マシンの状態がリセットされ、Azure ファイル共有に保持されていないファイルはすべて失われます。

## <a name="change-the-text-size"></a>テキスト サイズの変更
ウィンドウの左上の設定アイコンをクリックし、[文字のサイズ] オプションをポイントして、目的のテキスト サイズを選びます。 選択内容は、セッション間で保持されます。
![テキスト サイズ](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>フォントの変更
ウィンドウの左上の設定アイコンをクリックし、[フォント] オプションをポイントして、目的のフォントを選びます。  選択内容は、セッション間で保持されます。
![フォント](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>ファイルのアップロードとダウンロード
ウィンドウの左上にあるファイルのアップロード/ダウンロード アイコンをクリックし、アップロードまたはダウンロードを選択します。  
![ファイルのアップロード/ダウンロード](media/using-the-shell-window/uploaddownload.png)
* ファイルをアップロードする場合は、ポップアップを使用して、ローカル コンピューター上のファイルを参照し、目的のファイルを選択して、[開く] ボタンをクリックします。  ファイルが `/home/user` ディレクトリにアップロードされます。
* ファイルをダウンロードする場合は、ポップアップ ウィンドウに完全修飾ファイル パス (つまり、基本的には既定で表示される `/home/user` ディレクトリの下のパス) を入力し、[ダウンロード] ボタンを選択します。  
> [!NOTE] 
> Cloud Shell では、ファイルとファイル パスの大文字と小文字が区別されます。 ファイル パスの大文字と小文字の区別をよく確認してください。

## <a name="open-another-cloud-shell-window"></a>別の Cloud Shell ウィンドウを開く
Cloud Shell では、各セッションが別のプロセスとして存在できるようにすることで、ブラウザー タブで複数の同時セッションを行うことができます。
セッションを終了する場合は、必ず各セッション ウィンドウを終了してください。各プロセスは同一マシン上で実行していますが、それぞれが独立しているためです。  
ウィンドウの左上にある [新しいセッションを開く] アイコンをクリックします。 新しいタブが開き、新たなセッションが既存のコンテナーに接続されます。
![新しいセッションを開く](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Cloud Shell エディター
* 「[Using the Azure Cloud Shell editor (Azure Cloud Shell エディターの使用)](using-cloud-shell-editor.md)」ページを参照してください。

## <a name="web-preview"></a>Web プレビュー
ウィンドウの左上にある Web プレビュー アイコンをクリックし、[構成] を選択して、開放したいポートを指定します。  そのポートを開放するだけであれば [ポートを開く] を、ポートを開放して新しいタブでプレビューする場合は [開いて参照する] を選択します。  
![Web プレビュー](media/using-the-shell-window/preview.png)  
<br>
![ポートの構成](media/using-the-shell-window/preview-configure.png)  
ウィンドウの左上にある Web プレビュー アイコンをクリックし、[ポート ... のプレビュー] を選択して、新しいタブで開放ポートをプレビューします。ウィンドウの左上にある Web プレビュー アイコンをクリックし、[ポートを閉じる] を選択して開放ポートを閉じます。  
![プレビュー/ポートを閉じる](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>Cloud Shell ウィンドウの最小化と最大化
ウィンドウの右上の最小化アイコンをクリックすると、ウィンドウが非表示になります。 Cloud Shell アイコンをクリックすると再び表示されます。
最大化アイコンをクリックすると、ウィンドウの高さが最大に設定されます。 ウィンドウを元のサイズに戻すには、[復元] をクリックします。  
![ウィンドウの最小化と最大化](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>コピーと貼り付け
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Cloud Shell ウィンドウのサイズ変更
ツールバーの上端をクリックしてから、上下にドラッグして Cloud Shell ウィンドウのサイズを変更します。

## <a name="scrolling-text-display"></a>テキスト表示のスクロール
マウスまたはタッチパッドを使用して、ターミナルのテキストをスクロールできます。

## <a name="exit-command"></a>exit コマンド
`exit` を実行すると、アクティブなセッションが終了します。 この動作は、操作が行われていない状態で 20 分経過した場合も発生します。

## <a name="next-steps"></a>次のステップ

[Cloud Shell の Bash のクイックスタート](quickstart.md) <br>
[Cloud Shell の PowerShell のクイック スタート](quickstart-powershell.md)
