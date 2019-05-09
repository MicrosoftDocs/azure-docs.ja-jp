---
title: B2B コラボレーションの招待の利用 - Azure Active Directory | Microsoft Docs
description: プライバシー条件への同意など、エンド ユーザー向けの Azure AD B2B コラボレーションの招待の利用エクスペリエンスについて説明します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e5a8d46f67553cc10bd9cdb31cf64511858948f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046497"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B コラボレーションの招待の利用

Azure Active Directory (Azure AD) B2B コラボレーションを介してパートナー組織のユーザーと共同作業するために、共有アプリケーションにアクセスできるようにゲスト ユーザーを招待することができます。 ゲスト ユーザーがユーザー インターフェイスを介してディレクトリに追加された後、または PowerShell を介して招待された後、ゲスト ユーザーは、[プライバシー条件](#privacy-policy-agreement)に同意する初回の同意プロセスを実行する必要があります。 このプロセスは、次のいずれかの方法で実行されます。

- ゲストの招待元が共有アプリへの直接リンクを送信します。 招待された人はリンクをクリックしてサインインし、プライバシー条件に同意し、共有リソースにシームレスにアクセスします  (その後もゲスト ユーザーには利用 URL が記載された招待メールが送信されますが、特別な場合を除き、招待メールを使用する必要はありません)。  
- ゲスト ユーザーは招待メールを受信し、利用 URL をクリックします。 初回のサインインの一環として、プライバシー条件に同意するように求められます。

## <a name="redemption-through-a-direct-link"></a>直接リンクによる利用

ゲストの招待元は、[共有アプリの直接リンク](../manage-apps/end-user-experiences.md#direct-sign-on-links)を送信してゲスト ユーザーを招待できます。 ゲスト ユーザーの場合、利用エクスペリエンスは共有されたアプリにサインインする場合と同じくらい簡単です。 アプリのリンクをクリックし、プライバシー条件を確認して同意すると、アプリにシームレスにアクセスすることができます。 ほとんどの場合、ゲスト ユーザーは招待メールの利用 URL をクリックする必要はなくなります。

ゲスト ユーザーをユーザー インターフェイスを介して招待した場合、または PowerShell の招待エクスペリエンスの一環として招待メールを送信する場合、招待されたユーザーには引き続き招待メールが送信されます。 このメールは次のように特別な場合に便利です。

- ユーザーが Azure AD アカウントまたは Microsoft アカウント (MSA) を持っていない場合。 この場合、ユーザーはリンクをクリックする前に MSA を作成する必要があります。または、招待メールの利用 URL を使用することができます。 利用プロセスでは、自動的にユーザーには MSA を作成するように求められます。
- 連絡先オブジェクト (Outlook の連絡先オブジェクトなど) と競合するため、招待されたユーザー オブジェクトにメール アドレスを持っていないことがあります。 この場合、ユーザーは招待メールの利用 URL をクリックする必要があります。
- ユーザーは、招待されたメール アドレスの別名でサインインすることができます  (エイリアスは、メール アカウントに関連付けられた追加のメール アドレスです)。この場合、ユーザーは招待メールの利用 URL をクリックする必要があります。

このような特殊なケースが組織にとって重要な場合は、引き続き招待メールを送信する方法を使用してユーザーを招待することをお勧めします。 また、ユーザーがこのような特殊なケースのいずれにも該当しない場合でも、招待メールの URL をクリックしてアクセスできます。

## <a name="redemption-through-the-invitation-email"></a>招待メールによる利用

招待メールを送信する方法で招待された場合、招待メールを介して招待を利用することもできます。 招待されたユーザーは、メールの利用 URL をクリックし、プライバシー条件を確認して同意することができます。 このプロセスについては、ここで詳しく説明します。

1.  招待された後、招待された人は **Microsoft Invitations** から送信されたメールで招待を受け取ります。
2.  招待された人はメールの **[はじめに]** を選択します。
3.  招待された人が Azure AD アカウントまたは MSA を持っていない場合は、MSA を作成するよう求められます。
4.  招待された人は、**[Review permissions]\(アクセス許可の確認\)** 画面にリダイレクトされます。この画面で、招待元の組織のプライバシーに関する声明を確認し、条件に同意することができます。

## <a name="privacy-policy-agreement"></a>プライバシー ポリシー契約

ゲスト ユーザーがサインインし、初めてパートナー組織のリソースにアクセスすると、**[Review permissions]\(アクセス許可の確認\)** 画面が表示されます。 この画面では、招待元の組織のプライバシーに関する声明を確認することができます。 継続するには、招待元の組織のプライバシー ポリシーに従って情報を使用することに同意する必要があります。

![アクセス パネルのユーザー設定のスクリーンショット](media/redemption-experience/ConsentScreen.png) 

テナント管理者として組織のプライバシー ステートメントにリンクする方法については、[Azure Active Directory に組織のプライバシー情報を追加する方法](https://aka.ms/adprivacystatement)に関するページを参照してください。

## <a name="terms-of-use"></a>使用条件

ゲスト ユーザーには、初回利用プロセス中、Azure AD Terms of Use 機能を使用して利用規約を提示することができます。 この機能には、Azure Active Directory の **[管理]** > **[組織の関係]** > **[利用規約]** または **[セキュリティ]** > **[条件付きアクセス]** > **[利用規約]** からアクセスできます。 詳細については、[Azure AD Terms of Use 機能](../conditional-access/terms-of-use.md)に関するページを参照してください。

![新しい利用規約を示すスクリーンショット](media/redemption-experience/organizational-relationships-terms-of-use.png) 

## <a name="next-steps"></a>次の手順

- [Azure AD B2B コラボレーションとは](what-is-b2b.md)
- [Azure Portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](add-users-administrator.md)
- [インフォメーション ワーカーが B2B コラボレーション ユーザーを Azure Active Directory に追加する方法](add-users-information-worker.md)
- [PowerShell を使用して Azure Active Directory B2B コラボレーション ユーザーを追加する](customize-invitation-api.md#powershell)
- [ゲスト ユーザーとして組織を脱退する](leave-the-organization.md)
