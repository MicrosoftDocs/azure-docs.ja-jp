---
title: Azure Active Directory とのディレクトリ同期
description: Azure Active Directory を使用したディレクトリ同期の取得に関するアーキテクチャ ガイダンス。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98aa697c0120fd8a20adf11ad83e02406499a0d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648494"
---
# <a name="directory-synchronization"></a>ディレクトリ同期

多くの組織は、オンプレミスとクラウドの両方のコンポーネントを包含するハイブリッド インフラストラクチャを備えています。 ローカル ディレクトリとクラウド ディレクトリの間でユーザーの ID を同期させると、ユーザーは、1 つの資格情報セットを使用してリソースにアクセスできるようになります。 

同期とは、次のプロセスのことです。 

* 特定の条件に基づいてオブジェクトを作成すること。
* オブジェクトを更新したままにすること。
* 条件が満たされなくなった場合にオブジェクトを削除すること。 

オンプレミスのプロビジョニングには、オンプレミス ソース (Active Directory など) から Azure Active Directory (Azure AD) へのプロビジョニングが含まれます。 

## <a name="use-when"></a>使用する状況

オンプレミスの Active Directory 環境から Azure AD に ID データを同期する必要がある場合です。

![アーキテクチャ図](./media/authentication-patterns/dir-sync-auth.png)

## <a name="components-of-system"></a>システムのコンポーネント

* **[ユーザー]** :Azure AD を使用して、アプリケーションにアクセスします。

* **Web ブラウザー**: アプリケーションの外部 URL にアクセスするためにユーザーが操作するコンポーネントです。

* **アプリケーション**:認証と承認を目的とした Azure AD の使用に依存する Web アプリ。

* **Azure AD**:Azure AD Connect を経由して、組織のオンプレミス ディレクトリから ID 情報を同期します。 

* **Azure AD Connect**:オンプレミスの ID インフラストラクチャを Microsoft Azure AD に接続するためのツール。 ウィザード エクスペリエンスとガイド付きエクスペリエンスを使用すると、Active Directory から Azure AD への同期やサインオンなど、接続に必要な前提条件とコンポーネントを容易にデプロイおよび構成できます。 

* **Active Directory**: Active Directory は、ほとんどの Windows Server オペレーティング システムに含まれているディレクトリ サービスです。 Active Directory Domain Services (AD DS) が実行されているサーバーは、ドメイン コントローラーと呼ばれます。 これらによって、ドメイン内のすべてのユーザーとコンピューターが認証および承認されます。

## <a name="implement-directory-synchronization-with-azure-ad"></a>Azure AD とのディレクトリ同期を実装する

* [ID プロビジョニングとは](../cloud-sync/what-is-provisioning.md) 

* [ハイブリッド ID ディレクトリ統合ツール](../hybrid/plan-hybrid-identity-design-considerations-tools-comparison.md) 

* [Azure AD Connect インストールのロードマップ](../hybrid/how-to-connect-install-roadmap.md)