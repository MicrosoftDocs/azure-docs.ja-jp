---
title: B2B コラボレーションのライセンスに関するガイダンス - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B コラボレーションでは、Azure AD 有料ライセンスは必要ありませんが、B2B ゲスト ユーザー用の有料機能を利用することもできます。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2df98966b69250d31f2a741411aea6e5554f9ed6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112865"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Azure Active Directory B2B コラボレーションのライセンスに関するガイダンス

Azure Active Directory (Azure AD) 企業間 (B2B) コラボレーション機能を使用すると、有料の Azure AD サービスを使用できるように、外部ユーザー (つまり "ゲスト ユーザー") を招待できます。 ユーザーに割り当てる有料の Azure AD ライセンスごとに、外部ユーザー無料利用分に従って最大 5 人までのゲスト ユーザーを招待できます。

B2B ユーザーのライセンスは、1:5 の割合に基づいて自動的に計算され、報告されます。 現在のところ、B2B ゲスト ユーザー ライセンスをゲスト ユーザーに直接割り当てることはできません。

さらに、ゲスト ユーザーは、追加のライセンス要件なしで Azure AD の無料の機能を使用できます。 ゲスト ユーザーは、お客様が Azure AD の有料ライセンスを所有していない場合でも、Azure AD の無料の機能にアクセスできます。 

## <a name="examples-calculating-guest-user-licenses"></a>次に例を示します。ゲスト ユーザー ライセンスの計算
Azure AD の有料サービスにアクセスする必要があるゲスト ユーザーの数を判断したら、必要な 1:5 の割合でゲスト ユーザーに対応するのに十分な Azure AD 有料ライセンスを所有していることを確認してください。 次に例をいくつか示します。

- Azure AD のアプリやサービスに 100 人のゲスト ユーザーを招待したいと考えていて、すべてのゲスト ユーザーにアクセス管理とプロビジョニングを割り当てたいと考えています。 また、それらのゲスト ユーザーのうちの 50 人には、MFA と条件付きアクセスも必要にしたいと考えています。 この組み合わせに対応するには、10 個の Azure AD Basic ライセンスと 10 個の Azure AD Premium P1 ライセンスが必要になります。 お客様がゲスト ユーザーに対して Identity Protection の機能を使用する場合は、ゲスト ユーザーに対応するために同じ 1:5 の割合で Azure AD Premium P2 ライセンスが必要になります。
- 全員が MFA を必要とするゲスト ユーザーを 60 人招待したいと考えているため、少なくとも 12 個の Azure AD Premium P1 ライセンスが必要です。 お客様には、Azure AD Premium P1 ライセンスを持つ従業員が 10 人います。これらの従業員は、1:5 のライセンス割合に従い、最大 50 人のゲスト ユーザーを許容できます。 10 人の追加のゲスト ユーザーに対応するには、2 つの追加の Premium P1 ライセンスを購入する必要があります。

## <a name="next-steps"></a>次の手順

Azure AD B2B コラボレーションに関する以下のリソースを参照してください。

* [Azure Active Directory の料金](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure AD B2B コラボレーションとは](what-is-b2b.md)
* [Azure Active Directory B2B コラボレーションに関してよく寄せられる質問 (FAQ)](faq.md)
