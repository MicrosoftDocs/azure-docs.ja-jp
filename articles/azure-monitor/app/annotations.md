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
ms.date: 11/08/2018
ms.author: mbullwin
ms.openlocfilehash: 652591fc4539e6f19c0606c1502609a823327f2b
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55811020"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Application Insights のメトリック グラフの注釈

[メトリックス エクスプローラー](../../azure-monitor/app/metrics-explorer.md)のグラフの注釈により、新しいビルドのデプロイ先やその他の重要なイベントが示されます。 これにより、変更内容がアプリケーションのパフォーマンスに影響を与えたかどうかを簡単に把握できます。 注釈は、[Azure DevOps Services ビルド システム](https://docs.microsoft.com/azure/devops/pipelines/tasks/)により自動で作成されます。 PowerShell から作成することにより、任意のイベントにフラグを設定する注釈を作成することもできます。

> [!NOTE]
> この記事には、非推奨の**クラシック メトリック エクスペリエンス**が反映されています。 現在、注釈は、クラシック エクスペリエンスと**[ブック](../../azure-monitor/app/usage-workbooks.md)** においてのみ使用できます。 現在のメトリック エクスペリエンスについて詳しくは、[こちらの記事](../../azure-monitor/platform/metrics-charts.md)をご覧ください。

![サーバー応答時間と明確な相関関係のある注釈の例](./media/annotations/00.png)

## <a name="release-annotations-with-azure-devops-services-build"></a>Azure DevOps Services ビルドを使用したリリース注釈

リリース注釈は、Azure DevOps Services のクラウド ベースの Azure Pipelines サービスの機能です。 

### <a name="install-the-annotations-extension-one-time"></a>注釈拡張機能のインストール (1 回限り)
リリース注釈を作成できるようにするには、Visual Studio Marketplace で入手可能な Azure DevOps Services 拡張機能のいずれかをインストールする必要があります。

1. [Azure DevOps Services](https://visualstudio.microsoft.com/vso/) プロジェクトにサインインします。
2. Visual Studio Marketplace で [リリース注釈拡張機能を取得](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)して、Azure DevOps Services 組織に追加します。

![Azure DevOps Services Web ページの右上で Marketplace を開きます。 [Azure DevOps Services] を選択し、[Azure Pipelines] の下で [さらに参照する] を選択します。](./media/annotations/10.png)

これは、Azure DevOps Services 組織に対して一度だけ行う必要があります。 これで、リリース注釈を組織のプロジェクトに対して構成できるようになりました。 

### <a name="configure-release-annotations"></a>リリース注釈を構成する

Azure DevOps Services リリース テンプレートごとに別個の API キーを取得する必要があります。

1. [Microsoft Azure Portal](https://portal.azure.com) にサインインし、アプリケーションを監視する Application Insights リソースを開きます  (まだリソースを作成していない場合は、[この時点で作成します](../../azure-monitor/app/app-insights-overview.md))。
2. **[API アクセス]** を開き、**Application Insights Id** をコピーします。
   
    ![portal.azure.com で、Application Insights リソースを開き、[設定] を選択します。 [API アクセス] を開きます。 アプリケーション ID をコピーする](./media/annotations/20.png)

4. 別のブラウザー ウィンドウで、Azure DevOps Services のデプロイを管理するリリース テンプレートを開きます (または作成します)。 
   
    タスクを追加し、メニューから [Application Insights Release Annotation]\(Application Insights リリース注釈) タスクを選択します。
   
    [API アクセス] ブレードからコピーした **アプリケーション ID** を貼り付けます。
   
    ![Azure DevOps Services で、[リリース] を開き、リリース パイプラインを選択して [編集] を選択します。 [タスクの追加] をクリックし、[Application Insights Release Annotation] (Application Insights リリース注釈) を選択します。 Application Insights ID を貼り付けます。](./media/annotations/30.png)
4. **[APIKey]** フィールドを `$(ApiKey)` 変数に設定します。

5. Azure ウィンドウに戻り、新しい API キーを作成し、それをコピーします。
   
    ![[Azure] ウィンドウの [API アクセス] ブレードで、[API キーの作成] をクリックします。 コメントを指定し、[書き込み] 注釈にチェックを入れ、[キーの生成] をクリックします。 新しいキーをコピーします。](./media/annotations/40.png)

6. リリース テンプレートの [構成] タブを開きます。
   
    `ApiKey`の変数の定義を作成します。
   
    ApiKey の変数の定義に API キーを貼り付けます。
   
    ![[Azure DevOps Services] ウィンドウで、[構成] タブを選択し、[変数の追加] をクリックします。 ApiKey の名前を設定し、[値] に生成したキーを貼り付けて、ロック アイコンをクリックします。](./media/annotations/50.png)
7. 最後に、リリース パイプラインを **[保存]** します。


## <a name="view-annotations"></a>注釈を表示する
これで、このリリース テンプレートを使用して新しいリリースをデプロイするたびに、注釈が Application Insights に送信されるようになりました。 注釈は、メトリックス エクスプローラーのグラフに表示されます。

要求元、ソース管理の分岐、リリース パイプライン、環境などを含む、リリースに関する詳細を表示するための注釈マーカーをクリックします。

![任意のリリース注釈マーカーをクリックします。](./media/annotations/60.png)

## <a name="create-custom-annotations-from-powershell"></a>PowerShell からカスタム注釈を作成する
(Azure DevOps Services を利用せずに) あらゆるプロセスから注釈を作成することもできます。 


1. [GitHub から Powershell スクリプト](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)のローカル コピーを作成します。

2. [API アクセス] ブレードからアプリケーション ID を取得して API キーを作成します。

3. 次のようなスクリプトを呼び出します。

```PS

     .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }
```

このスクリプトは、過去に関する注釈を作成するなどの場合に簡単に修正できます。

## <a name="next-steps"></a>次の手順

* [作業項目を作成する](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [PowerShell でのオートメーション](../../azure-monitor/app/powershell.md)
