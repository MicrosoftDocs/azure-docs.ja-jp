---
title: Azure リソースからリソース ログを収集し、Azure Monitor で分析する
description: このチュートリアルでは、Azure リソースから Log Analytics ワークスペースにリソース ログが収集されるように診断設定を構成します。ワークスペースでは、ログ クエリを使用してログを分析できます。
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: 43a9c88bc64b688bfd1171e331232bb254b0eebe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102032876"
---
# <a name="tutorial-collect-and-analyze-resource-logs-from-an-azure-resource"></a>チュートリアル:Azure リソースからリソース ログを収集して分析する

リソース ログを収集すると、Azure リソースの詳細な操作に関する分析情報が提供され、正常性と可用性を監視するのに役立ちます。 Azure リソースではリソース ログが自動的に生成されますが、リソース ログを収集する場所を構成する必要があります。 このチュートリアルでは、Azure サブスクリプション内のリソースのリソース ログを収集するための診断設定を作成し、ログ クエリでログを分析する手順について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Monitor で Log Analytics ワークスペースを作成する
> * リソース ログを収集するための診断設定を作成する 
> * 簡単なログ クエリを作成してログを分析する


## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、監視する Azure リソースが必要です。 診断設定がサポートされている、Azure サブスクリプション内の任意のリソースを使用できます。 リソースで診断設定がサポートされているかどうかを調べるには、Azure portal でリソースのメニューの **[監視]** セクションに **[診断設定]** オプションがあることを確認します。


## <a name="log-in-to-azure"></a>Azure にログインする
Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にログインします。


## <a name="create-a-workspace"></a>ワークスペースの作成
Azure Monitor の Log Analytics ワークスペースでは、さまざまなソースからログ データが収集されてインデックスが設定され、強力なクエリ言語を使用して高度な分析を行うことができます。 Log Analytics ワークスペースにデータを送信するための診断設定を作成する前に、Log Analytics ワークスペースが存在している必要があります。 Azure サブスクリプションの既存のワークスペースを使用することも、次の手順でワークスペースを作成することもできます。 

> [!NOTE]
> Log Analytics ワークスペース内のデータは **Azure Monitor** のメニューで使用できますが、ワークスペースの作成と管理は **Log Analytics ワークスペース** のメニューで行います。

1. **[すべてのサービス]** で **[Log Analytics ワークスペース]** を選択します。
2. 画面の上部にある **[追加]** をクリックし、ワークスペースに関する次の詳細情報を指定します。
   - **Log Analytics ワークスペース**:新しいワークスペースの名前。 この名前は、すべての Azure Monitor サブスクリプションでグローバルに一意である必要があります。
   - **サブスクリプション**:ワークスペースを格納するサブスクリプションを選択します。 監視対象のリソースと同じサブスクリプションにする必要はありません。
   - **リソース グループ**:既存のリソース グループを選択するか、 **[新規作成]** をクリックして新しく作成します。 監視対象のリソースと同じリソース グループにする必要はありません。
   - **[場所]** :Azure リージョンを選択するか、新しいリージョンを作成します。 監視対象のリソースと同じ場所にする必要はありません。
   - **価格レベル**:価格レベルとして *[従量課金制]* を選択します。 この価格レベルは後で変更できます。 さまざまな価格レベルの詳細を確認するには、 **[Log Analytics の価格]** リンクをクリックします。

    ![新しいワークスペース](media/tutorial-resource-logs/new-workspace.png)

3. **[OK]** をクリックしてワークスペースを作成します。

