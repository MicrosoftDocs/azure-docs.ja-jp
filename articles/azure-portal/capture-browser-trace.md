---
title: トラブルシューティングのためにブラウザー トレースをキャプチャする | Microsoft Docs
description: Azure portal での問題のトラブルシューティングに役立つように、ブラウザー トレースからネットワーク情報をキャプチャします。
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 05/11/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: dba321d055e64d62ca91f95461c3299bee5f90d2
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714221"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>トラブルシューティングのためにブラウザー トレースをキャプチャする

Azure portal での問題のトラブルシューティングを行っていて、Microsoft サポートに問い合わせる必要がある場合は、最初にブラウザー トレースといくつかの追加情報をキャプチャすることをお勧めします。 収集した情報で、問題が発生したときのポータルに関する重要な詳細情報を提供できます。 使用しているブラウザーの開発者ツールに対応するこの記事の手順に従ってください: Google Chrome または Microsoft Edge (Chromium)、Microsoft Edge (EdgeHTML)、Apple Safari、または Firefox。

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome と Microsoft Edge (Chromium)

Google Chrome と Microsoft Edge (Chromium) はどちらも、[Chromium オープンソース プロジェクト](https://www.chromium.org/Home)が基になっています。 次の手順では、開発者ツールを使用する方法について説明します。これらのツールは、2 つのブラウザーで非常によく似ています。 詳細については、「[Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools)」および「[Microsoft Edge (Chromium) 開発者ツール](/microsoft-edge/devtools-guide-chromium)」を参照してください。

1. [Azure portal](https://portal.azure.com) にサインインします。 サインインに関連する機密情報がトレースに含まれないようにするため、トレースを始める " _前に_" サインインすることが重要です。 

1. [ステップ記録ツール](https://support.microsoft.com/help/22878/windows-10-record-steps)を使用して、ポータルで実行した手順の記録を始めます。

1. ポータルで、問題が発生した直前のステップに移動します。

1. F12 キーを押すか、![ブラウザー設定アイコンのスクリーンショット](media/capture-browser-trace/chromium-icon-settings.png) >  **[その他のツール]**  >  **[デベロッパー ツール]** を選択します。

1. 既定では、ブラウザーで現在読み込まれているページのトレース情報のみが保持されます。 再現するには複数のページを移動する必要がある場合であっても、次のオプションを設定して、すべてのトレース情報が保持されるようにします。

    1. **[Network]\(ネットワーク\)** タブを選択し、 **[Preserve log]\(ログを保持\)** を選択します。

          ![[Preserve log]\(ログを保持\) のスクリーンショット](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. **[Console]\(コンソール\)** タブを選択し、 **[Console settings]\(コンソールの設定\)** を選択してから、 **[Preserve Log]\(ログを保持\)** を選択します。 **[Console settings]\(コンソールの設定\)** をもう一度選択して、設定ペインを閉じます。

          ![[Preserve Log]\(ログを保持\) のスクリーンショット](media/capture-browser-trace/chromium-console-preserve-log.png)

1. **[Network]\(ネットワーク\)** タブを選択し、 **[Stop recording network log]\(ネットワーク ログの記録を停止\)** と **[Clear]\(クリア\)** を選択します。

    ![[Stop recording network log]\(ネットワーク ログの記録を停止\) と [Clear]\(クリア\) のスクリーンショット](media/capture-browser-trace/chromium-stop-clear-session.png)

1. **[Record network log]\(ネットワーク ログを記録\)** を選択して、ポータルで問題を再現します。

    ![[プロファイル セッションの開始] のスクリーンショット](media/capture-browser-trace/chromium-start-session.png)

    次の図のようなセッション出力が表示されます。

    ![ブラウザー トレース結果のスクリーンショット](media/capture-browser-trace/chromium-browser-trace-results.png)

1. 予期しないポータルの動作を再現した後、 **[Stop recording network log]\(ネットワーク ログの記録を停止\)** を選択し、 **[Export HAR]\(HAR のエクスポート\)** を選択してファイルを保存します。

    ![[Export HAR]\(HAR のエクスポート\) のスクリーンショット](media/capture-browser-trace/chromium-network-export-har.png)

1. ステップ記録ツールを停止し、記録を保存します。

1. ブラウザー開発者ツールのペインに戻り、 **[Console]\(コンソール\)** タブを選択します。いずれかのメッセージを右クリックし、 **[Save as...]\(名前を付けて保存...\)** を選択して、コンソールの出力をテキスト ファイルに保存します。

    ![コンソール出力のスクリーンショット](media/capture-browser-trace/chromium-console-select.png)

1. HAR ファイル、コンソール出力、および画面記録を .zip などの圧縮形式でパッケージ化し、Microsoft サポートと共有します。

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge (EdgeHTML)

次の手順では、Microsoft Edge (EdgeHTML) の開発者ツールを使用する方法について説明します。 詳細については、「[Microsoft Edge (EdgeHTML) 開発者ツール](/microsoft-edge/devtools-guide)」を参照してください。

1. [Azure portal](https://portal.azure.com) にサインインします。 サインインに関連する機密情報がトレースに含まれないようにするため、トレースを始める " _前に_" サインインすることが重要です。 

1. [ステップ記録ツール](https://support.microsoft.com/help/22878/windows-10-record-steps)を使用して、ポータルで実行した手順の記録を始めます。

1. ポータルで、問題が発生した直前のステップに移動します。

1. F12 キーを押すか、![ブラウザー設定アイコンのスクリーンショット](media/capture-browser-trace/edge-icon-settings.png) >  **[その他のツール]**  >  **[デベロッパー ツール]** を選択します。

1. 既定では、ブラウザーで現在読み込まれているページのトレース情報のみが保持されます。 再現するには複数のページを移動する必要がある場合であっても、次のオプションを設定して、すべてのトレース情報が保持されるようにします。

    1. **[ネットワーク]** タブを選択し、 **[ナビゲーション時にエントリをクリア]** オプションをオフにします。

          ![[ナビゲーション時にエントリをクリア] のスクリーンショット](media/capture-browser-trace/edge-network-clear-entries.png)

    1. **[コンソール]** タブを選択し、 **[Preserve Log]\(ログを保持\)** を選択します。

          ![[Preserve Log]\(ログを保持\) のスクリーンショット](media/capture-browser-trace/edge-console-preserve-log.png)

1. **[ネットワーク]** タブを選択し、 **[プロファイリング セッションの終了]** と **[セッションのクリア]** を選択します。

    ![[プロファイリング セッションの終了] と [セッションのクリア] のスクリーンショット](media/capture-browser-trace/edge-stop-clear-session.png)

1. **[プロファイリング セッションの開始]** を選択し、ポータルで問題を再現します。

    ![[プロファイル セッションの開始] のスクリーンショット](media/capture-browser-trace/edge-start-session.png)

    次の図のようなセッション出力が表示されます。

    ![ブラウザー トレース結果のスクリーンショット](media/capture-browser-trace/edge-browser-trace-results.png)

1. 予期しないポータルの動作を再現した後、 **[プロファイリング セッションの終了]** を選択し、 **[HAR としてエクスポート]** を選択してファイルを保存します。

    ![[HAR としてエクスポート] のスクリーンショット](media/capture-browser-trace/edge-network-export-har.png)

1. ステップ記録ツールを停止し、記録を保存します。

1. ブラウザー開発者ツールのペインに戻り、 **[コンソール]** タブを選択して、ウィンドウを展開します。 コンソール出力の先頭にカーソルを置き、出力の内容全体をドラッグして選択します。 右クリックし、 **[コピー]** を選択して、コンソールの出力をテキスト ファイルに保存します。

    ![コンソール出力のスクリーンショット](media/capture-browser-trace/edge-console-select.png)

1. HAR ファイル、コンソール出力、および画面記録を .zip などの圧縮形式でパッケージ化し、Microsoft サポートと共有します。

## <a name="apple-safari"></a>Apple Safari

次の手順では、Apple Safari の開発者ツールを使用する方法について説明します。 詳細については、「[Safari 開発者ツールの概要](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac)」を参照してください。

1. Apple Safari で開発者ツールを有効にします。

    1. **[Safari]** を選択し、 **[Preferences]\(基本設定\)** を選択します。

        ![Safari の基本設定のスクリーンショット](media/capture-browser-trace/safari-preferences.png)

    1. **[Advanced]\(詳細\)** タブを選択し、 **[Show Develop menu in menu bar]\(メニュー バーに開発メニューを表示する\)** を選択します。

        ![Safari の詳細設定のスクリーンショット](media/capture-browser-trace/safari-show-develop-menu.png)

1. [Azure portal](https://portal.azure.com) にサインインします。 サインインに関連する機密情報がトレースに含まれないようにするため、トレースを始める " _前に_" サインインすることが重要です。 

1. ポータルで実行した手順の記録を始めます。 詳細については、「[Mac の画面を記録する方法](https://support.apple.com/HT208721)」を参照してください。

1. ポータルで、問題が発生した直前のステップに移動します。

1. **[Develop]\(開発\)** を選択し、 **[Show Web Inspector]\(Web Inspector を表示\)** を選択します。

    ![[Show Web Inspector]\(Web Inspector を表示\) のスクリーンショット](media/capture-browser-trace/safari-show-web-inspector.png)

1. 既定では、ブラウザーで現在読み込まれているページのトレース情報のみが保持されます。 再現するには複数のページを移動する必要がある場合であっても、次のオプションを設定して、すべてのトレース情報が保持されるようにします。

    1. **[Network]\(ネットワーク\)** タブを選択し、 **[Preserve Log]\(ログを保持\)** を選択します。

          ![[Preserve Log]\(ログを保持\) のスクリーンショット](media/capture-browser-trace/safari-network-preserve-log.png)

    1. **[コンソール]** タブを選択し、 **[Preserve Log]\(ログを保持\)** を選択します。

          ![[Preserve Log]\(ログを保持\) のスクリーンショット](media/capture-browser-trace/safari-console-preserve-log.png)

1. **[Network]\(ネットワーク\)** タブを選択し、 **[Clear Network Items]\(ネットワーク アイテムのクリア\)** を選択します。

    ![[Clear Network Items]\(ネットワーク アイテムのクリア\) のスクリーンショット](media/capture-browser-trace/safari-clear-session.png)

1. ポータルで問題を再現します。 次の図のようなセッション出力が表示されます。

    ![ブラウザー トレース結果のスクリーンショット](media/capture-browser-trace/safari-browser-trace-results.png)

1. 予期しないポータルの動作を再現した後、 **[Export]\(エクスポート\)** を選択してファイルを保存します。

    ![[Export]\(エクスポート\) のスクリーンショット](media/capture-browser-trace/safari-network-export-har.png)

1. 画面収録ツールを停止し、記録を保存します。

1. ブラウザー開発者ツールのペインに戻り、 **[コンソール]** タブを選択して、ウィンドウを展開します。 コンソール出力の先頭にカーソルを置き、出力の内容全体をドラッグして選択します。 Command + C キーを使用して出力をコピーし、テキスト ファイルに保存します。

    ![コンソール出力のスクリーンショット](media/capture-browser-trace/safari-console-select.png)

1. HAR ファイル、コンソール出力、および画面記録を .zip などの圧縮形式でパッケージ化し、Microsoft サポートと共有します。

## <a name="firefox"></a>Firefox

次の手順では、Firefox の開発者ツールを使用する方法について説明します。 詳細については、[Firefox の開発者ツール](https://developer.mozilla.org/docs/Tools)に関するページを参照してください。

1. [Azure portal](https://portal.azure.com) にサインインします。 サインインに関連する機密情報がトレースに含まれないようにするため、トレースを始める " _前に_" サインインすることが重要です。 

1. ポータルで実行した手順の記録を始めます。 Windows で [ステップ記録ツール](https://support.microsoft.com/help/22878/windows-10-record-steps)を使用するか、「[Mac で画面を収録する方法](https://support.apple.com/HT208721)」を参照してください。

1. ポータルで、問題が発生した直前のステップに移動します。

1. F12 キーを押すか、![ブラウザー設定アイコンのスクリーンショット](media/capture-browser-trace/firefox-icon-settings.png) >  **[Web Developer]\(Web Developer\)**  >  **[Toggle Tools]\(ツールの切り替え\)** を選択します。

1. 既定では、ブラウザーで現在読み込まれているページのトレース情報のみが保持されます。 再現するには複数のページを移動する必要がある場合であっても、次のオプションを設定して、すべてのトレース情報が保持されるようにします。

    1. **[ネットワーク]** タブを選択し、 **[永続ログ]** をオンにします。

          ![[永続ログ] のスクリーンショット](media/capture-browser-trace/firefox-network-persist-logs.png)

    1. **[コンソール]** タブを選択し、 **[コンソール設定]** を選択してから、 **[永続ログ]** を選択します。

          ![[永続ログ] のスクリーンショット](media/capture-browser-trace/firefox-console-persist-logs.png)

1. **[ネットワーク]** タブを選択し、 **[消去]** を選択します。

    ![[消去] のスクリーンショット](media/capture-browser-trace/firefox-clear-session.png)

1. ポータルで問題を再現します。 次の図のようなセッション出力が表示されます。

    ![ブラウザー トレース結果のスクリーンショット](media/capture-browser-trace/firefox-browser-trace-results.png)

1. 予期しないポータルの動作を再現した後、 **[HAR のエクスポート/インポート]** を選択して **[HAR 形式ですべて保存]** を選択します。

    ![[Export HAR]\(HAR のエクスポート\) のスクリーンショット](media/capture-browser-trace/firefox-network-export-har.png)

1. Windows のステップ記録ツールまたは Mac の画面収録ツールを停止し、記録を保存します。

1. ブラウザー開発者ツールのペインに戻り、 **[Console]\(コンソール\)** タブを選択します。いずれかのメッセージを右クリックし、 **[表示メッセージをエクスポート]** を選択して、コンソールの出力をテキスト ファイルに保存します。

    ![コンソール出力のスクリーンショット](media/capture-browser-trace/firefox-console-select.png)

1. HAR ファイル、コンソール出力、および画面記録を .zip などの圧縮形式でパッケージ化し、Microsoft サポートと共有します。

## <a name="next-steps"></a>次のステップ

[Azure Portal の概要](azure-portal-overview.md)