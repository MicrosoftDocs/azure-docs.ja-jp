---
title: "タイル ギャラリーの関連リソースとリンク済みリソース"
description: "Azure プレビュー ポータルのタイル ギャラリーに表示される関連リソースとリンク済みリソースについて説明します。"
services: azure-portal
documentationcenter: 
author: adamabdelhamed
manager: wpickett
editor: 
ms.assetid: dba96d29-f518-49c8-bfd2-f14cecb44cbf
ms.service: azure-portal
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2015
ms.author: adamab
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: efa7bce26c2c4c153b083e0e34d689a11d27dd16
ms.lasthandoff: 03/06/2017


---
# <a name="related-and-linked-resources-in-the-tile-gallery"></a>タイル ギャラリーの関連リソースとリンク済みリソース
タイル ギャラリーを使用すると、特定のリソースのタイルを見つけて、現在のブレードにドラッグできます。 タイル ギャラリーを使用して、リソースにまたがる管理ビューを作成できます。 指定したすべてのリソースについて、関連リソースには、そのリソース グループ内のすべてのリソースと、相互にリンクする任意のリソースが含まれています。

## <a name="linked-resources-in-resource-manager"></a>Resource Manager のリンク済みリソース
リンクは、Resource Manager の機能です。  同じリソース グループに存在していない場合でも、リソース間のリレーションシップを宣言できます。 リンクがリソースのランタイム、課金、ロール ベースのアクセスに影響を与えることはありません。  これは、タイル ギャラリーなどのツールが豊富な管理機能を提供できるように、単にリレーションシップを表すために使用できるメカニズムです。  このツールは、API へのリンクを使用してリンクを検査し、カスタムのリレーションシップ管理機能も提供できます。 

## <a name="how-do-i-link-my-resources"></a>リソースをリンクする方法
ポータル経由、または Azure PowerShell や Azure CLI からテンプレートをデプロイすることで、リソースを作成すると、リンクが一部の依存リソースで自動的に作成されます。 また、[リンク済みリソースの REST API](/rest/api/resources/resourcelinks) を使うことで、プログラムでリソースをリンクすることもできます。

## <a name="next-steps"></a>次のステップ
* Resource Manager テンプレートの作成の概要については、「[テンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)」をご覧ください。
* ポータルからリソース グループを使用する方法の詳細については、「[ポータルを使用した Azure リソースの管理](../azure-resource-manager/resource-group-portal.md)」をご覧ください。


