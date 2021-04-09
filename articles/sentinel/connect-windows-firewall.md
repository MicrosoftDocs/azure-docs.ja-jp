---
title: Azure Sentinel に Windows Defender ファイアウォール データを接続する | Microsoft Docs
description: Azure Sentinel で Windows ファイアウォール コネクタを有効にすると、Log Analytics エージェントがインストールされている Windows マシンからファイアウォール イベントを簡単にストリーミングできます。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2020
ms.author: yelevin
ms.openlocfilehash: cf7e389fc4a8a8dfa88691dc034611cae3471731
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94655342"
---
# <a name="connect-windows-defender-firewall-with-advanced-security-to-azure-sentinel"></a>セキュリティが強化された Windows Defender ファイアウォールを Azure Sentinel に接続する

[セキュリティが強化された Windows Defender ファイアウォール](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) コネクタを使用すると、Azure Sentinel でワークスペース内の任意の Windows コンピューターから、セキュリティが強化された Windows Defender ファイアウォールのログを簡単に取り込むことができます。 この接続により、ブック内の Windows ファイアウォール イベントを表示して分析したり、これをカスタム アラートの作成に使用したり、セキュリティ調査に役立てたりしながら、組織のネットワークについての分析情報を手に入れ、セキュリティ運用機能を向上させることができます。 

ソリューションは、Log Analytics エージェントがインストールされている Windows マシンから Windows ファイアウォール イベントを収集します。 

> [!NOTE]
> - データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。
>
> - Azure Security Center からの Azure Defender アラートが既に Azure Sentinel ワークスペースに収集されている場合、このコネクタを介して Windows ファイアウォールソリューションを有効にする必要はありません。 ただし、これを有効にした場合は、重複したデータは発生しません。 

## <a name="prerequisites"></a>前提条件

- 監視するマシンが接続されているワークスペースの読み取りと書き込み権限を持っている必要があります。

- **Azure Sentinel** ロールに加えて、そのワークスペースの SecurityInsights ソリューションで **Log Analytics 共同作成者** ロールに割り当てられている必要があります。 [詳細情報](../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="enable-the-connector"></a>コネクタを有効にする 

1. Azure Sentinel ポータルのナビゲーション メニューから **[Data connectors]\(データ コネクタ\)** を選択します。

1. コネクタ ギャラリーから **[Windows ファイアウォール]** を選択し、 **[Open connector page]\(コネクタ ページを開く\)** をクリックしします。

### <a name="instructions-tab"></a>[手順] タブ

- **Windows マシンが Azure にある場合は、次の手順を実行します。**

    1. **[Install agent on Azure Windows Virtual Machine]\(Azure Windows Virtual Machine にエージェントをインストールする\)** をクリックします。

    1. 表示される **[Download & install agent for Azure Windows Virtual machines]\(Azure Windows Virtual Machine 用のエージェントをダウンロードしてインストールする\) >** リンクをクリックします。

    1. **[仮想マシン]** 一覧で、Azure Sentinel にストリーミングする Windows マシンを選択します。 ([OS] 列フィルターで **Windows** を選択して、Windows VM のみが表示されるようにすることができます)。

    1. 開いているその VM のウィンドウで、 **[接続]** をクリックします。

    1. **[仮想マシン]** ウィンドウに戻り、接続する他のすべての VM に対して前の 2 つの手順を繰り返します。 完了したら、 **[Windows ファイアウォール]** ウィンドウに戻ります。

- **Windows マシンが Azure VM ではない場合は、次の手順を実行します。**

    1. **[Azure 以外の Windows マシンにエージェントをインストールする]** を選択します。

    1. 表示される **[Download & install agent for non-Azure Windows machines]\(Azure 以外の Windows Machine 用のエージェントをダウンロードしてインストールする\) >** リンクをクリックします。

    1. **[エージェント管理]** ウィンドウで、必要に応じて **[Download Windows Agent (64 bit)]\(Windows エージェントのダウンロード (64 ビット)\)** または **[Download Windows Agent (32 bit)]\(Windows エージェントのダウンロード (32 ビット)\)** のいずれかを選択します。

    1. **[ワークスペース ID]** 、 **[主キー]** 、および **[セカンダリ キー]** 文字列をテキスト ファイルにコピーします。 そのファイルとダウンロードしたインストール ファイルを Windows マシンにコピーします。 インストール ファイルを実行し、メッセージが表示されたら、インストール時にテキスト ファイルに ID とキー文字列を入力します。

    1. **[Windows ファイアウォール]** ウィンドウに戻ります。

1. **[Install solution]\(ソリューションのインストール\)** をクリックします。

### <a name="next-steps-tab"></a>[次のステップ] タブ

- Windows ファイアウォールのログ データに関する分析情報を得るには、**Windows ファイアウォール** データ コネクタにバンドルされている使用可能な推奨ブックとクエリ サンプルを参照してください。

- **[ログ]** で Windows ファイアウォールのデータのクエリを実行するには、クエリ ウィンドウで「**WindowsFirewall**」と入力します。

## <a name="validate-connectivity"></a>接続の検証
 
Windows ファイアウォールのログはローカル ログ ファイルが容量に達した場合にのみ Azure Sentinel に送信されるため、ログを既定のサイズである 4096 KB のままにすると、多くの場合、コレクションの待機時間が長くなります。 ログ ファイルのサイズを小さくすることで、待機時間を短縮できます。 [Windows ファイアウォール ログを構成する](/windows/security/threat-protection/windows-firewall/configure-the-windows-firewall-log)手順を参照してください。 ログ サイズに最小値 (1 KB) を定義することで収集の待機時間を実質的に解消することができますが、これはローカル コンピューターのパフォーマンスに悪影響を及ぼす可能性があることに注意してください。 

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Windows ファイアウォールを Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。