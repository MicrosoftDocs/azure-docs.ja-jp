---
title: Azure DevTest Labs でのラボへのタグの追加 | Microsoft Docs
description: Azure DevTest Labs でラボにタグを追加する方法を説明します
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: dc5b327a-62e4-41bc-80ef-deb3c23d51b2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: e4d9aeb527461cc7292235fef1de0abdfa4242bd
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60148919"
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs でラボにタグを追加する

カスタム タグを作成して DevTest Labs リソースに適用すると、リソースを論理的に分類できます。 お使いのサブスクリプション内でそのタグが適用されているすべてのリソースは、後で簡単にすぐに確認できます。 タグは、課金または管理の目的でリソースを整理する必要がある場合に役立ちます。

タグでサポートされているリソースには、次のものがあります。

* 計算 VM
* NIC
* IP アドレス
* ロード バランサー
* ストレージ アカウント
* マネージド ディスク

[ラボを作成](devtest-lab-create-lab.md)するときにタグを適用すると、あとで [Configuration and settings]\(構成と設定\) の[タグ] ブレードから管理できます。

すべてのタグは **名前**/**値**ペアで構成されています。 たとえば、*costcenter* という名前の、*34543* という値を持つタグを作成するとします。 このようなタグは後々、所属組織の特定の領域に対して課金できるラボ リソースを識別するのに役立つ場合があります。 サブスクリプションを整理するうえで意味のある名前と値を選ぶようにします。

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>既存のラボのタグを管理する手順

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
1. 必要に応じて、**[All Services]\(その他のサービス\)** を選択し、一覧から **[DevTest Labs]** を選択します。 お使いのラボは、**[すべてのリソース]** の [ダッシュボード] に既に表示されている場合があります。
1. ラボの一覧から、タグを追加して管理するラボを選択します。
1. ラボの **[概要]** で、**[Configuration and policies]\(構成とポリシー\)** を選択します。

    ![[Configuration and policies]\(構成とポリシー\) ボタン](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. 左側の **[管理]** で、**[タグ]** を選択します。
1. このラボ用の新しいタグを作成するには、**名前**/**値**ペアを入力し、**[保存]** を選択します。 一覧から既存のタグを選択すると、そのタグに関連付けられているリソースの表示や管理もできます。

    ![タグの管理](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

## <a name="understanding-limitations-to-tags"></a>タグの制限事項について

タグには次の制限事項が適用されます。

* 各リソースまたはリソース グループには、最大で 15 個のタグ名/タグ値のペアを付けることができます。 この制限は、リソース グループまたはリソースに直接適用されたタグにのみ適用されます。 リソース グループには、それぞれ 15 個のタグ名/タグ値のペアが付けられたリソースを多数含めることができます。
* タグ名は 512 文字まで、タグ値は 256 文字までに制限されます。 ストレージ アカウントについては、タグ名は 128 文字まで、タグ値は 256 文字までに制限されます。
* リソース グループに適用したタグは、そのリソース グループ内のリソースには継承されません。

「[タグを使用した Azure リソースの整理](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)」のページでは、PowerShell や Azure CLI を使用してタグを管理する方法など、Azure でのタグの使用に関する詳細な情報を得られます。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>次の手順
* カスタマイズしたポリシーを使用して、サブスクリプションの制約と規則を適用できます。 定義するポリシーには、すべてのリソースが特定のタグに値が指定されていることが必要になる場合があります。 詳細については、[ポリシーとスケジュールの設定](devtest-lab-set-lab-policy.md)に関するページをご覧ください。
* [DevTest Labs Azure Resource Manager のクイックスタート テンプレート ギャラリー](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)を検索します。
