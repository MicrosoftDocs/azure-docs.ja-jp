---
title: 顧客データ要求機能 - Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights の顧客データ要求機能について説明します。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: b5ecbabe4a5c3458c77d145fbce1281550470e00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019074"
---
# <a name="summary-of-customer-data-request-features"></a>顧客データ要求機能の概要

Azure Time Series Insights は、ストレージ、分析、視覚化コンポーネントを備えたマネージド クラウド サービスです。これらのコンポーネントにより、簡単に、数十億ものイベントを並行して取り込み、保存、調査、分析することができます。

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

データ主体要求の対象となる可能性がある個人データを表示、エクスポート、削除するために、Azure Time Series Insights テナント管理者は Azure Portal または REST API を使用することができます。 Azure Portal を使用してデータ主体要求を処理することで、ほとんどのユーザーに必要な操作をより簡単に実行することができます。

## <a name="identifying-customer-data"></a>顧客データの識別

Azure Time Series Insights は、個人データを Time Series Insights の管理者とユーザーに関連付けられているデータと見なします。 Time Series Insights には、環境に対するアクセス権を持つユーザーの Azure Active Directory オブジェクト ID が格納されます。 Azure Portal にはユーザーのメール アドレスが表示されますが、これらのメール アドレスは Time Series Insights 内に格納されず、Azure Active Directory で Azure Active Directory オブジェクト ID を使用して動的に検索されます。

## <a name="deleting-customer-data"></a>顧客データの削除

テナント管理者は、Azure Portal を使用して顧客データを削除することができます。

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

ただし、Portal を使用して顧客データを削除する前に、Azure Portal 内で Time Series Insights 環境からユーザーのアクセス ポリシーを削除する必要があります。 詳細については、[Azure portal を使用して Time Series Insights 環境にデータ アクセスを許可する](./concepts-access-policies.md)方法に関するページを参照してください。

REST API を使用して、アクセス ポリシーに対して削除操作を実行することもできます。 詳細については、「[アクセス ポリシー - 削除](/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/delete)」をお読みください。

Time Series Insights は、Azure Portal のポリシー ブレードと統合されています。 Time Series Insights とポリシー ブレードの両方で、サービス内に格納されているユーザー データを表示、エクスポート、削除することができます。 Azure Portal のポリシー ブレード内で削除操作を実行すると、Time Series Insights 内のユーザー データが削除されます。 たとえば、ユーザーの個人クエリが保存されている場合、そのクエリは Time Series Insights エクスプローラーから完全に削除されます。 ユーザーの共有クエリが保存されている場合、クエリは保持されますが、ユーザー情報は完全に削除されます。 次のメモには、これらの作業の実行手順が含まれます。

## <a name="exporting-customer-data"></a>顧客データのエクスポート

データの削除の場合と同じように、テナント管理者は、Azure Portal のポリシー ブレードから、Time Series Insights に格納されているデータを表示およびエクスポートすることができます。

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

テナント管理者の場合、Azure Portal で Time Series Insights 環境内のデータ アクセス ポリシーを表示できます。 詳細については、[Azure portal を使用して Time Series Insights 環境にデータ アクセスを許可する](./concepts-access-policies.md)方法に関するページを参照してください。

指定された REST API の "環境で一覧表示" 操作を使用して、アクセス ポリシーに対してエクスポート操作を実行することもできます。 詳細については、「[アクセス ポリシー - 環境で一覧表示](/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/listbyenvironment)」をお読みください。

## <a name="to-delete-data-stored-within-time-series-insights"></a>Time Series Insights 内に格納されているデータを削除するには

個人データは、ユーザーおよび管理者データとは異なり、Time Series Insights ストレージに格納される場合があります。 Time Series Insights に格納されているデータが個人データである場合、次の手順を使用して、そのデータをエクスポートして削除できます。

### <a name="view-and-export-data"></a>データの表示とエクスポート

Time Series Insights 内に格納されているデータを表示およびエクスポートするには、そのデータを検索する必要があります。 Time Series Insights エクスプローラーまたは Time Series Insights クエリ API を使用して、データを表示およびエクスポートできます。 Time Series Insights エクスプローラーを使用してデータを表示およびエクスポートするには、まず、対象のユーザー データを検索して見つけます。 検索後に、グラフを右クリックし、**[イベントの探索]** を選択します。 イベント グリッドが表示され、CSV および JSON としてデータをエクスポートするオプションが示されます。

詳細については、「[Azure Time Series Insights エクスプローラー](time-series-insights-explorer.md)」をお読みください。

### <a name="delete-data"></a>データの削除

現時点では、Time Series Insights でデータの細かい削除はサポートされていません。 ただし、Time Series Insights では、リテンション ポリシーを構成することで、Time Series Insights 内に格納されている顧客データを削除することができます。 削除要件をサポートするために、Time Series Insights 環境全体のリテンション期間を任意の日数に調整することができます。

詳細については、「[Time Series Insights のリテンション期間の構成](time-series-insights-how-to-configure-retention.md)」をお読みください。

## <a name="next-steps"></a>次のステップ

* [Azure Time Series Insights 環境に対するデータ アクセスの許可](./concepts-access-policies.md)の詳細を確認します。

* [Azure Time Series Insights エクスプローラー](time-series-insights-explorer.md)について確認してください。

* [Time Series Insights のリテンション期間の構成](time-series-insights-how-to-configure-retention.md)について確認してください。