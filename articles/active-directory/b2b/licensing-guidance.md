---
title: B2B コラボレーションのライセンスに関するガイダンス - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B コラボレーションでは、Azure AD 有料ライセンスは必要ありませんが、B2B ゲスト ユーザー用の有料機能を利用することもできます。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28d02160163da4081ad8adbe233b27fee970a0df
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74868849"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Azure Active Directory B2B コラボレーションのライセンスに関するガイダンス

Azure Active Directory (Azure AD) 企業間 (B2B) コラボレーション機能を使用すると、有料の Azure AD サービスを使用できるように、外部ユーザー (つまり "ゲスト ユーザー") を招待できます。 一部の機能は無料ですが、有料の Azure AD 機能の場合は、テナント内の従業員または非ゲスト ユーザー用に所有している Azure AD エディション ライセンスごとに最大 5 人のゲスト ユーザーを招待できます。

> [!NOTE]
> Azure AD の価格と B2B コラボレーション機能の詳細については、「[Azure Active Directory の価格](https://azure.microsoft.com/pricing/details/active-directory/)」を参照してください。

B2B ユーザーのライセンスは、1:5 の割合に基づいて自動的に計算され、報告されます。 

さらに、ゲスト ユーザーは、追加のライセンス要件なしで Azure AD の無料の機能を使用できます。 ゲスト ユーザーは、お客様が Azure AD の有料ライセンスを所有していない場合でも、Azure AD の無料の機能にアクセスできます。 

## <a name="examples-calculating-guest-user-licenses"></a>次に例を示します。ゲスト ユーザー ライセンスの計算
Azure AD の有料サービスにアクセスする必要があるゲスト ユーザーの数を判断したら、必要な 1:5 の割合でゲスト ユーザーに対応するのに十分な Azure AD 有料ライセンスを所有していることを確認してください。 次に例をいくつか示します。

- お使いの Azure AD のアプリやサービスに 100 人のゲスト ユーザーを招待し、アクセス管理とプロビジョニングを行いたいと考えています。 これらのゲスト ユーザーの内の 50 人には、MFA と条件付きアクセスも必要です。したがって、これらの機能には、10 の Azure AD Premium P1 ライセンスが必要です。 お客様がゲスト ユーザーに対して Identity Protection の機能を使用する場合は、ゲスト ユーザーに対応するために同じ 1:5 の割合で Azure AD Premium P2 ライセンスが必要になります。
- 全員が MFA を必要とするゲスト ユーザーを 60 人招待したいと考えているため、少なくとも 12 個の Azure AD Premium P1 ライセンスが必要です。 お客様には、Azure AD Premium P1 ライセンスを持つ従業員が 10 人います。これらの従業員は、1:5 のライセンス割合に従い、最大 50 人のゲスト ユーザーを許容できます。 10 人の追加のゲスト ユーザーに対応するには、2 つの追加の Premium P1 ライセンスを購入する必要があります。

## <a name="next-steps"></a>次の手順

Azure AD B2B コラボレーションに関する以下のリソースを参照してください。

* [Azure Active Directory の料金](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure AD B2B コラボレーションとは](what-is-b2b.md)
* [Azure Active Directory B2B コラボレーションに関してよく寄せられる質問 (FAQ)](faq.md)
