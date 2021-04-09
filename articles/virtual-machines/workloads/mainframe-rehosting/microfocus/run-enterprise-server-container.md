---
title: Azure 上の Docker コンテナーで Micro Focus Enterprise Server 5.0 を実行する | Microsoft Docs
description: この記事では、Microsoft Azure 上の Docker コンテナーで Micro Focus Enterprise Server 5.0 を実行する方法について説明します。
services: virtual-machines
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 146fc59704719fd3aebb03b9b90a176221beea10
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950690"
---
# <a name="run-micro-focus-enterprise-server-50-in-a-docker-container-on-azure"></a>Azure 上の Docker コンテナーで Micro Focus Enterprise Server 5.0 を実行する

Azure 上の Docker コンテナーで Micro Focus Enterprise Server 5.0 を実行することができます。 このチュートリアルでは、その方法を説明します。 Enterprise Server 用の Windows CICS (顧客情報管理システム) acctdemo デモを使用します。

Docker により、移植性と分離性がアプリケーションに追加されます。 たとえば、ある Windows 仮想マシン (VM) から Docker イメージをエクスポートして別の VM で実行したり、Docker を使用してリポジトリから Windows Server にエクスポートしたりできます。 Docker イメージは同じ構成を使用して新しい場所で実行され、Enterprise Server をインストールする必要はありません。 それはイメージの一部です。 ライセンスに関する考慮事項は引き続き適用されます。

このチュートリアルでは、Azure Marketplace から **Windows 2016 Datacenter with Containers** VM をインストールします。 この VM には **Docker 18.09.0** が含まれます。 以下の手順では、コンテナーをデプロイして実行してから、3270 エミュレーターを使って接続する方法を示します。

## <a name="prerequisites"></a>前提条件

開始する前に、次の前提条件を確認してください。

-   Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

-   Micro Focus ソフトウェアおよび有効なライセンス (または試用版ライセンス)。 Micro Focus の既存の顧客である場合は、Micro Focus の担当者に問い合わせてください。 そうでない場合は、[試用版を要求](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)してください。

    > [!Note] 
    > Docker のデモ ファイルは、Enterprise Server 5.0 に含まれています。 このチュートリアルでは、ent\_server\_dockerfiles\_5.0\_windows.zip を使用します。 Enterprise Server のインストール ファイルにアクセスしたときと同じ場所からアクセスするか、*Micro Focus* に移動して開始します。

