---
title: VM insights のトラブルシューティング
description: VM insights のインストールに関するトラブルシューティングを行います。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.custom: references_regions
ms.openlocfilehash: 59b4f38efde2416687702647031d2b37553ff8ed
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555159"
---
# <a name="troubleshoot-vm-insights"></a>VM insights のトラブルシューティング
この記事では、VM insights の有効化または使用に関する問題が発生した場合のトラブルシューティング情報を提供します。

## <a name="cannot-enable-vm-insights-on-a-machine"></a>マシンで VM Insights を有効にできない
Azure portal から Azure 仮想マシンをオンボードすると、次の手順が実行されます。

- 既定の Log Analytics ワークスペースが作成されます (該当するオプションが選択されている場合)。
- Log Analytics エージェントが、VM 拡張機能を使用して Azure 仮想マシンにインストールされます (エージェントが既にインストールされている場合)。
- 依存関係エージェントが必要と判断されると、拡張機能を使用して Azure 仮想マシンにインストールされます。
  
オンボード プロセス中に、これらの各手順が確認され、ポータルに通知状態が表示されます。 ワークスペースとエージェントのインストールの構成には、通常 5 から 10 分かかります。 ポータルにデータが表示されるようになるまでに、さらに 5 から 10 分かかります。

オンボード プロセスの実行後に、仮想マシンをオンボードする必要があるというメッセージが表示された場合、プロセスが完了するまで最大 30 分を見込んでください。 問題が解決しない場合は、考えられる原因について、次のセクションを確認してください。

### <a name="is-the-virtual-machine-running"></a>仮想マシンは実行中ですか?
 VM がしばらくオフになっているか、現在オフになっているか、または最近オンになったばかりの場合、データが到着するまで表示するデータがありません。

### <a name="is-the-operating-system-supported"></a>オペレーティング システムはサポートされていますか?
オペレーティング システムが、[サポートされているオペレーティング システム](vminsights-enable-overview.md#supported-operating-systems)の一覧にない場合は、拡張機能のインストールに失敗し、データの到着を待機しているというメッセージが表示されます。

### <a name="did-the-extension-install-properly"></a>拡張機能が正しくインストールされましたか?
仮想マシンをオンボードする必要があるというメッセージがまだ表示されている場合は、拡張機能のいずれかまたは両方が正しくインストールできなかった可能性があります。 Azure portal の、仮想マシンの **[拡張機能]** ページで、次の拡張機能が表示されていることを確認します。

| オペレーティング システム | エージェント | 
|:---|:---|
| Windows | MicrosoftMonitoringAgent<br>Microsoft.Azure.Monitoring.DependencyAgent |
| Linux | OMSAgentForLinux<br>DependencyAgentForLinux |

インストールされている拡張機能の一覧に、ご使用のオペレーティング システム用の両方の拡張機能が表示されていない場合は、これらをインストールする必要があります。 拡張機能が一覧に表示されていても、 *[プロビジョニング成功]* の状態が表示されていない場合、拡張機能を削除して再インストールする必要があります。

### <a name="do-you-have-connectivity-issues"></a>接続の問題が発生していますか?
Windows マシンの場合、*TestCloudConnectivity* ツールを使用すると、接続に関する問題を識別できます。 このツールは、既定でエージェントと共にフォルダー *%SystemRoot%\Program Files\Microsoft Monitoring Agent\Agent* にインストールされています。 管理者特権のコマンド プロンプトから、このツールを実行します。 結果が返され、テストが失敗した場所が強調表示されます。 

![TestCloudConnectivity ツール](media/vminsights-troubleshoot/test-cloud-connectivity.png)

### <a name="more-agent-troubleshooting"></a>エージェントの追加のトラブルシューティング

Log Analytics エージェントに関する問題のトラブルシューティングについては、次の記事を参照してください。

- [Windows 用 Log Analytics エージェントに関する問題のトラブルシューティング方法](../agents/agent-windows-troubleshoot.md)
- [Linux 用 Log Analytics エージェントに関する問題のトラブルシューティング方法](../agents/agent-linux-troubleshoot.md)

## <a name="performance-view-has-no-data"></a>パフォーマンス ビューにデータが表示されない
エージェントが正しくインストールされているように見えても、パフォーマンス ビューにデータが表示されない場合は、考えられる原因について次のセクションを確認してください。

### <a name="has-your-log-analytics-workspace-reached-its-data-limit"></a>Log Analytics ワークスペースがデータの上限に達していませんか?
[データ インジェストの容量予約と価格](https://azure.microsoft.com/pricing/details/monitor/)を確認してください。

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>仮想マシンはログおよびパフォーマンス データを Azure Monitor Logs に送信していますか?

Azure portal で、[Azure Monitor] メニューの **[ログ]** から Log Analytics を開きます。 コンピューターに対して次のクエリを実行します。

```kuso
Usage 
| where Computer == "my-computer" 
| summarize sum(Quantity), any(QuantityUnit) by DataType
```

データが表示されない場合は、エージェントに問題がある可能性があります。 エージェントのトラブルシューティング情報については、上記のセクションを参照してください。

## <a name="virtual-machine-doesnt-appear-in-map-view"></a>仮想マシンがマップ ビューに表示されない

### <a name="is-the-dependency-agent-installed"></a>依存関係エージェントがインストールされていますか?
 上記のセクション内の情報を使用して、依存関係エージェントがインストールされ、正常に動作しているかどうかを確認します。

### <a name="are-you-on-the-log-analytics-free-tier"></a>Log Analytics Free レベルですか?
[Log Analytics Free レベル](https://azure.microsoft.com/pricing/details/monitor/)は、一意の Service Map マシンを最大 5 台まで使用できる従来の価格プランです。 それ以降のマシンは、前の 5 台でデータを送信していない場合でも、サービス マップに表示されません。

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>仮想マシンはログおよびパフォーマンス データを Azure Monitor Logs に送信していますか?
仮想マシンのデータが収集されているかどうかを判断するには、「[パフォーマンス ビューにデータが表示されない](#performance-view-has-no-data)」セクションのログ クエリを使用します。 データが収集されていない場合は、上で説明した TestCloudConnectivity ツールを使用して、接続に問題がないかどうかを判断します。


## <a name="virtual-machine-appears-in-map-view-but-has-missing-data"></a>仮想マシンがマップ ビューに表示されるがデータが表示されない
仮想マシンがマップビューに表示される場合、依存関係エージェントがインストールされて実行されていますが、カーネル ドライバーが読み込まれていません。 次の場所にあるログ ファイルを確認します。

| オペレーティング システム | ログ | 
|:---|:---|
| Windows | C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log |
| Linux | /var/opt/microsoft/dependency-agent/log/service.log |

ファイルの最後の行に、カーネルがロードされなかった原因が示されています。 たとえば、カーネルを更新した場合にそのカーネルが Linux でサポートされないことがあります。
## <a name="next-steps"></a>次のステップ

- VM insights エージェントのオンボードの詳細については、「[VM insights の有効化の概要](vminsights-enable-overview.md)」を参照してください。
