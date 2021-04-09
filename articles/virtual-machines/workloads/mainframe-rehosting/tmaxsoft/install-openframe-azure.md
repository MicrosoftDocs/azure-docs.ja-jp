---
title: Azure Virtual Machines 上に TmaxSoft OpenFrame をインストールする
description: 開発、デモ、テスト、運用のワークロードに適した OpenFrame 環境を Azure 上にセットアップする方法を学習します。
services: virtual-machines
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 2ca5f0311102c27c1233098a18ff16701b99ea2a
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950792"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Azure 上に TmaxSoft OpenFrame をインストールする

開発、デモ、テスト、運用のワークロードに適した OpenFrame 環境を Azure 上にセットアップする方法を学習します。 このチュートリアルでは、各ステップについて説明します。

OpenFrame には、Azure 上にメインフレーム エミュレーション環境を作成する複数のコンポーネントが含まれています。 たとえば、OpenFrame のオンライン サービスでは、IBM Customer Information Control System (CICS) などのメインフレーム ミドルウェアが置き換えられ、OpenFrame Batch の TJES コンポーネントでは、IBM メインフレームの Job Entry Subsystem (JES) が置き換えられます。

OpenFrame は、Oracle Database、Microsoft SQL Server、IBM Db2、MySQL など、任意のリレーショナル データベースで動作します。 OpenFrame のこのインストールでは、TmaxSoft の Tibero リレーショナル データベースを使用します。 OpenFrame と Tibero はどちらも、Linux オペレーティング システムで動作します。 このチュートリアルでは CentOS 7.3 をインストールしますが、他のサポートされる Linux ディストリビューションを使用できます。OpenFrame アプリケーション サーバーと Tibero データベースは、1 つの仮想マシン (VM) にインストールされます。

このチュートリアルでは、OpenFrame スイート コンポーネントをインストールする手順を説明します。 いくつかは、個別にインストールする必要があります。

OpenFrame のメイン コンポーネント:

- 必須のインストール パッケージ。
- Tibero データベース。
- Open Database Connectivity (ODBC) は、OpenFrame 内のアプリケーションによって、Tibero データベースとの通信に使用されます。
- OpenFrame Base。システム全体を管理するミドルウェアです。
- OpenFrame Batch。メインフレームのバッチ システムを置換するソリューションです。
- TACF。システムおよびリソースへのユーザー アクセスを制御するサービス モジュールです。
- ProSort。バッチ トランザクション用の並べ替えツールです。
- OFCOBOL。メインフレームの COBOL プログラムを解釈するコンパイラです。
- OFASM。メインフレームのアセンブラー プログラムを解釈するコンパイラです。
- OpenFrame Server Type C (OSC )。メインフレームのミドルウェアと IBM CICS を置き換えるソリューションです。
- Java Enterprise User Solution (JEUS)。Java Enterprise Edition 6 で認定されている Web アプリケーション サーバーです。
- OFGW。3270 リスナーを提供する OpenFrame ゲートウェイ コンポーネントです。
- OFManager。Web 環境で OpenFrame の運用と管理の機能を提供するソリューションです。

他の必要な OpenFrame コンポーネント:

- OSI。メインフレームのミドルウェアと IMS DC を置き換えるソリューションです。
- TJES。メインフレームの JES 環境を提供するソリューションです。
- OFTSAM。オープン システムで (V)SAM ファイルを使用できるようにするソリューションです。
- OFHiDB。メインフレームの IMS DB を置き換えるソリューションです。
- OFPLI。メインフレームの PL/I プログラムを解釈するコンパイラです。
- PROTRIEVE。メインフレームの言語 CA-Easytrieve を実行するソリューションです。
- OFMiner。メインフレームのアセットを分析し、それらを Azure に移行するソリューションです。

## <a name="architecture"></a>Architecture

次の図では、このチュートリアルでインストールされる OpenFrame 7.0 アーキテクチャのコンポーネントの概要を示します。

