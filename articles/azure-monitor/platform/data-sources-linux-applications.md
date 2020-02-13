---
title: Azure Monitor での Linux アプリケーション パフォーマンスの収集 | Microsoft Docs
description: この記事では、MySQL および Apache HTTP Server のパフォーマンス カウンターを収集するように Linux 用 Log Analytics エージェントを構成するための詳細について説明します。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2017
ms.openlocfilehash: 75fd0453534e3a656bb1d8e2940b716dadfdf869
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75395847"
---
# <a name="collect-performance-counters-for-linux-applications-in-azure-monitor"></a>Azure Monitor で Linux アプリケーションのパフォーマンス カウンターを収集する 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]
この記事では、特定のアプリケーションのパフォーマンス カウンターを Azure Monitor に収集するように [Linux 用 Log Analytics エージェント](https://github.com/Microsoft/OMS-Agent-for-Linux)を構成することの詳細について説明します。  この記事に含まれるアプリケーションは次のとおりです。  

- [MySQL](#mysql)
- [Apache HTTP Server](#apache-http-server)

## <a name="mysql"></a>MySQL
Log Analytics エージェントがインストールされるときにコンピューター上に MySQL Server または MariaDB Server が検出された場合は、MySQL Server 用のパフォーマンス監視プロバイダーが自動的にインストールされます。 このプロバイダーは、パフォーマンスの統計情報を公開するためにローカルの MySQL/MariaDB サーバーに接続します。 プロバイダーが MySQL Server にアクセスできるように、MySQL ユーザーの資格情報が構成されている必要があります。

### <a name="configure-mysql-credentials"></a>MySQL の資格情報を構成する
MySQL OMI プロバイダーがパフォーマンスや正常性に関する情報を MySQL インスタンスに照会するためには、MySQL ユーザーが事前に構成され、MySQL クライアント ライブラリがインストールされている必要があります。  これらの資格情報は、Linux エージェント上に格納されている認証ファイルに格納されます。  認証ファイルは、MySQL インスタンスがどのようなバインド アドレスとポートをリッスンしているかや、メトリックを収集するためにどのような資格情報を使用するかを指定します。  

Linux 用 Log Analytics エージェントのインストール中に、MySQL OMI プロバイダーは MySQL の my.cnf 構成ファイル (既定の場所) をスキャンしてバインド アドレスとポートを検出し、MySQL OMI 認証ファイルを部分的に設定します。

MySQL 認証ファイルは `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth` に格納されています。


### <a name="authentication-file-format"></a>認証ファイルの形式
MySQL OMI 認証ファイルの形式を次に示します

    [Port]=[Bind-Address], [username], [Base64 encoded Password]
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    AutoUpdate=[true|false]

認証ファイル内のエントリを次の表で説明します。

| プロパティ | 説明 |
|:--|:--|
| Port | MySQL インスタンスがリッスンしている現在のポートを表します。 ポート 0 は、以降のプロパティが既定のインスタンスに使用されることを指定します。 |
| Bind-Address| 現在の MySQL バインド アドレス。 |
| username| MySQL Server インスタンスを監視するために使用される MySQL ユーザー。 |
| Base64 encoded Password| Base64 でエンコードされた MySQL 監視ユーザーのパスワード。 |
| AutoUpdate| my.cnf ファイル内の変更を再スキャンし、MySQL OMI プロバイダーがアップグレードされたときに MySQL OMI 認証ファイルを上書きするかどうかを指定します。 |

### <a name="default-instance"></a>既定のインスタンス
MySQL OMI 認証ファイルは、1 つの Linux ホスト上での複数の MySQL インスタンスの管理を容易にするために、既定のインスタンスとポート番号を定義できます。  既定のインスタンスは、ポート 0 のインスタンスによって示されます。 追加のインスタンスはすべて、別の値を指定しない限り、既定のインスタンスからプロパティのセットを継承します。 たとえば、ポート '3308' をリッスンしている MySQL インスタンスが追加された場合、3308 をリッスンしているインスタンスの試行および監視には既定のインスタンスのバインド アドレス、ユーザー名、および Base64 エンコード パスワードが使用されます。 3308 上のインスタンスが別のアドレスにバインドされ、同じ MySQL ユーザー名とパスワードのペアを使用する場合は、バインド アドレスのみが必要であり、その他のプロパティは継承されます。

次の表に、インスタンス設定の例を示します 

| 説明 | ファイル |
|:--|:--|
| 既定のインスタンスとポート 3308 のインスタンス。 | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| 既定のインスタンスとポート 3308 のインスタンス、および異なるユーザー名とパスワード。 | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>MySQL OMI 認証ファイル プログラム
MySQL OMI プロバイダーのインストールには、MySQL OMI 認証ファイルを編集するために使用できる MySQL OMI 認証ファイル プログラムが含まれています。 この認証ファイル プログラムは、次の場所にあります。

    /opt/microsoft/mysql-cimprov/bin/mycimprovauth

> [!NOTE]
> 資格情報ファイルの読み取りが omsagent アカウントに対して許可されている必要があります。 mycimprovauth コマンドは、omsgent として実行することをお勧めします。

次の表は、mycimprovauth を使用するための構文に関する詳細を示しています。

| 操作 | 例 | 説明
|:--|:--|:--|
| autoupdate *false または true* | mycimprovauth autoupdate false | 再起動または更新で認証ファイルが自動的に更新されるかどうかを設定します。 |
| default *bind-address username password* | mycimprovauth default 127.0.0.1 root pwd | MySQL OMI 認証ファイル内の既定のインスタンスを設定します。<br>パスワード フィールドは、プレーンテキストで入力してください。MySQL OMI 認証ファイル内のパスワードは Base 64 でエンコードされます。 |
| delete *default または port_num* | mycimprovauth 3308 | 既定値またはポート番号のどちらかで指定されたインスタンスを削除します。 |
| help | mycimprov help | 使用するコマンドの一覧を出力します。 |
| print | mycimprov print | 読みやすい MySQL OMI 認証ファイルを出力します。 |
| update port_num *bind-address username password* | mycimprov update 3307 127.0.0.1 root pwd | 指定されたインスタンスを更新するか、または存在しない場合はインスタンスを追加します。 |

次のコマンド例は、localhost 上の MySQL Server の既定のユーザー アカウントを定義します。  パスワード フィールドは、プレーンテキストで入力してください。MySQL OMI 認証ファイル内のパスワードは Base 64 でエンコードされます

    sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
    sudo /opt/omi/bin/service_control restart

### <a name="database-permissions-required-for-mysql-performance-counters"></a>MySQL のパフォーマンス カウンターに必要なデータベース権限
MySQL ユーザーが MySQL Server のパフォーマンス データを収集するには、次のクエリへのアクセス権が必要です。 

    SHOW GLOBAL STATUS;
    SHOW GLOBAL VARIABLES:


MySQL ユーザーにはまた、次の既定のテーブルへの SELECT アクセス権も必要です。

- information_schema
- mysql。 

これらの特権は、次の grant コマンドで付与できます。

    GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
    GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;


> [!NOTE]
> MySQL 監視ユーザーに権限を付与するには、付与するユーザーに 'GRANT option' 特権および付与される特権が必要です。

### <a name="define-performance-counters"></a>パフォーマンス カウンターを定義する

Azure Monitor にデータを送信するように Linux 用 Log Analytics エージェントを構成したら、収集するパフォーマンス カウンターを構成する必要があります。  次の表のカウンターには、[Azure Monitor での Windows および Linux パフォーマンス データ ソース](data-sources-performance-counters.md)に関するページの手順を使用します。

| オブジェクト名 | カウンター名 |
|:--|:--|
| MySQL データベース | Disk Space in Bytes |
| MySQL データベース | テーブル |
| MySQL サーバー | Aborted Connection Pct |
| MySQL サーバー | Connection Use Pct |
| MySQL サーバー | Disk Space Use in Bytes |
| MySQL サーバー | Full Table Scan Pct |
| MySQL サーバー | InnoDB Buffer Pool Hit Pct |
| MySQL サーバー | InnoDB Buffer Pool Use Pct |
| MySQL サーバー | InnoDB Buffer Pool Use Pct |
| MySQL サーバー | Key Cache Hit Pct |
| MySQL サーバー | Key Cache Use Pct |
| MySQL サーバー | Key Cache Write Pct |
| MySQL サーバー | Query Cache Hit Pct |
| MySQL サーバー | Query Cache Prunes Pct |
| MySQL サーバー | Query Cache Use Pct |
| MySQL サーバー | Table Cache Hit Pct |
| MySQL サーバー | Table Cache Use Pct |
| MySQL サーバー | Table Lock Contention Pct |

## <a name="apache-http-server"></a>Apache HTTP Server 
omsagent バンドルがインストールされるときにコンピューター上に Apache HTTP Server が検出された場合は、Apache HTTP Server 用のパフォーマンス監視プロバイダーが自動的にインストールされます。 このプロバイダーは、パフォーマンス データにアクセスするために Apache HTTP Server に読み込む必要のある Apache モジュールに依存しています。 このモジュールは、次のコマンドを使用して読み込むことができます。
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Apache 監視モジュールをアンロードするには、次のコマンドを実行します。
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>パフォーマンス カウンターを定義する

Azure Monitor にデータを送信するように Linux 用 Log Analytics エージェントを構成したら、収集するパフォーマンス カウンターを構成する必要があります。  次の表のカウンターには、[Azure Monitor での Windows および Linux パフォーマンス データ ソース](data-sources-performance-counters.md)に関するページの手順を使用します。

| オブジェクト名 | カウンター名 |
|:--|:--|
| Apache HTTP Server | Busy Workers |
| Apache HTTP Server | Idle Workers |
| Apache HTTP Server | Pct Busy Workers |
| Apache HTTP Server | Total Pct CPU |
| Apache Virtual Host | Errors per Minute - Client |
| Apache Virtual Host | Errors per Minute - Server |
| Apache Virtual Host | KB per Request |
| Apache Virtual Host | Requests KB per Second |
| Apache Virtual Host | Requests per Second |



## <a name="next-steps"></a>次のステップ
* Linux エージェントから[パフォーマンス カウンターを収集します](data-sources-performance-counters.md)。
* [ログ クエリ](../log-query/log-query-overview.md)について学習し、データ ソースとソリューションから収集されたデータを分析します。 
