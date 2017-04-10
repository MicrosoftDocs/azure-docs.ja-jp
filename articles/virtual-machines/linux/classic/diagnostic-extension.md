---
title: "VM 拡張機能による Linux VM の監視 | Microsoft Doc"
description: "Linux の診断拡張機能を使用して Azure 内の Linux VM のパフォーマンスと診断データを監視する方法を説明します。"
services: virtual-machines-linux
documentationcenter: 
author: NingKuang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: f54a11c5-5a0e-40ff-af6c-e60bd464058b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: Ning
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 50a6b8288f63f7c18f7f84eae29cdd3195dd7a35
ms.lasthandoff: 03/29/2017


---
# <a name="use-the-linux-diagnostic-extension-to-monitor-the-performance-and-diagnostic-data-of-a-linux-vm"></a>Linux の診断拡張機能を使用した Linux VM のパフォーマンスと診断データの監視
## <a name="introduction"></a>はじめに
(**注**: Linux の診断拡張機能は、[GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic) にソース コードが公開されています。この拡張機能についての最新情報もまず GitHub で公開されます。 最初に [GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic) のページをご覧ください。)

Linux 診断拡張機能は、Microsoft Azure で実行されている Linux VM を監視するのに役立ちます。 次の機能が用意されています。

* 診断や syslog 情報を含むシステムのパフォーマンス情報を Linux VM から収集して、ユーザーのストレージ テーブルにアップロードします。
* ユーザーは収集およびアップロードされるデータ メトリックをカスタマイズできます。
* ユーザーは指定したログ ファイルを指定したストレージ テーブルにアップロードできます。

現行のバージョン 2.3 では、以下のデータが含まれます。

