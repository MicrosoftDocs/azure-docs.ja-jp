---
title: B2B コラボレーションの招待の利用 - Azure Active Directory | Microsoft Docs
description: プライバシー条件への同意など、エンド ユーザー向けの Azure AD B2B コラボレーションの招待の利用エクスペリエンスについて説明します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: a80eaa134130195fce00ee6a4d68851e478c4532
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052491"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B コラボレーションの招待の利用

この記事では、ゲスト ユーザーがリソースにアクセスできる方法と実行する同意プロセスについて説明します。 招待メールをゲストに送信する場合、その招待には、ゲストがアプリまたはポータルにアクセスするために利用できるリンクを含めます。 招待メールは、ゲストがリソースにアクセスできる方法の 1 つにすぎません。 別の方法として、ゲストをディレクトリに追加し、共有したいポータルまたはアプリへの直接リンクを提供することができます。 使用する方法に関係なく、ゲストには初回の同意プロセスが示されます。 このプロセスにより、ゲストは確実にプライバシー条項に同意し、設定された[利用規約](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou)を承諾することになります。

ゲスト ユーザーをディレクトリに追加すると、そのゲスト ユーザーのアカウントは同意状態 (PowerShell で表示可能) になります。これは、最初は **PendingAcceptance** に設定されます。 ゲストが招待を受け入れ、プライバシー ポリシーと利用規約に同意するまで、この設定は維持されます。 その後、同意の状態が**承認済み**に変わり、同意ページはゲストに表示されなくなります。

## <a name="redemption-through-the-invitation-email"></a>招待メールによる利用

[Azure portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) を使用してディレクトリにゲスト ユーザーを追加すると、そのプロセスで招待メールがゲストに送信されます。 ゲスト ユーザーをディレクトリに追加するために [PowerShell を使用する](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell)際に、招待メールを送信するように選択することもできます。 以下は、メールのリンクを利用する場合のゲストのエクスペリエンスの説明です。

1. ゲストは、**Microsoft Invitations** から送信される[招待メール](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements)を受け取ります。
2. ゲストはメールの **[はじめに]** を選択します。
3. ゲストが Azure AD アカウント、Microsoft アカウント (MSA)、あるいはフェデレーション組織のメール アカウントを持っていない場合、MSA の作成を求められます (MSA を必要としない、[ワンタイム パスコード](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)機能が有効になっている場合を除く)。
4. ゲストには、以下に説明されている[同意エクスペリエンス](#consent-experience-for-the-guest)が示されます。

## <a name="redemption-through-a-direct-link"></a>直接リンクによる利用

招待メールの代わりに、アプリまたはポータルへの直接リンクをゲストに提供することができます。 まず、[Azure portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) または [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) を介して、ゲスト ユーザーをディレクトリに追加する必要があります。 その後、直接サインオン リンクを含む、[ユーザーにアプリケーションをデプロイするためのカスタマイズ可能な方法](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences)のいずれかを使用できます。 ゲストには、招待メールではなく直接リンクを使用する際にも、初回の同意エクスペリエンスが示されます。

> [!IMPORTANT]
> 直接リンクはテナントに固有である必要があります。 つまり、共有アプリが配置されている、テナントでゲストを認証できるように、テナント ID または確認済みドメインが含まれている必要があります。 https://myapps.microsoft.com のような一般的な URL では、認証のためにホーム テナントにリダイレクトされるため、ゲスト向けには機能しません。 テナント コンテキストを含む直接リンクの例をいくつか以下に示します。
 > - アプリ アクセス パネル: https://myapps.microsoft.com/?tenantid=&lt;テナント ID&gt; 
 > - 確認済みドメインのアプリ アクセス パネル: https://myapps.microsoft.com/&lt;確認済みドメイン&gt;
 > - Azure portal: https://portal.azure.com/&lt;テナント ID&gt;
 > - 個々のアプリ: [直接サインオン リンク](../manage-apps/end-user-experiences.md#direct-sign-on-links)の使用方法を参照してください

直接リンク経由の招待メールが推奨されるケースがいくつかあります。 このような特殊なケースが組織にとって重要な場合は、引き続き招待メールを送信する方法を使用してユーザーを招待することをお勧めします。
 - ユーザーが Azure AD、MSA、あるいはフェデレーション組織のメール アカウントを持っていません。 ワンタイム パスコード機能を使用していない場合は、ゲストは、MSA の作成手順が示される招待メールを利用する必要があります。
 - 連絡先オブジェクト (Outlook の連絡先オブジェクトなど) と競合するため、招待されたユーザー オブジェクトにメール アドレスを持っていないことがあります。 この場合、ユーザーは招待メールの利用 URL をクリックする必要があります。
 - ユーザーは、招待されたメール アドレスの別名でサインインすることができます (エイリアスは、メール アカウントに関連付けられた追加のメール アドレスです)。この場合、ユーザーは招待メールの利用 URL をクリックする必要があります。

## <a name="consent-experience-for-the-guest"></a>ゲストの同意エクスペリエンス

ゲストがサインインし、初めてパートナー組織のリソースにアクセスすると、以下のページが示されます。 

1. ゲストは、招待元の組織のプライバシーに関する声明について説明されている **[アクセス許可の確認]** ページを確認します。 ユーザーは、操作を続行するために、招待元の組織のプライバシー ポリシーに従って情報を使用することに**同意する**必要があります。

   ![[アクセス許可の確認] ページのスクリーンショット](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > テナント管理者として組織のプライバシー ステートメントにリンクする方法については、[Azure Active Directory に組織のプライバシー情報を追加する方法](https://aka.ms/adprivacystatement)に関するページを参照してください。

2. 利用規約が構成されている場合、ゲストはその利用規約を開き、確認してから、 **[同意]** を選択します。 

   ![新しい利用規約を示すスクリーンショット](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > **[管理]**  >  **[組織の関係]**  >  **[利用規約]** で、[利用規約](../governance/active-directory-tou.md)を構成することができます。

3. 特に指定されていない限り、ゲストはアプリ アクセス パネルにリダイレクトされます。そこには、ゲストがアクセスできるアプリケーションがリスト表示されています。

   ![アプリ アクセス パネルを示すスクリーンショット](media/redemption-experience/myapps.png) 

ディレクトリでは、ゲストの **[招待が受け入れられました]** の値が **[はい]** に変わります。 MSA が作成された場合、ゲストの **[ソース]** には **Microsoft アカウント**が示されます。 ゲスト ユーザー アカウントのプロパティの詳細については、[Azure AD B2B コラボレーション ユーザーのプロパティ](user-properties.md)に関するページを参照してください。 

## <a name="next-steps"></a>次の手順

- [Azure AD B2B コラボレーションとは](what-is-b2b.md)
- [Azure Portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](add-users-administrator.md)
- [インフォメーション ワーカーが B2B コラボレーション ユーザーを Azure Active Directory に追加する方法](add-users-information-worker.md)
- [PowerShell を使用して Azure Active Directory B2B コラボレーション ユーザーを追加する](customize-invitation-api.md#powershell)
- [ゲスト ユーザーとして組織を脱退する](leave-the-organization.md)
