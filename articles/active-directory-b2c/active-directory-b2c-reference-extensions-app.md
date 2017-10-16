---
title: "Extensions アプリ - Azure AD B2C | Microsoft Docs"
description: "b2c-extensions-app の復元"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: f0392e32-0771-473c-a799-81438ca2bcff
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/06/2017
ms.author: parja
ms.openlocfilehash: a2b883ad010b93ec83c273988493412c06597641
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: Extensions アプリ

Azure AD B2C ディレクトリが作成されるとき、`b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` というアプリがその新しいディレクトリ内に自動で作成されます。 このアプリは **b2c-extensions-app** と呼ばれ、*[アプリの登録]* に表示されます。 Azure AD B2C はこのアプリを使用して、ユーザーとカスタム属性の情報を保存します。 このアプリを削除してしまうと、Azure AD B2C が正しく機能しなくなり、運用環境に影響が出ます。

> [!IMPORTANT]
> テナントをすぐに削除する予定がなければ、b2c-extensions-app を削除しないでください。 アプリが削除された状態で 30 日以上が経過すると、ユーザー情報が完全に失われます。

## <a name="verifying-that-the-extensions-app-is-present"></a>Extensions アプリがあることの確認

b2c-extensions-app があることを確認するには:

1. Azure AD B2C テナント内で、左側のナビゲーション メニューにある **[More services]\(その他のサービス\)** をクリックします。
1. **[アプリの登録]** を探して開きます。
1. **b2c-extensions-app** で始まる名前のアプリを探します。

## <a name="recover-the-extensions-app"></a>b2c-extensions-app の回復

b2c-extensions-app を誤って削除してしまった場合でも、30 日以内なら回復できます。 Graph API を使用してこのアプリを復元できます。

1. [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/) をご覧ください。
1. グローバル管理者としてサイトにログインし、削除したアプリの復元場所にする Azure AD B2C ディレクトリに移動します。 このグローバル管理者は、次のような電子メール アドレスを持つ必要があります: `username@{yourTenant}.onmicrosoft.com`。
1. `https://graph.windows.net/myorganization/deletedApplications` という URL に対して、api-version=1.6 で HTTP GET を発行します。 この操作を行うと、過去 30 日以内に削除されたすべてのアプリケーションの一覧が表示されます。
1. 一覧の中から 'b2c-extension-app’ から始まる名前のアプリケーションを探して、その `objectid` プロパティの値をコピーします。
1. `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore` という URL に対して、HTTP POST を発行します。 URL の `{OBJECTID}` という部分を、前の手順で説明した `objectid` に置き換えます。 

Azure Portal で[復元されたアプリを確認](#verifying-that-the-extensions-app-is-present)できるようになりました。

> [!NOTE]
> 復元できるのは過去 30 日以内に削除されたアプリケーションのみです。 30 日以上経過してしまうと、データは完全に失われます。 さらにヘルプが必要な場合は、サポート チケットをご提出ください。