---
title: PostgreSQL を Linux VM にセットアップする
description: Azure の Linux 仮想マシンに PostgreSQL をインストールして構成する方法を説明します。
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: 321f6bd6324613967001139e365d96521217d50b
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87267253"
---
# <a name="install-and-configure-postgresql-on-azure"></a>Azure での PostgreSQL のインストールと構成
PostgreSQL は、Oracle や DB2 に似た高機能のオープン ソース データベースです。 PostgreSQL には、完全な ACID 準拠、信頼性の高いトランザクション処理、複数バージョンのコンカレンシー制御など、エンタープライズ対応の機能が含まれます。 また、ANSI SQL や SQL/MED などの標準をサポートします (Oracle、MySQL、MongoDB、その他多くの外部データ ラッパーを含みます). 12 を超える手続き型言語、GIN および GIST のインデックス、空間データ、および JSON またはキーと値に基づくアプリケーションに対する NoSQL に似た複数の機能などのサポートにより、高度な拡張を行えます。

この記事では、Linux を実行している Azure Virtual Machine に PostgreSQL をインストールして構成する方法を説明します。


## <a name="install-postgresql"></a>PostgreSQL のインストール
> [!NOTE]
> このチュートリアルを実行するには、Linux を実行する Azure Virtual Machines が既に存在している必要があります。 続行する前に、[Azure Linux VM チュートリアル](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を見て Linux VM を作成およびセットアップしてください。
> 
> 

この例では、PostgreSQL ポートとしてポート 1999 を使用します。  

PuTTY を使用して作成した Linux VM に接続します。 Azure Linux VM を初めて使用する場合は、「[Azure 上の Linux における SSH の使用方法](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照し、PuTTY を使用して Linux VM に接続する方法を確認してください。

1. 次のコマンドを実行して、ルート (admin) に切り替えます。

    ```console
    # sudo su -
    ```

2. 一部のディストリビューションには、PostgreSQL をインストールする前にインストールする必要がある依存アプリケーションがあります。 次の一覧でお使いのディストリビューションを確認し、適切なコマンドを実行してください。
   
   * Red Hat ベースの Linux:

        ```console
        # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y
        ```

   * Debian ベースの Linux:

        ```console
        # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y
        ```

   * Suse Linux:

        ```console
        # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y
        ```

3. PostgreSQL をルート ディレクトリにダウンロードし、パッケージを解凍します。

    ```console
    # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/

    # tar jxvf  postgresql-9.3.5.tar.bz2
    ```

    上に示したのは例です。 [Index of /pub/source/](https://ftp.postgresql.org/pub/source/)で、詳細なダウンロード アドレスを参照できます。

4. ビルドを開始するには、以下のコマンドを実行します。

    ```console
    # cd postgresql-9.3.5

    # ./configure --prefix=/opt/postgresql-9.3.5
    ```

5. ドキュメント (HTML およびマニュアル ページ) や追加モジュール (`contrib`) など、ビルドできるものをすべてビルドする場合は、代わりに次のコマンドを実行します。

    ```console
    # gmake install-world
    ```

    次の確認メッセージが表示されます。

    ```output
    PostgreSQL, contrib, and documentation successfully made. Ready to install.
    ```

## <a name="configure-postgresql"></a>PostgreSQL の構成
1. (省略可能) シンボリック リンクを作成して、バージョン番号を含まないように PostgreSQL の参照を短縮します。

    ```console
    # ln -s /opt/postgresql-9.3.5 /opt/pgsql
    ```

2. データベース用のディレクトリを作成します。

    ```console
    # mkdir -p /opt/pgsql_data
    ```

3. root 以外のユーザーを作成し、そのユーザーのプロファイルを変更します。 この新しいユーザーに切り替えます (この例では *postgres* )。

    ```console
    # useradd postgres
   
    # chown -R postgres.postgres /opt/pgsql_data
   
    # su - postgres
    ```
   
   > [!NOTE]
   > セキュリティ上の理由から、PostgreSQL ではデータベースの初期化、開始、またはシャットダウンに root 以外のユーザーを使用します。
   > 
   > 
4. 次のコマンドを入力して、*bash_profile* ファイルを編集します。 以下の行を *bash_profile* ファイルの最後に追加します。

    ```config
    cat >> ~/.bash_profile <<EOF
    export PGPORT=1999
    export PGDATA=/opt/pgsql_data
    export LANG=en_US.utf8
    export PGHOME=/opt/pgsql
    export PATH=\$PATH:\$PGHOME/bin
    export MANPATH=\$MANPATH:\$PGHOME/share/man
    export DATA=`date +"%Y%m%d%H%M"`
    export PGUSER=postgres
    alias rm='rm -i'
    alias ll='ls -lh'
    EOF
    ```

5. *bash_profile* ファイルを実行します。

    ```console
    $ source .bash_profile
    ```

6. 次のコマンドを使用してインストールを検証します。

    ```console
    $ which psql
    ```

    インストールが成功した場合は、次の応答が表示されます。

    ```output
    /opt/pgsql/bin/psql
    ```

7. PostgreSQL のバージョンをチェックすることもできます。

    ```sql
    $ psql -V
    ```

8. ディスクを初期化します。

    ```console
    $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
    ```

    次の出力が表示されます。

![image](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>PostgreSQL のセットアップ
<!--    [postgres@ test ~]$ exit -->

次のコマンドを実行します。

```console
# cd /root/postgresql-9.3.5/contrib/start-scripts

# cp linux /etc/init.d/postgresql
```

/etc/init.d/postgresql ファイルの 2 つの変数を変更します。 prefix には、PostgreSQL のインストール パス **/opt/pgsql**を設定します。 PGDATA には、PostgreSQL のデータ ストレージ パス **/opt/pgsql_data** を設定します。

```config
# sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

# sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql
```

![image](./media/postgresql-install/no2.png)

実行可能なようにファイルを変更します。

```console
# chmod +x /etc/init.d/postgresql
```

PostgreSQL を開始します。

```console
# /etc/init.d/postgresql start
```

PostgreSQL のエンドポイントがオンかどうかを確認します。

```console
# netstat -tunlp|grep 1999
```

次の出力が表示されます。

![image](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Postgres データベースへの接続
postgres ユーザーに再び切り替えます。

```console
# su - postgres
```

Postgres データベースを作成します。

```console
$ createdb events
```

先に作成したイベント データベースに接続します。

```console
$ psql -d events
```

## <a name="create-and-delete-a-postgres-table"></a>Postgres テーブルの作成および削除
データベースに接続しているため、そこにテーブルを作成できます。

たとえば、サンプルの Postgres テーブルを新しく作成するには、次のコマンドを実行します。

```sql
CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);
```

次の列名と制限で 4 列のテーブルを設定しました。

1. “name” 列は、VARCHAR コマンドによって、20 文字以下に制限されています。
2. "food" 列は各自が持ってくる食品アイテムを示します。 VARCHAR によってこのテキストは 30 文字以下に制限されます。
3. “confirmed” 列は、ユーザーが料理に対する RSVP を持っているかどうかを記録します。 許容される値は、"Y" と "N" です。
4. “date” 列は、イベントにサインアップした日付を示します。 Postgres では、日付を yyyy-mm-dd と記述する必要があります。

テーブルが正常に作成された場合、次が表示されます。

![image](./media/postgresql-install/no4.png)

次のコマンドを使用して、テーブルの構造をチェックすることもできます。

![image](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>テーブルにデータを追加する
最初に、行に情報を挿入します。

```sql
INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');
```

次のように出力されます。

![image](./media/postgresql-install/no6.png)

テーブルにさらに人を追加することもできます。 オプションをいくつか次に示します。独自に作成することもできます。

```sql
INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');
```

### <a name="show-tables"></a>テーブルの表示
テーブルを表示するには、次のコマンドを使用します。

```sql
select * from potluck;
```

出力は次のようになります。

![image](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>テーブルのデータの削除
テーブル内のデータを削除するには、次のコマンドを使用します。

```sql
delete from potluck where name=’John’;
```

これにより、"John" 列内のすべての情報が削除されます。 出力は次のようになります。

![image](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>テーブルのデータの更新
テーブル内のデータを更新するには、次のコマンドを使用します。 この場合、Sandy が参加を確定したため、RSVP を "N" から "Y" に変更します。

```sql
UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';
```

## <a name="get-more-information-about-postgresql"></a>PostgreSQL の詳細情報
PostgreSQL を Azure Linux VM にインストールする作業が完了したため、Azure でそれを使用できるようになりました。 PostgreSQL についてさらに学習するには、 [PostgreSQL の Web サイト](https://www.postgresql.org/)にアクセスしてください。
