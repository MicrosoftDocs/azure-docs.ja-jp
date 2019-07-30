---
title: Azure Log Analytics Linux エージェントのトラブルシューティング | Microsoft Docs
description: Azure Monitor の Linux 用 Log Analytics エージェントに関する最も一般的な問題の現象、原因、および解決策について説明します。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: 83f9cc050694344cdc5f4f5a2070bc875fcba3d9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071667"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>Linux 用 Log Analytics エージェントに関する問題のトラブルシューティング方法 

Azure Monitor の Linux 用 Log Analytics エージェントで発生する可能性のあるエラーのトラブルシューティングのヘルプを提供し、それらを解決するための考えられる解決策を提案します。

どの手順でも解決しない場合は、以下のサポート チャネルもご利用いただけます。

* Premier サポート ベネフィットをお持ちのお客様は、[Premier](https://premier.microsoft.com/) でサポート要求を開くことができます。
* Azure サポート契約のお客様は、[Azure portal](https://manage.windowsazure.com/?getsupport=true) でサポート要求を開くことができます。
* OMI の問題は、[OMI トラブルシューティング ガイド](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md)を参考にして診断してください。
* [GitHub の問題](https://github.com/Microsoft/OMS-Agent-for-Linux/issues)を提出します。
* Log Analytics のフィードバック ページ ([https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback)) で、提出されたアイデアやバグを確認したり、新しく登録します。  

## <a name="important-log-locations-and-log-collector-tool"></a>重要なログの場所とログ コレクター ツール

 ファイル | Path
 ---- | -----
 Linux 用 Log Analytics エージェントのログ ファイル | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 Log Analytics エージェントの構成のログ ファイル | `/var/opt/microsoft/omsconfig/omsconfig.log`

 トラブルシューティングでは、または GitHub の問題を送信する前に、ログ コレクター ツールを使用して重要なログを取得することをお勧めします。 ツールの詳細と実行方法については、[こちら](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)をご覧ください。

## <a name="important-configuration-files"></a>重要な構成ファイル

 Category | ファイルの場所
 ----- | -----
 syslog | `/etc/syslog-ng/syslog-ng.conf` または `/etc/rsyslog.conf` または `/etc/rsyslog.d/95-omsagent.conf`
 パフォーマンス、Nagios、Zabbix、Log Analytics の出力および汎用エージェント | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 追加の構成 | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >Azure portal でワークスペースに対する [Log Analytics の [詳細設定] の [データ] メニュー](../../azure-monitor/platform/agent-data-sources.md#configuring-data-sources)からコレクションを構成した場合、パフォーマンス カウンターおよび Syslog に関する構成ファイルの編集が上書きされます。 すべてのエージェントの構成を無効にするには、Log Analytics の **[詳細設定]** でコレクションを無効にします。1 つのエージェントの場合は、次を実行します。  
> `sudo su omsagent -c /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable`

## <a name="installation-error-codes"></a>インストール エラー コード

| エラー コード | 意味 |
| --- | --- |
| NOT_DEFINED | 必要な依存関係がインストールされていないため、auoms auditd プラグインはインストールされません | auoms のインストールが失敗しました。パッケージ auditd をインストールします。 |
| 2 | シェル バンドルに提供されたオプションが無効です。 使用方法については `sudo sh ./omsagent-*.universal*.sh --help` を実行してください |
| 3 | シェル バンドルにオプションが提供されていません。 使用方法については `sudo sh ./omsagent-*.universal*.sh --help` を実行してください。 |
| 4 | パッケージの種類またはプロキシの設定が無効です。omsagent-*rpm*.sh パッケージは RPM ベースのシステムにのみインストールでき、omsagent-*deb*.sh パッケージは Debian ベースのシステムにのみインストールできます。 [最新リリース](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux)のユニバーサル インストーラーを使うことをお勧めします。 また、プロキシの設定を確認してください。 |
| 5 | シェル バンドルはルートとして実行する必要があります。または、オンボード中に 403 エラーが返されました。 `sudo` を使用してコマンドを実行してください。 |
| 6 | パッケージのアーキテクチャが無効であるか、または、オンボード中に 200 エラーが返されました。omsagent-*x64.sh パッケージは 64 ビット システムにのみインストールでき、omsagent-* x86.sh パッケージは 32 ビット システムにのみインストールできます。 アーキテクチャに合った適切なパッケージを、[最新リリース](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest)からダウンロードしてください。 |
| 17 | OMS パッケージのインストールが失敗しました。 コマンド出力で根本的な障害を調べてください。 |
| 19 | OMI パッケージのインストールが失敗しました。 コマンド出力で根本的な障害を調べてください。 |
| 20 | SCX パッケージのインストールが失敗しました。 コマンド出力で根本的な障害を調べてください。 |
| 21 | プロバイダー キットのインストールが失敗しました。 コマンド出力で根本的な障害を調べてください。 |
| 22 | バンドルされているパッケージのインストールが失敗しました。 コマンド出力で根本的な障害を調べてください |
| 23 | SCX または OMI パッケージは既にインストールされています。 シェル バンドルをインストールするには、`--install` の代わりに `--upgrade` を使用してください。 |
| 30 | バンドルの内部エラー。 出力の詳細情報を添えて [GitHub の問題](https://github.com/Microsoft/OMS-Agent-for-Linux/issues)を提出してください。 |
| 55 | サポートされていない openssl バージョンか、Azure Monitor に接続できないか、または dpkg がロックされているか curl プログラムがありません。 |
| 61 | Python ctypes ライブラリが不足しています。 Python ctypes ライブラリまたはパッケージ (python-ctypes) をインストールします。 |
| 62 | tar プログラムがありません。tar をインストールしてください。 |
| 63 | sed プログラムがありません。sed をインストールしてください。 |
| 64 | curl プログラムがありません。curl をインストールしてください。 |
| 65 | gpg プログラムがありません。gpg をインストールしてください。 |

## <a name="onboarding-error-codes"></a>オンボードのエラー コード

| エラー コード | 意味 |
| --- | --- |
| 2 | omsadmin スクリプトに提供されたオプションが無効です。 使用方法については `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` を実行してください。 |
| 3 | omsadmin スクリプトに提供された構成が無効です。 使用方法については `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` を実行してください。 |
| 4 | omsadmin スクリプトに提供されたプロキシが無効です。 プロキシを確認し、[HTTP プロキシの使用方法に関するドキュメント](log-analytics-agent.md#network-firewall-requirements)を参照してください。 |
| 5 | Azure Monitor から受信された 403 HTTP エラー。 詳細については、omsadmin スクリプトの完全な出力を参照してください。 |
| 6 | Azure Monitor から受信された 200 以外の HTTP エラー。 詳細については、omsadmin スクリプトの完全な出力を参照してください。 |
| 7 | Azure Monitor に接続できません。 詳細については、omsadmin スクリプトの完全な出力を参照してください。 |
| 8 | Log Analytics ワークスペースへのオンボードでエラーが発生しました。 詳細については、omsadmin スクリプトの完全な出力を参照してください。 |
| 30 | スクリプトの内部エラーです。 出力の詳細情報を添えて [GitHub の問題](https://github.com/Microsoft/OMS-Agent-for-Linux/issues)を提出してください。 |
| 31 | エージェント ID の生成でエラーが発生しました。 出力の詳細情報を添えて [GitHub の問題](https://github.com/Microsoft/OMS-Agent-for-Linux/issues)を提出してください。 |
| 32 | 証明書の生成でエラーが発生しました。 詳細については、omsadmin スクリプトの完全な出力を参照してください。 |
| 33 | omsconfig のメタ構成の生成でエラーが発生しました。 出力の詳細情報を添えて [GitHub の問題](https://github.com/Microsoft/OMS-Agent-for-Linux/issues)を提出してください。 |
| 34 | メタ構成生成スクリプトが存在しません。 `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>` でオンボードを再試行してください。 |

## <a name="enable-debug-logging"></a>デバッグ ログの有効化
### <a name="oms-output-plugin-debug"></a>OMS 出力プラグインのデバッグ
 FluentD はプラグイン固有のログ レベルに対応しており、入力と出力に対して異なるログ レベルを指定できます。 OMS 出力に異なるログ レベルを指定するには、`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` で汎用エージェントの構成を編集します。  

 OMS 出力プラグインでは、構成ファイルの末尾の前にある `log_level` プロパティを `info` から `debug` に変更します。

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

デバッグ ログを使用すると、バッチ処理された Azure Monitor へのアップロードを、種類、データ項目の数、および送信にかかった時間で区切って表示できます。

*"デバッグを有効にしたログの例"* :

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>詳細出力
OMS 出力プラグインを使用する代わりに、データ項目を `stdout` に直接出力することもできます。この出力は、Linux 用 Log Analytics エージェントのログ ファイルで見ることができます。

`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` にある Log Analytics 汎用エージェントの構成ファイルで、OMS 出力プラグインをコメントアウトします。コメントアウトするには、各行の先頭に `#` を追加します。

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

出力プラグインの下で、各行の先頭にある `#` を削除することにより、次のセクションのコメントを解除します。

```
<match **>
  type stdout
</match>
```

## <a name="issue--unable-to-connect-through-proxy-to-azure-monitor"></a>問題: プロキシ経由で Azure Monitor に接続できない

### <a name="probable-causes"></a>考えられる原因
* オンボード中に指定されたプロキシが正しくありません
* Azure Monitor と Azure Automation サービスのエンドポイントがデータセンターでホワイトリストに登録されていない 

### <a name="resolution"></a>解決策
1. オプション `-v` が有効になった次のコマンドを使用して、Linux 用 Log Analytics エージェントで Azure Monitor に再オンボードします。 これにより、プロキシ経由で Azure Monitor に接続しているエージェントの詳細出力が可能になります。 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. 「[プロキシ設定を更新する](agent-manage.md#update-proxy-settings)」セクションを参照して、プロキシ サーバー経由で通信するようにエージェントを正しく構成したことを確認します。    
* 次の Azure Monitor エンドポイントがホワイトリストに登録されていることを再確認します。

    |エージェントのリソース| Port | Direction |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | ポート 443| 受信および送信 |  
    |*.oms.opinsights.azure.com | ポート 443| 受信および送信 |  
    |*.blob.core.windows.net | ポート 443| 受信および送信 |  
    |*.azure-automation.net | ポート 443| 受信および送信 | 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>問題: オンボードしようとすると 403 エラーが発生する

### <a name="probable-causes"></a>考えられる原因
* Linux サーバーの日付と時刻が正しくありません 
* 使用されているワークスペース ID とワークスペース キーが正しくありません

### <a name="resolution"></a>解決策

1. date コマンドを使用して、Linux サーバーの時刻を確認します。 時刻が現在の時刻より 15 分後または前である場合、オンボードは失敗します。 これを修正するには、Linux サーバーの日付やタイムゾーンを更新します。 
2. 最新バージョンの Linux 用 Log Analytics エージェントをインストールしたことを確認します。  最新バージョンでは、時間の差によってオンボードが失敗する場合に通知するようになりました。
3. この記事の前述のインストール手順に従って、正しいワークスペース ID とワークスペース キーを使用して再オンボードします。

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>問題: オンボードの直後にログ ファイルに 500 および 404 エラーが表示される
これは、Linux データを Log Analytics ワークスペースに最初にアップロードするときに発生する既知の問題です。 送信されているデータやサービス エクスペリエンスには影響しません。


## <a name="issue-you-see-omiagent-using-100-cpu"></a>問題: omiagent が 100% の CPU を使用している

### <a name="probable-causes"></a>考えられる原因
nss-pem パッケージ [v1.0.3-5.el7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-5.el7.x86_64.rpm.html) の回帰によって、重大なパフォーマンス上の問題が発生しました。これは、Redhat/Centos 7.x のディストリビューションで多く発生しています。 この問題の詳細については、「バグ [1667121 libcurl でのパフォーマンス回帰](https://bugzilla.redhat.com/show_bug.cgi?id=1667121)」というドキュメントを確認してください。

パフォーマンスに関連したバグは常に発生するわけではなく、再現するのは非常に困難です。 omiagent でそのような問題が発生する場合は、スクリプト omiHighCPUDiagnostics.sh を使用してください。これは、特定のしきい値を超えると、omiagent のスタック トレースを収集します。

1. スクリプトのダウンロード <br/>
`wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/LogCollector/source/omiHighCPUDiagnostics.sh`

2. 30% の CPU しきい値で 24 時間診断を実行します <br/>
`bash omiHighCPUDiagnostics.sh --runtime-in-min 1440 --cpu-threshold 30`

3. コールスタックが omiagent_trace ファイルにダンプされます。多くの Curl および NSS の関数呼び出しがある場合は、以下の解決手順を実行してください。

### <a name="resolution-step-by-step"></a>解決 (段階的)

1. nss-pem パッケージを [v1.0.3-5.el7_6.1](https://centos.pkgs.org/7/centos-updates-x86_64/nss-pem-1.0.3-5.el7_6.1.x86_64.rpm.html) にアップグレードします。 <br/>
`sudo yum upgrade nss-pem`

2. nss-pem がアップグレードに使用できない場合 (多くの場合、Centos で発生する) 場合は、curl を 7.29.0-46 にダウングレードします。 誤って "yum update" を実行すると、curl は 7.29.0-51 にアップグレードされ、問題が再度発生します。 <br/>
`sudo yum downgrade curl libcurl`

3. OMI を再起動します。 <br/>
`sudo scxadmin -restart`

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>問題: Azure portal にデータが表示されない

### <a name="probable-causes"></a>考えられる原因

- Azure Monitor へのオンボードに失敗しました
- Azure Monitor への接続がブロックされています
- Linux 用 Log Analytics エージェントのデータがバックアップされています

### <a name="resolution"></a>解決策
1. ファイル `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` が存在するかどうかをチェックして、Azure Monitor のオンボードに成功したかどうかを確認します。
2. `omsadmin.sh` コマンドライン命令を使用して再オンボードします。
3. プロキシを使用する場合は、上記のプロキシの解決手順を参照してください。
4. 場合によっては、Linux 用 Log Analytics エージェントがサービスと通信できないときに、エージェントのデータが最大バッファー サイズ (50 MB) までキューイングされます。 `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]` コマンドを実行して、エージェントを再起動する必要があります。 

    >[!NOTE]
    >この問題は、エージェント バージョン 1.1.0-28 以降で修正されます。


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>問題: 転送された Syslog メッセージが表示されない 

### <a name="probable-causes"></a>考えられる原因
* Linux サーバーに適用された構成で、送信されたファシリティやログ レベルの収集が許可されていません
* Syslog が Linux サーバーに正しく転送されていません
* 1 秒あたりに転送されるメッセージの数が多すぎて、Linux 用 Log Analytics エージェントの基本構成では処理できません

### <a name="resolution"></a>解決策
* Log Analytics ワークスペースでの Syslog の構成にすべてのファシリティと正しいログ レベルが含まれていることを確認します。 [Azure portal での Syslog コレクションの構成](../../azure-monitor/platform/data-sources-syslog.md#configure-syslog-in-the-azure-portal)に関するページを確認してください
* 転送されたメッセージをネイティブの syslog メッセージング デーモン (`rsyslog`、`syslog-ng`) が受信できることを確認します
* Syslog サーバーのファイアウォール設定をチェックして、メッセージがブロックされていないことを確認します
* `logger` コマンドを使用して、Log Analytics に対する Syslog メッセージをシミュレートします
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>問題: omsagent ログ ファイルで既に使用されている Errno アドレスを受け取る
omsagent.log で `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` が表示される場合。

### <a name="probable-causes"></a>考えられる原因
このエラーは、Linux Diagnostic Extension (LAD) が Log Analytics Linux VM 拡張機能と共にインストールされていて、omsagent として syslog データ コレクションと同じポートを使用していることを示します。

### <a name="resolution"></a>解決策
1. root として、次のコマンドを実行します (25224 は例であり、お使いの環境では LAD が異なるポート番号を使用している可能性があることに注意してください)。

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    その後、適切な `rsyslogd` または `syslog_ng` 構成ファイルを編集し、ポート 25229 に書き込むように LAD 関連の構成を変更する必要があります。

2. VM が `rsyslogd` を実行している場合、変更するファイルは `/etc/rsyslog.d/95-omsagent.conf` です (存在する場合。それ以外の場合は `/etc/rsyslog`)。 VM が `syslog_ng` を実行している場合、変更するファイルは `/etc/syslog-ng/syslog-ng.conf` です。
3. omsagent `sudo /opt/microsoft/omsagent/bin/service_control restart` を再起動します。
4. syslog サービスを開始しなおします。

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>問題: 消去オプションを使用して omsagent をアンインストールできない

### <a name="probable-causes"></a>考えられる原因

* Linux Diagnostic Extension がインストールされています
* Linux Diagnostic Extension をインストールしてアンインストールしましたが、omsagent が mdsd によって使用されていることを示すエラーがまだ表示され、削除できません。

### <a name="resolution"></a>解決策
1. Linux Diagnostic Extension (LAD) をアンインストールします。
2. Linux Diagnostic Extension のファイルが `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` および `/var/opt/microsoft/omsagent/LAD/` に存在する場合は、それらを削除します。

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>問題: Nagios データが表示されない 

### <a name="probable-causes"></a>考えられる原因
* omsagent のユーザーが、Nagios ログ ファイルの読み取りアクセス許可を持っていません
* Nagios のソースとフィルターが、omsagent.conf ファイルからコメント解除されていません

### <a name="resolution"></a>解決策
1. こちらの[説明](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts)に従って、Nagios ファイルから読み取るための omsagent ユーザーを追加します。
2. `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` にある Linux 用 Log Analytics エージェントの一般構成ファイルで、Nagios のソースとフィルターの**両方**がコメント解除されていることを確認します。

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

## <a name="issue-you-are-not-seeing-any-linux-data"></a>問題: Linux データが表示されない 

### <a name="probable-causes"></a>考えられる原因
* Azure Monitor へのオンボードに失敗しました
* Azure Monitor への接続がブロックされています
* 仮想マシンが再起動されました
* Linux 用 Log Analytics エージェント パッケージによってインストールされたものより新しいバージョンの OMI パッケージに手動でアップグレードされました
* DSC リソースにより、"*クラスが見つからない*" というエラーが `omsconfig.log` ログ ファイルに記録されています
* Log Analytics エージェントのデータがバックアップされています
* DSC ログの "*現在の構成が存在しません。-Path パラメーターで構成ファイルを指定して Start-DscConfiguration コマンドを実行し、現在の構成を先に作成します。* " `omsconfig.log` ログ ファイルに、`PerformRequiredConfigurationChecks` 操作に関するログ メッセージが存在しません。

### <a name="resolution"></a>解決策
1. auditd パッケージのようなすべての依存関係をインストールします。
2. ファイル `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` が存在するかどうかをチェックして、Azure Monitor へのオンボードに成功したかどうかを確認します。  成功しなかった場合は、omsadmin.sh コマンド ラインの[指示](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)を使用して再オンボードします。
4. プロキシを使用している場合は、上記のプロキシのトラブルシューティング手順を確認します。
5. 一部の Azure ディストリビューション システムでは、仮想マシンの再起動後に、omid OMI サーバー デーモンが開始しません。 これにより、Audit、ChangeTracking、または UpdateManagement ソリューション関連データが表示されません。 回避するには、`sudo /opt/omi/bin/service_control restart` を実行して omi サーバーを手動で開始します。
6. OMI パッケージを手動で新しいバージョンにアップグレードした後、Log Analytics エージェントが継続して機能するには、手動で再起動する必要があります。 この手順は、OMI サーバーがアップグレード後に自動的に起動しない一部のディストリビューションで必要です。 `sudo /opt/omi/bin/service_control restart` を実行して OMI を再起動します。
7. DSC リソースの "*クラスが見つからない*" というエラーが omsconfig.log に記録されている場合は、`sudo /opt/omi/bin/service_control restart` を実行します。
8. 場合によっては、Linux 用 Log Analytics エージェントが Azure Monitor に接続できないとき、エージェント上のデータが次のバッファー サイズ全体までバックアップされます。50 MB。 `/opt/microsoft/omsagent/bin/service_control restart` コマンドを実行して、エージェントを再起動する必要があります。

    >[!NOTE]
    >この問題は、エージェント バージョン 1.1.0-28 以降で修正されます
    >

* `PerformRequiredConfigurationChecks` 操作がシステムで定期的に実行されていることが `omsconfig.log` ログ ファイルで示されていない場合、cron ジョブ/サービスに問題がある可能性があります。 `/etc/cron.d/OMSConsistencyInvoker` に cron ジョブが存在することを確認します。 必要な場合は、次のコマンドを実行して cron ジョブを作成します。

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    また、cron サービスが実行されていることを確認します。 Debian、Ubuntu、SUSE では `service cron status` を使用して、または RHEL、CentOS、Oracle Linux では `service crond status` を使用して、このサービスの状態を確認できます。 サービスが存在しない場合は、以下のようにして、バイナリをインストールし、サービスを開始できます。

    **Ubuntu/Debian**

    ```
    # To Install the service binaries
    sudo apt-get install -y cron
    # To start the service
    sudo service cron start
    ```

    **SUSE**

    ```
    # To Install the service binaries
    sudo zypper in cron -y
    # To start the service
    sudo systemctl enable cron
    sudo systemctl start cron
    ```

    **RHEL/CeonOS**

    ```
    # To Install the service binaries
    sudo yum install -y crond
    # To start the service
    sudo service crond start
    ```

    **Oracle Linux**

    ```
    # To Install the service binaries
    sudo yum install -y cronie
    # To start the service
    sudo service crond start
    ```

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>問題: Syslog または Linux のパフォーマンス カウンターに対してポータルからコレクションを構成すると、設定が適用されない

### <a name="probable-causes"></a>考えられる原因
* Linux 用 Log Analytics エージェントで最新の構成が選択されていません
* ポータルで変更された設定が適用されていません

### <a name="resolution"></a>解決策
**背景知識:** `omsconfig` は Linux 用 Log Analytics エージェントの構成エージェントであり、5 分ごとに新しいポータル側構成を検索します。 その後、この構成が、/etc/opt/microsoft/omsagent/conf/omsagent.conf にある Linux 用 Log Analytics エージェント構成ファイルに適用されます。

* 場合によっては、Linux 用 Log Analytics エージェント構成エージェントがポータル構成サービスと通信できず、最新の構成が適用されないことがあります。
  1. `dpkg --list omsconfig` または `rpm -qi omsconfig` を実行して、`omsconfig` エージェントがインストールされていることを確認します。  インストールされていない場合は、Linux 用 Log Analytics エージェントの最新バージョンを再インストールします。

  2. コマンド `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` を実行して、`omsconfig` エージェントが Azure Monitor と通信できることを確認します。 このコマンドは、エージェントがサービスから受け取った構成を返します。この構成には、Syslog 設定、Linux のパフォーマンス カウンター、カスタム ログなどが含まれています。 このコマンドが失敗する場合は、`sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'` コマンドを実行します。 このコマンドは、omsconfig エージェントに強制的に Azure Monitor と通信して最新の構成を取得させます。

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>問題: カスタム ログ データが表示されない 

### <a name="probable-causes"></a>考えられる原因
* Azure Monitor へのオンボードに失敗しました。
* 設定 **[Apply the following configuration to my Linux Servers]\(次の構成を Linux サーバーに適用する\)** がオンになっていません。
* omsconfig がサービスから最新のカスタム ログ構成を取得していません。
* Linux 用 Log Analytics エージェントのユーザー `omsagent` は、アクセス許可が見つからないため、カスタム ログにアクセスできません。  次のエラーが表示されることがあります。
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Linux 用 Log Analytics エージェント バージョン 1.1.0-217 で修正された競合状態に関する既知の問題

### <a name="resolution"></a>解決策
1. ファイル `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` が存在するかどうかをチェックして、Azure Monitor へのオンボードに成功したことを確認します。 成功していない場合は、次のいずれかを行います。  

  1. omsadmin.sh コマンド ラインの[指示](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)を使用して再オンボードします。
  2. Azure portal の **[詳細設定]** で、設定 **[Apply the following configuration to my Linux Servers]\(次の構成を Linux サーバーに適用する\)** が有効になっていることを確認します。  

2. コマンド `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` を実行して、`omsconfig` エージェントが Azure Monitor と通信できることを確認します。  このコマンドは、エージェントがサービスから受け取った構成を返します。この構成には、Syslog 設定、Linux のパフォーマンス カウンター、カスタム ログなどが含まれています。 このコマンドが失敗する場合は、`sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py` コマンドを実行します。 このコマンドは、omsconfig エージェントに強制的に Azure Monitor と通信して最新の構成を取得させます。

**背景:** 特権ユーザー `root` として実行される Linux 用 Log Analytics エージェントの代わりに、エージェントは `omsagent` ユーザーとして実行されます。 ほとんどの場合、特定のファイルを読み取るためには、明示的なアクセス許可をユーザーに付与する必要があります。 `omsagent` ユーザーにアクセス許可を付与するには、次のコマンドを実行します。

1. `sudo usermod -a -G <GROUPNAME> <USERNAME>` で、`omsagent` ユーザーを特定のグループに追加します
2. `sudo chmod -R ugo+rx <FILE DIRECTORY>` で、必要なファイルへの汎用の読み取りアクセス許可を付与します

1\.1.0-217 より前のバージョンの Linux 用 Log Analytics エージェントには、競合状態に関する既知の問題があります。 最新のエージェントに更新した後、次のコマンドを実行して、出力プラグインの最新バージョンを取得します。`sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>問題: 新しいワークスペースに再オンボードしようとしている
エージェントを新しいワークスペースに再オンボードするときは、再オンボードの前に、Log Analytics エージェントの構成をクリーンアップする必要があります。 エージェントから古い構成をクリーンアップするには、`--purge` でシェル バンドルを実行します

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
または

```
sudo sh ./onboard_agent.sh --purge
```

`--purge` オプションを使用した後で、再オンボードを続行できます

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>Azure portal の Log Analytics エージェント拡張機能は、次のエラー状態でマークされます:"プロビジョニング失敗"

### <a name="probable-causes"></a>考えられる原因
* Log Analytics エージェントが、オペレーティング システムから削除されました
* Log Analytics エージェント サービスが、ダウン、無効、または未構成になっています

### <a name="resolution"></a>解決策 
問題を修正するには次の手順を実行します。
1. Azure portal から拡張機能を削除します。
2. [指示](../../azure-monitor/learn/quick-collect-linux-computer.md)に従ってエージェントをインストールします。
3. 次のコマンドを実行してエージェントを再起動します。`sudo /opt/microsoft/omsagent/bin/service_control restart`
* 数分待つと、プロビジョニングの状態が**プロビジョニング成功**に変わります。


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>問題: Log Analytics エージェントがオンデマンドでアップグレードする

### <a name="probable-causes"></a>考えられる原因

ホスト上の Log Analytics エージェント パッケージが期限切れです。

### <a name="resolution"></a>解決策 
問題を修正するには次の手順を実行します。

1. [こちらのページ](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/)で最新リリースを確認します。
2. インストール スクリプトをダウンロードします (例: 1.4.2-124)。

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. `sudo sh ./omsagent-*.universal.x64.sh --upgrade` を実行してパッケージをアップグレードします。
