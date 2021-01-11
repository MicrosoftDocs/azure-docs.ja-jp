---
title: Power BI を使用して Azure Cognitive Search のログとメトリックを視覚化する
description: Power BI を使用して Azure Cognitive Search のログとメトリックを視覚化する
manager: eladz
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 2caf4c66cdbb11ed42c4131ef1584c372ec10332
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935111"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>Power BI を使用して Azure Cognitive Search のログとメトリックを視覚化する
[Azure Cognitive Search](./search-what-is-azure-search.md) を使用すると、お使いの検索サービスに関する操作ログとサービス メトリックを Azure Storage アカウントに格納できます。 このページでは、Power BI テンプレート アプリを使用してその情報を視覚化する方法について説明します。 このアプリでは、検索、インデックス作成、操作、サービスのメトリックに関する情報など、お使いの検索サービスに関する詳細な分析情報が提供されます。

Power BI テンプレート アプリである **Azure Cognitive Search: Analyze Logs and Metrics** を [Power BI アプリのマーケットプレース](https://appsource.microsoft.com/marketplace/apps)で見つけることができます。

## <a name="how-to-get-started-with-the-app"></a>アプリの使用を開始する方法

1. 検索サービスに対してメトリックとリソースのログを有効にします。

    1. ログをアーカイブできる [Azure Storage アカウント](../storage/common/storage-account-create.md)を作成するか、既存のアカウントを特定します
    1. Azure portal 上で Azure Cognitive Search サービスに移動します
    1. 左側の列の [監視] セクションで、 **[診断設定]** を選択します

        ![Azure Cognitive Search サービスの [監視] セクションで [診断設定] を選択する方法を示すスクリーンショット。](media/search-monitor-logs-powerbi/diagnostic-settings.png)

    1. **[+ 診断設定を追加する]** を選択します
    1. **[ストレージ アカウントへのアーカイブ]** をオンにし、使用するストレージ アカウントの情報を入力して、 **[OperationLogs]** と **[AllMetrics]** をオンにします

        ![[診断設定] ページでメトリックおよびリソース ログを選択する方法を示すスクリーンショット。](media/search-monitor-logs-powerbi/add-diagnostic-setting.png)
    1. **[保存]** を選びます。

1. ログ記録を有効にしたら、検索サービスを使用してログとメトリックの生成を始めます。 これらのログを含むコンテナーが Blob Storage に表示されるまで最大 1 時間かかります。 検索トラフィック ログの **insights-logs-operationlogs** コンテナーと、メトリックの **insights-metrics-pt1m** コンテナーが表示されます。

1. [Power BI アプリのマーケットプレース](https://appsource.microsoft.com/marketplace/apps)で Azure Cognitive Search Power BI アプリを検索し、新しいワークスペースまたは既存のワークスペースにインストールします。 このアプリの名称は、**Azure Cognitive Search: Analyze Logs and Metrics** です。

1. アプリをインストールした後、Power BI でアプリの一覧からそのアプリを選択します。

    ![アプリの一覧から選択する Azure Cognitive Search アプリを示すスクリーンショット。](media/search-monitor-logs-powerbi/azure-search-app-tile.png)

1. **[接続]** を選択してデータを接続します。

    ![Azure Cognitive Search アプリでデータに接続する方法を示すスクリーンショット。](media/search-monitor-logs-powerbi/get-started-with-your-new-app.png)

1. ログとメトリックが含まれているストレージ アカウントの名前を入力します。 このアプリの既定では、過去 10 日間のデータが参照されますが、この値は **Days** パラメーターを使用して変更できます。

    ![Azure Cognitive Search に接続するページで問い合わせるストレージ アカウント名と日数を入力する方法を示すスクリーンショット。](media/search-monitor-logs-powerbi/connect-to-storage-account.png)

1. 認証方法として **[キー]** を選択し、お使いのストレージ アカウント キーを指定します。 プライバシー レベルとして **[非公開]** を選択します。 [サインイン] をクリックして、読み込みプロセスを開始します。

    ![Azure Cognitive Search に接続するページで認証方法、アカウント キー、プライバシー レベルを入力する方法を示すスクリーンショット。](media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png)

1. データが最新の情報に更新されるまで待ちます。 どれだけの量のデータがあるかによって、これには時間がかかる場合があります。 次のインジケーターに基づいて、データがまだ更新中かどうかを確認できます。

    ![データ更新ページで情報を読む方法を示すスクリーンショット。](media/search-monitor-logs-powerbi/workspace-view-refreshing.png)

1. データ更新が完了したら、 **[Azure Cognitive Search Report]\(Azure Cognitive Search レポート\)** を選択してレポートを表示します。

    ![データ更新ページで Azure Cognitive Search Report を選択する方法を示すスクリーンショット。](media/search-monitor-logs-powerbi/workspace-view-select-report.png)

1. レポートを開いた後にページを更新し、レポートにデータが表示されるようにしてください。

    ![Azure Cognitive Search Power BI レポートのスクリーンショット。](media/search-monitor-logs-powerbi/powerbi-search.png)

## <a name="how-to-change-the-app-parameters"></a>アプリのパラメーターを変更する方法
別のストレージ アカウントのデータを視覚化する場合や、何日分のデータにクエリを実行するかを変更する場合は、次の手順に従って、**Days** パラメーターと **StorageAccount** パラメーターを変更します。

1. Power BI アプリに移動し、Azure Cognitive Search アプリを見つけて、 **[アプリの編集]** ボタンを選択してワークスペースを表示します。

    ![Azure Cognitive Search アプリの [アプリの編集] ボタンを選択する方法を示すスクリーンショット。](media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png)

1. データセットのオプションから **[設定]** を選択します。

    ![Azure Cognitive Search データセット オプションから [設定] を選択する方法を示すスクリーンショット。](media/search-monitor-logs-powerbi/workspace-view-select-settings.png)

1. [データセット] タブで、パラメーターの値を変更し、 **[適用]** を選択します。 接続に問題がある場合は、同じページでデータ ソースの資格情報を更新します。

1. ワークスペースに戻り、データセットのオプションから **[今すぐ更新]** を選択します。

    ![Azure Congnitive Search データセット オプションから [今すぐ更新] を選択する方法を示すスクリーンショット。](media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png)

1. レポートを開くと、更新されたデータが表示されます。 また、最新のデータを表示するために、レポートを最新の情報に更新することが必要な場合もあります。

## <a name="troubleshooting"></a>トラブルシューティング
データが表示されない場合は、次のトラブルシューティング手順に従います。

1. レポートを開き、ページを最新の情報に更新して、最新のデータが表示されていることを確認します。 データを最新の情報に更新するオプションがレポートにあります。 これを選択して最新のデータを取得します。

1. 指定したストレージ アカウント名とアクセス キーが正しいことを確認します。 ストレージ アカウント名は、検索サービス ログに構成したアカウントに一致している必要があります。

1. ストレージ アカウントに **insights-logs-operationlogs** コンテナーと **insights-metrics-pt1m** コンテナーが含まれていて、各コンテナーにデータがあることを確認します。 ログとメトリックは、いくつかのフォルダー階層に配置されます。

1. データセットがまだ更新中であるかどうかを確認します。 更新状態インジケーターは、上記の手順 8 に示されています。 まだ更新中の場合は、更新が完了するまで待ってから、レポートを開いて最新の状態に更新します。

## <a name="next-steps"></a>次のステップ
[Azure Cognitive Search の詳細情報](./index.yml)

[Power BI とは?](/power-bi/fundamentals/power-bi-overview)

[Power BI サービスのデザイナー向けの基本的な概念](/power-bi/service-basic-concepts)