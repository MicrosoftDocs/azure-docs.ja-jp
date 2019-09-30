---
title: Azure HDInsight Tools - Visual Studio Code 用の PySpark 対話型環境
description: Azure HDInsight Tools for Visual Studio Code を使用して、クエリとスクリプトを作成および送信する方法について説明します。
keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,インタラクティブ Hive,インタラクティブ クエリ
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: d220d81b8dc57541113f7ef1e477bb77d394e206
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70879293"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Visual Studio Code 用の PySpark 対話型環境を設定する

以下の手順では、VS Code に PySpark 対話型環境を設定する方法を示します。

**python/pip** コマンドを使用して、ホーム パスに仮想環境を構築します。 別のバージョンを使用する場合は、**python/pip** コマンドの既定のバージョンを手動で変更する必要があります。 詳細については、[update-alternatives](https://linux.die.net/man/8/update-alternatives) を参照してください。

1. [Python](https://www.python.org/downloads/) と [pip](https://pip.pypa.io/en/stable/installing/) をインストールします。
   
   + [https://www.python.org/downloads/](https://www.python.org/downloads/) から Python をインストールします。
   + pip を [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) からインストールします (Python インストールからインストールされてない場合)。
   + 次のコマンドを使用して、Python と pip が正常にインストールされていることを確認します。 (省略可能)
 
        ![Python と pip のバージョン](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Python は、macOS の既定のバージョンを使用する代わりに、手動でインストールすることをお勧めします。


2. 以下のコマンドを実行して、**virtualenv** をインストールします。
   
   ```
   pip install virtualenv
   ```

## <a name="other-packages"></a>その他のパッケージ

エラー メッセージが表示される場合は、以下のコマンドを実行して、必要なパッケージをインストールします。

   ![libkrb5 パッケージ](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```
sudo apt-get install libkrb5-dev
```

```
sudo apt-get install python-dev
```

VSCode を再起動して、**HDInsight: PySpark Interactive を実行しているスクリプト エディターに戻ります。**

## <a name="next-steps"></a>次の手順

### <a name="demo"></a>デモ
* HDInsight for VS Code:[ビデオ](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [Azure HDInsight Tool for Visual Studio Code を使用する](hdinsight-for-vscode.md)
* [Azure Toolkit for IntelliJ を使用して Apache Spark Scala アプリケーションを作成して送信する](spark/apache-spark-intellij-tool-plugin.md)
* [Azure Toolkit for IntelliJ を使用して SSH 経由で Apache Spark アプリケーションをリモートでデバッグする](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Azure Toolkit for IntelliJ を使用して VPN 経由で Apache Spark アプリケーションをリモートでデバッグする](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Azure Toolkit for Eclipse の HDInsight Tools を使用して Apache Spark アプリケーションを作成する](spark/apache-spark-eclipse-tool-plugin.md)
* [HDInsight 上の Apache Spark クラスターで Apache Zeppelin Notebook を使用する](spark/apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Apache Spark クラスター内の Jupyter Notebook で使用可能なカーネル](spark/apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Azure HDInsight の Microsoft Power BI で Apache Hive データを視覚化する](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Azure HDInsight で Apache Zeppelin を使用して Apache Hive クエリを実行する](./interactive-query/hdinsight-connect-hive-zeppelin.md)
