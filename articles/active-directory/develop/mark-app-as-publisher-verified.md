---
title: アプリを発行者確認済みにする - Microsoft ID プラットフォーム | Microsoft
description: アプリを発行者確認済みとしてマークする方法を説明します。 アプリケーションが発行者確認済みとしてマークされている場合は、Microsoft Partner Network アカウントを使用する ID の確認プロセスが完了していることを発行者が確認し、発行者がこの MPN アカウントをアプリケーションの登録に関連付けていることを意味します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 2b658cdc58777274bb14f9e8069cef2facdb0479
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92363455"
---
# <a name="mark-your-app-as-publisher-verified"></a>アプリを発行者確認済みとしてマークする

アプリの登録に確認済み発行元が含まれている場合は、アプリの発行者が Microsoft Partner Network (MPN) アカウントを使用して自身の ID を[確認](/partner-center/verification-responses)し、この MPN アカウントと自身のアプリの登録を関連付けていることを意味します。 この記事では、[発行者の確認](publisher-verification-overview.md)プロセスを完了する方法について説明します。

## <a name="quickstart"></a>クイック スタート
Microsoft Partner Network (MPN) に既に登録していて、[前提条件](publisher-verification-overview.md#requirements)を満たしている場合は、すぐに始めることができます。 

1. [多要素認証](../fundamentals/concept-fundamentals-mfa-get-started.md)を使用して、[アプリ登録ポータル](https://aka.ms/PublisherVerificationPreview)にサインインします

1. アプリを選択し、 **[Branding]\(ブランド\)** をクリックします。 

1. **[Add MPN ID to verify publisher]\(発行者を確認するための MPN ID を追加する\)** をクリックし、表示される要件を確認します。

1. 自分の MPN ID を入力し、 **[Verify and save]\(確認して保存\)** をクリックします。

具体的な利点、要件、およびよく寄せられる質問の詳細については、[概要](publisher-verification-overview.md)に関するページを参照してください。


## <a name="mark-your-app-as-publisher-verified"></a>アプリを発行者確認済みとしてマークする
[前提条件](publisher-verification-overview.md#requirements)が満たされていることを確認した後、以下の手順に従って、アプリを発行者確認済みとしてマークします。  

1. [多要素認証](../fundamentals/concept-fundamentals-mfa-get-started.md)を使用して発行者確認済みとしてマークするアプリを変更することが許可されている組織 (Azure AD) アカウントでサインインし、パートナー センターに MPN アカウントでサインインしていることを確認します。

    - Azure AD では、このユーザーは次の[ロール](../roles/permissions-reference.md)のいずれかに属する必要があります。アプリケーション管理者、クラウド アプリケーション管理者、全体管理者。 

    - パートナー センターでは、このユーザーには次のいずれかの[ロール](/partner-center/permissions-overview)が必要です: MPN 管理者、アカウント管理者、全体管理者 (これは、Azure AD で管理されている共有ロールです)。 

1. アプリ登録ポータルに移動します。  

1. 発行者確認済みとしてマークするアプリをクリックし、[Branding]\(ブランド化\) ブレードを開きます。 

1. アプリの[パブリッシャー ドメイン](howto-configure-publisher-domain.md)が設定されていることを確認します。 

1. テナントのパブリッシャー ドメインまたは DNS 検証済みの[カスタム ドメイン](../fundamentals/add-custom-domain.md)のいずれかが、MPN アカウントの検証プロセスで使用されたメール アドレスのドメインと一致することを確認します。

1. ページの下部近くにある **[Add MPN ID to verify publisher]\(発行者を確認するための MPN ID を追加する\)** をクリックします。 

1. **[MPN ID]** を入力します。 この MPN ID は以下に対するものである必要があります。 

    - 確認プロセスが完了している有効な Microsoft Partner Network アカウント。  

    - 組織のパートナー グローバル アカウント (PGA)。 

1. **[Verify and save]\(確認して保存\)** をクリックします。 

1. 要求が処理されるまで待ちます。これには数分かかることがあります。 

1. 確認が成功した場合は、発行者確認ウィンドウが閉じ、[Branding]\(ブランド化\) ブレードに戻ります。 確認済みの **[Publisher display name]\(発行者の表示名\)** の横に、青い確認済みバッジが表示されます。 

1. アプリへの同意を求めるメッセージが表示されるユーザーに対しては、プロセスが正常に終了した直後から、バッジが表示されるようになります。ただし、この情報がシステム全体に複製されるまで、少し時間がかかる場合があります。 

1. アプリケーションにサインインし、確認済みバッジが同意画面に表示されることを確認して、この機能をテストします。 アプリへの同意を既に許可しているユーザーとしてサインインしている場合は、*prompt=consent* クエリ パラメーターを使用して、同意プロンプトを強制することができます。 このパラメーターはテストのみに使用し、アプリの要求にはハードコーディングしないでください。

1. バッジを表示する追加のアプリについて、必要に応じてこのプロセスを繰り返します。 Microsoft Graph を使用してこの処理を一括ですばやく行うことができ、間もなく PowerShell コマンドレットが利用可能になります。 詳細については、「[Microsoft Graph API の呼び出しを行う](troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls)」を参照してください。 

これで終了です。 プロセス、結果、または一般的な機能に関するフィードバックがある場合は、お知らせください。 

## <a name="next-steps"></a>次のステップ
問題が発生する場合は、[トラブルシューティング情報](troubleshoot-publisher-verification.md)をお読みください。
