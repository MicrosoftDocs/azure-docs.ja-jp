---
title: "VM で Oracle Data Guard を構成する | Microsoft Docs"
description: "高可用性と障害復旧のために Oracle Data Guard を Azure 仮想マシンにセットアップして実装するチュートリアルの手順。"
services: virtual-machines-windows
author: rickstercdn
manager: timlt
documentationcenter: 
tags: azure-service-management
ms.assetid: 5ec5b9a1-7b09-4e01-ab0a-4897585d08d7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/06/2016
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 2085b485311f0889c646bc100a32bb41b7363cd2
ms.lasthandoff: 03/27/2017


---
# <a name="configuring-oracle-data-guard-for-azure"></a>Azure 用の Oracle データ保護の構成
このチュートリアルでは、高可用性と障害復旧のための Azure Virtual Machines 環境に Oracle Data Guard を設定、実装する方法について説明します。 このチュートリアルでは、RAC 以外の Oracle データベースの一方向レプリケーションについて説明します。

Oracle Data Guard は Oracle データベースのデータの保護および障害復旧をサポートします。 シンプルで高パフォーマンスな、障害復旧、データの保護、および Oracle データベース全体の高可用性のすぐに使えるソリューションです。

このチュートリアルでは、Oracle データベースの高可用性と障害復旧の概念に関する理論的かつ実用的な知識があることを前提としています。 詳細については、[Oracle の Web サイト](http://www.oracle.com/technetwork/database/features/availability/index.html)と [Oracle Data Guard の概念と管理に関するガイド](https://docs.oracle.com/cd/E11882_01/server.112/e41134/toc.htm)を参照してください。

また、このチュートリアルでは、次の前提条件が既に実装されていることを前提としています。

* 「[Oracle 仮想マシンのイメージに関するその他の考慮事項](oracle-considerations.md)」の「高可用性と障害復旧に関する考慮」を既に確認していること。 Azure では、スタンドアロンの Oracle Database インスタンスをサポートしていますが、Oracle Real Application Cluster (Oracle RAC) をサポートしていません。
* 同じプラットフォームで提供される Oracle Enterprise Edition のイメージを使用して、Azure に 2 つの Virtual Machines (VM) を作成していること。 Virtual Machines が永続的なプライベート IP アドレスを介して相互にアクセスできるように、[同じクラウド サービス](../../virtual-machines-windows-load-balance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にあり、同じ Virtual Network にあることを確認します。 さらに、VM を同じ[可用性セット](../../virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に配置することにより、Azure は個別のフォールト ドメインとアップグレード ドメインに配置できるようになります。 Oracle Data Guard は Oracle Database Enterprise Edition でのみ使用できます。 それぞれのマシンには、少なくとも 2 GB のメモリと 5 GB のディスク領域が必要です。 プラットフォームが提供する VM サイズの最新情報については、「[Azure の仮想マシンのサイズ](../../virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。 VM に追加のディスク ボリュームが必要な場合は、追加のディスクをアタッチすることができます。 詳細については、「[How to Attach a Data Disk to a Virtual Machine (データ ディスクを仮想マシンに追加する方法)](attach-disk.md)」を参照してください。
* Azure クラシック ポータルでは、仮想マシンの名前は、プライマリ VM が「Machine1」として、スタンバイ VM が「Machine2」として設定されています。
* **ORACLE_HOME** 環境変数を `C:\OracleDatabase\product\11.2.0\dbhome_1\database` のようなプライマリおよびスタンバイ Virtual Machines 内で同じ oracle ルート インストール パスをポイントするよう設定していること。
* Windows サーバーに、**Administrators** グループのメンバーまたは **ORA_DBA** グループのメンバーとしてログオンしていること。

このチュートリアルでは、次のことについて説明します。

物理スタンバイ データベース環境の展開

1. プライマリ データベースの作成
2. スタンバイ データベースを作成るためのプライマリ データベースの準備
   
   1. 強制ログ記録の有効化
   2. パスワード ファイルの作成
   3. スタンバイ redo ログの構成
   4. アーカイブの有効化
   5. プライマリ データベースの初期化パラメーターの設定

物理スタンバイデータベースの作成

1. スタンバイ データベースの初期化パラメーターのファイルを準備
2. プライマリおよびスタンバイのマシンでデータベースをサポートするためのリスナーと tnsnames の構成
   
   1. 両方のデータベースのエントリを保持するために両方のサーバーで listener.ora を構成
   2. プライマリおよびスタンバイの両方のデータベースのエントリを保持するために、プライマリおよびスタンバイの Virtual Machines で tnsnames.ora を構成。 
   3. リスナーを開始し、両方のサービスの両方の Virtual Machines で tnsping を確認します。
3. マウント状態でスタンバイ インスタンスを起動
4. RMAN を使用して、データベースのクローンとスタンバイ データベースを作成
5. 物理スタンバイ データベースを管理回復モードで起動
6. 物理スタンバイデータベースの検証

> [!IMPORTANT]
> このチュートリアルは、次のハードウェアおよびソフトウェア構成に対して設定、テストされています。
> 
> |  | **プライマリ データベース** | **スタンバイ データベース** |
> | --- | --- | --- |
> | **Oracle リリース** |Oracle11g Enterprise リリース (11.2.0.4.0) |Oracle11g Enterprise リリース (11.2.0.4.0) |
> | **マシン名** |マシン 1 |マシン 2 |
> | **オペレーティング システム** |Windows 2008 R2 |Windows 2008 R2 |
> | **Oracle SID** |TEST |TEST\_STBY |
> | **メモリ** |最小 2 GB |最小 2 GB |
> | **ディスク領域** |最小 5 GB |最小 5 GB |
> 
> 

Oracle データベースおよび Oracle Data Guard の今後のリリースでは、追加の変更を実装する必要が生じる可能性があります。 最新バージョンについては、Oracle Web サイトの [Data Guard](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) と [Oracle データベース](http://www.oracle.com/us/corporate/features/database-12c/index.html)に関するドキュメントをご覧ください。

## <a name="implement-the-physical-standby-database-environment"></a>物理スタンバイ データベース環境の展開
### <a name="1-create-a-primary-database"></a>1.プライマリ データベースの作成
* プライマリ仮想マシンでプライマリ データベース "TEST" を作成 詳細は、「Oracle データベースの作成と構成」を参照してください。
* データベースの名前を表示するには、SQL*Plus コマンド プロンプトで、SYSDBA ロールを持つ SYS ユーザーとしてデータベースに接続し、次のステートメントを実行します。
  
        SQL> select name from v$database;
  
        The result will display like the following:
  
        NAME
        ---------
        TEST
* 次に、dba_data_files システム ビューからデータベース ファイルの名前をクエリします。
  
        SQL> select file_name from dba_data_files;
        FILE_NAME
        -------------------------------------------------------------------------------
        C:\ <YourLocalFolder>\TEST\USERS01.DBF
        C:\ <YourLocalFolder>\TEST\UNDOTBS01.DBF
        C:\ <YourLocalFolder>\TEST\SYSAUX01.DBF
        C:\<YourLocalFolder>\TEST\SYSTEM01.DBF
        C:\<YourLocalFolder>\TEST\EXAMPLE01.DBF

### <a name="2-prepare-the-primary-database-for-standby-database-creation"></a>2.スタンバイ データベースを作成るためのプライマリ データベースの準備
スタンバイ データベースを作成する前に、プライマリ データベースが正しく構成されていることを確認することを推奨します。 実行する必要のある手順の一覧を次に示します。

1. 強制ログ記録の有効化
2. パスワード ファイルの作成
3. スタンバイ redo ログの構成
4. アーカイブの有効化
5. プライマリ データベースの初期化パラメーターの設定

#### <a name="enable-forced-logging"></a>強制ログ記録の有効化
スタンバイ データベースを実装するには、プライマリ データベースでの "強制ログ" を有効にする必要があります。 このオプションにより、"nologging" 操作が完了しても、強制ログが優先され、すべての操作が redo ログに記録されます。 そのため、プライマリ データベース内のすべてがログに記録され、スタンバイへレプリケーションには、プライマリ データベースでのすべての操作が含まれていることを確認します。 強制ログを有効にするには、alter database ステートメントを実行します。

    SQL> ALTER DATABASE FORCE LOGGING;

    Database altered.

#### <a name="create-a-password-file"></a>パスワード ファイルの作成
プライマリサーバーからスタンバイサーバーまでアーカイブされたログを送り適用するは、sys パスワードはプライマリおよびスタンバイサーバーの両方で同一でなくてはなりません。 そのため、プライマリ データベースでパスワード ファイルを作成し、スタンバイ サーバーにコピーします。

> [!IMPORTANT]
> Oracle Database 12 c を使用する場合は、Oracle Data Guard の管理に使用できる新しいユーザー **SYSDG**があります。 詳細については、 [Oracle Database 12c Release の変更](http://docs.oracle.com/database/121/UNXAR/release_changes.htm#UNXAR404)を参照してください。
> 
> 

さらに、ORACLE\_HOME 環境が既に Machine1 で定義されていることを確認します。 そうでない場合は「環境変数」ダイアログ ボックスを使用して環境変数として定義します。 このダイアログ ボックスにアクセスするには、**コントロール パネル**の [システム] アイコンをダブルクリックして**システム** ユーティリティを開始します。次に **[詳細設定]** タブをクリックし、**[環境変数]** を選択します。 環境変数を設定するには、**[システム変数]** で **[新規]** をクリックします。 環境変数を設定した後に、既存の Windows コマンド プロンプトを閉じ、新しいものを開きます。

C:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\database などの Oracle\_Home ディレクトリに切り替えるには、次のステートメントを実行します。

    cd %ORACLE_HOME%\database

次に、パスワード ファイル作成ユーティリティ [ORAPWD](http://docs.oracle.com/cd/B28359_01/server.111/b28310/dba007.htm)を使用してパスワード ファイルを作成します。 同じ Machine1 Windows のコマンド プロンプトで **SYS**のパスワードのパスワード値を設定することにより、次のコマンドを実行します。

    ORAPWD FILE=PWDTEST.ora PASSWORD=password FORCE=y

このコマンドは、ORACLE\_HOME\\database ディレクトリ内に PWDTEST.ora という名前のパスワード ファイルを作成します。 このファイルを手動で Machine2 内の %ORACLE\_HOME%\\database ディレクトリにコピーする必要があります。

#### <a name="configure-a-standby-redo-log"></a>スタンバイ redo ログの構成
次に、スタンバイ状態になったときに、プライマリが再実行を受信正しくできるように、スタンバイの redo ログを構成する必要があります。 ここで事前作成すると、スタンバイ状態で redo ログも自動的に作成できます。 オンライン redo ログと同じサイズでスタンバイ redo ログ (SRL) を構成することが重要です。 現在のスタンバイ redo ログ ファイルのサイズは、現在のプライマリ データベースのオンライン redo ログ ファイルのサイズと正確に一致しなくてはなりません。

Machine1 の SQL\*PLUS コマンド プロンプトで次のステートメントを実行します。 v$logfile は redo ログ ファイルに関する情報を含むシステム ビューです。

    SQL> select * from v$logfile;
    GROUP# STATUS  TYPE    MEMBER                                                       IS_
    ---------- ------- ------- ------------------------------------------------------------ ---
    3         ONLINE  C:\<YourLocalFolder>\TEST\REDO03.LOG               NO
    2         ONLINE  C:\<YourLocalFolder>\TEST\REDO02.LOG               NO
    1         ONLINE  C:\<YourLocalFolder>\TEST\REDO01.LOG               NO
    Next, query the v$log system view, displays log file information from the control file.
    SQL> select bytes from v$log;
    BYTES
    ----------
    52428800
    52428800
    52428800


52428800 が 50 メガバイトであることに注意してください。

次に、SQL\*Plus ウィンドウで、次のステートメントを実行して、スタンバイ redo ログ ファイル グループをスタンバイ データベースに追加し、GROUP 句を使用してグループを識別する番号を指定します。 グループ番号を使用するとスタンバイ redo ログのファイル グループを簡単に管理することができます。

    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 4 'C:\<YourLocalFolder>\TEST\REDO04.LOG' SIZE 50M;
    Database altered.
    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 5 'C:\<YourLocalFolder>\TEST\REDO05.LOG' SIZE 50M;
    Database altered.
    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 6 'C:\<YourLocalFolder>\TEST\REDO06.LOG' SIZE 50M;
    Database altered.

次に、 redo ログ ファイルについての情報の一覧するために次のシステム ビューを実行します。 この操作では、スタンバイ redo ログ ファイルのグループが作成されたことを確認できます。

    SQL> select * from v$logfile;
    GROUP# STATUS  TYPE MEMBER IS_
    ---------- ------- ------- --------------------------------------------- ---
    3         ONLINE C:\<YourLocalFolder>\TEST\REDO03.LOG NO
    2         ONLINE C:\<YourLocalFolder>\TEST\REDO02.LOG NO
    1         ONLINE C:\<YourLocalFolder>\TEST\REDO01.LOG NO
    4         STANDBY C:\<YourLocalFolder>\TEST\REDO04.LOG
    5         STANDBY C:\<YourLocalFolder>\TEST\REDO05.LOG NO
    6         STANDBY C:\<YourLocalFolder>\TEST\REDO06.LOG NO
    6 rows selected.

#### <a name="enable-archiving"></a>アーカイブの有効化
次に、プライマリ データベースを ARCHIVELOG モードにして自動保存を有効にするには、次のステートメントを実行してアーカイブを有効にします。 データベースをマウントすることにより、アーカイブ ログ モードを有効にし、archivelog コマンドを実行します。

まず、sysdba としてログインします。 Windows のコマンド プロンプトで次を実行します。

    sqlplus /nolog

    connect / as sysdba

次に SQL\*Plus コマンド プロンプトでデータベースをシャットダウンします。

    SQL> shutdown immediate;
    Database closed.
    Database dismounted.
    ORACLE instance shut down.

次に、データベースをマウントするために、 startup mount コマンドを実行します。 これにより、Oracle で指定されたデータベースでインスタンスを関連付けます。

    SQL> startup mount;
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes
    Database mounted.

次に、以下を実行します。

    SQL> alter database archivelog;
    Database altered.

次に、Alter database ステートメントをオープンクローズで実行、データベースを通常の使用に使用できるようにします。

    SQL> alter database open;

    Database altered.

#### <a name="set-primary-database-initialization-parameters"></a>プライマリ データベースの初期化パラメーターの設定
Data Guard を構成するには、正規 pfile (初期化パラメーターのテキスト ファイル) で、スタンバイのパラメーターを作成して構成する必要があります。 Pfile の準備ができたら、server パラメーター ファイル (SPFILE) に変換する必要があります。

INIT.ORA パラメーターを使用して、Data Guard 環境を制御できます。 このチュートリアルに従うと、プライマリ データベースの INIT.ORA を更新する必要があります。そのためプライマリ状態またはスタンバイ状態の両方のロールを保持できます。 

    SQL> create pfile from spfile;
    File created.

次に、スタンバイのパラメーターを追加するには pfile を編集する必要があります。 そのためには %ORACLE\_HOME%\\database の場所で INITTEST.ORA ファイルを開きます。 次に、INITTEST.ora ファイルに次のステートメントを追加します。 INIT.ORA ファイルの名前付け規則は INIT\<YourDatabaseName\>.ORA です。

    db_name='TEST'
    db_unique_name='TEST'
    LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
    LOG_ARCHIVE_DEST_1= 'LOCATION=C:\OracleDatabase\archive   VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=TEST'
    LOG_ARCHIVE_DEST_2= 'SERVICE=TEST_STBY LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE) DB_UNIQUE_NAME=TEST_STBY'
    LOG_ARCHIVE_DEST_STATE_1=ENABLE
    LOG_ARCHIVE_DEST_STATE_2=ENABLE
    REMOTE_LOGIN_PASSWORDFILE=EXCLUSIVE
    LOG_ARCHIVE_FORMAT=%t_%s_%r.arc
    LOG_ARCHIVE_MAX_PROCESSES=30
    # Standby role parameters --------------------------------------------------------------------
    fal_server=TEST_STBY
    fal_client=TEST
    standby_file_management=auto
    db_file_name_convert='TEST_STBY','TEST'
    log_file_name_convert='TEST_STBY','TEST'
    # ---------------------------------------------------------------------------------------------


前のステートメント ブロックには、3 つの重要なセットアップ項目が含まれています。

* **LOG_ARCHIVE_CONFIG...:** このステートメントを使用して、一意のデータベースの ID を定義します。
* **LOG_ARCHIVE_DEST_1...:** このステートメントを使用して、ローカルのアーカイブ フォルダーの場所を定義します。 データベース用のアーカイブのニーズのための新しいディレクトリを作成し、Oracle の既定フォルダー %ORACLE_HOME%\database\archive を使用する代わりに、明示的にこのステートメントを使用して、ローカルのアーカイブ場所を指定することをお勧めします。
* **LOG_ARCHIVE_DEST_2 ....LGWR ASYNC...:**トランザクション redo データを収集するために、非同期ログライター プロセス (LGWR) を定義し、スタンバイ変換先に転送します。 ここでは、DB_UNIQUE_NAME は転送先のスタンバイ サーバーにあるデータベースの一意の名前を指定します。

新規パラメーターファイルが準備できたら、ここから spfile を作成する必要があります。

最初に、データベースをシャット ダウンします:

    SQL> shutdown immediate;

    Database closed.

    Database dismounted.

    ORACLE instance shut down.

次に、スタートアップ マウント コマンドを次のように実行します。

    SQL> startup nomount pfile='c:\OracleDatabase\product\11.2.0\dbhome_1\database\initTEST.ora';
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes

次に、spfile を作成します。

    SQL>create spfile frompfile='c:\OracleDatabase\product\11.2.0\dbhome\_1\database\initTEST.ora';

    File created.

次に、データベースをシャット ダウンします:

    SQL> shutdown immediate;

    ORA-01507: database not mounted

次に、インスタンスを起動するスタートアップ コマンドを使用します。

    SQL> startup;
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes
    Database mounted.
    Database opened.

## <a name="create-a-physical-standby-database"></a>物理スタンバイデータベースの作成
このセクションでは、物理スタンバイ データベースを準備するために Machine2 で実行しなくてはならない手順について説明します。

まず、Azure クラシック ポータルを使用して Machine2 にリモート デスクトップをポイントする必要があります。

次に、スタンバイ サーバー (Machine2) 上には C:\\\<YourLocalFolder\>\\TEST などのスタンバイ データベースのために必要なすべてのフォルダーを作成します。 このチュートリアルに従いながら、controlfile、datafiles, redologfiles、udump、bdump cdump ファイルなどのすべての必要なファイルを保持するために、ファイル構造が Machine1 上のフォルダー構造と一致していることを確認します。 さらに、Machine2 で ORACLE\_HOME および ORACLE\_BASE 環境変数を定義します。 そうでない場合は環境変数ダイアログ ボックスを使用して環境変数として定義します。 このダイアログ ボックスにアクセスするには、**コントロール パネル**の [システム] アイコンをダブルクリックして**システム** ユーティリティを開始します。次に **[詳細設定]** タブをクリックし、**[環境変数]** を選択します。 環境変数を設定するには、**[システム変数]** で **[新規]** をクリックします。 環境変数を設定した後に、既存の Windows コマンド プロンプトを閉じ、新しいものを開いて変更を確認します。

次に、次の手順に従います。

1. スタンバイ データベースの初期化パラメーターのファイルを準備
2. プライマリおよびスタンバイのマシンでデータベースをサポートするためのリスナーと tnsnames の構成
   
   1. 両方のデータベースのエントリを保持するために両方のサーバーで listener.ora を構成
   2. プライマリおよびスタンバイの両方のデータベースのエントリを保持するために、プライマリおよびスタンバイの Virtual Machines で tnsnames.ora を構成
   3. リスナーを開始し、両方のサービスの両方の Virtual Machines で tnsping を確認します。
3. マウント状態でスタンバイ インスタンスを起動
4. RMAN を使用して、データベースのクローンとスタンバイ データベースを作成
5. 物理スタンバイ データベースを管理回復モードで起動
6. 物理スタンバイデータベースの検証

### <a name="1-prepare-an-initialization-parameter-file-for-standby-database"></a>1.スタンバイ データベースの初期化パラメーターのファイルを準備
このセクションでは、初期化パラメーターのファイルのスタンバイ データベースを準備する方法を示します。 これには、まず INITTEST.ORA を Machine 1 から Machine2 へ手動でコピーします。 両方のマシンの %ORACLE\_HOME%\\database フォルダーで、INITTEST.ORA ファイルが表示されている必要があります。 次に、スタンバイ ロールを以下のように設定するには、Machine2 で INITTEST.ora ファイルを変更します。

    db_name='TEST'
    db_unique_name='TEST_STBY'
    db_create_file_dest='c:\OracleDatabase\oradata\test_stby’
    db_file_name_convert=’TEST’,’TEST_STBY’
    log_file_name_convert='TEST','TEST_STBY'


    job_queue_processes=10
    LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
    LOG_ARCHIVE_DEST_1='LOCATION=c:\OracleDatabase\TEST_STBY\archives VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=’TEST'
    LOG_ARCHIVE_DEST_2='SERVICE=TEST LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE)
    LOG_ARCHIVE_DEST_STATE_1='ENABLE'
    LOG_ARCHIVE_DEST_STATE_2='ENABLE'
    LOG_ARCHIVE_FORMAT='%t_%s_%r.arc'
    LOG_ARCHIVE_MAX_PROCESSES=30


前のステートメント ブロックには、2 つの重要なセットアップ項目が含まれています。

* **\*.LOG_ARCHIVE_DEST_1:** Machine2 で手動で c:\OracleDatabase\TEST_STBY\archives フォルダーを作成する必要があります。
* **\*.LOG_ARCHIVE_DEST_2:** これは省略可能な手順です。 この設定が必要になるため、プライマリ マシンがメンテナンス中の場合にスタンバイマシンがプライマリ データベースになる場合に必要となる場合があるため、これを設定します。

次に、スタンバイインスタンスを開始する必要があります。 スタンバイデータベースサーバーで、Windows サービスを作成することにより Oracle インスタンスを作成するために Windows コマンド プロンプトで次のコマンドを入力します。

    oradim -NEW -SID TEST\_STBY -STARTMODE MANUAL

**Oradim** コマンドは Oracle インスタンスを作成しますが、開始しません。 これは C:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\BIN ディレクトリにあります。

## <a name="configure-the-listener-and-tnsnames-to-support-the-database-on-primary-and-standby-machines"></a>プライマリおよびスタンバイのマシンでデータベースをサポートするためのリスナーと tnsnames の構成
スタンバイ データベースを作成する際、構成内のプライマリ データベースとスタンバイ データベースが互いに通信しあえることを確認する必要があります。 この場合は、手動でまたはネットワーク構成ユーティリティ NETCA を使用してリスナーと TNSNames の両方を構成する必要があります。 Recovery Manager ユーティリティ (RMAN) を使用する場合、これは必須のタスクです。

### <a name="configure-listenerora-on-both-servers-to-hold-entries-for-both-databases"></a>両方のデータベースのエントリを保持するために両方のサーバーで listener.ora を構成
リモート デスクトップから Machine1 へ、および以下で指定した listener.ora ファイルを編集します。 listener.ora ファイルを編集するときには、常にかっこひらきと閉じかっこが同じ列に並んでいることを確認します。 listener.ora ファイルは c:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\NETWORK\\ADMIN\\ にあります。

    # listener.ora Network Configuration File: C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\listener.ora

    # Generated by Oracle configuration tools.

    SID_LIST_LISTENER =
      (SID_LIST =
        (SID_DESC =
          (SID_NAME = test)
          (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
          (PROGRAM = extproc)
          (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
        )
      )

    LISTENER =
      (DESCRIPTION_LIST =
        (DESCRIPTION =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
          (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
        )
      )

次に、リモート デスクトップから Machine 2 へ、および次のように listener.ora ファイルを編集します。

    # listener.ora Network Configuration File: C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\listener.ora

    # Generated by Oracle configuration tools.

    SID_LIST_LISTENER =
      (SID_LIST =
        (SID_DESC =
          (SID_NAME = test_stby)
          (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
          (PROGRAM = extproc)
          (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
        )
      )

    LISTENER =
      (DESCRIPTION_LIST =
        (DESCRIPTION =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
          (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
        )
      )


### <a name="configure-tnsnamesora-on-the-primary-and-standby-virtual-machines-to-hold-entries-for-both-primary-and-standby-databases"></a>プライマリおよびスタンバイの両方のデータベースのエントリを保持するために、プライマリおよびスタンバイの Virtual Machines で tnsnames.ora を構成
リモート デスクトップから Machine1 へ、および以下で指定した tnsnames.ora ファイルを編集します。 tnsnames.ora ファイルは c:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\NETWORK\\ADMIN\\ にあります。

    TEST =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test)
        )
      )

    TEST_STBY =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test_stby)
        )
      )

リモート デスクトップから Machine 2 へ、および次のように tnsnames.ora ファイルを編集します:

    TEST =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test)
        )
      )

    TEST_STBY =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test_stby)
        )
      )


### <a name="start-the-listener-and-check-tnsping-on-both-virtual-machines-to-both-services"></a>リスナーを開始し、両方のサービスの両方の Virtual Machines で tnsping を確認します。
プライマリおよびスタンバイの両方の Virtual Machines で、新しい Windows のコマンド プロンプトを開き、次のステートメントを実行します。

    C:\Users\DBAdmin>tnsping test

    TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:08
    Copyright (c) 1997, 2010, Oracle.  All rights reserved.
    Used parameter files:
    C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
    Used TNSNAMES adapter to resolve the alias
    Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))) (CONNECT_DATA = (SER
    VICE_NAME = test)))
    OK (0 msec)


    C:\Users\DBAdmin>tnsping test_stby

    TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:16
    Copyright (c) 1997, 2010, Oracle.  All rights reserved.
    Used parameter files:
    C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
    Used TNSNAMES adapter to resolve the alias
    Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))) (CONNECT_DATA = (SER
    VICE_NAME = test_stby)))
    OK (260 msec)


