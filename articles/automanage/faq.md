---
title: Azure Automanage for virtual machines に関する FAQ
description: Azure Automanage for virtual machines についてよく寄せられる質問に回答します。
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 0b4e116210cf68dc672122ad4ddc98f85067f3b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101688010"
---
# <a name="frequently-asked-questions-for-azure-automanage-for-vms"></a>Azure Automanage for VMs に関してよく寄せられる質問

この記事では、[Azure Automanage for virtual machines](automanage-virtual-machines.md) に関してよく寄せられる質問への回答を提供します。

この記事で Azure の問題に対処できない場合は、[MSDN および Stack Overflow の Azure 関連フォーラム](https://azure.microsoft.com/support/forums/)を参照してください。 問題をこれらのフォーラムまたは [Twitter の @AzureSupport](https://twitter.com/AzureSupport) に投稿できます。 Azure サポート要求を送信することもできます。 サポート リクエストを送信するには、[Azure サポート ページ](https://azure.microsoft.com/support/options/)で **[サポートの利用]** を選択します。


## <a name="azure-automanage-for-virtual-machines"></a>Azure Automanage for virtual machines

**Azure Automanage を有効にするために必要な前提条件は何ですか?**

Azure Automanage を有効にするための前提条件は次のとおりです。
- サポートされている [Windows Server のバージョン](automanage-windows-server.md#supported-windows-server-versions)と [Linux ディストリビューション](automanage-linux.md#supported-linux-distributions-and-versions)
- VM はサポート対象のリージョンに存在する必要があります
- ユーザーは正しいアクセス許可を持っている必要があります
- スケール セット以外の VM のみ
- 現時点では、サンドボックス サブスクリプションは Automanage ではサポートされていません

**Automanage を有効にするために必要な Azure RBAC アクセス許可は何ですか?**

既存の Automanage アカウントを使用して VM で Automanage を有効にする場合は、VM が存在するリソース グループに対する共同作成者ロールが必要です。

有効にするときに新しい Automanage アカウントを使用する場合は、サブスクリプションに対する所有者ロール、または共同作成者とユーザー アクセス管理者のロールが必要です。


**どのリージョンがサポートされていますか?**

サポートされているリージョンの完全な一覧については、[こちら](./automanage-virtual-machines.md#supported-regions)で確認できます。


**Azure Automanage によって自動化されるのはどのような機能ですか?**

Automanage を使用すると、VM のライフサイクル全体を通して、[こちら](automanage-virtual-machines.md)の一覧に記載されているサービスの登録、構成、監視が行われます。

**Azure Automanage は Azure Arc 対応 VM で動作しますか?**

現在、Automanage では、Arc 対応 VM はサポートされていません。

**Azure Automanage の構成をカスタマイズできますか?**

お客様は、構成設定を使用して、Azure Backup の保持期間など、特定のサービスの設定をカスタマイズできます。 変更できる設定の完全な一覧については、[こちら](automanage-virtual-machines.md#customizing-an-environment-using-preferences)のドキュメントを参照してください。


**Azure Automanage は、Linux と Windows 両方の VM で動作しますか?**

はい、サポートされている [Windows Server のバージョン](automanage-windows-server.md#supported-windows-server-versions)と [Linux ディストリビューション](automanage-linux.md#supported-linux-distributions-and-versions)をご確認ください。


**VM のセットに対してだけ Automanage を選択的に適用できますか?**

Automanage は、選択した新規および既存の VM をクリックしてポイントするだけで有効にできます。 また、Automanage はいつでも無効にすることができます。


**Azure Automanage では仮想マシン スケール セット内の VM はサポートされますか?**

いいえ、現在、Azure Automanage では仮想マシン スケール セット内の VM はサポートされていません。


**Azure Automanage のコストはどのくらいですか?**

パブリック プレビューの期間中は、Azure Automanage を追加料金なしで利用できます。 Azure Backup などのアタッチされた Azure リソースには、コストが発生します。


**Azure Policy を使用して Automanage を適用できますか?**

はい、ユーザーが定義したスコープ内のすべての VM に Automanage を自動的に適用する組み込みのポリシーがあります。 また、Automanage アカウントと共に環境構成 (DevTest または運用) も指定します。 Azure Policy による Automanage の有効化の詳細については、[こちら](virtual-machines-policy-enable.md)を参照してください。


**Automanage アカウントとは何ですか?**

Automanage アカウントは、自動化された操作が行われるセキュリティ コンテキストまたは ID を提供する MSI (管理サービス ID) です。


**Automanage を有効にすると、選択した VM 以外のその他の VM にも影響がありますか?**

VM が既存の Log Analytics ワークスペースにリンクされている場合は、そのワークスペースを再利用して次のソリューションを適用します。Change Tracking、インベントリ、Update Management。 そのワークスペースに接続されているすべての VM で、これらのソリューションが有効になります。


**自分の VM の環境を変更することはできますか?**

現時点では、その VM の Automanage を無効にしてから、必要な環境と基本設定を使用して Automanage を再度有効にする必要があります。


**Update Management などのサービス用に VM が既に構成されている場合、Automanage によってそれが再構成されますか?**
いいえ、Automanage では再構成は行われません。 そのサービスに関連付けられているリソースでドリフトの監視が開始されます。


**Automanage ポータルで VM の状態が "失敗" になるのはなぜですか?**

状態が "*失敗*" と表示される場合は、VM が配置されているリソース グループを使用してデプロイのトラブルシューティングを行うことができます。 **[リソース グループ]** に移動し、リソース グループを選択し、 **[デプロイ]** をクリックして、"*失敗*" 状態とエラーの詳細を確認します。

**Automanage のトラブルシューティングのサポートを受けるにはどうすればよいですか?**

[テクニカル サポート ケースのチケット](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を提出できます。 **[サービス]** オプションについては、"*監視と管理*" のセクションで *Automanage* を探して選択します。


## <a name="next-steps"></a>次の手順

Azure portal でAzure Automanage for virtual machines を有効にしてみてください。

> [!div class="nextstepaction"]
> [Azure portal で Azure Automanage for virtual machines を有効にする](quick-create-virtual-machines-portal.md)