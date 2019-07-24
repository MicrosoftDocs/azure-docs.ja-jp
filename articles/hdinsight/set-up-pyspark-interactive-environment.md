---
title: Azure HDInsight Tools - Visual Studio Code 用の PySpark 対話型環境を設定する
description: Azure HDInsight Tools for Visual Studio Code を使用して、クエリとスクリプトを作成および送信する方法について説明します。
keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,インタラクティブ Hive,インタラクティブ クエリ
services: hdinsight
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 1/17/2019
ms.openlocfilehash: 8c0b40f0e6204d6cff59d3de3f20604d6913b9cb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58111635"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Visual Studio Code 用の PySpark 対話型環境を設定する

以下の手順では、VS Code に PySpark 対話型環境を設定する方法を示します。

**python/pip** コマンドを使用して、ホーム パスに仮想環境を構築します。 別のバージョンを使用する場合は、**python/pip** コマンドの既定のバージョンを手動で変更する必要があります。 詳細については、[update-alternatives](https://linux.die.net/man/8/update-alternatives) を参照してください。

1. [Python](https://www.python.org/downloads/) と [pip](https://pip.pypa.io/en/stable/installing/) をインストールします。
   
   + [https://www.python.org/downloads/](https://www.python.org/downloads/) から Python をインストールします。
   + [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) から pip をインストールします。 (Python インストールからインストールされていない場合)
   + 次のコマンドを使用して、Python と pip が正常にインストールされていることを確認します。 (省略可能)
 
        ![Python と pip のバージョン](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Python は、MacOS の既定のバージョンを使用する代わりに、手動でインストールすることをお勧めします。


2. 以下のコマンドを実行して、**virtualenv** をインストールします。
   
   ```
   pip install virtualenv
   ```

3. Linux の場合のみ、エラー メッセージが発生した場合は、次のコマンドを実行して、必要なパッケージをインストールします。
   
    ![Python と pip のバージョン](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)
       
   ```
   sudo apt-get install libkrb5-dev 
   ```

   ```
   sudo apt-get install python-dev
   ```

4. VSCode を再起動して、**HDInsight: PySpark Interactive** を実行しているスクリプト エディターに戻ります。

## <a name="next-steps"></a>次の手順

### <a name="demo"></a>デモ
* HDInsight for VS Code:[ビデオ](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [Azure HDInsight Tool for Visual Studio Code を使用する](hdinsight-for-vscode.md)
* [Azure Toolkit for IntelliJ を使用して Apache Spark Scala アプリケーションを作成して送信する](spark/apache-spark-intellij-tool-plugin.md)
* [Azure Toolkit for IntelliJ を使用して SSH 経由で Apache Spark アプリケーションをリモートでデバッグする](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Azure Toolkit for IntelliJ を使用して VPN 経由で Apache Spark アプリケーションをリモートでデバッグする](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Azure Toolkit for Eclipse 上の HDInsight Tools を使用して Apache Spark アプリケーションを作成する](spark/apache-spark-eclipse-tool-plugin.md)
* [Hortonworks Sandbox と IntelliJ 用 HDInsight ツールを使用する](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [HDInsight 上の Apache Spark クラスターで Apache Zeppelin Notebook を使用する](spark/apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Apache Spark クラスター内の Jupyter Notebook で使用可能なカーネル](spark/apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Azure HDInsight の Microsoft Power BI で Apache Hive データを視覚化する](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Azure HDInsight で Apache Zeppelin を使用して Apache Hive クエリを実行する](hdinsight-connect-hive-zeppelin.md)
