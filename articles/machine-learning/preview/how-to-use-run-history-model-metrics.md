---
title: "Azure Machine Learning Workbench で実行履歴とモデル メトリックを使用する方法 | Microsoft Docs"
description: "Azure Machine Learning Workbench の実行履歴機能とモデル メトリック機能を使用するためのガイド"
services: machine-learning
author: jopela
ms.author: jopela
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 1a4f2d69907d220a8bbec5087ba431dc243c46d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-run-history-and-model-metrics-in-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench で実行履歴とモデル メトリックを使用する方法

Azure Machine Learning Workbench は、**実行履歴**機能と**モデル メトリック**機能を通じてデータ サイエンス実験をサポートしています。
**実行履歴**を使用すると、Machine Learning 実験の出力を追跡して、結果をフィルター処理したり比較したりできます。
**モデル メトリック**はスクリプトのどの時点でもログ記録することができ、データ サイエンス実験で最も重要なあらゆる値を追跡できます。
この記事では、これらの機能を効果的に使用してデータ サイエンス実験の速度と質を向上させる方法について説明します。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下のことを行う必要があります。
* [Azure Machine Learning を作成およびインストールする](quickstart-installation.md)
- [プロジェクトを作成する](quickstart-installation.md)


## <a name="azure-ml-logging-api-overview"></a>Azure ML ログ API の概要
Azure ML ログ API は、Python (Azure ML Workbench と一緒にインストールされます) の **azureml.logging** モジュールを通じて利用できます。このモジュールをインポートしたら、**get_azureml_logger** メソッドを使用して**ロガー** オブジェクトをインスタンス化できます。
次に、そのロガーの **log** メソッドを使用して、Python スクリプトによって生成されたキー/値のペアを格納できます。
現時点では、次に示すように scalar、dictionary、list、dataframe 型のモデル メトリックのログがサポートされています。

```Python
# create a logger instance in already set up environment 
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# log scalar (any integer or floating point type is fine)
logger.log("simple value", 7)

# log dictionary
logger.log("range", {"min":5, "max":7})

# log list
logger.log("all values", [5, 6, 7])

# log dataframe
import pandas
df = pandas.DataFrame.from_records([(1,2),(3,4)],columns=["a","b"])
logger.log("dataframe", df)
```
Azure ML Workbench プロジェクトでロガーを使用するのは簡単です。この記事で、その方法を説明します。

## <a name="create-a-project-in-azure-ml-workbench"></a>Azure ML Workbench でプロジェクトを作成する
まだプロジェクトがない場合は、[クイックスタートの作成とインストール](quickstart-installation.md)に関する記事から作成できます。下に示すように、**プロジェクト ダッシュボード**から **iris_sklearn.py** スクリプトを開きます。

![[ファイル] タブからスクリプトへのアクセス](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-01b.png)

このスクリプトを、Azure ML でのモデル メトリック ログの実装のガイドとして使用できます。

## <a name="parameterize-and-log-model-metrics-from-script"></a>スクリプトからモデル メトリックをパラメーター化してログ記録する
**iris_sklearn.py** スクリプトでは、Python のロガーをインポートして作成するための想定されるパターンを次のコード行に短縮できます。

```Python
from azureml.logging import get_azureml_logger
run_logger = get_azureml_logger()
```

作成したら、任意の名前/値のペアに対して **log** メソッドを起動できます。

開発が完了したら、コマンド ラインで値を渡せるように、スクリプトをパラメーター化すると便利です。
次の例は、標準の Python ライブラリを使用してコマンド ライン パラメーター (存在する場合) を受け入れる方法を示しています。
このスクリプトは、オーバーフィットを発生させずに*精度*を上げる作業に分類モデルを適合させるために使用される [Regularization Rate]\(正則化率)\ (*reg*) の 1 つのパラメーターを受け取ります。
これらの変数は*[Regularization Rate]\(正則化率)\*および*[Accuracy] \(精度)\* としてログ記録されるため、最適な結果が得られるモデルを簡単に識別できます。

```Python
# change regularization rate and you will likely get a different accuracy.
reg = 0.01
# load regularization rate from argument if present
if len(sys.argv) > 1:
    reg = float(sys.argv[1])

print("Regularization rate is {}".format(reg))

# log the regularization rate
run_logger.log("Regularization Rate", reg)

# train a logistic regression model on the training set
clf1 = LogisticRegression(C=1/reg).fit(X_train, Y_train)
print (clf1)

# evaluate the test set
accuracy = clf1.score(X_test, Y_test)
print ("Accuracy is {}".format(accuracy))

# log accuracy
run_logger.log("Accuracy", accuracy)
```

スクリプトでこれらの手順を実行することで、**実行履歴**を最大限に活用できます。

