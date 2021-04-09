---
title: Windows Virtual Desktop に Microsoft Office FSLogix アプリケーション コンテナーをインストールする - Azure
description: Windows Virtual Desktop の Office でアプリ ルール エディターを使用して FSLogix アプリケーション コンテナーを作成する方法。
author: Heidilohr
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7d66d370f321323ec1aeb45ad0fe628904b14fe6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744021"
---
# <a name="install-microsoft-office-using-fslogix-application-containers"></a>FSLogix アプリケーション コンテナーを使用して Microsoft Office をインストールする

ホスト プール内の他の仮想マシン (VM) 用のテンプレートとして FSLogix アプリケーション コンテナーを使用することにより、Microsoft Office をすばやく効率的にインストールできます。

FSLogix アプリコンテナーを使用するとインストールが速くなる理由は次のとおりです。

- Office アプリをアプリ コンテナーにオフロードすると、C ドライブに必要なサイズが減ります。
- VM のスナップショットまたはバックアップのリソースが少なくなります。
- 1 つのイメージの更新による自動化されたパイプラインを使用すると、VM の更新が容易になります。
- Windows Virtual Desktop のデプロイに含まれるすべての VM にインストールする必要のある Office (および他のアプリ) のイメージが 1 つだけで済みます。

この記事では、Office で FSLogix アプリケーション コンテナーを設定する方法について説明します。

## <a name="requirements"></a>要件

ルール エディターを設定するには、次のものが必要です。

- Office がインストールされていない Windows が実行されている VM
- Office のコピー
- デプロイにインストールする FSLogix のコピー
- ホスト プール内のすべての VM から読み取り専用でアクセスできるネットワーク共有

## <a name="install-office"></a>Office をインストールする

VHD または VHDX に Office をインストールするには、VM でリモート デスクトップ プロトコルを有効にした後、[VHD マスター イメージへの Office のインストール](install-office-on-wvd-master-image.md)に関する記事の手順に従います。 インストールするときに、[正しいライセンス](overview.md#requirements)を使用していることを確認します。

>[!NOTE]
>Windows Virtual Desktop には、共有コンピューターのライセンス認証 (SCA) が必要です。

## <a name="install-fslogix"></a>FSLogix をインストールする

FSLogix とルール エディターをインストールするには、「[FSLogix をダウンロードしてインストールする](/fslogix/install-ht)」の手順のようにします。

## <a name="create-and-prepare-a-vhd-to-store-office"></a>Office を格納するための VHD を作成して準備する

次に、ルール エディターを使用するための VHD イメージを作成して準備する必要があります。

1. コマンド プロンプトを管理者として開きます。 次のコマンドを実行します。

    ```cmd
        taskkill /F /IM MicrosoftEdge.exe /T
    ```

    >[!NOTE]
    > 空白スペースをこのコマンドのとおりにしてください。

2. 次に、以下のコマンドを実行します。

    ```cmd
    sc queryex type=service state=all | find /i "ClickToRunSvc"
    ```
    
   サービスが見つかった場合は、ステップ 3 に進む前に VM を再起動します。

    ```cmd
    net stop ClickToRunSvc
    ```

3. その後、 **[Program Files]**  >  **[FSLogix]**  >  **[Apps]** に移動し、次のコマンドを実行してターゲット VHD を作成します。

    ```cmd
    frx moveto-vhd -filename <path to network share>\office.vhdx -src "C:\Program Files\Microsoft Office" -size-mbs 5000 
    ```

    このコマンドで作成した VHD には、C:\\Program Files\\Microsoft Office フォルダーが含まれているはずです。

    >[!NOTE]
    >エラーが発生する場合は、Office をアンインストールし、ステップ 1 からやり直してください。

## <a name="configure-the-rule-editor"></a>ルール エディターを構成する

イメージの準備ができたので、ルール エディターを構成し、ルールを格納するファイルを作成する必要があります。

1. **[Program Files]**  >  **[FSLogix]**  >  **[Apps]** に移動し、**RuleEditor.exe** を実行します。

2. **[File]\(ファイル\)**  >  **[New]\(新規\)**  >  **[Create]\(作成\)** を選択して新しいルール セットを作成し、そのルール セットをローカル フォルダーに保存します。

3. **[Blank Rule Set]\(空のルール セット\)** を選択し、 **[OK]** を選択します。

4. **[+]\(適用\)** ボタンを選択します。 **[Add Rule]\(ルールの追加\)** ウィンドウが開きます。 これにより、 **[Add Rule]\(ルールの追加\)** ダイアログ ボックスのオプションが変更されます。

5. ドロップダウン メニューから、 **[App Container (VHD) Rule]\(アプリ コンテナー (VHD) のルール\)** を選択します。

6. **[Folder]\(フォルダー\)** フィールドに「**C:\\Program Files\\Microsoft Office**」と入力します。

7. **[Disk file]\(ディスク ファイル\)** フィールドの **[Create target VHD]\(ターゲット VHD の作成\)** セクションで **[\<path\>\\office.vhd]** を選択します。

8. **[OK]** を選択します。

9. **C:\\Users\\\<username\>\\Documents\\FSLogix Rule Sets** の作業フォルダーに移動し、.frx ファイルと .fxa ファイルを探します。 ルールを動作させるには、これらのファイルを **C:\\Program Files\\FSLogix\\Apps\\Rules** にある [Rules] フォルダーに移動する必要があります。

10. **[Apply Rules to System]\(ルールをシステムに適用\)** を選択してルールを有効にします。

     >[!NOTE]
     > すべてのセッション ホストにアプリ ルール ファイルを適用する必要があります。

## <a name="next-steps"></a>次のステップ

FSLogix の詳細については、[FSLogix のドキュメント](/fslogix/)を参照してください。