* システム、セキュリティ、アプリケーション ログを含む、すべての Linux Rsyslog ログ。
* [System Center Cross Platform Solutions のサイト](https://scx.codeplex.com/wikipage?title=xplatproviders)で指定されているすべてのシステム データ。
* ユーザーが指定したログ ファイル。

この拡張機能は、クラシック デプロイメント モデルと Resource Manager デプロイメント モデルの両方で機能します。

### <a name="current-version-of-the-extension-and-deprecation-of-old-versions"></a>現行バージョンの拡張機能と以前のバージョンの廃止予定
この拡張機能の最新バージョンは **2.3** です。**以前のバージョン (2.0、2.1、2.2) は今年 (2016 年) の終わりまでに廃止され、公開が停止されます**。 マイナー バージョンの自動アップグレードを無効にして Linux の診断拡張機能をインストールした場合は、一度拡張機能をアンインストールし、マイナー バージョンの自動アップグレードを有効にして再インストールすることを強くお勧めします。 クラシック (ASM) VM に、Azure XPLAT CLI または PowerShell で拡張機能をインストールする場合、バージョンとして「2.*」を指定します。 ARM VM の場合は、VM デプロイ テンプレートに「"autoUpgradeMinorVersion": true」を追加します。 また、拡張機能を新規インストールする場合は、マイナー バージョンの自動アップグレード オプションをオンにする必要があります。

## <a name="enable-the-extension"></a>拡張機能を有効にする
この拡張機能は、 [Azure ポータル](https://portal.azure.com/#)、Azure PowerShell、または Azure CLI スクリプトから有効にできます。

システムおよびパフォーマンス データを Azure Portal で直接表示および構成するには、[Azure ブログのこちらの手順](https://azure.microsoft.com/en-us/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/)に従ってください。

この記事では、Azure CLI コマンドを使用して、拡張機能を有効にして構成する方法を説明します。 これにより、ストレージ テーブルからデータを直接読み込んで表示することができます。

ここで説明する構成方法は、Azure ポータルでは機能しないことに注意してください。 Azure ポータルからシステムとパフォーマンス データを直接表示および構成するには、この拡張機能を Azure ポータルから有効にする必要があります。

## <a name="prerequisites"></a>前提条件
* **Azure Linux エージェント バージョン 2.0.6 またはそれ以降**。
  大部分の Azure VM Linux ギャラリー イメージにはバージョン 2.0.6 以降が含まれています。 **WAAgent -version** を実行して、VM にインストールされているバージョンを確認できます。 VM が 2.0.6 より前のバージョンを実行している場合は、 [GitHub のこちらの説明](https://github.com/Azure/WALinuxAgent "説明") に従って更新できます。
* **Azure CLI**。 [このガイダンスの CLI のインストール手順](../../../cli-install-nodejs.md) に従って、コンピューターに Azure CLI 環境をセットアップします。 Azure CLI をインストールすると、コマンド ライン インターフェイス (Bash、ターミナル、またはコマンド プロンプト) から **azure** コマンドを使用して Azure CLI コマンドにアクセスできるようになります。 次に例を示します。
  
  * ヘルプ情報の詳細については、 **azure vm extension set --help** を実行します。
  * Azure にサインインするには、 **azure login** を実行します。
  * Azure に存在するすべての仮想マシンの一覧を表示するには、 **azure vm list** を実行します。
* データを格納するためのストレージ アカウント。 データをストレージにアップロードするには、事前に作成したストレージ アカウント名と、アクセス キーが必要になります。

## <a name="use-the-azure-cli-command-to-enable-the-linux-diagnostic-extension"></a>Azure CLI コマンドを使用して Linux 診断拡張機能を有効にする
### <a name="scenario-1-enable-the-extension-with-the-default-data-set"></a>シナリオ 1. 既定のデータ セットで拡張機能を有効にする
バージョン 2.3 以降では、以下のデータが既定で収集されます。

* すべての Rsyslog 情報 (システム ログ、セキュリティ ログ、アプリケーション ログ)。  
* 基本システム データのコア セット。 データ セット全体の説明については、 [System Center Cross Platform Solutions のサイト](https://scx.codeplex.com/wikipage?title=xplatproviders)をご覧ください。
  追加のデータを有効にする場合は、シナリオ 2 および 3 の手順を続行してください。

手順 1. 次の内容を含む PrivateConfig.json という名前のファイルを作成します。

    {
        "storageAccountName" : "the storage account to receive data",
        "storageAccountKey" : "the key of the account"
    }

手順 2. **azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json** を実行します。

### <a name="scenario-2-customize-the-performance-monitor-metrics"></a>シナリオ 2. パフォーマンス モニターのメトリックをカスタマイズする
このセクションでは、パフォーマンスと診断データのテーブルをカスタマイズする方法について説明します。

手順 1. シナリオ 1 で説明した内容を含む PrivateConfig.json という名前のファイルを作成します。 PublicConfig.json という名前のファイルも作成します。 収集する特定のデータを指定します。

サポートされているすべてのプロバイダーと変数については、 [System Center Cross Platform Solutions のサイト](https://scx.codeplex.com/wikipage?title=xplatproviders)をご覧ください。 スクリプトにさらにクエリを追加することにより、複数のクエリを使用して複数のテーブルにそれを格納することができます。

Rsyslog データは既定で常に収集されます。

    {
          "perfCfg":
          [
              {
                  "query" : "SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
                  "table" : "LinuxMemory"
              }
          ]
    }


手順 2. **azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json** を実行します。

### <a name="scenario-3-upload-your-own-log-files"></a>シナリオ 3. 独自のログ ファイルをアップロードする
このセクションでは、ストレージ アカウントに特定のログ ファイルを収集およびアップロードする方法について説明します。 ログ ファイルのパスと、ログを格納するテーブルの名前を指定する必要があります。 複数の file/table エントリをスクリプトに追加することで、複数のログ ファイルを作成できます。

手順 1. シナリオ 1 で説明した内容を含む PrivateConfig.json という名前のファイルを作成します。 その後、次の内容を含む PublicConfig.json という名前の別のファイルを作成します。

    {
        "fileCfg" :
        [
            {
                "file" : "/var/log/mysql.err",
                "table" : "mysqlerr"
             }
        ]
    }


手順 2. **azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json** を実行します。

2.3 までの拡張機能バージョンでこのように設定すると、`/var/log/mysql.err` に書き込まれたすべてのログが `/var/log/syslog` (Linux ディストリビューションによっては `/var/log/messages` ) にも複製される可能性があります。 ログが重複して記録されるのを防ぐには、rsyslog の構成で `local6` ファシリティ ログの記録を除外してください。 この点は Linux ディストリビューションによっても異なりますが、Ubuntu 14.04 システムの場合、変更対象となるファイルは `/etc/rsyslog.d/50-default.conf` です。`*.*;auth,authpriv.none -/var/log/syslog` という行を `*.*;auth,authpriv,local6.none -/var/log/syslog` に差し替えてください。 この問題は、最新の修正プログラム リリース 2.3 (2.3.9007) で修正されます。そのため、拡張機能バージョン 2.3 をお持ちの場合はこの問題は発生しません。 VM を再起動しても解消されない場合は、弊社にお問い合わせいただき、最新の修正プログラムが自動的にインストールされない原因を究明するためにご協力をお願いします。

### <a name="scenario-4-stop-the-extension-from-collecting-any-logs"></a>シナリオ 4. 拡張機能によるログ収集を停止する
このセクションでは、拡張機能によるログ収集を停止する方法について説明します。 この再構成を使用しても、監視エージェント プロセスはまだ稼働していることに注意してください。 監視エージェント プロセスを完全に停止するには、拡張機能を無効にします。 拡張機能を無効にするコマンドは、**azure vm extension set --disable <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions '2.*'** です。

手順 1. シナリオ 1 で説明した内容を含む PrivateConfig.json という名前のファイルを作成します。 次の内容を含む PublicConfig.json という名前の別のファイルを作成します。

    {
        "perfCfg" : [],
        "enableSyslog" : "false"
    }


手順 2. **azure vm extension set vm_name LinuxDiagnostic Microsoft.OSTCExtensions '2.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json** を実行します。

## <a name="review-your-data"></a>データを確認する
パフォーマンスと診断のデータが Azure Storage テーブルに保存されます。 「 [Azure Ruby から Table Storage を使用する方法](../../../storage/storage-ruby-how-to-use-table-storage.md) 」を確認して、Azure CLI スクリプトを使用してストレージ テーブルのデータにアクセスする方法を学びます。

さらに、次の UI ツールを使用してデータにアクセスすることもできます。

1. Visual Studio のサーバー エクスプローラー。 ストレージ アカウントに移動します。 VM を約 5 分間実行した後に、既定のテーブル「LinuxCpu」、「LinuxDisk」、「LinuxMemory」、および「Linuxsyslog」が表示されます。 データを表示するには、テーブル名をダブルクリックします。
2. [Azure ストレージ エクスプローラー](https://azurestorageexplorer.codeplex.com/ "Azure ストレージ エクスプローラー")で指定されているすべてのシステム データ。

![image](./media/diagnostic-extension/no1.png)

(シナリオ 2 および 3 の説明に従って) fileCfg または perfCfg を有効にした場合は、Visual Studio サーバー エクスプローラーと Azure ストレージ エクスプローラーを使用して、既定以外のデータを表示することができます。

## <a name="known-issues"></a>既知の問題
* Linux 診断拡張機能の最新バージョン (2.3) の場合、Rsyslog 情報およびユーザー指定のログ ファイルには、スクリプトからのみアクセスできます。


