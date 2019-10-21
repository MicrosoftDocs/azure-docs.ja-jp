---
title: Azure Sentinel に Cloud App Security データを接続する | Microsoft Docs
description: Azure Sentinel に Cloud App Security データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 6e71028855344d083dedf3493682e1e27685de48
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240119"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Microsoft Cloud App Security からデータを接続する 



[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) から Azure Sentinel にシングル クリックでログをストリーミングできます。 この接続により、Cloud App Security からのアラートを Azure Sentinel にストリーミングすることができます。 

## <a name="prerequisites"></a>前提条件

- 全体管理者またはセキュリティ管理者のアクセス許可を持つユーザー

## <a name="connect-to-cloud-app-security"></a>Cloud App Security に接続する

Cloud App Security が既にある場合は、[ネットワークで有効](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)になっていることを確認します。
Cloud App Security がデプロイされていてデータを取り込んでいる場合は、アラート データを Azure Sentinel に簡単にストリーミングすることができます。


1. Azure Sentinel で **[Data connectors]\(データ コネクタ\)** を選択し、 **[Cloud App Security]** タイルをクリックします。

1. Azure Sentinel にストリーミングするログを選択します。 **[アラート]** を選択できます。 

1. Microsoft Cloud App Security のアラートによって Azure Sentinel で自動的にインシデントが生成されるようにするかどうかを選択できます。 **[Create incidents]\(インシデントの作成\)** で **[有効化]** を選択して、接続されたセキュリティ サービスで生成されたアラートからインシデントを自動的に作成する既定の分析ルールを有効にします。 次に、 **[分析]** でこのルールを編集してから、 **[Active rules]\(アクティブなルール\)** を選択します。

1. **[接続]** をクリックします。

1. Cloud App Security アラートで Log Analytics 内の関連スキーマを使用するには、**SecurityAlert** を検索します。




## <a name="next-steps"></a>次の手順
このドキュメントでは、Microsoft Cloud App Security を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、以下の記事を参照してください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
