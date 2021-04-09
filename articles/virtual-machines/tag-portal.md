---
title: Azure portal を使用して VM にタグを付ける方法
description: Azure portal を使用して仮想マシンにタグを付ける方法について説明します。
ms.topic: how-to
ms.workload: infrastructure-services
author: cynthn
ms.service: virtual-machines
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 9c220814ae1f714e9eac0c0c11a50d9cf68a621d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897410"
---
# <a name="tagging-a-vm-using-the-portal"></a>ポータルを使用して VM にタグを付ける

この記事では、ポータルを使用して VM にタグを追加する方法について説明します。 タグはユーザー定義のキーと値ペアです。リソースまたはリソース グループに直接設定できます。 現在、Azure では、1 つのリソースまたはリソース グループにつき最大 50 個のタグがサポートされます。 タグは、リソースの作成時に付けたり、既存のリソースに追加したりすることができます。


1. ポータルで目的の VM に移動します。
1. **[要点]** で、 **[タグを追加するにはここをクリック]** を選択します。

    :::image type="content" source="media/tag/azure-portal-tag.png" alt-text="VM ページの [要点] セクションのスクリーンショット。":::

1. **[名前]** と **[値]** に値を追加し、 **[保存]** を選択します。

    :::image type="content" source="media/tag/key-value.png" alt-text="キーと値のペアをタグとして追加するためのページのスクリーンショット。":::

### <a name="next-steps"></a>次のステップ

- Azure リソースへのタグ付けについて詳しくは、「[Azure Resource Manager の概要](../azure-resource-manager/management/overview.md)」と「[タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)」をご覧ください。
- Azure リソースの使用を管理するときにタグを役立てる方法については、「[Azure の課金内容の確認」](../cost-management-billing/understand/review-individual-bill.md)を参照してください。
