---
title: Azure Firewall ブックを使用してログを監視する
description: Azure Firewall ブックにより、Azure Firewall のデータ分析のための柔軟なキャンバスが提供され、Azure portal 内で高度な視覚的レポートを作成できます。
services: firewall
author: gopimsft
ms.service: firewall
ms.topic: how-to
ms.date: 09/22/2020
ms.author: victorh
ms.openlocfilehash: e63336b0e84d303b51eda56f90ca6fb453d8ae0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831843"
---
# <a name="monitor-logs-using-azure-firewall-workbook"></a>Azure Firewall ブックを使用してログを監視する

Azure Firewall ブックにより、Azure Firewall のデータ分析のための柔軟なキャンバスが提供されます。 これを使用して、Azure portal 内で高度な視覚的レポートを作成できます。 Azure 全体でデプロイされる複数のファイアウォールを活用し、それらを結合して、統合された対話型エクスペリエンスにすることができます。

Azure Firewall のイベントの分析情報を得たり、アプリケーションやネットワークの規則について調べたり、URL、ポート、アドレスでのファイアウォール アクティビティの統計を確認したりできます。 Azure Firewall ブックを使用すると、ファイアウォールとリソース グループをフィルター処理したり、ログ内の問題を調査するときにデータセットが見やすくなるように、カテゴリごとに動的にフィルター処理したりできます。 

## <a name="prerequisites"></a>前提条件

開始する前に、Azure portal を使用して[診断ログを有効化する](firewall-diagnostics.md#enable-diagnostic-logging-through-the-azure-portal)必要があります。 また、「[Azure Firewall のログとメトリック](logs-and-metrics.md)」を参照して、Azure Firewall で入手できる診断ログとメトリックの概要を確認してください。

## <a name="get-started"></a>はじめに

ブックをデプロイするには、「[Azure Monitor Workbook for Azure Firewall](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20Firewall/Workbook%20-%20Azure%20Firewall%20Monitor%20Workbook)」にアクセスし、ページの指示に従ってください。 Azure Firewall ブックは、マルチテナントおよび複数のサブスクリプションで動作するように設計されており、複数のファイアウォールに対してフィルター処理できます。

## <a name="overview-page"></a>[概要] ページ

[概要] ページには、ワークスペース、時間、ファイアウォールでフィルター処理するための方法が提供されています。 複数のファイアウォールとログの種類 (アプリケーション、ネットワーク、Threat intel、DNS プロキシ) のイベントが時間別に表示されます。

:::image type="content" source="./media/firewall-workbook/firewall-workbook-overview.png" alt-text="Azure Firewall ブックの概要":::

## <a name="application-rule-log-statistics"></a>アプリケーション規則のログの統計

このページには、IP アドレスの一意のソース数の時間推移、アプリケーション規則の数、使用状況、拒否または許可された FQDN 数の時間推移、およびフィルター処理されたデータが表示されます。 IP アドレスに基づいてデータをフィルター処理できます。

:::image type="content" source="./media/firewall-workbook/firewall-workbook-application-rule.png" alt-text="Azure Firewall ブックのアプリケーション規則のログ":::

## <a name="network-rule-log-statistics"></a>ネットワーク規則のログの統計

このページには、規則のアクション別のビューが表示されます (許可/拒否、IP 別のターゲット ポート、DNAT の時間推移)。 アクション、ポート、送信先の種類でフィルター処理することもできます。

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule.png" alt-text="Azure Firewall ブックのネットワーク規則のログ":::

また、時間枠に基づいてログをフィルター処理することもできます。

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule-time.png" alt-text="Azure Firewall ブックのネットワーク規則のログの時間枠":::

## <a name="investigations"></a>調査

ログを確認し、ソース IP アドレスに基づいてリソースの詳細を把握できます。 仮想マシン名やネットワーク インターフェイス名などの情報を取得できます。 ログからのリソースにフィルターを適用するのは簡単です。

:::image type="content" source="./media/firewall-workbook/firewall-workbook-investigation.png" alt-text="Azure Firewall ブックの調査":::

## <a name="next-steps"></a>次の手順

- [Azure Firewall の診断](firewall-diagnostics.md)の詳細をご覧ください。
