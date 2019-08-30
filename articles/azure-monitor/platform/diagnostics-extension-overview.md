---
title: Azure Diagnostics 拡張機能の概要
description: Azure Diagnostics は、Cloud Services、Virtual Machines、および Service Fabric でのデバッグ、パフォーマンス測定、監視、トラフィック分析に使用できます。
author: rboucher
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: e0325a3bda912c95d8d27646bc1e80fff5ce10a8
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639422"
---
# <a name="what-is-azure-diagnostics-extension"></a>Azure Diagnostics 拡張機能とは何か
Azure Diagnostics 拡張機能は、デプロイされたアプリケーションで診断データを収集できるようにする Azure 内のエージェントです。 さまざまなソースで診断拡張機能を使用することができます。 現時点でのサポート対象は、Azure クラウド サービス (クラシック) の Web ロールおよび Worker ロール、Virtual Machines、仮想マシン スケール セット、および Service Fabric となっています。 他の Azure サービスでは、異なる診断方法を使用します。 「[Azure Monitor の概要 ](../../azure-monitor/overview.md)」を参照してください。

## <a name="linux-agent"></a>Linux エージェント
Linux を実行する仮想マシンには、[Linux 版の拡張機能](../../virtual-machines/extensions/diagnostics-linux.md)をご利用いただけます。 収集される統計情報と動作は、Windows 版とは異なります。

## <a name="data-you-can-collect"></a>収集可能なデータ
Azure Diagnostics 拡張機能では、次の種類のデータを収集できます。

| データ ソース | 説明 |
| --- | --- |
| パフォーマンス カウンター メトリック |オペレーティング システムとカスタム パフォーマンス カウンター |
| アプリケーション ログ |アプリケーションによって書き込まれたメッセージをトレースする |
| Windows イベント ログ |Windows イベント ログ システムに送信された情報 |
| .NET EventSource ログ |.NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) クラスを使用するコード作成イベント |
| IIS ログ |IIS Web サイトに関する情報 |
| [マニフェスト ベースの ETW ログ](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |すべてのプロセスによって生成された Windows イベント トレーシング イベント。(1) |
| クラッシュ ダンプ (ログ) |アプリケーションがクラッシュした場合のプロセスの状態に関する情報 |
| カスタム エラー ログ |アプリケーションまたはサービスで作成されたログ |
| Azure 診断インフラストラクチャ ログ |Azure Diagnostics 自体に関する情報 |

(1) ETW プロバイダーの一覧を取得するには、情報を収集するコンピューターのコンソール ウィンドウで `c:\Windows\System32\logman.exe query providers` を実行します。

## <a name="data-storage"></a>データ ストレージ
この拡張機能では、指定した [Azure Storage アカウント](diagnostics-extension-to-storage.md)にそのデータが格納されます。

[Application Insights](../../azure-monitor/app/cloudservices.md) に送信することもできます。 

[Event Hub](../../event-hubs/event-hubs-about.md) にストリーム配信するという選択肢もあります。その場合、後で Azure 以外の監視サービスに送信できます。

Azure Monitor メトリック時系列データベースにデータを送信することもできます。 現時点では、このシンクはパフォーマンス カウンターにのみ適用されます。 パフォーマンス カウンターをカスタム メトリックとして送信できます。 この機能はプレビュー段階にあります。 Azure Monitor シンクは以下をサポートします。
* [Azure Monitor メトリック API](https://docs.microsoft.com/rest/api/monitor/) を使用して、Azure Monitor に送信されたすべてのパフォーマンス カウンターを取得します。
* Azure Monitor の[メトリック アラート](../../azure-monitor/platform/alerts-overview.md)を介して Azure Monitor に送信されたすべてのパフォーマンス カウンターに関して警告します
* パフォーマンス カウンターでワイルドカード演算子をメトリックの "Instance" ディメンションとして扱います。  たとえば、"LogicalDisk(\*)/DiskWrites/sec" カウンターを収集した場合、"Instance" ディメンションでフィルターして分割し、VM 上の各論理ディスクのディスク書き込み回数/秒をプロットまたはアラートすることができます (C: など)。

このシンクを構成する方法については、[Azure Diagnostics スキーマのドキュメント](diagnostics-extension-schema-1dot3.md)を参照してください。

## <a name="costs"></a>コスト
上記のオプションのそれぞれでコストが発生する場合があります。 予期しない請求を回避するために、それらを必ず調べておいてください。  Application Insights、イベント ハブ、Azure Storage には、取り込みと格納の時刻に関連付けられている個別のコストがあります。 特に Azure Storage はデータを永久的に保持するので、コストを引き下げておくために古いデータは一定期間後に消去できます。    

## <a name="versioning-and-configuration-schema"></a>バージョン管理と構成スキーマ
[Azure Diagnostics のバージョン履歴とスキーマ](diagnostics-extension-schema.md)に関するページを参照してください。


## <a name="next-steps"></a>次の手順
診断情報を収集するサービスを選択し、以下の記事の説明に従って操作を開始してください。 特定のタスクのリファレンスについては、一般的な Azure Diagnostics リンクを使用してください。

## <a name="cloud-services-using-azure-diagnostics"></a>Cloud Services で Azure Diagnostics を使用する
* Visual Studio を使用する場合は、[Visual Studio を使用した Cloud Services アプリケーションのトレース](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines)に関するページを参照して操作を開始してください。 それ以外の場合は次を参照してください。
* [Azure Diagnostics を使用して Cloud Services を監視する方法](../../cloud-services/cloud-services-how-to-monitor.md)
* [Cloud Services アプリケーションでの Azure Diagnostics の設定](../../cloud-services/cloud-services-dotnet-diagnostics.md)

詳細なトピックについては、次を参照してください。

* [Cloud Services 向けの Application Insights での Azure Diagnostics の使用](../../azure-monitor/app/cloudservices.md)
* [Azure Diagnostics で Cloud Services アプリケーションのフローをトレースする](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [PowerShell を使用した Cloud Services での診断の設定](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Virtual Machines
* Visual Studio を使用する場合は、[Visual Studio を使用した Azure Virtual Machines のトレース](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines)に関するページを参照して操作を開始してください。 それ以外の場合は次を参照してください。
* [Azure 仮想マシンでの Azure Diagnostics の設定](/azure/virtual-machines/extensions/diagnostics-windows)

詳細なトピックについては、次を参照してください。

* [PowerShell を使用した Azure Virtual Machines での診断の設定](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure Resource Manager テンプレートを使用して監視および診断を含む Windows 仮想マシンを登録する](../../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
[Service Fabric アプリケーションの監視](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)に関するページを参照して操作を開始してください。 この記事にアクセスすると、左側のナビゲーション ツリーに Service Fabric 診断に関するその他の記事が多数あります。

## <a name="general-articles"></a>全般記事
* [Azure Diagnostics でのパフォーマンス カウンターの使用](../../cloud-services/diagnostics-performance-counters.md)について説明します。
* Azure Storage テーブルでの診断の開始またはデータの検索に問題がある場合は、[Azure Diagnostics のトラブルシューティング](diagnostics-extension-troubleshooting.md)に関するページを参照してください。

