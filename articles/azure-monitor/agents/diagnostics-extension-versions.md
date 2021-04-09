---
title: Windows Azure Diagnostics 拡張機能 (WAD) の構成スキーマのバージョン履歴
description: Azure Virtual Machines、VM Scale Sets、Service Fabric、および Cloud Services のパフォーマンス カウンターの収集に関連しています。
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/29/2020
ms.openlocfilehash: 57ba1c760fe8919fff95493c5c81da4368894f73
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101719816"
---
# <a name="windows-azure-diagnostics-extension-wad-configuration-schema-versions-and-history"></a>Windows Azure Diagnostics 拡張機能 (WAD) の構成スキーマのバージョンと履歴
この記事では、Microsoft Azure SDK の一部として付属している、[Windows 用の Azure Diagnostics 拡張機能 (WAD)](diagnostics-extension-overview.md) スキーマ バージョンのバージョン履歴を提供します。  


## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Azure SDK のバージョンと診断のバージョンの一覧  

|Azure SDK のバージョン | 診断拡張機能のバージョン | モデル|  
|------------------|-------------------------------|------|  
|1.x               |1.0                            |プラグイン|  
|2.0 ～ 2.4         |1.0                            |プラグイン|  
|2.5               |1.2                            |拡張機能|  
|2.6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2.8               |1.5                            |"|  
|2.9               |1.6                            |"|
|2.96              |1.7                            |"|
|2.96              |1.8                            |"|
|2.96              |1.8.1                          |"|
|2.96              |1.9                            |"|
|2.96              |1.11                           |"|


 Azure Diagnostics バージョン 1.0 は最初はプラグイン モデルとして付属しており、Azure SDK をインストールすると、その SDK に付属する Azure Diagnostics バージョンを入手できました。  

 SDK 2.5 (診断バージョン 1.2) 以降、Azure Diagnostics は拡張機能モデルに移行しています。 新しい機能を利用するツールは新しい Azure SDK でのみ使用できましたが、Azure Diagnostics を使用するサービスは、Azure から最新バージョンを直接選択できます。 たとえば、まだ SDK 2.5 を使用しているユーザーは、新しい機能を使用しているかどうかに関係なく、前の表に示されている最新バージョンが読み込むことができます。  

## <a name="schemas-index"></a>スキーマのインデックス  
異なるバージョンの Azure Diagnostics は、異なる構成スキーマを使用します。 スキーマ 1.0 と 1.2 は非推奨になりました。 バージョン 1.3 以降の詳細については、「[Microsoft Azure Diagnostics の 1.3 以降の構成スキーマ](diagnostics-extension-schema-windows.md)」を参照してください。  

## <a name="version-history"></a>バージョン履歴

### <a name="diagnostics-extension-111"></a>診断拡張機能 1.11
Azure Monitor シンクに対応しました。 このシンクはパフォーマンス カウンターにのみ適用されます。 VM、VMSS、またはクラウド サービスで収集されたパフォーマンス カウンターを Azure Monitor にカスタム メトリックとして送信できるようになります。 Azure Monitor シンクは以下をサポートします。
* [Azure Monitor メトリック API](/rest/api/monitor/metrics/list) を使用して、Azure Monitor に送信されたすべてのパフォーマンス カウンターを取得します。
* Azure Monitor の新しい[統合アラート エクスペリエンス](../alerts/alerts-overview.md)を介して Azure Monitor に送信されたすべてのパフォーマンス カウンターに関して警告します
* パフォーマンス カウンターでワイルドカード演算子をメトリックの "Instance" ディメンションとして扱います。 たとえば、"LogicalDisk(\*)/DiskWrites/sec" カウンターを収集した場合、"Instance" ディメンションでフィルターして分割し、各論理ディスクのディスク書き込み回数/秒をプロットまたはアラートすることができます (C:、D: など)。

診断拡張機能の構成で新しいシンクとして Azure Monitor を定義します
```json
"SinksConfig": {
    "Sink": [
        {
            "name": "AzureMonitorSink",
            "AzureMonitor": {}
        },
    ]
}
```

