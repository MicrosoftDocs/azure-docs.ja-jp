---
title: Microsoft Defender for Identity (旧称 Azure ATP) データを Azure Sentinel に接続する | Microsoft Docs
description: シングル クリックで、Microsoft Defender for Identity (旧称 Azure Advanced Threat Protection (ATP)) から Azure Sentinel にログをストリーミングする方法について説明します。
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
ms.openlocfilehash: 1fe36dc7b3c04f033c1b693b657e07bcf42e3223
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101715005"
---
# <a name="connect-data-from-microsoft-defender-for-identity-formerly-azure-advanced-threat-protection"></a>Microsoft Defender for Identity (旧称 Azure Advanced Threat Protection) からデータを接続する

> [!IMPORTANT]
> Azure Sentinel での Microsoft Defender for Identity データ コネクタは、現在パブリック プレビューです。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、[Microsoft Defender for Identity](/azure-advanced-threat-protection/what-is-atp) から Azure Sentinel にセキュリティ アラートをストリーミングする方法について説明します。 

セキュリティ アラートに加えて正常性アラートを転送するには、Microsoft Defender for Identity を Syslog サーバーと統合します。 詳細については、[Microsoft Defender for Identity のドキュメント](/defender-for-identity/setting-syslog)を参照してください。 

## <a name="prerequisites"></a>前提条件

- グローバル管理者またはセキュリティ管理者のアクセス許可を持つユーザー
- Microsoft Defender for Identity のプレビュー ユーザーになり、Microsoft Defender for Identity と Microsoft Cloud App Security の統合を有効にする必要があります。 詳細については、[Microsoft Defender for Identity の統合](https://www.microsoft.com/microsoft-365/identity/advance-threat-protection)に関するページを参照してください。

## <a name="connect-to-microsoft-defender-for-identity"></a>Microsoft Defender for Identity に接続する

Microsoft Defender for Identity プレビュー バージョンが[お使いのネットワークで有効にされている](/azure-advanced-threat-protection/install-atp-step1)ことを確認します。
Microsoft Defender for Identity がデプロイされ、データの取り込みを行っている場合は、不審なアラートを Azure Sentinel に簡単にストリーム配信できます。 アラートが Azure Sentinel へのストリーミングを開始するには、最大で 24 時間かかる場合があります。


1. Microsoft Defender for Identity を Azure Sentinel に接続するには、最初に Microsoft Defender for Identity と Microsoft Cloud App Security の統合を有効にする必要があります。 これを行う方法については、[Microsoft Defender for Identity の統合](https://www.microsoft.com/microsoft-365/identity/advance-threat-protection)に関するページを参照してください。

1. Azure Sentinel で **[データ コネクタ]** を選択し、 **[Microsoft Defender for Identity (プレビュー)]** タイルをクリックします。

1. Microsoft Defender for Identity からのアラートによって Azure Sentinel で自動的にインシデントが生成されるようにするかどうかを選択できます。 **[Create incidents]\(インシデントの作成\)** で **[有効化]** を選択して、接続されたセキュリティ サービスで生成されたアラートからインシデントを自動的に作成する既定の分析ルールを有効にします。 次に、 **[分析]** でこのルールを編集してから、 **[Active rules]\(アクティブなルール\)** を選択します。

1. **[Connect]** をクリックします。

1. Microsoft Defender for Identity のアラートで Log Analytics の関連スキーマを使用するには、**SecurityAlert** を検索します。

> [!NOTE]
> アラートが 30 KB より大きい場合、Azure Sentinel はアラートの [エンティティ] フィールドの表示を停止します。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Microsoft Defender for Identity を Azure Sentinel に接続する方法を学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。