## <a name="create-a-diagnostic-setting"></a>診断設定の作成
[診断設定](../essentials/diagnostic-settings.md)では、特定のリソースのリソース ログを送信する場所を定義します。 1 つの診断設定で複数の[送信先](../essentials/diagnostic-settings.md#destinations)を指定できますが、このチュートリアルでは Log Analytics ワークスペースのみを使用します。

1. リソースのメニューの **[監視]** セクションで、 **[診断設定]** を選択します。
2. "診断設定が定義されていません" というメッセージが表示されます。 **[診断設定の追加]** をクリックします。

    ![診断設定](media/tutorial-resource-logs/diagnostic-settings.png)

3. 各診断設定には、次の 3 つの基本部分があります。
 
   - **Name**:これには大きな影響はなく、わかりやすいものにする必要だけがあります。
   - **宛先**:ログを送信する 1 つ以上の送信先。 可能な 3 つの送信先の同じセットを、すべての Azure サービスで共有します。 各診断設定では、1 つまたは複数の送信先を定義できますが、特定の種類の送信先は 1 つだけです。 
   - **カテゴリ**: 各送信先に送信するログのカテゴリ。 カテゴリのセットは、Azure サービスごとに異なります。

4. **[Log Analytics への送信]** を選択し、作成したワークスペースを選択します。
5. 収集するカテゴリを選択します。 使用可能なカテゴリの定義については、各サービスのドキュメントを参照してください。

    ![診断設定](media/tutorial-resource-logs/diagnostic-setting.png)

6. **[保存]** をクリックして診断設定を保存します。

    
 
 ## <a name="use-a-log-query-to-retrieve-logs"></a>ログ クエリを使用してログを取得する
Log Analytics ワークスペースからデータを取得するには、Kusto クエリ言語 (KQL) で記述したログ クエリを使用します。 Azure Monitor の分析情報とソリューションには、特定のサービスのデータを取得するためのログ クエリが用意されていますが、Azure portal では Log Analytics を使用してログ クエリとその結果を直接処理することができます。 

1. リソースのメニューの **[監視]** セクションで、 **[ログ]** を選択します。
2. Log Analytics で、お使いのリソースにスコープが設定された空のクエリ ウィンドウが表示されます。 すべてのクエリには、そのリソースのレコードだけが含まれます。

    > [!NOTE]
    > Azure Monitor のメニューからログを開いた場合は、スコープが Log Analytics ワークスペースに設定されます。 この場合、すべてのクエリにはワークスペース内のすべてのレコードが含まれます。
   
    ![スクリーンショットに示すロジック アプリのログには、新しいクエリが表示され、ロジック アプリの名前が強調表示されています。](media/tutorial-resource-logs/logs.png)

4. 例で示されているサービスでは、**AzureDiagnostics** テーブルにリソース ログが書き込まれますが、他のサービスでは他のテーブルに書き込むことができます。 さまざまな Azure サービスで使用されるテーブルについては、「[Azure リソース ログでサポートされているサービス、スキーマ、カテゴリ](../essentials/resource-logs-schema.md)」を参照してください。

    > [!NOTE]
    > AzureDiagnostics テーブルには、複数のサービスからリソース ログが書き込まれます。 Azure Monitor のメニューから Log Analytics を開始した場合は、`ResourceProvider` 列に `where` ステートメントを追加して、特定のサービスを指定する必要があります。 リソースのメニューから Log Analytics を開始すると、スコープはこのリソースのレコードのみに設定されるので、この列は必要ありません。 クエリのサンプルについては、サービスのドキュメントを参照してください。


5. クエリを入力し、 **[実行]** をクリックして結果を調べます。 
6. ログ クエリの記述に関するチュートリアルについては、「[Azure Monitor でログ クエリの使用を開始する](../logs/get-started-queries.md)」を参照してください。

    ![Log query](media/tutorial-resource-logs/log-query-1.png)




## <a name="next-steps"></a>次のステップ
Log Analytics ワークスペースにリソース ログを収集する方法を学習したので、このデータを分析するためのログ クエリの作成に関するチュートリアルを済ませてくさい。

> [!div class="nextstepaction"]
> [Azure Monitor でログ クエリの使用を開始する](../logs/get-started-queries.md)