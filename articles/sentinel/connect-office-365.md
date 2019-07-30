---
title: Azure Sentinel Preview に Office 365 データを接続する | Microsoft Docs
description: Azure Sentinel に Office 365 データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: ff7c862e-2e23-4a28-bd18-f2924a30899d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 1d6a467307e4816ffbb45f23bac55b8023267352
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611253"
---
# <a name="connect-data-from-office-365-logs"></a>Office 365 ログからデータを接続する

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

[Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) から Azure Sentinel にシングル クリックで監査ログをストリーミングできます。 複数のテナントから Azure Sentinel の 1 つのワークスペースに監査ログをストリーミングできます。 Office 365 アクティビティ ログのコネクタは、継続的なユーザー アクティビティに分析情報を提供します。 Office 365 からさまざまなユーザー、管理者、システム、ポリシーのアクションとイベントに関する情報を取得します。 Office 365 のログをAzure Sentinel に接続することで、このデータを使用して、ダッシュボードの表示、カスタムのアラートの作成、調査プロセスの向上を実現できます。


## <a name="prerequisites"></a>前提条件

- テナントのグローバル管理者またはセキュリティ管理者である
- 接続を作成するために Azure Sentinel にログインしているコンピューターで、Web トラフィックに対してポート 4433 が開かれていることを確認します。

## <a name="connect-to-office-365"></a>Office 365 への接続

1. Azure Sentinel で **[Data connectors]\(データ コネクタ\)** を選択し、 **[Office 365]** タイルをクリックします。

2. まだ有効でない場合は、 **[接続]** で、 **[有効化]** を使用して Office 365 ソリューションを有効にします。 既に有効な場合は、既に有効になっていることが接続画面で識別されます。
1. Office 365 により、複数のテナントから Azure Sentinel にデータをストリーミングできます。 接続先のテナントごとに、 **[Connect tenants to Azure Sentinel]\(Azure Sentinel にテナントを接続\)** でテナントを追加します。 
1. Active Directory Domain Services の画面が開きます。 Azure Sentinel に接続するテナントごとにグローバル管理者ユーザーによる認証を求められるので、ログを読み取るための Azure Sentinel へのアクセス許可を付与します。 
5. Stream の Office 365 アクティビティ ログで、 **[選択]** をクリックし、Azure Sentinel にストリーミングするログの種類を選択します。 現時点では、Azure Sentinel は、Exchange および SharePoint をサポートしています。

4. **[変更の適用]** をクリックします。

3. Log Analytics で Office 365 のログに関連するスキーマを使用するために、**OfficeActivity** を検索します。


## <a name="next-steps"></a>次の手順
このドキュメントでは、Office 365 を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。

