---
title: "Visual Studio Team Services を使用してアプリケーションのロード テストを実行する | Microsoft Docs"
description: "Visual Studio Team Services を使用して Azure Service Fabric アプリケーションのストレス テストを実行する方法について説明します。"
services: service-fabric
documentationcenter: na
author: cawams
manager: timlt
editor: 
ms.assetid: fc743585-0d1b-483f-981d-493f4552ac07
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/18/2016
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: e8e270ce865d4da3ee219958b308db2c1c89b11b


---
# <a name="load-test-your-application-by-using-visual-studio-team-services"></a>Visual Studio Team Services を使用してアプリケーションのロード テストを実行する
この記事では、Microsoft Visual Studio ロード テスト機能を使用して、アプリケーションのストレス テストを実行する方法について説明します。 この機能では、Azure Service Fabric のステートフル サービス バックエンドとステートレス サービス Web フロントエンドが使用されます。 この記事で使用するサンプル アプリケーションは、飛行機の位置特定シミュレーターです。 ユーザーが飛行機の ID、出発時刻、および目的地を指定すると、 アプリケーションのバックエンドによって要求が処理され、フロントエンドによって条件に一致する飛行機がマップに表示されます。

次の図は、テストする Service Fabric アプリケーションを示しています。

![航空機の位置を特定するサンプル アプリケーションの図][0]

## <a name="prerequisites"></a>前提条件
始める前に、次の手順を実行する必要があります。

