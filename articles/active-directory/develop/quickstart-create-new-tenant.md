---
title: クイックスタート:Azure Active Directory テナントを作成する
titleSuffix: Microsoft identity platform
description: このクイックスタートでは、認証と認可に Microsoft ID プラットフォームを使用するアプリケーションの開発に使用する Azure Active Directory テナントを作成する方法について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: quickstart
ms.date: 02/15/2021
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: cb4995c0c71d0bcebb3cdd8ec471a92b7820cb9d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100535163"
---
# <a name="quickstart-set-up-a-tenant"></a>クイック スタート:テナントを設定する

ID とアクセスの管理に Microsoft ID プラットフォームを使用するアプリを構築するには、Azure Active Directory (Azure AD) "*テナント*" にアクセスする必要があります。 アプリの登録と管理、Microsoft 365 や他の Web API のデータへのアクセスの構成、条件付きアクセスなどの機能の有効化は、Azure AD テナント内で行います。

テナントは組織を表します。 これは、組織やアプリ開発者が Microsoft との関係を築いたときに受け取る Azure AD の専用インスタンスです。 この関係は、たとえば、Azure、Microsoft Intune、または Microsoft 365 へのサインアップから始めることができます。

各 Azure AD テナントは独立しており、他の Azure AD テナントとは切り離されています。 テナントには、職場および学校の ID、コンシューマー ID (Azure AD B2C テナントの場合)、アプリの登録の独自の表現があります。 テナント内のアプリの登録では、自分のテナント内のみまたはすべてのテナント内のアカウントからの認証を許可できます。

## <a name="prerequisites"></a>前提条件

アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="determining-the-environment-type"></a>環境の種類の決定

2 種類の環境を作成できます。 環境は、アプリが認証するユーザーの種類だけで決まります。 

このクイックスタートは、構築するアプリの種類に応じて、次の 2 つのシナリオに対応しています。

* 職場および学校 (Azure AD) アカウントまたは Microsoft アカウント (outlook.com や live.com など)
* ソーシャルおよびローカル (Azure AD B2C) アカウント

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>職場や学校のアカウント、または個人用 Microsoft アカウント

職場および学校アカウントまたは個人用 Microsoft アカウント向けの環境を構築するには、既存の Azure AD テナントを使用するか、新しいテナントを作成します。
### <a name="use-an-existing-azure-ad-tenant"></a>既存の Azure AD テナントの使用

多くの開発者は、Azure AD テナントに関連付けられたサービスまたはサブスクリプション (Microsoft 365 や Azure サブスクリプションなど) を通じてテナントを既に持っています。

テナントを確認するには、次の手順を実行します。

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。 アプリケーションの管理に使用するアカウントを使用します。
1. 右上隅を確認します。 テナントがある場合は、自動的にサインインされます。 テナント名は、アカウント名のすぐ下に表示されます。
   * アカウント名をポイントすると、名前、メール アドレス、ディレクトリまたはテナント ID (GUID)、ドメインが表示されます。
   * アカウントが複数のテナントに関連付けられている場合は、アカウント名を選択してメニューを開き、そこでテナントを切り替えることができます。 各テナントには独自の ID があります。

> [!TIP]
> テナント ID を確認するには、次の操作を行います。
> * アカウント名をポイントして、ディレクトリまたはテナント ID を表示します。
> * Azure portal で、 **[Azure Active Directory]**  >  **[プロパティ]**  >  **[テナント ID]** を探して選択します。

アカウントにテナントが関連付けられていない場合は、アカウント名の下に GUID が表示されます。 Azure AD テナントを作成するまで、アプリの登録などの操作は実行できません。

### <a name="create-a-new-azure-ad-tenant"></a>新しい Azure AD テナントの作成

Azure AD テナントがまだない場合や、開発用の新しいテナントを作成する場合は、[Azure AD での新しいテナントの作成](../fundamentals/active-directory-access-create-new-tenant.md)に関する記事をご覧ください。 または、Azure portal で[ディレクトリ作成エクスペリエンス](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory)を使用します。 

新しいテナントを作成するには、次の情報を入力します。

- **組織名**
- **初期ドメイン** - このドメインは、*.onmicrosoft.com の一部です。 ドメインは後でカスタマイズできます。
- **国または地域**

> [!NOTE]
> テナントに名前を付けるときは、英数字を使用してください。 特殊文字は使用できません。 名前は 256 文字を超えてはいけません。

## <a name="social-and-local-accounts"></a>ソーシャル アカウントとローカル アカウント

ソーシャルおよびローカル アカウントにサインインするアプリを構築するには、Azure AD B2C テナントを作成します。 開始するには、[Azure AD B2C テナントの作成](../../active-directory-b2c/tutorial-create-tenant.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> Microsoft ID プラットフォームと統合する[アプリを登録](quickstart-register-app.md)します。
