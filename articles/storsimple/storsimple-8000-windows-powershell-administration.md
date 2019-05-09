---
title: PowerShell for StorSimple デバイス管理 | Microsoft Docs
description: Windows PowerShell for StorSimple を使用して StorSimple デバイスを管理する方法について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli@microsoft.com
ms.openlocfilehash: e6053ef9b5e1fc113e3bd3057d627347f285cd99
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58003125"
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Windows PowerShell for StorSimple を使用してデバイスを管理する

## <a name="overview"></a>概要

Windows PowerShell for StorSimple は、Microsoft Azure StorSimple デバイスを管理するために使用できるコマンドライン インターフェイスです。 名前が示すように、これは制約付き実行空間に組み込まれている Windows PowerShell ベースのコマンド ライン インターフェイスです。 コマンド ラインを使用するユーザーの観点から見ると、制約付き実行空間は、Windows PowerShell の制限付きバージョンとして表示されます。 このインターフェイスは、Windows PowerShell の基本機能の一部を維持するとともに、Microsoft Azure StorSimple デバイスの管理に適合するように調整された専用のコマンドレットを持っています。

この記事では、Windows PowerShell for StorSimple の機能について説明します。説明には、このインターフェイスへの接続方法と、このインターフェイスを使用して実行できるワークフローや詳細な手順を示すリンクが含まれています。 ワークフローには、デバイスの登録方法、デバイスでのネットワーク インターフェイスの構成方法、デバイスをメンテナンス モードにする必要がある更新プログラムのインストール方法、デバイスの状態の変更方法、および発生する可能性がある問題のトラブルシューティングの実行方法があります。

この記事を読むと、次のことができるようになります。

* Windows PowerShell for StorSimple を使用して StorSimple デバイスに接続する。
* Windows PowerShell for StorSimple を使用して StorSimple デバイスを管理する。
* Windows PowerShell for StorSimple のヘルプを取得する。

