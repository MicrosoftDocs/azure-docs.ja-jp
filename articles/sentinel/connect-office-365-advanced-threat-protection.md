---
title: Microsoft Defender for Office 365 (旧称 Office 365 ATP) データを Azure Sentinel に接続する | Microsoft Docs
description: Microsoft Defender for Office 365 のデータを Azure Sentinel に取り込むことで、可視性を向上させて自動応答シナリオを構築します。
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
ms.date: 09/06/2020
ms.author: yelevin
ms.openlocfilehash: d5140bcd85606213f00185d4ba1f50dbe70dba63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94655496"
---
# <a name="connect-alerts-from-microsoft-defender-for-office-365"></a>Microsoft Defender for Office 365 からアラートに接続する 

> [!IMPORTANT]
>
> - **Microsoft Defender for Office 365** は、以前は **Office 365 Advanced Threat Protection (ATP)** と呼ばれていました。
>
>     一定の期間は、古い名前が製品 (Azure Sentinel のデータ コネクタなど)で引き続き使用される場合があります。
>
> - Microsoft Defender for Office 365 アラートのインジェストは、現在パブリック プレビュー段階です。 この機能は、サービス レベル アグリーメントなしで提供されており、運用環境のワークロード用には推奨されていません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
 
[Microsoft Defender for Office 365](/office365/servicedescriptions/office-365-advanced-threat-protection-service-description) は、電子メール メッセージ、悪意のある URL リンク、コラボレーション ツール内の不明なマルウェアによってもたらされるゼロデイその他の高度な脅威から組織を保護します。 Microsoft Defender for Office 365 アラートを Azure Sentinel に取り込むことにより、電子メール、ファイル共有、URL ベースの脅威に関する情報をセキュリティ操作で利用できるようになります。 これにより、組織全体のセキュリティ イベントをより包括的に分析し、効果的かつ迅速に対応するためのプレイブックを構築できます。

コネクタによって、次のアラートがインポートされます。

- 悪意のある可能性がある URL のクリックが検出された 

- マルウェアを含む電子メール メッセージが配信後に削除された

- フィッシング URL を含む電子メール メッセージが配信後に削除された 

- 電子メールがマルウェアまたはフィッシングとしてユーザーから報告された 

- 疑わしい電子メール送信パターンが検出された 

- ユーザーの電子メールの送信が制限された 

これらのアラートは、**Office セキュリティ/コンプライアンス センター** で Office ユーザーに表示されます。

## <a name="prerequisites"></a>前提条件

- コネクタを有効にするときは、Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- Azure Sentinel ワークスペースのテナントのグローバル管理者またはセキュリティ管理者である必要があります。

- [Office 365 ATP プラン 2](/microsoft-365/security/office-365-security/office-365-atp#office-365-atp-plan-1-and-plan-2) の有効なライセンスが必要です (Office 365 E5、Office 365 A5、Microsoft 365 E5 のライセンスに含まれており、別途購入できます)。 

## <a name="connect-to-microsoft-defender-for-office-365"></a>Microsoft Defender for Office 365 に接続する

Microsoft Defender for Office 365 がデプロイされていて、ポリシーが構成されている場合、アラートを Azure Sentinel に簡単に取り込むことができます。

1. Azure Sentinel で、ナビゲーション メニューから **[Data connectors]\(データ コネクタ\)** を選択します。

1. コネクタ ギャラリーで **[Microsoft Defender for Office 365]** (*Office 365 Advanced Threat Protection* という名称のままの可能性があります) を選択し、 **[コネクタ ページを開く]** を選択します。

1. **[Configuration]\(構成\)** セクションで、 **[Connect]\(接続\)** をクリックします。 

1. **[Create incidents]\(インシデントの作成\)** セクションで、 **[Enable]\(有効化\)** をクリックします。

1. 関連スキーマを使用して Office 365 ATP アラートのクエリを実行するには、**SecurityAlert** を検索し、 **[プロバイダー名]** に **OATP** を指定します。

1. **[Next steps]\(次のステップ\)** タブを選択して、Microsoft Defender for Office 365 コネクタにバンドルされているクエリ サンプルおよび分析ルール テンプレートを表示および使用します。

## <a name="next-steps"></a>次の手順

このドキュメントでは、Microsoft Defender for Office 365 を Azure Sentinel に接続する方法を学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](./tutorial-detect-threats-built-in.md)の概要。