![OpenFrame のコンポーネント](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Azure のシステム要件

次の表では、Azure にインストールするための要件を示します。
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>要件</th><th>説明</th></tr>
</thead>
<tbody>
<tr><td>Azure でサポートされる Linux ディストリビューション
</td>
<td>
Linux x86 2.6 (32 ビット、64 ビット)<br/>
Red Hat 7.x<br/>
CentOS 7.x<br/>
</td>
</tr>
<tr><td>ハードウェア
</td>
<td>コア:2 (最低)<br/>
メモリ:4 GB (最低)<br/>
スワップ領域:1 GB (最低)<br/>
ハード ディスク:100 GB (最低)<br/>
</td>
</tr>
<tr><td>Windows ユーザー用のオプション ソフトウェア
</td>
<td>PuTTY:このガイドでは、VM の機能を構成するために使用します<br/>
WinSCP:使用できる一般的な SFTP クライアントと FTP クライアントです<br/>
Eclipse for Windows:TmaxSoft でサポートされている開発プラットフォームです<br/>
(現時点では、Microsoft Visual Studio はサポートされていません)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>前提条件

数日かけて、必要なソフトウェアをすべてアセンブルし、すべての手動プロセスを完了するように計画します。

始める前に、次のことを行います。

- TmaxSoft から OpenFrame のインストール メディアを入手します。 TmaxSoft の既存の顧客は、TmaxSoft の担当者に連絡してライセンスされたコピーを用意します。 それ以外の場合は、[TmaxSoft](https://www.tmaxsoft.com/contact/) から試用版を要求します。

- <support@tmaxsoft.com> にメールを送り、OpenFrame のドキュメントを要求します。

- まだない場合は、Azure サブスクリプションを入手します。 始める前に、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成することもできます。

- 省略可能。 組織内の許可されたユーザーに Azure VM へのアクセスを制限する、サイト間 VPN トンネルまたは Jumpbox を設定します。 このステップは必須ではありませんが、ベスト プラクティスです。

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>OpenFrame と Tibero 用の VM を Azure 上に設定します

さまざまなデプロイ パターンを使用して OpenFrame 環境を設定できますが、以下の手順では、1 つの VM 上に OpenFrame アプリケーション サーバーと Tibero データベースをデプロイする方法を示します。 大規模な環境と大きなワークロードの場合のベスト プラクティスは、パフォーマンス向上のため、データベースを専用の VM に別にデプロイすることです。

**VM を作成するには**

1. Azure portal (<https://portal.azure.com>) に移動し、ご自分のアカウントでサインインします。

2. **[仮想マシン]** をクリックします。

    ![Azure portal でのリソース一覧](media/vm-01.png)

3. **[追加]** をクリックします。

    ![Azure portal の [追加] オプション](media/vm-02.png)

4. **[オペレーティング システム]** の右側で、 **[その他]** をクリックします。

     ![Azure portal の [その他] オプション](media/vm-03.png)

5. **[CentOS ベース 7.3]** をクリックしてこのチュートリアルに正確に従うか、または別のサポートされている Linux ディストリビューションを選択してもかまいません。

     ![Azure portal でのオペレーティング システムのオプション](media/vm-04.png)

6. **[基本]** 設定で、 **[名前]** 、 **[ユーザー名]** 、 **[認証の種類]** 、 **[サブスクリプション]** (従量課金制は AWS スタイルの支払いです)、 **[リソース グループ]** (既存のものを使うか、TmaxSoft グループを作成します) を入力します。

7. 完了したら ( **[認証の種類]** の公開/秘密キー ペアを含めて)、 **[送信]** をクリックします。

> [!NOTE]
> **[認証の種類]** に SSH 公開キーを使用する場合は、次のセクションの手順を参照して公開/秘密キー ペアを生成してから、この手順を再開します。

### <a name="generate-a-publicprivate-key-pair"></a>公開/秘密キー ペアを生成する

Windows オペレーティング システムを使用している場合は、公開/秘密キー ペアを生成するために PuTTYgen が必要です。

公開キーは自由に共有できますが、秘密キーは完全に秘密に保持し、別のパーティとは共有しないようにする必要があります。 キーを生成した後、**SSH 公開キー** を構成に貼り付ける必要があります。実際には、Linux VM にアップロードします。 ユーザー アカウントのホーム ディレクトリの \~/.ssh ディレクトリ内の authorized\_keys の内部に格納されます。 その後、Linux VM では、SSH クライアント (この例では PuTTY) の関連付けられた **SSH 秘密キー** が提供されると、接続を認識して検証できるようになります。

新しいユーザーに VM へのアクセスを許可する場合: 

- 新しい各ユーザーは、PuTTYgen を使用して、独自の公開/秘密キーを生成します。
- ユーザーは、自分の秘密キーを分けて保存し、公開キーの情報を VM の管理者に送信します。
- 管理者は、公開キーの内容を \~/.ssh/authorized\_keys ファイルに貼り付けます。
- 新しいユーザーは、PuTTY を使用して接続します。

**公開/秘密キー ペアを生成するには**

1.  <https://www.putty.org/> から PuTTYgen をダウンロードし、すべて既定の設定を使用してインストールします。

2.  PuTTYgen を開くには、C:\\Program Files\\PuTTY で PuTTY のインストール ディレクトリを探します。

    ![PuTTY のインターフェイス](media/puttygen-01.png)

3.  **[Generate]\(生成\)** をクリックします。

    ![[PuTTY Key Generator] ダイアログ ボックスを表示し、[Generate]\(生成\) ボタンを強調表示するスクリーンショット。](media/puttygen-02.png)

4.  生成した後、公開キーと秘密キーの両方を保存します。 公開キーの内容を、 **[仮想マシンの作成] \> [基本]** ウィンドウの **[SSH 公開キー]** セクションに貼り付けます (前のセクションのステップ 6 と 7 で示されています)。

    ![[PuTTY Key Generator] ダイアログ ボックス](media/puttygen-03.png)

### <a name="configure-vm-features"></a>VM の機能を構成する

1. Azure portal の **[サイズの選択]** ブレードで、必要な Linux マシンのハードウェア設定を選択します。 Tibero と OpenFrame の両方をインストールするための "*最小*" 要件は、このインストール例で示すように、2 つの CPU と 4 GB の RAM です。

    ![仮想マシンを作成する - 基本](media/create-vm-01.png)

2. **[3 設定]** をクリックし、既定の設定を使用してオプション機能を構成します。
3. 支払いの詳細を確認します。

    ![仮想マシンを作成する - 購入](media/create-vm-02.png)

4. 選択内容を送信します。 Azure で VM のデプロイが開始されます。 通常、このプロセスには数分かかります。

5. VM がデプロイされると、ダッシュボードが表示されて、構成中に選したすべての設定が示されます。 **パブリック IP アドレス** を書き留めておきます。

    ![Azure ダッシュボード上の tmax](media/create-vm-03.png)

6. PuTTY を開きます。

7. **[ホスト名]** に、ご自分のユーザー名と、コピーしたパブリック IP アドレスを入力します。 たとえば、**username\@publicip** などです。

    ![[PuTTY Configuration]\(PuTTY 構成\) ダイアログ ボックスを表示し、[ホスト名 (または IP アドレス)] フィールドを強調表示するスクリーンショット。](media/putty-01.png)

8. **[Category]\(カテゴリ\)** ボックスで、 **[Connection]\(接続\) \> [SSH] \> [Auth]** をクリックします。**秘密キー** ファイルのパスを指定します。

    ![[PuTTY Configuration]\(PuTTY 構成\) ダイアログ ボックス](media/putty-02.png)

9. **[Open]\(開く\)** をクリックして、PuTTY ウィンドウを起動します。 成功すると、Azure で実行されている新しい CentOS VM に接続されます。

10. ルート ユーザーとしてログオンするには、「**sudo bash**」と入力します。

    ![コマンド ウィンドウでのルート ユーザーのログオン](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>環境とパッケージを設定する

VM が作成されてログオンしたので、いくつかの設定手順を実行して、必要なプレインストール パッケージをインストールする必要があります。

1. vi を使用してホスト ファイル (`vi /etc/hosts`) を編集し、名前 **ofdemo** をローカル IP アドレスにマップします。 ここでは IP が 192.168.96.148 ofdemo であるとします。次に示すのは変更前です。

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     次に示すのは変更後です。

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. グループとユーザーを作成します。

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. ユーザー oframe7 のパスワードを変更します。

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. /etc/sysctl.conf でカーネル パラメーターを更新します。

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. 再起動せず、動的にカーネル パラメーターを更新します。

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. 必要なパッケージを取得します。サーバーがインターネットに接続されていることを確認し、次のパッケージをダウンロードして、インストールします。

     - dos2unix
     - glibc
     - glibc.i686 glibc.x86\_64
     - libaio
     - ncurses

          > [!NOTE]
          > ncurses パッケージをインストールした後、次のシンボリック リンクを作成します。
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - gcc
     - gcc-c++
     - libaio-devel.x86\_64
     - strace
     - ltrace
     - gdb

7. Java RPM のインストールの場合は、次のようにします。

```
root@ofdemo ~]# rpm -ivh jdk-7u79-linux-x64.rpm
[root@ofdemo ~]# vi .bash_profile

# JAVA ENV
export JAVA_HOME=/usr/java/jdk1.7.0_79/
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar

[root@ofdemo ~]# source /etc/profile
[root@ofdemo ~]# java –version

java version "1.7.0_79"
Java(TM) SE Runtime Environment (build 1.7.0_79-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)

[root@ofdemo ~]# echo $JAVA_HOME /usr/java/jdk1.7.0_79/
```

## <a name="install-the-tibero-database"></a>Tibero データベースをインストールする

Tibero では、Azure 上の OpenFrame 環境でいくつかの主要な機能が提供されています。

- Tibero は、さまざまなシステム機能のための OpenFrame の内部データ ストアとして使用されます。
- KSDS、RRDS、ESDS などの VSAM ファイルでは、データ ストレージ用に Tibero データベースを内部的に使用します。
- TACF データ リポジトリは Tibero に格納されます。
- OpenFrame カタログ情報は Tibero に格納されます。
- IBM Db2 の代わりとして Tibero データベースを使用し、アプリケーション データを格納できます。

**Tibero をインストールするには**

1. Tibero バイナリ インストーラー ファイルが存在することを確認し、バージョン番号を確認します。
2. Tibero ソフトウェアを、Tibero のユーザー アカウント (oframe) にコピーします。 次に例を示します。

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. .bash\_profile を vi で開き (`vi .bash_profile`)、以下を貼り付けます。

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. bash プロファイルを実行するには、コマンド プロンプトで次のように入力します。

    ```
    source .bash_profile
    ```

5. ヒント ファイル (Tibero の構成ファイル) を生成し、vi で開きます。 次に例を示します。

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. \$TB\_HOME/client/config/tbdsn.tbr を変更し、次に示すように oflocalhost を 127.0.0.1 に置き換えます。

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. データベースを作成します。 次のような出力が表示されます。

    ```
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NOMOUNT mode).
     /--------------------- newmount sql ------------------------/
    create database character set MSWIN949 national character set UTF16;
    /-----------------------------------------------------------/
    Database created.
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    /opt/tmaxdb/tibero6/bin/tbsvr
    ………………………..
    Creating agent table...
    Done.
    For details, check /opt/tmaxdb/tibero6/instance/TVSAM/log/system_init.log.
    ************************************************** 
    * Tibero Database TVSAM is created successfully on Fri Aug 12 19:10:43 UTC 2016.
    *     Tibero home directory ($TB_HOME) =
    *         /opt/tmaxdb/tibero6
    *     Tibero service ID ($TB_SID) = TVSAM
    *     Tibero binary path =
    *         /opt/tmaxdb/tibero6/bin:/opt/tmaxdb/tibero6/client/bin
    *     Initialization parameter file =
    *         /opt/tmaxdb/tibero6/config/TVSAM.tip
    * 
    * Make sure that you always set up environment variables $TB_HOME and
    * $TB_SID properly before you run Tibero.
     ******************************************************************************
    ```

8. Tibero をリサイクルするには、まず `tbdown` コマンドを使用してシャットダウンします。 次に例を示します。

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. 次に、`tbboot` を使用して Tibero を起動します。 次に例を示します。

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. テーブルスペースを作成するには、SYS ユーザー (sys/tmax) を使用してデータベースにアクセスした後、既定のボリュームと TACF に必要なテーブルスペースを作成します。

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. そして、次の SQL コマンドを入力します。

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Tibero を起動し、Tibero プロセスが実行されていることを確認します。

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

出力:

![Tibero の出力](media/tibero-01.png)

## <a name="install-odbc"></a>ODBC をインストールする

OpenFrame 内のアプリケーションは、オープン ソースの unixODBC プロジェクトによって提供されている ODBC API を使用して、Tibero データベースと通信します。

ODBC をインストールするには:

1. unixODBC-2.3.4.tar.gz インストール ファイルがあることを確認するか、または `wget unixODBC-2.3.4.tar.gz` コマンドを使用します。 次に例を示します。

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. バイナリを解凍します。 次に例を示します。

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. unixODBC-2.3.4 ディレクトリに移動し、チェック マシン情報を使用して、メイクファイルを生成します。 次に例を示します。

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     既定では、unixODBC は /usr/local にインストールされるので、場所を変更する値を `--prefix` で渡します。 同様に、構成ファイルは /etc に既定でインストールされるため、目的の場所の値を `--sysconfdir` で渡します。

4. メイクファイル `[oframe7@ofdemo unixODBC-2.3.4]$ make` を実行します。

5. コンパイルした後、実行可能ファイルをプログラム ディレクトリにコピーします。 次に例を示します。

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. vi を使用して bash プロファイル (`vi ~/.bash_profile`) を編集し、以下を追加します。

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. ODBC を適用します。 それに応じて、次のファイルを編集します。 次に例を示します。

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ source ~/.bash_profile

     [oframe7@ofdemo ~]$ cd

     [oframe7@ofdemo ~]$ odbcinst -j unixODBC 2.3.4
     DRIVERS............: /home/oframe7/odbcinst.ini
     SYSTEM DATA SOURCES: /home/oframe7/odbc.ini
     FILE DATA SOURCES..: /home/oframe7/ODBCDataSources
     USER DATA SOURCES..: /home/oframe7/unixODBC/etc/odbc.ini
     SQLULEN Size.......: 8
     SQLLEN Size........: 8
     SQLSETPOSIROW Size.: 8

     [oframe7@ofdemo ~]$ vi odbcinst.ini

     [Tibero]
     Description = Tibero ODBC driver for Tibero6
     Driver = /opt/tmaxdb/tibero6/client/lib/libtbodbc.so
     Setup = 
     FileUsage = 
     CPTimeout = 
     CPReuse = 
     Driver Logging = 7

     [ODBC]
     Trace = NO 
     TraceFile = /home/oframe7/odbc.log 
     ForceTrace = Yes 
     Pooling = No 
     DEBUG = 1

     [oframe7@ofdemo ~]$ vi odbc.ini

     [TVSAM]
     Description = Tibero ODBC driver for Tibero6 
     Driver = Tibero 
     DSN = TVSAM 
     SID = TVSAM 
     User = tibero 
     password = tmax
     ```

8. シンボリック リンクを作成し、Tibero データベースの接続を検証します。

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

次の出力が表示されます。

![SQL への接続を示す ODBC の出力](media/odbc-01.png)

## <a name="install-openframe-base"></a>OpenFrame Base をインストールする

Azure 上でシステムを管理するために OpenFrame で使用される個別のサービス (トランザクション処理サーバー プロセスなど) の前に、Base アプリケーション サーバーをインストールします。

**OpenFrame Base をインストールするには**

1. Tibero が正常にインストールされたことを確認した後、次の OpenFrame\_Base7\_0\_Linux\_x86\_64.bin インストーラー ファイルと base.properties 構成ファイルがあることを確認します。

2. 次の Tibero 固有の情報で bash プロファイルを更新します。

     ```bash
     alias ofhome='cd $OPENFRAME_HOME'
     alias ulog='cd $OPENFRAME_HOME/log/tmax/ulog'
     alias sysjcl='cd $OPENFRAME_HOME/volume_default/SYS1.JCLLIB'
     alias sysload='cd $OPENFRAME_HOME/volume_default/SYS1.LOADLIB'
     alias sysproc='cd $OPENFRAME_HOME/volume_default/SYS1.PROCLIB'
     alias oscsrc='cd $OPENFRAME_HOME/osc/oivp'
     alias osisrc='cd $OPENFRAME_HOME/osi/oivp'
     alias defvol='cd $OPENFRAME_HOME/volume_default'
     ```

3. bash プロファイル `[oframe7@ofdemo ~]$ . .bash_profile` を実行します。
4. Tibero プロセスが実行されていることを確認します。 次に例を示します。

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![基本](media/base-01.png)

     > [!IMPORTANT]
     > インストールの前に必ず Tibero を起動します。

5. [technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) でライセンスを生成し、OpenFrame Base、Batch、TACF、OSC のライセンスを適切なフォルダーに配置します。

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. OpenFrame Base のバイナリ ファイルと base.properties ファイルをダウンロードします。

     ```
     [oframe7@ofdemo ~]$ vi base.properties
     OPENFRAME_HOME= <appropriate location for installation> ex. /opt/tmaxapp/OpenFrame TP_HOST_NAME=<your IP Hostname> ex. ofdemo 
     TP_HOST_IP=<your IP Address> ex. 192.168.96.148 
     TP_SHMKEY=63481 
     TP_TPORTNO=6623 
     TP_UNBLOCK_PORT=6291 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     MASCAT_NAME=SYS1.MASTER.ICFCAT 
     MASCAT_CREATE=YES 
     DEFAULT_VOLSER=DEFVOL 
     VOLADD_DEFINE=YES TSAM_USERNAME=tibero 
     TSAM_PASSWORD=tmax 
     TSAM_DATABASE=oframe 
     DATASET_SHMKEY=63211 
     DSLOCK_DATA=SYS1.DSLOCK.DATA 
     DSLOCK_LOG=SYS1.DSLOCK.LOG 
     DSLOCK_SEQ=dslock_seq.dat 
     DSLOCK_CREATE=YES 
     OPENFRAME_LICENSE_PATH=/opt/tmaxapp/license/OPENFRAME TMAX_LICENSE_PATH=/opt/tmaxapp/license/TMAX
     ```

7. base.properties ファイルを使用してインストーラーを実行します。 次に例を示します。

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    完了すると、インストール完了メッセージが表示されます。

8. `ls -ltr` コマンドを使用して、OpenFrame Base のディレクトリ構造を確認します。 次に例を示します。

     ```
     [oframe7@ofdemo OpenFrame]$ ls -ltr
     total 44

     drwxrwxr-x. 4 oframe7 oframe7 61 Nov 30 16:57 UninstallerData 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 bin 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 cpm drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 data 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 include 
     drwxrwxr-x. 2 oframe7 oframe7 8192 Nov 30 16:57 lib 
     drwxrwxr-x. 6 oframe7 oframe7 48 Nov 30 16:57 log 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 profile 
     drwxrwxr-x. 7 oframe7 oframe7 62 Nov 30 16:57 sample 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 schema 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 temp 
     drwxrwxr-x. 3 oframe7 oframe7 16 Nov 30 16:57 shared 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 license 
     drwxrwxr-x. 23 oframe7 oframe7 4096 Nov 30 16:58 core 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 config 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 scripts 
     drwxrwxr-x. 2 oframe7 oframe7 25 Nov 30 16:58 volume_default
     ```

9. OpenFrame Base を起動します。

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![tmboot コマンドの出力](media/base-02.png)

10. si で tmadmin コマンドを使用して、プロセスの状態が準備完了であることを確認します。 各プロセスの **[status]** 列に [RDY] と表示されます。

     ![tmadmin コマンドの出力](media/base-03.png)

11. OpenFrame Base をシャットダウンします。

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(vtammgr:43) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLH downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLL downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TLM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-openframe-batch"></a>OpenFrame Batch をインストールする

OpenFrame Batch は、メインフレームのバッチ環境をシミュレートするいくつかのコンポーネントで構成され、Azure でバッチ ジョブを実行するために使用されます。

**Batch をインストールするには**

1. Base が正常にインストールされたことを確認した後、OpenFrame\_Batch7\_0\_Fix2\_MVS\_Linux\_x86\_64.bin インストーラー ファイルと batch.properties 構成ファイルがあることを確認します。

2. コマンド プロンプトで「`vi batch.properties`」と入力し、vi を使用して batch.properties ファイルを編集します。

3. パラメーターを次のように変更します。

     ```
     OPENFRAME_HOME = /opt/tmaxapp/OpenFrame
     DEFAULT_VOLSER=DEFVOL 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     RESOURCE_SHMKEY=66991 
     #JOBQ_DATASET_CREATE=YES 
     #OUTPUTQ_DATASET_CREATE=YES 
     DEFAULT_JCLLIB_CREATE=YES 
     DEFAULT_PROCLIB_CREATE=YES 
     DEFAULT_USERLIB_CREATE=YES 
     TJES_USERNAME=tibero 
     TJES_PASSWORD=tmax 
     TJES_DATABASE=oframe 
     BATCH_TABLE_CREATE=YES
     ```

4. batch のインストーラーを実行するには、コマンド プロンプトで次のように入力します。

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. インストールが完了したら、コマンド プロンプトで「`tmboot`」と入力して、インストールされた OpenFrame スイートを起動します。

    ![tmboot の出力](media/tmboot-01.png)

6. コマンド プロンプトで「`tmadmin`」と入力して、OpenFrame プロセスを確認します。

    ![Tmax Admin 画面](media/tmadmin-01.png)

7. 次のコマンドを実行します。

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. `tmdown` コマンドを使用し、Batch を起動してシャットダウンします。

     ```
     [oframe7@ofdemo ~]$tmdown
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:44) downed: Wed Sep  7 16:01:46 2016
     TMDOWN: SERVER(vtammgr: 43) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:45) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:46) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjinit:55) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:49) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:51) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:52) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:53) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLL downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TLM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLH downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-tacf"></a>TACF をインストールする

