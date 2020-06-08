---
title: Azure Virtual Machines 上の Docker コンテナーで Micro Focus Enterprise Server 4.0 を実行する
description: Azure Virtual Machines 上の Docker コンテナーで Micro Focus Enterprise Server を実行することにより、IBM z/OS メインフレームのワークロードをリホストします。
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "61488462"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>Azure 上の Docker コンテナーで Micro Focus Enterprise Server 4.0 を実行する

Azure 上の Docker コンテナーで Micro Focus Enterprise Server 4.0 を実行することができます。 このチュートリアルでは、その方法を説明します。 Enterprise Server 用の Windows CICS (顧客情報管理システム) acctdemo デモを使用します。

Docker により、移植性と分離性がアプリケーションに追加されます。 たとえば、ある Windows VM から Docker イメージをエクスポートして別の VM で実行したり、Docker を使用してリポジトリから Windows Server にエクスポートしたりできます。 Docker イメージは同じ構成を使用して新しい場所で実行され、Enterprise Server をインストールする必要はありません。 それはイメージの一部です。 ライセンスに関する考慮事項は引き続き適用されます。

このチュートリアルでは、Azure Marketplace から **Windows 2016 Datacenter with Containers** 仮想マシン (VM) をインストールします。 この VM には **Docker 18.09.0** が含まれます。 以下の手順では、コンテナーをデプロイし、実行してから、3270 エミュレーターでそれに接続する方法を示します。

## <a name="prerequisites"></a>前提条件

開始する前に、次の前提条件を確認してください。

- Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

- Micro Focus ソフトウェアおよび有効なライセンス (または試用版ライセンス)。 Micro Focus の既存の顧客である場合は、Micro Focus の担当者に問い合わせてください。 そうでない場合は、[試用版を要求](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)してください。

     > [!NOTE]
     > Docker のデモ ファイルは、Enterprise Server 4.0 に含まれています。 このチュートリアルでは、ent\_server\_dockerfiles\_4.0\_windows.zip を使用します。 Enterprise Server のインストール ファイルにアクセスしたのと同じ場所からそれにアクセスするか、*Micro Focus* に移動して、開始します。

