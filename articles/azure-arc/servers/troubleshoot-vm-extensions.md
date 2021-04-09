---
title: Azure Arc 対応サーバーの VM 拡張機能に関する問題のトラブルシューティング
description: この記事では、Azure Arc 対応サーバーで発生する Azure VM 拡張機能に関する問題をトラブルシューティングして解決する方法について説明します。
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: 799e5c8ec00a894c6a54c64494edd8f259faf2dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100584665"
---
# <a name="troubleshoot-arc-enabled-servers-vm-extension-issues"></a>Arc 対応サーバーの VM 拡張機能に関する問題のトラブルシューティング

この記事では、Arc 対応サーバーに Azure VM 拡張機能をデプロイまたは削除しようとしたときに発生するおそれがある問題のトラブルシューティングと解決に関する情報について説明します。 一般的な情報については、[Azure VM 拡張機能の管理と使用](./manage-vm-extensions.md)に関するページを参照してください。

## <a name="general-troubleshooting"></a>一般的なトラブルシューティング

拡張機能のデプロイの状態に関するデータは、Azure portal で取得できます。

次のトラブルシューティング手順は、すべての VM 張機能に適用されます。

1. ゲスト エージェント ログを調べるには、拡張機能がプロビジョニングされたときのアクティビティを `%SystemDrive%\ProgramData\GuestConfig\ext_mgr_logs` (Windows の場合) および `/var/lib/GuestConfig/ext_mgr_logs` (Linux の場合) で確認します。

2. Windows の場合、特定の拡張機能の詳細な拡張機能ログは `%SystemDrive%\ProgramData\GuestConfig\extension_logs\<Extension>` で確認できます。 Linux の場合、拡張機能の出力は、インストールされる拡張機能ごとに `/var/lib/GuestConfig/extension_logs` のファイルに記録されます。

3. エラーコードや既知の問題などについて、拡張機能固有のドキュメントのトラブルシューティングのセクションを確認します。各拡張機能の追加のトラブルシューティング情報については、拡張機能の概要の「**トラブルシューティングとサポート**」セクションを参照してください。 これには、ログに書き込まれたエラー コードの説明が含まれます。 拡張機能の記事は、[拡張機能の表](manage-vm-extensions.md#extensions)にリンクされています。

4. システム ログを確認します。 排他的なパッケージ マネージャーのアクセスを必要とする別のアプリケーションのインストールが長時間実行されている場合など、拡張機能に干渉する可能性のある他の操作をチェックします。

## <a name="troubleshooting-specific-extension-scenarios"></a>特定の拡張シナリオのトラブルシューティング

### <a name="vm-insights"></a>VM Insights

- Azure Arc 対応サーバーで VM Insights を有効にすると、Dependency and Log Analytics エージェントがインストールされます。 低速の、あるいは低速のネットワークに接続しているマシンの場合、インストール プロセス中にタイムアウトが発生することがあります。 Microsoft は、この状態を改善するために、Connected Machine エージェントでこの問題に対処する措置を講じています。 暫定的に、インストールの再試行が成功する場合があります。

### <a name="log-analytics-agent-for-linux"></a>Linux 用 Log Analytics エージェント

- Log Analytics エージェント バージョン 1.13.9 (対応する拡張機能バージョンは 1.13.15) によって、アップロードされたデータが Azure Arc 対応サーバーのリソース ID を使用して正しくマークされていません。 ログはサービスに送信されますが、選択した対応サーバーのデータを表示しようとして **[ログ]** または **[分析情報]** を選択した場合、データは返されません。 ワークスペースにスコープが設定されている Azure Monitor Logs または Azure Monitor for VMs からクエリを実行することで、データを表示できます。

- 現在、一部のディストリビューションは、Linux 用 Log Analytics エージェントでサポートされていません。 エージェントには、Python 2 などの追加の依存関係をインストールする必要があります。 [こちら](../../azure-monitor/agents/agents-overview.md#supported-operating-systems)のサポート マトリックスと前提条件を確認してください。

- ステータス メッセージのエラー コード 52 は、不足している依存関係があることを示しています。 不足している依存関係に関する詳細情報を出力とログで確認します。

- インストールが失敗した場合は、拡張機能の概要に関するページの **トラブルシューティングとサポート** のセクションを確認してください。 ほとんどの場合、ステータス メッセージにはエラー コードが含まれています。 Linux 用 Log Analytics エージェントの場合、ステータス メッセージとこの VM 拡張機能の一般的なトラブルシューティング情報については、[こちら](../../virtual-machines/extensions/oms-linux.md#troubleshoot-and-support)を参照してください。

## <a name="next-steps"></a>次のステップ

該当する問題がここにない場合、または問題を解決できない場合は、追加のサポートを受けるために、次のいずれかのチャネルをお試しください。

- [Microsoft Q&A](/answers/topics/azure-arc.html) を通じて、Azure 専門家からの回答を得ることができます。

- [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure サポートにより、Azure コミュニティの回答、サポート、エキスパートと結び付けられます。

- Azure サポート インシデントを送信する。 [Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、 **[サポートを受ける]** を選択します。