TACF Manager は OpenFrame のサービス モジュールであり、RACF セキュリティによってシステムとリソースへのユーザー アクセスを制御します。

**TACF をインストールするには**

1. OpenFrame\_Tacf7\_0\_Fix2\_Linux\_x86\_64.bin インストーラー ファイルと tacf.properties 構成ファイルがあることを確認します。
2. Batch が正常にインストールされたことを確認した後、vi を使用して tacf.properties ファイルを開きます (`vi tacf.properties`)。
3. TACF のパラメーターを変更します。

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. TACF のインストーラーが完了した後、TACF の環境変数を適用します。 コマンド プロンプトに、次のコマンドを入力します。

     ```
     source \~/.bash\_profile
     ```

5. TACF インストーラーを実行します。 コマンド プロンプトに、次のコマンドを入力します。

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     次のような画面が出力されます。

     ```
     Wed Dec 07 17:36:42 EDT 2016
     Free Memory: 18703 kB 
     Total Memory: 28800 kB

     4 Command Line Args: 
     0:  -f 1:  tacf.properties 
     2:  -m 
     3:  SILENT 
     java.class.path: 
     /tmp/install.dir.41422/InstallerData 
     /tmp/install.dir.41422/InstallerData/installer.zip 
     ZGUtil.CLASS_PATH: 
     /tmp/install.dir.41422/InstallerData 
     tmp/install.dir.41422/InstallerData/installer.zip 
     sun.boot.class.path: 
     /tmp/install.dir.41422/Linux/resource/jre/lib/resources.jar /tmp/install.dir.41422/Linux/resource/jre/lib/rt.jar /tmp/install.dir.41422/Linux/resource/jre/lib/sunrsasign.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jsse.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jce.jar /tmp/install.dir.41422/Linux/resource/jre/lib/charsets.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jfr.jar /tmp/install.dir.41422/Linux/resource/jre/classes
     ```