## <a name="start-up-the-standby-instance-in-nomount-state"></a>マウント状態でスタンバイ インスタンスを起動
スタンバイ仮想マシン (MACHINE2) でスタンバイ データベースをサポートできるように環境を設定するには、

まず、パスワード ファイルを、プライマリ コンピューター (Machine1) からスタンバイ マシン (machine 2) に手動でコピーします。 これは **sys** パスワードは両方のコンピューターで同じである必要があるため必要となるものです。

次に、Machine2 で、Windows コマンド プロンプトを開き、次のようにスタンバイ データベースをポイントするよう環境変数を設定します。

    SET ORACLE_HOME=C:\OracleDatabase\product\11.2.0\dbhome_1
    SET ORACLE_SID=TEST_STBY

次に、nomount 状態でスタンバイ データベースを起動して spfile を生成します。

データベースを開始します:

    SQL>shutdown immediate;

    SQL>startup nomount
    ORACLE instance started.

    Total System Global Area  747417600 bytes
    Fixed Size                  2179496 bytes
    Variable Size             473960024 bytes
    Database Buffers          264241152 bytes
    Redo Buffers                7036928 bytes


## <a name="use-rman-to-clone-the-database-and-to-create-a-standby-database"></a>RMAN を使用して、データベースのクローンとスタンバイ データベースを作成
Recovery Manager ユーティリティ (RMAN) を使用して、物理スタンバイ データベースを作成するために、プライマリ データベースの任意のバックアップ コピーを取ることができます。

