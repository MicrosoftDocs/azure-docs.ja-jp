---
title: Azure Compute - Linux Diagnostic Extension |Microsoft ドキュメント
description: Azure Linux Diagnostic Extension (LAD) を構成して、Azure で実行中の Linux VM からメトリックとログ イベントを収集する方法。
services: virtual-machines-linux
author: abhijeetgaiha
manager: sankalpsoni
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/13/2018
ms.author: gwallace
ms.openlocfilehash: 0627361fdd4f94a329b08b184dbd542e1927af39
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871922"
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Linux Diagnostic Extension を使用して、メトリックとログを監視する

このドキュメントでは、Linux Diagnostic Extension のバージョン 3.0 以降について説明します。

> [!IMPORTANT]
> バージョン 2.3 以前については、[このドキュメント](../linux/classic/diagnostic-extension-v2.md)を参照してください。

## <a name="introduction"></a>はじめに

Linux Diagnostic Extension は、Microsoft Azure で実行中の Linux VM の正常性を監視するのに役立ちます。 次の機能が用意されています。

* VM からシステム パフォーマンス メトリックを収集し、指定されたストレージ アカウント内の特定のテーブルに格納します。
* Syslog からログ イベントを取得し、指定されたストレージ アカウント内の特定のテーブルに格納します。
* ユーザーは収集およびアップロードされたデータ メトリックをカスタマイズできます。
* ユーザーは収集およびアップロードされたイベントの syslog ファシリティと重大度レベルをカスタマイズできます。
* ユーザーは指定したログ ファイルを指定したストレージ テーブルにアップロードできます。
* 指定されたストレージ アカウント内の任意の EventHub エンドポイントおよび JSON 形式 の BLOB にメトリックとログ イベントを送信できるようにサポートしています。

この拡張機能は、両方の Azure デプロイメント モデルで動作します。

## <a name="installing-the-extension-in-your-vm"></a>VM への拡張機能のインストール

この拡張機能は、Azure PowerShell コマンドレット、Azure CLI スクリプト、ARM テンプレート、Azure portal のいずれかを使用して有効にできます。 詳細については、[拡張機能](features-linux.md)に関するページをご覧ください。

このインストール手順と[ダウンロード可能なサンプル構成](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json)により、LAD 3.0 は次のように構成されます。

* LAD 2.3 で提供されたものと同じメトリックをキャプチャし、保存します。
* LAD 3.0 の新機能である、便利な一連のファイル システム メトリックをキャプチャします。
* LAD 2.3 で有効になっている既定の syslog コレクションをキャプチャします。
* Azure ポータル エクスペリエンスを使用して、VM メトリックのチャート作成とアラート通知を行います。

ダウンロード可能な構成は単なる例です。自分のニーズに合わせて変更してください。

### <a name="prerequisites"></a>前提条件

