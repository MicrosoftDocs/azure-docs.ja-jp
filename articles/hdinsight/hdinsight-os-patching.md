---
title: "Linux ベースの HDInsight クラスターの OS 修正プログラム適用スケジュールを構成する - Azure |Microsoft Docs"
description: "Linux ベースの HDInsight クラスターの OS 修正プログラム適用スケジュールを構成する方法について説明します。"
services: hdinsight
documentationcenter: 
author: bprakash
manager: asadk
editor: bprakash
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: bhanupr
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: f39dfdff2239bb0b55eca50a1e7c706a5408b83a
ms.lasthandoff: 03/22/2017


---

# <a name="os-patching-for-hdinsight"></a>HDInsight 用の OS の修正プログラム 
管理された Hadoop サービスとして、HDInsight では、HDInsight クラスターで使用される、基盤となる VM の OS の修正プログラムを適用処理します。 2016 年 8 月 1 日の時点で、Linux ベースの HDInsight クラスター (バージョン 3.4 以降) に対するゲスト OS 更新プログラムの適用ポリシーが変更されました。 新しいポリシーの目的は、修正プログラム適用のための再起動の回数を大幅に削減することです。 新しいポリシーでは、Linux クラスターの仮想マシン (VM) への修正プログラム適用が継続されます。修正プログラム適用は、指定されたクラスターのノード間で交互に、毎週月曜日または木曜日の午前 12 時 (UTC) に開始されます。 ただし、どの VM も、ゲスト OS の修正プログラム適用のための再起動は 30 日ごとに最大で 1 回のみです。 また、新しく作成したクラスターの最初の再起動は、クラスターの作成日から 30 日以内には行われません。 修正プログラムは、VM が再起動されたら有効になります。

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Linux ベースの HDInsight クラスターの OS 修正プログラム適用スケジュールを構成する方法
HDInsight クラスターの仮想マシンは、重要なセキュリティ更新プログラムをインストールできるように、ときどき再起動する必要があります。 2016 年 8 月 1 日の時点で、新しい Linux ベースの HDInsight クラスター (バージョン 3.4 以降) は、次のスケジュールを使用して再起動します。

1. 更新プログラムの適用のためにクラスター内の仮想マシンを再起動できるのは、多くても 30 日間で 1 回のみです。
2. 再起動は、午前 12 時 (UTC) から実行されます。
3. 再起動プロセスはクラスター内の仮想マシン間で時間をずらして実行されるため、再起動プロセス中でもクラスターを使用できます。
4. 新しく作成したクラスターの最初の再起動は、クラスターの作成日から 30 日以内は行われません。

この記事で説明するスクリプト アクションを使用して、次のように OS の修正プログラム適用スケジュールを変更することができます。
1. 自動再起動を有効または無効にする
2. 再起動の頻度 (再起動間の日数) を設定する
3. 再起動を行う曜日を設定する

> [!NOTE]
> このスクリプト アクションは、2016 年 8 月 1 日以降に作成された Linux ベースの HDInsight クラスターでのみ機能します。 修正プログラムは、VM が再起動された場合のみ有効になります。 
>

## <a name="how-to-use-the-script"></a>スクリプトの使用方法 

このスクリプトを使用するには、次の情報が必要です。
1. スクリプトの場所: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv01/os-patching-reboot-config.sh。
     HDInsight では、この URI を使用してクラスター内のすべての仮想マシンでスクリプトを検索して実行します。
  
2. スクリプトが適用されるクラスター ノードの種類: headnode、workernode、zookeeper。 このスクリプトは、クラスター内のすべてのノードの種類に適用する必要があります。 スクリプトをノードの種類に適用しない場合、そのノードの種類の仮想マシンが引き続き以前の更新プログラム適用スケジュールを使用します。


3.  パラメーター: このスクリプトは、次の 3 つの数値パラメーターを受け取ります。

    | パラメーターが含まれる必要があります。 | 定義 |
    | --- | --- |
    | 自動再起動を有効または無効にする |0 または 1。 値 0 は自動再起動を無効にし、値 1 は有効にします。 |
    | 頻度 |7 ～ 90 (7 と 90 を含む)。 再起動を必要とする更新プログラムを適用するために、仮想マシンを再起動する前に待機する日数を指定します。 |
    | 曜日 |1 ～ 7 (1 と 7 を含む)。 値 1 を指定すると月曜日に、7 を指定すると日曜日に再起動が実行されます。たとえば、1 60 2 というパラメーターを指定すると、自動再起動は (多くても) 60 日ごとに、火曜日に実行されます。 |
    | 永続化 |既存のクラスターにスクリプト アクションを適用する場合、スクリプトを永続化としてマークできます。 永続化されたスクリプトは、スケーリング操作でクラスターに新しいワーカー ノードを追加したときに適用されます。 |

> [!NOTE]
> 既存のクラスターに適用する場合、このスクリプトは永続化としてマークする必要があります。 そうしない場合、スケーリング操作で作成された新しいノードでは、既定の修正プログラム適用スケジュールが使用されます。
クラスター作成プロセスの一部としてスクリプトを適用した場合、スクリプトは自動的に永続化されます。
>

## <a name="next-steps"></a>次のステップ

スクリプト アクションの使用法に関する具体的な手順については、「[スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」の以下のセクションを参照してください。

* [クラスターの作成時にスクリプト アクションを使用する](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [実行中のクラスターにスクリプト アクションを適用する](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)