リモートデスクトップからスタンバイ仮想マシン (MACHINE2) へ、および TARGET (プライマリ データベース、 Machine1) および AUXILLARY (スタンバイ データベース, Machine2) インスタンスの両方に完全な接続文字列を指定することで RMAN ユーティリティを実行します。

> [!IMPORTANT]
> スタンバイ サーバーマシンにはデータベースがまだないため、オペレーティング システムの認証は使用しないでください。
> 
> 

    C:\> RMAN TARGET sys/password@test AUXILIARY sys/password@test_STBY

    RMAN>DUPLICATE TARGET DATABASE
      FOR STANDBY
      FROM ACTIVE DATABASE
      DORECOVER
        NOFILENAMECHECK;

## <a name="start-the-physical-standby-database-in-managed-recovery-mode"></a>物理スタンバイ データベースを管理回復モードで起動
このチュートリアルでは、物理スタンバイデータベースを作成する方法について説明します。 論理スタンバイ データベースを作成する方法の詳細については、Oracle 文書を参照してください。

SQL\*Plus コマンド プロンプトを開き、次のように、スタンバイの仮想マシンまたはサーバー (MACHINE2) 上で Data Guard を有効にします。

    SHUTDOWN IMMEDIATE;
    STARTUP MOUNT;
    ALTER DATABASE RECOVER MANAGED STANDBY DATABASE DISCONNECT FROM SESSION;

