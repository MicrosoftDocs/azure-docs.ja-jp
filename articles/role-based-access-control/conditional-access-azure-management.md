---
title: Azure Active Directory での条件付きアクセスを使用して Azure 管理へのアクセスを管理する
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
ms.date: 01/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: b824d122a5d26c17c41a0e2ea1c595c9e2dd7206
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2019
ms.locfileid: "54354350"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>条件付きアクセスを使用して Azure 管理へのアクセスを管理する

Azure Active Directory (Azure AD) での条件付きアクセスでは、指定した特定の条件に基づいてクラウド アプリへのアクセスが制御されます。 アクセスを許可するには、ポリシーの要件を満たしているかどうかに基づいてアクセスを許可またはブロックする条件付きアクセス ポリシーを作成します。 

通常は、クラウド アプリへのアクセスを制御する場合に条件付きアクセスを使用します。 特定の条件 (サインイン リスク、場所、またはデバイスなど) に基づいて Azure 管理へのアクセスを制御したり、して、多要素認証 のような要件を施行したりするために、ポリシーを設定することもできます。

Azure 管理のポリシーを作成するには、ポリシーを適用するアプリを選択するときに**クラウド アプリ**の **Microsoft Azure Management** を選択します。

![Azure 管理の条件付きアクセス](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

作成したポリシーは、Azure portal、Azure Resource Manager のプロバイダー、従来の Service Management API、Azure PowerShell、および Visual Studio サブスクリプション管理者ポータルを含め、すべての Azure 管理エンドポイントに適用されます。 ポリシーは Azure Resource Manager API を呼び出す Azure PowerShell に適用されることに注意してください。 Microsoft Graph を呼び出す [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2) には適用されません。

> [!CAUTION]
> Azure 管理へのアクセスを管理するポリシーを設定する前に、条件付きアクセスのしくみについて理解しておくようにしてください。 ポータルへのアクセスをブロックする条件を作成しないようにしてください。

条件付きアクセスをセットアップして使用する方法の詳細については、 「[Azure Active Directory での条件付きアクセス](../active-directory/active-directory-conditional-access-azure-portal.md)」を参照してください。