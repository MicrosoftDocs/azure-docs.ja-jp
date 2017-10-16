---
title: "Azure Stack の再デプロイ | Microsoft Docs"
description: "Azure Stack を再デプロイします。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 795af5ea-892d-40b1-a080-42e4472e4bba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: erikje
ms.openlocfilehash: 891cde9b16bbbb51729129b6ad7a0f3794307baa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="redeploy-azure-stack"></a>Azure Stack の再デプロイ
Azure Stack を再デプロイするには、次に説明するように、最初から開始する必要があります。

## <a name="steps-to-redeploy-azure-stack"></a>Azure Stack を再デプロイする手順
1. 開発キット ホストで、管理者特権での PowerShell コンソールを開き、asdk-installer.ps1 スクリプトに移動して、それを実行し、**[再起動]** をクリックします。
2. ベース オペレーティング システム (**Azure Stack** でなく) を選択し、**[次へ]** をクリックします。
3. 開発キット ホストが再起動したら、以前のデプロイの一部として使用されていた CloudBuilder.vhdx ファイルを削除します。
4. [開発キットをデプロイ](azure-stack-run-powershell-script.md)します。

## <a name="next-steps"></a>次のステップ
[Azure Stack への接続](azure-stack-connect-azure-stack.md)

