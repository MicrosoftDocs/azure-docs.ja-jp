---
title: Azure で IBM zD&T 開発/テスト環境をインストールする | Microsoft Docs
description: Azure 仮想マシン (VM) IaaS (サービスとしてのインフラストラクチャ) に IBM Z Development and Test Environment (zD&T) をデプロイします。
services: virtual-machines
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 04/02/2019
tags: ''
keywords: ''
ms.openlocfilehash: 32b63256f89d6d051305890c3387140f243e1a70
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954328"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Azure で IBM zD&T 開発/テスト環境をインストールする

IBM Z Systems 上でメインフレーム ワークロードの開発/テスト環境を作成するには、Azure 仮想マシン (VM) IaaS (サービスとしてのインフラストラクチャ) に IBM Z Development and Test Environment (zD&T) をデプロイできます。

zD&T を使用すると、重要性の低い開発およびテスト環境に x86 プラットフォームのコスト削減を利用した後、それらの更新を元の Z System 運用環境にプッシュすることができます。 詳細については、[IBM ZD&T のインストール手順](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL)に関するページを参照してください。

Azure および Azure Stack では、次のバージョンがサポートされます。

- zD&T Personal Edition
- zD&T Parallel Sysplex
- zD&T Enterprise Edition

zD&T のすべてのエディションが Windows Server ではなく、x86 Linux システムでのみ実行されます。 Enterprise Edition は、Red Hat Enterprise Linux (RHEL) または Ubuntu/Debian のどちらかでサポートされます。 Azure では、RHEL イメージと Debian VM イメージの両方を使用できます。

この記事では、環境の作成および管理に zD&T Enterprise Edition Web サーバーを使用できるように、Azure で zD&T Enterprise Edition を設定する方法について説明します。 zD&T をインストールしても、環境はインストールされません。 これらは、インストール パッケージとして個別に作成する必要があります。 たとえば、Application Developers Controlled Distribution (ADCD) はテスト環境のボリューム イメージです。 これは、IBM から入手できるメディア ディストリビューション上の zip イメージに含まれています。 [Azure で ADCD 環境を設定する](demo.md)方法を参照してください。

詳細については、IBM Knowledge Center の [zD&T の概要](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html)に関するページを参照してください。

この記事では、Azure で Z Development and Test Environment (zD&T) Enterprise Edition を設定する方法について説明します。 その後、zD&T Enterprise Edition Web サーバーを使用して、Azure で Z ベースの環境を作成および管理できます。

## <a name="prerequisites"></a>前提条件

> [!NOTE]
> IBM では、zD&T Enterprise Edition を運用環境 *ではなく*、開発/テスト環境にのみインストールすることを許可しています。

- Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

- IBM の顧客およびパートナーのみが使用できるメディアへのアクセスが必要です。 詳細については、IBM の担当者に問い合わせるか、または [zD&T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment) の Web サイトにある連絡先情報を参照してください。

- [ライセンス サーバー](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html)。 これは、環境へのアクセスのために必要です。 これを作成する方法は、IBM からソフトウェアのライセンスをどのように取得するかによって異なります。

     - **ハードウェア ベースのライセンス サーバー** では、ソフトウェアのすべての部分にアクセスするために必要な Rational Token を含む USB ハードウェア デバイスが必要です。 これは、IBM から取得する必要があります。

     - **ソフトウェア ベースのライセンス サーバー** では、ライセンス キーを管理するための集中管理サーバーを設定する必要があります。 この方法が推奨されます。IBM から受け取ったキーを管理サーバーに設定する必要があります。

## <a name="create-the-base-image-and-connect"></a>基本イメージおよび接続を作成する

1. Azure Portal で、必要なオペレーティング システム構成を備えた [VM を作成](../../../linux/quick-create-portal.md)します。 この記事では、Ubuntu 16.04 を実行している B4ms VM (4 つの vCPU と 16 GB のメモリを装備) を前提にしています。

2. VM が作成されたら、SSH の場合は 22、FTP の場合は 21、Web サーバーの場合は 9443 の受信ポートを開きます。

3. **[接続]** ボタンを使用して、VM の **[概要]** ブレードに表示されている SSH 資格情報を取得します。 **[SSH]** タブを選択し、SSH ログオン コマンドをクリップボードにコピーします。

4. ローカル PC から [Bash シェル](../../../../cloud-shell/quickstart.md)にログオンし、コマンドを貼り付けます。 これは、**ssh\<user id\>\@\<IP Address\>** という形式になります。 資格情報の入力を求められたら、それを入力してホーム ディレクトリへの接続を確立します。

## <a name="copy-the-installation-file-to-the-server"></a>インストール ファイルをサーバーにコピーする

Web サーバーのインストール ファイルは **ZDT\_Install\_EE\_V12.0.0.1.tgz** です。 これは、IBM によって提供されたメディアに含まれています。 このファイルを Ubuntu VM にアップロードする必要があります。

1. コマンド ラインから、次のコマンドを入力して、新しく作成されたイメージですべてが最新であることを確認します。

    ```
    sudo apt-get update
    ```

2. インストール先のディレクトリを作成します。

    ```
    mkdir ZDT
    ```

3. ローカル コンピューターから VM にファイルをコピーします。

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> このコマンドは、インストール ファイルをホーム ディレクトリ内の ZDT ディレクトリにコピーします。これは、クライアントで Windows と Linux のどちらが実行されているかによって異なります。

## <a name="install-the-enterprise-edition"></a>Enterprise Edition をインストールする

1. ZDT ディレクトリに移動し、次のコマンドを使用して ZDT\_Install\_EE\_V12.0.0.1.tgz ファイルを展開します。

    ```
    cd ZDT
    tar zxvf ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. インストーラーを実行します。

    ```
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.x86_64
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. **1** を選択して Enterprise Server をインストールします。

4. **Enter** キーを押し、使用許諾契約書を注意深く読みます。 ライセンスの最後に「**Yes**」と入力して処理を続行します。

5. 新しく作成されたユーザー (**ibmsys1**) のパスワードを変更するよう求められたら、コマンド **sudo passwd ibmsys1** を使用し、新しいパスワードを入力します。

6. インストールが成功したかどうかを確認するには、次を入力します。

    ```
    dpkg -l | grep zdtapp
    ```

7. 出力に、パッケージが正常にインストールされたことを示す **zdtapp 12.0.0.0** という文字列が含まれていることを確認します。

### <a name="starting-enterprise-edition"></a>Enterprise Edition の起動

Web サーバーは、起動されると、インストール プロセス中に作成された zD&T ユーザー ID のもとで実行されることに注意してください。

1. Web サーバーを起動するには、root ユーザー ID を使用して次のコマンドを実行します。

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. スクリプトを使用して、次のような URL 出力をコピーします。

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. この URL を Web ブラウザーに貼り付けて、zD&T インストールのための管理コンポーネントを開きます。

## <a name="next-steps"></a>次のステップ

[IBM zD&T v1 の Application Developers Controlled Distribution (ADCD) を設定する](./demo.md)
