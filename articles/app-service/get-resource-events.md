---
title: Azure App Service でリソース イベントを取得する
description: App Service アプリのアクティビティ ログと Event Grid を通じてリソース イベントを取得する方法を学習します。
ms.topic: article
ms.date: 04/24/2020
ms.author: msangapu
ms.openlocfilehash: c20028a4f84dae9d292cf855a1e164bd69864909
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574046"
---
# <a name="get-resource-events-in-azure-app-service"></a>Azure App Service でリソース イベントを取得する

Azure App Service には、お使いのリソースの状態と正常性を監視するための組み込みツールが用意されています。 リソース イベントは、基になる Web アプリのリソースに加えられた変更を理解し、必要に応じてアクションを実行するのに役立ちます。 イベントの例としては、インスタンスのスケーリング、アプリケーション設定の更新、Web アプリの再起動などがあります。 この記事では、[Azure アクティビティ ログ](../azure-monitor/essentials/activity-log.md#view-the-activity-log)を表示する方法と、[Event Grid](../event-grid/index.yml) を使用して App Service Web アプリに関連するリソース イベントを監視する方法について学習します。

> [!NOTE]
> App Service と Event Grid の統合は **プレビュー** 段階です。 [詳細については、お知らせをご覧ください。](https://aka.ms/app-service-event-grid-announcement)
>

## <a name="view-azure-activity-logs"></a>Azure アクティビティ ログを表示する
Azure アクティビティ ログには、お使いのサブスクリプションのリソースに対して実行された操作によって生成されたリソース イベントが含まれます。 Azure portal と Azure Resource Manager テンプレートの両方のユーザー アクションに基づいて、イベントがアクティビティ ログにキャプチャされます。 

App Service の Azure アクティビティ ログには、次のような詳細が含まれます。
- リソースに対して実行された操作 (例: App Service プラン)
- 操作を開始したユーザー
- 操作が発生した時間
- 操作の状態
- 操作を調査するのに役立つプロパティ値

### <a name="what-can-you-do-with-azure-activity-logs"></a>Azure アクティビティ ログでできること

Azure アクティビティ ログは、Azure portal、PowerShell、REST API、または CLI を使用して照会できます。 ログは、ストレージ アカウント、Event Hub、および Log Analytics に送信できます。 また、Power BI でログを分析することも、リソース イベントに関する最新の状態を取得するためのアラートを作成することもできます。

[Azure アクティビティ ログ イベントを表示および取得する](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

## <a name="ship-activity-logs-to-event-grid"></a>アクティビティ ログを Event Grid に送信する

アクティビティ ログはユーザーベースですが、ユーザー アクションと自動イベントの両方をログに記録する App Service (プレビュー) との新しい [Event Grid](../event-grid/index.yml) 統合を利用できます。 Event Grid を使用すると、前記のイベントに反応するハンドラーを構成できます。 たとえば、Event Grid を使用すると、Blob Storage コンテナーに新しい写真が追加されるたびに画像分析を実行するサーバーレス関数を即時トリガーできます。

また、Event Grid を Logic Apps と共に使用して、コードを作成することなく、場所を問わずにデータを処理することもできます。 Event Grid はデータ ソースとイベント ハンドラーを接続します。 たとえば、Event Grid を使用すると、Blob Storage コンテナーに新しい写真が追加されるたびに画像分析を実行するサーバーレス関数を即時トリガーできます。

[Azure App Service イベントのプロパティとスキーマを確認する。](../event-grid/event-schema-app-service.md)

## <a name="next-steps"></a><a name="nextsteps"></a> 次のステップ
* [Azure Monitor でログにクエリを実行する](../azure-monitor/logs/log-query-overview.md)
* [Azure App Service を監視する方法](web-sites-monitor.md)
* [Visual Studio での Azure App Service のトラブルシューティング](troubleshoot-dotnet-visual-studio.md)
* [HDInsight でのアプリ ログの分析](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)