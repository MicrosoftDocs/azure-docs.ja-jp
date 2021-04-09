---
title: VMware Carbon Black Cloud Endpoint Standard データを Azure Sentinel に接続する | Microsoft Docs
description: VMware Carbon Black Cloud Endpoint Standard データを Azure Sentinel に接続する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 2c26931db617109c36c19b73bd3ec9263f4b40cc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100096448"
---
# <a name="connect-your-vmware-carbon-black-cloud-endpoint-standard-to-azure-sentinel-with-azure-function"></a>Azure Function を使用して、VMware Carbon Black Cloud Endpoint Standard を Azure Sentinel に接続します

> [!IMPORTANT]
> 現在、Azure Sentinel の VMware Carbon Black Cloud Endpoint Standard データ コネクタはパブリック プレビュー段階にあります。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

VMware Carbon Black Cloud Endpoint Standard コネクタを使用すると、Azure Sentinel にすべての [VMware Carbon Black Cloud Endpoint Standard](https://www.carbonblack.com/products/endpoint-standard/) セキュリティ ソリューションのログを簡単に接続して、ダッシュボードの表示、カスタム アラートの作成、調査の改善を行うことができます。 VMware Carbon Black と Azure Sentinel の統合では、Azure Functions を使用して REST API によりログ データをプルします。


> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="configure-and-connect-vmware-carbon-black-cloud-endpoint-standard"></a>VMware Carbon Black Cloud Endpoint Standard の構成と接続

Azure Functions では、VMware Carbon Black Cloud Endpoint Standard から直接イベントとログを統合およびプルし、Azure Sentinel に転送できます。

1. Azure Sentinel ポータルで、 **[Data connectors]\(データ コネクタ\)** をクリックし、 **[VMware Carbon Black]** コネクタを選択します。
2. **[Open connector page]\(コネクタ ページを開く\)** を選択します。
3. **VMware Carbon Black** ページの指示に従います。


## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、**CarbonBlackAuditLogs_CL**、**CarbonBlackNotifications_CL**、および ****CarbonBlackEvents_CL**** テーブルの下の Log Analytics にデータが表示されます。

## <a name="validate-connectivity"></a>接続の検証
ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。 


## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure Functions アプリを使用して VMware Carbon Black Cloud Endpoint Standard を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。