6. コマンド プロンプトで「`tmboot`」と入力して OpenFrame を再起動します。 次のような画面が出力されます。

     ```
     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/7 
     TMBOOT: TMM is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLL is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLH is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: TLM(tlm) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsasvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrlhsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdsedt) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrcmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofruisvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsmlog) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(vtammgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjschd) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjinit) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjhist) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjspbk) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrpmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmtsmgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(tmsvr) is starting: Wed Sep  7 17:48:53 2016
     ```

7. `si` コマンドで `tmadmin` を使用して、プロセスの状態が準備完了であることを確認します。 次に例を示します。

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     **[status]** 列に [RDY] と表示されます。

    ![[status] 列の [RDY]](media/tmboot-02.png)

8. 次のコマンドを実行します。

     ```
     $$2 NODE1 (tmadm): quit 
     DM quit for node (NODE1)

     [oframe7@ofdemo ~]$ tacfmgr 
     Input USERNAME  : ROOT 
     Input PASSWORD  : SYS1

     TACFMGR: TACF MANAGER START!!!
     QUIT TACFMGR: TACF MANAGER END!!!

     [oframe7@ofdemo ~]$ tmdow
     ```

9. `tmdown` コマンドを使用してサーバーをシャットダウンします。 次のような画面が出力されます。

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(tmsvr:60) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLL downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLH downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TLM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-prosort"></a>ProSort をインストールする

