---
title: "VM で Oracle GoldenGate を構成する | Microsoft Docs"
description: "高可用性と障害復旧のために Oracle GoldenGate を Azure VM にセットアップして実装するチュートリアルの手順。"
services: virtual-machines-windows
author: rickstercdn
manager: timlt
documentationcenter: 
tags: azure-service-management
ms.assetid: 85ca238c-5925-4c5a-86c7-08338587b8c3
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/06/2016
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 8b6a5e98cdc8da49e6387287a5a8d55b368a25c6
ms.lasthandoff: 03/27/2017


---
# <a name="configuring-oracle-goldengate-for-azure"></a>Azure 用の Oracle GoldenGate の構成
このチュートリアルでは、高可用性と障害復旧のために Azure Virtual Machines 環境用に Oracle GoldenGate をセットアップする方法について説明します。 このチュートリアルでは、RAC 以外の Oracle データベースに対する [双方向レプリケーション](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_about_gg.htm) に重点を合わせており、両サイトがアクティブである必要があります。

Oracle GoldenGate は、データ分布とデータ統合をサポートします。 これにより Oracle 間レプリケーション構成を使用して、データ分布とデータ同期ソリューションをセットアップでき、柔軟な高可用性ソリューションが得られます。 Oracle GoldenGate は、エンタープライズ全体の情報の分布とゼロダウンタイムのアップグレードと移行を有効にするレプリケーション機能を備えた Oracle Data Guard を補足します。 詳細については、「 [Oracle Data Guard での Oracle GoldenGate の使用](http://docs.oracle.com/cd/E11882_01/server.112/e17157/unplanned.htm)」をご覧ください。

Oracle GoldenGate に含まれる主なコンポーネントには、抽出、データ ポンプ、レプリケート、証跡または抽出ファイル、チェックポイント、マネージャー、コレクターがあります。 2 つのサイト間で双方向レプリケーションを使用するには、両サイトですべてのコンポーネントを作成して開始する必要があります。 Oracle GoldenGate アーキテクチャの詳細については、「 [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html)」のガイドをご覧ください。

このチュートリアルでは、Oracle データベースの高可用性と障害復旧の概念と、 [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html)に関する理論的かつ実用的な知識があることを前提としています。 詳細については、 [Oracle の Web サイト](http://www.oracle.com/technetwork/database/features/availability/index.html)をご覧ください。

また、このチュートリアルでは、次の前提条件が既に実装されていることを前提としています。

* 「[Oracle 仮想マシンのイメージに関するその他の考慮事項](oracle-considerations.md)」の「高可用性と障害復旧に関する考慮」を既に確認していること。 Azure では、スタンドアロンの Oracle Database インスタンスをサポートしていますが、Oracle Real Application Cluster (Oracle RAC) をサポートしていないことにご注意ください。
* [Oracle ダウンロード](http://www.oracle.com/us/downloads/index.html) Web サイトから Oracle GoldenGate ソフトウェアを既にダウンロードしている。 製品パックとして、Oracle Fusion Middleware – Data Integration を選択している。 その後、Oracle 11g データベース用の Oracle GoldenGate on Oracle v11.2.1 Media Pack for Microsoft Windows x64 (64-bit) を選択している。 次に、Oracle GoldenGate V11.2.1.0.3 for Oracle 11g 64bit on Windows 2008 (64bit) をダウンロードします。
* Windows Server 上の Oracle Enterprise Edition を使用して、Azure に 2 つの Virtual Machines (VM) を作成していること。 Virtual Machines が永続的なプライベート IP アドレスを介して相互にアクセスできるように、[同じクラウド サービス](../../virtual-machines-linux-load-balance.md)にあり、同じ [Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) にあることを確認します。
* Azure クラシック ポータルで、サイト A に "MachineGG1"、サイト B に "MachineGG2" という仮想マシン名を設定している。
* サイト A には "TestGG1"、サイト B には "TestGG2" というテスト データベースを作成している。
* Windows サーバーに、Administrators グループのメンバーまたは **ORA_DBA** グループのメンバーとしてログオンしていること。

このチュートリアルでは、次のことについて説明します。

1. サイト A とサイト B でデータベースをセットアップする  
   
   1. 初期データ ロードを実行する
2. データベース レプリケーション用にサイト A とサイト B を準備する
3. DDL レプリケーションのサポートに必要なすべてのオブジェクトを作成する 
4. サイト A とサイト B で GoldenGate マネージャーを構成する
5. サイト A とサイト B で抽出グループとデータ ポンプ プロセスを作成する
   
   1. サイト A で抽出とデータ ポンプ プロセスを作成する
   2. サイト B で GoldenGate チェックポイント テーブルを作成する
   3. サイト B で REPLICAT を追加する
   4. サイト B で抽出とデータ ポンプ プロセスを作成する
   5. サイト A で GoldenGate チェックポイント テーブルを作成する
   6. サイト A に REPLICAT を追加する
   7. サイト A とサイト B で trandata を追加する
   8. サイト A で抽出とデータ ポンプ プロセスを開始する
   9. サイト B で抽出とデータ ポンプ プロセスを開始する
   10. サイト A で REPLICAT プロセスを開始する
   11. サイト B で REPLICAT プロセスを開始する
6. 双方向のレプリケーション プロセスを確認する

> [!IMPORTANT]
> このチュートリアルは、次のソフトウェア構成に対してセットアップ、テストされています。
> 
> |  | **サイト A のデータベース** | **サイト B のデータベース** |
> | --- | --- | --- |
> | **Oracle リリース** |Oracle11g リリース 2 – (11.2.0.1) |Oracle11g リリース 2 – (11.2.0.1) |
> | **マシン名** |MachineGG1 |MachineGG2 |
> | **オペレーティング システム** |Windows 2008 R2 |Windows 2008 R2 |
> | **Oracle SID** |TESTGG1 |TESTGG2 |
> | **レプリケーション スキーマ** |SCOTT |SCOTT |
> 
> 

Oracle データベースと Oracle GoldenGate の今後のリリースでは、追加の変更を実装する必要が生じる可能性があります。 最新バージョンに固有の情報については、Oracle Web サイトの [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) と [Oracle データベース](http://www.oracle.com/us/corporate/features/database-12c/index.html)に関するドキュメントをご覧ください。 たとえば、リリース 11.2.0.4 以降のソース データベースの場合、DDL のキャプチャはログマイニング サーバーによって非同期的に実行され、特別なトリガー、テーブル、その他のデータベース オブジェクトをインストールする必要はありません。 Oracle GoldenGate のアップグレードは、ユーザー アプリケーションを停止しなくても実行できます。 DDL トリガーとサポート オブジェクトを使用する必要があるのは、抽出が統合モードで、Oracle 11g ソース データベースがバージョン 11.2.0.4 より前の場合です。 詳細については、「 [Installing and Configuring Oracle GoldenGate for Oracle Database (Oracle データベース用の Oracle GoldenGate のインストールと構成)](http://docs.oracle.com/goldengate/1212/gg-winux/GIORA.pdf)」をご覧ください。

## <a name="1-setup-database-on-site-a-and-site-b"></a>1.サイト A とサイト B でデータベースをセットアップする
このセクションでは、サイト A とサイト B の両方でデータベース前提条件を実行する方法について説明します。このセクションのすべての手順をサイト A とサイト B の両方で実行する必要があります。

まず、Azure クラシック ポータルでリモート デスクトップを使用して、サイト A とサイト B に接続します。 Windows コマンド プロンプトを開き、Oracle GoldenGate セットアップ ファイルのホーム ディレクトリを作成します。

    mkdir C:\OracleGG

次に、このフォルダーに Oracle GoldenGate ソフトウェアを解凍してインストールします。 この手順の後、次のコマンドを実行することで、GoldenGate ソフトウェア コマンド インタープリター (GGSCI) を開始できます。

    C:\OracleGG\.\ggsci

[GGSCI](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_gettingstarted.htm) を使用すると、Oracle GoldenGate の構成、制御、監視を行ういくつかのコマンドを実行できます。

次に、以下のコマンドを実行して、インストール パッケージからすべてのサブフォルダーを作成します。

    GGSCI (Hostname) 1> CREATE SUBDIRS

以下のコマンドを実行して、GGSCI コマンド プロンプトを終了します。

    GGSCI (Hostname) 1> EXIT

次に、Oracle GoldenGate マネージャー、抽出、レプリケーション プロセスで使用されるデータベース ユーザーを作成する必要があります。 各プロセスの個々のユーザーを作成することも、1 人の共通ユーザーのみを構成することもできます。 このチュートリアルでは、ggate と呼ばれる 1 人のユーザーを作成します。 次に、そのユーザーに必要な特権を付与します。 サイト A とサイト B で次の操作を実行する必要があることにご注意ください。

次のような **SYSDBA** を使用して、データベース管理者特権でサイト A とサイト B で SQL\*Plus コマンド ウィンドウを開きます。

    Enter username: / as sysdba

次に、以下を実行します。

    SQL> create tablespace ggs_data   datafile 'c:\OracleDatabase\oradata\<DBNAME>\<DBNAME>ggs_data01.dbf' size 200m;
    SQL> create user ggate identified by ggate default tablespace ggs_data  temporary tablespace temp;
          grant connect, resource to ggate;
          grant select any dictionary, select any table to ggate;
          grant create table to ggate;
          grant flashback any table to ggate;
          grant execute on dbms_flashback to ggate;
          grant execute on utl_file to ggate;
          grant create any table to ggate;
          grant insert any table to ggate;
          grant update any table to ggate;
          grant delete any table to ggate;
          grant drop any table to ggate;

次に、サイト A とサイト B で %ORACLE\_HOME%\\database フォルダー内の INIT\<DatabaseSID\>.ORA ファイルを見つけて、以下のデータベース パラメーターを INITTEST.ora に追加します。

    UNDO\_MANAGEMENT=AUTO
    UNDO\_RETENTION=86400

すべての Oracle GoldenGate GGSCI コマンドの完全な一覧については、 [「Oracle GoldenGate for Windows のリファレンス](http://docs.oracle.com/goldengate/1212/gg-winux/GWURF/ggsci_commands.htm)」をご覧ください。

### <a name="perform-initial-data-load"></a>初期データ ロードを実行する
次のいくつかの方法で、データベースで初期データ ロードを実行できます。 たとえば、 [Oracle GoldenGate ダイレクト ロード](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_initsync.htm) や通常のエクスポートとインポート ユーティリティを使用して、サイト A からサイト B にテーブル データをエクスポートできます。

Oracle GoldenGate レプリケーション プロセスを説明するために、このチュートリアルでは以下のコマンドを使用して、サイト A とサイト B の両方でテーブルを作成する方法を示します。

まず、SQL\*Plus コマンド ウィンドウを開き、以下のコマンドを実行して、サイト A とサイト B のデータベースでインベントリ テーブルを作成します。

    create table scott.inventory
    (prod_id number,
    prod_category varchar2(20),
    qty_in_stock number,
    last_dml timestamp default systimestamp);

次に、サイト A とサイト B のデータベースで新しく作成されたテーブルに制約を追加します。

    alter table scott.inventory add constraint pk_inventory primary key (prod_id) ;

次に、サイト A とサイト B のユーザー ggate に新しいインベントリ テーブルに対するすべての特権を付与します。

    grant all on scott.inventory to ggate;

次に、新しく作成されたテーブルでデータベース トリガー INVENTORY_CDR_TRG を作成して有効にし、ユーザーが ggate でない場合に、新しいテーブルへのすべてのトランザクションが記録されることを確認します。 サイト A とサイト B でこの操作を実行します。

    CREATE OR REPLACE TRIGGER INVENTORY_CDR_TRG
    BEFORE UPDATE
    ON SCOTT.INVENTORY
    REFERENCING NEW AS New OLD AS Old
    FOR EACH ROW
    BEGIN
    IF SYS_CONTEXT ('USERENV', 'SESSION_USER') != 'GGATE'
    THEN
    :NEW.LAST_DML := SYSTIMESTAMP;
    END IF;
    END;
    /


## <a name="2-prepare-site-a-and-site-b-for-database-replication"></a>2.データベース レプリケーション用にサイト A とサイト B を準備する
このセクションでは、データベース レプリケーション用にサイト A とサイト B を準備する方法について説明します。 このセクションのすべての手順をサイト A とサイト B の両方で実行する必要があります。

まず、Azure クラシック ポータルでリモート デスクトップを使用して、サイト A とサイト B に接続します。 SQL*Plus コマンド ウィンドウを使用して、データベースを archivelog モードに切り替えます。

    sql>shutdown immediate
    sql>startup mount
    sql>alter database archivelog;
    sql>alter database open;


次に、以下のようにして [補足ログ](http://docs.oracle.com/cd/E11882_01/server.112/e22490/logminer.htm) を有効にします。

    SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;

次に、DDL (データ定義言語) レプリケーションをサポートするためにデータベースを準備します。

    SQL> alter system set recyclebin=off scope=spfile;

次に、データベースをシャットダウンして再起動します。

    sql>shutdown immediate
    sql>startup


## <a name="3-create-all-necessary-objects-to-support-ddl-replication"></a>3.DDL レプリケーションのサポートに必要なすべてのオブジェクトを作成する 
このセクションでは、DDL レプリケーションのサポートに必要なすべてのオブジェクトを作成する際に使用する必要があるスクリプトを一覧表示します。 サイト A とサイト B の両方で、このセクションで指定されているスクリプトを実行する必要があります。

Windows コマンド プロンプトを開き、C:\\OracleGG などの Oracle GoldenGate フォルダーに移動します。 サイト A とサイト B で **SYSDBA** などのデータベース管理者特権を使用して、SQL\*Plus コマンド プロンプトを起動します。

次に、以下のスクリプトを実行します。

    SQL> @marker_setup.sql  
    Enter GoldenGate schema name: ggate
    SQL> @ddl_setup.sql  
    Enter GoldenGate schema name: ggate
    SQL> @role_setup.sql
    Enter GoldenGate schema name: ggate
    SQL> grant ggs_ggsuser_role to ggate;
     Grant succeeded.
     SQL> @ddl_enable
     Trigger altered.
     SQL> @ddl_pin ggate


Oracle GoldenGate ツールには、DDL (データ定義言語) をサポートするためのテーブル レベルのログインが必要です。 これが、ADD TRANDATA コマンドを使用して、テーブル レベルで補足ログを有効にする理由です。 Oracle GoldenGate コマンド インタープリター ウィンドウを開き、データベースにログインしてから、以下の ADD TRANDATA コマンドを実行します。

    GGSCI 5> DBLOGIN USERID ggate, PASSWORD ggate

    GGSCI(Hostname) 6> add trandata scott.inventory

## <a name="4-configure-goldengate-manager-on-site-a-and-site-b"></a>4.サイト A とサイト B で GoldenGate マネージャーを構成する
Oracle GoldenGate マネージャーは、他の GoldenGate プロセスの開始、証跡ログ ファイルの管理とレポートのような多数の機能を実行します。

サイト A とサイト B の両方で Oracle GoldenGate マネージャー プロセスを構成する必要があります。そのためには、サイト A とサイト B で次の手順を実行します。

Windows コマンド ウィンドウを開き、Oracle GoldenGate コマンド インタープリターを開始します。

    cd C:\OracleGG\
    c:\OracleGG>ggsci
    Oracle GoldenGate Command Interpreter for Oracle
    Version 11.2.1.0.3 14400833 OGGCORE_11.2.1.0.3_PLATFORMS_120823.1258
    Windows x64 (optimized), Oracle 11g on Aug 23 2012 16:50:36
    Copyright (C) 1995, 2012, Oracle and/or its affiliates. All rights reserved.


データベースに影響を与えるコマンドを実行できるように、データベースへの GGSCI セッションのログインを行います。

    GGSCI (HostName) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

システム上のすべてのマネージャー、抽出、レプリケート プロセスに関する状態と遅延 (関係する場合) を表示します。

    GGSCI (HostName) 2> info all
    Program     Status      Group       Lag           Time Since Chkpt
    MANAGER     STOPPED

EDIT PARAMS コマンドを使用してパラメーター ファイルを開き、以下の情報を追加します。

    GGSCI (HostName) 3> edit params mgr
    PORT 7809
    USERID ggate, PASSWORD ggate
    PURGEOLDEXTRACTS  C:\OracleGG\dirdat\ex, USECHECKPOINTS

システム上のすべてのマネージャー、抽出、レプリケート プロセスに関する状態と遅延 (関係する場合) を表示します。

    GGSCI (HostName) 46> info all
    Program     Status      Group       Lag           Time Since Chkpt
    MANAGER     STOPPED

データベースに影響を与えるコマンドを実行できるように、データベースへの GGSCI セッションのログインを行います。

    GGSCI (HostName) 47> dblogin USERID ggate, PASSWORD ggate

    Successfully logged into database.

マネージャー プロセスを開始します。

    GGSCI (HostName) 48> start manager
    Manager started.

## <a name="5-create-extract-group-and-data-pump-processes-on-site-a-and-site-b"></a>5.サイト A とサイト B で抽出グループとデータ ポンプ プロセスを作成する
### <a name="create-extract-and-data-pump-processes-on-site-a"></a>サイト A で抽出とデータ ポンプ プロセスを作成する
サイト A とサイト B で抽出とデータ ポンプ プロセスを作成する必要があります。Azure クラシック ポータルでリモート デスクトップを使用して、サイト A とサイト B に接続します。 GGSCI コマンド インタープリター ウィンドウを開きます。 サイト A で次のコマンドを実行します。

    GGSCI (MachineGG1) 14> add extract ext1 tranlog begin now
    EXTRACT added.
    GGSCI (MachineGG1) 4> add exttrail C:\OracleGG\dirdat\lt, extract ext1
    EXTTRAIL added.
    GGSCI (MachineGG1) 16> add extract dpump1 exttrailsource C:\OracleGG\dirdat\aa
    EXTRACT added.
    GGSCI (MachineGG1) 17> add rmttrail C:\OracleGG\dirdat\ab extract dpump1
    RMTTRAIL added.

EDIT PARAMS コマンドを使用してパラメーター ファイルを開き、以下の情報を追加します。GGSCI (MachineGG1) 18> edit params ext1 EXTRACT ext1 USERID ggate, PASSWORD ggate EXTTRAIL C:\OracleGG\dirdat\aa TRANLOGOPTIONS EXCLUDEUSER ggate TABLE scott.inventory, GETBEFORECOLS ( ON UPDATE KEYINCLUDING (prod_category,qty_in_stock, last_dml), ON DELETE KEYINCLUDING (prod_category,qty_in_stock, last_dml));

EDIT PARAMS コマンドを使用してパラメーター ファイルを開き、以下の情報を追加します。

    GGSCI (MachineGG1) 15> edit params dpump1
    EXTRACT dpump1
     USERID ggate, PASSWORD ggate
     RMTHOST ActiveGG2orcldb, MGRPORT 7809, TCPBUFSIZE 100000
     RMTTRAIL C:\OracleGG\dirdat\ab
     PASSTHRU
     TABLE scott.inventory;

### <a name="create-a-goldengate-checkpoint-table-on-site-b"></a>サイト B で GoldenGate チェックポイント テーブルを作成する
次に、サイト B でチェックポイント テーブルを追加する必要があります。それには、GoldenGate コマンド インタープリター ウィンドウを開いて、以下を実行する必要があります。

    C:\OracleGG\ggsci
    GGSCI (MachineGG2) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

次に、データベースにチェックポイント テーブルを追加します。ここで、ggate は所有者を表します。

    GGSCI (MachineGG2) 2> ADD CHECKPOINTTABLE ggate.checkpointtable
    Successfully created checkpoint table ggate.checkpointtable.

この手順のサイト B である対象サーバー上の GLOBALS ファイルに、チェックポイント テーブルの名前を追加します。 サイト B で GLOBALS ファイルを編集します。

    GGSCI (MachineGG2) 1> EDIT PARAMS ./GLOBALS

次に、既存の GLOBALS ファイルに CHECKPOINTTABLE パラメーターを追加します。

    GGSCHEMA ggate
    CHECKPOINTTABLE ggate.checkpointtable

最後の手順として、GLOBALS パラメーター ファイルを保存して閉じます。

### <a name="add-replicat-on-site-b"></a>サイト B で REPLICAT を追加する
このセクションでは、サイト B に REPLICAT プロセス "REP2" を追加する方法について説明します。

ADD REPLICAT コマンドを使用して、サイト B でレプリケート グループを作成します。

    GGSCI (MachineGG2) 37> add replicat rep2 exttrail C:\OracleGG\dirdatab, checkpointtable ggate.checkpointtable

EDIT PARAMS コマンドを使用してパラメーター ファイルを開き、以下の情報を追加します。

    GGSCI (MachineGG2) 10> edit params rep2
    REPLICAT rep2
    ASSUMETARGETDEFS
    USERID ggate, PASSWORD ggate
    DISCARDFILE C:\OracleGG\dirdat\discard.txt, append,megabytes 10
    MAP scott.inventory, TARGET scott.inventory;

### <a name="create-extract-and-data-pump-processes-on-site-b"></a>サイト B で抽出とデータ ポンプ プロセスを作成する
このセクションでは、サイト B で新しい抽出プロセス "EXT2" と、新しいデータ ポンプ プロセス "DPUMP2" を作成する方法について説明します。

    GGSCI (MachineGG2) 3> add extract ext2 tranlog begin now
     EXTRACT added.
    GGSCI (MachineGG2) 4> add exttrail C:\OracleGG\dirdat\ac extract ext2
     EXTTRAIL added.
    GGSCI (MachineGG2) 5> add extract dpump2 exttrailsource C:\OracleGG\dirdat\ac
     EXTRACT added.
    GGSCI (MachineGG2) 6> add rmttrail C:\OracleGG\dirdat\ad extract dpump2
     RMTTRAIL added.

EDIT PARAMS コマンドを使用してパラメーター ファイルを開き、以下の情報を追加します。

    GGSCI (MachineGG2) 31> edit params ext2
    EXTRACT ext2
    USERID ggate, PASSWORD ggate
    EXTTRAIL C:\OracleGG\dirdat\ac
    TRANLOGOPTIONS EXCLUDEUSER ggate
    TABLE scott.inventory,
    GETBEFORECOLS (
    ON UPDATE KEYINCLUDING (prod_category,qty_in_stock, last_dml),
    ON DELETE KEYINCLUDING (prod_category,qty_in_stock, last_dml));

EDIT PARAMS コマンドを使用してパラメーター ファイルを開き、以下の情報を追加します。

    GGSCI (MachineGG2) 32> edit params dpump2
    EXTRACT dpump2
    USERID ggate, PASSWORD ggate
    RMTHOST MachineGG1, MGRPORT 7809, TCPBUFSIZE 100000
    RMTTRAIL C:\OracleGG\dirdat\ad
    PASSTHRU
    TABLE scott.inventory;

### <a name="create-a-goldengate-checkpoint-table-on-site-a"></a>サイト A で GoldenGate チェックポイント テーブルを作成する
Oracle GoldenGate コマンド インタープリター ウィンドウを開き、チェックポイント テーブルを作成します。

    GGSCI (MachineGG1) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

    GGSCI (MachineGG1) 2> ADD CHECKPOINTTABLE ggate.checkpointtable

    Successfully created checkpoint table ggate.checkpointtable.

また、サイト A で、GLOBALS ファイルにチェックポイント テーブルの名前を追加する必要があります。

Oracle GoldenGate コマンド インタープリター ウィンドウを開き、サイト A で GLOBALS ファイルを編集します。

    GGSCI (MachineGG1) 1> EDIT PARAMS ./GLOBALS
    Add the CHECKPOINTTABLE parameter to the existing GLOBALS file as follows:
    GGSCHEMA ggate
    CHECKPOINTTABLE ggate.checkpointtable

GLOBALS パラメーター ファイルを保存して閉じます。

### <a name="add-replicat-on-site-a"></a>サイト A に REPLICAT を追加する
このセクションでは、サイト A に REPLICAT プロセス "REP1" を追加する方法について説明します。

以下のコマンドは、証跡の名前と、関連付けられている checkpointtable を使用して、レプリケート グループ rep1 を作成します。

    GGSCI (MachineGG1) 21> add replicat rep1 exttrail C:\OracleGG\dirdat\ad,checkpointtable ggate.checkpointtable
     REPLICAT added.

EDIT PARAMS コマンドを使用してパラメーター ファイルを開き、以下の情報を追加します。

    GGSCI (MachineGG1) 10> edit params rep1
    REPLICAT rep1
    ASSUMETARGETDEFS
    USERID ggate, PASSWORD ggate
    DISCARDFILE C:\OracleGG\dirdat\discard.txt, append, megabytes 10
    MAP scott.inventory, TARGET scott.inventory;

### <a name="add-trandata-on-site-a-and-site-b"></a>サイト A とサイト B で trandata を追加する
ADD TRANDATA コマンドを使用して、テーブル レベルで補足ログを有効にします。 Oracle GoldenGate コマンド インタープリター ウィンドウを開き、データベースにログインしてから ADD TRANDATA コマンドを実行します。

リモート デスクトップを使用して MachineGG1 に接続し、Oracle GoldenGate コマンド インタープリターを開いて以下を実行します。

    GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    GGSCI (MachineGG1) 13> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

リモート デスクトップを使用して MachineGG2 に接続し、Oracle GoldenGate コマンド インタープリターを開いて以下を実行します。

    GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    Logging of supplemental redo data enabled for table SCOTT.INVENTORY.

テーブル レベルの補足ログの状態に関する情報を表示します。

    GGSCI (MachineGG2) 15> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

### <a name="add-trandata-on-site-a-and-site-b"></a>サイト A とサイト B で trandata を追加する
ADD TRANDATA コマンドを使用して、テーブル レベルで補足ログを有効にします。 Oracle GoldenGate コマンド インタープリター ウィンドウを開き、データベースにログインしてから ADD TRANDATA コマンドを実行します。

リモート デスクトップを使用して MachineGG1 に接続し、Oracle GoldenGate コマンド インタープリターを開いて以下を実行します。

    GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    GGSCI (MachineGG1) 13> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

リモート デスクトップを使用して MachineGG2 に接続し、Oracle GoldenGate コマンド インタープリターを開いて以下を実行します。

    GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    Logging of supplemental redo data enabled for table SCOTT.INVENTORY.
    Display information about the state of table-level supplemental logging:
    GGSCI (MachineGG2) 15> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

### <a name="start-extract-and-data-pump-processes-on-site-a"></a>サイト A で抽出とデータ ポンプ プロセスを開始する
サイト A で抽出プロセス ext1 を開始します。

    GGSCI (MachineGG1) 31> start extract ext1
    Sending START request to MANAGER …
    EXTRACT EXT1 starting

サイト A でデータ ポンプ プロセス dpump1 を開始します。

    GGSCI (MachineGG1) 23> start extract dpump1
    Sending START request to MANAGER …
    EXTRACT DPUMP1 starting
抽出グループ ext1 に関する情報を表示します。GGSCI (MachineGG1) 32> info extract ext1 EXTRACT    EXT1      Last Started 2013-11-25 08:03   Status RUNNING Checkpoint Lag       00:00:00 (updated 00:00:02 ago) Log Read Checkpoint  Oracle Redo Logs 2013-11-25 08:03:18  Seqno 6, RBA 3230720 SCN 0.1074371 (1074371)

システム上のすべてのマネージャー、抽出、レプリケート プロセスに関する状態と遅延 (関係する場合) を表示します。

    GGSCI (MachineGG1) 16> info all
    Program     Status      Group       Lag at Chkpt  Time Since Chkpt

    MANAGER     RUNNING
    EXTRACT     RUNNING     DPUMP1      00:00:00      00:46:33
    EXTRACT     RUNNING     EXT1        00:00:00      00:00:04

### <a name="start-extract-and-data-pump-processes-on-site-b"></a>サイト B で抽出とデータ ポンプ プロセスを開始する
サイト B で抽出プロセス ext2 を開始します。

    GGSCI (MachineGG2) 22> start extract ext2
    Sending START request to MANAGER …
    EXTRACT EXT2 starting

サイト B でデータ ポンプ プロセス dpump2 を開始します。

    GGSCI (MachineGG2) 23> start extract dpump2
    Sending START request to MANAGER …
    EXTRACT DPUMP2 starting

システム上のすべてのマネージャー、抽出、レプリケート プロセスに関する状態と遅延 (関係する場合) を表示します。

    GGSCI (ActiveGG2orcldb) 6> info all
    Program     Status      Group       Lag at Chkpt  Time Since Chkpt

    MANAGER     RUNNING
    EXTRACT     RUNNING     DPUMP2      00:00:00      136:13:33
    EXTRACT     RUNNING     EXT2        00:00:00      00:00:04

### <a name="start-replicat-process-on-site-a"></a>サイト A で REPLICAT プロセスを開始する
このセクションでは、サイト A で REPLICAT プロセス "REP1" を開始する方法について説明します。

サイト A でレプリケート プロセスを開始します。

    GGSCI (MachineGG1) 38> start replicat rep1
    Sending START request to MANAGER …
    REPLICAT REP1 starting

レプリケート グループの状態を表示します。

    GGSCI (MachineGG1) 39> status replicat rep1
     REPLICAT REP1: RUNNING

### <a name="start-replicat-process-on-site-b"></a>サイト B で REPLICAT プロセスを開始する
このセクションでは、サイト B で REPLICAT プロセス "REP2" を開始する方法について説明します。

サイト B でレプリケート プロセスを開始します。

    GGSCI (MachineGG2) 26> start replicat rep2
    Sending START request to MANAGER …
    REPLICAT REP2 starting

レプリケート グループの状態を表示します。

    GGSCI (MachineGG2) 27> status replicat rep2
    REPLICAT REP2: RUNNING

## <a name="6-verify-the-bi-directional-replication-process"></a>6.双方向のレプリケーション プロセスを確認する
Oracle GoldenGate 構成を確認するには、サイト A でデータベースに行を挿入します。リモート デスクトップを使用してサイト A に接続します。SQL*Plus コマンド ウィンドウを開いて、以下を実行します。SQL> select name from v$database; 

    NAME
    ———
    TESTGG

    SQL> insert into inventory values  (100,’TV’,100,sysdate);

    1 row created.

    SQL> commit;

    Commit complete.

次に、その行がサイト B にレプリケートされているかどうかを確認します。それには、リモート デスクトップを使用してサイト B に接続します。SQL Plus ウィンドウを開き、以下を実行します。

    SQL> select name from v$database;

    NAME
    ———
    TESTGG

    SQL> select * from inventory;

    PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
    ———- ——————– ———— ———
    100 TV 100 22-MAR-13

サイト B で新しいレコードを挿入します。

    SQL> insert into inventory  values  (101,’DVD’,10,sysdate);
    1 row created.

    SQL> commit;

    Commit complete.

リモート デスクトップを使用してサイト A に接続し、レプリケーションが行われたかどうかを確認します。

    SQL> select * from inventory;

    PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
    ———- ——————– ———— ———
    100 TV 100 22-MAR-13
    101 DVD 10 22-MAR-13

## <a name="additional-resources"></a>その他のリソース
[Azure の Oracle 仮想マシン イメージ](../../linux/classic/oracle-images.md)


