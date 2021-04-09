---
title: Ubuntu (18.04 x64) Simulator Reference Agent を使用した Device Update for Azure IoT Hub のチュートリアル | Microsoft Docs
description: Ubuntu (18.04 x64) Simulator Reference Agent を使用して、Device Update for Azure IoT Hub を開始します。
author: valls
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 4740bf02c33314dd7c887356f2ef1ed12bea44cf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443813"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-ubuntu-1804-x64-simulator-reference-agent"></a>Ubuntu (18.04 x64) Simulator Reference Agent を使用した Device Update for Azure IoT Hub のチュートリアル

Device Update for IoT Hub では、イメージベースとパッケージベースの 2 つの形式の更新がサポートされています。

イメージ更新を使用すると、デバイスの最終状態の信頼性が高くなります。 通常、運用前環境と運用環境の間でイメージ更新の結果をレプリケートする方が簡単です。そうすれば、パッケージやその依存関係の場合と同様の課題が生じることがないからです。 アトミックな性質のため、A/B フェールオーバー モデルを簡単に導入することもできます。

このチュートリアルでは、Device Update for IoT Hub を使用して、エンド ツー エンドのイメージベースの更新を行う手順について説明します。 

このチュートリアルで学習する内容は次のとおりです。
> [!div class="checklist"]
> * イメージをダウンロードしてインストールする
> * IoT デバイスにタグを追加する
> * 更新プログラムをインポートする
> * デバイス グループを作成する
> * イメージの更新プログラムをデプロイする
> * 更新プログラムのデプロイを監視する

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件
* IoT Hub へのアクセス。 S1 (Standard) レベル以上を使用することをお勧めします。

### <a name="download-and-install"></a>ダウンロードしてインストールする

* PowerShell の Az (Azure CLI) コマンドレット:
  * PowerShell を開き、"Install Azure CLI" を実行します ("信頼されていない" ソースからインストールするか尋ねるプロンプトで "Y" を選択します)

```powershell
PS> Install-Module Az -Scope CurrentUser
```

### <a name="enable-wsl-on-your-windows-device-windows-subsystem-for-linux"></a>Windows デバイスで WSL を有効にする (Linux 用 Windows サブシステム)

1. お使いのコンピューターで PowerShell を管理者として開き、次のコマンドを実行します (各手順の後に再起動するように求められる場合があります。その場合は再起動してください)。

```powershell
PS> Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
PS> Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

(*この手順の後に再起動を求めるメッセージが表示される場合があります*)

2. Web 上の Microsoft Store にアクセスし、[Ubuntu 18.04 LTS](https://www.microsoft.com/p/ubuntu-1804-lts/9n9tngvndl3q?activetab=pivot:overviewtab`) をインストールします。

3. "Ubuntu 18.04 LTS" を起動して、インストールします。

4. インストールすると、ルート名 (ユーザー名) とパスワードを設定するよう求められます。 覚えやすいルート名とパスワードを使用してください。

5. PowerShell で次のコマンドを実行して、Ubuntu を既定の Linux ディストリビューションに設定します。

```powershell
PS> wsl --setdefault Ubuntu-18.04
```

6. すべての Linux ディストリビューションを一覧表示して、Ubuntu が既定のディストリビューションであることを確認します。

```powershell
PS> wsl --list
```

7. **Ubuntu-18.04 (既定値)** のように表示されます。

## <a name="download-device-update-ubuntu-1804-x64-simulator-reference-agent"></a>Device Update Ubuntu (18.04 x64) Simulator Reference Agent のダウンロード