```XML
<SinksConfig>  
  <Sink name="AzureMonitorSink">
      <AzureMonitor/>
  </Sink>
</SinksConfig>
```
> [!NOTE]
> クラシック VM とクラシック CLoud Service 用に Azure Monitor シンクを構成するには、診断拡張機能のプライベート構成でその他のパラメーターを定義する必要があります。
>
> 詳細については、[詳細な診断拡張機能スキーマのドキュメント](diagnostics-extension-schema-windows.md)を参照してください。

次に、Azure Monitor シンクにルーティングされるようにパフォーマンス カウンターを構成することができます。
```json
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "AzureMonitorSink",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT1M",
            "unit": "percent"
        }
    ]
},
```
```XML
<PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
</PerformanceCounters>
```

### <a name="diagnostics-extension-19"></a>診断拡張機能 1.9
Docker のサポートが追加されました。


### <a name="diagnostics-extension-181"></a>診断拡張機能 1.8.1
ストレージ アカウント キーの代わりに SAS トークンをプライベート構成に指定できます。SAS トークンを指定した場合、ストレージ アカウント キーは無視されます。


```json
{
    "storageAccountName": "diagstorageaccount",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    }
}
```

```xml
<PrivateConfig>
    <StorageAccount name="diagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    <SecondaryStorageAccounts>
        <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>
</PrivateConfig>
```


### <a name="diagnostics-extension-18"></a>診断拡張機能 1.8
PublicConfig にストレージの種類が追加されました。 StorageType は、*Table*、*Blob*、*TableAndBlob* が可能です。 既定値は *Table* です。


```json
{
    "WadCfg": {
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

```xml
<PublicConfig>
    <WadCfg />
    <StorageAccount>diagstorageaccount</StorageAccount>
    <StorageType>TableAndBlob</StorageType>
