---
title: Azure Sentinel に Office 365 のログを接続する | Microsoft Docs
description: Office 365 のログ コネクタを使用して、Exchange と SharePoint (OneDrive を含む) で実行中のユーザーと管理者のアクティビティに関する情報を取得する方法について説明します。
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
ms.date: 05/21/2020
ms.author: yelevin
ms.openlocfilehash: 180b25f80bd27caea20b1c17cd84fda38c172e0f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559335"
---
# <a name="connect-office-365-logs-to-azure-sentinel"></a>Azure Sentinel に Office 365 のログを接続する

[Office 365](https://docs.microsoft.com/office/) のログ コネクタは、**Exchange** および **SharePoint** (**OneDrive** を含む) で実行中のユーザーと管理者のアクティビティに関する情報を Azure Sentinel に取り込みます。 この情報には、ファイルのダウンロード、送信されたアクセス要求、グループ イベントへの変更、メールボックスの操作などのアクションの詳細と、アクションを実行したユーザーの詳細が含まれます。 Office 365 のログを Azure Sentinel に接続することで、ブックでのこのデータの表示と分析、カスタム アラートの作成のためのこのデータに対するクエリの実行、調査プロセスの改善のためのこのデータの組み込みが可能になり、Office 365 のセキュリティをより深く把握できるようになります。

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- テナントのグローバル管理者またはセキュリティ管理者である必要があります。

- Office 365 のデプロイは、Azure Sentinel ワークスペースと同じテナントに存在する必要があります。

> [!IMPORTANT]
> - コネクタが Office 365 Management Activity API を使用してデータにアクセスできるようにするには、Office 365 のデプロイで**統合監査ログ**を有効にする必要があります。 お使いの Office 365 または Microsoft 365 のライセンスの種類によっては、既定で有効になっている場合もあれば、有効になっていない場合もあります。 お使いのライセンスの種類に応じた統合監査ログの状態を確認するには、[Office 365 セキュリティ/コンプライアンス センター](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center)を参照してください。
> - Office 365 統合監査ログの現在の状態を手動で有効にしたり、無効にしたり、確認したりすることもできます。 手順については、「[Turn Office 365 audit log search on or off](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off)」 (Office 365 監査ログの検索を有効または無効にする) を参照してください。
> - 詳細については、「[Office 365 管理アクティビティ API リファレンス](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)」を参照してください。


   > [!NOTE]
   > 前述のように、またコネクタ ページの **[データ型]** の下に表示されるように、現在、Azure Sentinel Office 365 コネクタでは、Microsoft Exchange と SharePoint (OneDrive を含む) からのみ、監査ログの取り込みがサポートされています。 ただし、[Teams のデータ](https://techcommunity.microsoft.com/t5/azure-sentinel/protecting-your-teams-with-azure-sentinel/ba-p/1265761) または[他の Office のデータ](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-office-365-alerts-with-graph-security-api/ba-p/984888)を Azure Sentinel に取り込みたい場合には、いくつかの外部ソリューションを使用できます。 

## <a name="enable-the-office-365-log-connector"></a>Office 365 ログ コネクタを有効にする

1. Azure Sentinel のナビゲーション メニューから、 **[Data connectors]\(データ コネクタ\)** を選択します。

1. **[Data connectors]\(データ コネクタ\)** の一覧で **[Office 365]** をクリックし、右下にある **[Open connector page]\(コネクタ ページを開く\)** ボタンをクリックします。

1. **[構成]** というラベルのセクションで、Azure Sentinel に接続する Office 365 アクティビティ ログのチェック ボックスをオンにし、 **[変更を適用]** をクリックします。 

   > [!NOTE]
   > 以前に複数のテナントを Azure Sentinel に接続し、その際にこれをサポートする古いバージョンの Office 365 コネクタを使用した場合は、各テナントから収集するログを表示して変更することができます。 テナントをさらに追加することはできませんが、以前に追加したテナントを削除することはできます。

1. Log Analytics で Office 365 のログ データに対してクエリを実行するには、クエリ ウィンドウの最初の行に「`OfficeActivity`」と入力します。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Office 365 を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [組み込み](tutorial-detect-threats-built-in.md)または[カスタム](tutorial-detect-threats-custom.md)のルールを使用して、Azure Sentinel で脅威の検出を開始する。

