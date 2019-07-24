---
title: Azure Active Directory でユーザーのエクスペリエンスからアプリケーションを非表示にする | Microsoft Docs
description: Azure Active Directory アクセス パネルまたは Office 365 ランチャーでユーザーのエクスペリエンスからアプリケーションを非表示にする方法
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: celested
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59c4fa0149b4a8f59dc4fa1e30936b35c1a5ac90
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56191760"
---
# <a name="hide-applications-from-end-users-in-azure-active-directory"></a>Azure Active Directory のエンドユーザーに対してアプリケーションを非表示にする

エンドユーザーの MyApps パネルや Office 365 ランチャーにアプリケーションが表示されないようにする方法について説明します。 アプリケーションを非表示にしても、アプリケーションに対するユーザーのアクセス許可は維持されます。 

## <a name="prerequisites"></a>前提条件

MyApps パネルと Office 365 ランチャーにアプリケーションが表示されないようにするには、アプリケーション管理者特権が必要です。

すべての Office 365 アプリケーションを非表示にするは、グローバル管理者特権が必要です。


## <a name="hide-an-application-from-the-end-user"></a>エンドユーザーに対してアプリケーションを非表示にする
MyApps パネルと Office 365 アプリケーション ランチャーにアプリケーションが表示されないようにするには、次の手順を使用します。

1.  ディレクトリのグローバル管理者として [Azure portal](https://portal.azure.com) にサインインします。
2.  **[Azure Active Directory]** を選択します。
3.  **[エンタープライズ アプリケーション]** を選択します。 **[Enterprise applications - All applications (エンタープライズ アプリケーション - すべてのアプリケーション)]** ブレードが開きます。
4.  **[アプリケーションの種類]** で、**[エンタープライズ アプリケーション]** を選択します (まだ選択されていない場合)。
5.  非表示にするアプリケーションを検索し、アプリケーションをクリックします。  アプリケーションの概要が表示されます。
6.  **[プロパティ]** をクリックします。 
7.  **[ユーザーに表示しますか?]** という質問に対し、**[いいえ]** をクリックします。
8.  **[Save]** をクリックします。


## <a name="hide-office-365-applications-from-the-myapps-panel"></a>MyApps パネルに Office 365 アプリケーションが表示されないようにする

MyApps パネルに一切の Office 365 アプリケーションが表示されないようにするには、次の手順を使用します。 なお、アプリケーションは Office 365 ポータルには表示されます。

1.  ディレクトリのグローバル管理者として [Azure portal](https://portal.azure.com) にサインインします。
2.  **[Azure Active Directory]** を選択します。
3.  **[ユーザー設定]** を選択します。
4.  **[エンタープライズ アプリケーション]** で、**[エンド ユーザーがアプリケーションを起動して表示する方法を管理する]** をクリックします。
5.  **[ユーザーは Office 365 ポータルでのみ Office 365 アプリを表示できる]** で、**[はい]** をクリックします。
6.  **[Save]** をクリックします。


## <a name="next-steps"></a>次の手順
* [自分のグループをすべて表示する](../fundamentals/active-directory-groups-view-azure-portal.md)
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](assign-user-or-group-access-portal.md)
* [エンタープライズ アプリケーションからユーザーまたはグループの割り当てを削除する](remove-user-or-group-access-portal.md)
* [エンタープライズ アプリケーションの名前またはロゴを変更する](change-name-or-logo-portal.md)