</PublicConfig>
```


### <a name="diagnostics-extension-17"></a>診断拡張機能 1.7
EventHub にルーティングする機能が追加されました。

### <a name="diagnostics-extension-15"></a>診断拡張機能 1.5
Sink 要素と、診断データを [Application Insights](../app/cloudservices.md) に送信することで、アプリケーションとシステムおよびインフラストラクチャのレベル全体の問題を診断しやすくする機能が追加されました。

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>Azure SDK 2.6 と診断拡張 1.3
Visual Studio のクラウド サービス プロジェクトで、次の変更が行われました (これらの変更はそれ以降のバージョンの Azure SDK にも当てはまります)。

* ローカル エミュレーターで診断がサポートされるようになりました。 この変更により、Visual Studio での開発およびテスト時に診断データを収集し、開発中のアプリケーションで正しくトレースが作成されることを確認できるようになりました。 接続文字列 `UseDevelopmentStorage=true` を指定すると、Visual Studio で Azure Storage エミュレーターを使ってクラウド サービス プロジェクトを実行している間に診断データを収集できます。 すべての診断データは、(開発ストレージ) ストレージ アカウントに収集されます。
* 診断ストレージ アカウントの接続文字列 (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) の保存先が、再びサービス構成 (.cscfg) ファイルに戻されました。 Azure SDK 2.5 では、診断ストレージ アカウントが diagnostics.wadcfgx ファイルで指定されていました。

Azure SDK 2.4 以前と Azure SDK 2.6 以降とで、接続文字列の働きに大きな違いがいくつかあります。

* Azure SDK 2.4 以前では、実行時に診断プラグインが診断ログを転送するためのストレージ アカウント情報を取得する目的で接続文字列を使用していました。
* Azure SDK 2.6 以降では、Visual Studio が診断接続文字列を使用して、発行時に診断拡張機能を適切なストレージ アカウント情報で構成します。 Visual Studio が発行時に使用する各種サービス構成に対し、異なるストレージ アカウントを接続文字列で定義することができます。 しかし、(Azure SDK 2.5 以降) 診断プラグインが使用できなくなったため、.cscfg ファイルだけでは診断拡張機能を有効にできません。 Visual Studio や PowerShell などのツールを使用して個別に拡張機能を有効にする必要があります。
* PowerShell を使用して診断拡張機能を構成するプロセスを単純化するために、Visual Studio からの出力パッケージには、ロールごとの診断拡張機能のパブリック構成 XML も含まれます。 Visual Studio は、診断接続文字列を使用して、パブリック構成に存在するストレージ アカウント情報を取り込みます。 パブリック構成ファイルは拡張機能フォルダーに作成され、`PaaSDiagnostics.<RoleName>.PubConfig.xml` というパターンに従います。 このファイルを PowerShell ベースのデプロイで使用し、各構成をロールにマップすることができます。
* .cscfg ファイル内の接続文字列は、Azure Portal で診断データにアクセスするときにも使用されるため、 **[監視]** タブで確認できます。ポータルで監視データを詳細出力するようにサービスを構成するには、この接続文字列が必要となります。

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Azure SDK 2.6 以降へのプロジェクトの移行
Azure SDK 2.5 から Azure SDK 2.6 以降に移行するとき、.wadcfgx ファイルで指定した診断ストレージ アカウントがある場合、そのアカウントが維持されます。 さまざまなストレージ構成で異なるストレージ アカウントの使用の柔軟性を利用するには、接続文字列をプロジェクトに手動で追加する必要があります。 Azure SDK 2.4 以前から Azure SDK 2.6 にプロジェクトを移行する場合は、診断接続文字列が保持されます。 ただし、前のセクションでも触れたように、Azure SDK 2.6 では接続文字列の扱いが変更されているので注意してください。

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>使用する診断ストレージ アカウントを Visual Studio がどのように決定するか
* 診断接続文字列が .cscfg ファイルに指定されている場合、Visual Studio は、発行時のほか、パッケージ化時にパブリック構成の xml ファイルを生成するときに、.cscfg ファイルに指定されている診断接続文字列を使用して診断拡張機能を構成します。
* 診断接続文字列が .cscfg ファイルに指定されていない場合、Visual Studio は .wadcfgx ファイルに指定されたストレージ アカウントを使用して、発行時、およびパッケージ化におけるパブリック構成 xml ファイルの生成時に診断拡張機能を構成するようにフォールバックします。
* .cscfg ファイルの診断接続文字列は、.wadcfgx ファイルのストレージ アカウントよりも優先されます。 診断接続文字列が .cscfg ファイルに指定されている場合、Visual Studio はそちらを使用し、.wadcfgx のストレージ アカウントは無視します。

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>[...開発ストレージの接続文字列を...更新する] チェックボックスの機能
**[Microsoft Azure への公開時に診断とキャッシュのための開発ストレージの接続文字列を Microsoft Azure ストレージ アカウントの資格情報で更新する]** チェック ボックスの機能は、発行時に指定した Azure ストレージ アカウントで開発ストレージ アカウントの接続文字列を更新することです。

たとえば、このチェック ボックスをオンにして、診断接続文字列で `UseDevelopmentStorage=true`を指定するとします。 この診断接続文字列は、プロジェクトを Azure に発行するとき、公開ウィザードで指定されたストレージ アカウントに自動的に更新されます。 ただし、実際のストレージ アカウントが診断接続文字列として指定されている場合は、そのアカウントが代わりに使用されます。

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Azure SDK 2.4 以前と Azure SDK 2.5 以降の診断機能の違い
プロジェクトを Azure SDK 2.4 から Azure SDK 2.5 以降にアップグレードする場合、次の診断機能の違いに留意してください。

* **構成 API は非推奨となりました** – Azure SDK 2.4 以前のバージョンでは、プログラムから診断機能を構成できましたが、Azure SDK 2.5 以降ではプログラムによる診断の構成が非推奨扱いとなりました。 現在、診断の構成をコードで定義している場合、その診断機能を引き続き利用するためには、移行したプロジェクトでそれらの設定を最初から構成し直す必要があります。 Azure SDK 2.4 の診断構成ファイルは diagnostics.wadcfg で、Azure SDK 2.5 以降では diagnostics.wadcfgx です。
* **クラウド サービス アプリケーションの診断はロール レベルでのみ構成でき、インスタンス レベルでは構成できません。**
* **アプリケーションをデプロイするたびに診断の構成が更新されます** – サーバー エクスプローラーで診断の構成を変更してからアプリを再デプロイした場合、これによってパリティの問題が発生する可能性があります。
* **Azure SDK 2.5 以降では、クラッシュ ダンプがコードからではなく診断構成ファイルで構成されます** - クラッシュ ダンプをコードから構成していた場合は、その構成を手動でコードから構成ファイルに移す必要があります。Azure SDK 2.6 への移行の際にクラッシュ ダンプは移行されません。