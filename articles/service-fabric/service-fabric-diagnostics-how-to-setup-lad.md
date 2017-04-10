---
title: "Linux Azure 診断でログを収集する方法 | Microsoft Docs"
description: "この記事では、Azure で実行されている Service Fabric Linux クラスターのログを収集するように Azure 診断を設定する方法について説明します。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: a160d469-8b7d-4560-82dd-8500db34a44a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/02/2017
ms.author: subramar
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 46b62b0ebc5b81241815e3b5b4fa3fe275b88af1
ms.lasthandoff: 03/27/2017


---
# <a name="collect-logs-by-using-azure-diagnostics"></a>Azure 診断でログを収集する方法
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
> * [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
> 
> 

Azure Service Fabric クラスターを実行している場合、1 か所ですべてのノードのログを収集することをお勧めします。 1 か所にログを収集すると、サービス、アプリケーション、またはクラスター自体のどこに問題があるかに関係なく、問題の分析と解決が簡単になります。 ログを収集してアップロードするための方法としては、Azure 診断拡張機能を使用する方法があります。この拡張機能では、Azure Storage、Azure Application Insights、または Azure Event Hubs にログをアップロードできます。 また、ストレージや Event Hubs からイベントを読み取って、 [Log Analytics](../log-analytics/log-analytics-service-fabric.md) などの製品や、その他のログ解析ソリューションにログを送信することもできます。 [Azure  Application Insights](https://azure.microsoft.com/services/application-insights/) には、包括的なログ検索と分析サービスが組み込みで付属しています。

## <a name="log-sources-that-you-might-want-to-collect"></a>収集することができるログ ソース
* **Service Fabric のログ**: [LTTng](http://lttng.org) を使用してプラットフォームによって出力され、ストレージ アカウントにアップロードされます。 ログには、プラットフォームから出力される操作イベントまたは実行時イベントが含まれます。 これらのログは、クラスター マニフェストで指定された場所に保存されます。 (ストレージ アカウントの詳細を取得するには、タグ **AzureTableWinFabETWQueryable** を検索し、**StoreConnectionString** を探してください。)
* **アプリケーション イベント:** サービス コードから出力されます。 テキスト ベースのログ ファイルを書き込む任意のログ記録ソリューションを使用できます (たとえば、LTTng)。 詳細については、アプリケーションでトレースを実行する方法を LTTng に関するドキュメントで参照してください。  

## <a name="deploy-the-diagnostics-extension"></a>診断拡張機能のデプロイ
ログ収集の最初の手順は、Service Fabric クラスター内の各 VM に診断拡張機能をデプロイすることです。 診断拡張機能を使用すると、各 VM のログが収集され、指定したストレージ アカウントにアップロードされます。 手順は、Azure Portal と Azure Resource Manager のどちらを使用するかに応じて変わります。

クラスター作成の一環としてクラスター内の VM に診断拡張機能をデプロイするには、**[診断]** を **[オン]** に設定します。 クラスターを作成した後、ポータルを使用してこの設定を変更することはできません。

次に、ファイルを収集する Linux Azure Diagnostics (LAD) を構成し、ストレージ アカウントに配置します。 このプロセスについては、 [LAD を使用して Linux VM を監視および診断する方法](../virtual-machines/linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)に関する記事でシナリオ 3 (「独自のログ ファイルをアップロードする」) として説明されています。 この手順に従うと、トレースへのアクセスを取得できます。 トレースは、好みのビジュアライザーにアップロードできます。

診断拡張機能は Azure Resource Manager を使用してデプロイすることもできます。 このプロセスは、Windows と Linux とで似ています。Windows クラスター向けのプロセスについては、「[Azure 診断でログを収集する方法](service-fabric-diagnostics-how-to-setup-wad.md)」を参照してください。

[Operations Management Suite Log Analytics with Linux](https://blogs.technet.microsoft.com/hybridcloud/2016/01/28/operations-management-suite-log-analytics-with-linux/)」で説明されているとおりに Operations Management Suite を使用することもできます。

この構成を完了すると、指定されたログ ファイルが LAD エージェントによって監視されます。 新しい行がファイルに追加されるたびに、指定したストレージに送信される syslog エントリが作成されます。

## <a name="next-steps"></a>次のステップ
問題をトラブルシューティングするときに調査する必要があるイベントの詳細については、[LTTng のドキュメント](http://lttng.org/docs)と [LAD の使用](../virtual-machines/linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)に関するページを参照してください。


