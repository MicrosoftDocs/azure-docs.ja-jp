---
title: B2B コラボレーションの制限 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B コラボレーションの現在の制限事項
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: b45277c89193c51f70836bcef8a21636fc9c7973
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113122"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Azure AD B2B コラボレーションの制限
現在、Azure Active Directory (Azure AD) B2B コラボレーションには、この記事に記載されている制限が適用されます。

## <a name="possible-double-multi-factor-authentication"></a>多要素認証重複の可能性
Azure AD B2B を使用すると、多要素認証をリソース組織 (招待する側の組織) で実施することができます。 このアプローチの理由については、「[B2B コラボレーション ユーザーの条件付きアクセス](conditional-access.md)」を参照してください。 パートナー側で既に多要素認証が設定され、実施されている場合、パートナー側のユーザーは、ホーム組織と招待する側の組織の両方で認証が必要になる可能性があります。

## <a name="instant-on"></a>インスタント オン
B2B コラボレーションのフローでは、ユーザーをディレクトリに追加し、招待の使用、アプリ割り当てなどの際に動的に更新します。 更新と書き込みは、通常、1 つのディレクトリ インスタンスで行い、すべてのインスタンス間でレプリケートする必要があります。 すべてのインスタンスが更新されると、レプリケーションが完了します。 いずれかのインスタンスでオブジェクトの書き込みまたは更新が行われ、そのオブジェクトを取得する呼び出しが別のインスタンスに対して行われた場合は、レプリケーションの遅延が発生する可能性があります。 その場合は、更新または再試行が役立つことがあります。 API を使用してアプリを記述する場合は、バックオフの再試行がこの問題を軽減するための推奨される防御的な方法です。

## <a name="azure-ad-directories"></a>Azure AD ディレクトリ
Azure AD B2B は、Azure AD サービス ディレクトリの制限に従います。 ユーザーが作成できるディレクトリ数と、ユーザーまたはゲスト ユーザーが所属できるディレクトリ数の詳細については、「[Azure AD サービスの制限と制約](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions)」を参照してください。

## <a name="national-clouds"></a>各国のクラウド
[国内クラウド](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) は、物理的に分離された Azure のインスタンスです。 国内クラウドの境界を越えた B2B Collaboration はサポートされません。 たとえば、ご利用の Azure テナントがパブリックのグローバル クラウドに存在する場合、アカウントが国内クラウドにあるユーザーを招待することはできません。 ユーザーと共同作業を行うには、そのユーザーに別のメール アドレスを要求するか、またはご利用のディレクトリ内にメンバー ユーザー アカウントを作成してください。

## <a name="next-steps"></a>次の手順

Azure AD B2B コラボレーションに関する以下の記事を参照してください。

- [Azure AD B2B コラボレーションとは](what-is-b2b.md)
- [B2B コラボレーションの招待の委任](delegate-invitations.md)

