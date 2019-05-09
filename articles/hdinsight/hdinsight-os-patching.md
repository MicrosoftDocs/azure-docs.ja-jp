---
title: Linux ベースの HDInsight クラスターの OS 修正プログラム適用スケジュールを構成する - Azure
description: Linux ベースの HDInsight クラスターの OS 修正プログラム適用スケジュールを構成する方法について説明します。
services: hdinsight
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: ef57608d092c05b30be63a54bb41ba87558eabc3
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55694620"
---
# <a name="os-patching-for-hdinsight"></a>HDInsight 用の OS の修正プログラム 

> [!IMPORTANT]
> Ubuntu イメージは、公開から 3 か月以内に、新しい HDInsight クラスターの作成のために入手できるようになります。 2019 年 1 月時点で、実行中のクラスターに修正プログラムは自動適用**されません**。 お客様は、スクリプトによるアクションまたはその他のメカニズムを使用して、実行中のクラスターに修正プログラムを適用する必要があります。 新しく作成されたクラスターには、利用可能な最新の更新プログラムが常に保持されます (最新のセキュリティ パッチを含む)。

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Linux ベースの HDInsight クラスターの OS 修正プログラム適用スケジュールを構成する方法
HDInsight クラスターの仮想マシンは、重要なセキュリティ更新プログラムをインストールできるように、ときどき再起動する必要があります。 

この記事で説明するスクリプト アクションを使用して、次のように OS の修正プログラム適用スケジュールを変更することができます。
1. 自動再起動を有効または無効にする
2. 再起動の頻度 (再起動間の日数) を設定する
3. 再起動を行う曜日を設定する

> [!NOTE]  
> このスクリプト アクションは、2016 年 8 月 1 日以降に作成された Linux ベースの HDInsight クラスターでのみ機能します。 修正プログラムは、VM が再起動された場合のみ有効になります。 

## <a name="how-to-use-the-script"></a>スクリプトの使用方法 

このスクリプトを使用するには、次の情報が必要です。
1. スクリプトの場所: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv01/os-patching-reboot-config.sh。HDInsight では、この URI を使用してクラスター内のすべての仮想マシンでスクリプトを検索して実行します。
  
2. スクリプトが適用されるクラスター ノードの種類: headnode、workernode、zookeeper。 このスクリプトは、クラスター内のすべてのノードの種類に適用する必要があります。 スクリプトをノードの種類に適用しない場合、そのノードの種類の仮想マシンが引き続き以前の更新プログラム適用スケジュールを使用します。


3.  パラメーター:このスクリプトは、次の 3 つの数値パラメーターを受け入れます。

    | パラメーター | 定義 |
    | --- | --- |
    | 自動再起動を有効または無効にする |0 または 1。 値 0 は自動再起動を無効にし、値 1 は有効にします。 |
    | 頻度 |7 ～ 90 (7 と 90 を含む)。 再起動を必要とする更新プログラムを適用するために、仮想マシンを再起動する前に待機する日数を指定します。 |
    | 曜日 |1 ～ 7 (1 と 7 を含む)。 値 1 を指定すると月曜日に、7 を指定すると日曜日に再起動が実行されます。たとえば、1 60 2 というパラメーターを指定すると、自動再起動は (多くても) 60 日ごとに、火曜日に実行されます。 |
    | 永続化 |既存のクラスターにスクリプト アクションを適用する場合、スクリプトを永続化としてマークできます。 永続化されたスクリプトは、スケーリング操作でクラスターに新しいワーカー ノードを追加したときに適用されます。 |

> [!NOTE]  
> 既存のクラスターに適用する場合、このスクリプトは永続化としてマークする必要があります。 そうしない場合、スケーリング操作で作成された新しいノードでは、既定の修正プログラム適用スケジュールが使用されます。  クラスター作成プロセスの一部としてスクリプトを適用した場合、スクリプトは自動的に永続化されます。

## <a name="next-steps"></a>次の手順

スクリプト アクションの使用法に関する具体的な手順については、「[スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」の以下のセクションをご覧ください。

* [クラスターの作成時にスクリプト アクションを使用する](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [実行中のクラスターにスクリプト アクションを適用する](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
