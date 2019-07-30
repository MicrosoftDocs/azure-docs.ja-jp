---
title: B2B コラボレーションと B2C の比較 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B コラボレーションと Azure AD B2C の違いは何でしょうか。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 07/22/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e7f5aa324db869e30a8b2fe214416129baca8d9
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68380734"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Azure Active Directory での B2B コラボレーションと B2C の比較

Azure Active Directory (Azure AD) B2B コラボレーションと Azure AD B2C は、両方とも Azure AD で外部ユーザーと共同で作業できます。 しかし、どのような違いがあるのでしょうか。

**Azure AD B2B** は、ファイルとリソースを外部ユーザーと安全に共有してコラボレーションできるようにすることを望んでいる企業向けです。 Azure 管理者が Azure portal で B2B を設定し、Azure AD が貴社と外部パートナー間のフェデレーションを処理します。 ユーザーは、職場または学校アカウント、または電子メール アカウントによる単純な招待と受諾プロセスを使用して、共有リソースにサインインします。
 
**Azure AD B2C** は、主に顧客向けのアプリを作成する企業と開発者向けです。 Azure AD B2C では、開発者はアプリケーション用の完全な機能を備えた ID システムとして Azure AD を使用でき、顧客は既に確立している ID (Facebook や Gmail など) を使用してサインインできます。

次の表に、詳細な比較を示します。


B2B コラボレーション機能 |     Azure AD B2C スタンドアロン製品
-------- | --------
対象: ID プロバイダーに関係なく、取引先組織のユーザーを認証できるようにする必要のある組織。 | 対象: 個人、法人や企業の顧客を問わず、モバイル アプリや Web アプリの顧客を Azure AD に招待するお客様。
サポートされている ID: 会社または学校のアカウントを持つ従業員、会社または学校のアカウントを持つパートナー、または任意のメール アドレス。 近々直接フェデレーションをサポートします。  | サポートされている ID: ローカル アプリケーションのアカウント (任意のメール アドレスまたはユーザー名) を持つコンシューマー ユーザーまたは直接フェデレーションを使用するサポート対象の任意のソーシャル ID。
外部ユーザーは、従業員と同じディレクトリで管理されますが、特別な注釈が付与されます。 従業員と同じように管理し、同じグループに追加するなどの操作を実行できます。  | 外部ユーザーはアプリケーション ディレクトリで管理されます。 組織の従業員やパートナーのディレクトリ (存在する場合) とは別に管理されます。
あらゆる Azure AD 接続アプリへのシングル サインオン (SSO) がサポートされています。 たとえば、Office 365 またはオンプレミスのアプリケーションや、Salesforce、Workday などの SaaS アプリへのアクセスを提供できます。  |  Azure AD B2C テナント内のお客様所有のアプリへの SSO をサポートします。 Office 365 やその他のマイクロソフト SaaS アプリへの SSO はサポートされていません。
パートナーのライフサイクル: ホスト/招待元の組織によって管理されます。  | お客様のライフサイクル: セルフサービスまたはアプリケーションによって管理されます。
セキュリティ ポリシーとコンプライアンス: ホストまたは招待元の組織によって管理されます ([条件付きアクセス ポリシー](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)を使用するなど)。  | セキュリティ ポリシーとコンプライアンス: アプリケーションによって管理されます。
ブランド化: ホスト/招待元の組織のブランドが使用されます。  |    ブランド化: アプリケーションによって管理されます。 一般的には製品によりブランド化される傾向があり、組織は目立たなくなります。
詳細情報: [ブログ記事](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/)、[ドキュメント](what-is-b2b.md)  | 詳細情報: [製品ページ](https://azure.microsoft.com/services/active-directory-b2c/)、[ドキュメント](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>次の手順

- [Azure AD B2B コラボレーションとは](what-is-b2b.md)
- [B2B コラボレーション ユーザーのプロパティ](user-properties.md)

