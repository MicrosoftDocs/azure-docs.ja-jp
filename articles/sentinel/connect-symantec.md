---
title: Azure Sentinel プレビューに Symantec ICDX データを接続する | Microsoft Docs
description: Azure Sentinel に Symantec ICDX データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 1a6843fb1668307aa442011233999c648901d404
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59793474"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Symantec ICDX アプライアンスを接続する 

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Symantec ICDX コネクタを使用すると、Azure Sentinel にすべての Symantec セキュリティ ソリューションのログを簡単に接続でき、ダッシュボードの表示、カスタム アラートの作成、調査の改善を行うことができます。 これにより、組織のネットワークに関するより詳しい分析情報が得られ、セキュリティ運用機能が向上します。 Symantec ICDX と Azure Sentinel との統合には、REST API を使用します。


> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="configure-and-connect-symantec-icdx"></a>Symantec ICDX の構成および接続 

Symantec ICDX では、ログを統合し、Azure Sentinel に直接エクスポートすることができます。

1. ICDX 管理コンソールを開きます。
2. 左側のナビゲーション メニューで  **[構成]**、 **[フォワーダー]** タブの順に選択します。
3. Microsoft Azure Log Analytics の行で **[More]\(詳細\)**、**[編集]** の順にクリックします。 
4. **[Microsoft Azure Log Analytics Forwarder]\(Microsoft Azure Log Analytics フォワーダー\)** ウィンドウで、次のように設定します。
    - カスタム ログの名前は、既定の SymantecICDX のままにします。
    - ワークスペース ID をコピーして、 **[Customer Identifier]\(顧客 ID\)** フィールドに貼り付けます。 **[主キー]** をコピーして、[共有キー] フィールドに貼り付けます。 この値は、**[データ コネクタ]**、**[Symantec ICDX]** の順に選択して Azure Sentinel ポータルからコピーすることができます。
6. Log Analytics で Symantec ICDX イベントに関連するスキーマを使用するには、**SymantecICDX_CL** を検索します。


## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、20 分以上かかる場合があります。 



## <a name="next-steps"></a>次の手順
このドキュメントでは、Symantec ICDX を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。

