---
title: 会社の職場アカウントとパートナー センター
description: 会社が Microsoft を使用して職場アカウントを設定しているかどうかを確認する方法、新しい職場アカウントを作成する方法、または複数の職場アカウントを設定してパートナー センターで使用する方法。
author: ChJenk
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 744b4e4975c5d4cec5c705e639e9cc8d252a7dd6
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194281"
---
# <a name="company-work-accounts-and-partner-center"></a>会社の職場アカウントとパートナー センター

パートナー センターでは、会社の職場アカウント (Azure Active Directory (AD) テナントとも呼ばれます) を使用して、複数のユーザーのアカウント アクセス、アクセス許可の制御、グループとアプリケーションのホスト、プロファイル データの維持を行います。 職場の電子メール アカウント ドメインをパートナー センター アカウントにリンクすることにより、会社の従業員は、独自の職場アカウントのユーザー名とパスワードを使用してパートナー センターにサインインし、Marketplace のオファーを管理できるようになります。

## <a name="check-whether-your-company-already-has-a-work-account"></a>会社が既に職場アカウントを持っているかどうかを確認する

会社が Azure、Microsoft Intune、Office 365 などの Microsoft クラウド サービスにサブスクライブしている場合、パートナー センターで使用できる職場の電子メール アカウント ドメイン (Azure Active Directory テナントとも呼ばれます) は既にあります。

確認するには、次の手順に従います。
1. [https://portal.azure.com](https://portal.azure.com ) から Azure 管理ポータルにサインインします。
2. 左側のナビゲーション メニューから **[Azure Active Directory]** を選択し、 **[カスタム ドメイン名]** を選択します。
3. 既に職場アカウントがある場合は、ドメイン名が表示されます。

会社の職場アカウントがまだない場合は、パートナー センターの登録プロセス中に作成されます。

## <a name="set-up-multiple-work-accounts"></a>複数の職場アカウントを設定する

既存の職場アカウントを使用する前に、職場アカウントで何人のユーザーがパートナー センターにアクセスする必要があるかを検討してください。 パートナー センターにアクセスする必要がないユーザーが職場アカウントに含まれている場合は、複数の職場アカウントを作成して、パートナー センターにアクセスする必要があるユーザーのみが特定のアカウントに表示されるようにすることを検討します。

## <a name="create-a-new-work-account"></a>新しい職場アカウントを作成する

会社の新しい職場アカウントを作成するには、次の手順に従います。 会社の Microsoft Azure アカウントに対する管理アクセス許可を持つすべてのユーザーに協力を要請する必要があります。

1. [Microsoft Azure ポータル](https://portal.azure.com)にサインインします。
2. 左側のナビゲーション メニューで、 **[Azure Active Directory]**  ->  **[ユーザー]** を選択します。
3. **[新しいユーザー]** を選択し、名前と電子メール アドレスを入力して、新しい Azure 職場アカウントを作成します。 **[ディレクトリ ロール]** が **[ユーザー]** に設定されていることを確認し、下部にある **[パスワードを表示]** チェック ボックスを選択して、自動生成されたパスワードを表示してメモしておきます。
4. **[作成]** を選択して、新しいユーザーを保存します。

このユーザー アカウントの電子メール アドレスは、ご使用のディレクトリで確認済みのドメイン名である必要があります。 左側のナビゲーション メニューで、 **[Azure Active Directory]**  ->  **[カスタム ドメイン名]** を選択することによって、ディレクトリで確認済みのドメインをすべて一覧表示できます。

Azure Active Directory にカスタム ドメインを追加する方法の詳細については、[Azure AD へのドメインの追加または関連付け](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain)に関するページをご覧ください。

## <a name="troubleshoot-work-email-sign-in"></a>職場の電子メールによるサインインのトラブルシューティング

職場アカウント (Azure AD テナントとも呼ばれます) にサインインする際に問題が発生した場合は、状況に最も当てはまるシナリオを下のダイアグラムで見つけ、推奨される手順に従ってください。

![職場アカウントによるサインインに関するトラブルシューティングのダイアグラム](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>次の手順

- [パートナー センターで Commercial Marketplace アカウントを管理する](./manage-account.md) 