スタンバイ データベースを **MOUNT** モードで開いた時、アーカイブ ログ配布は続行され、管理されている回復プロセスはスタンバイ データベースに適用するログを続行します。 これにより、スタンバイ データベースをプライマリ データベースともに最新の状態に保ちます。 スタンバイ データベースはこの期間にレポートの目的ではアクセスできないことに注意してください。

**READ ONLY** モードでプライマリ データベースを開いた時、アーカイブ ログ配布は続行されます。 ただし、管理されている回復プロセスは停止されます。 これにより、管理されている回復プロセスが再開されるまで、スタンバイデータベースはどんどん古くなっていきます。 この期間中には、レポート目的でスタンバイ データベースにアクセスできますが、データには最新の変更が反映されない場合があります。

一般的には、万一プライマリ データベースに不具合が発生した場合に、データをスタンバイ データベースで最新に保つため、スタンバイ データベースは **マウント** モードで保持することをお勧めします。 ただし、アプリケーションの要件によっては、スタンバイデータベースは **READ ONLY** モードでレポート目的で保持できます。 次の手順では、SQL\*Plus を使用して、読み取り専用モードでの Data Guard を有効にする方法を示します。

    SHUTDOWN IMMEDIATE;
    STARTUP MOUNT;
    ALTER DATABASE OPEN READ ONLY;


