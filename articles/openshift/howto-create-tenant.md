---
title: Azure Red Hat OpenShift 用の Azure AD テナントの作成
description: Microsoft Azure Red Hat OpenShift クラスターをホストする Azure Active Directory (Azure AD) テナントを作成する方法について説明します。
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: b98f02adeb850f16127658c7d02d44754512e216
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100635011"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift 用の Azure AD テナントの作成

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 は、2022 年 6 月 30 日に廃止されます。 新しい Azure Red Hat OpenShift 3.11 クラスターの作成のサポートは、2020 年 11 月 30 日まで継続されます。 廃止された後、残っている Azure Red Hat OpenShift 3.11 クラスターは、セキュリティの脆弱性を防ぐためにシャットダウンされます。
> 
> このガイドに従って、[Azure Red Hat OpenShift 4 クラスターを作成](tutorial-create-cluster.md)します。
> ご質問がある場合は、[お問い合わせください](mailto:arofeedback@microsoft.com)。

Microsoft Azure Red Hat OpenShift には、その中に自分のクラスターを作成するための [Azure Active Directory (Azure AD)](../active-directory/develop/quickstart-create-new-tenant.md) テナントが必要です。 *テナント* は、組織やアプリの開発者が、Azure、Microsoft Intune、または Microsoft 365 へのサインアップのような Microsoft とのリレーションシップを作成するときに受信する Azure AD の専用インスタンスです。 Azure AD テナントはそれぞれ異なり、他の Azure AD テナントとは分離されていて、職場や学校の ID とアプリの登録は個別です。

Azure AD テナントをまだお持ちでない場合は、次の手順に従って作成してください。

## <a name="create-a-new-azure-ad-tenant"></a>新しい Azure AD テナントの作成

テナントを作成するには:

1. Azure Red Hat OpenShift クラスターと関連付けるアカウントを使用して [Azure portal](https://portal.azure.com/) にサインインします。
2. 新しいテナント (新しい "*Azure Active Directory*" とも呼ばれる) を作成するために [Azure Active Directory ブレード](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory)を開きます。
3. **[組織名]** を入力します。
4. **初期ドメイン名** を入力します。 これには *onmicrosoft.com* が追加されます。 ここで "*組織名*" の値を再利用できます。
5. テナントが作成される国またはリージョンを選択します。
6. **Create** をクリックしてください。
7. Azure AD テナントが作成されたら、**[Click here to manage your new directory]\(ここをクリックして、新しいディレクトリを管理します\)** のリンクを選択します。 Azure portal の右上に新しいテナントの名前が表示されます。  

    ![右上にテナント名が表示された portal のスクリーンショット][tenantcallout]  

8. 後で Azure Red Hat OpenShift クラスターを作成する場所を指定できるように、*[テナント ID]* をメモしておいてください。 portal に新しいテナントの Azure Active Directory 概要ブレードが表示されているはずです。 **[プロパティ]** を選択し、**[ディレクトリ ID]** の値をコピーします。 [Azure Red Hat OpenShift クラスターの作成](tutorial-create-cluster.md)のチュートリアルでは、この値を `TENANT` と呼びます。

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>リソース

[Azure AD テナント](../active-directory/develop/quickstart-create-new-tenant.md)の詳細については、[Azure Active Directory のドキュメント](../active-directory/index.yml)を参照してください。

## <a name="next-steps"></a>次の手順

サービス プリンシパルの作成、クライアント シークレットと認証コールバック URL の生成、Azure Red Hat OpenShift クラスター上でアプリをテストするための Active Directory ユーザーの新規作成の方法を学習します。

[Azure AD アプリ オブジェクトとユーザーの作成](howto-aad-app-configuration.md)