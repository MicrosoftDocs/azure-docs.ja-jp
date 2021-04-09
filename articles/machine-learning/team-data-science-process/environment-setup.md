---
title: Azure でデータ サイエンス環境を設定する - Team Data Science Process
description: Team Data Science Process で使うためのデータ サイエンス環境を Azure で設定します。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 557550627fe2c39571a848723e5d716324fee240
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93321176"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Team Data Science Process で使用するためのデータ サイエンス環境の設定
Team Data Science Process は、データの格納、処理、および分析のために、さまざまなデータ サイエンス環境を使用します。 たとえば、Azure Blob Storage、いくつかの種類の Azure 仮想マシン、HDInsight (Hadoop) クラスター、Azure Machine Learning ワークスペースなどです。 使用する環境を決定する方法は、モデル化するデータの種類と量、さらにクラウド内でのデータの宛先によって異なります。 

* この決定を行う際の考慮事項については、「[Azure Machine Learning のデータ サイエンス環境を計画する](plan-your-environment.md)」をご覧ください。 
* 高度な分析を実施する際に発生する可能性があるシナリオのカタログについては、 [Team Data Science Process のシナリオ](plan-sample-scenarios.md)

以下の記事では、Team Data Science Process によって使用されるさまざまなデータ サイエンス環境の設定方法が説明されています。

* [Azure のストレージ アカウント](../../storage/common/storage-account-create.md)
* [HDInsight (Hadoop) クラスター](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)
* [Azure Machine Learning Studio (クラシック) ワークスペース](../classic/create-workspace.md)

**Microsoft データ サイエンス仮想マシン (DSVM)** は、Azure Virtual Machine (VM) イメージとしても利用できます。 VM は、あらかじめインストールされており、データ分析と機械学習用に一般的に使用されているいくつかのツールで構成されています。 DSVM は、Windows と Linux の両方で使用できます。 詳しくは、[Linux および Windows 用のクラウドベースの Data Science Virtual Machine の概要](../data-science-virtual-machine/overview.md)に関するページをご覧ください。

それぞれの作成方法に関するページを参照してください。

- [Windows DSVM](../data-science-virtual-machine/provision-vm.md)
- [Ubuntu DSVM](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS DSVM](../data-science-virtual-machine/release-notes.md)