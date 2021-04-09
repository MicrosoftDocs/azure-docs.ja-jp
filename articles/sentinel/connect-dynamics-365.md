---
title: Dynamics 365 のログを Azure Sentinel に接続する | Microsoft Docs
description: Dynamics 365 Common Data Service (CDS) アクティビティ コネクタを使用して、管理者、ユーザー、サポートの実行中のアクティビティに関する情報を取得する方法について説明します。
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
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: 018368b6284cf39edec01f0a9a943b8ea15c85d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98103884"
---
# <a name="connect-dynamics-365-activity-logs-to-azure-sentinel"></a>Dynamics 365 アクティビティ ログを Azure Sentinel に接続する

[Dynamics 365](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description) Common Data Service (CDS) アクティビティ コネクタは、管理者、ユーザー、サポートのアクティビティ、および Microsoft Social Engagement のログ イベントに関する分析情報を提供します。 Dynamics 365 CRM のログを Azure Sentinel に接続すると、このデータをブックで表示したり、それを使用してカスタム アラートを作成したり、それを活用して調査プロセスを改善したりすることができます。 この新しい Azure Sentinel コネクタは、Office Management API から Dynamics CDS データを収集します。 Power Platform で監査される Dynamics CDS アクティビティの詳細については、[アクティビティ ログの有効化と使用](/power-platform/admin/enable-use-comprehensive-auditing)に関するページを参照してください。

> [!IMPORTANT]
>
> Dynamics 365 Common Data Service (CDS) アクティビティ コネクタは、現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- [Microsoft Dynamics 365 の運用ライセンス](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description)が必要です。 このコネクタは、サンドボックス環境では使用できません。
    - アクティビティのログ記録を行うには、Microsoft 365 Enterprise [E3 または E5](/power-platform/admin/enable-use-comprehensive-auditing#requirements) サブスクリプションが必要です。

- Office Management API からデータを取得するには:
    - テナントの全体管理者である必要があります。

    - [Office セキュリティ/コンプライアンス センター](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance)で [Office 監査ログ](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center)を有効にする必要があります。

## <a name="enable-the-dynamics-365-activities-data-connector"></a>Dynamics 365 アクティビティ データ コネクタを有効にする

1. Azure Sentinel のナビゲーション メニューから、 **[Data connectors]\(データ コネクタ\)** を選択します。

1. **データ コネクタ** ギャラリーから、 **[Dynamics 365 (Preview)]\(Dynamics 365 (プレビュー)\)** を選択し、プレビュー ペインで **[コネクタ ページを開く]** を選択します。

1. **[手順]** タブの **[構成]** で **[接続]** をクリックします。 

    コネクタがアクティブになった後、到着したデータをグラフで表示できるようになるまで約 30 分かかります。 

    [コンプライアンス センターの Office 監査ログ](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#requirements-to-search-the-audit-log)によっては、イベントが発生した後、該当する監査ログ レコードが結果に返されるまでに、最大 30 分または最大 24 時間かかることがあります。

1. Microsoft Dynamics の監査ログは、`Dynamics365Activity` テーブルにあります。 テーブルの[スキーマ リファレンス](/azure/azure-monitor/reference/tables/dynamics365activity)を参照してください。

## <a name="querying-the-data"></a>データのクエリ

1. Azure Sentinel のナビゲーション メニューから **[ログ]** を選択します。

1. 次のクエリを実行して、ログが到着したことを確認します。

    ```kusto
    Dynamics365Activity
    | take 10
    ```


## <a name="next-steps"></a>次のステップ
このドキュメントでは、Dynamics 365 のアクティビティ データを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [組み込み](tutorial-detect-threats-built-in.md)または[カスタム](tutorial-detect-threats-custom.md)のルールを使用して、Azure Sentinel で脅威の検出を開始する。
