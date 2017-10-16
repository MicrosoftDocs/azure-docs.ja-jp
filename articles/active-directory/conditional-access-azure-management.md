---
title: "Azure Active Directory での条件付きアクセスを使用して Azure 管理へのアクセスを管理する"
description: "Azure AD での条件付きアクセスを使用して、Azure 管理へのアクセスを管理する方法について説明します。"
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: bryanla
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/22/2017
ms.author: skwan
ms.openlocfilehash: d4fa31d664209ba7fea9ee85773d0ab9efb81bed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>条件付きアクセスを使用して Azure 管理へのアクセスを管理する

Azure Active Directory (Azure AD) での条件付きアクセスでは、指定した特定の条件に基づいてクラウド アプリへのアクセスが制御されます。 アクセスを許可するには、ポリシーの要件を満たしているかどうかに基づいてアクセスを許可またはブロックする条件付きアクセス ポリシーを作成します。 

通常は、クラウド アプリへのアクセスを制御する場合に条件付きアクセスを使用します。 特定の条件 (サインイン リスク、場所、またはデバイスなど) に基づいて Azure 管理へのアクセスを制御したり、して、多要素認証 のような要件を施行したりするために、ポリシーを設定することもできます。

Azure 管理のポリシーを作成するには、ポリシーを適用するアプリを選択するときに**クラウド アプリ**の **Microsoft Azure Management** を選択します。

![Azure 管理の条件付きアクセス](./media/conditional-access-azure-mgmt.png)

作成したポリシーは、従来の Azure ポータル、Azure ポータル、Azure Resource Manager のプロバイダー、従来の Service Management API、および PowerShell を含む、すべての Azure 管理エンドポイントに適用されます。

> [!CAUTION]
> Azure 管理へのアクセスを管理するポリシーを設定する前に、条件付きアクセスのしくみについて理解しておくようにしてください。 ポータルへのアクセスをブロックする条件を作成しないようにしてください。

条件付きアクセスをセットアップして使用する方法の詳細については、 「[Azure Active Directory での条件付きアクセス](active-directory-conditional-access-azure-portal.md)」を参照してください。