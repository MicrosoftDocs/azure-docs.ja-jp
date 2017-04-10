---
title: "Azure 診断の概要 | Microsoft Docs"
description: "Azure 診断は、Cloud Services、Virtual Machines、および Service Fabric でのデバッグ、パフォーマンス測定、監視、トラフィック分析に使用できます。"
services: multiple
documentationcenter: .net
author: rboucher
manager: 
editor: 
ms.assetid: baad40d8-c915-4f93-b486-8b160bf33463
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: f31a8347bf6bb36fa166d35f52915813394d85e9
ms.lasthandoff: 03/22/2017


---
# <a name="what-is-azure-diagnostics"></a>Azure 診断について
Azure 診断は、デプロイされたアプリケーションで診断データを収集できるようにする Azure 内の機能です。 さまざまなソースで診断拡張機能を使用することができます。 現在時点でのサポート対象は、Azure Cloud Service Web および Worker ロール、Microsoft Windows を実行している Azure Virtual Machines、および Service Fabric となっています。 その他の Azure サービスには、独自の独立した診断があります。

## <a name="data-you-can-collect"></a>収集可能なデータ
Azure 診断は、次の種類のデータを収集できます。

| データ ソース | Description |
| --- | --- |
| パフォーマンス カウンター |オペレーティング システムとカスタム パフォーマンス カウンター |
| Application Logs |アプリケーションによって書き込まれたメッセージをトレースする |
| Windows イベント ログ |Windows イベント ログ システムに送信された情報 |
| .NET イベント ソース |.NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) クラスを使用するコード作成イベント |
| IIS ログ |IIS Web サイトに関する情報 |
| マニフェスト ベースの ETW |すべてのプロセスによって生成された Windows イベントのイベント トレース |
| クラッシュ ダンプ |アプリケーションがクラッシュした場合のプロセスの状態に関する情報 |
| カスタム エラー ログ |アプリケーションまたはサービスで作成されたログ |
| Azure 診断インフラストラクチャ ログ |診断自体に関する情報 |

Azure 診断拡張機能は、このデータを Azure ストレージ アカウントに転送したり、[Application Insights](../application-insights/app-insights-cloudservices.md) のようなサービスに送信したりすることができます。 データは、デバッグ、トラブルシューティング、パフォーマンス測定、リソース使用状況の監視、トラフィック解析と容量計画、監査などに使用できます。

## <a name="versioning"></a>バージョン管理
「 [Azure Diagnostics Versioning History (Azure 診断のバージョン履歴)](azure-diagnostics-versioning-history.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
診断情報を収集するサービスを選択し、以下の記事の説明に従って操作を開始してください。 特定のタスクのリファレンスについては、一般的な Azure 診断リンクを使用してください。

## <a name="web-apps"></a>Web Apps
Web Apps では Azure 診断が使用されないので注意してください。 Azure 診断に相当する情報については、[Web Apps](../app-service-web/web-sites-enable-diagnostic-log.md) に関するページを参照してください。

## <a name="cloud-services-using-azure-diagnostics"></a>Cloud Services で Azure 診断を使用する
* Visual Studio を使用する場合は、[Visual Studio を使用した Cloud Services アプリケーションのトレース](../vs-azure-tools-debug-cloud-services-virtual-machines.md)に関するページを参照して操作を開始してください。 それ以外の場合は次を参照してください。
* [Azure 診断を使用して Cloud Services を監視する方法](../cloud-services/cloud-services-how-to-monitor.md)
* [Cloud Services アプリケーションでの Azure 診断の設定](../cloud-services/cloud-services-dotnet-diagnostics.md)

詳細なトピックについては、次を参照してください。

* [Cloud Services 向けの Application Insights での Azure 診断の使用](../application-insights/app-insights-cloudservices.md)
* [Azure 診断で Cloud Services アプリケーションのフローをトレースする](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [PowerShell を使用した Cloud Services での診断の設定](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines-using-azure-diagnostics"></a>Virtual Machines で Azure 診断を使用する
* Visual Studio を使用する場合は、[Visual Studio を使用した Azure Virtual Machines のトレース](../vs-azure-tools-debug-cloud-services-virtual-machines.md)に関するページを参照して操作を開始してください。 それ以外の場合は次を参照してください。
* [Azure 仮想マシンでの Azure 診断の設定](../virtual-machines-dotnet-diagnostics.md)

詳細なトピックについては、次を参照してください。

* [PowerShell を使用した Azure Virtual Machines での診断の設定](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure Resource Manager テンプレートを使用して監視および診断を含む Windows 仮想マシンを登録する](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric-using-azure-diagnostics"></a>Service Fabric で Azure 診断を使用する
[Service Fabric アプリケーションの監視](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)に関するページを参照して操作を開始してください。 この記事にアクセスすると、左側のナビゲーション ツリーに Service Fabric 診断に関するその他の記事が多数あります。

## <a name="general-azure-diagnostics-articles"></a>Azure 診断に関する一般的な記事
* [Azure Diagnostics Schema Configuration (Azure 診断スキーマの構成)](https://msdn.microsoft.com/library/azure/mt634524.aspx) - 診断データを収集して送信するようにスキーマ ファイルを変更する方法について説明します。 Visual Studio を使用してスキーマ ファイルを変更することもできます。
* [Azure Storage に Azure 診断データを格納する方法](../cloud-services/cloud-services-dotnet-diagnostics-storage.md) - 診断データが書き込まれるテーブルと BLOB の名前を示します。
* [Azure 診断でのパフォーマンス カウンターの使用](../cloud-services/cloud-services-dotnet-diagnostics-performance-counters.md)について説明します。
* [Application Insights への Azure 診断情報の送信](azure-diagnostics-configure-application-insights.md)について説明します。
* Azure Storage テーブルでの診断の開始またはデータの検索に問題がある場合は、[Azure 診断のトラブルシューティング](azure-diagnostics-troubleshooting.md)に関するページを参照してください。

