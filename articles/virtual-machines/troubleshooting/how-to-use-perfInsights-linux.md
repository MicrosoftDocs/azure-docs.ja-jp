---
title: Microsoft Azure で PerfInsights Linux を使用する方法 | Microsoft Docs
description: PerfInsights を使用して Linux VM のパフォーマンスに関する問題のトラブルシューティングを行う方法を説明します。
services: virtual-machines-linux'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 7/10/2019
ms.author: genli
ms.openlocfilehash: 19b2fcaed2c80d4ca52ada9f9f0898479e73bcf2
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70080514"
---
# <a name="how-to-use-perfinsights"></a>PerfInsights を使用する方法

[PerfInsights Linux](https://aka.ms/perfinsightslinuxdownload) は、診断データを収集および解析するセルフヘルプ診断ツールであり、Azure で Linux 仮想マシンのパフォーマンスに関する問題をトラブルシューティングする際に役立つレポートが提供されます。 PerfInsights は、サポート対象の仮想マシンでスタンドアロン ツールとして実行することも、[Azure 仮想マシンのパフォーマンス診断](performance-diagnostics.md)を使用してポータルから直接実行することもできます。

仮想マシンでパフォーマンスの問題が発生している場合は、サポートに連絡する前にこのツールを実行してください。

## <a name="supported-troubleshooting-scenarios"></a>サポートされているトラブルシューティングのシナリオ

PerfInsights は、いくつかの種類の情報を収集して分析できます。 以降のセクションでは、一般的なシナリオについて説明します。

### <a name="quick-performance-analysis"></a>パフォーマンスのクイック分析

このシナリオでは、利用中の仮想マシンのストレージとハードウェア構成のような基本的な情報や、次のようなさまざまなログが収集されます。

- オペレーティング システムの情報

- PCI デバイス情報

- 一般的なゲスト OS のログ

- 構成ファイル

- ストレージ情報

- Azure 仮想マシンの構成 ([Azure Instance Metadata Service](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service) を使用して収集)

- 実行中のプロセス、ディスク、メモリ、CPU 使用率の一覧

- ネットワーク情報

これは、システムに影響しない情報のパッシブ コレクションです。

>[!Note]
>クイック パフォーマンス解析シナリオは、次の各シナリオに自動的に追加されます。

### <a name="performance-analysis"></a>パフォーマンス分析

このシナリオは、クイック パフォーマンス解析に似ていますが、より長い期間の診断情報をキャプチャすることができます。

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>PerfInsights で収集される情報の種類

Linux 仮想マシン、オペレーティング システム、ブロック デバイス、高リソース コンシューマー、構成、さまざまなログに関する情報が収集されます。 次のような詳細です。

- オペレーティング システム
  - Linux ディストリビューションとバージョン
  - カーネルの情報
  - ドライバーの情報

- ハードウェア
  - PCI デバイス [`*`]

- プロセスとメモリ
  - プロセスの一覧 (タスク名、使用されているメモリ、開いているファイル)
  - 物理メモリの合計、使用可能、空き
  - スワップ メモリの合計、使用可能、空き
  - CPU のキャプチャと、5 秒間隔での CPU 使用率の処理をプロファイリング
  - 5 秒間隔でのメモリ使用量の処理のキャプチャをプロファイリング

- ネットワーク  
  - ネットワーク アダプターとアダプターの統計情報の一覧
  - ネットワーク ルーティング テーブル
  - 開いているポートと状態

- Storage
  - ブロック デバイスの一覧
  - パーティションの一覧
  - マウント ポイントの一覧
  - MDADM ボリュームの情報
  - LVM ボリュームの情報
  - 5 秒ごとのすべてのディスクのプロファイルのキャプチャ

- ログ
  - /var/log/messages
  - /var/log/syslog
  - /var/log/kern.log
  - /var/log/cron.log
  - /var/log/boot.log
  - /var/log/yum.log
  - /var/log/dpkg.log
  - /var/log/cloud-init.log
  - /var/log/cloud-init-output.log
  - /var/log/gpu-manager.log
  - /var/log/waagent.log
  - /var/log/azure/[拡張フォルダー]/\*log\*
  - /var/opt/microsoft/omsconfig/omsconfig.log
  - /var/opt/microsoft/omsagent/log/omsagent.log
  - /etc/waagent.config
  - 過去 5 日間の journalctl の出力

- [Azure 仮想マシン インスタンスのメタデータ](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

>[!Note]
>[`*`] Debian と SLES のディストリビューションでは、まだ PCI 情報が収集されません

## <a name="run-the-perfinsights-linux-on-your-vm"></a>VM で PerfInsights Linux を実行する

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>ツールを実行する前の確認事項

#### <a name="tool-requirements"></a>ツールの要件

- このツールを、パフォーマンスに問題がある VM で実行する必要があります。
- Python 2.7 が VM にインストールされている必要があります

- 現在、次のディストリビューションがサポートされています。

    | ディストリビューション               | Version                                         |
    |----------------------------|-------------------------------------------------|
    | Oracle Linux Server        | 6.10 [`*`]、7.3、7.6、7.5 (Oracle-Database-Ee 13.8 マーケットプレース イメージ)|
    | CentOS                     | 6.5 [`*`]、7.6                                    |
    | RHEL                       | 7.2、7.5、8.0 [`*`]                               |
    | Ubuntu                     | 14.04、16.04、18.04                               |
    | Debian                     | 8、9、10 [`*`]                                    |
    | SLES                       | 12 SP4 [`*`]                                      |
    |                            |                                                   |

>[!Note]
>[`*`] [既知のイシュー](#known-issues)のセクションを参照してください

### <a name="known-issues"></a>既知の問題

- RHEL 8 には、既定では Python がインストールされていません。 PerfInsights Linux を実行するには、最初に Python 2.7 をインストールする必要があります

- CentOS 6.x でゲスト エージェント情報の収集が失敗する場合があります

- Debian ベースのディストリビューションで PCI デバイス情報が収集されません

- 一部のディストリビューションで LVM 情報が部分的に収集されます

### <a name="how-do-i-run-perfinsights"></a>PerfInsights の実行方法

PerfInsights は、Azure portal から Azure Performance Diagnostics をインストールすることによって仮想マシンで実行できます。 また、スタンドアロン ツールとしても実行できます。

>[!Note]
>PerfInsights では、単純にデータが収集されて解析されます。 これによりシステムに変更は加えられません。

#### <a name="install-and-run-perfinsights-from-the-azure-portal"></a>Azure portal から PerfInsights をインストールして実行する

このオプションの詳細については、[Azure Performance Diagnostics](performance-diagnostics.md) に関する記事を参照してください。  

#### <a name="run-perfinsights-in-standalone-mode"></a>スタンドアロン モードで PerfInsights を実行する

PerfInsights ツールを実行するには、次の手順に従います。

1. [PerfInsights.tar.gz](https://aka.ms/perfinsightslinuxdownload) を仮想マシン上のフォルダーにダウンロードし、ターミナルから次のコマンドを使用してコンテンツを抽出します。

   ```bash
   wget https://download.microsoft.com/download/9/F/8/9F80419C-D60D-45F1-8A98-718855F25722/PerfInsights.tar.gz
   ```

   ```bash
   tar xzvf PerfInsights.tar.gz
   ```

2. `perfinsights.py` ファイルが格納されているフォルダーに移動し、`perfinsights.py` を実行して、使用可能なコマンドライン パラメーターを表示します。

    ```bash
    cd <the path of PerfInsights folder>
    sudo python perfinsights.py
    ```

    ![PerfInsights Linux コマンドライン出力のスクリーンショット](media/how-to-use-perfinsights-linux/perfinsights-linux-commandline.png)

    PerfInsights シナリオを実行する基本構文は次のとおりです。

    ```bash
    sudo python perfinsights.py -r <ScenarioName> -d [duration]<H | M | S> [AdditionalOptions]
    ```

    次の例を使用すると、クイック パフォーマンス解析シナリオを 1 分間実行し、/tmp/output フォルダーの下に結果を作成できます。

    ```bash
    sudo python perfinsights.py -r quick -d 1M -a -o /tmp/output
    ```

    次の例を使用して、パフォーマンス解析シナリオを 5 分間実行し、結果の tar ボールをストレージ アカウントにアップロードできます。

    ```bash
    sudo python perfinsights.py -r vmslow -d 300S -a -t <StorageAccountName> -k <StorageAccountKey> -i <full resource Uri of the current VM>
    ```

    >[!Note]
    >シナリオを実行する前に、PerfInsights から、診断情報を共有し、使用許諾契約書に同意するよう求めるプロンプトが表示されます。 このプロンプトをスキップするには、 **-a または --accept-disclaimer-and-share-diagnostics** オプションを使用します。
    >
    >有効な Microsoft のサポート チケットをお持ちで、サポート エンジニアからの依頼に従って PerfInsights を実行している場合は、 **/s または --support-request** オプションを使用してサポート チケット番号を入力してください。

実行が完了すると、出力フォルダーが指定されていない場合は、PerfInsights と同じフォルダーに新しい tar ファイルが表示されます。 ファイルの名前は **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.tar.gz.** です。 このファイルをサポート エージェントに送信して解析してもらったり、ファイル内のレポートを開いて、結果や推奨事項を確認したりすることができます。

## <a name="review-the-diagnostics-report"></a>診断レポートを確認する

**PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.tar.gz** ファイル内に、PerfInsights の結果が詳細に示された HTML レポートがあります。 レポートを確認するには、**PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.tar.gz** ファイルを展開し、**PerfInsights Report.html** ファイルを開きます。

### <a name="overview-tab"></a>[概要] タブ

**[概要]** タブには、基本的な実行の詳細と仮想マシンの情報が表示されます。 **[Findings]\(検出結果\)** タブには、PerfInsights レポートのすべてのセクションの推奨事項の概要が表示されます。

![PerfInsights レポートのスクリーンショット](media/how-to-use-perfinsights-linux/perfinsights-linux-overview.png)  
![PerfInsights レポートのスクリーンショット](media/how-to-use-perfinsights-linux/perfinsights-linux-findings-tab.png)

> [!NOTE]
> [高] と分類された結果は、パフォーマンスの問題の原因となる可能性のある既知の問題です。 [中] と分類された結果は、最適ではないが、必ずしもパフォーマンス問題の原因とはならない構成です。 [低] と分類された結果は、単なる情報提供のためのステートメントです。

[高] の結果と [中] の結果に対する推奨事項とリンクを確認します。 それらがパフォーマンスに影響を与える可能性について、またパフォーマンスが最適化された構成のベスト プラクティスについても学習してください。

### <a name="cpu-tab"></a>[CPU] タブ

**[CPU]** タブには、PerfInsights 実行中のシステム全体の CPU 使用量に関する情報が表示されます。 CPU 使用率が高い期間や、特に CPU を長く実行しているコンシューマーに関する情報は、高い CPU 使用率に関するイシューのトラブルシューティングに役立ちます。

![PerfInsights レポートの [CPU] タブのスクリーンショット](media/how-to-use-perfinsights-linux/perfinsights-linux-cpu-tab.png)

### <a name="storage-tab"></a>[ストレージ] タブ

**[Findings]\(評価結果\)** セクションには、ストレージに関連する分析結果と推奨事項が表示されます。

**[ブロック デバイス]** と、 **[パーティション]** 、 **[LVM]** 、 **[MDADM]** の各タブのようなその他の関連セクションでは、ブロック デバイスがどのように構成され、相互に関連しているかがわかります。

![[ストレージ] タブのスクリーンショット](media/how-to-use-perfinsights-linux/perfinsights-linux-storage-tab.png)  
![[MDADM] タブのスクリーンショット](media/how-to-use-perfinsights-linux/perfinsights-linux-mdadm-config.png)

### <a name="linux-tab"></a>[Linux] タブ

**[Linux]** タブには、ハードウェアと VM で実行されているオペレーティング システムに関する情報が表示されます。 詳細には、実行中のプロセスの一覧と、ゲスト エージェント、PCI、CPU、ドライバー、LIS ドライバーに関する情報が含まれます。

![[Linux] タブのスクリーンショット](media/how-to-use-perfinsights-linux/perfinsights-linux-tab.png)

## <a name="next-steps"></a>次の手順

さらに詳しく調べるために、診断ログとレポートを Microsoft サポートにアップロードできます。 Microsoft サポートに連絡すると、トラブルシューティング プロセスのために、担当者から PerfInsights で生成された出力を送信するように求められることがあります。

次のスクリーンショットは、受信する可能性があるものと同様のメッセージを示しています。

![Microsoft サポートからのサンプル メッセージのスクリーンショット](media/how-to-use-perfinsights-linux/support-email.png)

メッセージ内の指示に従って、ファイル転送ワークスペースにアクセスします。 セキュリティを強化するために、最初の使用時にパスワードを変更する必要があります。

サインインすると、PerfInsights によって収集された **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.tar.gz** ファイルをアップロードするためのダイアログ ボックスが表示されます。
