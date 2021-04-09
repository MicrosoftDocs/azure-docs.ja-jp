---
title: Azure Data Box Gateway シリーズ デバイスに更新プログラムをインストールする | Microsoft Docs
description: Azure portal と Azure Data Box Gateway シリーズ デバイスのローカル Web UI を使用して更新プログラムを適用する方法について説明します
services: databox
author: alkohli
ms.service: databox
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 7fda10310a4bac085b248248d80d708dfa7f3ff7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96581241"
---
# <a name="update-your-azure-data-box-gateway"></a>Azure Data Box Gateway を更新する

この記事では、ローカル Web UI および Azure portal を使用して、Azure Data Box Gateway に更新プログラムをインストールするために必要な手順について説明します。 Data Box Gateway デバイスを最新の状態に保つために、ソフトウェアの更新プログラムまたは修正プログラムを適用します。

> [!IMPORTANT]
>
> - Update **1911** は、デバイス上ではソフトウェア バージョン **1.6.1049.786** になります。 この更新プログラムの詳細については、[リリース ノート](data-box-gateway-1911-release-notes.md)を参照してください。
>
> - 更新プログラムまたは修正プログラムをインストールすると、デバイスが再起動されることに注意してください。 Data Box Gateway が単一ノード デバイスである場合、進行中のすべての I/O が中断され、デバイス ソフトウェアの更新のために、デバイスで最大 30 分のダウンタイムが発生します。

以下のセクションでは、これらの各手順について説明します。

## <a name="use-the-azure-portal"></a>Azure ポータルの使用

Azure portal から更新プログラムをインストールすることをお勧めします。 デバイスでは 1 日に 1 回、更新プログラムのスキャンが自動的に行われます。 更新プログラムが利用可能になると、ポータルに通知が表示されます。 その後、更新プログラムをダウンロードしてインストールすることができます。

> [!NOTE]
> 更新プログラムのインストールを続行する前に、デバイスが正常であり、状態が **オンライン** と表示されていることを確認します。

1. ご使用のデバイスで更新プログラムが利用可能になると、通知が表示されます。 通知を選択するか、上部のコマンド バーで **[デバイスの更新]** を選択します。 これにより、デバイスのソフトウェア更新プログラムが適用できるようになります。

    ![Azure Data Box Gateway のホーム ページのスクリーンショット。[概要] オプションと [デバイスの更新] オプションが強調表示されています。](./media/data-box-gateway-apply-updates/portal-apply-update-01a.png)

2. **[デバイスの更新プログラム]** ブレードで、リリース ノートの新機能に関連付けられているライセンス条項を確認したことを示すチェック ボックスをオンにします。

    更新プログラムを **ダウンロードしてインストール** するか、単に更新プログラムを **ダウンロード** するかを選択できます。 後でこれらの更新プログラムのインストールを選択できます。

    ![[デバイスの更新プログラム] ダイアログ ボックスのスクリーンショット。同意と理解に関するオプションと、ダウンロードしてインストールするためのオプションが強調表示されています。](./media/data-box-gateway-apply-updates/portal-apply-update-02.png)

    更新プログラムをダウンロードしてインストールする場合は、ダウンロードの完了後に更新プログラムが自動的にインストールされるオプションをオンにします。

    ![[デバイスの更新プログラム] ダイアログ ボックスのスクリーンショット。同意に関するオプションとダウンロードのオプションが強調表示されています。](./media/data-box-gateway-apply-updates/portal-apply-update-03.png)

3. 更新プログラムのダウンロードが開始されます。 ダウンロードが進行中であることが通知されます。

    ![更新プログラムのダウンロードとインストールが進行中であることを通知する画面のスクリーンショット。](./media/data-box-gateway-apply-updates/portal-apply-update-05.png)

    通知バナーは Azure portal にも表示されます。 これは、ダウンロードの進行状況を示します。

    ![Azure Data Box Gateway のホーム ページのスクリーンショット。[概要] オプションと、更新プログラムがダウンロード中であることを通知するバナーが強調表示されています。](./media/data-box-gateway-apply-updates/portal-apply-update-08a.png)

    更新の詳細な状態を確認するには、この通知を選択するか、 **[デバイスの更新]** を選択します。

    ![[更新プログラムのダウンロードとインストール] ダイアログ ボックスのスクリーンショット。[状態]\(進行中) と [更新プログラムのダウンロード] のメッセージが強調表示されています。](./media/data-box-gateway-apply-updates/portal-apply-update-09.png)

