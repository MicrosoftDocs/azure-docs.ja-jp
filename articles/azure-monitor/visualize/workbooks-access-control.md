---
title: Azure Monitor ブックのアクセス制御
description: ロールベースのアクセス制御を備えた作成済みのブックやパラメーター化されたカスタム ブックを使用して、複雑なレポート作成を簡素化します
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 7d3bc13dc373cda510153099859cf4cd61b3dd69
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101710823"
---
# <a name="access-control"></a>アクセス制御

ブックのアクセス制御は、次の 2 つのことを意味します。

* ブック内のデータを読み取るために必要なアクセス。 このアクセスは、ブックで使用されるリソースの標準 [Azure ロール](../../role-based-access-control/overview.md)によって制御されます。 ブックでは、これらのリソースへのアクセスを指定または構成しません。 ユーザーは、通常、これらのリソースに対する[監視閲覧者](../../role-based-access-control/built-in-roles.md#monitoring-reader)ロールを使用して、これらのリソースへのアクセスを取得します。

* ブックを保存するために必要なアクセス

    - プライベートな `("My")` ブックを保存するには、追加の特権は必要ありません。 すべてのユーザーはプライベート ブックを保存でき、そのユーザーのみがそのブックを表示することができます。
    - 共有ブックを保存するには、ブックの保存先のリソース グループに対する書き込み権限が必要です。 通常、これらの特権は "[監視共同作成者](../../role-based-access-control/built-in-roles.md#monitoring-contributor)" ロールによって指定されますが、"*ブック共同作成者*" ロールを使用して設定することもできます。
    
## <a name="standard-roles-with-workbook-related-privileges"></a>ブック関連の特権を持つ標準ロール

[監視閲覧者](../../role-based-access-control/built-in-roles.md#monitoring-reader)には、リソースからデータを読み取るために監視ツール (ブックを含む) によって使用される標準の /read 特権が含まれています。

[監視共同作成者](../../role-based-access-control/built-in-roles.md#monitoring-contributor)には、項目を保存するためにさまざまな監視ツールによって使用される一般的な `/write` 特権が含まれます (共有ブックを保存するための `workbooks/write` 特権を含みます)。
"ブック共同作成者" は、共有ブックを保存するために、オブジェクトに "workbooks/write" 特権を追加します。
自分だけが閲覧できるプライベート ブックをユーザーが保存するために特別な特権は必要ありません。

カスタム ロールの場合:

共有ブックを保存するには、`microsoft.insights/workbooks/write` を追加します。 詳細については、[ブック共同作成者](../../role-based-access-control/built-in-roles.md#monitoring-contributor)ロールに関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* ブックの豊富な視覚化オプションの学習を[開始](./workbooks-overview.md#visualizations)します。