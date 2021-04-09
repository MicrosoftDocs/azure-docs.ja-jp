---
title: Azure Application Insights を使ってランタイムの例外を診断する | Microsoft Docs
description: Azure Application Insights を使用して、お使いのアプリケーションのランタイムの例外を見つけて診断するチュートリアルです。
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 09/19/2017
ms.custom: mvc
ms.openlocfilehash: 98ccaef716ae2390dcbcfbc7c4a1916359115f93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100597636"
---
# <a name="find-and-diagnose-run-time-exceptions-with-azure-application-insights"></a>Azure Application Insights でランタイムの例外を見つけて診断する

Azure Application Insights は、お使いのアプリケーションからテレメトリを収集し、ランタイムの例外を特定して診断するのに役立てます。  このチュートリアルでは、アプリケーションを使用してこのプロセスを見ていきます。  学習内容は次のとおりです。

> [!div class="checklist"]
> * プロジェクトを変更して、例外の追跡を有効にする
> * アプリケーションの各種コンポーネントに対する例外を特定する
> * 例外の詳細を確認する
> * デバッグ用の例外のスナップショットをVisual Studio にダウンロードする
> * クエリ言語を使って失敗した要求の詳細を分析する
> * 欠陥のあるコードを修正する新しい作業項目を作成する


## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- 次のワークロードを使って、[Visual Studio 2019](https://www.visualstudio.com/downloads/) をインストールします。
    - ASP.NET および Web の開発
    - Azure の開発
