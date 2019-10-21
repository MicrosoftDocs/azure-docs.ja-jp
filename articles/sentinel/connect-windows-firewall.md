---
title: Azure Sentinel に Windows ファイアウォール データを接続する | Microsoft Docs
description: Azure Sentinel に Windows ファイアウォール データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 840e8b3bc86281a8c42689b1cb68917741ef2bd9
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240621"
---
# <a name="connect-windows-firewall"></a>Windows ファイアウォールの接続



Windows ファイアウォールのログが Azure Sentinel ワークスペースに接続されている場合、Windows ファイアウォール コネクタを使用すると、それらのログを簡単に接続できます。 この接続により、ダッシュボードを表示し、カスタム アラートを作成し、調査を改善することができます。 これにより、組織のネットワークに関するより詳しい分析情報が得られ、セキュリティ運用機能が向上します。 ソリューションは、Log Analytics エージェントがインストールされている Windows マシンから Windows ファイアウォール イベントを収集します。 


> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="enable-the-connector"></a>コネクタを有効にする 

1. Azure Sentinel ポータルで **[データ コネクタ]** を選択し、次に **[Windows ファイアウォール]** タイルをクリックします。 
1.  Windows マシンが Azure にある場合は、次の手順を実行します。
    1. **[Install agent on Azure Windows virtual machine]\(Azure Windows 仮想マシンにエージェントをインストールする\)** をクリックします。
    1. **[仮想マシン]** 一覧で、Azure Sentinel にストリーミングする Windows マシンを選択します。 これが Windows VM であることを確認します。
    1. 開いているその VM のウィンドウで、 **[接続]** をクリックします。  
    1. **[Windows firewall connector]\(Windows ファイアウォール コネクタ\)** ウィンドウで **[Enable]\(有効にする\)** をクリックします。 

2. Windows マシンが Azure VM ではない場合は、次の手順を実行します。
    1. **[Install agent on non-Azure machines]\(非 Azure マシンにエージェントをインストールする\)** をクリックします。
    1. **[ダイレクト エージェント]** ウィンドウで、 **[Download Windows agent (64 bit)]\(Windows エージェントのダウンロード (64 ビット)\)** または **[Download Windows agent (32 bit)]\(Windows エージェントのダウンロード (32 ビット)\)** を選択します。
    1. お使いの Windows マシンにエージェントをインストールします。 **ワークスペース ID**、**主キー**、**2 次キー**をコピーし、インストール中に入力を要求されたらこれらを使用します。

4. ストリーミングするデータの種類を選択します。
5. **[Install solution]\(ソリューションのインストール\)** をクリックします。
6. Log Analytics で Windows ファイアウォールに関連するスキーマを使用するために、**SecurityEvent** を検索します。

## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、20 分以上かかる場合があります。 



## <a name="next-steps"></a>次の手順
このドキュメントでは、Windows ファイアウォールを Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、以下の記事を参照してください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。