-   [Enterprise Server と Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#%22) のドキュメント。

## <a name="create-a-vm"></a>VM の作成

1.  ent\_server\_dockerfiles\_5.0\_windows.zip ファイルからのメディアをセキュリティ保護します。 ES-Docker-Prod-XXXXXXXX.mflic ライセンス ファイルをセキュリティ保護します (Docker イメージのビルドに必要)。

2.  VM を作成します。 これを行うには、Azure portal を開き、左上のメニューから **[リソースの作成]** を選択して、"*windows server オペレーティング システム*" でフィルター処理します。 結果画面で、 **[Windows Server]** を選択します。 次の画面で、 **[Windows Server 2016 Datacenter – with Containers]\(Windows Server 2016 Datacenter – コンテナー付き\)** を選択します。

    ![Azure portal での検索結果のスクリーンショット](./media/run-image-1.png)

3.  VM のプロパティを構成するには、インスタンスの詳細を選択します。

    1.  VM のサイズを選択します。 このチュートリアルでは、2 個の vCPU と 16 GB のメモリを備えた **Standard DS2\_v3** VM の使用を考えます。

    2.  **[リージョン]** 、デプロイ先の **[リソース グループ]** を順に選択します。

    3.  **[可用性オプション]** では、既定の設定を使用します。

    4.  **[ユーザー名]** には、使用する管理者アカウントとパスワードを入力します。

    5.  **ポート 3389 RDP** が開かれていることを確認します。 VM にサインインできるよう、このポートのみをパブリックに公開する必要があります。 次に、すべての既定値をそのまま使用して、 **[確認と作成]** をクリックします。

    ![[仮想マシンの作成] ペインのスクリーンショット](./media/run-image-2.png)

4.  デプロイが完了するのを待ちます (2 分)。 VM が作成されたことを示すメッセージが表示されます。

5.  **[リソースに移動]** を選択して、VM の **[概要]** ブレードに移動します。

6.  右側で、 **[接続]** を選択します。 右側に **[仮想マシンに接続する]** オプションが表示されます。

7.  **[RDP ファイルのダウンロード]** ボタンを選択して、VM にアタッチできるリモート デスクトップ プロトコル (RDP) ファイルをダウンロードします。

8.  ファイルのダウンロードが完了したら、それを開き、VM 用に作成したユーザー名とパスワードを入力します。

    > [!Note]    
    > 会社の資格情報を使用してサインインしないでください。 (RDP クライアントではそれを使用することが想定されています。 使用しないでください。)

9.  **[その他]** を選択し、ご自分の VM の資格情報を選択します。

この時点で、VM は実行されており、RDP を介してアタッチされています。 サインインが済み、次のステップの準備ができています。

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>サンドボックス ディレクトリを作成し、zip ファイルをアップロードする

1.  デモとライセンス ファイルをアップロードできるディレクトリを VM 上に作成します。 たとえば、**C:\\Sandbox** などです。

2.  **ent\_server\_dockerfiles\_5.0\_windows.zip** と **ES-Docker-Prod-XXXXXXXX.mflic** ファイルを、作成したディレクトリにアップロードします。

3.  zip ファイルの内容を、抽出プロセスによって作成される **ent\_server\_dockerfiles\_5.0\_windows** ディレクトリに抽出します。 このディレクトリには、readme ファイル (.html ファイルと .txt ファイル) と、2 つのサブディレクトリ **EnterpriseServer** および **Examples** が含まれます。

4.  **ES-Docker-Prod-XXXXXXXX.mflic** を、C:\\Sandbox\\ent\_server\_dockerfiles\_5.0\_windows\\EnterpriseServer ディレクトリと、C:\\Sandbox\\ent\_server\_dockerfiles\_5.0\_windows\\Examples\\CICS ディレクトリにコピーします。  
      
    > [!Note]
    > 必ずライセンス ファイルを両方のディレクトリにコピーします。 Docker のビルド ステップでイメージが適切にライセンスされるために必要です。

## <a name="check-docker-version-and-create-base-image"></a>Docker のバージョンを確認し、基本イメージを作成する

> [!Important]  
> 適切な Docker イメージの作成は、2 段階のプロセスです。 最初に、Enterprise Server 5.0 の基本イメージを作成します。 次に、x64 プラットフォーム用の別のイメージを作成します。 x86 (32 ビット) イメージも作成できますが、64 ビット イメージを使用します。

1.  コマンド プロンプトを開きます。

2.  Docker がインストールされていることを確認します。 コマンド プロンプトで、「**docker version**」と入力します。  
    たとえば、これを書いた時点では、バージョンは 18.09.0 でした。

3.  ディレクトリを変更するには、次のように入力します:  
    **cd \\Sandbox\\ent\_server\_dockerfiles\_5.0\_windows\\EnterpriseServer**。

4.  「**bld.bat IacceptEULA**」と入力して、初期の基本イメージのビルド プロセスを開始します。 このプロセスが実行されるまで、数分間待ちます。 結果では、2 つのイメージが作成されたことに注意してください (x64 用と x86 用)。

    ![イメージが表示されたコマンド ウィンドウ](./media/run-image-3.png)

5.  CICS デモ用の最終的なイメージを作成するには、「**cd\\Sandbox\\ent\_server\_dockerfiles\_5.0\_windows\\Examples\\CICS**」と入力して CICS ディレクトリに切り替えます。

6.  イメージを作成するには、「**bld.bat x64**」と入力します。 プロセスが実行され、イメージが作成されたことを示すメッセージが表示されるまで、数分間待ちます。

7.  「**docker images**」と入力し、VM にインストールされているすべての Docker イメージの一覧を表示します。 その中に **microfocus/es-acctdemo** があることを確認します。

    ![es-acctdemo イメージが表示されているコマンド ウィンドウ](./media/run-image-4.png)

## <a name="run-the-image"></a>イメージを実行する

1.  Enterprise Server 5.0 と acctdemo アプリケーションを起動するには、コマンド プロンプトで次のように入力します。

    ```
    **docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win\_5.0\_x64
    ```

1.  [x3270](http://x3270.bgp.nu/) などの 3270 端末エミュレーターをインストールし、それを使用してポート 9040 経由で実行しているイメージにアタッチします。

2.  Docker によって管理されるコンテナーに対して Dynamic Host Configuration Protocol (DHCP) サーバーとして機能できるように、acctdemo コンテナーの IP アドレスを取得します。

    1.  実行中のコンテナーの ID を取得します。 コマンド プロンプトで「**Docker ps**」と入力し、ID を書き留めます (この例では **22a0fe3159d0**)。 次のステップのためにそれを保存します。

    2.  acctdemo コンテナーの IP アドレスを取得するには、前のステップのコンテナー ID を使用して次のようにします。

    ```
    docker inspect \<containerID\> --format="{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

    次に例を示します。

    ```
    docker inspect 22a0fe3159d0 --format="{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

4. acctdemo イメージの IP アドレスを書き留めます。 たとえば、次の出力ではアドレスは 172.19.202.52 です。

    ![IP アドレスが表示されているコマンド ウィンドウのスクリーンショット](./media/run-image-5.png)

5. エミュレーターを使用してイメージをマウントします。 acctdemo イメージのアドレスとポート 9040 を使用するように、エミュレーターを構成します。 ここでは、**172.19.202.52:9040** です。 実際もこれに似ています。 **[Signon to CICS]\(CICS にサインオン\)** 画面が開きます。

    ![[Signon to CICS]\(CICS にサインオン\) のスクリーンショット](./media/run-image-6.png)

6. **[USERID]\(ユーザー ID\)** に「**SYSAD**」と入力し、 **[Password]\(パスワード\)** に「**SYSAD**」と入力して、CICS 領域にサインインします。

7. エミュレーターのキーマップを使用して、画面をクリアします。 x3270 の場合は、 **[Keymap]\(キーマップ\)** メニュー オプションを選択します。

8. acctdemo アプリケーションを起動するには、「**ACCT**」と入力します。 アプリケーションの最初の画面が表示されます。

     ![スクリーンショットには、アプリケーションが表示されているコンソール ウィンドウが示されています。](./media/run-image-7.png)

9. 表示アカウントの種類を調べます。 たとえば、要求の場合は「**D**」と入力し、**アカウント** の場合は「**11111**」と入力します。 22222、33333 など、他のアカウント番号を試してください。

    ![スクリーンショットでは、アプリケーションのさまざまな値を編集しています。](./media/run-image-8.png)

10. Enterprise Server 管理コンソールを表示するには、コマンド プロンプトに移動して、「**start http:172.19.202.52:86**」と入力します。

    ![Enterprise Server 管理コンソール](media/run-image-9.png)

これで完了です。 今は、Docker コンテナーで CICS アプリケーションを実行し、管理しています。

## <a name="next-steps"></a>次のステップ

-   [Azure で Micro Focus Enterprise Server 5.0 および Enterprise Developer 5.0 をインストールする](./set-up-micro-focus-azure.md)

-   [メインフレーム アプリケーションの移行](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
