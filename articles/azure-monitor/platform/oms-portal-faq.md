---
title: OMS ポータルから Azure portal への切り替えに関する Log Analytics ユーザーの一般的な質問 | Microsoft Docs
description: OMS ポータルから Azure portal への切り替えについて、Log Analytics ユーザーから多く寄せられる質問の回答を示します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: bwren
ms.openlocfilehash: 6b76249c85f9b8c64435043a75e39601d445ab0c
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384719"
---
# <a name="common-questions-for-transition-from-oms-portal-to-azure-portal-for-log-analytics-users"></a>OMS ポータルから Azure portal への切り替えに関する Log Analytics ユーザーの一般的な質問
当初、Log Analytics では、その構成を管理したり収集されたデータを分析したりすることを目的とした "OMS ポータル" と呼ばれる独自のポータルが使用されていました。  このポータルの機能はすべて Azure portal に移行され、今後はそちらで開発が継続されることになります。

この記事では、この切り替えに関してユーザーからよく寄せられる質問に回答しています。  OMS ポータルで Log Analytics を使用していた方は、同じ作業を Azure portal で実行する方法についての質問の回答をこちらでご覧いただけます。

## <a name="do-i-need-to-migrate-anything"></a>何を移行する必要がありますか。
いいえ。 Log Analytics 自体は何も変更されていないため、移行する必要はありません。 変更されているのは、アクセスに使用するインターフェイスだけです。 実際には、今でも Azure portal を使用して、現在 OMS ポータルで使用しているのと同じワークスペース、ソリューション、ビュー、ログ検索にアクセスすることができます。

## <a name="where-do-i-find-log-analytics-in-azure"></a>Log Analytics は Azure のどこにあるのですか?
Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にログインします。  **[すべてのサービス]** をクリックし、リソースの一覧で「**Log Analytics**」と入力します。 **[Log Analytics]** を選択し、ワークスペースを選択します。 ワークスペースのサマリー ページが表示されます。

![Log Analytics ワークスペース](media/oms-portal-faq/log-analytics.png)

