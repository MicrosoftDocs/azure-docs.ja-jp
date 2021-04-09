---
title: Visual Studio で Azure Stream Analytics ジョブを表示する
description: Visual Studio を使用して、Azure Stream Analytics ジョブの表示、開始と停止、接続テスト、結果確認、およびエクスポートを行う方法を説明します。
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 07/10/2018
ms.openlocfilehash: 4b568fa8d9e2fb8bd00acb27b0dc3f7c45c09c57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019127"
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

1. **サーバー エクスプローラー** で **[Stream Analytics ジョブ]** を選択し、 **[更新]** を選択します。 **[Stream Analytics ジョブ]** の下にジョブが表示されます。

    ![Stream Analytics のサーバー エクスプローラーの一覧](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-list-jobs-01.png)

2. ジョブ ノードを展開し、 **[ジョブ ビュー]** ノードをダブルクリックしてジョブ ビューを開きます。
    
   ![展開されたジョブ ノード](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-job-view-01.png)

### <a name="start-and-stop-jobs"></a>ジョブの開始と停止

Azure Stream Analytics ジョブは、Visual Studio のジョブ ビューから完全に管理することができます。 コントロールを使用して、ジョブを開始、停止、または削除します。
    
   ![Stream Analytics ジョブのコントロール](./media/stream-analytics-vs-tools/azure-stream-analytics-job-view-controls.png)

## <a name="check-job-results"></a>ジョブ結果の確認

Visual Studio の Stream Analytics ツールは現在、Azure Data Lake Storage と BLOB ストレージの出力のプレビューをサポートしています。 結果を表示するには、**ジョブ ビュー** でジョブ ダイアグラムの出力ノードをダブルクリックして、適切な資格情報を入力するだけです。

   ![Stream Analytics ジョブの BLOB 出力](./media/stream-analytics-vs-tools/stream-analytics-blob-preview.png)

## <a name="export-jobs-to-a-project"></a>プロジェクトへのジョブのエクスポート

既存のジョブをプロジェクトにエクスポートする方法は 2 つあります。

1. **サーバー エクスプローラー** の [Stream Analytics ジョブ] ノードでジョブ ノードを右クリックします。 **[新しい Stream Analytics プロジェクトにエクスポート]** を選択します。
    
   ![プロジェクトへのジョブのエクスポート](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-01.png)
    
    生成されたプロジェクトが、**ソリューション エクスプローラー** に表示されます。
    
   ![ソリューション エクスプローラー](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-02.png)

2. ジョブ ビューで、 **[プロジェクトの生成]** を選択します。
    
   ![ジョブ ビューからのプロジェクトの生成](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="test-connections"></a>テスト接続

入力と出力の接続をテストするには、**ジョブ ビュー** で **[テスト接続]** ドロップダウンからオプションを選択します。

   ![[テスト接続] ドロップダウン](./media/stream-analytics-vs-tools/stream-analytics-test-connection-dropdown.png)

**テスト接続** の結果が **[出力]** ウィンドウに表示されます。

   ![テスト接続の結果](./media/stream-analytics-vs-tools/stream-analytics-test-connection-results.png)

## <a name="next-steps"></a>次のステップ

* [Visual Studio を使用して Azure Stream Analytics ジョブを監視および管理する](stream-analytics-monitor-jobs-use-vs.md)
* [クイック スタート: Visual Studio を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-vs.md)
* [Stream Analytics ツールで継続的に統合および開発する](stream-analytics-tools-for-visual-studio-cicd.md)
