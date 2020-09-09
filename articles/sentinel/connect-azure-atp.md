---
title: Azure Sentinel に Azure ATP データを接続する | Microsoft Docs
description: シングル クリックで、Azure Advanced Threat Protection (ATP) から Azure Sentinel にログをストリーミングする方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: f58c38ccfa234752a80c05c300d245c6c9e97cf0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559179"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Azure Advanced Threat Protection (ATP) からデータを接続する

> [!IMPORTANT]
> Azure Sentinel の Azure Advanced Threat Protection データ コネクタは、現在パブリック プレビューです。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

シングル クリックで、[Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) から Azure Sentinel にログをストリーミングできます。

## <a name="prerequisites"></a>前提条件

- グローバル管理者またはセキュリティ管理者のアクセス許可を持つユーザー
- Azure ATP のプレビューのお客様であることと、Azure ATP と Microsoft Cloud App Security の統合を有効にすること。 詳細については、「[Azure Advanced Protection の統合](https://docs.microsoft.com/cloud-app-security/aatp-integration)」を参照してください。

## <a name="connect-to-azure-atp"></a>Azure ATP への接続

Azure ATP のプレビュー バージョンが、[ネットワーク上で有効になっていること](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1) を確認します。
Azure ATP がデプロイされていて、データを取り込んでいる場合、不審なアラートの Azure Sentinel へのストリーミングは簡単に実行できます。 アラートが Azure Sentinel へのストリーミングを開始するには、最大で 24 時間かかる場合があります。


1. Azure ATP を Azure Sentinel に接続するには、まず Azure ATP と Microsoft Cloud App Security の統合を有効にする必要があります。 この方法の詳細については、「[Azure Advanced Threat Protection の統合](https://docs.microsoft.com/cloud-app-security/aatp-integration)」を参照してください。

1. Azure Sentinel で **[Data connectors]\(データ コネクタ\)** を選択し、 **[Azure Advanced Threat Protection (プレビュー)]** タイルをクリックします。

1. Azure ATP のアラートによって Azure Sentinel で自動的にインシデントが生成されるようにするかどうかを選択できます。 **[Create incidents]\(インシデントの作成\)** で **[有効化]** を選択して、接続されたセキュリティ サービスで生成されたアラートからインシデントを自動的に作成する既定の分析ルールを有効にします。 次に、 **[分析]** でこのルールを編集してから、 **[Active rules]\(アクティブなルール\)** を選択します。

1. **[Connect]** をクリックします。

1. Azure ATP アラートで Log Analytics 内の関連スキーマを使用するには、**SecurityAlert** を検索します。

> [!NOTE]
> アラートが 30 KB より大きい場合、Azure Sentinel はアラートの [エンティティ] フィールドの表示を停止します。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure Advanced Threat Protection を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。

