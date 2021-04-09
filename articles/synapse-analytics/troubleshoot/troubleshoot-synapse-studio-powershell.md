---
title: Synapse Studio の接続についてのトラブルシューティング
description: PowerShell を使用した Azure Synapse Studio 接続のトラブルシューティング
author: saveenr
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 6ff3d985bb24ec852bb5c6cfaedf295fd79a8247
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98120361"
---
# <a name="troubleshoot-synapse-studio-connectivity-with-powershell"></a>PowerShell を使用した Synapse Studio 接続のトラブルシューティング

Azure Synapse Studio の正常な動作は、一連の Web API エンドポイントに依存しています。 このガイドは、次のような場合に接続の問題の原因を特定するのに役立ちます。
- Azure Synapse Studio にアクセスするためのローカル ネットワーク (企業のファイアウォールの内側にあるネットワークなど) を構成しようとしている。
- Azure Synapse Studio を使用した接続の問題が発生している。

## <a name="prerequisite"></a>前提条件

* PowerShell 5.0 以降のバージョン (Windows の場合)、または
* PowerShell Core 6.0 以降のバージョン (Windows または Linux の場合)。

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順

次のリンクを右クリックし、[対象をファイルに保存] を選択します。

- [Test-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

または、リンクを直接開いて、開いたスクリプト ファイルを保存することもできます。 今後変更される可能性があるため、上記のリンクのアドレスは保存しないでください。

エクスプローラーで、ダウンロードしたスクリプト ファイルを右クリックし、[PowerShell で実行] を選択します。

![ダウンロードしたスクリプト ファイルを PowerShell で実行する](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

プロンプトが表示されたら、現在問題が発生している、または接続をテストする Azure Synapse ワークスペースの名前を入力し、Enter キーを押します。

![ワークスペース名を入力する](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

診断セッションが開始されます。 完了するまで待ちます。

![診断の完了を待機する](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

最後に、診断の概要が表示されます。 PC から 1 つ以上のエンドポイントに接続できない場合は、"Summary" (概要) セクションにいくつかの提案が表示されます。

![診断ログを確認する](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

さらに、このセッションの診断ログ ファイルが、トラブルシューティング スクリプトと同じフォルダーに生成されます。 その場所は、"General tips" (一般的なヒント) セクション (`D:\TestAzureSynapse_2020....log`) に示されています。 必要に応じて、このファイルをテクニカル サポートに送信することができます。

ネットワーク管理者が Azure Synapse Studio のファイアウォール構成を調整している場合は、"Summary" (概要) セクションの上に表示されている技術的な詳細が役に立つことがあります。

* "Passed" (成功) とマークされたすべてのテスト項目 (要求) は、HTTP 状態コードに関係なく、接続テストに合格したことを意味します。
 失敗した要求については、`NamedResolutionFailure` や `ConnectFailure` などの理由が黄色で表示されます。 これらの理由は、ネットワーク環境に誤った構成があるかどうかを判断するのに役立ちます。


## <a name="next-steps"></a>次のステップ
前の手順で問題が解決しない場合は、[サポート チケットを作成](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md)してください。