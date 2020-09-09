---
title: Azure Sentinel に Alcide kAudit データを接続する | Microsoft Docs
description: Azure Sentinel に Alcide kAudit データを接続する方法について説明します。
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
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: cf8da1d88529a823ff4399fb955c8a5e0abbd20e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87038241"
---
# <a name="connect-your-alcide-kaudit-to-azure-sentinel"></a>Alcide kAudit を Azure Sentinel に接続する

> [!IMPORTANT]
> Azure Sentinel の Alcide kAudit データ コネクタは、現在パブリック プレビュー段階にあります。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

[Alcide kAudit](https://www.alcide.io/kaudit-K8s-forensics/) を使用すると、異常な Kubernetes の動作を特定して、検出時間を削減したうえで Kubernetes の侵害とインシデントに専念できます。 この記事では、Alcide kAudit ソリューションを Azure Sentinel に接続する方法について説明します。 Alcide kAudit データ コネクタを使用すると、kAudit ログ データを Azure Sentinel に簡単に取り込むことができます。これにより、ブック内で表示し、これを利用してカスタム アラートを作成し、調査を向上させる目的で組み入れることができます。 Alcide kAudit と Azure Sentinel 間の統合には、REST API を使用します。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- ワークスペースの共有キーに対する読み取りアクセス許可が必要です。

## <a name="configure-and-connect-alcide-kaudit"></a>Alcide kAudit を構成して接続する

Alcide kAudit によって、ログを Azure Sentinel に直接エクスポートできます。

1. Azure Sentinel ポータル内のナビゲーション メニューにある **[データ コネクタ]** をクリックします。

1. ギャラリーから **[Alcide kAudit]** を選択し、 **[Open connector page]\(コネクタ ページを開く\)** ボタンをクリックします。

1. [Alcide kAudit インストール ガイド](https://get.alcide.io/hubfs/Azure%20Sentinel%20Integration%20with%20kAudit.pdf)に記載されている詳細な手順に従ってください。

1. ワークスペース ID と主キーを要求された場合は、Alcide kAudit データ コネクタのページからコピーできます。

    :::image type="content" source="media/connect-alcide-kaudit/alcide-workspace-id-primary-key.png" alt-text="ワークスペース ID と主キー":::

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、**CustomLogs** の以下のデータ型にある **[ログ]** にデータが表示されます。

- **alcide_kaudit_detections_1_CL** - Alcide kAudit の検出 
- **alcide_kaudit_activity_1_CL** - Alcide kAudit アクティビティ ログ
- **alcide_kaudit_selections_count_1_CL** - Alcide kAudit アクティビティ数
- **alcide_kaudit_selections_details_1_CL** - Alcide kAudit アクティビティの詳細

Alcide kAudit のログで関連するスキーマを使用するには、上記のデータ型を検索します。

ログが Log Analytics に表示され始めるまで、20 分以上かかる場合があります。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Alcide kAudit を Azure Sentinel に接続する方法について学習しました。 このデータ コネクタに組み込まれている機能を最大限に活用するには、データ コネクタ ページの **[次のステップ]** タブをクリックします。 ここには、役に立つ情報の検索を開始できるように、既製のサンプル クエリがいくつか用意されています。

Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
