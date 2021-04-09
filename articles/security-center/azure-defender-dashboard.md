---
title: Azure Defender ダッシュボードとその機能
description: Azure Defender ダッシュボードの機能について説明します。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 16379380fc35bb2355c496dc857e9de3b41347f9
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096906"
---
# <a name="the-azure-defender-dashboard"></a>Azure Defender ダッシュボード

Azure Defender ダッシュボードでは、以下を提供します。

- さまざまなリソースの種類にわたる Azure Defender の対象範囲の可視化
- 高度な脅威防止機能を構成するためのリンク
- オンボード状態とエージェントのインストール
- Azure Defender の脅威検出アラート 

Azure Defender ダッシュボードにアクセスするには、Security Center のメニューの [Cloud Security]\(クラウド セキュリティ\) セクションで **[Azure Defender]** を選択します。

## <a name="whats-shown-on-the-dashboard"></a>ダッシュボードに表示される内容

:::image type="content" source="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png" alt-text="Azure Defender ダッシュボードの例" lightbox="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png":::

ダッシュボードには次のセクションがあります。

1. **[Azure Defender coverage]\(Azure Defender の対象範囲\)** - ここでは、サブスクリプション内にあり、Azure Defender による保護の対象となるリソースの種類を確認できます。 必要に応じて、アップグレードすることもできます。 対象となる可能性のあるすべてのリソースをアップグレードする場合は、 **[すべてをアップグレード]** を選択します。

2. **[セキュリティのアラート]** - Azure Defender によって環境のいずれかの領域で脅威が検出されると、アラートが生成されます。 これらのアラートでは、影響を受けるリソースや推奨される修復手順の詳細と、場合によっては、ロジック アプリを応答でトリガーするオプションが示されます。 このグラフ内の任意の場所を選択すると、 **[セキュリティのアラート] ページ** が開きます。

3. **[高度な保護]** - Azure Defender には、仮想マシン、SQL データベース、コンテナー、Web アプリケーション、ネットワークなどを対象とする多数の高度な脅威防止機能が用意されています。 この [高度な保護] セクションでは、これらの各保護について、選択したサブスクリプション内のリソースの状態を確認できます。 それらのいずれかを選択して、その保護の種類の構成領域に直接移動します。

4. **[Insights]\(インサイト\)** - ニュース、お勧めの記事、優先度の高いアラートのこのローリング ペインでは、お客様とお客様のサブスクリプションに関連する緊急のセキュリティの問題に関する Security Center の分析情報が提供されます。 脆弱性分析ツールによって VM で検出された重大度の高い CVE の一覧も、Security Center チームのメンバーによる新しいブログ記事も、**Azure Defender ダッシュボード** の [インサイト] ペインで確認できます。




## <a name="next-steps"></a>次の手順

この記事では、Azure Defender ダッシュボードについて説明しました。 

Azure Defender の詳細については、[Azure Defender の概要](azure-defender.md)に関する記事をご覧ください。

> [!div class="nextstepaction"]
> [Azure Defender を有効にする](enable-azure-defender.md)