---
title: Azure Active Directory テナントを作成する
description: アプリケーションを登録および構築するために使用する Azure AD テナントを作成する方法について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: quickstart
ms.date: 03/12/2020
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: df873e49e1bc581a32bcb4e4b40b2b24db98cee5
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88120815"
---
# <a name="quickstart-set-up-a-tenant"></a>クイック スタート:テナントを設定する

Microsoft ID プラットフォームでは、開発者がさまざまなカスタムの Microsoft 365 環境と ID を対象とするアプリをビルドできます。 Microsoft ID プラットフォームの使用を開始するには、アプリの登録と管理、Microsoft 365 のデータへのアクセス、およびカスタムの条件付きアクセスとテナント制限のデプロイが可能な環境 (Azure AD テナントとも言う) にアクセスする必要があります。

テナントは、組織を表したものです。 これは、組織やアプリの開発者が、Azure、Microsoft Intune、または Microsoft 365 へのサインアップのような Microsoft とのリレーションシップを作成するときに受信する Azure AD の専用インスタンスです。

Azure AD テナントはそれぞれ異なり、その他の Azure AD とは分離されていて、職場や学校の ID、コンシューマー ID (Azure AD B2C テナントの場合)、およびアプリの登録の独自の表現があります。 テナント内部のアプリの登録では、自分のテナント内のみ、またはすべてのテナント内のアカウントからの認証を許可できます。

## <a name="determining-environment-type"></a>環境の種類の決定

次の 2 種類の環境を作成できます。 必要なものは、アプリが認証するユーザーのタイプのみに基づいて決定します。

* 職場や学校のアカウント (Azure AD アカウント) または Microsoft アカウント (outlook.com や live.com など)
* ソーシャル アカウントとローカル アカウント (Azure AD B2C)

このクイック スタートは、ビルドするアプリの種類に応じて 2 つのシナリオに分けられています。

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>職場や学校のアカウント、または個人用 Microsoft アカウント

### <a name="use-an-existing-tenant"></a>既存のテナントの使用

多くの開発者は、Azure AD テナントに関連付けられたサービスまたはサブスクリプション (Microsoft 365 や Azure サブスクリプションなど) を通じてテナントを既に持っています。

1. テナントを確認するには、アプリケーションの管理に使用したいアカウントで [Azure portal](https://portal.azure.com)にサインインします。
1. 右上隅を確認します。 テナントがある場合、自動的にそのテナントにログインされ、アカウント名のすぐ下でテナント名を確認できます。
   * Azure portal の右上のアカウント名をポイントすると、名前、電子メール、ディレクトリ/テナント ID (GUID)、ドメインが表示されます。
   * アカウントが複数のテナントに関連付けられている場合は、アカウント名を選択してメニューを開き、そこでテナントを切り替えることができます。 各テナントには独自の ID があります。

> [!TIP]
> テナント ID を調べる必要がある場合は、次の操作を実行できます。
> * アカウント名をポイントして、ディレクトリ/テナント ID を取得します。または、
> * Azure portal で、 **[Azure Active Directory] > [プロパティ] > [ディレクトリ ID]** の順に選択します

アカウントに関連付けられた既存のテナントがない場合は、アカウント名の下に GUID が表示されます。次のセクションの手順に従うまで、アプリの登録などのアクションは実行できません。

### <a name="create-a-new-azure-ad-tenant"></a>新しい Azure AD テナントの作成

Azure AD テナントがない場合、または開発用の新しいテナントを作成する場合は、[クイックスタート](../fundamentals/active-directory-access-create-new-tenant.md)を参照するか、[ディレクトリ作成エクスペリエンス](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory)に従ってください。 新しいテナントを作成するには、次の情報を提供する必要があります。

- **組織名**
- **初期ドメイン** - これは *. onmicrosoft.com の一部となります。 ドメインは後でカスタマイズできます。
- **国または地域**

> [!NOTE]
> テナントに名前を付けるときは、英数字を使用してください。 特殊文字は使用できません。 名前は 256 文字を超えてはいけません。

## <a name="social-and-local-accounts"></a>ソーシャル アカウントとローカル アカウント

ソーシャル アカウントとローカル アカウントにサインインするアプリをビルドするには、Azure AD B2C テナントを作成する必要があります。 最初に、[Azure AD B2C テナントの作成](../../active-directory-b2c/tutorial-create-tenant.md)に従ってください。

## <a name="next-steps"></a>次のステップ

* [アプリを登録](quickstart-register-app.md)し、Microsoft ID プラットフォームと統合します。 
* [認証の基本](./authentication-vs-authorization.md)について説明します。
* サブスクリプションと Azure AD テナントの関係について詳しくは、「[Azure サブスクリプションを Azure Active Directory テナントに関連付けるまたは追加する](../fundamentals/active-directory-how-subscriptions-associated-directory.md)」を参照してください。