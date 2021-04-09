---
title: Azure Sentinel に Cloud App Security データを接続する | Microsoft Docs
description: Microsoft Cloud App Security (MCAS) のコネクタを使用して、アラートと Cloud Discovery のログを MCAS から Azure Sentinel にストリーミングする方法を説明します。
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
ms.date: 03/24/2020
ms.author: yelevin
ms.openlocfilehash: 3312eed71865508e5e83d37c7ced8cf220f13ca9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97835110"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Microsoft Cloud App Security からデータを接続する 

[Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) (MCAS) のコネクタにより、アラートと [Cloud Discovery のログ](/cloud-app-security/tutorial-shadow-it)を MCAS から Azure Sentinel にストリーミングできます。 これにより、クラウド アプリの可視化が可能になり、サイバー脅威を特定して対処するための高度な分析とデータの移動の制御を実行できるようになります。

## <a name="prerequisites"></a>前提条件

- ユーザーには、ワークスペースに対する読み取りと書き込みのアクセス許可が必要です。
- ユーザーには、ワークスペースのテナントに対するグローバル管理者またはセキュリティ管理者のアクセス許可が必要です。
- Cloud Discovery のログを Azure Sentinel にストリーミングするには、[Microsoft Cloud App Security 内で Azure Sentinel を SIEM として有効にします](/cloud-app-security/siem-sentinel)。

> [!IMPORTANT]
> Cloud Discovery ログの取り込みは、現在パブリック プレビューの段階にあります。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
 
## <a name="connect-to-cloud-app-security"></a>Cloud App Security に接続する

Cloud App Security が既にある場合は、[ネットワークで有効](/cloud-app-security/getting-started-with-cloud-app-security)になっていることを確認します。
Cloud App Security がデプロイされていてデータを取り込んでいる場合は、アラート データを Azure Sentinel に簡単にストリーミングすることができます。


1. Azure Sentinel のナビゲーション メニューから、 **[Data connectors]\(データ コネクタ\)** を選択します。 コネクタの一覧で **[Microsoft Cloud App Security]** タイルをクリックし、右下にある **[Open connector page]\(コネクタ ページを開く\)** ボタンをクリックします。

1. Azure Sentinel にストリーミングするログを選択します。 **[アラート]** と **[Cloud Discovery ログ]** (プレビュー) を選択できます。 

1. **[変更の適用]** をクリックします。

1. Cloud App Security のアラートによって Azure Sentinel で自動的にインシデントが生成されるようにするかどうかを選択できます。 **[Create incidents - Recommended!]\(インシデントの作成 - 推奨\)** で **[有効化]** を選択して、アラートからインシデントを自動的に作成する既定の分析ルールを有効にします。 次に、 **[Active rules]\(アクティブなルール\)** タブの **[分析]** でこのルールを編集します。

1. Cloud App Security のアラートに Log Analytics 内の関連スキーマを使用するには、クエリ ウィンドウで「`SecurityAlert`」と入力します。 Cloud Discovery ログのスキーマの場合は、「`McasShadowItReporting`」と入力します。

> [!NOTE]
> Cloud Discovery は、クラウド アプリへのユーザーの接続の基になるデータを集計することで、傾向を検出し、特定します。
>
> Cloud Discovery データは日単位で集計されるため、最大 24 時間分の最新データが Azure Sentinel には反映されないことに注意してください。 低レベルの調査でより最近のデータが必要な場合は、生データが存在するソース アプライアンスまたはサービスで直接行う必要があります。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Microsoft Cloud App Security を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [組み込み](./tutorial-detect-threats-built-in.md)または[カスタム](tutorial-detect-threats-custom.md)のルールを使用して、Azure Sentinel で脅威の検出を開始する。