4. ダウンロードが完了すると、通知バナーが更新され、完了が示されます。 更新プログラムをダウンロードしてインストールすることを選択した場合は、インストールが自動的に開始されます。

    ![Azure Data Box Gateway のホーム ページのスクリーンショット。[概要] オプションと、更新プログラムがダウンロードされたことを通知するバナーが強調表示されています。](./media/data-box-gateway-apply-updates/portal-apply-update-10a.png)

    更新プログラムのダウンロードのみを選択した場合は、通知を選択して **[デバイスの更新プログラム]** ブレードを開きます。 **[インストール]** を選択します。
  
    ![[デバイスの更新プログラム] ダイアログ ボックスのスクリーンショット。[インストール] オプションが強調表示されています。](./media/data-box-gateway-apply-updates/portal-apply-update-11a.png)

5. インストールが進行中であることが通知されます。

    ![Azure Data Box Gateway のホーム ページのスクリーンショット。[概要] オプションと、ダウンロードとインストールが進行中であることを示すメッセージが強調表示されています。](./media/data-box-gateway-apply-updates/portal-apply-update-12a.png)

    ポータルには、インストールが進行中であることを示す情報アラートも表示されます。 <!-- The device goes offline and is in maintenance mode.-->

    <!-- ![Software version after update](./media/data-box-gateway-apply-updates/update-13.png)-->

6. これは 1 ノードのデバイスなので、更新プログラムのインストール後にデバイスが再起動されます。 再起動中に重大なアラートが発生すると、デバイスのハートビートが失われたことが示されます。

    ![Azure Data Box Gateway のホーム ページのスクリーンショット。[概要] オプションと、重大なアラートを通知するバナーが強調表示されています。](./media/data-box-gateway-apply-updates/portal-apply-update-19a.png)

    アラートを選択して、対応するデバイス イベントを確認します。

    ![[デバイスのイベント] セクションのスクリーンショット。[Lost heartbeat from your device]\(デバイスからのハートビート喪失\) イベントが強調表示されています。](./media/data-box-gateway-apply-updates/portal-apply-update-20a.png)

7. 更新プログラムがインストールされると、デバイスの状態が **オンライン** に更新されます。

    ![Azure Data Box Gateway のホーム ページのスクリーンショット。[概要] オプションとオンライン状態が強調表示されています。](./media/data-box-gateway-apply-updates/portal-apply-update-23a.png)

    上部のコマンド バーで、 **[デバイスの更新プログラム]** を選択します。 更新プログラムが正常にインストールされたこと、およびデバイス ソフトウェアのバージョンにそれが反映されていることを確認します。

    ![[デバイスの更新プログラム] ダイアログ ボックスのスクリーンショット。[インストール済みのソフトウェアのバージョン] セクションが強調表示されています。](./media/data-box-gateway-apply-updates/portal-apply-update-24.png)

## <a name="use-the-local-web-ui"></a>ローカル Web UI を使用する

ローカル Web UI を使用するときは、2 つの手順があります。

- 更新プログラムまたは修正プログラムをダウンロードする
- 更新プログラムまたは修正プログラムをインストールする

以下のセクションでは、これらの各手順について詳しく説明します。

### <a name="download-the-update-or-the-hotfix"></a>更新プログラムまたは修正プログラムをダウンロードする

次の手順を行い、更新プログラムをダウンロードします。 更新プログラムは、Microsoft が提供する場所または Microsoft Update カタログからダウンロードできます。

次の手順を行い、Microsoft Update カタログから更新プログラムをダウンロードします。

1. ブラウザーを起動し、[https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) に移動します。

   ![ブラウザー ウィンドウに表示された Microsoft Update カタログのスクリーンショット。[検索] テキスト ボックスに「Data Box Gateway」と入力されています。 ブラウザーのアドレス バーと [検索] テキスト ボックスが強調表示されています。](./media/data-box-gateway-apply-updates/download-update-1.png)

