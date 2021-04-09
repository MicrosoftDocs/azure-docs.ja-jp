---
title: Application Insights を使用したライブ Azure App Service アプリのプロファイリング | Microsoft Docs
description: Application Insights Profiler を使用して Azure App Service でライブ アプリをプロファイルします。
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 2a5ba6cb0778e0c013f150d787d694b23f0497bd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218011"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Application Insights を使用したライブ Azure App Service アプリのプロファイリング

Profiler は、Basic 以上のサービス レベルを使用し、Azure App Service で実行されている ASP.NET および ASP.NET Coreアプリで実行できます。 現時点では、[この方法](profiler-aspnetcore-linux.md)でのみ Linux で Profiler を有効にできます。

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a> アプリの Profiler を有効にする
アプリで Profiler を有効にするには、次の手順に従います。 別の種類の Azure サービスを実行している場合、以下のサポートされている他のプラットフォームで Profiler を有効にする手順を参照してください。
* [Cloud Services](./profiler-cloudservice.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Service Fabric アプリケーション](./profiler-servicefabric.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Virtual Machines](./profiler-vm.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

Application Insights Profiler は、App Services ランタイムの一部として事前インストールされています。 次の手順は、これを App Service で有効にする方法を示しています。 App Insights SDK をアプリケーションのビルド時に含めている場合でも、これらの手順に従ってください。

> [!NOTE]
> Application Insights Profiler のコードなしインストールは .NET Core サポート ポリシーに準拠します。
> サポートされているランタイムの詳細については、[.Net Core サポート ポリシー](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)に関するページを参照してください。

1. ご自身の App Service の Azure コントロール パネルに移動します。
1. App Service の "Always On" 設定を有効にします。 この設定を確認するには、 **[設定]** 、 **[構成]** ページ (次の手順のスクリーンショットを参照) の順にクリックし、 **[全般設定]** タブを選択します。
1. **[設定] > [Application Insights]** ページに移動します。

   ![App Service ポータルで App Insights を有効にする](./media/profiler/AppInsights-AppServices.png)

1. ウィンドウの指示に従って新しいリソースを作成するか、既存の App Insights リソースを選択してアプリを監視します。 また、Profiler が **オン** になっていることを確認します。 Application Insights リソースと App Service のサブスクリプションが異なる場合、このページを使用して Application Insights を構成することはできません。 ただし、必要なアプリの設定を手動で作成することにより、手動で行うことはできます。 [次のセクションには、Profiler を手動で有効にするための手順が含まれています。](#enable-profiler-manually-or-with-azure-resource-manager) 

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


これらの値は、[Azure Resource Manager テンプレート](./azure-web-apps.md#app-service-application-settings-with-azure-resource-manager)、[Azure PowerShell](/powershell/module/az.websites/set-azwebapp) または [Azure CLI](/cli/azure/webapp/config/appsettings) を使用して設定できます。

## <a name="enable-profiler-for-other-clouds"></a>他のクラウドの Profiler を有効にする

現在、エンドポイントの変更が必要なリージョンは [Azure Government](https://docs.microsoft.com/azure/azure-government/compare-azure-government-global-azure#application-insights) と [Azure China](https://docs.microsoft.com/azure/china/resources-developer-guide) のみです。

|アプリ設定    | 米国政府のクラウド | China Cloud |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://profiler.monitor.azure.us`    | `https://profiler.monitor.azure.cn` |
|ApplicationInsightsEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

## <a name="disable-profiler"></a>Profiler を無効にする

個々のアプリのインスタンスに対して Profiler を停止または再起動するには、左のサイドバーで **[Web ジョブ]** を選択し、`ApplicationInsightsProfiler3` という名前の Web ジョブを停止します。

  ![Web ジョブで Profiler を無効にする][disable-profiler-webjob]

パフォーマンスの問題をできるだけ早く検出するために、すべてのアプリで Profiler を有効にすることをお勧めします。

WebDeploy を使用して Web アプリケーションへの変更をデプロイするときに、Profiler のファイルを削除できます。 App_Data フォルダーを除外しておけば、デプロイ中に削除されることを防ぐことができます。 


## <a name="next-steps"></a>次のステップ

* [Visual Studio での Application Insights の操作](./visual-studio.md)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png

