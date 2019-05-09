---
title: Azure Active Directory B2C で B2C テナントに切り替える | Microsoft Docs
description: Active Directory B2C テナントのコンテキストに切り替える方法。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 4/13/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 9315b3a5e1641098daf2c7dadf9fa4f09d0cb2a7
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438510"
---
# <a name="switching-to-your-azure-ad-b2c-tenant"></a>Azure AD B2C テナントへの切り替え

Azure AD B2C を構成するには、Azure AD B2C テナントのコンテキストに移動する必要があります。

## <a name="log-into-azure-ad-b2c-tenant"></a>Azure AD B2C テナントへのログイン

Azure AD B2C テナントに移動するには、Azure AD B2C テナントのグローバル管理者として Azure Portal にログインする必要があります。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. テナントを切り替えるには、右上隅にある電子メール アドレスまたは画像をクリックします。
1. 表示された `Directory` の一覧で、管理する Azure AD B2C テナントを選択します。

Azure Portal が更新されます。  これで、Azure AD B2C テナントのコンテキストで Azure Portal にサインインしました。

## <a name="navigate-to-the-b2c-features-pane"></a>B2C 機能ウィンドウに移動する

1. 左側のナビゲーションで **[参照]** をクリックします。
1. **[すべてのサービス]** をクリックし、左側のナビゲーション ウィンドウで `Azure AD B2C` を検索します   (左側のスタート画面にピン留めするには、Azure AD B2C の左側にある星をクリックします)。
1. **[Azure AD B2C]** をクリックして B2C 機能ウィンドウにアクセスします。
   
    ![B2C 機能ウィンドウへの移動のスクリーンショット](./media/active-directory-b2c-get-started/b2c-browse.png)

> [!IMPORTANT]
> B2C 機能ウィンドウにアクセスするには、B2C テナントのグローバル管理者である必要があります。 他のテナントのグローバル管理者やテナントのユーザーはアクセスできません。  Azure Portal の右上隅にあるテナント スイッチャーを使用して、B2C テナントに切り替えることができます。
