---
title: "Azure Stack でポータルを使用してテンプレートをデプロイする | Microsoft Docs"
description: "Azure Stack ポータルを使用してテンプレートをデプロイする方法について説明します。"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 630d86ed7e3558ae2c8a62f4e1a94f10ca11812d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Azure Stack ポータルを使用したテンプレートのデプロイ

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

ポータルを使用して、Azure Resource Manager テンプレートを Azure Stack Development Kit にデプロイします。

Resource Manager テンプレートは、お使いのアプリケーションのすべてのリソースを、単一の連携した操作でデプロイしてプロビジョニングします。

1. ポータルにログインし、**[新規]**、**[カスタム]**、**[テンプレートのデプロイ]** の順にクリックします。
2. **[テンプレートの編集]** をクリックし、JSON テンプレート コードをブレードに貼り付けて、**[保存]** をクリックします。
3. **[パラメーターの編集]** をクリックし、一覧表示されたパラメーターの値を入力して、**[OK]** をクリックします。
4. **[サブスクリプション]** をクリックし、使用するサブスクリプションを選択して、**[OK]** をクリックします。
5. **[リソース グループ]** をクリックし、既存のグループを選択するか新しいグループを作成して、**[OK]** をクリックします。
6. **Create** をクリックしてください。 ダッシュボードの新しいタイルにより、テンプレート デプロイの進行状況を追跡します。

## <a name="next-steps"></a>次のステップ
[PowerShell を使用したテンプレートのデプロイ](azure-stack-deploy-template-powershell.md)