## <a name="launch-runs-from-project-dashboard"></a>プロジェクト ダッシュボードから実行を起動する
**プロジェクト ダッシュボード**に戻り、**iris_sklearn.py** スクリプトを選択した後で **[引数]** 編集ボックスに**[Regularization Rate]\(正則化率)\** のパラメーターを入力して、**追跡対象の実行**を起動します。

![パラメーターの入力と実行の起動](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-05.png)

追跡対象の実行を起動しても Azure ML Workbench はブロックされないので、複数の実行を並列で起動できます。
それぞれの追跡対象の実行の状態が、図に示すように**[ジョブ] パネル**に表示されます。

![[ジョブ] パネルでの実行の追跡](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-06.png)

これにより、各ジョブを直列で実行しなくてもリソースを最適に使用できます。

## <a name="view-results-in-run-history"></a>実行履歴で結果を表示する
追跡対象の実行の進行状況と結果は、Azure ML Workbench の **[実行履歴]** で分析に使用できます。
**[実行履歴]** には 3 つの個別のビューがあります。
- ダッシュボード
- 詳細
- 比較

**[ダッシュボード]** ビューには、指定されたスクリプトのすべての実行全体のデータが、グラフィック形式と表形式の両方で表示されます。
**[詳細]** ビューには、指定されたスクリプトの特定の実行で生成されたすべてのデータが表示され、ログ記録されたメトリックと出力ファイル (レンダリングされたプロットなど) も含まれます。**[比較]** ビューでは、2 つまたは 3 つの実行を並べて表示できます。このビューにも、ログ記録されたメトリックと出力ファイルが含まれます。

[実行履歴] ビューの使用方法を示すために、**iris_sklearn.py** の 8 つの追跡された実行の**[Regularization Rate]\(正則化率)\** パラメーターと**[Accuracy]\(精度)\** の結果がログ記録されています。

### <a name="run-history-dashboard"></a>実行履歴ダッシュボード
8 つの実行すべての結果が、**実行履歴ダッシュボード**に表示されます。
**iris_sklearn.py** では *[Regularization Rate]\(正則化率)\*と*[Accuracy]\(精度)\*がログ記録されるので、既定では**実行履歴ダッシュボード**にこれらの値のグラフが表示されます。

![実行履歴ダッシュボード](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-07.png)

ログ記録された値がグリッドで表示されるように、**実行履歴ダッシュボード**をカスタマイズすることもできます。  **[カスタマイズ]** アイコンをクリックすると、**[List View Customization]\(リスト ビューのカスタマイズ)\** ダイアログが表示されます。

![実行履歴ダッシュボードのグリッドのカスタマイズ](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-08.png)

追跡対象の実行でログ記録された任意の値を表示に使用できます。**[Regularization Rate]\(正則化率)\**と**[Accuracy]\(精度)\** をクリックすると、これらの値がグリッドに追加されます。

![カスタマイズしたグリッドに表示されたログ記録された値](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-09.png)

グラフ内の点をマウスでポイントすると、興味深い実行を簡単に見つけることができます。  この場合は、実行 7 が短期間で高い精度を実現しています。

![興味深い実行の発見](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-10.png)

いずれかのグラフで実行 7 に関連する点をクリックするか、グリッドで実行 7 へのリンクをクリックすると、**[Run History Details]\(実行履歴の詳細)\** が表示されます。

### <a name="run-history-details"></a>実行履歴の詳細
このビューで、実行 7 の完全な結果と、実行 7 によって生成されたアーティファクトがすべて表示されます。

![実行履歴の詳細](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-11.png)

**[Run History Details]\(実行履歴の詳細)\** ビューでは、**./outputs** フォルダーに書き込まれたファイルを**ダウンロード**することもできます (これらのファイルは Azure ML Workbench の実行履歴用のクラウド ストレージにバックアップされますが、それについては別の記事で説明します)。

さらに、**[Run History Details]\(実行履歴の詳細)\** では、プロジェクトをこの実行時点での状態に復元できます。
**[復元]** ボタンをクリックすると、次のような確認ダイアログが表示されます。

![実行の復元の確認](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-13.png)

確認するとファイルが上書きまたは削除される可能性があるため、この機能は慎重に使用してください。

### <a name="run-history-comparison"></a>実行履歴の比較
**実行履歴ダッシュボード**で 2 つまたは 3 つの実行を選択して **[比較]** をクリックすると、**[Run History Comparison]\(実行履歴の比較)\** ビューが表示されます。
または、**[比較]** をクリックしてから **[Run History Details]\(実行履歴の詳細)\** で実行を 1 つ選択しても、**[Run History Comparison]\(実行履歴の比較)\** ビューが表示されます。
いずれの場合も、**[Run History Comparison]\(実行履歴の比較)\** ビューで、2 つまたは 3 つの実行のログ記録された結果とアーティファクトを並べて表示できます。

![実行履歴の比較](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-12.png)

