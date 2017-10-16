---
title: "Azure Data Catalog の前提条件 | Microsoft Docs"
description: "Azure Data Catalog を使用するために必要な前提条件について説明します。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: ef497a54-dc4d-4820-b5bf-c361b64b964d
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: 3fdef7bb58a5cd5dfbe4d37d9baf9c8e392ebe42
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-data-catalog-prerequisites"></a>Azure Data Catalog の前提条件

Azure Data Catalog をセットアップする前に、いくつかのことに注意する必要があります。 このプロセスにはそれほど時間はかからないので、ご安心ください。

## <a name="azure-subscription"></a>Azure サブスクリプション
Data Catalog をセットアップするには、Azure サブスクリプションの所有者または共同所有者であることが必要です。

Azure サブスクリプションは、Data Catalog のようなクラウド サービス リソースへのアクセスを整理するために役立ちます。 さらに、リソースの使用状況の報告、課金、および支払い方法の制御にも役立ちます。 サブスクリプションごとに異なる課金および支払いを設定することができるため、部門別、プロジェクト別、支社別などで、異なるサブスクリプションやプランを利用することができます。 すべてのクラウド サービスがサブスクリプションに属しているため、Data Catalog をセットアップする前に、サブスクリプションが必要です。 詳細については、 [アカウント、サブスクリプション、管理ロールの管理](../active-directory/active-directory-assign-admin-roles.md)に関するページを参照してください。

## <a name="azure-active-directory"></a>Azure Active Directory
Data Catalog をセットアップするには、Azure Active Directory (Azure AD) ユーザー アカウントでサインインする必要があります。

Azure AD は、企業がクラウドとオンプレミス環境の両方で ID とアクセスを簡単に管理できるサービスです。 ユーザーは、任意のクラウドおよびオンプレミス Web アプリケーションへのシングル サインインのために、1 つの職場または学校アカウントを使用できます。 Data Catalog は、Azure AD を使用してサインインを認証します。 詳細については、「[Azure Active Directory とは](../active-directory/active-directory-whatis.md)」を参照してください。

> [!NOTE]
> [Azure Portal](http://portal.azure.com/) を使用すると、個人の Microsoft アカウント、または Azure Active Directory の職場や学校のアカウントを使用してサインインできます。 Azure Portal または [Data Catalog ポータル](http://www.azuredatacatalog.com) を使用して Data Catalog を設定するには、個人用アカウントではなく、Azure Active Directory アカウントを使用してサインインする必要があります。
>
>

## <a name="active-directory-policy-configuration"></a>Active Directory ポリシーの構成
Data Catalog ポータルにはサインインできるものの、データ ソース登録ツールにサインインしようとするとエラー メッセージが表示され、サインインできないという状況に直面することがあります。 このエラー動作は、企業ネットワーク上にいる場合、または企業ネットワークの外部から接続している場合にのみ発生することがあります。

データ ソース登録ツールは、フォーム認証を使用して、Active Directory に対するユーザーの資格情報を検証します。 サインインを成功させるためには、Active Directory 管理者が、グローバル認証ポリシーでフォーム認証を有効にする必要があります。

グローバル認証ポリシーでは、次のスクリーンショットに示すように、イントラネット接続とエクストラネット接続で個別に認証を有効にすることができます。 サインイン エラーは、接続元のネットワークで、フォーム認証が有効にされていない場合に発生する可能性があります。

 ![Active Directory グローバル認証ポリシー](./media/data-catalog-prerequisites/global-auth-policy.png)

## <a name="next-steps"></a>次のステップ
詳細については、「 [認証ポリシーの構成](https://technet.microsoft.com/library/dn486781.aspx)」を参照してください。
