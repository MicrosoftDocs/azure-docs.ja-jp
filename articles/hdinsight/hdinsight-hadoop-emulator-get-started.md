---
title: "Hadoop サンドボックス (エミュレーター) の使用方法 - Azure HDInsight | Microsoft Docs"
description: "Hadoop エコシステムの使用について学ぶには、まず Hortonworks の Hadoop サンドボックスを Azure 仮想マシンに設定します。 "
keywords: hadoop emulator,hadoop sandbox
editor: cgronlun
manager: jhubbard
services: hdinsight
author: nitinme
documentationcenter: 
tags: azure-portal
ms.assetid: 6ad5bb58-8215-4e3d-a07f-07fcd8839cc6
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: b701879464205860edd1c097651b532f87bae388
ms.contentlocale: ja-jp
ms.lasthandoff: 07/01/2017

---
# <a name="get-started-with-a-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Hadoop サンドボックス (仮想マシンのエミュレーター) の概要

Hortonworks の Hadoop サンドボックスを仮想マシンにインストールする方法と、Hadoop エコシステムについて学ぶことができます。 Hadoop、Hadoop 分散ファイル システム (HDFS)、ジョブの送信などについて理解できるように、サンドボックスにはローカル開発環境が用意されています。 Hadoop について理解できたら、HDInsight クラスターを作成して、Azure で Hadoop を使い始めることができます。 使用方法の詳細については、 [HDInsight での Hadoop の使用](hdinsight-hadoop-linux-tutorial-get-started.md)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件
* [Oracle VirtualBox](https://www.virtualbox.org/). [こちら](https://www.virtualbox.org/wiki/Downloads)からダウンロードしてインストールします。



## <a name="download-and-install-the-virtual-machine"></a>仮想マシンのダウンロードとインストール
1. [Hortonworks のダウンロード ページ](http://hortonworks.com/downloads/#sandbox)に移動します。

2. **[VIRTUALBOX 用ダウンロード]** をクリックして、最新の Hortonworks Sandbox を VM にダウンロードします。 ダウンロードが始まる前に、Hortonworks に登録するように求められます。 ダウンロードは、ネットワーク速度に応じて 1 ～ 2 時間かかります。
   
    ![Link image for download Hortonworks Sandbox for VirtualBox](./media/hdinsight-hadoop-emulator-get-started/download-sandbox.png)
3. 同じ Web ページで、**[Import on Virtual Box (仮想ボックスにインポート)]** リンクをクリックして、仮想マシン用のインストール手順が記載された PDF をダウンロードします。

古い HDP バージョンのサンドボックスをダウンロードするには、アーカイブを展開します。

![Hortonworks Sandbox のアーカイブ](./media/hdinsight-hadoop-emulator-get-started/hortonworks-sandbox-archive.png)


## <a name="start-the-virtual-machine"></a>仮想マシンの開始

1. Oracle VM VirtualBox を開きます。
2. **[ファイル]** メニューの**[Import Appliance (アプライアンスのインストール)]** をクリックしHortonworks Sandbox イメージを指定します。
1. Hortonworks Sandbox を選択し、**[Start (開始)]**、**[Normal Start (通常の方法で開始)]** の順にクリックします。 仮想マシンのブート プロセスが終了すると、ログインに関する手順が表示されます。
   
    ![[Normal Start (通常の方法で開始)]](./media/hdinsight-hadoop-emulator-get-started/normal-start.png)
2. Web ブラウザーを開き、表示された URL に移動します (通常は http://127.0.0.1:8888 )。

## <a name="set-sandbox-passwords"></a>サンドボックスのパスワードの設定

1. Hortonworks Sandbox のページの **[get started (開始)]** 手順で、**[View Advanced Options (詳細設定の表示)]** をクリックします。 SSH を使用してサンドボックスにログインするには、このページの情報を使用します。 提供された名前とパスワードを使用してください。
   
   > [!NOTE]
   > SSH クライアントがインストールされていない場合は、**http://localhost:4200/** の仮想マシンから提供される Web ベースの SSH を使用することができます。
   > 
   
    SSH を使用して初めて接続する際に、ルート アカウントのパスワードを変更するように求められます。 SSH を使用してログインするときに使用する新しいパスワードを入力します。

2. ログインが完了したら、次のコマンドを入力します。
   
        ambari-admin-password-reset
   
    メッセージが表示されたら、Ambari の管理者アカウントのパスワードを入力します。 これは、Ambari Web UI にアクセスするときに使用します。

## <a name="use-hive-commands"></a>Hive コマンドの使用

1. サンドボックスへの SSH 接続から、次のコマンドを使用して Hive シェルを起動します。
   
        hive
2. シェルが起動したら、次のコマンドを使用して、サンドボックスに用意されているテーブルを表示します。
   
        show tables;
3. 次のコマンドを使用して、 `sample_07` テーブルから 10 行を取得します。
   
        select * from sample_07 limit 10;

## <a name="next-steps"></a>次のステップ
* [Hortonworks Sandbox で Visual Studio を使用する方法](hdinsight-hadoop-emulator-visual-studio.md)
* [Hortonworks Sandbox の使い方のヒント](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop チュートリアル: HDP の概要](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)


