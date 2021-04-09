---
title: Azure Active Directory を使用して Azure App Configuration へのアクセスを承認する
description: Azure RBAC を有効にして、Azure App Configuration インスタンスへのアクセスを承認します
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 05/26/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: f29be1807dfcc314c89d30301107670a970263ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172877"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Azure Active Directory を使用して Azure App Configuration へのアクセスを承認する
Azure App Configuration では、ハッシュ ベースのメッセージ認証コード (HMAC) の使用に加えて、App Configuration インスタンスに対する要求を承認するための Azure Active Directory (Azure AD) の使用がサポートされています。  Azure AD を使用することにより、Azure ロールベースのアクセス制御 (Azure RBAC) を使用してセキュリティ プリンシパルにアクセス許可を付与することができます。  セキュリティ プリンシパルは、ユーザー、[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) または[アプリケーション サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md)のいずれかになります。  ロールおよびロールの割り当ての詳細については、[各種ロールについて](../role-based-access-control/overview.md)のページを参照してください。

## <a name="overview"></a>概要
App Configuration リソースにアクセスするためにセキュリティ プリンシパルによって行われる要求は、承認される必要があります。 Azure AD では、リソースへのアクセスは 2 段階のプロセスです。
1. セキュリティ プリンシパルの ID が認証され、OAuth 2.0 トークンが返されます。  トークンを要求するリソース名は、`https://login.microsoftonline.com/{tenantID}` です。この場合の `{tenantID}` は、サービス プリンシパルが属する Azure Active Directory テナント ID と一致します。
2. 指定されたリソースへのアクセスを承認するために、トークンが要求の一部として App Configuration サービスに渡されます。

認証の手順により、実行時にアプリケーション要求に OAuth 2.0 アクセス トークンが含まれる必要があります。  アプリケーションが Azure エンティティ (Azure Functions アプリ、Azure Web アプリ、Azure VM など) 内で実行されている場合、マネージド ID を使用してリソースにアクセスできます。  マネージド ID によって Azure App Configuration に対して行われる要求を認証する方法については、[Azure Active Directory と Azure リソースのマネージド ID を使用して Azure App Configuration リソースへのアクセスを承認する](howto-integrate-azure-managed-service-identity.md)方法に関するページをご覧ください。

承認の手順では、セキュリティ プリンシパルに 1 つまたは複数の Azure ロールを割り当てる必要があります。 Azure App Configuration は、App Configuration リソースに対するアクセス許可セットを含む Azure ロールを提供します。 セキュリティ プリンシパルに割り当てられたロールによって、そのプリンシパルが持つアクセス許可が決定されます。 Azure ロールの詳細については、「[Azure App Configuration の Azure 組み込みロール](#azure-built-in-roles-for-azure-app-configuration)」を参照してください。 

## <a name="assign-azure-roles-for-access-rights"></a>アクセス権の Azure ロールを割り当てる
Azure Active Directory (Azure AD) では、[Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md) を通じて、セキュリティで保護されたリソースへのアクセス権が承認されます。

Azure ロールが Azure AD セキュリティ プリンシパルに割り当てられると、Azure によりそのセキュリティ プリンシパルのリソースへのアクセス権が付与されます。 アクセスの範囲は、App Configuration リソースに制限されます。 Azure AD セキュリティ プリンシパルは、Azure リソースのユーザー、またはアプリケーション サービス プリンシパル、または[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) の場合があります。

## <a name="azure-built-in-roles-for-azure-app-configuration"></a>Azure App Configuration 用の Azure 組み込みロール
Azure には、Azure AD と OAuth を使用した、App Configuration データへのアクセスを承認するために、次の Azure 組み込みロールが用意されています。

- **App Configuration データ所有者**: このロールを使用して、App Configuration データへの読み取り/書き込み/削除アクセス権を付与します。 App Configuration リソースへのアクセスは許可されません。
- **App Configuration データ閲覧者**: このロールを使用して、App Configuration データへの読み取りアクセス権を付与します。 App Configuration リソースへのアクセスは許可されません。
- **共同作成者**:このロールを使用して、App Configuration リソースを管理します。 App Configuration データにはアクセス キーを使用してアクセスできますが、このロールでは Azure AD を使用したデータへの直接アクセスは許可されません。
- **閲覧者**:このロールを使用して、App Configuration リソースへの読み取りアクセス権を付与します。 リソースのアクセス キーへのアクセスと、App Configuration に格納されているデータへのアクセスは許可されません。

## <a name="next-steps"></a>次のステップ
App Configuration サービスを管理するための [マネージド ID](howto-integrate-azure-managed-service-identity.md) の使用について詳しく説明します。