2. Microsoft Update カタログの検索ボックスに、ダウンロードする修正プログラムのサポート技術情報 (KB) 番号または更新プログラムの用語を入力します。 たとえば、「**Azure Data Box Gateway**」と入力し、 **[検索]** をクリックします。

   更新プログラムのリストが、**Azure Data Box Gateway 1911** として表示されます。

   ![ブラウザー ウィンドウに表示された Microsoft Update カタログのスクリーンショット。「Data Box Gateway」の検索結果が強調表示されています。](./media/data-box-gateway-apply-updates/download-update-2.png)

3. **[Download]** を選択します。 デバイスのソフトウェア更新プログラムに対応する *SoftwareUpdatePackage.exe* というダウンロードする 1 つのファイルがあります。 このファイルをローカル システム上のフォルダーにダウンロードします。 デバイスからアクセスできるネットワーク共有に、このフォルダーをコピーすることもできます。

   ![[ダウンロード] ダイアログ ボックスのスクリーンショット。ソフトウェアの更新パッケージの .exe ファイルへのリンクが強調表示されています。](./media/data-box-gateway-apply-updates/download-update-3.png)

### <a name="install-the-update-or-the-hotfix"></a>更新プログラムまたは修正プログラムをインストールする

更新プログラムまたは修正プログラムをインストールする前に、次のことを確認してください。

- 更新プログラムまたは修正プログラムがローカルでホストにダウンロードされている、またはネットワーク共有を介してアクセスできる。
- ローカル Web UI の **[概要]** ページに示されているように、デバイスの状態が正常であること。

   ![Data Box Gateway のローカル Web UI のスクリーンショット。[ダッシュボード] オプションと、ソフトウェアの状態が正常であることを示すメッセージが強調表示されています。](./media/data-box-gateway-apply-updates/local-ui-update-1.png)

この手順の所要時間は約 20 分です。 次の手順を実行して、更新プログラムまたは修正プログラムをインストールします。

1. ローカル Web UI で、 **[メンテナンス]**  >  **[ソフトウェア更新プログラム]** に移動します。 実行しているソフトウェアのバージョンをメモしておきます。

   ![Data Box Gateway のローカル Web UI のスクリーンショット。[ソフトウェア更新プログラム] オプションと現在のソフトウェアのバージョンを示すメッセージが強調表示されています。](./media/data-box-gateway-apply-updates/local-ui-update-2.png)

2. 更新プログラム ファイルのパスを指定します。 更新プログラムのインストール ファイルがネットワーク共有にある場合は、ファイルを参照することもできます。 *SoftwareUpdatePackage.exe* サフィックスが付いたソフトウェア更新プログラム ファイルを選択します。

   ![エクスプローラーのスクリーンショット。ソフトウェアの更新パッケージの .exe ファイルが強調表示されています。](./media/data-box-gateway-apply-updates/local-ui-update-3.png)

3. **[適用]** を選択します。

   ![Data Box Gateway のローカル Web UI のスクリーンショット。[ソフトウェア更新プログラム] オプション、[ファイル パスの更新] テキスト ボックス、[Apply Update]\(更新プログラムの適用\) オプションが強調表示されています。](./media/data-box-gateway-apply-updates/local-ui-update-4.png)

4. 確認を求められたら、 **[はい]** を選択して続行します。 デバイスは単一ノード デバイスであることから、更新プログラムが適用された後にデバイスが再起動され、ダウンタイムが発生します。

   ![[ソフトウェア更新プログラム] ダイアログ ボックスのスクリーンショット。[はい] オプションが強調表示されています。](./media/data-box-gateway-apply-updates/local-ui-update-5.png)

5. 更新プログラムが開始します。 デバイスが正常に更新されると、再起動されます。 この期間は、ローカル UI にはアクセスできません。

6. 再起動が完了したら、 **サインイン** ページが表示されます。 デバイス ソフトウェアが更新されたことを確認するには、ローカル Web UI で、 **[メンテナンス]**  >  **[ソフトウェア更新プログラム]** に移動します。 この例で表示されるソフトウェアのバージョンは **1.6.1049.786** です。

   ![Data Box Gateway のローカル Web UI のスクリーンショット。[ソフトウェア更新プログラム] オプションと、更新後の現在のソフトウェア バージョンを示すメッセージが強調表示されています。](./media/data-box-gateway-apply-updates/local-ui-update-6.png)

## <a name="next-steps"></a>次のステップ

[Azure Data Box Gateway の管理](data-box-gateway-manage-users.md)について説明します。