* **Azure Linux エージェント バージョン 2.2.0 以降**。 ほとんどの Azure VM Linux ギャラリー イメージには、バージョン 2.2.7 以降が含まれています。 `/usr/sbin/waagent -version` を実行して、VM にインストールされているバージョンを確認します。 VM で古いバージョンのゲスト エージェントを実行している場合は、[次の手順](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent)に従って更新します。
* **Azure CLI**。 ご使用のマシンに [Azure CLI 環境をセットアップ](https://docs.microsoft.com/cli/azure/install-azure-cli)します。
* wget コマンド。まだ持っていない場合は `sudo apt-get install wget` を実行します。
* 既存の Azure サブスクリプションと、データをその中に格納するための既存のストレージ アカウント。
* サポートされている Linux ディストリビューションのリストは、 https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic#supported-linux-distributions にあります。

### <a name="sample-installation"></a>サンプル インストール

最初の 3 行に適切なパラメーターを入力し、このスクリプトを root として実行します。

```bash
# Set your Azure VM diagnostic parameters correctly below
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finallly tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

サンプル構成の URL と内容は変更される可能性があります。 ポータル設定の JSON ファイルのコピーをダウンロードし、必要に応じてカスタマイズします。 作成するテンプレートやオートメーションは、その都度 URL をダウンロードするのではなく、独自のコピーを使用する必要があります。

### <a name="updating-the-extension-settings"></a>拡張機能の設定の更新

Protected 設定または Public 設定を変更した後、同じコマンドを実行して、VM にそれらを展開します。 設定に変更を加えると、更新された設定が拡張機能に送信されます。 LAD は構成を再読み込みして、再起動します。

### <a name="migration-from-previous-versions-of-the-extension"></a>以前のバージョンの拡張機能からの移行

拡張機能の最新バージョンは **3.0** です。 **以前のバージョン (2.x) は非推奨となり、2018 年 7 月 31 日以降は非公開になる可能性があります**。

> [!IMPORTANT]
> この拡張機能では、構成の面で大幅な変更点が導入されます。 その変更点の 1 つは、拡張機能のセキュリティを向上させるために行われました。結果として、2.x との下位互換性を維持できなくなりました。 また、この拡張機能の Extension Publisher は、2.x バージョンのパブリッシャーとは異なります。
>
> 2\.x からこの新しいバージョンの拡張機能に移行するには、 (古いパブリッシャー名で) 古い拡張機能をアンインストールしてから、バージョン 3 の拡張機能をインストールする必要があります。

推奨事項:

* 自動マイナー バージョン アップグレードを有効にして、拡張機能をインストールします。
  * Azure XPLAT CLI または Powershell を使用して拡張機能をインストールする場合は、クラシック デプロイ モデルの VM では、バージョンを「3. *」に指定します。
  * Azure Resource Manager デプロイ モデルの VM では、VM デプロイ テンプレートに「"autoUpgradeMinorVersion": true」を含めます。
* LAD 3.0 では、新規/別のストレージ アカウントを使用します。 LAD 2.3 と LAD 3.0 の間には小さな非互換性がいくつかあり、アカウントの共有には手間がかかります。
  * LAD 3.0 では、syslog イベントを別の名前のテーブルに格納します。
  * LAD 3.0 では、`builtin` メトリックの counterSpecifier 文字列が異なります。

## <a name="protected-settings"></a>保護された設定

この一連の構成情報には、ストレージ資格情報など、人目に付かないように保護する必要がある機密情報が含まれています。 これらの設定は、暗号化された形式で拡張機能に転送され、格納されます。

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

EnableAdfsAuthentication | 値
---- | -----
storageAccountName | 拡張機能によってデータが書き込まれるストレージ アカウントの名前。
storageAccountEndPoint | (省略可能) ストレージ アカウントが存在するクラウドを識別するエンドポイント。 この設定がない場合、LAD の既定値は Azure パブリック クラウド `https://core.windows.net` になります。 Azure Germany、Azure Government、Azure China でストレージ アカウントを使用するには、この値を適切に設定します。
storageAccountSasToken | BLOB および Table service (`ss='bt'`) の[アカウント SAS トークン](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/)。追加、作成、一覧表示、更新、書き込み権限 (`sp='acluw'`) を付与するコンテナーおよびオブジェクト (`srt='co'`) に適用できます。 先頭の疑問符 (?) を*含めないで*ください。
mdsdHttpProxy | (省略可能) 指定されたストレージ アカウントとエンドポイントに拡張機能が接続するために必要な HTTP プロキシ情報。
sinksConfig | (省略可能) メトリックとイベントの配信が可能な代替宛先の詳細。 拡張機能でサポートされている各データ シンクの詳細については、以降のセクションで説明します。


> [!NOTE]
> Azure 展開テンプレートを使用して拡張機能を展開する場合は、事前にストレージ アカウントと SAS トークンを作成し、テンプレートに渡す必要があります。 VM、ストレージ アカウントの展開と拡張機能の構成を、1 つのテンプレート内で行うことはできません。 現時点では、テンプレート内での SAS トークンの作成はサポートされていません。

必要な SAS トークンを Azure ポータルで簡単に構築できます。

1. 拡張機能の書き込み先となる汎用ストレージ アカウントを選択します
1. 左側のメニューの [設定] から [Shared access signature] を選択します
1. 前述のように、適切なセクションを作成します
1. [SAS の生成] ボタンをクリックします。

![image](./media/diagnostics-linux/make_sas.png)

生成された SAS を storageAccountSasToken フィールドにコピーします。先頭の疑問符 ("?") を削除します。

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

この省略可能なセクションでは、拡張機能が収集した情報を送信する追加の宛先を定義します。 "シンク" 配列には、追加のデータ シンクごとのオブジェクトが含まれています。 "Type" 属性は、オブジェクトの他の属性を決定します。

要素 | 値
------- | -----
名前 | このシンクを拡張機能構成の他の場所で参照するために使用される文字列。
type | 定義されているシンクの型。 この型のインスタンス内のその他の値 (存在する場合) を決定します。

Linux Diagnostic Extension のバージョン 3.0 では、EventHub と JsonBlob という 2 つのシンク型がサポートされています。

#### <a name="the-eventhub-sink"></a>EventHub シンク

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

"sasURL" エントリには、データの発行先となる Event Hub の完全な URL (SAS トークンを含む) が含まれています。 LAD には、送信要求を有効にするポリシーに名前付けする SAS が必要です。 例:

* `contosohub` という名前の Event Hubs 名前空間を作成します
* `syslogmsgs` という名前の Event Hub を名前空間に作成します
* 送信要求を有効にする `writer` という名前の共有アクセス ポリシーを Event Hub に作成します

2018 年 1 月 1 日の深夜 0 時 (UTC) までに SAS を作成した場合、 sasURL 値は次のようになります。

```url
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Event Hubs 用の SAS トークン生成の詳細については、[この Web ページ](../../event-hubs/event-hubs-authentication-and-security-model-overview.md)を参照してください。

#### <a name="the-jsonblob-sink"></a>JsonBlob シンク

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

JsonBlob シンクに転送されたデータは、Azure ストレージ内の BLOB に格納されます。 LAD の各インスタンスは、各シンク名に対して 1 時間ごとに BLOB を作成します。 各 BLOB には、オブジェクトの構文的に有効な JSON 配列が必ず含まれています。 新しいエントリは、配列にアトミックに追加されます。 BLOB はシンクと同じ名前のコンテナーに格納されます。 BLOB コンテナー名に対する Azure ストレージ ルールは、JsonBlob シンクの名前に適用されます。3〜63 文字で、小文字の英数の ASCII 文字またはダッシュを使用します。

## <a name="public-settings"></a>Public 設定

この構造体には、拡張機能によって収集された情報を制御するさまざまな設定ブロックが含まれています。 各設定は省略可能です。 `ladCfg` を指定した場合、`StorageAccount` も指定する必要があります。

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

要素 | 値
------- | -----
StorageAccount | 拡張機能によってデータが書き込まれるストレージ アカウントの名前。 [保護された設定](#protected-settings)で指定されている名前と同じにする必要があります。
mdsdHttpProxy | (省略可能) [保護された設定](#protected-settings)での場合と同じです。 プライベート値が設定されている場合、パブリック値はこれにオーバーライドされます。 [保護された設定](#protected-settings)に、パスワードなどの秘密を含むプロキシ設定を配置します。

残りの要素については、以降のセクションで詳しく説明します。

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

この省略可能な構造体は、Azure Metrics サービスおよび他のデータ シンクに配信するためのメトリックとログの収集を制御します。 `performanceCounters` または `syslogEvents` のどちらか、またはその両方を指定する必要があります。 `metrics` 構造体を指定する必要があります。

要素 | 値
------- | -----
eventVolume | (省略可能) ストレージ テーブル内で作成されるパーティションの数を制御します。 `"Large"`、`"Medium"`、`"Small"` のいずれかである必要があります。 指定しない場合は、既定値の `"Medium"` が使用されます。
sampleRateInSeconds | (省略可能) 生の (未集計) メトリックを収集する既定の間隔。 サポートされている最小サンプル レートは 15 秒です。 指定しない場合は、既定値の `15` が使用されます。

#### <a name="metrics"></a>metrics

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

要素 | 値
------- | -----
resourceId | VM または VM が所属する仮想マシン スケール セットの Azure Resource Manager リソース ID。 この設定は、JsonBlob シンクが構成で使用されている場合にも指定する必要があります。
scheduledTransferPeriod | 集計メトリックが計算され、Azure Metrics に転送される頻度。これは、IS 8601 の時間間隔として表されます。 最小の転送間隔は 60 秒、つまり、PT1M です。 少なくとも 1 つの scheduledTransferPeriod を指定する必要があります。

performanceCounters セクションで指定されたメトリックのサンプルは、15 秒ごと、またはカウンターに明示的に定義されたサンプル レートで収集されます。 複数の scheduledTransferPeriod 頻度が表示されている場合 (この例のように)、各集計は独立して計算されます。

#### <a name="performancecounters"></a>performanceCounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

この省略可能なセクションでは、メトリックの収集を制御します。 生のサンプルは、[scheduledTransferPeriod](#metrics) ごとに集計され、次の値を生成します。

* mean
* minimum
* maximum
* 最後に収集された値
* 集計を計算するために使用された生のサンプルの数

要素 | 値
------- | -----
sinks | (省略可能) LAD が集計したメトリック結果を送信するシンクの名前をコンマで区切ったリスト。 集計されたすべてのメトリックは、一覧表示された各シンクに発行されます。 [sinksConfig](#sinksconfig) を参照してください。 例: `"EHsink1, myjsonsink"`.
type | メトリックの実際のプロバイダーを識別します。
class | "counter" とともに、プロバイダーの名前空間内の特定のメトリックを識別します。
counter | "class" とともに、プロバイダーの名前空間内の特定のメトリックを識別します。
counterSpecifier | Azure Metrics 名前空間内で特定のメトリックを識別します。
condition | (省略可能) メトリックを適用するオブジェクトの特定のインスタンスを選択するか、そのオブジェクトのすべてのインスタンスの集計を選択します。 詳細については、`builtin` メトリックの定義を参照してください。
sampleRate | このメトリックの生のサンプルが収集されるレートを設定する IS 8601 間隔。 設定されていない場合、収集間隔は [sampleRateInSeconds](#ladcfg) 値によって設定されます。 サポートされている最小サンプル レートは 15 秒 (PT15S) です。
unit | 値は、"Count"、"Bytes"、"Seconds"、"Percent"、"CountPerSecond"、"BytesPerSecond"、"Millisecond" のいずれかの文字列である必要があります。 メトリックの単位を定義します。 収集されたデータのコンシューマーは、収集されたデータ値がこの単位に一致することを期待しています。 LAD はこのフィールドを無視します。
displayName | Azure メトリックスのこのデータに添付されるラベル (関連付けられているロケール設定で指定された言語で記述) です。 LAD はこのフィールドを無視します。

counterSpecifier は任意の識別子です。 Azure ポータルのチャート作成およびアラート通知機能のようなメトリックのコンシューマーは、メトリックまたはメトリックのインスタンスを識別する "キー" として counterSpecifier を使用します。 `builtin` メトリックについては、`/builtin/` で始まる counterSpecifier 値を使用することをお勧めします。 メトリックの特定のインスタンスを収集する場合は、counterSpecifier 値にインスタンスの識別子を添付することをお勧めします。 次に例をいくつか示します。

* `/builtin/Processor/PercentIdleTime` - すべての vCPU の平均アイドル時間
* `/builtin/Disk/FreeSpace(/mnt)` - /mnt ファイル システム用の空き領域
* `/builtin/Disk/FreeSpace` - マウントされたすべてのファイル システムの平均空き領域

LAD でも Azure ポータル でも、counterSpecifier の値がいずれかのパターンに一致することは期待されていません。 counterSpecifier 値の構成は一貫した方法で行ってください。

`performanceCounters` を指定すると、LAD は常に Azure ストレージのテーブルにデータを書き込みます。 同じデータを JSON BLOB や Event Hub に書き込むことはできますが、テーブルへのデータ格納を無効にすることはできません。 同じストレージ アカウント名とエンドポイントを使用するように設定されたすべての診断拡張機能のインスタンスは、メトリックとログを同じテーブルに追加します。 あまりにも多くの VM が同じテーブル パーティションに書き込んでいる場合、Azure はそのパーティションへの書き込みをスロットルできます。 eventVolume 設定によって、エントリは 1 (小)、10 (中)、100 (大) の異なる数のパーティションに分散されます。 トラフィックがスロットルされないようにするには、通常、"中" で十分です。 Azure ポータルの Azure メトリックス機能は、このテーブルのデータを使用して、グラフを生成したり、アラートをトリガーしたりします。 テーブル名は、次の文字列を連結したものです。

* `WADMetrics`
* テーブルに格納された集計値の "scheduledTransferPeriod"
* `P10DV2S`
* 10 日ごとに変更される "YYYYMMDD" 形式の日付

例は、`WADMetricsPT1HP10DV2S20170410` や `WADMetricsPT1MP10DV2S20170609` のようになります。

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

この省略可能なセクションでは、syslog からのログ イベントの収集を制御します。 セクションを省略すると、syslog イベントはまったくキャプチャされません。

syslogEventConfiguration コレクションには、対象の syslog ファシリティごとに 1 つのエントリがあります。 特定のファシリティで minSeverity が "NONE" の場合、またはそのファシリティが要素にまったく表示されない場合、そのファシリティからのイベントはキャプチャされません。

要素 | 値
------- | -----
sinks | 個々のログ イベントの発行先となるシンクの名前をコンマで区切ったリスト。 syslogEventConfiguration の制限に一致するすべてのログ イベントが一覧表示されている各シンクに発行されます。 例:"EHforsyslog"
facilityName | syslog ファシリティ名 ("LOG\_USER" や "LOG\_LOCAL0" など)。 完全なリストについては、[syslog man ページ](http://man7.org/linux/man-pages/man3/syslog.3.html)の「facility」セクションを参照してください。
minSeverity | Syslog の重大度レベル ("LOG\_ERR" や "LOG\_INFO" など)。 完全なリストについては、[syslog man ページ](http://man7.org/linux/man-pages/man3/syslog.3.html)の「level」セクションを参照してください。 拡張機能は、指定されたレベル以上のファシリティに送信されるイベントをキャプチャします。

`syslogEvents` を指定すると、LAD は常に Azure ストレージのテーブルにデータを書き込みます。 同じデータを JSON BLOB や Event Hub に書き込むことはできますが、テーブルへのデータ格納を無効にすることはできません。 このテーブルのパーティション分割動作は、`performanceCounters` についての説明と同じです。 テーブル名は、次の文字列を連結したものです。

* `LinuxSyslog`
* 10 日ごとに変更される "YYYYMMDD" 形式の日付

例は、`LinuxSyslog20170410` や `LinuxSyslog20170609` のようになります。

### <a name="perfcfg"></a>perfCfg

この省略可能なセクションは、任意の [OMI](https://github.com/Microsoft/omi) クエリの実行を制御します。

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

要素 | 値
------- | -----
namespace | (省略可能) クエリが実行される OMI 名前空間。 指定されていない場合、既定値は "root/scx" で、[System Center クロスプラットフォーム プロバイダー](https://scx.codeplex.com/wikipage?title=xplatproviders&referringTitle=Documentation)によって実装されます。
query | 実行される OMI クエリ。
table | (省略可能) 指定されたストレージ アカウントの Azure ストレージ テーブル ([保護された設定](#protected-settings)を参照してください)。
frequency | (省略可能) クエリの実行間隔 (秒) 。 既定値は 300 (5 分) です。最小値は 15 秒です。
sinks | (省略可能) メトリック結果の生のサンプルが発行される追加のシンクの名前をコンマで区切ったリスト。 これらの生のサンプルの集計は、拡張機能または Azure メトリックスによって計算されません。

"table" または "sinks" のどちらか、またはその両方を指定する必要があります。

### <a name="filelogs"></a>fileLogs

ログ ファイルのキャプチャを制御します。 LAD は、新しいテキスト行がファイルに書き込まれるときにそれをキャプチャし、テーブル行や任意の指定されたシンク (JsonBlob または EventHub) に書き込みます。

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

要素 | 値
------- | -----
file | 監視され、キャプチャされるログ ファイルの完全なパス名。 パス名は単一のファイルを指定する必要があります。ディレクトリを指定したり、ワイルドカードを含めたりすることはできません。
table | (省略可能) 指定されたストレージ アカウント (保護された構成で指定) の Azure ストレージ テーブル。file の "末尾" から新しい行が書き込まれます。
sinks | (省略可能) ログ行が送信される追加のシンクの名前をコンマで区切ったリスト。

"table" または "sinks" のどちらか、またはその両方を指定する必要があります。

## <a name="metrics-supported-by-the-builtin-provider"></a>組み込みプロバイダーでサポートされているメトリック

組み込みメトリック プロバイダーは、幅広いユーザーにとって最も興味深いメトリックのソースです。 これらのメトリックは、大きく 5 つのクラスに分類されます。

* プロセッサ
* メモリ
* ネットワーク
* ファイルシステム
* ディスク

### <a name="builtin-metrics-for-the-processor-class"></a>"プロセッサ" クラスの組み込みのメトリック

"プロセッサ" クラスのメトリックは、VM のプロセッサ使用率に関する情報を提供します。 パーセンテージを集計すると、結果はすべての CPU の平均になります。 2 つの vCPU の VM で 1 つの vCPU が 100% ビジーで、もう 1 つのコアが 100% アイドル状態だった場合、報告 PercentIdleTime は 50 になります。 各 vCPU が同じ期間に 50% ビジーだった場合、報告された結果はやはり 50 になります。 4 つの vCPU の VM で 1 つのコアが 100% ビジーで、他の vCPU がアイドル状態だった場合、報告 PercentIdleTime は 75 になります。

counter | 意味
------- | -------
PercentIdleTime | 集計ウィンドウ中にプロセッサがカーネル アイドル ループを実行していた時間の割合
PercentProcessorTime | 非アイドルのスレッドを実行している時間の割合
PercentIOWaitTime | IO 操作が完了するのを待つ時間の割合
PercentInterruptTime | ハードウェア/ソフトウェア割り込みと DPC (遅延プロシージャ呼び出し) を実行する時間の割合
PercentUserTime | 集計ウィンドウ中の非アイドル時間のうち、ユーザーが通常の優先度でより多く消費した時間の割合
PercentNiceTime | 非アイドル時間のうち、低下した (適した) 優先度で消費された割合
PercentPrivilegedTime | 非アイドル時間のうち、特権 (カーネル) モードで消費された割合

最初の 4 つのカウンターの合計は 100% となる必要があります。 最後の 3 つカウンターの合計も 100% になります。PercentProcessorTime、PercentIOWaitTime、PercentInterruptTime の合計を細分したものが、それらのカウンターです。

すべてのプロセッサで集計された単一のメトリックを取得するには、`"condition": "IsAggregate=TRUE"` のように設定します。 4 つの vCPU の VM の 2 番目の論理プロセッサなど、特定のプロセッサのメトリックを取得するには、`"condition": "Name=\\"1\\""` のように設定します。 論理プロセッサ番号は、 `[0..n-1]` の範囲内です。

### <a name="builtin-metrics-for-the-memory-class"></a>"メモリ" クラスの組み込みのメトリック

"メモリ" クラスのメトリックは、メモリの使用率、ページング、スワップに関する情報を提供します。

counter | 意味
------- | -------
AvailableMemory | 使用可能な物理メモリ (MiB)
PercentAvailableMemory | 合計メモリに対する使用可能な物理メモリの割合
UsedMemory | 使用中の物理メモリ (MiB)
PercentUsedMemory | 合計メモリに対する使用中の物理メモリの割合
PagesPerSec | 合計ページング (読み取り/書き込み)
PagesReadPerSec | バッキング ストアから読み取ったページ (スワップ ファイル、プログラム ファイル、マップ済みファイルなど)
PagesWrittenPerSec | バッキング ストアに書き込まれたページ (スワップ ファイル、マップ済みファイルなど)
AvailableSwap | 未使用のスワップ領域 (MiB)
PercentAvailableSwap | 合計スワップに対する未使用のスワップ領域の割合
UsedSwap | 使用中のスワップ領域 (MiB)
PercentUsedSwap | 合計スワップに対する使用中のスワップ領域の割合

このメトリックのクラスには、1 つのインスタンスしかありません。 "condition" 属性は有用な設定を持たず、省略する必要があります。

### <a name="builtin-metrics-for-the-network-class"></a>"ネットワーク" クラスの組み込みのメトリック

"ネットワーク" クラスのメトリックは、起動後の個々のネットワーク インターフェイス上のネットワーク アクティビティに関する情報を提供します。 LAD は、ホスト メトリックから取得できる帯域幅メトリックを公開しません。

counter | 意味
------- | -------
BytesTransmitted | 起動後に送信された合計バイト数
BytesReceived | 起動後に受信した合計バイト数
BytesTotal | 起動後に送受信された合計バイト数
PacketsTransmitted | 起動後に送信された合計パケット数
PacketsReceived | 起動後に受信した合計パケット数
TotalRxErrors | 起動後の受信エラー数
TotalTxErrors | 起動後の送信エラー数
TotalCollisions | 起動後にネットワーク ポートによって報告された競合の数

 このクラスはインスタンス化されていますが、LAD は、すべてのネットワーク デバイスから集計されたネットワーク メトリックのキャプチャをサポートしていません。 eth0 などの特定のインターフェイスのメトリックを取得するには、`"condition": "InstanceID=\\"eth0\\""` のように設定します。

### <a name="builtin-metrics-for-the-filesystem-class"></a>"ファイルシステム" クラスの組み込みメトリック

"ファイルシステム" クラスのメトリックは、ファイル システムの使用状況に関する情報を提供します。 絶対値と割合の値は、(root ではなく) 通常のユーザーに表示されるように報告されます。

カウンター | 意味
------- | -------
FreeSpace | 使用可能なディスク領域 (バイト単位)
UsedSpace | 使用済みディスク領域 (バイト単位)
PercentFreeSpace | 空き領域の割合
PercentUsedSpace | 使用領域の割合
PercentFreeInodes | 未使用 inode の割合
PercentUsedInodes | すべてのファイル システム全体で合計された割り当て済み (使用中) inode の割合
BytesReadPerSecond | 1 秒あたりの読み取りバイト数
BytesWrittenPerSecond | 1 秒あたりの書き込みバイト数
BytesPerSecond | 1 秒あたりの読み取りまたは書き込みバイト数
ReadsPerSecond | 1 秒あたりの読み取り操作
WritesPerSecond | 1 秒あたりの書き込み操作
TransfersPerSecond | 1 秒あたりの読み取りまたは書き込み操作

すべてのファイル システムの集計値は、`"condition": "IsAggregate=True"` のように設定すると取得できます。 "/mnt" のような特定のマウントされたファイル システムの値は、`"condition": 'Name="/mnt"'` のように設定すると取得できます。 

**注**: JSON ではなく Azure Portal を使用する場合、正しい条件フィールド形式は Name='/mnt' になります。

### <a name="builtin-metrics-for-the-disk-class"></a>"ディスク" クラスの組み込みメトリック

"ディスク" クラスのメトリックは、ディスク デバイスの使用状況に関する情報を提供します。 これらの統計情報は、ドライブ全体に適用されます。 デバイス上に複数のファイル システムが存在する場合、そのデバイスのカウンターは、実際にはファイル システム全体で集計されます。

counter | 意味
------- | -------
ReadsPerSecond | 1 秒あたりの読み取り操作
WritesPerSecond | 1 秒あたりの書き込み操作
TransfersPerSecond | 1 秒あたりの合計操作数
AverageReadTime | 読み取り操作あたりの平均秒数
AverageWriteTime | 書き込み操作あたりの平均秒数
AverageTransferTime | 操作あたりの平均秒数
AverageDiskQueueLength | キューに登録済みのディスク操作数の平均値
ReadBytesPerSecond | 1 秒あたりの読み取りバイト数
WriteBytesPerSecond | 1 秒あたりの書き込みバイト数
BytesPerSecond | 1 秒あたりの読み取りまたは書き込みバイト数

すべてのディスクの集計値は、`"condition": "IsAggregate=True"` のように設定すると取得できます。 特定のデバイス (たとえば、dev/sdf1) の情報を取得するには、`"condition": "Name=\\"/dev/sdf1\\""` のように設定します。

## <a name="installing-and-configuring-lad-30-via-cli"></a>CLI を経由した LAD 3.0 のインストールと設定

保護された設定が PrivateConfig.json ファイルにあり、パブリック構成情報が PublicConfig.json にあると仮定して、このコマンドを実行します。

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

このコマンドは、Azure CLI の Azure Resource Management モード (arm) を使用していることを前提としています。 クラシック デプロイ モデル (ASM) VM 用に LAD を構成するには、"asm" モード (`azure config mode asm`) に切り替え、コマンド内のリソース グループ名を省略します。 詳細については、[クロスプラットフォーム CLI ドキュメント](https://docs.microsoft.com/azure/xplat-cli-connect)をご覧ください。

## <a name="an-example-lad-30-configuration"></a>LAD 3.0 の構成例

前述の定義に基づき、以下に LAD 3.0 拡張機能の構成例を示し、説明を加えます。 このサンプルを特定のケースに適用するには、独自のストレージ アカウント名、アカウント SAS トークン、EventHubs SAS トークンを使用する必要があります。

### <a name="privateconfigjson"></a>PrivateConfig.json

これらのプライベート設定では、次の項目を構成します。

* ストレージ アカウント
* 一致するアカウント SAS トークン
* いくつかのシンク ( SAS トークンを持つ JsonBlob または EventHubs)

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="publicconfigjson"></a>PublicConfig.json

これらの Public 設定により、LAD は次の操作を行います。

* percent-processor-time メトリックと used-disk-space メトリックを `WADMetrics*` テーブルにアップロードする
* Syslog ファシリティ "user" と重大度 "info" からのメッセージを `LinuxSyslog*` テーブルにアップロードする
* 生の OMI クエリ結果 (PercentProcessorTime と PercentIdleTime) を指定された `LinuxCPU` テーブルにアップロードする
* ファイル `/var/log/myladtestlog` の追加行を `MyLadTestLog` テーブルにアップロードする

いずれの場合も、データは次の場所にもアップロードされます。

* Azure BLOB ストレージ (コンテナー名は JsonBlob シンクで定義されているとおりです)
* EventHubs エンドポイント (EventHubs シンクで指定)

```json
{
  "StorageAccount": "yourdiagstgacct",
  "sampleRateInSeconds": 15,
  "ladCfg": {
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

構成の `resourceId` は、VM または仮想マシン スケール セットのものと一致する必要があります。

* Azure プラットフォームのメトリックによるチャート作成およびアラート通知では、作業中の VM の resourceId が認識されています。 resourceId がルックアップ キーとして使用され、VM のデータが検索されます。
* Azure 自動スケールを使用する場合、自動スケール構成の resourceId は LAD で使用する resourceId と一致する必要があります。
* resourceId は、LAD が書き込んだ JsonBlob の名前に組み込まれています。

## <a name="view-your-data"></a>データの表示

Azure ポータルを使用して、パフォーマンス データを表示したり、アラートを設定したりします。

![image](./media/diagnostics-linux/graph_metrics.png)

`performanceCounters` データは常に Azure Storage テーブルに格納されます。 Azure Storage API は、さまざまな言語とプラットフォームで利用できます。

JsonBlob シンクに送信されるデータは、[保護された設定](#protected-settings)で指定されたストレージ アカウント内の BLOB に格納されます。 Azure Blob Storage API を使用すると、BLOB データを使用できます。

さらに、次の UI ツールを使用して、Azure Storage のデータにアクセスすることもできます。

* Visual Studio のサーバー エクスプローラー。
* [Microsoft Azure ストレージ エクスプローラー](https://azurestorageexplorer.codeplex.com/ "Azure ストレージ エクスプローラー")。

この Microsoft Azure Storage エクスプ ローラー セッションのスナップショットは、テスト VM 上で正しく構成された LAD 3.0 拡張機能から生成された Azure Storage テーブルとコンテナーが表示されています。 イメージは [サンプル LAD 3.0 構成](#an-example-lad-30-configuration)と正確には一致しません。

![image](./media/diagnostics-linux/stg_explorer.png)

EventHubs エンドポイントに発行されたメッセージを使用する方法については、関連する [EventHubs ドキュメント](../../event-hubs/event-hubs-what-is-event-hubs.md)を参照してください。

## <a name="next-steps"></a>次の手順

* 収集するメトリックのメトリック アラートを [Azure Monitor](../../monitoring-and-diagnostics/insights-alerts-portal.md) で作成します。
* メトリックの[監視グラフ](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)を作成します。
* メトリックを使用して[仮想マシン スケール セットを作成](../linux/tutorial-create-vmss.md)し、自動スケールを制御する方法について説明します。
