---
title: IBM zD&T v1 の Application Developers Controlled Distribution (ADCD) を設定する | Microsoft Docs
description: Azure Virtual Machines (VM) 上で、IBM Z Development and Test Environment (zD&T) 環境を実行します。
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: d527b08f3610531bef8e98a11998942411651d27
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621346"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>IBM zD&T v1 の Application Developers Controlled Distribution (ADCD) を設定する

Azure Virtual Machines (VM) 上で、IBM Z Development and Test Environment (zD&T) 環境を実行できます。 この環境では、IBM Z Series アーキテクチャがエミュレートされます。 それは、さまざまな Z Series オペレーティング システムまたはインストール (Z インスタンスまたはパッケージとも言います) をホストでき、IBM Application Developers Controlled Distributions (ADCD) という名前のカスタマイズされたバンドルを通して利用できます。

この記事では、zD&T 環境の ADCD インスタンスを Azure 上に設定する方法を示します。 ADCD では、zD&T で実行される開発およびテスト環境向けの完全な Z Series オペレーティング システムの実装が作成されます。

zD&T 同様、ADCD は IBM のお客様とパートナー様だけが利用でき、開発およびテストを行うことのみを目的としています。 それらは運用環境で使用されるものではありません。 [パスポート アドバンテージ](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html)または [IBM PartnerWorld](https://www.ibm.com/partnerworld/public) を通して、多数の IBM インストール パッケージをダウンロードできます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

- Azure 上にセットアップ済みの [zD&T 環境][ibm-install-z]。 この記事では、作成済みの Ubuntu 16.04 VM イメージと同じイメージを使用することを前提とします。

- IBM PartnerWorld またはパスポート アドバンテージ経由の ADCD メディアへのアクセス。

- [ライセンス サーバー](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html)。 これは IBM zD&T を実行するために必要です。 その作成方法は、IBM からのソフトウェア ライセンスの取得方法によって異なります。

  - **ハードウェア ベースのライセンス サーバー**では、ソフトウェアのすべての部分にアクセスするために必要な Rational Token を含む USB ハードウェア デバイスが必要です。 これは、IBM から取得する必要があります。

  - **ソフトウェア ベースのライセンス サーバー**では、ライセンス キーを管理するための集中管理サーバーを設定する必要があります。 この方法が推奨されます。IBM から受け取ったキーを管理サーバーに設定する必要があります。

## <a name="download-the-installation-packages-from-passport-advantage"></a>パスポート アドバンテージからインストール パッケージをダウンロードする

ADCD メディアへのアクセスが必要です。 以下の手順は、IBM のお客様であり、パスポート アドバンテージを使用できることを前提としています。 IBM のパートナー様は [IBM PartnerWorld](https://www.ibm.com/partnerworld/public) を利用できます。

> [!NOTE]
> この記事では、Windows PC を使用して Azure portal にアクセスし、IBM メディアをダウンロードすることを前提としています。 Mac または Ubuntu デスクトップを使用している場合は、IBM メディアを取得するためのコマンドとプロセスは若干異なる可能性があります。

1. [パスポート アドバンテージ](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer)にログオンします。

2. **[Software Downloads]\(ソフトウェアのダウンロード\)** と **[Media Access]\(メディア アクセス\)** を選択します。

3. **[Program offering and agreement number]\(プログラム オファリングと契約番号\)** を選択し、 **[Continue]\(続行\)** をクリックします。

4. パーツの説明またはパーツ番号を入力し、 **[Finder]\(ファインダー\)** をクリックします。

5. 必要に応じて、アルファベット順の一覧をクリックして、製品の名前を表示します。

6. **[Operating system field]\(オペレーティング システム フィールド\)** で **[All Operating Systems]\(すべてのオペレーティング システム\)** を、 **[Languages field]\(言語フィールド\)** で **[All Languages]\(すべての言語\)** を選択します。 次に、 **[Go]\(実行\)** をクリックします。

7. **[Select individual files]\(個々のファイルを選択する\)** をクリックして一覧を展開し、ダウンロードする個々のメディアを表示します。

8. ダウンロードするパッケージを確認し、 **[Download]\(ダウンロード\)** を選択し、任意のディレクトリにファイルをダウンロードします。

## <a name="upload-the-adcd-packages"></a>ADCD パッケージをアップロードする

パッケージが用意できたので、Azure 上の VM にアップロードする必要があります。

1. Azure portal で、作成済みの Ubuntu VM を使用して **ssh** セッションを開始します。 VM に移動し、 **[概要]** ブレードを選択し、 **[接続]** を選択します。

2. **[SSH]** タブを選択し、ssh コマンドをクリップボードにコピーします。

3. 資格情報と任意の [SSH クライアント](/azure/virtual-machines/linux/use-remote-desktop)を使用して VM にログオンします。 このデモでは、Windows コマンド プロンプトに bash シェルを追加する Windows 10 用の Linux 拡張機能を使用します。 PuTTY はうまく機能します。

4. ログオンしたら、IBM パッケージをアップロードするディレクトリを作成します。 Linux では大文字と小文字が区別されることに注意してください。 たとえば、このデモでは、パッケージは次の場所にアップロードされることを前提としています。

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. [WinSCP](https://winscp.net/eng/index.php) などの SSH クライアントを使用して、ファイルをアップロードします。 SCP は SSH の一部であるため、SSH で使用されるポート 22 が使用されます。 ローカル コンピューターが Windows でない場合は、SSH セッションで [scp コマンド](http://man7.org/linux/man-pages/man1/scp.1.html)を入力できます。

6. 作成済みの Azure VM ディレクトリへのアップロードを開始します。このディレクトリが zD&T のイメージ ストレージになります。

    > [!NOTE]
    > **home/MyUserID/ZDT/adcd/nov2017** ディレクトリへのアップロードの中に **ADCDTOOLS.XML** が含まれていることを確認します。 この情報は後で必要になります。

7. ファイルがアップロードされるまで待機します。Azure への接続状態に応じて、時間がかかる場合があります。

8. アップロードが完了したら、ボリューム ディレクトリに移動し、すべての **gz** ボリュームを圧縮解除します。

    ```
        gunzip \*.gz
    ```
    
![圧縮解除された gz ボリュームを示すファイル エクスプローラー](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>イメージ ストレージを構成する

次の手順では、アップロードされたパッケージを使用するように zD&T を構成します。 zD&T 内のイメージ ストレージ プロセスによって、イメージをマウントして使用できます。 SSH または FTP を使用できます。

1. **zDTServer** を起動します。 これを行うには、ルート レベルにいる必要があります。 次の 2 つのコマンドを順に入力します。
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. コマンドの URL 出力をメモし、その URL を使用して Web サーバーにアクセスします。 それは次のようになります。
     > https://(VM 名または IP アドレス):9443/ZDTMC/index.html
     >
     > Web アクセスではポート 9443 が使用されることを思い出してください。 これを使用して、Web サーバーにログオンします。 zD&T のユーザー ID は **zdtadmin** であり、パスワードは **password** です。

    ![IBM zD&T Enterprise Edition の [Welcome]\(ようこそ\) 画面](media/02-welcome.png)

3. **[Quick Start]\(クイック スタート\)** ページで、 **[Configure]\(構成\)** の下の **[Image Storage]\(イメージ ストレージ\)** を選択します。

     ![IBM zD&T Enterprise Edition の [Quick Start]\(クイック スタート\) 画面](media/03-quickstart.png)

4. **[Configure image storage]\(イメージ ストレージの構成\)** ページで、 **[SSH File Transfer Protocol]\(SSH ファイル転送プロトコル\)** を選択します。

5. **[Host name]\(ホスト名\)** として「**Localhost**」と入力し、イメージをアップロードしたディレクトリのパスを入力します。 例: /home/MyUserID/ZDT/adcd/nov2017/volumes。

6. VM の**ユーザー ID** と**パスワード**を入力します。 zD&T のユーザー ID とパスワードは使用しないでください。

7. 接続をテストしてアクセスできることを確認し、 **[Save]\(保存\)** を選択して構成を保存します。

## <a name="configure-the-target-environments"></a>ターゲット環境を構成する

次の手順では、zD&T ターゲット環境を構成します。 このエミュレートされたホスト環境で、イメージが実行されます。

1. **[Quick Start]\(クイック スタート\)** ページで、 **[Configure]\(構成\)** の下の **[Target environments]\(ターゲット環境\)** を選択します。

2. **[Configure target environments]\(ターゲット環境の構成\)** ページで、 **[Add Target]\(ターゲットの追加\)** を選択します。

3. **[Linux]** を選択します。 IBM では 2 種類の環境 (Linux と Cloud(OpenStack)) をサポートしていますが、このデモは Linux 上で実行されます。

4. **[Add target environment]\(ターゲット環境の追加\)** ページで、 **[Host name]\(ホスト名\)** に「**localhost**」と入力します。 **[SSH port]\(SSH ポート\)** の設定は **[22]** のままにします。

5. **[Target Environment label]\(ターゲット環境のラベル\)** ボックスに、ラベル (**MyCICS** など) を入力します。

     ![[Add target environment]\(ターゲット環境の追加\) 画面](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>ADCD を構成してデプロイする

前の構成手順を完了したら、パッケージとターゲット環境を使用するように zD&T を構成する必要があります。 ここでも、zD&T のイメージ ストレージ プロセスを使用して、イメージをマウントして使用できます。 SSH または FTP を使用できます。

1. **[Quick Start]\(クイック スタート\)** ページで、 **[Configure]\(構成\)** の下の **[ADCD]** を選択します。 ADCD パッケージをマウントする前に完了する必要がある手順が表示されます。 先ほどターゲット ディレクトリに名前を付けたのはこれが理由です。

2. すべてのイメージが適切なディレクトリにアップロードされたことを前提として、右下に表示されている **[IMAGE from ADCD]\(ADCD からのイメージ\)** リンクをクリックします (次のスクリーンショットの手順 7 に示されています)。

     ![IBM zD&T Enterprise Edition - ADCD 構成画面](media/05-adcd.png)

## <a name="create-the-image"></a>イメージの作成

前の構成手順が完了すると、 **[Create an image using ADCD Components]\(ADCD コンポーネントを使用してイメージを作成する\)** ページが表示されます。

1. ボリューム (ここでは Nov 2017) を選択して、そのボリューム内にある別のパッケージを表示します。

2. このデモでは、 **[Customer Information Control System (CICS) - 5.3]** を選択します。

3. **[Image name]\(イメージ名\)** ボックスに、イメージの名前 (**MyCICS Image** など) を入力します。

4. 右下にある **[Create Image]\(イメージの作成\)** ボタンを選択します。

     ![IBM zD&T Enterprise Edition - [Create an image using ADCD Components]\(ADCD コンポーネントを使用してイメージを作成する\) 画面](media/06-adcd.png)

5. イメージが正常にデプロイされたことを通知するウィンドウが表示されたら、 **[Deploy images]\(イメージのデプロイ\)** を選択します。

6. **[Deploy an image to a target environment]\(イメージをターゲット環境にデプロイする\)** ページで、前のページで作成したイメージ (**MyCICS Image**) と、作成済みのターゲット環境 (**MyCICS**) を選択します。

7. 次の画面で、VM の資格情報 (ztadmin 資格情報ではありません) を指定します。

8. [Properties]\(プロパティ\) ウィンドウで、実行するイメージ用の **[Central processors (CPs)]\(中央処理装置 (CP)\)** の数、 **[System memory (GB)]\(システム メモリ (GB)\)** の量、および **[Deployment directory]\(デプロイ ディレクトリ\)** を入力します。 これはデモであるため、小さくしておきます。

9. **[Automatically issue IPL command to z/OS after deploy]\(デプロイ後に IPL コマンドを z/OS に自動的に発行する\)** チェック ボックスがオンになっていることを確認します。

     ![[Properties]\(プロパティ\) 画面](media/07-properties.png)

10. **[Complete]\(完了\)** を選択します。

11. **[Deploy an image to a target environment]\(イメージをターゲット環境にデプロイする\)** ページで **[Deploy Image]\(イメージのデプロイ\)** を選択します。

イメージがデプロイされ、3270 ターミナル エミュレーターによってマウントする準備ができました。

> [!NOTE]
> 十分なディスク領域がないというエラーが発生した場合は、151 GB の容量が必要であることに注意してください。

お疲れさまでした。 現在、IBM メインフレーム環境が Azure 上で実行されています。

## <a name="learn-more"></a>詳細情報

- [メインフレームの移行: 通説と事実](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Azure 上の IBM DB2 pureScale](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [トラブルシューティング](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [メインフレームから Azure への移行の解明](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
