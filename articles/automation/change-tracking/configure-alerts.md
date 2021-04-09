---
title: Azure Automation の変更履歴とインベントリのアラートを作成する方法
description: この記事では、変更履歴とインベントリによって検出された変更の状態について通知するように Azure アラートを構成する方法について説明します。
services: automation
ms.subservice: change-inventory-management
ms.date: 10/15/2020
ms.topic: conceptual
ms.openlocfilehash: 91be2f8641a061d009962cdcd03a8d56048594da
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594506"
---
# <a name="how-to-create-alerts-for-change-tracking-and-inventory"></a>変更履歴とインベントリのアラートを作成する方法

Azure のアラートでは、Runbook ジョブの結果、サービス正常性の問題、または Automation アカウントに関連するその他のシナリオが事前に通知されます。 Azure Automation には事前に構成されたアラート ルールは含まれませんが、生成されるデータに基づいて独自のものを作成することができます。 この記事では、変更履歴とインベントリによって特定された変更に基づいてアラート ルールを作成するためのガイダンスを示します。

Azure Monitor のアラートに詳しくない場合は、事前に「[Microsoft Azure のアラートの概要](../../azure-monitor/alerts/alerts-overview.md)」を参照してください。 ログ クエリを使用したアラートの詳細については、「[Azure Monitor でのログ アラート](../../azure-monitor/alerts/alerts-unified-log.md)」を参照してください。

## <a name="create-alert"></a>アラートを作成する

次の例は、マシン上でファイル **c:\windows\system32\drivers\etc\hosts** が変更されたことを示しています。 このファイルは、Windows がホスト名を IP アドレスに解決するために使用するため、重要です。 この操作は DNS より優先されるため、接続の問題が発生する可能性があります。 また、悪意のある、またはその他の危険な Web サイトにトラフィックがリダイレクトされる可能性もあります。

![hosts ファイルの変更を示すグラフ](./media/configure-alerts/changes.png)

次の例を使用して、変更に関するアラートを作成する手順について説明します。

1. Automation アカウントの **[変更履歴]** ページで、 **[Log Analytics]** を選択します。

2. ログ検索で、クエリ `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` を使用して、**hosts** ファイルに対するコンテンツ変更を検索します。 このクエリでは、完全修飾パス名に `hosts` という単語が含まれているファイルのコンテンツ変更を検索します。 パスの部分を完全修飾の形式に変更する (たとえば、`FileSystemPath == "c:\windows\system32\drivers\etc\hosts"` を使用する) ことで、特定のファイルを要求することもできます。

3. クエリからその結果が返されたら、ログ検索で **[新しいアラート ルール]** を選択し、 **[Alert creation]\(アラートの作成\)** ページを開きます。 Azure portal で **Azure Monitor** を使用してこのページに移動することもできます。

4. もう一度クエリを確認し、警告ロジックを変更してください。 この場合は、環境内のすべてのマシンで 1 つでも変更が検出されたら、アラートがトリガーされるようにします。

    ![hosts ファイルに対する変更を追跡するためのクエリへの変更](./media/configure-alerts/change-query.png)

5. アラート ロジックを設定したら、アラートのトリガーに応じてアクションを実行するアクション グループを割り当てます。 この場合は、送信する電子メールと、作成する IT サービスマネジメント (ITSM) チケットを設定します。

更新プログラムの展開の状態を通知するアラートを設定するには、次の手順のようにします。 Azure アラートを初めて使用する場合は、[Azure アラートの概要](../../azure-monitor/alerts/alerts-overview.md)に関するページを参照してください。

## <a name="configure-action-groups-for-your-alerts"></a>アラートにアクション グループを構成する

アラートを構成したら、アクション グループを設定できます。これは複数のアラートに対して使用するアクションのグループです。 このアクションには、メール通知、Runbook、Webhook など多くのものを含めることができます。 アクション グループの詳細については、[アクション グループの作成および管理](../../azure-monitor/alerts/action-groups.md)に関するページを参照してください。

1. アラートを選択し、 **[アクション グループ]** で **[新規作成]** を選択します。

2. アクション グループの完全な名前と短い名前を入力します。 Update Management では、指定のグループを使用して通知を送信する場合に短縮名を使用します。

3. **[アクション]** で、アクションを指定する名前 (**電子メール通知** など) を入力します。

4. **[アクションの種類]** には、適切な種類 (**メール、SMS メッセージ、プッシュ、音声** など) を選択します。

5. 鉛筆アイコンを選択してアクションの詳細を編集します。

6. アクションの種類のペインに入力します。 たとえば、**メール、SMS メッセージ、プッシュ、音声** を使用して電子メールを送る場合は、アクション名を入力し、 **[電子メール]** チェックボックスをオンにして、有効なメール アドレスを入力し、 **[OK]** を選択します。

    ![電子メール アクション グループの構成](./media/configure-alerts/configure-email-action-group.png)

7. [アクション グループの追加] ペインで、 **[OK]** を選択します。

8. アラート メールでは、メールの件名をカスタマイズできます。 **[ルールの作成]** で **[アクションをカスタマイズする]** を選択し、 **[メールの件名]** を選択します。

9. 完了したら、 **[アラート ルールの作成]** を選択します。

## <a name="next-steps"></a>次のステップ

* [Azure Monitor のアラート](../../azure-monitor/alerts/alerts-overview.md)について、さらに詳しく学習します。

* Log Analytics ワークスペースからデータを取得して分析する[ログ クエリ](../../azure-monitor/logs/log-query-overview.md)について説明します。

* 「[Azure Monitor ログで使用量とコストを管理する](../../azure-monitor/logs/manage-cost-storage.md)」では、データ保持期間を変更してコストを管理する方法、およびデータの使用状況を分析してアラートを作成する方法について説明します。