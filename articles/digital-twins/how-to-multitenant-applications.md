---
title: Azure Digital Twins でマルチテナント アプリケーションを有効にする | Microsoft Docs
description: Azure Digital Twins のマルチテナント Azure Active Directory アプリケーションを構成する方法。
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2019
ms.author: mavoge
ms.openlocfilehash: 2b4f9bf87122f047e496dca1dbd425db8ad7c16c
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119970"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Azure Digital Twins でマルチテナント アプリケーションを有効にする

Azure Digital Twins でソリューションを構築する開発者は、単一のサービスまたはソリューションで複数の顧客をサポートすることが必要になる場合があります。 実際、*マルチテナント* アプリケーションは、最も一般的な Azure Digital Twins 構成の 1 つです。

このドキュメントでは、複数の Azure Active Directory テナントおよび顧客をサポートするように Azure Digital Twins アプリを構成する方法について説明します。

## <a name="multitenancy"></a>マルチテナント

*マルチテナント* リソースは、複数の顧客をサポートする、1 つのプロビジョニング済みインスタンスです。 それぞれの顧客は、固有の独立したデータと特権を持ちます。 各顧客のエクスペリエンスは、アプリケーションのそれぞれの「ビュー」が区別されるように互いに分離されています。

マルチテナントの詳細については、「[Multitenant Applications in Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications)」(Azure でのマルチテナント アプリケーション) を参照してください。

## <a name="problem-scenario"></a>問題のシナリオ

このシナリオでは、Azure Digital Twins ソリューションを構築している開発者 (**DEVELOPER**) とそのソリューションを使用する顧客 (**CUSTOMER**) を考えてみます。

- **DEVELOPER** は、Azure Active Directory テナントを含む Azure サブスクリプションを持っています。
- **DEVELOPER** は、自分の Azure サブスクリプションに Azure Digital Twins インスタンスをデプロイします。 Azure Active Directory は、**DEVELOPER** の Azure Active Directory テナントにサービス プリンシパルを自動的に作成しました。
- **DEVELOPER** の Azure Active Directory テナント内のユーザーは、Azure Digital Twins サービスから [OAuth 2.0 トークン](./security-authenticating-apis.md)を取得できます。
- ここで **DEVELOPER** は、Azure Digital Twins の管理 API と直接統合するモバイル アプリを作成します。
- **DEVELOPER** は、**CUSTOMER** に、そのモバイル アプリケーションの使用を許可します。
- **CUSTOMER** が **DEVELOPER** のアプリケーション内で Azure Digital Twins の管理 API を使用するには、承認される必要があります。

問題:

- **CUSTOMER** が **DEVELOPER** のアプリケーションにログインしたときに、アプリは **CUSTOMER** のユーザーが Azure Digital Twins Management API で認証するためのトークンを取得できません。
- Azure Digital Twins が **CUSTOMER** のディレクトリ内で認識されないことを示す例外が Microsoft Azure Active Directory で発行されます。

## <a name="problem-solution"></a>問題のソリューション

前記の問題シナリオを解決するには、**CUSTOMER** の Azure Active Directory テナント内に Azure Digital Twins のサービス プリンシパルを作成するために、以下のアクションが必要になります。

- **CUSTOMER** が、Azure Active Directory テナントを含む Azure サブスクリプションをまだ持っていない場合:

  - **CUSTOMER** の Azure Active Directory テナント管理者は、[従量課金制の Azure サブスクリプション](https://azure.microsoft.com/offers/ms-azr-0003p/)を取得する必要があります。
  - その後、**CUSTOMER** の Azure Active Directory テナント管理者は、[テナントを新しいサブスクリプションとリンクする](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)必要があります。

- **CUSTOMER** の Azure Active Directory テナント管理者は、[Azure portal](https://portal.azure.com) で以下の手順を実行します。

  1. **[サブスクリプション]** を開きます。
  1. **DEVELOPER** のアプリケーションで使用する Azure Active Directory テナントがあるサブスクリプションを選択します。

     ![Azure Active Directory のサブスクリプション][1]

  1. **[リソース プロバイダー]** を選択します。
  1. **Microsoft.IoTSpaces** を検索します。
  1. **[登録]** を選択します。

     ![Azure Active Directory リソース プロバイダー][2]
  
## <a name="next-steps"></a>次の手順

- Azure Digital Twins でユーザー定義関数を使用する方法の詳細については、[Azure Digital Twins のユーザー定義関数の作成方法](./how-to-user-defined-functions.md)に関するページを参照してください。

- ロールベースのアクセス制御を使用して、ロールの割り当てによってアプリケーションのセキュリティ保護を強化する方法については、[Azure Digital Twins のロールベースのアクセス制御の作成および管理方法](./security-create-manage-role-assignments.md)に関するページを参照してください。

<!-- Images -->
[1]: media/multitenant/ad-subscriptions.png
[2]: media/multitenant/ad-resource-providers.png
