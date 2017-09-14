---
title: "ライセンス: Azure AD SSPR | Microsoft Docs"
description: "Azure AD のセルフ サービスによるパスワード リセットのライセンス要件"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 3ed13c819f8e32cab44013cdcbf1b3a921ba98b8
ms.contentlocale: ja-jp
ms.lasthandoff: 05/03/2017

---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Azure AD のセルフ サービスによるパスワード リセットのライセンス要件

Azure AD のパスワード リセットが機能するには、**組織で少なくとも 1 つのライセンスが割り当てられている必要があります**。 パスワード リセット エクスペリエンスでユーザーごとのライセンスを適用することはありません。 Microsoft ライセンス契約とのコンプライアンスを維持するには、プレミアム機能を使用するすべてのユーザーにライセンスを割り当てる必要があります。

* **クラウド ユーザーのみ** - Office 365 (O365) のすべての有料 SKU、または Azure AD Basic
* **クラウド**および/または**内部設置型ユーザー** - Azure AD Premium P1 または P2、Enterprise Mobility + Security (EMS)、または Secure Productive Enterprise (SPE)

## <a name="licenses-required-for-password-writeback"></a>パスワード ライトバックで必要なライセンス

パスワード ライトバックを使用するのには、次のライセンスのいずれかがテナントに割り当てられている必要があります。

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3
* Enterprise Mobility + Security E5
* Secure Productive Enterprise E3
* Secure Productive Enterprise E5

> [!NOTE]
> スタンドアロンの Office 365 ライセンス プランは、**パスワード ライトバックをサポートしていません**。この機能を動作させるには、上記プランのいずれかが必要になります。

追加のライセンス情報 (コストを含む) については、以下のページをご覧ください。

* [Azure Active Directory の料金サイト](https://azure.microsoft.com/pricing/details/active-directory/)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Secure Productive Enterprise](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

## <a name="enable-group-or-user-based-licensing"></a>グループまたはユーザー ベースのライセンスの有効化

現在、Azure AD はグループ ベースのライセンスをサポートしています。これにより、管理者はライセンスを一度に 1 つずつユーザーに割り当てるのではなく、ライセンスをユーザーのグループに一括して割り当てることができます。 [ライセンスの割り当て、検証、および問題の解決](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses)

一部の Microsoft サービスは、すべての場所で利用できないことがあります。 ライセンスをユーザーに割り当てる前に、管理者はユーザーの [利用場所] プロパティを指定しておく必要があります。 ライセンスの割り当ては、Azure Portal の [ユーザー] > [プロファイル] > [設定] セクションで行います。 **グループ ライセンス割り当てを使用するとき、利用場所が指定されていないユーザーは、ディレクトリの場所を継承します。**

## <a name="next-steps"></a>次のステップ

次のリンク先では、Azure AD を使用したパスワードのリセットに関する追加情報が得られます。

* [**クイック スタート**](active-directory-passwords-getting-started.md) - Azure AD のセルフ サービスによるパスワードのリセットの管理を始めることができます。 
* [**データ**](active-directory-passwords-data.md) - パスワード管理に必要なデータとその使用方法がわかります
* [**展開**](active-directory-passwords-best-practices.md) - ここで見つかるガイダンスを使用してユーザーに対する SSPR を計画してデプロイできます
* [**カスタマイズ**](active-directory-passwords-customize.md) - 会社の SSPR エクスペリエンスの外観をカスタマイズします。
* [**レポート**](active-directory-passwords-reporting.md) - ユーザーが SSPR 機能にアクセスしたかどうかや、アクセスしたタイミングと場所を検出します
* [**技術的詳細**](active-directory-passwords-how-it-works.md) - しくみを詳しく説明しています
* [**よく寄せられる質問**](active-directory-passwords-faq.md) - 方法は? なぜですか? 何ですか? どこですか? 誰ですか? いつですか? - ずっと確認したかった質問に対する回答
* [**トラブルシューティング**](active-directory-passwords-troubleshoot.md) - SSPR の一般的な問題を解決する方法について説明しています