ProSort は、バッチ トランザクションでデータの並べ替えに使用されるユーティリティです。

**ProSort をインストールするには**

1. Batch が正常にインストールされたことを確認した後、**prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz** インストーラー ファイルがあることを確認します。

2. プロパティ ファイルを使用してインストーラーを実行します。 コマンド プロンプトに、次のコマンドを入力します。

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. prosort ディレクトリをホームの場所に移動します。 コマンド プロンプトに、次のコマンドを入力します。

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. license サブディレクトリを作成し、ライセンス ファイルをコピーします。 次に例を示します。

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. vi で bash.profile を開き (`vi .bash_profile`)、次のように更新します。

     ```bash
     #       PROSORT

     PROSORT_HOME=/opt/tmaxapp/prosort 
     PROSORT_SID=gbg 
     PATH=$PATH:$PROSORT_HOME/bin LD_LIBRARY_PATH=$PROSORT_HOME/lib:$LD_LIBRARY_PATH LIBPATH$PROSORT_HOME/lib:$LIBPATH 
     export PROSORT_HOME PROSORT_SID 
     PATH LD_LIBRARY_PATH LIBPATH 
     PATH=$PATH:$OPENFRAME_HOME/shbin 
     export PATH
     ```

6. bash プロファイルを実行するには、コマンド プロンプトで次のように入力します: `. .bash_profile`。

