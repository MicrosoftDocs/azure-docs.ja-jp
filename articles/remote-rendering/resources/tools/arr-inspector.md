---
title: ArrInspector 検査ツール
description: ArrInspector ツールのユーザー マニュアル
author: florianborn71
ms.author: flborn
ms.date: 03/09/2020
ms.topic: article
ms.openlocfilehash: 300e0ff26d643ae0263d21e604cb26da37a18841
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "97723827"
---
# <a name="the-arrinspector-inspection-tool"></a>ArrInspector 検査ツール

ArrInspector は、実行中の Azure Remote Rendering セッションを検査するために使用される Web ベースのツールです。 デバッグを目的として、レンダリングされているシーンの構造を調べたり、ログ メッセージを表示したり、サーバーのライブ パフォーマンスを監視したりするために使用することが想定されています。

![ArrInspector](./media/arr-inspector.png)

## <a name="connecting-to-the-arrinspector"></a>ArrInspector に接続する

ARR サーバーのホスト名 (末尾が `mixedreality.azure.com`) を取得したら、[ConnectToArrInspectorAsync](../../how-tos/frontend-apis.md#connect-to-arr-inspector) を使用して接続します。 この関数により、アプリケーションが実行されているデバイス上に `StartArrInspector.html` が作成されます。 ArrInspector を起動するには、PC のブラウザー (Microsoft Edge、Firefox、または Chrome) でそのファイルを開きます。 そのファイルは 24 時間だけ有効です。

`ConnectToArrInspectorAsync` を呼び出すアプリが PC で既に実行されている場合:

* Unity 統合を使用している場合は、自動的に起動されることがあります。
* そうでない場合は、*User Folders\\LocalAppData\\[your_app]\\AC\\Temp* でファイルを探します。

アプリが HoloLens で実行されている場合:

1. [Windows デバイス ポータル](/windows/mixed-reality/using-the-windows-device-portal)を使用して HoloLens にアクセスします。
1. *[System]\(システム\) > [File Explorer]\(エクスプローラー\)* にアクセスします。
1. *User Folders\\LocalAppData\\[your_app]\\AC\\Temp* に移動します。
1. *StartArrInspector.html* をお使いの PC に保存します。
1. *StartArrInspector.html* を開いて、セッションの ArrInspector を読み込みます。

## <a name="the-performance-panel"></a>[Performance]\(パフォーマンス\) パネル

![[Performance]\(パフォーマンス\) パネル](./media/performance-panel.png)

このパネルには、サーバーによって公開される、すべてのフレームごとのパフォーマンス値のグラフが表示されます。 その値には、現在、フレーム時間、FPS、CPU とメモリの使用率、全体的な RAM 使用率などのメモリ統計、オブジェクト数などが含まれます。

これらのパラメーターのいずれかを表示するには、 **[Add New]\(新規追加\)** ボタンをクリックして、ダイアログに表示される使用可能な値のいずれかを選択します。 この操作により、新しいスクロール グラフがパネルに追加されて、値がリアルタイムでトレースされます。 右側には、"*最小*"、"*最大*"、"*現在*" の値が表示されます。

マウスで内容をドラッグすることによって、グラフをパンできます。ただし、横方向にパンできるのは、ArrInspector が一時停止状態になっている場合のみです。

Ctrl キーを押しながらドラッグすると、ズームできます。 横ズームは、下部にあるスライダーで制御することもできます。

縦方向の範囲は、既定では、現在表示されている値に基づいて計算され、最小値と最大値が右側のテキスト ボックスに表示されます。 テキスト ボックスに直接入力するか、パンまたはズームすることによって、値を手動で設定すると、それらの値がグラフで使用されます。 縦方向の自動フレーミングを元に戻すには、右上隅にあるアイコンをクリックします。

![縦方向の範囲](./media/vertical-range.png)

## <a name="the-log-panel"></a>[Log]\(ログ\) パネル

![[Log]\(ログ\) パネル](./media/log-panel.png)

[Log]\(ログ\) パネルには、サーバー側で生成されたログ メッセージの一覧が表示されます。 接続時には、以前のログ メッセージが最大 200 件表示され、新しいログ メッセージが発生するたびに表示されます。

上部にあるボタンを使用すると、ログの種類 `[Error/Warning/Info/Debug]` に基づいて一覧をフィルター処理できます。
![ログ フィルターのボタン](./media/log-filter.png)

## <a name="the-timing-data-capture-panel"></a>[Timing Data Capture]\(タイミング データ キャプチャ\) パネル

![[Timing Data Capture]\(タイミング データ キャプチャ\)](./media/timing-data-capture.png)

このパネルは、サーバーからタイミング情報をキャプチャしてダウンロードするために使用します。 ファイルでは、[Chrome Tracing JSON 形式](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/edit)が使用されます。 データを調べるには、Chrome で URL `Chrome://tracing` を開き、ダウンロードしたファイルをそのページにドラッグ アンド ドロップします。 タイミング データは、固定サイズのリング バッファーに継続的に収集されます。 書き込まれるキャプチャには、直前の情報、つまり数秒から数分の情報のみが含まれます。

## <a name="the-scene-inspection-panel"></a>[Scene Inspection]\(シーン検査\) パネル

![[Scene Inspection]\(シーン検査\) パネル](./media/scene-inspection-panel.png)

このパネルには、レンダリングされたシーンの構造が表示されます。 左側にはオブジェクトの階層が表示され、右側には選択したオブジェクトの内容が表示されます。 パネルは読み取り専用であり、リアルタイムで更新されます。

## <a name="the-vm-debug-information-panel"></a>[VM Debug Information]\(VM デバッグ情報\) パネル

![[VM Debug Information]\(VM デバッグ情報\) パネル](./media/state-debugger-panel.png)

このパネルには、デバッグ機能がいくつか用意されています。

### <a name="restart-service"></a>サービスを再起動する

**[Restart Service]\(サービスの再起動\)** ボタンでは、ArrInspector が接続されている仮想マシン上のランタイムが再起動されます。 接続されているすべてのクライアントが切断されるので、再起動されたサービスに接続するには、ArrInspector のページを再度読み込む必要があります。

### <a name="collect-debug-information"></a>デバッグ情報を収集する

**[Collect Debug Information for VM]\(VM のデバッグ情報の収集\)** ボタンをクリックすると開くダイアログでは、VM でデバッグ情報を収集する ARR インスタンスをトリガーできます。

![VM デバッグ情報ダイアログ](./media/state-debugger-dialog.png)

Azure Remote Rendering チームは、デバッグ情報を使用することで、実行中の ARR インスタンスで発生している問題を分析できます。 このダイアログのテキスト フィールドを使用して、問題の再現手順などの追加情報を提供できます。

**[Start Collecting]\(収集開始\)** ボタンをクリックすると、ダイアログが閉じて、収集プロセスが開始されます。 VM での情報の収集には数分かかることがあります。

![進行中の VM デバッグ情報の収集](./media/state-debugger-panel-in-progress.png)

収集が完了すると、ArrInspector のウィンドウに通知が表示されます。 この通知には、この特定の収集を示す ID が含まれています。 この ID を必ず保存しておき、Azure Remote Rendering チームに渡してください。

![VM デバッグ情報の収集の成功](./media/state-debugger-snackbar-success.png)

> [!IMPORTANT]
> VM デバッグ情報をダウンロードしたり、それ以外の方法で情報にアクセスしたりすることはできません。 収集されたデータにアクセスできるのは、Azure Remote Rendering チームだけです。 チームに連絡して収集 ID を送信、発生している問題の調査を依頼する必要があります。

## <a name="pause-mode"></a>一時停止モード

右上隅にあるスイッチを使用して、パネルのライブ更新を一時停止することができます。 このモードは、特定の状態を詳しく検査するのに役立ちます。

![一時停止モード](./media/pause-mode.png)

ライブ更新を再度有効にすると、すべてのパネルがリセットされます。