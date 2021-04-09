---
title: Active Directory サービス アカウントのセキュリティ保護の概要
description: Azure Active Directory で使用できるサービス アカウントの種類について説明します。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27353e6086085aadae622e007a38f97e15cbcb7f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587867"
---
# <a name="introduction-to-securing-azure-service-accounts"></a>Azure サービス アカウントのセキュリティ保護の概要

Azure Active Directory のネイティブなサービス アカウントには、マネージ ID、サービス プリンシパル、ユーザーベースのサービス アカウントの 3 種類があります。 サービス アカウントは、アプリケーション、API、その他のサービスなどの人間以外のエンティティを表すことを目的とした特別な種類のアカウントです。 これらのエンティティは、サービス アカウントによって提供されるセキュリティ コンテキスト内で動作します。 

## <a name="types-of-azure-active-directory-service-accounts"></a>Azure Active Directory サービス アカウントの種類

Azure でホストされるサービスでは、可能であればマネージ ID を使用し、そうでない場合はサービス プリンシパルを使用することをお勧めします。 マネージド ID は、Azure の外部でホストされているサービスには使用できません。 その場合は、サービス プリンシパルをお勧めします。 マネージド ID またはサービス プリンシパルを使用できる場合は、それらを使用してください。 Azure Active Directory ユーザー アカウントは、サービス プリンシパルとして使用しないことをお勧めします。 概要については、次の表を参照してください。
 

| サービスのホスト| マネージド ID| サービス プリンシパル| Azure ユーザー アカウント |
| - | - | - | - |
|サービスは Azure でホストされている。| はい。 <br>サービスでマネージド ID が <br>サポートされる場合に推奨。| はい。| 非推奨。 |
| サービスは Azure でホストされていない。| いいえ| はい。 推奨。| 非推奨。 |
| サービスはマルチテナントである| いいえ| はい。 推奨。| いいえ。 |


## <a name="managed-identities"></a>マネージド ID

マネージド ID は、Azure リソースの ID を提供するために作成されるセキュリティで保護された Azure Active Directory (Azure AD) の ID です。 [マネージド ID には次の 2 種類](../managed-identities-azure-resources/overview.md#managed-identity-types)があります。 
 
* システムによって割り当てられたマネージド ID は、サービスのインスタンスに直接割り当てることができます。 

* ユーザーが割り当てたマネージド ID は、スタンドアロン リソースとして作成できます。 

詳細については、[マネージド ID のセキュリティ保護](service-accounts-managed-identities.md)に関するページを参照してください。 マネージド ID の一般的な情報については、「[Azure リソースのマネージド ID とは](../managed-identities-azure-resources/overview.md)」を参照してください。

## <a name="service-principals"></a>サービス プリンシパル

アプリケーションを表すためにマネージド ID を使用できない場合は、サービス プリンシパルを使用します。 サービス プリンシパルは、シングル テナント アプリケーションとマルチテナント アプリケーションの両方で使用できます。 

サービス プリンシパルは、単一の Azure AD テナント内のアプリケーション オブジェクトのローカル表現です。 アプリケーション インスタンスの ID として機能し、アプリケーションにアクセスできるユーザーと、アプリケーションでアクセスできるリソースが定義されます。 サービス プリンシパルは、アプリケーションが使用される各テナントで (ローカルに) 作成され、グローバルに一意なアプリケーション オブジェクトが参照されます。 テナントによって、サービス プリンシパルのサインインとリソースへのアクセスがセキュリティで保護されます。

サービス プリンシパルを使用した認証には、2 つのメカニズム (クライアント証明書とクライアント シークレット) があります。 証明書のほうがより安全です。可能な場合は、クライアント証明書を使用してください。 クライアント シークレットとは異なり、クライアント証明書が誤ってコードに埋め込まれることはありません。

サービス プリンシパルのセキュリティ保護の詳細については、サービス プリンシパルのセキュリティ保護に関するページを参照してください。

 
## <a name="next-steps"></a>次のステップ


Azure サービス アカウントのセキュリティ保護の詳細については、次を参照してください。

[マネージド ID のセキュリティ保護](service-accounts-managed-identities.md)

[サービス プリンシパルのセキュリティ保護](service-accounts-principal.md)

[Azure サービス アカウントの管理](service-accounts-governing-azure.md)