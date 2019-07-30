---
title: Application Insights を使用したライブ Azure App Service アプリのプロファイリング | Microsoft Docs
description: Application Insights Profiler を使用して Azure App Service でライブ アプリをプロファイルします。
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 71a8a0e268c1b264a0a1a7f955f310bfddc830d2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439936"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Application Insights を使用したライブ Azure App Service アプリのプロファイリング

Profiler は、Basic 以上のサービス レベルを使用し、Azure App Service で実行されている ASP.NET および ASP.NET Coreアプリで実行できます。 現時点では、[この方法](profiler-aspnetcore-linux.md)でのみ Linux で Profiler を有効にできます。

## <a id="installation"></a> アプリの Profiler を有効にする
アプリで Profiler を有効にするには、次の手順に従います。 別の種類の Azure サービスを実行している場合、以下のサポートされている他のプラットフォームで Profiler を有効にする手順を参照してください。
* [Cloud Services](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric アプリケーション](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtual Machines](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler は、App Services ランタイムの一部として事前インストールされています。 次の手順は、これを App Service で有効にする方法を示しています。 App Insights SDK をアプリケーションのビルド時に含めている場合でも、これらの手順に従ってください。

1. App Service の "Always On" 設定を有効にします。 この設定は、App Service の構成ページの [全般設定] で更新することができます。
1. Azure Portal の **[App Services]** ウィンドウに移動します。
1. **[設定] > [Application Insights]** ウィンドウに移動します。

   ![App Service ポータルで App Insights を有効にする](./media/profiler/AppInsights-AppServices.png)

1. ウィンドウの指示に従って新しいリソースを作成するか、既存の App Insights リソースを選択してアプリを監視します。 また、Profiler が**オン**になっていることを確認します。 Application Insights リソースと App Service のサブスクリプションが異なる場合、このページを使用して Application Insights を構成することはできません。 ただし、必要なアプリの設定を手動で作成することにより、手動で行うことはできます。 [次のセクションには、Profiler を手動で有効にするための手順が含まれています。](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![App Insights のサイト拡張機能を追加する][Enablement UI]

1. App Services のアプリ設定を使用して Profiler を有効にできるようになりました。

    ![Profiler 用のアプリ設定][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>手動または Azure Resource Manager を使用して Profiler を有効にする
Azure App Service のアプリ設定を作成することで、Application Insights Profiler を有効にすることができます。 これらのアプリ設定は、上記のオプションのページを使って作成します。 ただし、テンプレートやその他の手段を使えば、これらの設定の作成を自動化することができます。 これらの設定は、Application Insights リソースと Azure App Service のサブスクリプションが違っていても動作します。
Profiler を有効にするために必要な設定は次のとおりです。

|アプリ設定    | 値    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | Application Insights リソースの iKey    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


これらの値は、[Azure Resource Manager テンプレート](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager)、[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp) または [Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest) を使用して設定できます。

### <a name="enabling-profiler-for-other-clouds-manually"></a>他のクラウドに対して Profiler を手動で有効にする

他のクラウドに対して Profiler を有効にする場合は、次のアプリ設定を使用できます。

|アプリ設定    | 米国政府の値| China Cloud |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | https://agent.serviceprofiler.azure.us    | https://profiler.applicationinsights.azure.cn |
|ApplicationInsightsEndpoint | https://dc.applicationinsights.us | https://dc.applicationinsights.azure.cn |

## <a name="disable-profiler"></a>Profiler を無効にする

個々の Web アプリのインスタンスで Profiler を停止または再起動するには、 **[Web ジョブ]** で Web Apps リソースに移動します。 Profiler を削除するには、 **[拡張機能]** に移動します。

![Web ジョブで Profiler を無効にする][disable-profiler-webjob]

パフォーマンスの問題をできるだけ早く検出するために、すべてのアプリで Profiler を有効にすることをお勧めします。

WebDeploy を使用して Web アプリケーションへの変更をデプロイするときに、Profiler のファイルを削除できます。 App_Data フォルダーを除外しておけば、デプロイ中に削除されることを防ぐことができます。 


## <a name="next-steps"></a>次の手順

* [Visual Studio での Application Insights の操作](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
