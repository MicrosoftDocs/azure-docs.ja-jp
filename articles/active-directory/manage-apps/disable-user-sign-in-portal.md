---
title: Azure Active Directory でエンタープライズ アプリのユーザー サインインを無効にする | Microsoft Docs
description: Azure Active Directory で、ユーザーがサインインできないようにエンタープライズ アプリケーションを無効にする方法
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7256791c0b6bfbc72a26f6093cdd3c39410f702f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807601"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory でエンタープライズ アプリのユーザー サインインを無効にする

Azure Active Directory (Azure AD) で、ユーザーがサインインできないようにエンタープライズ アプリケーションを簡単に無効にすることができます。 エンタープライズ アプリを管理するには、適切なアクセス許可が必要です。 また、ディレクトリの全体管理者である必要があります。

## <a name="how-do-i-disable-user-sign-ins"></a>ユーザー サインインを無効にする方法

1. ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。
1. **[すべてのサービス]** を選択し、テキスト ボックスに「**Azure Active Directory**」と入力して、**Enter** キーを押します。
1. **[Azure Active Directory** -  ***directoryname]*** ウィンドウ (管理対象のディレクトリの Azure AD ウィンドウ) で、 **[エンタープライズ アプリケーション]** を選択します。
1. **[エンタープライズ アプリケーション - すべてのアプリケーション]** ウィンドウには、管理できるアプリのリストが表示されます。 アプリを選択します。
1. [***appname***] ウィンドウ (選択したアプリの名前がタイトルに含まれるページ) で、 **[プロパティ]** を選択します。
1. [***appname*** - **プロパティ**] ウィンドウで、 **[ユーザーのサインインが有効になっていますか?]** の **[いいえ]** を選択します。
1. **[保存]** をクリックします。

## <a name="next-steps"></a>次の手順

* [自分のグループをすべて表示する](../fundamentals/active-directory-groups-view-azure-portal.md)
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](assign-user-or-group-access-portal.md)
* [エンタープライズ アプリケーションからユーザーまたはグループの割り当てを削除する](remove-user-or-group-access-portal.md)
* [エンタープライズ アプリケーションの名前またはロゴを変更する](change-name-or-logo-portal.md)