## <a name="verify-the-physical-standby-database"></a>物理スタンバイデータベースの検証
このセクションでは、管理者として高可用性構成を確認する方法を示します。

SQL\*Plus コマンド プロンプト ウィンドウを開き、スタンバイ仮想マシン (Machine2) でアーカイブされた redo ログを確認します。

    SQL> show parameters db_unique_name;

    NAME                                TYPE       VALUE
    ------------------------------------ ----------- ------------------------------
    db_unique_name                      string     TEST_STBY

    SQL> SELECT NAME FROM V$DATABASE

    SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

    SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
    ----------------  ---------------  --------------- ------------
    45                    23-FEB-14   23-FEB-14   YES
    45                    23-FEB-14   23-FEB-14   NO
    46                    23-FEB-14   23-FEB-14   NO
    46                    23-FEB-14   23-FEB-14   YES
    47                    23-FEB-14   23-FEB-14   NO
    47                    23-FEB-14   23-FEB-14   NO

SQL * Plus コマンド プロンプト ウィンドウを開き、プライマリマシン (Machine1) でログファイルを切り替えます。

    SQL> alter system switch logfile;
    System altered.

    SQL> archive log list
    Database log mode              Archive Mode
    Automatic archival             Enabled
    Archive destination            C:\OracleDatabase\archive
    Oldest online log sequence     69
    Next log sequence to archive   71
    Current log sequence           71

