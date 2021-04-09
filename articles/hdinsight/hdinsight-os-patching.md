---
title: Azure HDInsight クラスターの OS 修正プログラム適用スケジュールを構成する
description: Linux ベースの HDInsight クラスターの OS 修正プログラム適用スケジュールを構成する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/21/2020
ms.openlocfilehash: 636caf592baa4df771f7cc50095911d0337456d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939384"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Linux ベースの HDInsight クラスターの OS 修正プログラム適用スケジュールを構成する

> [!IMPORTANT]
> Ubuntu イメージは、公開から 3 か月以内に入手できるようになり、新しい Azure HDInsight クラスターの作成に使用できます。 実行中のクラスターに修正プログラムが自動適用されることはありません。 お客様は、スクリプトによるアクションまたはその他のメカニズムを使用して、実行中のクラスターに修正プログラムを適用する必要があります。 ベスト プラクティスとして、これらのスクリプト アクションを実行し、クラスターの作成直後にセキュリティ更新プログラムを適用することができます。

HDInsight では、OS パッチのインストール、セキュリティ更新プログラム、ノードの再起動など、クラスターでの一般的なタスクの実行がサポートされています。 これらのタスクは、次の 2 つのスクリプトを使用して実行されます。これらは[スクリプト アクション](hdinsight-hadoop-customize-cluster-linux.md)として実行でき、パラメーターを使用して構成できます。

- `schedule-reboots.sh` - クラスター ノードを即座に再起動する、またはクラスター ノードの再起動をスケジュールします。
- `install-updates-schedule-reboots.sh` - すべての更新プログラム、カーネル + セキュリティ更新プログラム、またはカーネル更新プログラムのみをインストールします。

> [!NOTE]  
> スクリプト アクションにより将来のすべての更新サイクルで更新プログラムが自動的に適用されることはありません。 新しい更新プログラムを適用する必要があるたびにスクリプトを実行して更新プログラムをインストールし、その後 VM を再起動します。

## <a name="preparation"></a>準備

運用環境にデプロイする前に、代表的な非運用環境に修正プログラムを適用します。 実際に修正プログラムを適用する前に、システムを適切にテストする計画を立てます。

クラスターとの ssh セッションから、セキュリティ更新プログラムが使用可能であることを示すメッセージを受け取る場合があります。 メッセージは次のようになります。

```
89 packages can be updated.
82 updates are security updates.

*** System restart required ***

Welcome to Spark on HDInsight.

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
| インストールする更新プログラムの種類 | 0、1、または 2 | 値 0 の場合、カーネル更新プログラムのみインストールされます。 値 1 の場合、カーネル + セキュリティ更新プログラムがインストールされ、2 の場合、すべての更新プログラムがインストールされます。 パラメーターを指定しない場合、既定値は 0 です。 |
| 実行する再起動の種類 | 0、1、または 2 | 値 0 の場合、再起動は無効になります。 値 1 の場合、スケジュール再起動が有効になります。値 2 の場合、即時再起動が有効になります。 パラメーターを指定しない場合、既定値は 0 です。 ユーザーは、入力パラメーター 1 を入力パラメーター 2 に変更する必要があります。 |

> [!NOTE]
> スクリプトを既存のクラスターに適用した後は、保存済みとしてマークする必要があります。 そうしない場合、スケーリング操作で作成された新しいノードでは、既定の修正プログラム適用スケジュールが使用されます。 クラスター作成プロセスの一部としてスクリプトを適用した場合、スクリプトは自動的に保存されます。

> [!NOTE]
> [Scheduled Restart]\(スケジュールされた再起動\) オプションでは、12 から 24 時間の期間でパッチが適用されたクラスター ノードのローリング再起動が自動的に行われ、高可用性、更新ドメイン、および障害ドメインに関して考慮されます。 [Scheduled Restart]\(スケジュールされた再起動\) では実行中のワークロードは終了されませんが、ノードが使用不可能な期間にクラスターの容量が消費される可能性があり、その場合は処理時間が長くなります。 

## <a name="next-steps"></a>次の手順

スクリプト アクションの使用法に関する具体的な手順については、「[スクリプト アクションを使用して Azure HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」の以下のセクションをご覧ください。

- [クラスターの作成時にスクリプト アクションを使用する](hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)
- [実行中のクラスターにスクリプト アクションを適用する](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
