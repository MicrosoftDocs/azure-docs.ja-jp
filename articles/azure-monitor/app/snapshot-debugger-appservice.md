---
title: Azure App Service で .NET アプリでスナップショット デバッガーを有効にする | Microsoft Docs
description: Azure App Service で .NET アプリでスナップショット デバッガーを有効にする
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: brahmnes
ms.openlocfilehash: 7ff93dffe2dd82bdbba204b4235a297b337438f5
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/14/2019
ms.locfileid: "57876243"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Azure App Service で .NET アプリでスナップショット デバッガーを有効にする

スナップショット デバッガーは、現在、Windows サービス プランの Azure App Service で実行されている ASP.NET アプリと ASP.NET Core アプリで機能します。

## <a id="installation"></a> スナップショット デバッガーを有効にする
アプリでスナップショット デバッガーを有効にするには、次の手順に従います。 別の種類の Azure サービスを実行している場合、他のサポート対象プラットフォームでスナップショット デバッガーを有効にする手順については以下を参照してください。
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric サービス](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines と仮想マシン スケール セット](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [オンプレミスの仮想マシンまたは物理マシン](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights スナップショット デバッガーは App Services ランタイムの一部としてプレインストールされますが、App Service アプリのスナップショットを取得するには、スナップショット デバッガーを有効にする必要があります。 App Insights SDK をソース コードに組み込んでいる場合でも、アプリをデプロイしたら以下の手順に従ってスナップショット デバッガーを有効にしてください。

1. Azure Portal の **[App Services]** ウィンドウに移動します。
2. **[設定] > [Application Insights]** ウィンドウに移動します。

   ![App Service ポータルで App Insights を有効にする](./media/snapshot-debugger/applicationinsights-appservices.png)

3. ウィンドウの指示に従って新しいリソースを作成するか、既存の App Insights リソースを選択してアプリを監視します。 また、スナップショット デバッガーの両方のスイッチが**オン**になっていることも確認します。

   ![App Insights のサイト拡張機能を追加する][Enablement UI]

4. スナップショット デバッガーは、App Services のアプリ設定を使用して有効になりました。

    ![スナップショット デバッガーのアプリ設定][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>スナップショット デバッガーを無効にする

「**スナップショット デバッガーを有効にする**」と同じ手順に従ってください。ただし、スナップショット デバッガーの両方のスイッチを**オフ**にします。
アプリケーション例外の診断を容易にするために、すべてのアプリでスナップショット デバッガーを有効にすることをお勧めします。

## <a name="next-steps"></a>次の手順

* [Visual Studio での Application Insights の操作](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

