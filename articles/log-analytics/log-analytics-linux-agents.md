---
title: "Azure Log Analytics への Linux コンピューターの接続 | Microsoft Docs"
description: "Log Analytics を使用すると、Linux コンピューターから生成されたデータを収集し、そのデータに基づいた行動を起こすことができます。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: ab5b76d8-9ab5-406e-8768-76fb0632d830
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: b01b0d3d61168c1eec52f3fd040b829e0c51a878
ms.lasthandoff: 03/30/2017


---
# <a name="connect-your-linux-computers-to-log-analytics"></a>Linux コンピューターを Log Analytics に接続する
Log Analytics を使用すると、Linux コンピューターから生成されたデータを収集し、そのデータに基づいた行動を起こすことができます。 Linux から収集されたデータを OMS に追加すれば、実質的にどこからでも Linux システムやコンテナー ソリューション (Docker など) を管理できます。 こうしたデータの生成元は、オンプレミスのデータセンターに物理サーバーとして存在することもあれば、Amazon Web Services (AWS) や Microsoft Azure などのクラウドでホストされるサービス内の仮想コンピューターとして存在することもあります。デスク上のノート PC がデータの生成元となる場合もあるでしょう。 OMS は、Windows コンピューターからもデータを収集するため、紛れもなく異種混合の IT 環境に対応します。

OMS の Log Analytics では、こうした種々雑多なソースのデータを単一の管理ポータルで表示し、管理することができます。 そのため監視に必要なシステムの種類や数が減って、データが取り込みやすくなり、ビジネス分析ソリューションや既存のシステムに必要なデータをエクスポートすることができます。

