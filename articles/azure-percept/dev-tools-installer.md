---
title: Azure Percept Dev Tools Pack Installer の概要
description: Dev Tools Pack Installer を使用して Azure Percept による高度な開発を促進する方法について説明します。
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 66498fabadc0784a4a4ab1c3762daaaa9a5738c4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503217"
---
# <a name="dev-tools-pack-installer-overview"></a>Dev Tools Pack Installer の概要

Dev Tools Pack Installer は、インテリジェント エッジ ソリューションの開発に必要なすべてのツールをインストールして構成するワンストップ ソリューションです。 以下に示すソフトウェア パッケージを既にインストールしている場合は、ツールが Dev Tools Pack Installer のソフトウェア バージョンと一致するように、この Installer によってこれらのパッケージが再インストールされます。

## <a name="mandatory-tools-installed"></a>インストールされる必須ツール

* [Visual Studio Code](https://code.visualstudio.com/)
* [Python 3.6 (Windows) または 3.5 (Linux)](https://www.python.org/)
* [Docker 19.03](https://www.docker.com/)
* [PIP3](https://pip.pypa.io/en/stable/user_guide/)
* [TensorFlow 1.13](https://www.tensorflow.org/)
* [Azure Machine Learning SDK 1.1](https://docs.microsoft.com/python/api/overview/azure/ml/)

## <a name="optional-tools-available-for-installation"></a>インストールできるオプションのツール

* [Nvidia DeepStream SDK 5](https://developer.nvidia.com/deepstream-sdk) (Nvidia アクセラレータ用ソリューションを開発するためのツールキット)
* [Intel OpenVino Toolkit 2020.2](https://docs.openvinotoolkit.org/) (Intel アクセラレータ用ソリューションを開発するためのツールキット)
* [Lobe.ai](https://lobe.ai/)  
* [Streamlit](https://www.streamlit.io/)
* [Pytorch 1.4.0 (Windows) または 1.2.0 (Linux)](https://pytorch.org/)
* [Miniconda3](https://docs.conda.io/en/latest/miniconda.html)
* [Chainer 5.2](https://chainer.org/)
* [Caffe](https://caffe.berkeleyvision.org/)
* [CUDA Toolkit 10.0.130](https://developer.nvidia.com/cuda-toolkit)
* [Microsoft Cognitive Toolkit 2.5.1](https://www.microsoft.com/research/product/cognitive-toolkit/?lang=fr_ca)

## <a name="known-issues"></a>既知の問題

- Docker がシステム上で正常に動作していない場合、オプションの Caffe のインストールは失敗する可能性があります。 Caffe をインストールする場合は、Dev Tools Pack Installer を使用して Caffe のインストールを試行する前に、Docker がインストールされ、動作していることを確認してください。 

- 互換性のないシステムでは、オプションの CUDA のインストールは失敗します。 Dev Tools Pack Installer を使用して [CUDA Toolkit 10.0.130](https://developer.nvidia.com/cuda-toolkit) をインストールする前に、システムの互換性を確認してください。

## <a name="minimum-requirements"></a>最小要件

* Docker の最小要件:

    * Windows:
        * https://docs.docker.com/docker-for-windows/install/#system-requirements

        - Windows 10 64 ビット: Pro、Enterprise、または Education (ビルド 16299 以降)

             Windows 10 Home の場合は、Windows Home での Docker Desktop のインストールに関するページを参照してください。
           - Windows の機能である Hyper-V と Containers が有効になっている必要があります。
           - Windows 10 でクライアント Hyper-V を正常に実行するには、次のハードウェアの前提条件が必要になります。

              - [第 2 レベルのアドレス変換 (SLAT)](https://en.wikipedia.org/wiki/Second_Level_Address_Translation) を備えた 64 ビット プロセッサ
              - 4 GB のシステム RAM
              - BIOS レベルのハードウェア仮想化のサポートが BIOS の設定で有効になっている必要があります。 詳細については、仮想化に関するページを参照してください。

        > [!NOTE]
        > Docker は、Microsoft の Windows 10 オペレーティング システムのサポート ライフサイクルに基づいて、Windows 上の Docker Desktop をサポートしています。 詳細については、[Windows ライフサイクルのファクト シート](https://support.microsoft.com/help/13853/windows-lifecycle-fact-sheet)に関するページを参照してください。

    * MAC: 
        * https://docs.docker.com/docker-for-mac/install/#system-requirements
       
        Docker Desktop を正常にインストールするには、Mac で次の要件を満たす必要があります。
         
         - **Mac のハードウェアは、Intel プロセッサを搭載した 2010 以降のモデルである必要があります**。この Intel のハードウェアは、Extended Page Tables (EPT) や無制限モードなど、メモリ管理ユニット (MMU) の仮想化をサポートしています。 このサポートが自分のマシンに備わっているかどうかを確認するには、ターミナルで次のコマンドを実行します: ```sysctl kern.hv_support```

        Mac でハイパーバイザー フレームワークがサポートされている場合は、このコマンドによって ```kern.hv_support: 1``` が出力されます。

         - **macOS はバージョン10.14 以降である必要があります**。 つまり、Mojave、Catalina、または Big Sur です。 macOS の最新バージョンにアップグレードすることをお勧めします。

        macOS をバージョン 10.15 にアップグレードした後も問題が発生する場合は、このバージョンの macOS との互換性のために Docker Desktop の最新バージョンをインストールする必要があります。

        - 少なくとも 4 GB の RAM。
        - バージョン 4.3.30 より前の VirtualBox は、Docker Desktop と互換性がないため、インストールしないでください。

        > [!NOTE]
        > Docker は、macOS の最近のバージョンで Docker Desktop をサポートしています。 macOS の最新リリースとその前の 2 つのリリースが該当します。 macOS の新しいメジャーバージョンが一般提供されると、Docker は最も古いバージョンのサポートを終了し、(前の 2 つのリリースに加えて) macOS の最新バージョンをサポートします。 Docker Desktop では、現在、macOS Mojave、macOS Catalina、macOS Big Sur がサポートされています。
        > 
        - インストーラーは、Apple M1 ではサポートされていません。

## <a name="instructions"></a>Instructions

1. [Windows](https://go.microsoft.com/fwlink/?linkid=2132187)、[Linux](https://go.microsoft.com/fwlink/?linkid=2132186)、[Mac](https://go.microsoft.com/fwlink/?linkid=2132296) 用の Dev Tools Pack Installer をダウンロードします。

1. プラットフォームに応じて、インストーラーの起動にいくつかの違いがあります。

    1. Windows の場合:
    
        1. **Dev-Tools-Pack-Installer** をクリックして、インストール ウィザードを開きます。
        
    1. Mac の場合:
    
        1. ダウンロード後、Dev-Tools-Pack-Installer.app ファイルを Applications フォルダーに移動します。
        
        1. **Dev-Tools-Pack-Installer.app** をクリックしてインストール ウィザードを開きます。
        
        1. "開発元が未確認" のセキュリティ ダイアログが表示された場合:
        
            1. [System Preferences]\(システム環境設定\) -> [Security & Privacy]\(セキュリティとプライバシー\) -> [General]\(一般\) に移動し、"Dev-Tools-Pack-Installer.app" の横にある [Open Anyway]\(このまま開く\) ボタンをクリックします。
        
            1. Dock にある Electron アイコンをもう一度クリックします。
        
            1. セキュリティ ダイアログの [Open]\(開く\) ボタンをクリックします。
    
    1. Linux の場合:
    
        1. ブラウザーでメッセージが表示されたら、[Save]\(保存\) をクリックしてインストーラーのダウンロードを完了します。
        
        1. **.appimage** ファイル実行権限を追加します (方法 1、コマンドライン)。
            
            1. Linux ターミナルを開きます。
            
            1. ターミナルで次のように入力して、Downloads フォルダーに移動します。
            
                1. cd ~/Downloads/
                
            1. ターミナルで次のように入力して、AppImage を実行可能にします。
            
                1. chmod +x **Dev-Tools-Pack-Installer.AppImage**
                
            1. ターミナルで次のように入力して、インストーラーを実行します。
            
                1. ./Dev-Tools-Pack-Installer.AppImage
        
        1. **.appimage** ファイルに実行権限を追加します (方法 2、UI)。
        
            1. .appimage ファイルを右クリックし、プロパティを選択します。
            
            1. [Permissions]\(権限\) タブを開きます。
            
            1. [Allow executing file as a program]\(実行ファイルをプログラムとして許可する\) チェック ボックスをオンにします。
            
            1. プロパティを閉じて、.appimage ファイルを開きます。

1. **[Install Dev Tools Pack Installer]\(Dev Tools Pack Installer のインストール\)** ページで、 **[View license]\(ライセンスの表示\)** をクリックして、このインストーラーに含まれる各ソフトウェア パッケージのライセンス契約を表示します。 ライセンス契約の条項に同意する場合は、このチェック ボックスをオンにし、 **[Next]\(次へ\)** をクリックします。

    :::image type="content" source="./media/dev-tools-installer/dev-tools-license-agreements.png" alt-text="インストーラーのライセンス契約画面。":::

1. **[Privacy Statement]\(プライバシーに関する声明\)** をクリックして、Microsoft プライバシーに関する声明を確認します。 プライバシーに関する声明に同意し、診断データを Microsoft に送信する場合は、 **[Yes]\(はい\)** を選択して、 **[Next]\(次へ\)** をクリックします。 それ以外の場合は、 **[No]\(いいえ\)** を選択して、 **[Next]\(次へ)** をクリックします。

    :::image type="content" source="./media/dev-tools-installer/dev-tools-privacy-statement.png" alt-text="インストーラーのプライバシーに関する声明の同意画面。":::

1. **[Configure Components]\(コンポーネントの構成)** ページで、インストールするオプションのツールを選択します (必須のツールは既定でインストールされます)。

    1. Azure Percept DK に含まれている Azure Percept Audio SoM を使用している場合は、必ず Intel OpenVino Toolkit と Miniconda3 をインストールしてください。

    1. **[Install]\(インストール\)** をクリックしてインストールを続行します。

    :::image type="content" source="./media/dev-tools-installer/dev-tools-configure-components.png" alt-text="入手可能なソフトウェア パッケージを示すインストーラー画面。":::

1. 選択したすべてのコンポーネントが正常にインストールされると、ウィザードは **[Completing the Setup Wizard]\(セットアップ ウィザードの完了\)** ページに進みます。 **[Finish]\(完了\)** をクリックして、ウィザードを終了します。

    :::image type="content" source="./media/dev-tools-installer/dev-tools-finish.png" alt-text="インストーラーの完了画面。":::

## <a name="docker-status-check"></a>Docker の状態の確認

Docker Desktop が良好な状態で実行されていることを確認するようにインストーラーから通知された場合は、次の手順を参照してください。

   1. Windows:
   
      1. システム トレイの非表示アイコンを展開します。
      
         1. システム トレイの非表示アイコンを展開します (非表示の場合)。

            :::image type="content" source="./media/dev-tools-installer/system-tray.png" alt-text="システム トレイ。":::
         
         1. Docker Desktop アイコンで "Docker Desktop is Running (Docker Desktop が実行中)" と示されていることを確認します。

            :::image type="content" source="./media/dev-tools-installer/docker-status-running.png" alt-text="Docker の状態。":::
         
         1. 上のアイコンがシステム トレイに表示されていない場合は、[スタート] メニューから Docker Desktop を起動します。
         
         1. Docker から再起動するようメッセージが表示された場合は、再起動が完了し、Docker の状態が実行中になった後に、インストーラーを閉じてから再開しても問題はありません。 正常にインストールされたサードパーティ製アプリケーションは検出されるため、自動的に再インストールされることはありません。

## <a name="next-steps"></a>次のステップ

Azure Percept DK 向けの高度な開発を始めるには、「[Azure Percept の高度な開発リポジトリ](https://github.com/microsoft/azure-percept-advanced-development)」を参照してください。