- [Enterprise Server と Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#") のドキュメント。

## <a name="create-a-vm"></a>VM の作成

1. ent\_server\_dockerfiles\_4.0\_windows.zip ファイルからのメディアをセキュリティ保護します。 ES-Docker-Prod-XXXXXXXX.mflic ライセンス ファイルをセキュリティ保護します (Docker イメージのビルドに必要)。

2. VM を作成します。 これを行うには、Azure portal を開き、左上の **[リソースの作成]** を選択して、*windows server* でフィルター処理します。 結果で、"**Windows Server 2016 Datacenter – コンテナー付き**" を選択します。

     ![Azure portal での検索結果](media/01-portal.png)

3. VM のプロパティを構成するには、インスタンスの詳細を選択します。

    1. VM のサイズを選択します。 このチュートリアルでは、2 個の vCPU と 7 GB のメモリを備えた **Standard DS2\_v2** VM を使用します。

    2. **[リージョン]** を選択し、デプロイ先の **[リソース グループ]** を選択します。

    3. **[可用性オプション]** では、既定の設定を使用します。

    4. **[ユーザー名]** には、使用する管理者アカウントとパスワードを入力します。

    5. **ポート 3389 RDP** が開かれていることを確認します。 VM にサインインできるよう、このポートのみをパブリックに公開する必要があります。 すべての既定値をそのまま使用して、 **[確認と作成]** をクリックします。

     ![仮想マシンの作成ウィンドウ](media/container-02.png)

4. デプロイが完了するのを待ちます (2 分)。 VM が作成されたことを示すメッセージが表示されます。

5. **[リソースに移動]** をクリックして、VM の **[概要]** ブレードに移動します。

6. 右側の **[接続]** ボタンをクリックします。 右側に **[仮想マシンに接続する]** オプションが表示されます。

7. **[RDP ファイルのダウンロード]** ボタンをクリックして、VM にアタッチできる RDP ファイルをダウンロードします。

8. ファイルのダウンロードが完了したら、それを開き、VM 用に作成したユーザー名とパスワードを入力します。

     > [!NOTE]
     > 会社の資格情報を使用してサインインしないでください。 (RDP クライアントではそれを使用することが想定されています。 使用しないでください。)

9.  **[その他]** を選択し、ご自分の VM の資格情報を選択します。

この時点で、VM は実行されており、RDP を介してアタッチされています。 サインインが済み、次のステップの準備ができています。

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>サンドボックス ディレクトリを作成し、zip ファイルをアップロードする

1.  デモとライセンス ファイルをアップロードできるディレクトリを VM 上に作成します。 たとえば、**C:\\Sandbox** などです。

2.  **ent\_server\_dockerfiles\_4.0\_windows.zip** と、**ES-Docker-Prod-XXXXXXXX.mflic** ファイルを、作成したディレクトリにアップロードします。

3.  zip ファイルの内容を、抽出プロセスによって作成される **ent\_server\_dockerfiles\_4.0\_windows** ディレクトリに抽出します。 このディレクトリには、readme ファイル (.html ファイルと .txt ファイル) と、2 つのサブディレクトリ **EnterpriseServer** および **Examples** が含まれます。

4.  **ES-Docker-Prod-XXXXXXXX.mflic** を、C:\\Sandbox\\ent\_server\_dockerfiles\_4.0\_windows\\EnterpriseServer ディレクトリと、C:\\Sandbox\\ent\_server\_dockerfiles\_4.0\_windows\\Examples\\CICS ディレクトリにコピーします。

> [!NOTE]
> 必ずライセンス ファイルを両方のディレクトリにコピーします。 Docker のビルド ステップでイメージが適切にライセンスされるために必要です。

## <a name="check-docker-version-and-create-base-image"></a>Docker のバージョンを確認し、基本イメージを作成する

> [!IMPORTANT]
> 適切な Docker イメージの作成は、2 段階のプロセスです。 最初に、Enterprise Server 4.0 の基本イメージを作成します。 次に、x64 プラットフォーム用の別のイメージを作成します。 x86 (32 ビット) イメージも作成できますが、64 ビット イメージを使用します。

1. コマンド プロンプトを開きます。

2. Docker がインストールされていることを確認します。 コマンド プロンプトに、次のコマンドを入力します。

    ```
        docker version
    ```

     たとえば、これを書いた時点では、バージョンは 18.09.0 でした。

3. ディレクトリを変更するには、「**cd \Sandbox\ent_server_dockerfiles_4.0_windows\EnterpriseServer**」と入力します。

4. 「**bld.bat IacceptEULA**」と入力して、初期の基本イメージのビルド プロセスを開始します。 このプロセスが実行されるまで、数分間待ちます。 結果では、2 つのイメージが作成されたことに注意してください (x64 用と x86 用)。

     ![イメージが表示されたコマンド ウィンドウ](media/container-04.png)

5. CICS デモ用の最終的なイメージを作成するには、「**cd\\Sandbox\\ent\_server\_dockerfiles\_4.0\_windows\\Examples\\CICS**」と入力して CICS ディレクトリに切り替えます。

6. イメージを作成するには、「**bld.bat x64**」と入力します。 プロセスが実行され、イメージが作成されたことを示すメッセージが表示されるまで、数分間待ちます。

7. 「**docker images**」と入力し、VM にインストールされているすべての Docker イメージの一覧を表示します。 その中に **microfocus/es-acctdemo** があることを確認します。

     ![es-acctdemo イメージが表示されているコマンド ウィンドウ](media/container-05.png)

## <a name="run-the-image"></a>イメージを実行する 

1.  Enterprise Server 4.0 と acctdemo アプリケーションを起動するには、コマンド プロンプトで次のように入力します。

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  [x3270](http://x3270.bgp.nu/) などの 3270 ターミナルエミュレーターをインストールし、それを使用してポート 9040 経由で実行しているイメージにアタッチします。

3.  Docker が管理するコンテナーに対して DHCP サーバーとして機能できるように、acctdemo コンテナーの IP アドレスを取得します。

    1.  実行中のコンテナーの ID を取得します。 コマンド プロンプトで「**Docker ps**」と入力し、ID を書き留めます (この例では **22a0fe3159d0**)。 次のステップのためにそれを保存します。

    2.  acctdemo コンテナーの IP アドレスを取得するには、前のステップのコンテナー ID を使用して次のようにします。

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       次に例を示します。

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. acctdemo イメージの IP アドレスを書き留めます。 たとえば、次の出力ではアドレスは 172.19.202.52 です。

     ![IP アドレスが表示されているコマンド ウィンドウ](media/container-06.png)

5. エミュレーターを使用してイメージをマウントします。 acctdemo イメージのアドレスとポート 9040 を使用するように、エミュレーターを構成します。 ここでは、**172.19.202.52:9040** です。 実際もこれに似ています。 **[Signon to CICS]\(CICS にサインオン\)** 画面が開きます。

    ![CICS へのサインオン画面](media/container-07.png)

6. **[USERID]\(ユーザー ID\)** に「**SYSAD**」と入力し、 **[Password]\(パスワード\)** に「**SYSAD**」と入力して、CICS 領域にサインインします。

7. エミュレーターのキーマップを使用して、画面をクリアします。 x3270 の場合は、 **[Keymap]\(キーマップ\)** メニュー オプションを選択します。

8. acctdemo アプリケーションを起動するには、「**ACCT**」と入力します。 アプリケーションの最初の画面が表示されます。

     ![アカウント デモ画面](media/container-08.png)

9. 表示アカウントの種類を調べます。 たとえば、要求の場合は「**D**」と入力し、**アカウント**の場合は「**11111**」と入力します。 22222、33333 など、他のアカウント番号を試してください。

     ![アカウント デモ画面](media/container-09.png)

10. Enterprise Server 管理コンソールを表示するには、コマンド プロンプトに移動し、「**start http:172.19.202.52:86**」と入力します

     ![Enterprise Server 管理コンソール](media/container-010.png)

これで完了です。 今は、Docker コンテナーで CICS アプリケーションを実行し、管理しています。

## <a name="next-steps"></a>次のステップ

- [Azure で Micro Focus Enterprise Server 4.0 および Enterprise Developer 4.0 をインストールする](./set-up-micro-focus-azure.md)
- [メインフレーム アプリケーションの移行](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