7. 構成ファイルを作成します。 次に例を示します。

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. シンボリック リンクを作成します。 次に例を示します。

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. `prosort -h` コマンドを実行して、ProSort のインストールを確認します。 次に例を示します。

     ```
     oframe@oframe7: prosort -h

     Usage: prosort [options] [sort script files]
     options ------
     -h             Display this information 
     -v             Display version information 
     -s             Display state information 
     -j             Display profile information 
     -x             Use SyncSort compatible mode
     ```

## <a name="install-ofcobol"></a>OFCOBOL をインストールする

OFCOBOL は、メインフレームの COBOL プログラムを解釈する OpenFrame コンパイラです。 

**OFCOBOL をインストールするには**

1. Batch/Online が正常にインストールされたことを確認した後、OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin インストーラー ファイルが存在することを確認します。

2. OFCOBOL インストーラーを実行するには、コマンド プロンプトで次のように入力します。

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. ライセンス契約を読み、Enter キーを押して続行します。

4. ライセンス契約に同意します。 インストールが完了すると、次のように表示されます。

     ```
     Choose Install Folder 
     --------------------
     Where would you like to install?
     Default Install Folder: /home/oframe7/OFCOBOL

     ENTER AN ABSOLUTE PATH, OR PRESS <ENTER> TO ACCEPT THE DEFAULT : /opt/tmaxapp/OFCOBOL

     INSTALL FOLDER IS: /opt/tmaxapp/OFCOBOL 
     IS THIS CORRECT? (Y/N): Y[oframe7@ofdemo ~]$ vi .bash_profile

     ============================================================================ Installing... 
     ------------
     [==================|==================|==================|==================] 
     [------------------|------------------|------------------|------------------]

     =============================================================================== Installation Complete 
     --------------------
     Congratulations. OpenFrame_COBOL has been successfully installed
     PRESS <ENTER> TO EXIT THE INSTALLER
     ```

5. vi で bash プロファイルを開き (`vi .bash_profile`)、OFCOBOL の変数で更新されていることを確認します。
6. bash プロファイルを実行します。 コマンド プロンプトに、次のコマンドを入力します。

     ```
      source ~/.bash_profile
     ```

7. OFCOBOL ライセンスをインストールされたフォルダーにコピーします。 次に例を示します。
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. OpenFrame の tjclrun.conf 構成ファイルに移動し、vi で開きます。 次に例を示します。
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   次に示すのは、変更前の SYSLIB セクションです。
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   次に示すのは、変更後の SYSLIB セクションです。
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. OpenFrame\_COBOL\_InstallLog.log ファイルを vi で開き、エラーがないことを確認します。 次に例を示します。
     ```
     [oframe7@ofdemo ~]$ vi $OFCOB_HOME/UninstallerData/log/OpenFrame_COBOL_InstallLog.log 
     …….. 
     Summary 
     ------
     Installation: Successful. 
     131 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalError
     ```