アーカイブ済みの redo ログ、スタンバイ仮想マシンで (マシン 2) を確認します。

    SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

    SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
    ----------------  ---------------  --------------- ------------
    45                    23-FEB-14   23-FEB-14   YES
    46                    23-FEB-14   23-FEB-14   YES
    47                    23-FEB-14   23-FEB-14   YES
    48                    23-FEB-14   23-FEB-14   YES

    49                    23-FEB-14   23-FEB-14   YES
    50                    23-FEB-14   23-FEB-14   IN-MEMORY

スタンバイ仮想マシンで (マシン 2) ギャップを確認します。

    SQL> SELECT * FROM V$ARCHIVE_GAP;
    no rows selected.

別の確認方法には、スタンバイ データベースへのフェールオーバーと、可能な場合にプライマリ データベースへのフェールバックをテストします。 プライマリ データベースとしてスタンバイ データベースを有効にするには、次のステートメントを使用します:

    SQL> ALTER DATABASE RECOVER MANAGED STANDBY DATABASE FINISH;
    SQL> ALTER DATABASE ACTIVATE STANDBY DATABASE;

元のプライマリ データベースで後記を有効にしていない場合、元のプライマリ データベースを削除して、スタンバイ データベースとして再作成することが推奨されます。

プライマリ データベースと、スタンバイ データベースでフラッシュバックデータベースを有効にすることをお勧めします。 フェールオーバーが発生した場合、プライマリ データベースはフェールオーバーの前にフラッシュバックされ、スタンバイ データベースにすばやく変換されます。

## <a name="additional-resources"></a>その他のリソース
[Azure の Oracle 仮想マシン イメージ](oracle-images.md)