* Visual Studio Team Services アカウントを取得する。 [Visual Studio Team Services](https://www.visualstudio.com)で無料で取得できます。
* Visual Studio 2013 または Visual Studio 2015 を取得してインストールする。 この記事では、Visual Studio 2015 Enterprise エディションを使用しますが、Visual Studio 2013 など他のエディションも同様に機能します。
* ステージング環境にアプリケーションをデプロイする。 詳細については、 [Visual Studio を使用してリモート クラスターにアプリケーションをデプロイする方法](service-fabric-publish-app-remote-cluster.md) を参照してください。
* アプリケーションの使用パターンを理解する。 この情報は、ロード パターンをシミュレートするために使用されます。
* ロード テストの目標を理解する。 これは、ロード テストの結果の解釈と分析に役立ちます。

## <a name="create-and-run-the-web-performance-and-load-test-project"></a>Web パフォーマンスとロード テストのプロジェクトの作成と実行
### <a name="create-a-web-performance-and-load-test-project"></a>Web パフォーマンスとロード テストのプロジェクトを作成する
1. Visual Studio 2015 を開きます。 メニュー バーで、**[ファイル]** > **[新規作成]** > **[プロジェクト]** の順に選択し、**[新しいプロジェクト]** ダイアログ ボックスを開きます。
2. **Visual C#** ノードを展開し、**[テスト]** > **[Web パフォーマンスとロード テストのプロジェクト]** を選択します。 プロジェクトの名前を入力し、 **[OK]** をクリックします。

    ![[新しいプロジェクト] ダイアログ ボックスのスクリーン ショット][1]

    新しい Web パフォーマンスとロード テストのプロジェクトがソリューション エクスプローラーに表示されます。

    ![新しいプロジェクトを表示したソリューション エクスプ ローラーのスクリーン ショット][2]

### <a name="record-a-web-performance-test"></a>Web パフォーマンス テストを記録する
1. .webtest プロジェクトを開きます。
2. **[記録の追加]** アイコンを選択して、ブラウザーで記録セッションを開始します。

    ![ブラウザーの [記録の追加] アイコンのスクリーン ショット][3]

    ![ブラウザーの [記録] ボタンのスクリーン ショット][4]
3. Service Fabric アプリケーションを参照します。 記録パネルに Web 要求が表示されます。

    ![記録パネルの Web 要求のスクリーン ショット][5]
4. ユーザーが実行すると予想される一連のアクションを実行します。 これらのアクションは、負荷を生成するパターンとして使用されます。
5. 完了したら、 **[停止]** をクリックして記録を停止します。

    ![[停止] ボタンのスクリーン ショット][6]

    Visual Studio の .webtest プロジェクトで一連の要求がキャプチャされます。 動的パラメーターが自動的に置き換えられます。 この時点で、テスト シナリオに含まれていない余分な依存関係要求の繰り返しを削除できます。
6. プロジェクトを保存し、 **[テストの実行]** を選択して Web パフォーマンス テストをローカルで実行し、すべてが正常に機能していることを確認します。

    ![[テストの実行] コマンドのスクリーン ショット][7]

### <a name="parameterize-the-web-performance-test"></a>Web パフォーマンス テストをパラメーター化する
Web パフォーマンス テストをパラメーター化するには、コード化された Web パフォーマンス テストに変換し、そのコードを編集します。 または、テストでデータが反復処理されるように、データの一覧に Web パフォーマンス テストをバインドすることもできます。 Web パフォーマンス テストをコード化されたテストに変換する方法の詳細については、「 [コード化された Web パフォーマンス テストの生成と実行](https://msdn.microsoft.com/library/ms182552.aspx) 」を参照してください。 Web パフォーマンス テストにデータをバインドする方法については、「 [Web パフォーマンス テストへのデータ ソースの追加](https://msdn.microsoft.com/library/ms243142.aspx) 」を参照してください。

この例では、GUID を生成して飛行機 ID と置き換え、フライトを複数の場所に送信するための要求を追加できるように、Web パフォーマンス テストをコード化されたテストに変換します。

### <a name="create-a-load-test-project"></a>ロード テスト プロジェクトを作成する
ロード テスト プロジェクトは、Web パフォーマンス テストと単体テストで記述されている 1 つ以上のシナリオと、指定された追加のロード テスト設定で構成されます。 次の手順は、ロード テスト プロジェクトを作成する方法を示しています。

1. Web パフォーマンスとロード テストのプロジェクトのショートカット メニューで、 **[追加]** > **[ロード テスト]**で無料で取得できます。 **ロード テスト** ウィザードで、**[次へ]** をクリックし、テストの設定を構成します。
2. **[ロード パターン]** セクションで、持続ユーザー ロードとステップ ロードのどちらかを選択します。ステップ ロードは、少数のユーザーから開始して、徐々にユーザーを増やします。

    ユーザー負荷の量について正確な見積もりがあり、システムの現在のパフォーマンスを確認したい場合は、 **[持続ロード]**を選択します。 さまざまな量の負荷がある状況でシステムのパフォーマンスが一貫しているかどうかを把握したい場合は、 **[ステップ ロード]**を選択します。
3. **[テスト ミックス]** セクションで、**[追加]** をクリックし、ロード テストに含めるテストを選択します。 **[配分]** 列で、合計テスト実行に対する各テストの割合を指定します。
4. **[実行設定]** セクションで、ロード テストの時間を指定します。

   > [!NOTE]
   > **[テスト イテレーション]** オプションは、Visual Studio を使用してローカルでロード テストを実行する場合にのみ使用できます。
   >
   >
5. **[実行設定]** の **[場所]** で、ロード テストの要求が生成される場所を指定します。 ウィザードで、Team Services アカウントにログインするよう要求される場合があります。 地理的な場所を選択してログインします。 完了したら、 **[完了]** をクリックします。
6. ロード テストを作成したら、で無料で取得できます。loadtest プロジェクトを開き、 **[実行設定]** > **[実行設定]1 [Active]**で無料で取得できます。 **[プロパティ]** ウィンドウの実行設定が開きます。
7. **[実行設定]** プロパティ ウィンドウの **[結果]** セクションで、**[タイミングの詳細ストレージ]** 設定の既定値が **[なし]** に設定されています。 ロード テスト結果の詳細情報を取得するために、この値を **[すべての個別詳細]** に変更します。 Visual Studio Team Services に接続し、ロード テストを実行する方法の詳細については、 [ロード テスト](https://www.visualstudio.com/load-testing.aspx) に関するページを参照してください。

### <a name="run-the-load-test-by-using-visual-studio-team-services"></a>Visual Studio Team Services を使用してロード テストを実行する
**[ロード テストの実行]** を選択して、テストの実行を開始します。

![[ロード テストの実行] コマンドのスクリーン ショット][8]

## <a name="view-and-analyze-the-load-test-results"></a>ロード テスト結果の表示と分析
ロード テストの進行に合わせて、パフォーマンス情報がグラフ化されます。 次のようなグラフが表示されます。

![ロード テスト結果のパフォーマンス グラフのスクリーン ショット][9]

1. ページの上部にある **[レポートのダウンロード]** リンクを選択します。 レポートをダウンロードしたら、 **[レポートの表示]** をクリックします。

    **[グラフ]** タブで、さまざまなパフォーマンス カウンターに対応したグラフを確認できます。 **[概要]** タブに、全体的なテスト結果が表示されます。 **[テーブル]** タブには、成功および失敗したロード テストの合計数が表示されます。
2. **[テスト]** の  > **[失敗]** 列と **[エラー]** の  > **[エラー数]** 列で数字のリンクを選択すると、エラーの詳細が表示されます。

    **[詳細]** タブには、失敗した要求の仮想ユーザーとテスト シナリオの情報が表示されます。 このデータは、ロード テストに複数のシナリオが含まれている場合に役立ちます。

ロード テストの結果を表示する方法の詳細については、 [ロード テスト アナライザーのグラフ ビューでのロード テスト結果の分析](https://www.visualstudio.com/load-testing.aspx) に関するページを参照してください。

## <a name="automate-your-load-test"></a>ロード テストの自動化
Visual Studio Team Services のロード テストで提供されている API を使用して、Team Services アカウントでロード テストを管理し、結果を分析できます。 詳細については、 [クラウドのロード テスト用 Rest API](http://blogs.msdn.com/b/visualstudioalm/archive/2014/11/03/cloud-load-testing-rest-apis-are-here.aspx) に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
* [ローカル コンピューターの開発のセットアップでのサービスの監視と診断](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[0]: ./media/service-fabric-vso-load-test/OverviewDiagram.png
[1]: ./media/service-fabric-vso-load-test/NewProjectDialog.png
[2]: ./media/service-fabric-vso-load-test/Project.png
[3]: ./media/service-fabric-vso-load-test/AddRecording.png
[4]: ./media/service-fabric-vso-load-test/AddRecording2.png
[5]: ./media/service-fabric-vso-load-test/ActionSequence.png
[6]: ./media/service-fabric-vso-load-test/StopRecording.png
[7]: ./media/service-fabric-vso-load-test/RunTest.png
[8]: ./media/service-fabric-vso-load-test/RunTest2.png
[9]: ./media/service-fabric-vso-load-test/Graph.png



<!--HONumber=Dec16_HO2-->


