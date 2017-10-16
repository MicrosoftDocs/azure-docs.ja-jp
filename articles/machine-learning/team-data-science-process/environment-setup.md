---
title: "Azure でデータ サイエンス環境を設定する | Microsoft Docs"
description: "Team Data Science Process で使うためのデータ サイエンス環境を Azure で設定します。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 481cfa6a-7ea3-46ac-b0f9-2e3982c37153
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: bradsev
ms.openlocfilehash: 00f767bffc86eceb16e3e69a205983af6ba4703b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Team Data Science Process で使用するためのデータ サイエンス環境の設定
Team Data Science Process は、データの格納、処理、および分析のために、さまざまなデータ サイエンス環境を使用します。 たとえば、Azure Blob Storage、いくつかの種類の Azure 仮想マシン、HDInsight (Hadoop) クラスター、Azure Machine Learning ワークスペースなどです。 使用する環境を決定する方法は、モデル化するデータの種類と量、さらにクラウド内でのデータの宛先によって異なります。 

* この決定を行う際の考慮事項については、「[Azure Machine Learning のデータ サイエンス環境を計画する](plan-your-environment.md)」をご覧ください。 
* 高度な分析を実施する際に発生する可能性があるシナリオのカタログについては、 [Team Data Science Process のシナリオ](plan-sample-scenarios.md)

このメニューは、Team Data Science Process によって使用されるさまざまなデータ サイエンス環境の設定方法を説明するトピックにリンクしています。

[!INCLUDE [data-science-environment-setup](../../../includes/cap-setup-environments.md)]

**Microsoft データ サイエンス仮想マシン (DSVM)** は、Azure Virtual Machine (VM) イメージとしても利用できます。 VM は、あらかじめインストールされており、データ分析と機械学習用に一般的に使用されているいくつかのツールで構成されています。 DSVM は、Windows と Linux の両方で使用できます。 詳しくは、「[Linux および Windows 用のクラウド ベースのデータ サイエンス仮想マシンの概要](../data-science-virtual-machine/overview.md)」をご覧ください。