> [!NOTE]
> * Windows PowerShell for StorSimple コマンドレットを使用すると、シリアル コンソールから、または Windows PowerShell リモート処理経由で StorSimple デバイスを管理できます。 このインターフェイスで使用できる個々のコマンドレットの詳細については、「 [Windows PowerShell for StorSimple コマンドレット リファレンス](https://technet.microsoft.com/library/dn688168.aspx)」を参照してください。
> * Azure PowerShell StorSimple コマンドレットは、StorSimple サービス レベルのタスクと移行タスクをコマンド ラインから自動化できるコレクションです。 StorSimple に対する Azure PowerShell コマンドレットの詳細については、「 [Azure StorSimple コマンドレット リファレンス](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0&viewFallbackFrom=azuresmps-3.7.0#azure)」を参照してください。


Windows PowerShell for StorSimple には、次のいずれかの方法でアクセスできます。

* [StorSimple デバイスのシリアル コンソールに接続する](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Windows PowerShell を使用して StorSimple にリモート接続する](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Windows PowerShell for StorSimple にデバイスのシリアル コンソール経由で接続する

Windows PowerShell for StorSimple に接続するには、 [PuTTY をダウンロードする](https://www.putty.org/) か、類似するターミナル エミュレーション ソフトウェアをダウンロードする必要があります。 Microsoft Azure StorSimple デバイスにアクセスするように PuTTY を明確に構成する必要があります。 次のトピックで、PuTTy の構成方法とデバイスへの接続方法の詳細な手順について説明します。 シリアル コンソールのさまざまなメニュー オプションについても説明します。

### <a name="putty-settings"></a>PuTTY 設定

シリアル コンソールから Windows PowerShell インターフェイスに接続するには、次の PuTTY 設定を必ず使用してください。

#### <a name="to-configure-putty"></a>PuTTY を構成するには

1. PuTTY の **[Reconfiguration]** ダイアログ ボックスで、**[Category]** パネルの **[Keyboard]** を選択します。
2. 次のオプションが選択されていることを確認します (これらは新しいセッションを開始したときの既定の設定です)。
   
   | キーボードの項目 | Select |
   | --- | --- |
   | Backspace key |Control-? (127) |
   | Home and End keys |Standard |
   | Function keys and keypad |ESC[n~ |
   | Initial state of cursor keys |Normal |
   | Initial state of numeric keypad |Normal |
   | Enable extra keyboard features |Control-Alt is different from AltGr |
   
    ![サポートされる Putty 設定](./media/storsimple-windows-powershell-administration/IC740877.png)
3. **[Apply]** をクリックします。
4. **[Category]** パネルで、**[Translation]** を選択します。
5. **[Remote character set]** ボックスの一覧から **[UTF-8]** を選択します。
6. **[Handling of line drawing characters]** の **[Use Unicode line drawing code points]** を選択します。 次のスクリーンショットに、正しい PuTTY の選択を示します。
   
    ![UTF PuTTY 設定](./media/storsimple-windows-powershell-administration/IC740878.png)
7. **[Apply]** をクリックします。

これで、PuTTY を使用して、次の手順に従ってデバイスのシリアル コンソールに接続できます。

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>シリアル コンソールについて

シリアル コンソール経由で StorSimple デバイスの Windows PowerShell インターフェイスにアクセスすると、バナー メッセージとメニュー オプションが表示されます。

バナー メッセージには、モデル、名前、インストールされているソフトウェアのバージョン、アクセス中のコントローラーの状態などの StorSimple デバイスの基本情報が含まれます。 次の図に、バナー メッセージの例を示します。

![シリアル バナー メッセージ](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> バナー メッセージを使用して、接続中のコントローラーの状態 ("_アクティブ_" または "_パッシブ_") を識別できます。

次の図に、シリアル コンソール メニューで利用できるさまざまな実行空間オプションを示します。

![デバイス 2 の登録](./media/storsimple-windows-powershell-administration/IC740906.png)

次の設定から選択できます。

1. **Log in with full access**。このオプションでは、(適切な資格情報を使用して) ローカル コント ローラーの **SSAdminConsole** に接続できます  (ローカル コント ローラーとは、StorSimple デバイスのシリアル コンソール経由でアクセス中のコントローラーのことです)。このオプションは、デバイスの問題をトラブルシューティングするために無制限実行空間にアクセスすること (サポート セッション) を Microsoft サポートに許可するために使用することもできます。 オプション 1 を使用してログオンした後、特定のコマンドレットを実行することで、無制限実行空間へのアクセスを Microsoft サポート エンジニアに許可できます。 詳細については、「 [サポート セッションを開始する](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple)」を参照してください。
   
2. **Log in to peer controller with full access**。このオプションはオプション 1 と同じですが、接続先が異なり、ピア コントローラーの **SSAdminConsole** 実行空間に接続できます (適切な資格情報を使用します)。 StorSimple デバイスはアクティブ/パッシブ構成された 2 つのコントローラーを持つ可用性の高いデバイスであるため、ピア コントローラーとは、シリアル コンソールからアクセス中のデバイスのもう一方のコントローラーを指します。
   オプション 1 と同じように、このオプションも、ピア コントローラーの無制限実行空間にアクセスすることを Microsoft サポートに許可するために使用できます。

3. **Connect with limited access**。このオプションは、Windows PowerShell インターフェイスに制限モードでアクセスするために使用します。 アクセス資格情報の入力は必要ありません。 このオプションでは、オプション 1 とオプション 2 に比べて、さらに制限された制限付き実行空間に接続します。  オプション 1 では利用できるがこの実行空間では "*利用できない*" タスクの一部を次に示します。
   
   * 工場出荷時設定をリセットする
   * パスワードを変更する
   * サポート アクセスを有効または無効にする
   * 更新プログラムを適用する
   * 修正プログラムをインストールする

     > [!NOTE]
     > これは、デバイス管理者パスワードを忘れてしまい、オプション 1 またはオプション 2 を使用して接続できない場合に推奨するオプションです。

4. **Change language**。このオプションでは、Windows PowerShell インターフェイスでの表示言語を変更できます。 サポートされている言語は、英語、日本語、ロシア語、フランス語、韓国語、スペイン語、イタリア語、ドイツ語、中国語、およびポルトガル語 (ブラジル) です。

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Windows PowerShell for StorSimple を使用して StorSimple にリモート接続する

Windows PowerShell リモート処理を使用して StorSimple デバイスに接続できます。 この方法で接続すると、メニューは表示されません  (メニューは、デバイスのシリアル コンソールを使用して接続する場合のみ表示されます。 リモート接続すると、シリアル コンソールで "オプション 1 – フル アクセス" を選択した場合と同じ結果に直接移行します。)Windows PowerShell リモート処理を使用して、特定の実行空間に接続します。 表示言語を指定することもできます。

表示言語は、シリアル コンソール メニューの **[Change Language]** オプションを使用して設定する言語とは無関係です。 リモート PowerShell は、何も指定されていない場合は、接続元のデバイスのロケールを自動的に選択します。

> [!NOTE]
> Microsoft Azure 仮想ホストと StorSimple Cloud Appliance を操作する場合は、Windows PowerShell リモート処理と仮想ホストを使用して、クラウド アプライアンスに接続できます。 Windows PowerShell セッションからの情報を保存するホスト上に共有場所を設定している場合、_Everyone_ プリンシパルには認証ユーザーのみが含まれることに注意してください。 そのため、_Everyone_ によって共有へのアクセスを許可するように設定しているときに、資格情報を指定せずに接続した場合、認証されていない Anonymous プリンシパルが使用され、エラーが表示されます。 この問題を解決するには、共有ホストで Guest アカウントを有効にし、Guest アカウントに共有へへのフル アクセス許可を与えるか、Windows PowerShell コマンドレットを有効な資格情報を使用して指定する必要があります。


HTTP または HTTPS を使用して Windows PowerShell リモート処理経由で接続できます。 次のチュートリアルの手順を使用してください。

* [HTTP を使用してリモート接続する](storsimple-8000-remote-connect.md#connect-through-http)
* [HTTPS を使用してリモート接続する](storsimple-8000-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>接続のセキュリティに関する考慮事項

Windows PowerShell for StorSimple に接続する方法を決定するときは、次の点を考慮してください。

* デバイスのシリアル コンソールへの直接接続はセキュリティで保護されていますが、ネットワーク スイッチ経由での接続は保護されていません。 ネットワーク スイッチ経由でデバイスのシリアル コンソールに接続するときは、セキュリティ上のリスクに注意してください。
* HTTP セッション経由での接続は、ネットワーク経由でシリアル コンソールに接続するよりもセキュリティが高くなる可能性があります。 これは最も安全な方法ではありませんが、信頼されたネットワークでは容認できます。
* HTTPS セッション経由での接続は、最も安全であり、推奨されるオプションです。

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Windows PowerShell for StorSimple を使用して StorSimple デバイスを管理する

次の表に、StorSimple デバイスの Windows PowerShell インターフェイス内で実行できるすべての一般的な管理タスクと複雑なワークフローの概要を示します。 各ワークフローの詳細については、表内の適切なエントリをクリックしてください。

#### <a name="windows-powershell-for-storsimple-workflows"></a>Windows PowerShell for StorSimple のワークフロー

| 目的の操作 | 実行する手順 |
| --- | --- |
| デバイスを登録する |[Windows PowerShell for StorSimple を使用してデバイスを構成して登録する](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| Web プロキシの </br>Web プロキシ表示設定を構成する |[StorSimple デバイスの Web プロキシを構成する](storsimple-8000-configure-web-proxy.md) |
| デバイスの DATA 0 ネットワーク インターフェイス設定を変更する |[StorSimple デバイスの DATA 0 ネットワーク インターフェイスを変更する](storsimple-8000-modify-data-0.md) |
| コントローラーを停止する </br> コントローラーの再起動とシャットダウンを行う </br> デバイスをシャット ダウンする</br>デバイスを出荷時の設定にリセットする |[デバイス コントローラーを管理する](storsimple-8000-manage-device-controller.md) |
| メンテナンス モードで更新プログラムと修正プログラムをインストールする |[デバイスを更新する](storsimple-update-device.md) |
| メンテナンス モードに移行する </br>メンテナンス モードを終了する |[StorSimple デバイスのモード](storsimple-8000-device-modes.md) |
| サポート パッケージを作成する</br>サポート パッケージの暗号化を解除して編集する |[サポート パッケージを作成および管理する](storsimple-8000-create-manage-support-package.md) |
| サポート セッションを開始する</br> |[StorSimple 用 Windows PowerShell でのサポート セッションの開始](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Windows PowerShell for StorSimple のヘルプを取得する

Windows PowerShell for StorSimple では、コマンドレット ヘルプを利用できます。 このヘルプのオンラインの最新バージョンも利用できます。これを使用して、システム上のヘルプを更新できます。

このインターフェイスでのヘルプの取得は、Windows PowerShell での操作に似ています。また、ほとんどのヘルプ関連コマンドレットで機能します。 Windows PowerShell のヘルプは、次の TechNet ライブラリでオンライン検索できます: [Windows PowerShell を使用したスクリプト](https://go.microsoft.com/fwlink/?LinkID=108518)。

Windows PowerShell インターフェイスで取得できるヘルプの種類の簡単な説明を、ヘルプの更新方法も含めて次に示します。

### <a name="to-get-help-for-a-cmdlet"></a>コマンドレットのヘルプを取得するには

* 任意のコマンドレッドまたは関数のヘルプを表示するには、次のコマンドを使用します。`Get-Help <cmdlet-name>`
* 任意のコマンドレットのオンライン ヘルプを取得するには、上のコマンドレットと `-Online`パラメーターを使用します。`Get-Help <cmdlet-name> -Online`
* ヘルプ全体を取得するには、`–Full` パラメーターを使用します。たとえば `–Examples` パラメーターを使用します。

### <a name="to-update-help"></a>ヘルプを更新するには

Windows PowerShell インターフェイスのヘルプは簡単に更新できます。 ヘルプを更新するには、次の手順を実行します。

#### <a name="to-update-cmdlet-help"></a>コマンドレットのヘルプを更新するには
1. Windows PowerShell を **[管理者として実行]** オプションを指定して起動します。
2. コマンド プロンプトに「`Update-Help`」と入力します。
3. 更新されたヘルプ ファイルがインストールされます。
4. ヘルプ ファイルがインストールされた後、「 `Get-Help Get-Command`」と入力します。 これにより、ヘルプを利用できるコマンドレットの一覧が表示されます。

> [!NOTE]
> 実行空間で利用できるすべてのコマンドレットの一覧を取得するには、該当するメニュー オプションにログインし、`Get-Command` コマンドレットを実行します。


## <a name="next-steps"></a>次の手順

上記のワークフローのいずれかを実行したときに、StorSimple デバイスで問題が発生した場合は、「 [StorSimple デプロイメントのトラブルシューティング用ツール](storsimple-8000-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments)」をご覧ください。

