---
title: Apache Hadoop サンドボックスの使用方法、エミュレーター - Azure HDInsight
description: 'Apache Hadoop エコシステムの使用について学ぶには、まず Hortonworks の Hadoop サンドボックスを Azure 仮想マシンに設定します。 '
keywords: hadoop emulator,hadoop sandbox
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: how-to
ms.date: 05/29/2019
ms.openlocfilehash: 60f4e4998e1077ca373f47a0cdf35c9d7a064605
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866677"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Apache Hadoop サンドボックス (仮想マシンのエミュレーター) の概要

Hortonworks の Hadoop サンドボックスを仮想マシンにインストールする方法と、Apache Hadoop エコシステムについて学ぶことができます。 Hadoop、Hadoop 分散ファイル システム (HDFS)、ジョブの送信などについて理解できるように、サンドボックスにはローカル開発環境が用意されています。 Hadoop について理解できたら、HDInsight クラスターを作成して、Azure で Hadoop を使い始めることができます。 使用方法の詳細については、 [HDInsight での Hadoop の使用](apache-hadoop-linux-tutorial-get-started.md)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

* [Oracle VirtualBox](https://www.virtualbox.org/). [こちら](https://www.virtualbox.org/wiki/Downloads)からダウンロードしてインストールします。

## <a name="download-and-install-the-virtual-machine"></a>仮想マシンのダウンロードとインストール

1. [Cloudera のダウンロード](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html)を参照します。

1. **[Choose Installation Type]** の下の **[VIRTUALBOX]** をクリックして、最新の Hortonworks サンドボックスを VM にダウンロードします。 サインインするか、または製品への関心についてのフォームを完了します。

1. **[HDP SANDBOX (LATEST)]** ボタンをクリックしてダウンロードを開始します。

サンドボックスの設定に関する手順については、[サンドボックスの展開およびインストール ガイド](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/)を参照してください。

古い HDP バージョンのサンドボックスをダウンロードするには、**[Older Versions]** の下のリンクを参照してください。

## <a name="start-the-virtual-machine"></a>仮想マシンの開始

1. Oracle VM VirtualBox を開きます。
1. **[ファイル]** メニューの **[Import Appliance (アプライアンスのインストール)]** をクリックしHortonworks Sandbox イメージを指定します。
1. Hortonworks Sandbox を選択し、**[Start (開始)]**、**[Normal Start (通常の方法で開始)]** の順にクリックします。 仮想マシンのブート プロセスが終了すると、ログインに関する手順が表示されます。

    :::image type="content" source="./media/apache-hadoop-emulator-get-started/virtualbox-normal-start.png" alt-text="virtualbox マネージャーの通常の方法での開始" border="true":::

1. Web ブラウザーを開き、表示された URL に移動します (通常は `http://127.0.0.1:8888`)。

## <a name="set-sandbox-passwords"></a>サンドボックスのパスワードの設定

1. Hortonworks Sandbox のページの **[get started (開始)]** 手順で、**[View Advanced Options (詳細設定の表示)]** をクリックします。 SSH を使用してサンドボックスにログインするには、このページの情報を使用します。 提供された名前とパスワードを使用してください。

   > [!NOTE]
   > SSH クライアントがインストールされていない場合は、 **http://localhost:4200/** の仮想マシンから提供される Web ベースの SSH を使用することができます。

    SSH を使用して初めて接続する際に、ルート アカウントのパスワードを変更するように求められます。 SSH を使用してログインするときに使用する新しいパスワードを入力します。

2. ログインが完了したら、次のコマンドを入力します。

    ```bash
    ambari-admin-password-reset
    ```

    メッセージが表示されたら、Ambari の管理者アカウントのパスワードを入力します。 これは、Ambari Web UI にアクセスするときに使用します。

## <a name="use-hive-commands"></a>Hive コマンドの使用

1. サンドボックスへの SSH 接続から、次のコマンドを使用して Hive シェルを起動します。

    ```bash
    hive
    ```

2. シェルが起動したら、次のコマンドを使用して、サンドボックスに用意されているテーブルを表示します。

    ```hiveql
    show tables;
    ```

3. 次のコマンドを使用して、 `sample_07` テーブルから 10 行を取得します。

    ```hiveql
    select * from sample_07 limit 10;
    ```

## <a name="next-steps"></a>次のステップ

* [Hortonworks Sandbox で Visual Studio を使用する方法](./apache-hadoop-visual-studio-tools-get-started.md)

* [Hortonworks Sandbox の使い方のヒント](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)

* [Hadoop チュートリアル: HDP の概要](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)