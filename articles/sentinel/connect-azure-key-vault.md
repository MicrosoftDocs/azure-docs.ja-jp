---
title: Azure Key Vault の診断ログを Azure Sentinel に接続する
description: Azure Policy を使用して Azure Key Vault の診断ログを Azure Sentinel に接続する方法について説明します。
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 03/07/2021
ms.author: yelevin
ms.openlocfilehash: 00e634ec62d1fda4b1f2763a8c69b8e950d54beb
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102505190"
---
# <a name="connect-azure-key-vault-diagnostics-logs"></a>Azure Key Vault の診断ログを接続する

Azure Key Vault は、シークレットを安全に保管し、それにアクセスするためのクラウド サービスです。 シークレットは、API キー、パスワード、証明書、暗号化キーなど、アクセスを厳密に制御する必要がある任意のものです。

このコネクタを使用すると、Azure Key Vault の診断ログを Azure Sentinel にストリーミングできます。これにより、すべてのインスタンスでのアクティビティを継続的に監視できます。

[Azure Key Vault の監視](../azure-monitor/insights/key-vault-insights-overview.md)と [Azure Key Vault 診断テレメトリ](../key-vault/general/logging.md)の詳細に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

Azure Key Vault のログを Azure Sentinel に取り込むには:

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- Azure Policy を使用して Azure Key Vault リソースにログ ストリーミング ポリシーを適用するには、ポリシー割り当てスコープの所有者ロールが割り当てられている必要があります。

## <a name="connect-to-azure-key-vault"></a>Azure Key Vault に接続する

このコネクタでは、Azure Policy を使用して、単一の Azure Key Vault のログ ストリーミング構成が、スコープとして定義されたインスタンスのコレクションに適用されます。 **[データ型]** の下のコネクタ ページの左側で、Azure Key Vault から取り込まれたログの種類を確認できます。

1. Azure Sentinel のナビゲーション メニューから、 **[Data connectors]\(データ コネクタ\)** を選択します。

1. データ コネクタ ギャラリーから **[Azure Key Vault]** を選択し、プレビュー ペインで **[コネクタ ページを開く]** を選択します。

1. コネクタ ページの **[構成]** セクションで、 **[Azure Key Vault の診断ログを有効にする]** を展開します。

1. **[Azure Policy 割り当てウィザードの起動]** ボタンを選択します。

    ポリシー割り当てウィザードが開き、**Deploy - Configure diagnostic settings for Azure Key Vault to Log Analytics workspace** いう名前の新しいポリシーを作成できるようになります。

    1. **[基本]** タブで、 **[スコープ]** の下にある 3 つのドットがあるボタンをクリックして、お使いのサブスクリプション (および必要に応じてリソース グループ) を選択します。 説明を追加することもできます。

    1. **[パラメーター]** タブで、 **[Log Analytics ワークスペース]** ドロップダウン リストからお使いの Azure Sentinel ワークスペースを選択します。 残りのドロップダウン フィールドは、使用可能な診断ログの種類を表します。 取り込み対象のすべてのログの種類を "True" とマークされたままにします。

    1. 既存のリソースにポリシーを適用するには、 **[修復]** タブを選択し、 **[Create a remediation task]\(修復タスクを作成する\)** チェック ボックスにチェックを付けます。

    1. **[確認と作成]** タブで、 **[作成]** をクリックします。 これで、選択したスコープにポリシーが割り当てられました。

> [!NOTE]
>
> この特定のデータ コネクタを使用すると、過去 2 週間のある時点でデータが取り込まれた場合にのみ、接続状態インジケーター (データ コネクタ ギャラリーのカラー ストライプとデータ型の名前の横にある接続アイコン) が "*接続済み*" (緑) と表示されます。 データ インジェストがないまま 2 週間が経過すると、コネクタは切断済みと表示されます。 さらに多くのデータが入ってくると、"*接続済み*" 状態が返されます。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure Key Vault を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
