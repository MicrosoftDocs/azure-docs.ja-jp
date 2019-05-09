---
title: 他の人と共同作業を行う
titleSuffix: Language Understanding - Azure Cognitive Services
description: アプリの所有者は、コラボレーターをアプリに追加できます。 これらの共同作業者は、アプリのモデル作成、トレーニング、および発行が可能です。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/12/2019
ms.author: diberry
ms.openlocfilehash: f23212a854fb37dda89fd2bf6b223cf0dc69526b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56167577"
---
# <a name="how-to-manage-authors-and-collaborators"></a>作成者とコラボレーターを管理する方法 

アプリの所有者は、コラボレーターをアプリに追加できます。 これらの共同作業者は、アプリのモデル作成、トレーニング、および発行が可能です。 

<a name="owner-and-collaborators"></a>

## <a name="add-collaborator"></a>コラボレーターの追加

アプリの作成者は 1 人 (所有者) ですが、コラボレーターは複数いてもかまいません。 コラボレーターが LUIS アプリを編集できるようにするには、コラボレーターの一覧に、LUIS ポータルへのアクセスに使用する電子メールを追加する必要があります。 追加されると、LUIS ポータルにアプリが表示されます。

1. 右上のメニューから **[管理]** を選択してから、左側のメニューで **[コラボレーター]** を選択します。

2. ツールバーから **[Add Collaborator]\(コラボレーターの追加)** を選択します。

    [![コラボレーターの追加](./media/luis-how-to-collaborate/add-collaborator.png "コラボレーターの追加")](./media/luis-how-to-collaborate/add-collaborator.png#lightbox)

3. コラボレーターが LUIS ポータルにサインインするために使用するメール アドレスを入力します。

    ![コラボレーターのメール アドレスを追加する](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)

## <a name="transfer-of-ownership"></a>所有権の移転

現在、LUIS では、所有権の移転はサポートされていませんが、アプリをエクスポートし、そのアプリを別の LUIS ユーザーがインポートできます。 この 2 つのアプリケーション間では LUIS スコアに若干の違いがある可能性があります。 

## <a name="azure-active-directory-resources"></a>Azure Active Directory リソース

組織で [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (Azure AD) を使用する場合、Language Understanding (LUIS) には、ユーザーが LUIS を使用するときのユーザー アクセスに関する情報に対するアクセス許可が必要です。 LUIS に必要なリソースはごくわずかです。 

以下に関する管理者の同意など、管理者の同意があるか、管理者の同意が不要のアカウントでサインアップしようとしたときに、詳細な説明が表示されます。

* 組織のアカウントでアプリにサインインし、アプリにユーザーのプロファイルを読み取らせることを許可します。 基本的な会社情報の読み取りもアプリに許可します。 これにより、ユーザー ID、電子メール、名前などの基本的なプロファイル データを読み取るアクセス許可を LUIS に与えます
* ユーザーが現在アプリを使用していないときでも、ユーザーのデータの参照と更新をアプリに許可します。 このアクセス許可は、ユーザーのアクセス トークンを更新するために必要です。


## <a name="azure-active-directory-tenant-user"></a>Azure Active Directory テナント ユーザー

LUIS では、Azure Active Directory (Azure AD) の標準的な同意フローが使用されます。 

テナント管理者は、Azure AD で LUIS を使用するためのアクセス許可を必要とするユーザーと直接的に協力する必要があります。 

* まず、ユーザーが LUIS にサインインすると、管理者の承認を必要とするポップアップ ダイアログ ボックスが表示されます。 ユーザーは、続行する前にテナント管理者に問い合わせます。 
* 次に、テナント管理者が LUIS にサインインすると、同意フロー ポップアップ ダイアログが表示されます。 これは、管理者がユーザーにアクセス許可を与えるために必要なダイアログです。 管理者がアクセス許可を承認すると、ユーザーは LUIS の使用を続行できます。 テナント管理者は、次のスクリーンショットに示すように、LUIS にサインインしなくても LUIS のための[同意](https://account.activedirectory.windowsazure.com/r#/applications)にアクセスできます。 この一覧はフィルター処理されて、名前 `LUIS` を含む項目になっていることに注目してください。

![アプリ Web サイトによる Azure Active Directory のアクセス許可](./media/luis-how-to-collaborate/tenant-permissions.png)

テナント管理者が、特定のユーザーのみに LUIS を使わせたい場合は、いくつかの可能性のあるソリューションがあります。
* "管理者の同意" を付与します (Azure AD のすべてのユーザーに同意する) が、エンタープライズ アプリケーションのプロパティの下の [ユーザーの割り当てが必要ですか] を [はい] に設定し、最後に目的のユーザーのみをアプリケーションに割り当て/追加します。 この方法では、管理者はアプリに "管理者の同意" を与えますが、それにアクセスできるユーザーを制御できます。
* 2 つ目のソリューションは、[Azure AD Graph API](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) を使用して、特定の各ユーザーに同意を与えることです。 

Azure Active Directory ユーザーと同意の詳細を確認します。 
* [ご利用のアプリを特定のユーザー セットに制限する](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md)

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>コラボレーター用の複数の電子メールを持つユーザー アカウント

LUIS アプリにコラボレーターを追加する場合は、コラボレーターがコラボレーターとして LUIS を使用するために必要な正確な電子メール アドレスを指定します。 Azure Active Directory (Azure AD) では、1 人のユーザーが複数のメール アカウントを交互に使用できますが、LUIS では、コラボレーターの一覧に指定されたメール アドレスを使用してサインインする必要があります。

