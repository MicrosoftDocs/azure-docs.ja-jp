---
title: Windows Virtual Desktop 向けの Monitor (プレビュー) のトラブルシューティング - Azure
description: Windows Virtual Desktop 向けの Azure Monitor に関するイシューのトラブルシューティング方法。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c335c1cf7e5319b812345714dbdc6b87ddc4e81b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709174"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop-preview"></a>Windows Virtual Desktop 向けの Azure Monitor (プレビュー) のトラブルシューティング

>[!IMPORTANT]
>Windows Virtual Desktop 向けの Azure Monitor は現在、パブリック プレビュー段階です。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、Windows Virtual Desktop 向けの Azure Monitor (プレビュー) の既知のイシューと、一般的な問題の解決策について説明します。

## <a name="issues-with-configuration-and-setup"></a>構成と設定に関する問題

構成ブックで設定の自動化が正常に動作していない場合は、これらのリソースを使用してご自身の環境を手動で設定できます。

- 診断を手動で有効にするか、Log Analytics ワークスペースにアクセスする場合は、[Log Analytics への Windows Virtual Desktop 診断の送信](diagnostics-log-analytics.md)に関する記事をご覧ください。
- Log Analytics 拡張機能をホストに手動でインストールする場合は、「[Windows 用の Log Analytics 仮想マシン拡張機能](../virtual-machines/extensions/oms-windows.md)」をご覧ください。
- 新しい Log Analytics ワークスペースを設定する場合は、「[Azure portal で Log Analytics ワークスペースを作成する](../azure-monitor/logs/quick-create-workspace.md)」をご覧ください。
- パフォーマンス カウンターを追加または削除する場合は、「[パフォーマンス カウンターの構成](../azure-monitor/agents/data-sources-performance-counters.md)」をご覧ください。
- Log Analytics ワークスペースのイベントを構成する場合は、「[Log Analytics エージェントを使用して Windows イベント ログのデータソースを収集する](../azure-monitor/agents/data-sources-windows-events.md)」をご覧ください。

## <a name="my-data-isnt-displaying-properly"></a>自分のデータが正しく表示されない

データが正しく表示されない場合は、構成とアクセス許可を確認し、必要な IP アドレスのブロックが解除されていることを確認してください。 

- まず、「[Windows Virtual Desktop 向けの Azure Monitor を使用してデプロイを監視する](azure-monitor.md)」の説明に従って、構成ブックのすべてのフィールドを入力していることを確認します。 カウンターまたはイベントに入力されていない場合は、それらに関連付けられているデータが Azure portal に表示されません。

- 自分のアクセス許可を確認し、リソース所有者に連絡して不足しているアクセス許可を要請してください。Windows Virtual Desktop を監視するすべてのユーザーには、次のアクセス許可が必要です。

    - Windows Virtual Desktop リソースを保持する Azure サブスクリプションへの読み取りアクセス
    - Windows Virtual Desktop セッション ホストを保持するサブスクリプションのリソース グループへの読み取りアクセス 
    - Log Analytics ワークスペースへの読み取りアクセス

- Azure Monitor がポータルにデータを送信できるように、お使いのサーバーのファイアウォールで送信ポートを開く必要がある場合があります。[送信ポート](../azure-monitor/app/ip-addresses.md)に関する記事を参照してください。 

- 最近のアクティビティのデータが表示されない場合。 15 分間待機して、フィードを更新することをお勧めします。 Azure Monitor にはログ データの入力に 15 分の待機時間があります。 詳細については、「[Azure Monitor でのログ データ インジェスト時間](../azure-monitor/logs/data-ingestion-time.md)」をご覧ください。

情報がすべて入力されていても、データが正しく表示されない場合は、クエリまたはデータソースに問題があるおそれがあります。 既知の問題と制限事項を確認してください。 

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Windows Virtual Desktop 向けの Azure Monitor をカスタマイズしたい

Windows Virtual Desktop 向けの Azure Monitor は、Azure Monitor ブックを使用します。 ブックを使用すると、Windows Virtual Desktop ブック テンプレートのコピーを保存して、独自のカスタマイズを行うことができます。

仕様により、カスタム ブック テンプレートでは、製品グループの更新プログラムが自動的に適用されることはありません。 詳細については、「[ブックベースの分析情報のトラブルシューティング](../azure-monitor/insights/troubleshoot-workbooks.md)」および[ブックの概要](../azure-monitor/visualize/workbooks-overview.md)に関するページをご覧ください。

## <a name="i-cant-interpret-the-data"></a>データを解釈できない

データの用語の詳細については、「[Windows Virtual Desktop 向けの Azure Monitor の用語集](azure-monitor-glossary.md)」をご覧ください。

## <a name="the-data-i-need-isnt-available"></a>必要なデータを使用できない

より多くのパフォーマンス カウンターまたはイベントを監視する場合は、それらを自分の Log Analytics ワークスペースに送信できるようにして、[ホスト診断:ホスト ブラウザー] で監視できます。 

- パフォーマンス カウンターを追加するには、「[パフォーマンス カウンターの構成](../azure-monitor/agents/data-sources-performance-counters.md#configuring-performance-counters)」を参照してください
- Windows イベントを追加するには、「[Windows イベント ログの構成](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)」を参照してください

イシューの診断に役立つデータ ポイントが見つかりませんか。 フィードバックをお送りください。

- フィードバックを残す方法については、「[Windows Virtual Desktop のトラブルシューティングの概要、フィードバック、およびサポート](troubleshoot-set-up-overview.md)」をご覧ください。
- また、Windows Virtual Desktop についてのフィードバックは、[Windows Virtual Desktop フィードバック ハブ](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)、または [UserVoice フォーラム](https://windowsvirtualdesktop.uservoice.com/forums/921118-general)にお寄せいただくこともできます。

## <a name="known-issues-and-limitations"></a>既知の問題と制限事項

これらは、現在認識され、修正に向けて対処中の問題と制限事項です。

- 一度に監視できるホスト プールは 1 つのみです。 

- お気に入りの設定を保存するには、ブックのカスタム テンプレートを保存する必要があります。 カスタム テンプレートでは、製品グループの更新プログラムが自動的に適用されることはありません。

- 一部のエラー メッセージはユーザーにとってわかりやすい言い回しではありません。また、すべてのエラー メッセージがドキュメントに記載されているわけではありません。

- 合計セッション パフォーマンス カウンターでは、セッションがわずかにオーバーカウントされることがあり、セッションの合計数が最大セッション数を超えているように見える場合があります。

- 使用可能なセッション数には、ホスト プールのスケーリング ポリシーは反映されません。 
    
- まれに、接続の完了イベントが失われる可能性があります。これは、経時的な接続やユーザーの接続状態などの一部の表示に影響を与える可能性があります。  
    
- 構成ブックでは、そのリソース グループと同じリージョン内でのホストの構成のみがサポートされます。 

- 接続までの時間には、ユーザーが資格情報を入力するのにかかる時間が含まれます。これは経験と相関しますが、場合によっては、偽のピークが表示されることがあります。 
    

## <a name="next-steps"></a>次のステップ

データの解釈方法がわからない場合や、一般的な用語の詳細については、「[Windows Virtual Desktop 向けの Azure Monitor の用語集](azure-monitor-glossary.md)」をご覧ください。 Windows Virtual Desktop 向けの Azure Monitor を設定して使用する方法については、「[Windows Virtual Desktop 向けの Azure Monitor を使用してデプロイを監視する](azure-monitor.md)」をご覧ください。