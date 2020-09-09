---
title: Azure AD で条件付きアクセスを使用して Azure 管理へのアクセスを管理する
description: Azure AD での条件付きアクセスを使用して、Azure 管理へのアクセスを管理する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 2a52635dbaa7a76034f3a535b099320a901e8c07
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758777"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>条件付きアクセスを使用して Azure 管理へのアクセスを管理する

> [!CAUTION]
> Azure 管理へのアクセスを管理するポリシーを設定する前に、条件付きアクセスのしくみについて理解しておくようにしてください。 ポータルへのアクセスをブロックする条件を作成しないようにしてください。

Azure Active Directory (Azure AD) での条件付きアクセスでは、指定した特定の条件に基づいてクラウド アプリへのアクセスが制御されます。 アクセスを許可するには、ポリシーの要件を満たしているかどうかに基づいてアクセスを許可またはブロックする条件付きアクセス ポリシーを作成します。 

通常は、クラウド アプリへのアクセスを制御する場合に条件付きアクセスを使用します。 特定の条件 (サインイン リスク、場所、またはデバイスなど) に基づいて Azure 管理へのアクセスを制御したり、して、多要素認証 のような要件を施行したりするために、ポリシーを設定することもできます。

Azure 管理のポリシーを作成するには、ポリシーを適用するアプリを選択するときに**クラウド アプリ**の **Microsoft Azure Management** を選択します。

![Azure 管理の条件付きアクセス](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

作成したポリシーは、次のようなすべての Azure 管理エンドポイントに適用されます。

- Azure portal
- Azure Resource Manager プロバイダー
- 従来の Service Management API
- Azure PowerShell
- Visual Studio サブスクリプション管理者ポータル
- Azure DevOps
- Azure Data Factory ポータル

ポリシーは Azure Resource Manager API を呼び出す Azure PowerShell に適用されることに注意してください。 Microsoft Graph を呼び出す [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2) には適用されません。

Microsoft Azure の管理で条件付きアクセスを有効にするサンプル ポリシーを設定する方法の詳細については、「[条件付きアクセス: Azure 管理のために MFA を必須にする](../active-directory/conditional-access/howto-conditional-access-policy-azure-management.md)」を参照してください。
