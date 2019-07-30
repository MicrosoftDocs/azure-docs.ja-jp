---
title: Visual Studio で Azure Stream Analytics ジョブを表示する
description: この記事では、Visual Studio で Azure Stream Analytics ジョブを表示する方法について説明します。
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: 1c7133801eb0d95616cacf501162e6cee3da7c80
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "61477910"
---
# <a name="use-visual-studio-to-view-azure-stream-analytics-jobs"></a>Visual Studio を使用して Azure Stream Analytics ジョブを表示する

Visual Studio の Azure Stream Analytics ツールを使用すると、開発者は IDE から直接、Stream Analytics ジョブを容易に管理できるようになります。 Azure Stream Analytics ツールを使用すると、次のことができます。
- [新しいジョブの作成](stream-analytics-quick-create-vs.md)
- ジョブの開始、停止、および[管理](stream-analytics-monitor-jobs-use-vs.md)
- ジョブ結果の確認
- 既存のジョブのプロジェクトへのエクスポート
- 入力と出力の接続のテスト
- [ローカルでのクエリの実行](stream-analytics-vs-tools-local-run.md)

Visual Studio の Azure Stream Analytics ツールのインストール方法については、[こちら](stream-analytics-tools-for-visual-studio-install.md)をご覧ください。

## <a name="explore-the-job-view"></a>ジョブ ビューを調べる

ジョブ ビューを使用して、Visual Studio から Azure Stream Analytics ジョブとやりとりすることができます。

### <a name="open-the-job-view"></a>ジョブ ビューを開く

1. **サーバー エクスプローラー**で **[Stream Analytics ジョブ]** を選択し、 **[更新]** を選択します。 **[Stream Analytics ジョブ]** の下にジョブが表示されます。

    ![Stream Analytics のサーバー エクスプローラーの一覧](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-list-jobs-01.png)



2. ジョブ ノードを展開し、 **[ジョブ ビュー]** ノードをダブルクリックしてジョブ ビューを開きます。
    
   ![展開されたジョブ ノード](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-job-view-01.png)

### <a name="start-and-stop-jobs"></a>ジョブの開始と停止

Azure Stream Analytics ジョブは、Visual Studio のジョブ ビューから完全に管理することができます。 コントロールを使用して、ジョブを開始、停止、または削除します。
    
   ![Stream Analytics ジョブのコントロール](./media/stream-analytics-vs-tools/azure-stream-analytics-job-view-controls.png)


## <a name="check-job-results"></a>ジョブ結果の確認

Visual Studio の Stream Analytics ツールは現在、Azure Data Lake Storage と BLOB ストレージの出力のプレビューをサポートしています。 結果を表示するには、**ジョブ ビュー**でジョブ ダイアグラムの出力ノードをダブルクリックして、適切な資格情報を入力するだけです。

   ![Stream Analytics ジョブの BLOB 出力](./media/stream-analytics-vs-tools/stream-analytics-blob-preview.png)


## <a name="export-jobs-to-a-project"></a>プロジェクトへのジョブのエクスポート

既存のジョブをプロジェクトにエクスポートする方法は 2 つあります。

1. **サーバー エクスプローラー**の [Stream Analytics ジョブ] ノードでジョブ ノードを右クリックします。 **[新しい Stream Analytics プロジェクトにエクスポート]** を選択します。
    
   ![プロジェクトへのジョブのエクスポート](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-01.png)
    
    生成されたプロジェクトが、**ソリューション エクスプローラー**に表示されます。
    
   ![ソリューション エクスプローラー](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-02.png)

2. ジョブ ビューで、 **[プロジェクトの生成]** を選択します。
    
   ![ジョブ ビューからのプロジェクトの生成](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="test-connections"></a>テスト接続

入力と出力の接続をテストするには、**ジョブ ビュー**で **[テスト接続]** ドロップダウンからオプションを選択します。

   ![[テスト接続] ドロップダウン](./media/stream-analytics-vs-tools/stream-analytics-test-connection-dropdown.png)

**テスト接続**の結果が **[出力]** ウィンドウに表示されます。

   ![テスト接続の結果](./media/stream-analytics-vs-tools/stream-analytics-test-connection-results.png)

## <a name="next-steps"></a>次の手順

* [Visual Studio を使用して Azure Stream Analytics ジョブを監視および管理する](stream-analytics-monitor-jobs-use-vs.md)
* [クイック スタート:Visual Studio を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-vs.md)
* [チュートリアル:Azure Pipelines を使用して CI/CD で Azure Stream Analytics ジョブをデプロイする](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Stream Analytics ツールで継続的に統合および開発する](stream-analytics-tools-for-visual-studio-cicd.md)
