---
title: Azure Active Directory との SCIM 同期
description: Azure Active Directory を使用した SCIM 同期の取得に関するアーキテクチャ ガイダンス。
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
ms.openlocfilehash: b95aac504bc6ee72c353faecad25384e2dc90840
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172420"
---
# <a name="scim-synchronization-with-azure-active-directory"></a>Azure Active Directory との SCIM 同期

クロスドメイン ID 管理システム (SCIM) とは、ID ドメインと IT システムの間で行うユーザー ID 情報の交換を自動化するためのオープンな標準プロトコルです。 SCIM を使用すれば、人材管理 (HCM) システムに追加された従業員のアカウントを、確実に Azure Active Directory (Azure AD) または Windows Server Active Directory によって自動的に作成することができます。 ユーザーの属性およびプロファイルは 2 つのシステム間で同期されているので、ユーザーの状態または役割の変更に基づいてユーザーの更新および削除が行われます。

SCIM とは、2 つのエンドポイント (/Users エンドポイントおよび /Groups エンドポイント) の標準化された定義です。 オブジェクトの作成、更新、および削除を行う共通の REST Verb を使用します。 また、グループ名、ユーザー名、名、姓、電子メールなどの一般的な属性に対しては定義済みのスキーマも使用します。 SCIM 2.0 REST API を提供するアプリケーションであれば、独自のユーザー管理 API または製品を使用する煩わしさを軽減するか、なくすことができます。 たとえば、SCIM に準拠しているクライアントがあれば、JSON オブジェクトの HTTP POST を /Users エンドポイントに送信して新しいユーザー エントリを作成することができます。 同じ基本的なアクションに対して若干異なる API を必要とするのではなく、SCIM 標準に準拠しているアプリでは、既存のクライアント、ツール、およびコードをすぐに利用できます。 

## <a name="use-when"></a>次の場合に使用します。 

ユーザー情報を HCM システムから Azure AD および Windows Server Active Directory に、さらに必要があればターゲット システムに自動的にプロビジョニングする必要があります。 

![アーキテクチャ図](./media/authentication-patterns/scim-auth.png)


## <a name="components-of-system"></a>システムのコンポーネント 

* **HCM システム**: 従業員のライフサイクル全体にわたって人事プロセスをサポートおよび自動化する人材管理プロセスおよびプラクティスを実現するアプリケーションとテクノロジ。 

* **Azure AD プロビジョニング サービス**: 自動プロビジョニングにおいて SCIM 2.0 プロトコルを使用します。 このサービスが、アプリケーションの SCIM エンドポイントに接続されると、SCIM ユーザー オブジェクト スキーマおよび REST API を使用してユーザーとグループのプロビジョニングとプロビジョニング解除が自動化されます。  

* **Azure AD**: ID とその権利のライフサイクルを管理するために使用されるユーザー リポジトリです。 

* **ターゲット システム**: SCIM エンドポイントを備えていて、ユーザーとグループの自動プロビジョニングを有効にするために Azure AD プロビジョニングと連携するアプリケーションまたはシステムです。  

## <a name="implement-scim-with-azure-ad"></a>Azure AD による SCIM を実装する 

* [Azure AD でのプロビジョニングのしくみ ](../app-provisioning/how-provisioning-works.md)

* [Azure portal でエンタープライズ アプリのユーザー アカウント プロビジョニングを管理する](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [SCIM エンドポイントの構築と Azure AD を使用したユーザー プロビジョニングの構成](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [Azure AD プロビジョニング サービスの SCIM 2.0 プロトコルへのコンプライアンス](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md)