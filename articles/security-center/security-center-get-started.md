---
title: Azure Defender にアップグレードする - Azure Security Center
description: このクイックスタートでは、セキュリティを強化するために Security Center の Azure Defender にアップグレードする方法を示します。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 2f37a59d5db3883754b602b2b2525e07b57206b7
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099354"
---
# <a name="quickstart-set-up-azure-security-center"></a>クイックスタート: Azure Security Center を設定する

Azure Security Center は、ハイブリッドおよびマルチクラウド ワークロード全体で統合されたセキュリティ管理と脅威の防止を実現します。 無料の機能で提供されるのは Azure リソースのみの制限付きセキュリティですが、Azure Defender ではこれらの機能がオンプレミスと他のクラウドに拡張されます。 Azure Defender は、セキュリティの脆弱性の検出と修正、悪意のあるアクティビティをブロックするためのアクセス制御とアプリケーション制御の適用、分析とインテリジェンスを使用した脅威の検出、攻撃を受けたときのすばやい対応を支援します。 Azure Defender は無料でお試しいただけます。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。

このクイックスタート セクションでは、Azure Security Center と Azure Defender を有効にするために推奨されるすべての手順について説明します。 このクイックスタートの手順をすべて完了すると、次のようになります。

> [!div class="checklist"]
> * Azure サブスクリプションで Security Center が有効になる
> * Azure サブスクリプションで Azure Defender が有効になる
> * 自動データ収集が設定される
> * セキュリティ アラートのメール通知が設定される
> * ハイブリッドおよびマルチクラウド マシンが Azure に接続される

## <a name="prerequisites"></a>前提条件
セキュリティ センターを使用するには、Microsoft Azure のサブスクリプションが必要です。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。

サブスクリプションで Azure Defender を有効にするには、サブスクリプションの所有者、サブスクリプションの共同作成者、またはセキュリティ管理者の役割が割り当てられている必要があります。

## <a name="enable-security-center-on-your-azure-subscription"></a>お使いの Azure サブスクリプションで Security Center を有効にする

> [!TIP]
> 管理グループ内のすべてのサブスクリプションで Security Center を有効にするには、[複数の Azure サブスクリプションの Security Center を有効にする](onboard-management-group.md)方法に関するページを参照してください。

1. [Azure Portal](https://azure.microsoft.com/features/azure-portal/) にサインインします。

1. ポータルのメニューから **[セキュリティ センター]** を選択します。 

    Security Center の概要ページが開きます。

    :::image type="content" source="./media/security-center-get-started/overview.png" alt-text="Security Center の概要ダッシュボード" lightbox="./media/security-center-get-started/overview.png":::

    **[セキュリティ センター - 概要]** では、ハイブリッド クラウド ワークロードのセキュリティ対策の統合ビューが表示されるので、ワークロードのセキュリティを検出して評価したり、リスクを特定して軽減したりできます。 ご自身や他のサブスクリプション ユーザーによってまだオンボードされていない Azure サブスクリプションは、自動的に無料で有効になります。

**[サブスクリプション]** メニュー項目を選択して、サブスクリプションの一覧を表示およびフィルター処理できます。 選択されたサブスクリプションのセキュリティの状態を反映するように、Security Center の表示が調整されます。 

Security Center を初めて起動してから数分以内に、以下の項目が表示されます。

- 接続されているリソースのセキュリティを向上させる方法についての **推奨事項**。
- Security Center によって現在評価されているリソースとそれぞれのセキュリティの状態のインベントリ。

Security Center を最大限に活用するには、クイックスタート セクションの次のステップに進んでください。



## <a name="next-steps"></a>次のステップ
このクイックスタートでは、Azure Security Center を有効にしました。 次のステップでは、Azure Defender を有効にして、ハイブリッド クラウド ワークロード全体で統合されたセキュリティ管理と脅威の防止を実現します。

> [!div class="nextstepaction"]
> [クイックスタート: Azure Defender を有効にする](enable-azure-defender.md)