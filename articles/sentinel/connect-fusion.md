---
title: Azure Sentinel プレビューで Fusion を有効にする | Microsoft Docs
description: Azure Sentinel で Fusion を有効にする方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 82becf50-6628-47e4-b3d7-18d7d72d505f
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: f90bb86632886d8a2ca6c6e8c60d3b79f5d0b9e9
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780436"
---
# <a name="enable-fusion"></a>Fusion の有効化

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Sentinel 内の Machine Learning は最初から組み込まれています。 セキュリティ アナリスト、セキュリティ データ サイエンティスト、セキュリティ データ エンジニアの生産性向上を目的として、ML の革新的技術でシステムを周到に設計しています。 このような革新的技術の 1 つが、特にアラートによる疲労を軽減するために組み込まれている Azure Sentinel Fusion です。

Fusion では、グラフ駆動型の機械学習アルゴリズムを使用して、Azure AD Identity Protection や Microsoft Cloud App Security などのさまざまな製品から発生する数百万件もの忠実性の低い異常アクティビティが関連付けられ、管理しやすい数の有用なセキュリティ インシデントにまとめられます。

## <a name="enable-fusion"></a>Fusion の有効化

1. Azure portal で Cloud Shell を開く アイコンを選びます。
  ![Cloud Shell](./media/connect-fusion/cloud-shell.png)

2.  下に開く **[Welcome to Cloud Shell]\(Cloud Shell へようこそ\)** ウィンドウで [PowerShell] を選択します。

3.  Azure Sentinel をデプロイした対象のサブスクリプションを選択し、**ストレージを作成**します。

4. 認証され、Azure ドライブが作成されたら、コマンド プロンプトで次のコマンドを実行します。

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=true --subscription "{Subscription Guid}"

## <a name="disable-fusion"></a>Fusion の無効化

上記と同じ手順を実行し、次のコマンドを実行します。

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=false --subscription "{Subscription Guid}"

## <a name="view-the-status-of-fusion"></a>Fusion の状態の表示

            az resource show --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --subscription "{Subscription Guid}"


## <a name="next-steps"></a>次の手順

このドキュメントでは、Azure Sentinel で Fusion を有効にする方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。