このビューはプロットの比較に特に役立ちますが、通常は実行のすべてのプロパティをここで比較できます。

### <a name="command-line-interface"></a>コマンド ライン インターフェイス
Azure Machine Learning Workbench では、**コマンド ライン インターフェイス**から実行履歴にアクセスすることもできます。
**コマンド ライン インターフェイス**にアクセスするには、下に示すように **[コマンド プロンプトを開く]** メニューをクリックします。

![コマンド プロンプトを開く](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-14.png)

実行履歴に使用できるコマンドには `az ml history` を使用してアクセスできます。また、`-h` フラグを追加するとオンライン ヘルプも利用できます。
```
$ az ml history -h

Group
    az ml history: View run history of machine learning experiments.

Commands:
    compare : Compare two runs.
    download: Download all the artifacts from a run into the destination path.
    info    : Details about one run.
    last    : Get detail about most recent run.
    list    : List runs.
    promote : Promote Artifacts.
```
これらのコマンドは、**実行履歴のビュー**と同じ機能を提供し、同じデータを返します。
たとえば、最後の実行の結果を JSON オブジェクトとして表示できます。
```
$ az ml history last
{
  "Accuracy": 0.6792452830188679,
  "Regularization Rate": 0.078125,
  "attachments": "control_log, control_log.txt, driver_log, driver_log.txt, pid.txt, dataprep/backgroundProcess.log, dataprep/backgroundProcess_Engine.log, dataprep/backgroundProcess_EngineHost.log, dataprep/backgroundProcess_ProjectProvider.log, dataprep/backgroundProcess_Sampling.log, dataprep/backgroundProcess_Telemetry.log, outputs/cm.png, outputs/model.pkl, outputs/sdk_debug.txt, outputs/roc.png",
  "created_utc": "2017-09-08T00:58:01.611105+00:00",
  "description": null,
  "duration": "0:00:04.892389",
  "end_time_utc": "2017-09-08T00:58:07.951403+00:00",
  "experiment_id": "ce92d0a9-3e2c-4d51-85de-93ef22249ce1",
  "heartbeat_enabled": true,
  "hidden": false,
  "name": null,
  "parent_run_id": null,
  "properties": {
    "CommitId": "e77a5f0df2af1a482bbe39b70bfbb16b62228cb3"
  },
  "run_id": "IrisDemo_1504832281116",
  "run_number": 8,
  "script_name": "iris_sklearn.py",
  "start_time_utc": "2017-09-08T00:58:03.059014+00:00",
  "status": "Completed",
  "target": "local",
  "user_id": "e9fafe06-b0e4-4154-8374-aae34f9977b2"
}
```
すべての実行の一覧を表形式で表示することもできます。
```
$ az ml history list -o table
  Accuracy    Regularization Rate  Duration        Run_id                  Script_name      Start_time_utc                    Status
----------  ---------------------  --------------  ----------------------  ---------------  --------------------------------  ---------
  0.679245               0.078125  0:00:04.892389  IrisDemo_1504832281116  iris_sklearn.py  2017-09-08T00:58:03.059014+00:00  Completed
  0.679245               0.15625   0:00:04.734956  IrisDemo_1504832255198  iris_sklearn.py  2017-09-08T00:57:38.507196+00:00  Completed
  0.660377               0.3125    0:00:04.913762  IrisDemo_1504832234904  iris_sklearn.py  2017-09-08T00:57:16.849881+00:00  Completed
  0.660377               0.625     0:00:06.107764  IrisDemo_1504832210767  iris_sklearn.py  2017-09-08T00:56:54.602813+00:00  Completed
  0.641509               1.25      0:00:04.742727  IrisDemo_1504832171373  iris_sklearn.py  2017-09-08T00:56:13.879280+00:00  Completed
  0.660377               2.5       0:00:04.915401  IrisDemo_1504832148526  iris_sklearn.py  2017-09-08T00:55:52.937083+00:00  Completed
  0.641509               5         0:00:04.730627  IrisDemo_1504832127172  iris_sklearn.py  2017-09-08T00:55:29.612382+00:00  Completed
  0.641509              10         0:00:06.059082  IrisDemo_1504832109906  iris_sklearn.py  2017-09-08T00:55:14.739806+00:00  Completed

```
**コマンド ライン インターフェイス**は、Azure Machine Learning Workbench の機能にアクセスするための代替経路となります。

## <a name="next-steps"></a>次のステップ
これらの機能は、データ サイエンス実験の処理を支援するために提供されています。
マイクロソフトでは、これらの機能が皆様のお役に立つことを願っております。ご意見がございましたら、ぜひお知らせください。
これは最初の実装であり、数多くの機能強化を現在計画中です。
今後も引き続き Azure Machine Learning Workbench の機能を強化していきますので、どうぞご期待ください。 
