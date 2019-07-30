---
title: Linux ベースの HDInsight クラスターの OS 修正プログラム適用スケジュールを構成する - Azure
description: Linux ベースの HDInsight クラスターの OS 修正プログラム適用スケジュールを構成する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: efe74618b269000749f7ba6c24d35903e540dcfb
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657050"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Linux ベースの HDInsight クラスターの OS 修正プログラム適用スケジュールを構成する 

> [!IMPORTANT]
> Ubuntu イメージは、公開から 3 か月以内に入手できるようになり、新しい Azure HDInsight クラスターの作成に使用できます。 2019 年 1 月時点で、実行中のクラスターに修正プログラムは自動適用されません。 お客様は、スクリプトによるアクションまたはその他のメカニズムを使用して、実行中のクラスターに修正プログラムを適用する必要があります。 新しく作成されたクラスターには、利用可能な最新の更新プログラムが常に保持されます (最新のセキュリティ パッチを含む)。

HDInsight クラスターの仮想マシン (VM) をときどき再起動して、重要なセキュリティ更新プログラムをインストールする必要があります。

この記事で説明するスクリプト アクションを使用して、次のように OS 修正プログラム適用スケジュールを変更することができます。

1. すべての更新プログラムをインストールするか、カーネル + セキュリティ更新プログラムまたはカーネル更新プログラムのみをインストールする。
2. VM を即座に再起動する、または VM の再起動をスケジュールする。

> [!NOTE]  
> この記事で説明されているスクリプト アクションは、2016 年 8 月 1 日より後に作成された Linux ベースの HDInsight クラスターでのみ機能します。 修正プログラムは、VM を再起動した後にのみ有効になります。
> スクリプト アクションにより将来のすべての更新サイクルで更新プログラムが自動的に適用されることはありません。 新しい更新プログラムを適用する必要があるたびにスクリプトを実行して更新プログラムをインストールし、その後 VM を再起動します。

## <a name="add-information-to-the-script"></a>情報をスクリプトに追加する

スクリプトを使用するには、次の情報が必要です。

- install-updates-schedule-reboots スクリプトの場所: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh 。
    
   HDInsight では、この URI を使用してクラスター内のすべての VM でスクリプトが検索され実行されます。 このスクリプトでは、更新プログラムをインストールして VM を再起動するためのオプションが提供されています。
  
- schedule-reboots スクリプトの場所: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh 。
    
   HDInsight では、この URI を使用してクラスター内のすべての VM でスクリプトが検索され実行されます。 このスクリプトにより VM が再起動されます。
  
- スクリプトが適用されるクラスター ノードの種類は、headnode、workernode、zookeeper です。 このスクリプトをクラスター内のすべてのノードの種類に適用します。 スクリプトをノードの種類に適用しないと、そのノードの種類の仮想マシンは更新または再起動されません。

- install-updates-schedule-reboots スクリプトでは、2 つの数値パラメーターが受け付けられます。

    | パラメーター | 定義 |
    | --- | --- |
    | カーネル更新プログラムのみをインストールする/すべての更新プログラムをインストールする/カーネル + セキュリティ更新プログラムのみをインストールする|0、1、または 2。 値 0 の場合、カーネル更新プログラムのみインストールされます。 値 1 の場合、すべての更新プログラムがインストールされます。値 2 の場合、カーネル + セキュリティ更新プログラムのみインストールされます。 パラメーターを指定しない場合、既定値は 0 です。 |
    | 再起動なし/スケジュール再起動を有効にする/即時再起動を有効にする |0、1、または 2。 値 0 の場合、再起動は無効になります。 値 1 の場合、スケジュール再起動が有効になります。値 2 の場合、即時再起動が有効になります。 パラメーターを指定しない場合、既定値は 0 です。 ユーザーは、入力パラメーター 1 を入力パラメーター 2 に変更する必要があります。 |
   
 - schedule-reboots スクリプトでは、1 つの数値パラメーターが受け付けられます。

    | パラメーター | 定義 |
    | --- | --- |
    | スケジュール再起動を有効にする/即時再起動を有効にする |1 または 2。 値 1 の場合、スケジュール再起動が有効になります (12 時間から 24 時間以内にスケジュールされます)。 値 2 の場合、即時再起動が有効になります (5 分以内)。 パラメーターを指定しない場合、既定値は 1 です。 |  

> [!NOTE]
> スクリプトを既存のクラスターに適用した後は、保存済みとしてマークする必要があります。 そうしない場合、スケーリング操作で作成された新しいノードでは、既定の修正プログラム適用スケジュールが使用されます。 クラスター作成プロセスの一部としてスクリプトを適用した場合、スクリプトは自動的に保存されます。


## <a name="next-steps"></a>次の手順

スクリプト アクションの使用法に関する具体的な手順については、「[スクリプト アクションを使用して Azure HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」の以下のセクションをご覧ください。

* [クラスターの作成時にスクリプト アクションを使用する](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [実行中のクラスターにスクリプト アクションを適用する](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
