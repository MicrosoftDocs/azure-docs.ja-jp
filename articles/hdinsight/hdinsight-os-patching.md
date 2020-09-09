---
title: Azure HDInsight クラスターの OS 修正プログラム適用スケジュールを構成する
description: Linux ベースの HDInsight クラスターの OS 修正プログラム適用スケジュールを構成する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/21/2020
ms.openlocfilehash: ddc70ccbbb5c964f16b078470517ce667bc878f1
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86082643"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Linux ベースの HDInsight クラスターの OS 修正プログラム適用スケジュールを構成する

> [!IMPORTANT]
> Ubuntu イメージは、公開から 3 か月以内に入手できるようになり、新しい Azure HDInsight クラスターの作成に使用できます。 2019 年 1 月時点で、実行中のクラスターに修正プログラムは自動適用されません。 お客様は、スクリプトによるアクションまたはその他のメカニズムを使用して、実行中のクラスターに修正プログラムを適用する必要があります。 新しく作成されたクラスターには、利用可能な最新の更新プログラムが常に保持されます (最新のセキュリティ パッチを含む)。

HDInsight では、OS パッチのインストール、セキュリティ更新プログラム、ノードの再起動など、クラスターでの一般的なタスクの実行がサポートされています。 これらのタスクは、次の 2 つのスクリプトを使用して実行されます。これらは[スクリプト アクション](hdinsight-hadoop-customize-cluster-linux.md)として実行でき、パラメーターを使用して構成できます。

- `schedule-reboots.sh` - クラスター ノードを即座に再起動する、またはクラスター ノードの再起動をスケジュールします。
- `install-updates-schedule-reboots.sh` - すべての更新プログラム、カーネル + セキュリティ更新プログラム、またはカーネル更新プログラムのみをインストールします。

> [!NOTE]  
> スクリプト アクションにより将来のすべての更新サイクルで更新プログラムが自動的に適用されることはありません。 新しい更新プログラムを適用する必要があるたびにスクリプトを実行して更新プログラムをインストールし、その後 VM を再起動します。

## <a name="preparation"></a>準備

運用環境にデプロイする前に、代表的な非運用環境に修正プログラムを適用します。 実際に修正プログラムを適用する前に、システムを適切にテストする計画を立てます。

クラスターとの ssh セッションで、アップグレードが使用可能であることを示すメッセージが表示される場合があります。 メッセージは次のようになります。

```
New release '18.04.3 LTS' available.
Run 'do-release-upgrade' to upgrade it
```

修正プログラムの適用は任意であり、ユーザーの判断に委ねられています。

## <a name="restart-nodes"></a>ノードの再起動
  
スクリプト [schedule-reboots](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh) では、クラスター内のマシンで実行される再起動の種類を設定します。 スクリプト アクションを送信するときは、3 つのノードの種類 (ヘッド ノード、ワーカー ノード、zookeeper) すべてに適用するように設定してください。 スクリプトをノードの種類に適用しないと、そのノードの種類の仮想マシンは更新または再起動されません。

`schedule-reboots script` は、1 つの数値パラメーターを受け取ります。

| パラメーター | 指定可能な値 | 定義 |
| --- | --- | --- |
| 実行する再起動の種類 | 1 または 2 | 値 1 の場合、スケジュール再起動が有効になります (12 時間から 24 時間以内にスケジュールされます)。 値 2 の場合、即時再起動が有効になります (5 分以内)。 パラメーターを指定しない場合、既定値は 1 です。 |  

## <a name="install-updates-and-restart-nodes"></a>更新プログラムのインストールとノードの再起動

スクリプト [install-updates-schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) には、さまざまな種類の更新プログラムのインストールと VM の再起動を行うためのオプションが用意されています。

`install-updates-schedule-reboots` スクリプトは、次の表に示すように、2 つの数値パラメーターを受け取ります。

| パラメーター | 指定可能な値 | 定義 |
| --- | --- | --- |
| インストールする更新プログラムの種類 | 0、1、または 2 | 値 0 の場合、カーネル更新プログラムのみインストールされます。 値 1 の場合、すべての更新プログラムがインストールされます。値 2 の場合、カーネル + セキュリティ更新プログラムのみインストールされます。 パラメーターを指定しない場合、既定値は 0 です。 |
| 実行する再起動の種類 | 0、1、または 2 | 値 0 の場合、再起動は無効になります。 値 1 の場合、スケジュール再起動が有効になります。値 2 の場合、即時再起動が有効になります。 パラメーターを指定しない場合、既定値は 0 です。 ユーザーは、入力パラメーター 1 を入力パラメーター 2 に変更する必要があります。 |

> [!NOTE]
> スクリプトを既存のクラスターに適用した後は、保存済みとしてマークする必要があります。 そうしない場合、スケーリング操作で作成された新しいノードでは、既定の修正プログラム適用スケジュールが使用されます。 クラスター作成プロセスの一部としてスクリプトを適用した場合、スクリプトは自動的に保存されます。

## <a name="next-steps"></a>次のステップ

スクリプト アクションの使用法に関する具体的な手順については、「[スクリプト アクションを使用して Azure HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」の以下のセクションをご覧ください。

- [クラスターの作成時にスクリプト アクションを使用する](hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)
- [実行中のクラスターにスクリプト アクションを適用する](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