この記事は、OMS Agent for Linux で Linux コンピューターのデータを収集して管理するための入門ガイドとしてご利用ください。 プロキシ サーバーの構成、CollectD メトリックの情報、カスタム JSON データ ソースなどの技術的詳細については、GitHub で [OMS Agent for Linux の概要](https://github.com/Microsoft/OMS-Agent-for-Linux)と [OMS Agent for Linux の詳細なドキュメント](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md)を参照してください。

現在、Linux コンピューターから収集できるデータの種類は次のとおりです。

* パフォーマンス メトリック
* Syslog イベント
* Nagios と Zabbix からのアラート
* Docker コンテナーのパフォーマンス メトリック、インベントリ、ログ

## <a name="supported-linux-versions"></a>サポートされている Linux バージョン
各種 Linux ディストリビューションの x86 バージョンと x64 バージョンが公式にサポートされています。 ただし OMS Agent for Linux は、ここに記載された以外のディストリビューションでも動作します。

* Amazon Linux 2012.09 ～ 2015.09
* CentOS Linux 5、6、7
* Oracle Linux 5、6、7
* Red Hat Enterprise Linux Server 5、6、7
* Debian GNU/Linux 6、7、8
* Ubuntu 12.04 LTS、14.04 LTS、15.04、15.10
* SUSE Linux Enterprise Server 11 および 12

## <a name="oms-agent-for-linux"></a>OMS Agent for Linux
Operations Management Suite Agent for Linux は、複数のパッケージで構成されます。 リリース ファイルには、次のパッケージが含まれており、シェル バンドルを `--extract`で実行することによって抽出できます。

| **パッケージ** | **バージョン** | **説明** |
| --- | --- | --- |
| omsagent |1.1.0 |Operations Management Suite Agent for Linux |
| omsconfig |1.1.1 |OMS Agent 用の構成エージェント |
| omi |1.0.8.3 |Open Management Infrastructure (OMI) -- 軽量の CIM サーバー |
| scx |1.6.2 |オペレーティング システムのパフォーマンス メトリックの OMI CIM プロバイダー |
| apache-cimprov |1.0.0 |OMI の Apache HTTP Server パフォーマンス監視プロバイダー。 Apache HTTP Server が検出された場合にのみインストールされます。 |
| mysql-cimprov |1.0.0 |OMI の MySQL Server パフォーマンス監視プロバイダー。 MySQL/MariaDB サーバーが検出された場合にのみインストールされます。 |
| docker-cimprov |0.1.0 |OMI の Docker プロバイダー。 Docker が検出された場合にのみインストールされます。 |

### <a name="additional-installation-artifacts"></a>その他のインストール アーティファクト
OMS Agent for Linux のパッケージをインストールした後、システム全体に関連した構成の変更が別途適用されます。 omsagent パッケージをアンインストールすると、これらのアーティファクトは削除されます。

* `omsagent` という名前の非特権ユーザーが作成されます。 omsagent デーモンはこのアカウントで実行されます。
* sudoers の "include" ファイルが /etc/sudoers.d/omsagent に作成されます。これによって、syslog デーモンや omsagent デーモンを再起動する権限が omsagent に与えられます。 sudo の "include" ディレクティブが、インストールされているバージョンの sudo でサポートされていない場合、そのエントリが /etc/sudoers に書き込まれます。
* イベントの一部をエージェントに転送するよう syslog の構成が変更されます。 詳細については、以下の「 **データ収集の構成** 」を参照してください。

### <a name="linux-data-collection-details"></a>Linux データ収集の詳細
次の表は、データの収集手段と、データの収集方法に関する各種情報をまとめたものです。

| source セクション | 直接エージェント | SCOM エージェント | Azure Storage (Azure Storage) | SCOM の要否 | 管理グループによって送信される SCOM エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- |
| Zabbix |![はい](./media/log-analytics-linux-agents/oms-bullet-green.png) |![なし](./media/log-analytics-linux-agents/oms-bullet-red.png) |![いいえ](./media/log-analytics-linux-agents/oms-bullet-red.png) |![いいえ](./media/log-analytics-linux-agents/oms-bullet-red.png) |![なし](./media/log-analytics-linux-agents/oms-bullet-red.png) |1 分 |
| Nagios |![はい](./media/log-analytics-linux-agents/oms-bullet-green.png) |![なし](./media/log-analytics-linux-agents/oms-bullet-red.png) |![いいえ](./media/log-analytics-linux-agents/oms-bullet-red.png) |![いいえ](./media/log-analytics-linux-agents/oms-bullet-red.png) |![なし](./media/log-analytics-linux-agents/oms-bullet-red.png) |着信時 |
| syslog |![はい](./media/log-analytics-linux-agents/oms-bullet-green.png) |![なし](./media/log-analytics-linux-agents/oms-bullet-red.png) |![いいえ](./media/log-analytics-linux-agents/oms-bullet-red.png) |![いいえ](./media/log-analytics-linux-agents/oms-bullet-red.png) |![なし](./media/log-analytics-linux-agents/oms-bullet-red.png) |10 分 (Azure Storage からデータを収集する場合) または着信時 (エージェントからデータを収集する場合) |
| Linux パフォーマンス カウンター |![はい](./media/log-analytics-linux-agents/oms-bullet-green.png) |![なし](./media/log-analytics-linux-agents/oms-bullet-red.png) |![いいえ](./media/log-analytics-linux-agents/oms-bullet-red.png) |![いいえ](./media/log-analytics-linux-agents/oms-bullet-red.png) |![なし](./media/log-analytics-linux-agents/oms-bullet-red.png) |スケジュールに基づく頻度 (間隔は 10 秒以上) |
| 変更の追跡 |![はい](./media/log-analytics-linux-agents/oms-bullet-green.png) |![なし](./media/log-analytics-linux-agents/oms-bullet-red.png) |![いいえ](./media/log-analytics-linux-agents/oms-bullet-red.png) |![いいえ](./media/log-analytics-linux-agents/oms-bullet-red.png) |![なし](./media/log-analytics-linux-agents/oms-bullet-red.png) |1 時間に 1 回 |

### <a name="package-requirements"></a>パッケージの要件
| **必須パッケージ** | **説明** | **最小バージョン** |
| --- | --- | --- |
| Glibc |GNU C ライブラリ |2.5-12 |
| Openssl |OpenSSL ライブラリ |0.9.8e または 1.0 |
| Curl |cURL Web クライアント |7.15.5 |
| Python-ctypes |関数ライブラリ |該当なし |
| PAM |Pluggable Authentication Module (プラグ可能な認証モジュール) |該当なし |

> [!NOTE]
> syslog メッセージを収集するには、rsyslog または syslog-ng が必要となります。 syslog イベントの収集に関して、バージョン 5 の Red Hat Enterprise Linux、CentOS、Oracle Linux 版の既定の syslog デーモン (sysklog) はサポートされません。 このバージョンの各種ディストリビューションから syslog データを収集するには、rsyslog デーモンをインストールし、sysklog を置き換えるように構成する必要があります。
>
>

## <a name="quick-install"></a>クイック インストール
omsagent をダウンロードしてチェックサムを検証し、エージェントをインストールして利用できる状態にするためには、以下のコマンドを実行します。 コマンドは 64 ビット用です。 ワークスペース ID とプライマリ キーは OMS ポータルの **[Settings (設定)]** の **[Connected Sources (接続されているソース)]** タブで確認できます。

![workspace details](./media/log-analytics-linux-agents/oms-direct-agent-primary-key.png)

```
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR OMS WORKSPACE ID> -s <YOUR OMS WORKSPACE PRIMARY KEY>
```

エージェントは、他にもさまざまな方法でインストールしたりアップグレードしたりすることができます。 詳細については、「 [Steps to install the OMS Agent for Linux (OMS Agent for Linux のインストール手順)](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#steps-to-install-the-oms-agent-for-linux)」を参照してください。

[Azure ビデオ チュートリアル](https://www.youtube.com/watch?v=mF1wtHPEzT0)を見ることもできます。

## <a name="choose-your-linux-data-collection-method"></a>Linux データの収集手段の選択
収集するデータの種類をどのように選ぶかは、OMS ポータルを使用するか、各種構成ファイルを Linux クライアント上で直接編集するかによって異なります。 ポータルを使用する場合、その構成が自動的にすべての Linux クライアントに送信されます。 Linux クライアントごとに異なる構成が必要な場合は、クライアントのファイルを個別に編集するか、代替手段 (PowerShell DSC、Chef、Puppet など) を使用する必要があります。

収集対象とする syslog イベントとパフォーマンス カウンターは、Linux コンピューター上の構成ファイルで指定できます。 *エージェントの構成ファイルを編集することによってデータの収集を構成する場合は、構成の一元管理を無効にする必要があります。*  以降、エージェントの構成ファイルでデータの収集設定を行う手順と、すべての OMS Agent for Linux または個々のコンピューターに対して構成の一元管理を無効化する手順について説明しています。

### <a name="disable-oms-management-for-an-individual-linux-computer"></a>Linux コンピューターの OMS 管理を個別に無効化する
構成データ収集の一元管理を Linux コンピューターごとに無効化するには、OMS_MetaConfigHelper.py スクリプトを使用します。 この方法は、特殊な構成を一部のコンピューターにのみ適用する必要がある場合に効果的です。

構成の一元管理を無効化するには:

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
```

構成の一元管理を再び有効化するには:

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py –enable
```

## <a name="linux-performance-counters"></a>Linux パフォーマンス カウンター
Linux のパフォーマンス カウンターは、Windows のパフォーマンス カウンターと似ており、どちらも同じような働きがあります。 パフォーマンス カウンターの追加と構成は、以下の手順で行います。 パフォーマンス カウンターが OMS に追加された後、30 秒おきにデータが収集されます。

### <a name="to-add-a-linux-performance-counter-in-oms"></a>Linux のパフォーマンス カウンターを OMS で追加するには
1. OMS ポータルを使用して OMS Agent for Linux を構成するには、[Settings] (設定) ページで Linux のパフォーマンス カウンターを追加し、 **[Data]**(データ) をクリックします。  
2. **[Settings (設定)]** ページの **[Data (データ)]** で、**[Linux performance counters (Linux パフォーマンス カウンター)]** をクリックし、追加するカウンターの名前を選択するか入力します。  
    ![データ](./media/log-analytics-linux-agents/oms-settings-data01.png)
3. カウンターのフル ネームがわからない場合は、名前の一部分を入力すると、使用できるカウンターが一覧表示されます。 追加するカウンターが見つかったら、目的の名前を一覧から選んでクリックし、プラス アイコンをクリックしてカウンターを追加します。
4. カウンターの一覧に、追加したカウンターが色付きのバーで強調表示されます。
5. 既定では、 **[Apply below configuration to my machines]** (以下の構成をマシンに適用する) オプションが選択されます。 構成データの送信を無効にするには、この選択を解除します。
6. パフォーマンス カウンターへの変更が済んだら、ページの一番下にある **[Save]** (保存) をクリックして変更を確定します。 その後通常 5 分以内に、適用した構成の変更が OMS に登録されているすべての OMS Agent for Linux に送信されます。

### <a name="configure-linux-performance-counters-in-oms"></a>OMS で Linux のパフォーマンス カウンターを構成する
Windows のパフォーマンス カウンターの場合、パフォーマンス カウンターごとに特定のインスタンスを選択できます。 ただし、Linux のパフォーマンス カウンターの場合、カウンターに対して選択したインスタンスが、そのすべての子カウンターに適用されます。 次の表は、Linux と Windows の両方のパフォーマンス カウンターで利用できる共通のインスタンスを示しています。

| **インスタンス名** | **意味** |
| --- | --- |
| \_Total |すべてのインスタンスの合計 |
| \* |すべてのインスタンス |
| (/&#124;/var) |/ または /var という名前のインスタンスと一致します。 |

同様に、親カウンターに対して選択したサンプリング間隔が、そのすべての子カウンターに適用されます。 つまり、すべての子カウンターのサンプリング間隔とインスタンスは 1 つのまとまりとして存在しています。

### <a name="add-and-configure-performance-metrics-with-linux"></a>Linux でのパフォーマンス メトリックの追加と構成
収集するパフォーマンス メトリックは、/etc/opt/microsoft/omsagent/&lt;workspace id&gt;/conf/omsagent.conf の構成によって制御されます。 OMS Agent for Linux で利用できるクラスとメトリックについては、「 [Available performance metrics (利用可能なパフォーマンス メトリック)](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#appendix-available-performance-metrics) 」を参照してください。

収集するパフォーマンス メトリックの各オブジェクト (カテゴリ) は、構成ファイルの中で単一の `<source>` 要素として定義する必要があります。 次の構文形式に従って記述してください。

```
<source>
  type oms_omi  
  object_name "Processor"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 30s
</source>

```

この要素の構成可能なパラメーターは以下のとおりです。

* **Object\_name**: 収集のオブジェクト名。
* **Instance\_regex**: 収集するインスタンスを定義する "*正規表現*"。 すべてのインスタンスは、 `.*` という値で指定します。 \_Total インスタンスのみを対象にプロセッサ メトリックを収集するには、`_Total` を指定します。 crond または sshd のインスタンスのみを対象にプロセス メトリックを収集するには、「 `(crond|sshd)`」と指定します。
* **Counter\_name\_regex**: 収集する (オブジェクトの) カウンターを定義する "*正規表現*"。 オブジェクトのすべてのカウンターを収集するには、「 `.*`」と指定します。 メモリ オブジェクトを対象にスワップ領域カウンターを収集するには、「 `.+Swap.+`
* **Interval:**: オブジェクトのカウンターを収集する頻度。

パフォーマンス メトリックの既定の構成を次に示します。

```
<source>
  type oms_omi
  object_name "Physical Disk"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 5m
</source>

<source>
  type oms_omi
  object_name "Logical Disk"
  instance_regex ".*
  counter_name_regex ".*"
  interval 5m
</source>

<source>
  type oms_omi
  object_name "Processor"
  instance_regex ".*
  counter_name_regex ".*"
  interval 30s
</source>

<source>
  type oms_omi
  object_name "Memory"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 30s
</source>

```

### <a name="enable-mysql-performance-counters-using-linux-commands"></a>Linux のコマンドを使用して MySQL のパフォーマンス カウンターを有効にする
omsagent バンドルをインストールするときに、コンピューターに MySQL Server または MariaDB Server が検出された場合、MySQL Server 用のパフォーマンス監視プロバイダーが自動的にインストールされます。 このプロバイダーは、パフォーマンスの統計情報を公開するためにローカルの MySQL/MariaDB サーバーに接続します。 プロバイダーが MySQL Server にアクセスできるよう、MySQL ユーザーの資格情報を構成しておく必要があります。

たとえば、localhost 上の MySQL Server に使用する既定のユーザー アカウントを定義するには、以下のコマンドを使用します。

> [!NOTE]
> 資格情報ファイルの読み取りが omsagent アカウントに対して許可されている必要があります。 mycimprovauth コマンドは、omsgent として実行することをお勧めします。
>
>

```
sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>

sudo /opt/omi/bin/service_control restart
```


必要な MySQL の資格情報をファイルに指定してもかまいません。その場合は、/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth ファイルを作成します。 監視に使用する MySQL の資格情報を mysql-auth ファイルで管理する方法の詳細については、「[MySQL の監視に使用する資格情報を認証ファイルで管理する](#manage-mysql-monitoring-credentials-in-the-authentication-file)」を参照してください。

MySQL ユーザーが MySQL Server のパフォーマンス データを収集するために必要なオブジェクトの権限の詳細については、「 [MySQL のパフォーマンス カウンターに必要なデータベース権限](#database-permissions-required-for-mysql-performance-counters) 」を参照してください。

### <a name="enable-apache-http-server-performance-counters-using-linux-commands"></a>Linux のコマンドを使用して Apache HTTP Server のパフォーマンス カウンターを有効にする
omsagent バンドルをインストールするときに、コンピューターに Apache HTTP Server が検出された場合、Apache HTTP Server 用のパフォーマンス監視プロバイダーが自動的にインストールされます。 このプロバイダーは、Apache の "モジュール" に依存しています。パフォーマンス データにアクセスするためには、このモジュールを Apache HTTP Server に読み込んでおく必要があります。

モジュールの読み込みには、次のコマンドを使用します。

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Apache 監視モジュールをアンロードするには、次のコマンドを実行します。

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```
### <a name="to-view-performance-data-with-log-analytics"></a>Log Analytics でパフォーマンス データを表示するには
1. Operations Management Suite ポータルで、[Log Search] (ログの検索) タイルをクリックします。
2. すべてのパフォーマンス カウンターを表示するには、検索バーに「 `* (Type=Perf)` 」と入力します。

OMS の収集対象には Windows のパフォーマンス カウンター データも含まれるので、Linux 固有のデータに検索範囲を限定する必要があります。 次の例では、Chorizo21 という名前の Linux サーバーに固有のパフォーマンス データが表示されます。

```
Type=Perf Computer=chorizo*
```

![example server shown in search results](./media/log-analytics-linux-agents/oms-perfsearch01.png)

検索結果の **[Metrics]** (メトリック) をクリックすると、データが収集されているカウンターを表示できます。 リアルタイム データは、カウンターごとにグラフとして表示されます。

![[Metrics]](./media/log-analytics-linux-agents/oms-perfmetrics01.png)

## <a name="syslog"></a>syslog
Syslog は、Windows イベント ログに似たイベント ログ プロトコルです。OMS に表示されるときの動作は、どちらもほぼ同じです。

### <a name="to-add-a-new-linux-syslog-facility-in-oms"></a>OMS に Linux の syslog ファシリティを新しく追加するには
1. **[Settings (設定)]** ページの **[Data (データ)]** で、**[Syslog]** をクリックし、プラス アイコンの左側に、追加する syslog ファシリティの名前を入力します。
    ![Linux syslog](./media/log-analytics-linux-agents/oms-linuxsyslog01.png)
2. syslog ファシリティのフル ネームがわからない場合は、名前の一部分を入力すると、使用できる syslog ファシリティが一覧表示されます。 追加する syslog ファシリティが見つかったら、目的の名前を一覧から選んでクリックし、プラス アイコンをクリックして syslog ファシリティを追加します。
3. syslog ファシリティの一覧に、追加したファシリティが色付きのバーで強調表示されます。 次に、収集の対象とする重要度 (syslog ファシリティ情報のカテゴリ) を選択します。
4. ページの一番下にある **[Save]** (保存) をクリックして変更を確定します。 その後通常 5 分以内に、適用した構成の変更が OMS に登録されているすべての OMS Agent for Linux に送信されます。

### <a name="configure-linux-syslog-facilities-in-linux"></a>Linux でその syslog ファシリティを構成する
Syslog イベントは、syslog デーモン (rsyslog や syslog-ng など) から、エージェントが待機しているローカル ポートに送信されます。 既定では、25224 ポートが使用されます。 エージェントをインストールすると、既定の syslog 構成が適用されます。 この構成の場所は次のとおりです。

Rsyslog: /etc/rsyslog.d/rsyslog-oms.conf

Syslog-ng: /etc/syslog-ng/syslog-ng.conf

OMS エージェントの既定の syslog 構成では、重要度が警告以上の syslog イベントがすべてのファシリティからアップロードされます。

> [!NOTE]
> syslog 構成を編集した場合、変更を有効にするには、syslog デーモンを再起動する必要があります。
>
>

OMS Agent for Linux の既定の syslog 構成は次のとおりです。

#### <a name="rsyslog"></a>Rsyslog
```
kern.warning       @127.0.0.1:25224
user.warning       @127.0.0.1:25224
daemon.warning     @127.0.0.1:25224
auth.warning       @127.0.0.1:25224
syslog.warning     @127.0.0.1:25224
uucp.warning       @127.0.0.1:25224
authpriv.warning   @127.0.0.1:25224
ftp.warning        @127.0.0.1:25224
cron.warning       @127.0.0.1:25224
local0.warning     @127.0.0.1:25224
local1.warning     @127.0.0.1:25224
local2.warning     @127.0.0.1:25224
local3.warning     @127.0.0.1:25224
local4.warning     @127.0.0.1:25224
local5.warning     @127.0.0.1:25224
local6.warning     @127.0.0.1:25224
local7.warning     @127.0.0.1:25224
```

#### <a name="syslog-ng"></a>Syslog-ng
```
#OMS_facility = all
filter f_warning_oms { level(warning); };
destination warning_oms { tcp("127.0.0.1" port(25224)); };
log { source(src); filter(f_warning_oms); destination(warning_oms); };
```

### <a name="to-view-all-syslog-events-with-log-analytics"></a>Log Analytics ですべての Syslog イベントを表示するには
1. Operations Management Suite ポータルで、 **[Log Search]** (ログの検索) タイルをクリックします。
2. **[Log Management]** (ログの管理) グループで、あらかじめ定義されている syslog 検索を選択し、いずれかを選んで実行します。

この例では、すべての Syslog イベントを表示しています。

![Syslog events shown in Log Search](./media/log-analytics-linux-agents/oms-linux-syslog.png)

この状態から、検索結果を掘り下げて調査することができます。

## <a name="linux-alerts"></a>Linux のアラート
Nagios または Zabbix を使用して Linux マシンを管理している場合、これらのツールから生成されたアラートを OMS で受信できます。 ただし、受信するアラート データを OMS ポータルで構成する手段が現時点では存在しません。 アラートを OMS に送信するには、構成ファイルを直接編集する必要があります。

### <a name="collect-alerts-from-nagios"></a>Nagios からのアラートの収集
Nagios サーバーからアラートを収集するには、次のように構成を変更する必要があります。

1. Nagios ログ ファイル (/var/log/nagios/nagios.log) の読み取りアクセス権をユーザー **omsagent** に追加します。 たとえば nagios.log ファイルをグループ **nagios** が所有している場合、ユーザー **omsagent** を **nagios** グループに追加します。

    ```
    sudo usermod –a -G nagios omsagent
    ```
2. omsagent.confconfiguration ファイル (/etc/opt/microsoft/omsagent/&lt;workspace id&gt;/conf/omsagent.conf) に変更を加えます。 次のエントリが存在し、コメント アウトされていないことを確認します。

    ```
    <source>
    type tail
    #Update path to point to your nagios.log
    path /var/log/nagios/nagios.log
    format none
    tag oms.nagios
    </source>

    <filter oms.nagios>
    type filter_nagios_log
    </filter>
    ```
3. omsagent デーモンを再起動します。

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart
    ```

### <a name="collect-alerts-from-zabbix"></a>Zabbix からのアラートの収集
Zabbix サーバーからアラートを収集する手順は、前述した Nagios の場合と同様ですが、ユーザーとパスワードは *クリア テキスト*で指定する必要があります。 本来このような仕様は好ましくありませんので、近い将来改善される可能性は十分あります。 この問題に対処するために、ユーザーを作成し、監視専用の権限を付与することをお勧めします。

Zabbix に使用する omsagent.conf 構成ファイル (/etc/opt/microsoft/omsagent/&lt;workspace id&gt;/conf/omsagent.conf) のセクションの例を次に示します。

```
<source>
  type zabbix_alerts
  run_interval 1m
  tag oms.zabbix
  zabbix_url http://localhost/zabbix/api_jsonrpc.php
  zabbix_username Admin
  zabbix_password zabbix
</source>

```

### <a name="view-alerts-in-log-analytics-search"></a>Log Analytics 検索でのアラートの表示
アラートを OMS に送信するための構成が Linux コンピューターで済んでいれば、いくつかの簡単なログ検索クエリを使用してアラートを表示できます。 以下の例で取り上げる検索クエリでは、生成された記録済みのアラートがすべて返されます。 たとえば、IT インフラストラクチャでなんらかの問題が発生した場合、以下のサンプル クエリの結果から、問題の原因を判別できる可能性があります。 さらに、アラートをその生成元のシステムごとに深く掘り下げて、調査範囲を絞り込むことも簡単に行えます。 その利点は、さまざまな管理システムにゼロからアクセスする必要がないという点です。つまり、アラートが OMS に送信されていれば、そこを調査の足掛かりにすることができます。

```
Type=Alert
```

#### <a name="to-view-all-nagios-alerts-with-log-analytics"></a>Log Analytics ですべての Nagios アラートを表示するには
1. Operations Management Suite ポータルで、 **[Log Search]** (ログの検索) タイルをクリックします。
2. クエリ バーに、次の検索クエリを入力します。

    ```
    Type=Alert SourceSystem=Nagios
    ```
   ![Nagios alerts shown in Log Search](./media/log-analytics-linux-agents/oms-linux-nagios-alerts.png)

表示された検索結果を掘り下げて、さらに詳しい情報を得ることができます ( *AlertState*など)。

### <a name="to-view-all-zabbix-alerts-with-log-analytics"></a>Log Analytics ですべての Zabbix アラートを表示するには
1. Operations Management Suite ポータルで、 **[Log Search]** (ログの検索) タイルをクリックします。
2. クエリ バーに、次の検索クエリを入力します。

    ```
    Type=Alert SourceSystem=Zabbix
    ```
   ![Zabbix alerts shown in Log Search](./media/log-analytics-linux-agents/oms-linux-zabbix-alerts.png)

表示された検索結果を掘り下げて、さらに詳しい情報を得ることができます ( *AlertName*など)。

## <a name="compatibility-with-system-center-operations-manager"></a>System Center Operations Manager との共存
OMS Agent for Linux は、エージェントのバイナリを System Center Operations Manager エージェントと共有します。 現在 Operations Manager で管理されているシステムに OMS Agent for Linux をインストールすると、コンピューター上の OMI パッケージと SCX パッケージが、より新しいバージョンにアップグレードされます。 OMS Agent for Linux と System Center 2012 R2 は共存させることができます。 ただし、 **System Center 2012 SP1 以前のバージョンは現在 OMS Agent for Linux に対応しておらず、共存させることはできません。**

> [!NOTE]
> OMS Agent for Linux のインストール先となるコンピューターが現在は Operations Manager によって管理されていないものの、将来的には Operations Manager で管理することを予定している場合、そのコンピューターを検出するには、OMI の構成に変更を加える必要があります。 **Operations Manager エージェントが OMS Agent for Linux より先にインストールされている場合、この手順は不要です。**
>
>

### <a name="to-enable-the-oms-agent-for-linux-to-communicate-with-operations-manager"></a>Operations Manager と連携できるように OMS Agent for Linux を設定するには
1. /etc/opt/omi/conf/omiserver.conf ファイルを編集します。
2. **httpsport=** で始まる行にポート 1270 が定義されていることを確認します  (例: `httpsport=1270`)
3. 次のコマンドで OMI サーバーを再起動します。

    ```
    sudo /opt/omi/bin/service_control restart
    ```

## <a name="database-permissions-required-for-mysql-performance-counters"></a>MySQL のパフォーマンス カウンターに必要なデータベース権限
MySQL の監視ユーザーに権限を追加するには、権限を付与する側のユーザーに 'GRANT option' 特権と、追加の対象となる特権が必要になります。

MySQL ユーザーがパフォーマンス データを返すには、そのユーザーに、次のクエリへのアクセス権が必要となります。

```
SHOW GLOBAL STATUS;
SHOW GLOBAL VARIABLES:
```

MySQL ユーザーには、これらのクエリに加え、次に示す既定のテーブルに対する SELECT アクセス権が必要です。

* information_schema
* mysql

これらの特権は、次の grant コマンドで付与できます。

```
GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;
```

## <a name="manage-mysql-monitoring-credentials-in-the-authentication-file"></a>MySQL の監視に使用する資格情報を認証ファイルで管理する
以降のセクションでは、MySQL の資格情報を管理する方法について説明します。

### <a name="configure-the-mysql-omi-provider"></a>MySQL OMI プロバイダーの構成
MySQL OMI プロバイダーがパフォーマンスや正常性に関する情報を MySQL インスタンスに照会するためには、MySQL ユーザーが事前に構成され、MySQL クライアント ライブラリがインストールされている必要があります。

### <a name="mysql-omi-authentication-file"></a>MySQL OMI 認証ファイル
MySQL OMI プロバイダーは、MySQL インスタンスが接続を待ち受けるバインド アドレスとポートのほか、メトリックの収集に使用する資格情報を特定するために認証ファイルを使用します。 MySQL OMI プロバイダーは、インストールの過程で MySQL の my.cnf 構成ファイル (既定の場所) をスキャンしてバインド アドレスとポートを検出し、MySQL の OMI 認証ファイルを部分的に設定します。

MySQL Server インスタンスの監視を実行するには、事前に生成した MySQL OMI 認証ファイルを適切なディレクトリに追加します。

### <a name="authentication-file-format"></a>認証ファイルの形式
MySQL OMI 認証ファイルは、次の情報を含むテキスト ファイルです。

* ポート
* バインド アドレス
* MySQL ユーザー名
* Base64 でエンコードされたパスワード

MySQL OMI 認証ファイルによって読み取り/書き込み特権が付与されるのは、それを生成した Linux ユーザーだけです。

```
[Port]=[Bind-Address], [username], [Base64 encoded Password]
(Port)=(Bind-Address), (username), (Base64 encoded Password)
(Port)=(Bind-Address), (username), (Base64 encoded Password)
AutoUpdate=[true|false]
```

既定の MySQL OMI 認証ファイルには、検出された MySQL 構成ファイルから取得して解析した情報に基づいて、既定のインスタンスとポート番号が記述されます。

既定のインスタンスは、1 台の Linux ホストに存在する複数の MySQL インスタンスを管理しやすくする手段であり、ポート 0 のインスタンスがそれに該当します。 追加されたインスタンスはすべて、既定のインスタンスから一連のプロパティを継承します。 たとえばポート '3308' で接続を待ち受ける MySQL インスタンスを追加した場合、3308 を待機ポートとするインスタンスの試行と監視には、既定のインスタンスのバインド アドレス、ユーザー名、Base64 エンコードのパスワードが使用されます。 ここで 3308 のインスタンスを別のアドレスにバインドし、MySQL ユーザー名とパスワードは同じものを使用する場合、書き換えが必要なのはバインド アドレスのみで、他のプロパティは継承されます。

認証ファイルの例を次に示します。

既定のインスタンスとポート 3308 のインスタンス:

```
0=127.0.0.1, myuser, cnBwdA==3308=, ,AutoUpdate=true
```

既定のインスタンスとポート 3308 のインスタンス (+ 異なる Base 64 エンコード パスワード):

```
0=127.0.0.1, myuser, cnBwdA==3308=127.0.1.1, , AutoUpdate=true
```


| **プロパティ** | **説明** |
| --- | --- |
| ポート |MySQL インスタンスが接続を待ち受けている現在のポートを表します。  ポート 0 は、それ以降のプロパティが既定のインスタンスに使用されることを意味します。 |
| バインド アドレス |MySQL の現在のバインド アドレス。 |
| username |MySQL Server インスタンスの監視に使用する MySQL ユーザーのユーザー名。 |
| Base64 でエンコードされたパスワード |MySQL 監視ユーザーのパスワードを Base64 でエンコードしたもの。 |
| AutoUpdate |MySQL OMI プロバイダーがアップグレードされたとき、プロバイダーは、my.cnf ファイルに変更がないか再スキャンし、MySQL OMI 認証ファイルを上書きします。 MySQL OMI 認証ファイルに対する更新の必要性に応じて、このフラグを true または false に設定してください。 |

#### <a name="authentication-file-location"></a>認証ファイルの場所
MySQL OMI 認証ファイルは、次の場所に "mysql-auth" という名前で格納されている必要があります。

/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth

このファイル (と auth/omsagent ディレクトリ) の所有者は、omsagent ユーザーであることが必要です。

## <a name="agent-logs"></a>エージェントのログ
OMS Agent for Linux のログは次の場所に格納されています。

/var/opt/microsoft/omsagent/&lt;workspace id&gt;/log/

OMS Agent for Linux の omsconfig (エージェント構成) プログラムのログは次の場所に格納されています。

/var/opt/microsoft/omsconfig/log/

(パフォーマンス メトリック データを提供する) SCX コンポーネントと OMI コンポーネントのログは次の場所に格納されています。

/var/opt/omi/log/ and /var/opt/microsoft/scx/log

## <a name="troubleshooting-the-oms-agent-for-linux"></a>OMS Agent for Linux のトラブルシューティング
一般的な問題の診断およびトラブルシューティングには、以下の情報を使用します。

このセクションのトラブルシューティング情報が役に立たなかった場合は、問題を解決するために以下のリソースを使用することもできます。

* Premier サポートのお客様は、[Premier](https://premier.microsoft.com/) を通じてサポート ケースを登録できます
* Azure サポート契約のお客様は、[Azure Portal](https://manage.windowsazure.com/?getsupport=true) でサポート ケースを登録できます
* [GitHub の問題](https://github.com/Microsoft/OMS-Agent-for-Linux/issues)を提出します
* フォーラムに意見をフィードバックし、バグ レポートを作成します ([http://aka.ms/opinsightsfeedback](http://aka.ms/opinsightsfeedback))

### <a name="important-log-locations"></a>重要なログの場所
| ファイル | パス |
| --- | --- |
| OMS Agent for Linux のログ ファイル |`/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log ` |
| OMS エージェント構成のログ ファイル |`/var/opt/microsoft/omsconfig/omsconfig.log` |

### <a name="important-configuration-files"></a>重要な構成ファイル
| カテゴリ | ファイルの場所 |
| --- | --- |
| syslog |`/etc/syslog-ng/syslog-ng.conf` または `/etc/rsyslog.conf` または `/etc/rsyslog.d/95-omsagent.conf` |
| パフォーマンス、Nagios、Zabbix、OMS の出力および汎用エージェント |`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` |
| 追加の構成 |`/etc/opt/microsoft/omsagent/<workspace id>/omsagent.d/*.conf` |

> [!NOTE]
> 編集しているパフォーマンス カウンターおよび syslog の構成ファイルは、OMS ポータル構成が有効になると上書きされます。 OMS ポータルで、すべてのノードの構成を無効にすることができます。1 つのノードの場合は、次のように実行します。
>
>

```
sudo su omsagent -c /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
```


### <a name="enable-debug-logging"></a>デバッグ ログの有効化
デバッグ ログを有効にするには、OMS 出力プラグインと詳細出力を使用できます。

#### <a name="oms-output-plugin"></a>OMS 出力プラグイン
FluentD を使用すると、プラグインは入力と出力に対して異なるログ レベルを指定できます。 OMS 出力に異なるログ レベルを指定するには、`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` ファイルで汎用エージェントの構成を編集します。

構成ファイルの末尾の近くで、`log_level` プロパティを `info` から `debug` に変更します。

 ```
 <match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
 ```

デバッグ ログを使用すると、種類、データ項目の数、および送信にかかった時間によって区分された、OMS サービスへの一括アップロードを確認できます。

*"デバッグを有効にしたログの例"*:

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

#### <a name="verbose-output"></a>詳細出力
OMS 出力プラグインを使用する代わりに、データ項目を `stdout` に直接出力することもできます。この出力は、OMS Agent for Linux のログ ファイルで見ることができます。

`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` にある OMS 汎用エージェントの構成ファイルで、OMS 出力プラグインをコメントアウトします。コメントアウトするには、各行の先頭に `#` を追加します。

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

出力プラグインの下で、以下のセクションのコメントを削除します。各行の先頭にある `#` 記号を削除してください。

```
<match **>
  type stdout
</match>
```

### <a name="forwarded-syslog-messages-do-not-appear-in-the-log"></a>転送された Syslog メッセージがログに表示されない
#### <a name="probable-causes"></a>考えられる原因
* Linux サーバーに適用された構成で、送信されたファシリティやログ レベルの収集が許可されていません
* Syslog が Linux サーバーに正しく転送されていません
* 1 秒あたりに転送されるメッセージの数が多すぎて、OMS Agent for Linux の基本構成では処理できません

#### <a name="resolutions"></a>解決策
* OMS ポータルでの Syslog の構成にすべてのファシリティと正しいログ レベルが含まれていることを確認します
  * **OMS ポータル、[Settings (設定)]、[Data (データ)]、[Syslog] の順に移動します**
* 転送されたメッセージをネイティブの syslog メッセージング デーモン (`rsyslog``syslog-ng`) が受信できることを確認します
* Syslog サーバーのファイアウォール設定をチェックして、メッセージがブロックされていないことを確認します
* `logger` コマンドを使用して、OMS への Syslog メッセージをシミュレートします。たとえば、次のようにします。
  * `logger -p local0.err "This is my test message"`

### <a name="problems-connecting-to-oms-when-using-a-proxy"></a>プロキシを使用している場合の OMS への接続の問題
#### <a name="probable-causes"></a>考えられる原因
* エージェントをインストールおよび構成したときに指定したプロキシが正しくありません
* OMS サービス エンドポイントが、データセンターの許可リストに載っていません

#### <a name="resolutions"></a>解決策
* オプション `-v` を有効にして以下のコマンドを使用して、OMS Agent for Linux を再インストールします。 そうすると、プロキシ経由で OMS サービスに接続しているエージェントで詳細出力ができるようになります。
  * `/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`
  * OMS プロキシのドキュメント「[Configuring the agent for use with an HTTP proxy server (HTTP プロキシ サーバーと共に使用するためのエージェントの構成)](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#configuring-the-agent-for-use-with-an-http-proxy-server)」を参照してください
* 以下の OMS サービス エンドポイントが許可リストに載っていることを確認します

| エージェントのリソース | ポート |
| --- | --- |
| &#42;.ods.opinsights.azure.com |ポート 443 |
| &#42;.oms.opinsights.azure.com |ポート 443 |
| ods.systemcenteradvisor.com |ポート 443 |
| &#42;.blob.core.windows.net/ |ポート 443 |

### <a name="a-403-error-is-displayed-when-onboarding"></a>オンボード時に 403 エラーが表示される
#### <a name="probable-causes"></a>考えられる原因
* Linux サーバーで日付と時刻が正しくありません
* 使用されているワークスペース ID とワークスペース キーが正しくありません

#### <a name="resolution"></a>解決策
* `date` コマンドを使用して、Linux サーバーの時刻を確認します。 データが現在の時刻から 15 分より前または後である場合、オンボードは失敗します。 これを修正するには、Linux サーバーの日付やタイムゾーンを更新します。
* OMS Agent for Linux の最新バージョンは、時間の差によってオンボードが失敗する場合に通知します。
* 正しいワークスペース ID とワークスペース キーを使用して、再オンボードします。 詳細については、「[Onboarding using the command line (コマンド ラインを使用したオンボード)](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)」を参照してください。

### <a name="a-500-error-or-404-error-appears-in-the-log-file-after-onboarding"></a>オンボード後、ログ ファイルに 500 エラーまたは 404 エラーが記録される
これは、OMS ワークスペースへの Linux データの最初のアップロード中に発生する既知の問題です。 送信されているデータに影響したり、他の問題が生じたりすることはありません。 最初のオンボードでは、このエラーを無視できます。

### <a name="nagios-data-does-not-appear-in-the-oms-portal"></a>Nagios データが OMS ポータルに表示されない
#### <a name="probable-causes"></a>考えられる原因
* omsagent ユーザーが、Nagios ログ ファイルからの読み取りのアクセス許可を持っていない
* omsagent.conf ファイル内で、Nagios ソースおよびフィルター セクションがコメントのままになっている

#### <a name="resolutions"></a>解決策
* Nagios ファイルからの読み取りができるように、omsagent ユーザーを追加します。 詳細については、「[Nagios alerts (Nagios の警告)](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts)」を参照してください。
* `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` にある OMS Agent for Linux の汎用構成ファイルで、Nagios ソースとフィルターの**両方**のセクションから、次の例のようにコメントが削除されていることを確認します。

```
<source>
  type tail
  path /var/log/nagios/nagios.log
  format none
  tag oms.nagios
</source>

<filter oms.nagios>
  type filter_nagios_log
</filter>
```


### <a name="linux-data-doesnt-appear-in-the-oms-portal"></a>Linux データが OMS ポータルに表示されない
#### <a name="probable-causes"></a>考えられる原因
* OMS サービスへのオンボードが失敗しました
* OMS サービスへの接続がブロックされています
* OMS Agent for Linux のデータがバックアップされました

#### <a name="resolutions"></a>解決策
* `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` が存在していることを確認することにより、OMS サービスへのオンボードが成功したことを確認します。
* omsadmin.sh コマンド ラインを使用して再オンボードします。 詳細については、「[Onboarding using the command line (コマンド ラインを使用したオンボード)](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)」を参照してください。
* プロキシを使用する場合は、上記のプロキシのトラブルシューティング手順に従います
* 場合によっては、OMS Agent for Linux が OMS サービスと通信できない場合、Agent のデータが最大バッファー サイズである 50 MB までバックアップされます。 `/opt/microsoft/omsagent/bin/service_control restart` コマンドを実行して OMS Agent for Linux を再起動します。
  >[AZURE.NOTE] この問題は、Agent Version 1.1.0-28 以降で修正されます。

### <a name="syslog-linux-performance-counter-configuration-is-not-applied-in-the-oms-portal"></a>Syslog Linux パフォーマンス カウンターの構成が OMS ポータルで適用されない
#### <a name="probable-causes"></a>考えられる原因
* OMS Agent for Linux の構成エージェントが、OMS ポータルから最新の構成を取得していません。
* ポータルで変更された設定が適用されていません

#### <a name="resolutions"></a>解決策
OMS Agent for Linux の構成エージェントである `omsconfig` は、OMS ポータル構成の変更を 5 分ごとに取得します。 この構成が、`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` にある OMS Agent for Linux 構成ファイルに適用されます。

* 場合によっては、OMS Agent for Linux 構成エージェントがポータル構成サービスと通信できず、最新の構成が適用されないことがあります。
* 次のようにして、`omsconfig` エージェントがインストールされていることを確認してください。

  * `dpkg --list omsconfig` または `rpm -qi omsconfig`
  * インストールされていない場合は、OMS Agent for Linux の最新バージョンを再インストールします
* `omsconfig` エージェントが OMS サービスと通信できることを確認します

  * `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` コマンドを実行します
    * 上記のコマンドは、エージェントがポータルから取得した構成を返します。この構成には、Syslog 設定、Linux のパフォーマンス カウンター、カスタム ログなどが含まれています
    * 上記のコマンドが失敗した場合は、`sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py` コマンドを実行します。 このコマンドは、omsconfig エージェントに OMS サービスと通信して最新の構成を取得するように強制します。

### <a name="custom-linux-log-data-does-not-appear-in-the-oms-portal"></a>カスタム Linux ログ データが OMS ポータルに表示されない
#### <a name="probable-causes"></a>考えられる原因
* OMS サービスへのオンボードが失敗しました
* **[Apply the following configuration to my Linux Servers (次の構成を Linux サーバーに適用する)]** 設定がオンになっていません
* omsconfig がポータルから最新のカスタム ログを取得していません
* `omsagent` が、アクセス許可に問題があるか、`omsagent` が見つからなかったため、カスタム ログにアクセスできません。 この場合、次の出力が表示されます。
  * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
  * `[DATETIME] [error]: file not accessible by omsagent.`
* これは、OMS Agent for Linux Version 1.1.0-217 で修正された、競合状態に関する既知の問題です

#### <a name="resolutions"></a>解決策
* `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` ファイルが存在するかどうかを調べることによって、正常にオンボードできていることを確認します。
  * 必要であれば、omsadmin.sh コマンド ラインを使用して、もう一度オンボードします。 詳細については、「[Onboarding using the command line (コマンド ラインを使用したオンボード)](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)」を参照してください。
* OMS ポータルの **[Settings (設定)]** の **[Data (データ)]** タブで、**[Apply the following configuration to my Linux Servers (次の構成を Linux サーバーに適用する)]** 設定がオンになっていることを確認します  
  ![構成の適用](./media/log-analytics-linux-agents/customloglinuxenabled.png)
* `omsconfig` エージェントが OMS サービスと通信できることを確認します

  * `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` コマンドを実行します
  * 上記のコマンドは、エージェントがポータルから取得した構成を返します。この構成には、Syslog 設定、Linux のパフォーマンス カウンター、カスタム ログなどが含まれています
  * 上記のコマンドが失敗した場合は、`sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py` コマンドを実行します。 このコマンドは、omsconfig エージェントに OMS サービスと通信して最新の構成を取得するように強制します。

特権ユーザー `root` として実行される OMS Agent for Linux ユーザーの代わりに、OMS Agent for Linux は `omsagent` ユーザーとして実行されます。 ほとんどの場合、特定のファイルを読み取るためには、明示的なアクセス許可をユーザーに付与する必要があります。

`omsagent` ユーザーにアクセス許可を付与するには、次のコマンドを実行します。

1. `sudo usermod -a -G <GROUPNAME> <USERNAME>` で、`omsagent` ユーザーを特定のグループに追加します
2. `sudo chmod -R ugo+rw <FILE DIRECTORY>` で、必要なファイルへの汎用の読み取りアクセス許可を付与します

OMS Agent for Linux Version 1.1.0-217 で修正された、競合状態に関する既知の問題があります。 最新のエージェントに更新した後、次のコマンドを実行して、出力プラグインの最新バージョンを取得します。

```
sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf
```

## <a name="known-limitations"></a>既知の制限事項
現時点で OMS Agent for Linux に存在する制限事項については、以降のセクションをご覧ください。

### <a name="azure-diagnostics"></a>Azure 診断
Azure で実行されている Linux 仮想マシンの場合、Azure 診断と Operations Management Suite によるデータ収集を有効にするには、別途作業が必要となります。 **Version 2.2** が必要です。

Diagnostic Extension for Linux のインストールと構成の詳細については、「[Azure CLI コマンドを使用して Linux Diagnostic Extension を有効にする](../virtual-machines/linux/classic/diagnostic-extension.md#use-the-azure-cli-command-to-enable-the-linux-diagnostic-extension)」を参照してください。

**Linux Diagnostics Extension 2.0 から 2.2 へのアップグレード (Azure CLI ASM):**

```
azure vm extension set -u <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions 2.0
azure vm extension set <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions 2.2 --private-config-path PrivateConfig.json
```

**ARM**

```
azure vm extension set -u <resource-group> <vm-name> Microsoft.Insights.VMDiagnosticsSettings Microsoft.OSTCExtensions 2.0
azure vm extension set <resource-group> <vm-name> LinuxDiagnostic Microsoft.OSTCExtensions 2.2 --private-config-path PrivateConfig.json
```

これらのコマンド例では、PrivateConfig.json という名前のファイルを参照しています。 このファイルに使用される形式の例を次に示します。

```
    {
    "storageAccountName":"the storage account to receive data",
    "storageAccountKey":"the key of the account"
    }
```

### <a name="sysklog-is-not-supported"></a>Sysklog はサポート対象外
syslog メッセージを収集するには、rsyslog または syslog-ng が必要となります。 syslog イベントの収集に関して、バージョン 5 の Red Hat Enterprise Linux、CentOS、Oracle Linux 版の既定の syslog デーモン (sysklog) はサポートされません。 このバージョンの各種ディストリビューションから syslog データを収集するには、rsyslog デーモンをインストールし、sysklog を置き換えるように構成する必要があります。 sysklog から rsyslog への置き換えについて詳しくは、「 [Install the newly built rsyslog RPM (新しく構築された rsyslog RPM のインストール)](http://wiki.rsyslog.com/index.php/Rsyslog_on_CentOS_success_story#Install_the_newly_built_rsyslog_RPM)」をご覧ください。

## <a name="next-steps"></a>次のステップ
* [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md) 」 (ソリューションギャラリーから Log Analytics ソリューションを追加する) を参照してください。
* [ログ検索](log-analytics-log-searches.md) について理解を深め、ソリューションによって収集された情報の詳細を確認します。
* カスタム検索結果を保存および表示するには、 [ダッシュボード](log-analytics-dashboards.md) を使用します。

