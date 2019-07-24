---
title: Azure Sentinel プレビューに Azure ATP データを接続する | Microsoft Docs
description: Azure Sentinel に Azure ATP データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5bf3cc44-ecda-4c78-8a63-31ab42f43605
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 153b6cb44cbcf5ecca9a4941c6bef7a8721dc56c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59789290"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Azure Advanced Threat Protection (ATP) からデータを接続する

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


シングル クリックで、[Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) から Azure Sentinel にログをストリーミングできます。

## <a name="prerequisites"></a>前提条件

- グローバル管理者またはセキュリティ管理者のアクセス許可を持つユーザー
- Azure ATP のプライベート プレビューのお客様であること

## <a name="connect-to-azure-atp"></a>Azure ATP への接続

Azure ATP プライベート プレビューのバージョンが、[ネットワーク上で有効になっていること](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1) を確認します。
Azure ATP がデプロイされていて、データを取り込んでいる場合、不審なアラートの Azure Sentinel へのストリーミングは簡単に実行できます。 アラートが Azure Sentinel へのストリーミングを開始するには、最大で 24 時間かかる場合があります。



1. Azure Sentinel で **[データ コネクタ]** を選択し、**[Azure ATP]** タイルをクリックします。

2. **[接続]** をクリックします。

6. Azure ATP アラートで Log Analytics 内の関連スキーマを使用するには、**SecurityAlert** を検索します。

## <a name="next-steps"></a>次の手順
このドキュメントでは、Azure Advanced Threat Protection を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、以下の記事を参照してください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。

