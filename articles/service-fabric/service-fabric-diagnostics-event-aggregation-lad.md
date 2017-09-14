---
title: "Linux Azure 診断を使用した Azure Service Fabric のイベントの集計 | Microsoft Docs"
description: "Azure Service Fabric クラスターの監視と診断に LAD を使用したイベントの集計と収集について説明します。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2017
ms.author: dekapur
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: bcc3a229369a065cfcfbd32eadbf3f6ae6fe0036
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Linux Azure 診断を使用したイベントの集計と収集
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Azure Service Fabric クラスターを実行している場合、1 か所ですべてのノードのログを収集することをお勧めします。 1 か所でログを収集すると、クラスター内の問題と、そのクラスターで実行されているアプリケーションやサービスで発生する問題の分析と解決に役立ちます。

ログをアップロードして収集する方法として、Linux Azure 診断 (LAD) 拡張機能を使用できます。この機能を使用すると、ログが Azure Storage にアップロードされますが、Azure Application Insights や Event Hubs にログを送信することもできます。 また、外部プロセスを使用してストレージからイベントを読み取り、[OMS Log Analytics](../log-analytics/log-analytics-service-fabric.md) などの分析プラットフォーム製品や別のログ解析ソリューションで取り込めます。

## <a name="log-and-event-sources"></a>ログとイベントのソース

### <a name="service-fabric-platform-events"></a>Service Fabric プラットフォームのイベント
Service Fabric では、操作イベントやランタイム イベントなどのすぐに使用できるログを [LTTng](http://lttng.org) を介して生成します。 これらのログは、クラスターの Resource Manager テンプレートで指定された場所に保存されます。 ストレージ アカウントの詳細を取得または設定するには、タグ **AzureTableWinFabETWQueryable** を検索し、**StoreConnectionString** を探してください。

### <a name="application-events"></a>アプリケーション イベント
 ソフトウェアをインストルメント化するときに指定した、アプリケーションとサービスのコードから生成されたイベント。 テキスト ベースのログ ファイルを書き込む任意のログ記録ソリューションを使用できます (たとえば、LTTng)。 詳細については、アプリケーションでトレースを実行する方法を LTTng に関するドキュメントで参照してください。

[ローカル コンピューターの開発のセットアップでのサービスの監視と診断](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="deploy-the-diagnostics-extension"></a>診断拡張機能のデプロイ
ログ収集の最初の手順は、Service Fabric クラスター内の各 VM に診断拡張機能をデプロイすることです。 診断拡張機能を使用すると、各 VM のログが収集され、指定したストレージ アカウントにアップロードされます。 手順は、Azure Portal と Azure Resource Manager のどちらを使用するかに応じて変わります。

クラスター作成の一環としてクラスター内の VM に診断拡張機能をデプロイするには、**[診断]** を **[オン]** に設定します。 クラスターを作成した後、ポータルを使用してこの設定を変更することはできません。

次に、ファイルを収集する Linux Azure Diagnostics (LAD) を構成し、ストレージ アカウントに配置します。 このプロセスについては、 [LAD を使用して Linux VM を監視および診断する方法](../virtual-machines/linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)に関する記事でシナリオ 3 (「独自のログ ファイルをアップロードする」) として説明されています。 この手順に従うと、トレースへのアクセスを取得できます。 トレースは、好みのビジュアライザーにアップロードできます。

診断拡張機能は Azure Resource Manager を使用してデプロイすることもできます。 このプロセスは、Windows と Linux とで似ています。Windows クラスター向けのプロセスについては、「[Azure 診断でログを収集する方法](service-fabric-diagnostics-how-to-setup-wad.md)」を参照してください。

[Operations Management Suite Log Analytics with Linux](https://blogs.technet.microsoft.com/hybridcloud/2016/01/28/operations-management-suite-log-analytics-with-linux/)」で説明されているとおりに Operations Management Suite を使用することもできます。

この構成を完了すると、指定されたログ ファイルが LAD エージェントによって監視されます。 新しい行がファイルに追加されるたびに、指定したストレージに送信される syslog エントリが作成されます。

## <a name="next-steps"></a>次のステップ

問題をトラブルシューティングするときに調査する必要があるイベントの詳細については、[LTTng のドキュメント](http://lttng.org/docs)と [LAD の使用](../virtual-machines/linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)に関するページを参照してください。