10. `ofcob --version` コマンドを使用してバージョン番号を表示し、インストールを確認します。 次に例を示します。

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. `tmdown/tmboot` コマンドを使用して OpenFrame を再起動します。

## <a name="install-ofasm"></a>OFASM をインストールする

OFASM は、メインフレームのアセンブラー プログラムを解釈する OpenFrame コンパイラです。

**OFASM をインストールするには**

1. Batch/Online が正常にインストールされたことを確認した後、OpenFrame\_ASM3\_0\_Linux\_x86\_64.bin インストーラー ファイルが存在することを確認します。

2. インストーラーを実行します。 次に例を示します。

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. ライセンス契約を読み、Enter キーを押して続行します。
4. ライセンス契約に同意します。
5. bash プロファイルが OFASM の変数で更新されたことを確認します。 次に例を示します。

     ```
     [oframe7@ofdemo ~]$ source .bash_profile
     [oframe7@ofdemo ~]$ ofasm --version 
     # TmaxSoft OpenFrameAssembler v3 r328 
     (3ff35168d34f6e2046b96415bbe374160fcb3a34)

     [oframe7@ofdemo OFASM]$ vi .bash_profile

     # OFASM ENV 
     export OFASM_HOME=/opt/tmaxapp/OFASM 
     export OFASM_MACLIB=$OFASM_HOME/maclib/free_macro 
     export PATH="${PATH}:$OFASM_HOME/bin:" 
     export LD_LIBRARY_PATH="./:$OFASM_HOME/lib:$LD_LIBRARY_PATH"
     ```

6. OpenFrame の tjclrun.conf 構成ファイルを vi で開き、次のように編集します。

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     次に示すのは、変更 "*前*" の [SYSLIB] セクションです。

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     次に示すのは、変更 "*後*" の [SYSLIB] セクションです。

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. OpenFrame\_ASM\_InstallLog.log ファイルを vi で開き、エラーがないことを確認します。 次に例を示します。

     ```
     [oframe7@ofdemo ~]$ vi 
     $OFASM_HOME/UninstallerData/log/OpenFrame_ASM_InstallLog.log 
     …….. 
     Summary 
     ------

     Installation: Successful.

     55 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalErrors
     ```

8. 次のいずれかのコマンドを発行して OpenFrame を再起動します。

     ```
     tmdown / tmboot
     ```

     — または —

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>OSC をインストールする

OSC は、IBM CICS に似た OpenFrame 環境であり、高速の OLTP トランザクションと他の管理機能をサポートします。

**OSC をインストールするには**

1. Base が正常にインストールされたことを確認した後、OpenFrame\_OSC7\_0\_Fix2\_Linux\_x86\_64.bin インストーラー ファイルと osc.properties 構成ファイルがあることを確認します。
2. osc.properties ファイルで、次のパラメーターを編集します。
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. 次のようにプロパティ ファイルを使用してインストーラーを実行します。

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     完了すると、"インストール完了" メッセージが表示されます。

4. bash プロファイルが OSC の変数で更新されたことを確認します。
5. OpenFrame\_OSC7\_0\_Fix2\_InstallLog.log ファイルを確認します。 次のように表示されます。

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. vi を使用して、ofsys.seq 構成ファイルを開きます。 次に例を示します。

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. \#BASE および \#BATCH セクションで、次のようにパラメーターを編集します。

     ```
     Before changes
     #BASE
     ofrsasvr
     ofrlhsvr
     ofrdmsvr
     ofrdsedt
     ofrcmsvr
     ofruisvr
     ofrsmlog
     vtammgr
     TPFMAGENT

     #BATCH 
     #BATCH#obmtsmgr
     #BATCH#ofrpmsvr
     #BATCH#obmjmsvr
     #BATCH#obmjschd
     #BATCH#obmjinit
     #BATCH#obmjhist
     #BATCH#obmjspbk
     #TACF #TACF#tmsvr

     After changes  #BATCH
     #BASE          obmtsmgr 
     ofrsasvr       ofrpmsvr
     ofrlhsvr       obmjmsvr
     ofrdmsvr       obmjschd
     ofrdsedt       obmjinit
     ofrcmsvr       obmjhist
     ofruisvr       obmjspbk
     ofrsmlog
     vtammgr        #TACF
     TPFMAGENT      tmsvr
    ```

8. ライセンス ファイルをコピーします。 次に例を示します。

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. OSC を起動およびシャットダウンするには、コマンド プロンプトで「`osctdlinit OSCOIVP1`」と入力して、CICS 領域共有メモリを初期化します。

10. `oscboot` を実行して OSC を起動します。 次のような画面が出力されます。

     ```
     OSCBOOT : pre-processing       [ OK ]

     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/12 
          TMBOOT: TMM is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLL is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLH is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: TLM(tlm) is starting: Mon Sep 12 01:40:25 2016 
     ```

11. プロセスの状態が準備完了であることを確認するには、si で `tmadmin` コマンドを使用します。 すべてのプロセスの **[status]** 列に [RDY] と表示されている必要があります。

    ![RDY と表示されているプロセス](media/tmadmin-02.png)

12. `oscdown` コマンドを使用して OSC をシャットダウンします。

## <a name="install-jeus"></a>JEUS をインストールする

JEUS (Java Enterprise User Solution) では、OpenFrame Web アプリケーション サーバーのプレゼンテーション層が提供されます。

JEUS をインストールする前に、Apache Ant パッケージをインストールします。このパッケージでは、JEUS のインストールに必要なライブラリとコマンド ライン ツールが提供されます。

**Apache Ant をインストールするには**