Ubuntu 更新イメージは、[こちらから](https://github.com/Azure/iot-hub-device-update/releases)入手可能なリリース ノートの「*アセット*」セクションでダウンロードできます。

エージェントには 2 つのバージョンがあります。 イメージベースのシナリオを使用する場合は、AducIotAgentSim-microsoft-swupdate を使用します。パッケージベースのシナリオを使用する場合は、AducIotAgentSim-microsoft-apt を使用します。

## <a name="install-device-update-agent-simulator"></a>デバイス更新エージェント シミュレーターのインストール

1. Ubuntu WSL を起動し、次のコマンドを入力します (末尾に余分なスペースとドットがあることに注意してください)。

  ```shell
  explorer.exe .
  ```

2. AducIotAgentSim-microsoft-swupdate (または AducIotAgentSim-microsoft-apt) をローカル フォルダー (/mnt の下でダウンロードした場所) から WSL のホーム フォルダーにコピーします。

3. 次のコマンドを実行して、バイナリを実行可能ファイルにします。

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-swupdate
  ```

  or

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-apt
  ```
Device Update for Azure IoT Hub ソフトウェアには、次のライセンス条項が適用されます。
   * [Device Update for IoT Hub ライセンス](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [配信の最適化のクライアント ライセンス](https://github.com/microsoft/do-client/blob/main/LICENSE.md)
   
エージェントを使用する前に、ライセンス条項をお読みください。 インストールして使用すると、これらの条項に同意したものと見なされます。 ライセンス条項に同意しない場合は、Device Update for IoT Hub エージェントをお使いいただけません。

## <a name="add-device-to-azure-iot-hub"></a>デバイスを Azure IoT Hub に追加する

IoT デバイスでデバイス更新エージェントを実行するには、デバイスを Azure IoT Hub に追加する必要があります。  Azure IoT Hub で、特定のデバイスの接続文字列が生成されます。

1. Azure portal から Device Update IoT Hub を起動します。
2. 新しいデバイスを作成します。
3. ページの左側で、[エクスプローラー] > [IoT デバイス] の順に移動し、[新規] を選択します。
4. [デバイス ID] にデバイスの名前を指定します。[キーの自動生成] チェックボックスがオンになっていることを確認します。
5. [保存] を選択します。
6. [デバイス] ページに戻ると、作成したデバイスが一覧に表示されているはずです。 そのデバイスを選択します。
7. デバイス ビューで、[プライマリ接続文字列] の横にある [コピー] アイコンを選択します。
8. 後で以下の手順で使用できるように、コピーした文字をどこかに貼り付けておきます。 **このコピーした文字列は、対象のデバイスの接続文字列** です。

## <a name="add-connection-string-to-simulator"></a>シミュレーターに接続文字列を追加する

新しいソフトウェア デバイスでデバイス更新エージェントを起動します。

1. Ubuntu を起動します。
2. デバイス更新エージェントを実行し、前のセクションでコピーしたデバイス接続文字列をアポストロフィで囲んで指定します。

`<device connection string>` を対象の接続文字列に置き換えてください
```shell
./AducIotAgentSim-microsoft-swupdate -c '<device connection string>'
```

or

```shell
./AducIotAgentSim-microsoft-apt -c '<device connection string>'
```

3. 上にスクロールし、デバイスが "アイドル" 状態であることを示す文字列を探します。 "アイドル" 状態は、そのデバイスでサービス コマンドを実行する準備ができていることを示します。

```markdown
Agent running. [main]
```

## <a name="add-a-tag-to-your-device"></a>デバイスにタグを追加する

1. [Azure portal](https://portal.azure.com) にログインし、IoT Hub に移動します。

2. 左側のナビゲーション ウィンドウの [IoT デバイス] または [IoT Edge] から、対象の IoT デバイスを見つけて、[デバイス ツイン] に移動します。

3. デバイス ツインで、Device Update の既存のタグ値を null に設定することで削除します。

4. 次に示すように、Device Update の新しいタグ値を追加します。

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="import-update"></a>更新プログラムをインポートする

1. [手順](import-update.md)に従ってインポート マニフェストを作成します。
2. 左側のナビゲーション バーの [自動デバイス管理] にある [デバイスの更新] オプションを選択します。

3. [更新プログラム] タブを選択します。

4. [+ 新しい更新プログラムのインポート] を選択します。

5. [インポート マニフェスト ファイルを選択] の下にあるフォルダー アイコンまたはテキスト ボックスを選択します。 ファイル ピッカーのダイアログが表示されます。 先ほど作成したインポート マニフェストを選択します。  次に、[1 つまたは複数の更新プログラム ファイルの選択] の下にあるフォルダー アイコンまたはテキスト ボックスを選択します。 ファイル ピッカーのダイアログが表示されます。 先ほどダウンロードした Ubuntu 更新イメージを選択します。 

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="更新ファイルの選択を示すスクリーンショット。" lightbox="media/import-update/select-update-files.png":::

6. [ストレージ コンテナーの選択] の下にあるフォルダー アイコンまたはテキスト ボックスを選択します。 次に、適切なストレージ アカウントを選択します。

7. コンテナーを既に作成してある場合は、再利用できます (そうでない場合は、[+ コンテナー] を選択して、更新用の新しいストレージ コンテナーを作成します)。  使用するコンテナーを選択し、[選択] をクリックします。
  
  :::image type="content" source="media/import-update/container.png" alt-text="コンテナーの選択を示すスクリーンショット。" lightbox="media/import-update/container.png":::

8. [送信] を選択してインポート プロセスを開始します。

9. インポート プロセスが開始され、画面が [インポートの履歴] セクションに変わります。 [更新] を選択すると、インポート プロセスが完了するまで進行状況が表示されます。 更新プログラムのサイズに応じて、これは数分で完了する場合も、それよりも長くかかる場合もあります。
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="更新プログラムのインポート シーケンスを示すスクリーンショット。" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. [状態] 列にインポートが正常に完了したと表示されたら、[デプロイの準備完了] ヘッダーを選択します。 インポートされた更新プログラムが一覧に表示されるはずです。

更新プログラムのインポートについて詳しくは、[こちら](import-update.md)をご覧ください。

## <a name="create-update-group"></a>更新プログラム グループを作成する

1. 以前に Device Update インスタンスに接続した IoT Hub にアクセスします。

2. 左側のナビゲーション バーの [自動デバイス管理] にある [デバイスの更新] オプションを選択します。

3. ページの上部にある [グループ] タブを選択します。 

4. [追加] ボタンを選択して、新しいグループを作成します。

5. 前の手順で作成した IoT Hub のタグを一覧から選択します。 [更新プログラム グループの作成] を選択します。

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="タグの選択を示すスクリーンショット。" lightbox="media/create-update-group/select-tag.PNG":::

タグの追加と更新プログラム グループの作成に関する[詳細情報](create-update-group.md)


## <a name="deploy-update"></a>更新プログラムをデプロイする

1. グループが作成されると、デバイス グループで利用可能な新しい更新プログラムが表示され、[保留中の更新プログラム] の下にその更新プログラムへのリンクが表示されます。 場合によっては、1 回更新する必要があります。 

2. 利用可能な更新プログラムをクリックします。

3. 対象のグループとして、適切なグループが選択されていることを確認します。 展開をスケジュールし、[Deploy update]\(更新プログラムの展開\) を選択します。

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="更新プログラムを選択する" lightbox="media/deploy-update/select-update.png":::

4. コンプライアンス チャートを表示します。 更新が進行中であると表示されます。 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="更新が進行中です" lightbox="media/deploy-update/update-in-progress.png":::

5. デバイスが正常に更新されると、コンプライアンス チャートと展開の詳細が、同じ内容を反映するように更新されていることがわかります。 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="更新に成功しました" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>更新プログラムの展開を監視する

1. ページの上部にある [展開] タブを選択します。

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="[展開] タブ" lightbox="media/deploy-update/deployments-tab.png":::

2. 作成した展開を選択して、展開の詳細を表示します。

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="展開の詳細" lightbox="media/deploy-update/deployment-details.png":::

3. [更新] を選択し、最新の状態の詳細を表示します。 状態が [成功] に変わるまで、このプロセスを続行します。

これで、Device Update for IoT Hub と Ubuntu (18.04 x64) Simulator Reference Agent を使用した、エンドツーエンドのイメージの更新が正常に完了しました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、デバイス更新アカウント、インスタンス、IoT Hub、IoT デバイスをクリーンアップします。 

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [トラブルシューティング](troubleshoot-device-update.md)