- [Visual Studio Snapshot Debugger](https://aka.ms/snapshotdebugger) をダウンロードしてインストールします。
- [Visual Studio Snapshot Debugger](../app/snapshot-debugger.md) を有効にします
- .NET アプリケーションを Azure にデプロイし、[Application Insights SDK の有効化](../app/asp-net.md)を実行します。 
- このチュートリアルでは、お使いのアプリケーションで例外の ID を追跡し、例外を生成する開発環境またはテスト環境でお使いのコードを変更します。 

## <a name="log-in-to-azure"></a>Azure にログインする
Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にログインします。


## <a name="analyze-failures"></a>エラーを分析する
Application Insights は、お使いのアプリケーションのエラーを収集し、さまざまな操作全体のエラー頻度を表示して、影響が最も高いエラーに専念できるようにします。  その後、これらのエラーの詳細を掘り下げて、根本原因を特定できます。   

1. **Application Insights** を選んでから、ご利用のサブスクリプションを選びます。  
2. **[失敗]** パネルを開くには、 **[調査]** メニューの下にある **[失敗]** を選ぶか、 **[失敗した要求]** グラフをクリックします。

    ![失敗した要求](media/tutorial-runtime-exceptions/failed-requests.png)

3. **[失敗した要求]** パネルは、失敗した要求の数と、アプリケーションの各操作の影響を受けたユーザーの数を表示します。  ユーザーがこの情報を並べ替えることによって、ユーザーに大きく影響するそのようなエラーを特定できます。  この例では、**GET Employees/Create (従業員を取得/作成)**  と **GET Customers/Details (顧客を取得/詳細)** 　が、エラーと影響を受けたユーザーの数が多いため、有力な調査候補です。  操作を選ぶと、右のパネルにこの操作のより詳しい情報が表示されます。

    ![失敗した要求パネル](media/tutorial-runtime-exceptions/failed-requests-blade.png)

4. エラー発生率が急に増加している期間を詳しく見るには、タイム ウィンドウを狭めます。

    ![[失敗した要求] ウィンドウ](media/tutorial-runtime-exceptions/failed-requests-window.png)

5. フィルター処理された結果の数のボタンをクリックすると、関連するサンプルが表示されます。 "提案される" サンプルには、たとえサンプリングがいずれかのコンポーネントで有効であったとしても、すべてのコンポーネントの関連するテレメトリーが含まれます。 検索結果をクリックすると、エラーの詳細が表示されます。

    ![失敗した要求サンプル](media/tutorial-runtime-exceptions/failed-requests-search.png)

6. 失敗した要求の詳細にはガント チャートが表示されます。このチャートから、このトランザクションに 2 つの依存関係のエラーがあり、トランザクションの合計持続時間のうち 50% を超える時間を占めていることがわかります。 このエクスペリエンスは、この操作 ID に関連する分散アプリケーションのコンポーネント全体のすべてのテレメトリを示します。 [この新しいエクスペリエンスの詳細を参照してください](../app/transaction-diagnostics.md)。 いずれかの項目を選択すると、右側に詳細が表示されます。 

    ![失敗した要求の詳細](media/tutorial-runtime-exceptions/failed-request-details.png)

7. 操作の詳細には、エラーを引き起こしたと思われる FormatException も表示されます。  無効な郵便番号が原因であることを確認できます。 デバッグのスナップショットを開くと、Visual Studio でコード レベルのデバッグ情報を確認できます。

    ![例外の詳細](media/tutorial-runtime-exceptions/failed-requests-exception.png)

## <a name="identify-failing-code"></a>欠陥コードを特定する
Snapshot Debugger は、お使いのアプリケーションで最も一般的な例外のスナップショットを収集し、運用環境におけるその根本原因の診断をサポートします。  ポータルで [Debug Snapshots (デバッグ スナップショット)] を表示して、コール スタックを表示し、各呼び出しスタック フレームで変数を確認できます。 その後、スナップショットをダウンロードして Visual Studio 2019 で開くことで、ソース コードをデバッグできます。

1. 例外のプロパティで、 **[デバッグ スナップショットを開く]** をクリックします。
2. 要求のコール スタックで **[スナップショットのデバッグ]** パネルが開きます。  いずれかの方法をクリックして、要求時のすべてのローカル変数の値を表示します。  この例では、1 番目の方法から順に、値を持たないローカル変数が確認できます。

    ![スナップショットをデバッグする](media/tutorial-runtime-exceptions/debug-snapshot-01.png)

3. 有効な値を持つ最初の呼び出しが **ValidZipCode** であるため、郵便番号が整数に変換できない文字で提供されていたことが確認できます。  これは、修正する必要があるコードのエラーのようです。

    ![修正が必要なコード エラーを示すスクリーンショット。    ](media/tutorial-runtime-exceptions/debug-snapshot-02.png)

4. このオプションを Visual Studio にダウンロードして、修正する必要がある実際のコードをそこで見つけることができます。 これを行うには、 **[スナップショットのダウンロード]** をクリックします。
5. スナップショットは、Visual Studio に読み込まれます。
6. これで、例外の原因となったコード行を素早く特定するデバッグ セッションを Visual Studio Enterprise で実行できます。

    ![コードの例外](media/tutorial-runtime-exceptions/exception-code.png)


## <a name="use-analytics-data"></a>分析データを使用する
Application Insights によって収集されたすべてのデータはAzure Log Analytics に保存され、さまざまな方法でデータを分析できるようにする高度なクエリ言語が提供されます。  このデータを使って、調査中の例外を生成した要求を分析できます。 

1. コードの上の CodeLens 情報をクリックして、Application Insights が提供するテレメトリを表示します。

    ![コード](media/tutorial-runtime-exceptions/codelens.png)

1. **[影響の分析]** をクリックして、Application Insights Analytics を開きます。  影響を受けたユーザー、ブラウザー、リージョンなどの失敗した要求に関する詳細情報を提供するいくつかのクエリが表示されます。<br><br>![複数のクエリを含んだ [Application Insights] ウィンドウを示すスクリーンショット。](media/tutorial-runtime-exceptions/analytics.png)<br>

## <a name="add-work-item"></a>作業項目を追加する
Application Insights を Azure DevOps や GitHub などの追跡システムに接続する場合は、Application Insights から直接、作業項目を作成できます。

1. Application Insights の **[Exception Properties]\(例外プロパティ\)** パネルに戻ります。
2. **[新しい作業項目]** をクリックします。
3. **[新しい作業項目]** パネルが開き、既に取り込まれている例外の詳細が表示されます。  保存する前にその他の情報を追加できます。

    ![新しい作業項目](media/tutorial-runtime-exceptions/new-work-item.png)

## <a name="next-steps"></a>次のステップ
これで、ランタイムの例外を特定する方法を学習しました。次のチュートリアルに進んで、パフォーマンスの問題を特定して診断する方法をご覧ください。

> [!div class="nextstepaction"]
> [パフォーマンスの問題を特定する](./tutorial-performance.md)