1. `wget` コマンドを使用して Ant のバイナリをダウンロードします。 次に例を示します。

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. `tar` ユーティリティを使用してバイナリ ファイルを抽出し、適切な場所に移動します。 次に例を示します。

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. 効率をよくするため、シンボリック リンクを作成します。

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. vi で bash プロファイルを開き (`vi .bash_profile`)、次の変数で更新します。

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  変更した環境変数を適用します。 次に例を示します。

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**JEUS をインストールするには**

1. `tar` ユーティリティを使用して、インストーラーを展開します。 次に例を示します。

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. **jeus** フォルダーを作成し (`mkdir jeus7`)、バイナリを解凍します。
3. **setup** ディレクトリに変更します (または、ご自分の環境の JEUS パラメーターを使用します)。 次に例を示します。

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. ビルドを実行する前に、`ant clean-all` を実行します。 次のような画面が出力されます。

     ```
     Buildfile: /home/oframe7jeus7/setup/build.xml

     clean-bin:
     delete-domain:
     [echo] Deleting a domain configuration: domain = jeus_domain
     delete-nodesxml:
     clean-config:
     clean-all:
     BUILD SUCCESSFUL
     Total time: 0 seconds
     ```

5.  domain-config-template.properties ファイルのバックアップを作成します。 次に例を示します。

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. domain-config-template.properties ファイルを vi で開きます。

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. `jeus.password=jeusadmin nodename=Tmaxsoft` を `jeus.password=tmax1234 nodename=ofdemo` に変更します。

8. `ant install` コマンドを実行して JEUS をビルドします。
9.  次に示すように、.bash\_profile ファイルを JEUS の変数で更新します。

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. bash プロファイルを実行します。 次に例を示します。

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *オプション*。 JEUS コンポーネントを簡単にシャットダウンおよびブートできるように、別名を作成します。

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. インストールを確認するには、次のようにドメイン管理サーバーを起動します。

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. 次の構文で Web ログオンして確認します。

     ```
     http://<IP>:<port>/webadmin/login
     ```

     たとえば、<http://192.168.92.133:9736/webadmin/login.> などとします。ログオン画面が表示されます。
    
     ![JEUS WebAdmin ログオン画面](media/jeus-01.png)

     > [!NOTE]
     > ポート セキュリティで何らかの問題が発生する場合は、ポート 9736 を開くか、またはファイアウォールを無効にします (`systemctl stop firewall`)。

14. server1 のホスト名を変更するには、 **[Lock & Edit]\(ロックして編集\)** をクリックし、**server1** をクリックします。 [Server]\(サーバー\) ウィンドウで、ホスト名を次のように変更します。

    1.  **[Nodename]\(ノード名\)** を **ofdemo** に変更します。
    2.  ウィンドウの右側にある **[OK]** をクリックします。
    3.  ウィンドウの左下で **[Apply changes]\(変更の適用\)** をクリックし、説明に「*ホスト名の変更*」と入力します。

    ![JEUS WebAdmin 画面](media/jeus-02.png)

15. 確認画面で、構成が正常に行われたことを確認します。

    ![jeus_domain サーバー画面](media/jeus-03.png)

16. 次のコマンドを使用して、マネージド サーバー プロセス "server1" を起動します。

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>OFGW をインストールする

OFGW は OpenFrame ゲートウェイであり、3270 端末エミュレーターと OSI ベースの間の通信をサポートし、端末エミュレーターと OSI の間のセッションを管理します。

**OFGW をインストールするには**

1. JEUS が正常にインストールされたことを確認した後、OFGW7\_0\_1\_Generic.bin インストーラー ファイルが存在することを確認します。
2. インストーラーを実行します。 次に例を示します。

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. 対応するプロンプトに対して、次の場所を使用します。
     -   JEUS ホーム ディレクトリ
     -   JEUS ドメイン名
     -   JEUS サーバー名
     -   Tibero ドライバー
     -   Tmax ノード ID ofdemo

4. それ以外については既定値をそのまま使用し、Enter キーを押してインストーラーを終了します。

5. OFGW の URL が正しく動作することを確認します。

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     次の画面が表示されます。

    ![OpenFrame WebTerminal](media/ofgw-01.png)

## <a name="install-ofmanager"></a>OFManager をインストールする

OFManager では、Web 環境での OpenFrame の運用と管理の機能が提供されます。

**OFManager をインストールするには**

1. OFManager7\_Generic.bin インストーラー ファイルがあることを確認します。
2. インストーラーを実行します。 次に例を示します。

     ```
     OFManager7_Generic.bin
     ```

3.  Enter キーを押して続行し、ライセンス条項に同意します。
4.  インストール フォルダーを選択します。
5.  既定値を受け入れます。
6.  データベースとして Tibero を選択します。
7.  Enter キーを押してインストーラーを終了します。
8.  OFManager の URL が正しく動作することを確認します。

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

スタート画面が表示されます。

![Tmax OpenFrame Manager のログオン画面](media/ofmanager-01.png)

これで、OpenFrame コンポーネントのインストールは完了です。

## <a name="next-steps"></a>次のステップ

メインフレームの移行を検討している場合は、当社の拡大しているパートナー エコシステムを使用すると役に立ちます。 パートナー ソリューションの選択に関する詳細なガイダンスについては、「[Platform Modernization Alliance (プラットフォーム最新化アライアンス)](https://datamigration.microsoft.com/)」を参照してください。

-   [Azure を使ってみる](../../../../index.yml)
-   [Host Integration Server (HIS) のドキュメント](/host-integration-server/)
-   [Azure 仮想データ センターのリフトアンドシフト ガイド](/archive/blogs/azurecat/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide)
