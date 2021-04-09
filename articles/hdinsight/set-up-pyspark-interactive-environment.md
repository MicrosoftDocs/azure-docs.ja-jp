---
title: Azure HDInsight Tools を利用した PySpark 対話型環境
description: Azure HDInsight Tools for Visual Studio Code を使用して、クエリとスクリプトを作成および送信する方法について説明します。
keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,インタラクティブ Hive,インタラクティブ クエリ
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-python
ms.date: 04/23/2020
ms.openlocfilehash: a6247116cdf579691e48883231f57712da36c4ad
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864569"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Visual Studio Code 用の PySpark 対話型環境を設定する

以下の手順では、VS Code に PySpark 対話型環境を設定する方法を示します。 この手順は、Windows 以外のユーザーのみを対象とします。

**python/pip** コマンドを使用して、ホーム パスに仮想環境を構築します。 別のバージョンを使用する場合は、**python/pip** コマンドの既定のバージョンを手動で変更する必要があります。 詳細については、[update-alternatives](https://linux.die.net/man/8/update-alternatives) を参照してください。

1. [Python](https://www.python.org/downloads/) と [pip](https://pip.pypa.io/en/stable/installing/) をインストールします。

   * [https://www.python.org/downloads/](https://www.python.org/downloads/) から Python をインストールします。 
   * pip を [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) からインストールします (Python インストールからインストールされてない場合)。
   * 必要に応じてコマンド `python --version` および `pip --version` をそれぞれ使用して、Python と pip が正常にインストールされていることを確認します。 

     > [!NOTE]
     > Python は、macOS の既定のバージョンを使用する代わりに、手動でインストールすることをお勧めします。

2. 以下のコマンドを実行して、**virtualenv** をインストールします。

   ```bash
   pip install virtualenv
   ```

## <a name="other-packages"></a>その他のパッケージ

Linux では、下にエラー メッセージが表示された場合、次の 2 つのコマンドを実行し、必要なパッケージをインストールします。

   :::image type="content" source="./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png" alt-text="Python 用 libkrb5 パッケージをインストールする" border="true":::

```bash
sudo apt-get install libkrb5-dev
```

```bash
sudo apt-get install python-dev
```

VS Code を再起動し、VS Code エディターに戻り、**Spark: PySPark Interactive** コマンドを実行します。

## <a name="next-steps"></a>次のステップ

### <a name="demo"></a>デモ

* HDInsight for VS Code:[ビデオ](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>ツールと拡張機能

* [Azure HDInsight Tool for Visual Studio Code を使用する](hdinsight-for-vscode.md)
* [Azure Toolkit for IntelliJ を使用して Apache Spark Scala アプリケーションを作成して送信する](spark/apache-spark-intellij-tool-plugin.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](spark/apache-spark-jupyter-notebook-install-locally.md)
