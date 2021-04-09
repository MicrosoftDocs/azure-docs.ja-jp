---
author: mattchenderson
ms.service: app-service
ms.topic: include
ms.date: 04/20/2020
ms.author: mahender
ms.openlocfilehash: 3b282a99bb7f6f107717d9c265a46d285d03b849
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "83649085"
---
ご自分のアプリで Azure Active Directory (AAD) のマネージド ID を使用すると、Azure Key Vault など、Azure AD で保護されたその他のリソースに簡単にアクセスできます。 ID は Azure プラットフォームによって管理され、ユーザーがシークレットをプロビジョニングまたはローテーションする必要はありません。 Azure AD のマネージド ID について詳しくは、「[Azure リソースのマネージド ID とは](../articles/active-directory/managed-identities-azure-resources/overview.md)」をご覧ください。

アプリケーションには 2 種類の ID を付与できます。

- **システム割り当て ID** はアプリケーションに関連付けられているため、アプリが削除されると削除されます。 アプリは 1 つのシステム割り当て ID しか持つことはできません。
- **ユーザー割り当て ID** は、アプリに割り当てることができるスタンドアロン Azure リソースです。 アプリは複数のユーザー割り当て ID を持つことができます。