## <a name="how-do-i-manage-permissions"></a>アクセス許可の管理方法を教えてください
Azure portal における Log Analytics ワークスペースへのアクセス権がない場合は、[Azure のロールベースのアクセス](../../role-based-access-control/role-assignments-portal.md)を使用してアクセス許可を構成する必要があります。 ワークスペースのアクセス許可の管理について詳しくは、[ワークスペースの管理](manage-access.md#manage-accounts-and-users)に関するページを参照してください。 アラートに関するアクセス許可の管理については、「[Azure Monitor での役割、アクセス許可、およびセキュリティの使用](../../azure-monitor/platform/roles-permissions-security.md)」を参照してください。

## <a name="how-do-i-create-a-new-workspace"></a>新しいワークスペースの作成方法を教えてください 
Azure portal でワークスペースの一覧から、ワークスペースの一覧の **[追加]** をクリックします。  詳細については、「[Azure portal で Log Analytics ワークスペースを作成する](../learn/quick-create-workspace.md)」を参照してください。

![[概要] ページ](media/oms-portal-faq/new-workspace.png)

## <a name="where-is-my-overview-page"></a>概要ページはどこにあるのですか?
OMS ポータルのメイン画面には、ワークスペースにインストールされているすべての管理ソリューションのタイルのほか、自分で作成したカスタム ビューが表示されます。 Azure Portal でこの同じビューを利用できます。 ワークスペースから、**[Workspace Summary]**(ワークスペースの概要) を選択します。

![[概要] ページ](media/oms-portal-faq/overview.png)

## <a name="how-do-i-open-log-search-and-view-designer"></a>ログ検索とビュー デザイナーの開き方を教えてください
Azure portal では、**ログ検索**と**ビュー デザイナー**のどちらも、メイン ページからアクセスできるほか、ワークスペースの左側 (**[概要]** のすぐ横) のメニューからアクセスできます。

## <a name="where-do-i-find-settings"></a>一連の設定はどこにありますか?
OMS ポータルの **[設定]** セクションにあった設定の多くは、Azure portal ではワークスペースの **[詳細設定]** メニューから利用できます。

![詳細設定](media/oms-portal-faq/advanced-settings.png)

以前 OMS ポータルの **[設定]** セクションにあった各設定へのアクセス方法については、以降のセクションで詳しく取り上げます。

### <a name="accounts"></a>アカウント 
アカウント設定は、Azure portal 内のいくつかの場所で管理します。次の表を参照してください。

| OMS ポータル内の設定 | 対応する Azure portal 内の場所 |
|:---|:---|
| Automation アカウント | ワークスペースの **[Automation アカウント]** メニュー。 |
| [Azure サブスクリプション] と [データ プラン] | ワークスペースの **[価格レベル]** メニュー。 |
| ユーザーの管理 | [ワークスペースのアクセス許可の管理](#how-do-i-manage-permissions)には、Azure のロールベースのアクセスを使用します。 |
| ワークスペース情報 | ワークスペースの情報には **[OMS ワークスペース]** メニューからアクセスできます。 |

### <a name="alerts"></a>アラート
Log Analytics のクエリに基づくアラート ルールは現在、[一元化されたアラート機能](#how-do-i-create-and-manage-alerts)で管理するようになっています。 

### <a name="computer-groups"></a>コンピューター グループ
コンピューター グループは、ワークスペースの **[詳細設定]** メニューで管理します。 

### <a name="connected-sources"></a>接続先ソース
[接続されているソース] の設定の大半は、ワークスペースの **[詳細設定]** メニューで管理します。 次の表は、このメニューの各セクションの詳細を示しています。

| OMS ポータル内の設定 | 対応する Azure portal 内の場所 |
|:---|:---|
| Windows サーバー   | ワークスペースの **[詳細設定]** メニュー。 |
| Linux サーバー   | ワークスペースの **[詳細設定]** メニュー。 |
| Azure Storage     | ワークスペースの **[詳細設定]** メニュー。 |
| System Center     | ワークスペースの **[詳細設定]** メニュー。 |
| Office 365        | 構成の詳細については、[Office 365 管理ソリューションのドキュメント](../insights/solution-office-365.md)を参照してください。 |
| Windows のテレメトリ | ソリューションの [設定] メニュー。 構成の詳細については、[Azure portal での Windows Analytics](/windows/deployment/update/windows-analytics-azure-portal) に関するページを参照してください。 |
| ITSM Connector    | ITSM サービスを Log Analytics に接続する手順については、「[ITSM 製品/サービスを IT Service Management Connector に追加する](itsmc-connections.md)」を参照してください。 |

### <a name="data"></a>データ
[データ] の設定の大半は、ワークスペースの **[詳細設定]** メニューで管理します。 次の表は、このメニューの各セクションの詳細を示しています。

| OMS ポータル内の設定 | 対応する Azure portal 内の場所 |
|:---|:---|
| Windows イベント ログ           | ワークスペースの **[詳細設定]** メニュー。 |
| Windows パフォーマンス カウンター | ワークスペースの **[詳細設定]** メニュー。 |
| Linux パフォーマンス カウンター   | ワークスペースの **[詳細設定]** メニュー。 |
| IIS ログ                     | ワークスペースの **[詳細設定]** メニュー。 |
| カスタム フィールド                | ワークスペースの **[詳細設定]** メニュー。 |
| カスタム ログ                  | ワークスペースの **[詳細設定]** メニュー。 |
| syslog                       | ワークスペースの **[詳細設定]** メニュー。 |
| Application Insights | Log Analytics と Application Insights が同じデータ エンジンを共有するようになったことから、このソリューションは非推奨となっています。 |
| Windows ファイルの追跡        | Azure Automation の **[変更追跡]** メニュー。 詳細については、「[Change Tracking ソリューションを使用してユーザーの環境内の変更を追跡する](../../automation/automation-change-tracking.md)」を参照してください。 |
| Windows レジストリの追跡        | Azure Automation の **[変更追跡]** メニュー。 詳細については、「[Change Tracking ソリューションを使用してユーザーの環境内の変更を追跡する](../../automation/automation-change-tracking.md)」を参照してください。 |
| Linux ファイルの追跡          | Azure Automation の **[変更追跡]** メニュー。 詳細については、「[Change Tracking ソリューションを使用してユーザーの環境内の変更を追跡する](../../automation/automation-change-tracking.md)」を参照してください。 |

### <a name="solutions"></a>解決方法
ワークスペースの **[ソリューション]** メニューでソリューションを管理します。 

## <a name="how-do-i-install-and-remove-management-solutions"></a>管理ソリューションのインストールと削除の方法を教えてください
OMS ポータルでは、ソリューション ギャラリーから管理ソリューションをインストールし、その削除は **[設定]** から行います。 Azure portal では、[管理ソリューションのインストール](../insights/solutions.md#install-a-management-solution)を Azure Marketplace から行います。 インストールされているソリューションの一覧から[ソリューションを削除](../insights/solutions.md#remove-a-management-solution)してください。

## <a name="how-do-i-create-and-manage-alerts"></a>アラートの作成と管理の方法を教えてください
Log Analytics のクエリに基づくアラート ルールは現在、[一元化されたアラート機能](../../azure-monitor/platform/alerts-metric.md)で管理するようになっています。 Azure portal でのアラートの構成と使用について詳しくは、[Log Analytics から Azure にアラートを拡張する方法](../../azure-monitor/platform/alerts-extend-tool.md)に関するページを参照してください。

## <a name="how-do-i-access-my-dashboards"></a>ダッシュボードへのアクセス方法を教えてください
Log Analytics の [[マイ ダッシュボード]](dashboards.md) 機能は非推奨になりました。 この機能では、[ビュー デザイナー] 部分の個人のコレクションを利用できました。後継は、組み込みの Azure ダッシュボード機能になります。 共有ビューには [[ビュー デザイナー]](view-designer.md) を使用して、Log Analytics のデータを引き続き視覚化できます。 これらのビューからの視覚表現や、Azure ダッシュボードへの[個々のクエリ](../learn/tutorial-logs-dashboards.md)に対する視覚表現を固定することも可能です。

## <a name="how-do-i-check-my-usage"></a>使用量をチェックするにはどうすればよいですか?
Log Analytics の使用量とコストは、ワークスペースから **[使用量と推定コスト]** を選択して簡単に表示、管理できます。

![使用量と推定コスト](media/oms-portal-faq/usage.png)


## <a name="can-i-still-use-the-classic-portal"></a>クラシック ポータルはまだ使用できるのですか?
限られた期間、 https://\<<実際のワークスペース名>\>.portal.mms.microsoft.com という URL でポータルにアクセスできます。 ぜひ Azure portal をご利用のうえ、障害となっている問題がありましたら、LAUpgradeFeedback@microsoft.com までフィードバックをお寄せください。

## <a name="next-steps"></a>次の手順

- Azure portal を使用して[管理ソリューションの検索とインストール](../insights/solutions.md)を実行します。
- [Azure portal のログ検索](../log-query/portals.md)について確認します。
