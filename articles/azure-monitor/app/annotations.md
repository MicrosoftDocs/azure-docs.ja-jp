---
title: Application Insights のリリース注釈 | Microsoft Docs
description: Application Insights で、メトリックス エクスプローラーのグラフにデプロイ マーカーまたはビルド マーカーを追加します。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 23173e33-d4f2-4528-a730-913a8fd5f02e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mbullwin
ms.openlocfilehash: e3ec202ba6126b150fb78c76591682f163018661
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604543"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Application Insights のメトリック グラフの注釈

[メトリックス エクスプローラー](../../azure-monitor/app/metrics-explorer.md)のグラフの注釈では、新しいビルドのデプロイ先やその他の重要なイベントが示されます。 注釈により、変更内容がアプリケーションのパフォーマンスに影響を与えたかどうかを簡単に把握できます。 それらは、[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/tasks/) ビルド システムで自動的に作成できます。 PowerShell から作成することにより、任意のイベントにフラグを設定する注釈を作成することもできます。

> [!NOTE]
> この記事には、非推奨の**クラシック メトリック エクスペリエンス**が反映されています。 現在、注釈は、クラシック エクスペリエンスと **[ブック](../../azure-monitor/app/usage-workbooks.md)** においてのみ使用できます。 現在のメトリック エクスペリエンスについて詳しくは、「[Azure メトリックス エクスプローラーの高度な機能](../../azure-monitor/platform/metrics-charts.md)」をご覧ください。

![注釈の例](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Azure Pipelines ビルドでのリリース注釈

リリース注釈は、Azure DevOps のクラウド ベースの Azure Pipelines サービスの機能です。

### <a name="install-the-annotations-extension-one-time"></a>注釈拡張機能のインストール (1 回限り)
リリース注釈を作成できるようにするには、Visual Studio Marketplace で入手可能な Azure DevOps 拡張機能のいずれかをインストールする必要があります。

1. [Azure DevOps](https://azure.microsoft.com/services/devops/) プロジェクトにサインインします。
   
1. Visual Studio Marketplace の[リリース注釈拡張機能](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)のページで、自分の Azure DevOps 組織を選択し、 **[インストール]** を選択して Azure DevOps 組織に拡張機能を追加します。
   
   ![Azure DevOps 組織を選択してから、[インストール] を選択します。](./media/annotations/1-install.png)
   
Azure DevOps 組織に拡張機能をインストールする必要があるのは一度だけです。 これで、組織内の任意のプロジェクトに対してリリース注釈を構成できるようになります。

### <a name="configure-release-annotations"></a>リリース注釈を構成する

Azure Pipelines のリリース テンプレートごとに個別の API キーを作成します。

1. [Azure portal](https://portal.azure.com) にサインインし、アプリケーションを監視する Application Insights リソースを開きます。 または、まだない場合は、[新しい Application Insights リソースを作成](../../azure-monitor/app/app-insights-overview.md)します。
   
1. **[API アクセス]** タブを開き、 **[Application Insights ID]** をコピーします。
   
   ![[API アクセス] で、アプリケーション ID をコピーします。](./media/annotations/2-app-id.png)

1. 別のブラウザー ウィンドウで、Azure Pipelines のデプロイを管理するリリース テンプレートを開くか、作成します。
   
1. **[タスクの追加]** を選択した後、メニューから **[Application Insights Release Annotation]\(Application Insights リリース注釈\)** タスクを選択します。
   
   ![[タスクの追加] をクリックし、[Application Insights Release Annotation]\(Application Insights リリース注釈\) を選択する。](./media/annotations/3-add-task.png)
   
1. **[アプリケーション ID]** に、 **[API アクセス]** タブからコピーした Application Insights ID を貼り付けます。
   
   ![Application Insights ID を貼り付けます。](./media/annotations/4-paste-app-id.png)
   
1. Application Insights の **[API アクセス]** ウィンドウに戻り、 **[API キーの作成]** を選択します。 
   
   ![[API アクセス] タブで、[API キーの作成] を選択する。](./media/annotations/5-create-api-key.png)
   
1. **[API キーの作成]** ウィンドウで、説明を入力し、 **[コメントを書く]** を選択して、 **[キーの生成]** を選択します。 新しいキーをコピーします。
   
   ![[API キーの作成] ウィンドウで、説明を入力し、[コメントを書く] を選択して、[キーの生成] を選択します。](./media/annotations/6-create-api-key.png)
   
1. リリース テンプレート ウィンドウの **[変数]** タブで、 **[追加]** を選択して新しい API キーの変数定義を作成します。

1. **[名前]** に「`ApiKey`」と入力し、 **[値]** に **[API アクセス]** タブからコピーした API キーを貼り付けます。
   
   ![Azure DevOps の [変数] タブで、[追加] を選択し、変数名を ApiKey にして、[値] に API キーを貼り付ける。](./media/annotations/7-paste-api-key.png)
   
1. メイン リリース テンプレート ウィンドウで **[保存]** 選択して、テンプレートを保存します。

## <a name="view-annotations"></a>注釈を表示する
これで、このリリース テンプレートを使用して新しいリリースをデプロイするたびに、注釈が Application Insights に送信されるようになります。 注釈は、**メトリックス エクスプローラー**のグラフに表示されます。

注釈マーカー (薄いグレーの矢印) を選択すると、要求元、ソース管理の分岐、リリース パイプライン、環境を含む、リリースに関する詳細が表示されます。

![リリース注釈マーカーを選択する。](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>PowerShell からカスタム注釈を作成する
Azure DevOps を使わずに、GitHub の [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) PowerShell スクリプトを使って、任意のプロセスから注釈を作成できます。 

1. [CreateReleaseAnnotation.ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) のローカル コピーを作成します。
   
1. 前の手順のステップを使って、Application Insights ID を取得し、Application Insights の **[API アクセス]** タブから API キーを作成します。
   
1. 次のコードで PowerShell スクリプトを呼び出します。角かっこのプレースホルダーは実際の値に置き換えます。 `-releaseProperties` は省略可能です。 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

過去に関する注釈を作成する場合など、スクリプトを変更できます。

## <a name="next-steps"></a>次の手順

* [作業項目を作成する](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [PowerShell でのオートメーション](../../azure-monitor/app/powershell.md)
