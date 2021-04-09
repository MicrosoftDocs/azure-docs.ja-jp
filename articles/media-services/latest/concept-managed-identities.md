---
title: マネージド ID
description: Media Services を Azure マネージド ID と共に使用することができます。
keywords: ''
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: 71a2b8f0734de80f71dbb2372f8600b464d6c606
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258441"
---
# <a name="managed-identities"></a>マネージド ID

異なるサービス間でやり取りされる通信のセキュリティを確保するシークレットと資格情報の管理は、開発者に共通の課題です。 Azure のマネージド ID を使用すれば、開発者が資格情報を管理する必要がなくなります。Azure リソースの ID は Azure AD から提供され、その ID を使用して Azure Active Directory (Azure AD) トークンが取得されます。

現在、Media Services でマネージド ID を使用できるシナリオは 2 つあります。

- Media Services アカウントのマネージド ID を使用してストレージ アカウントにアクセスする。

- Media Services アカウントのマネージド ID を使用して Key Vault にアクセスし、顧客キーにアクセスする。

次の 2 つのセクションでは、2 つのシナリオの手順について説明します。

## <a name="use-the-managed-identity-of-the-media-services-account-to-access-storage-accounts"></a>Media Services アカウントのマネージド ID を使用してストレージ アカウントにアクセスする

1. マネージド ID が割り当てられた Media Services アカウントを作成します。
1. 自分が所有しているストレージ アカウントに、マネージド ID プリンシパルのアクセス権を付与します。
1. Media Services では、マネージド ID を使用して、ユーザーに代わってストレージ アカウントにアクセスできるようになります。

## <a name="use-the-managed-identity-of-the-media-services-account-to-access-key-vault-to-access-customer-keys"></a>Media Services アカウントのマネージド ID を使用して Key Vault にアクセスし、顧客キーにアクセスする

1. マネージド ID が割り当てられた Media Services アカウントを作成します。
1. 自分が所有している Key Vault に、マネージド ID プリンシパルのアクセス権を付与します。
1. 顧客キー ベースのアカウントの暗号化を使用するように Media Services アカウントを構成します。
1. Media Services は、マネージド ID を使用して、ユーザーに代わって Key Vault にアクセスします。

カスタマー マネージド キーと Key Vault の詳細については、「[Media Services での Bring Your Own Key (カスタマー マネージド キー)](concept-use-customer-managed-keys-byok.md)」を参照してください。

## <a name="tutorials"></a>チュートリアル

これらのチュートリアルには、前述の両方のシナリオが含まれます。

- [Azure portal を使用して Media Services でカスタマー マネージド キーまたは BYOK を使用する](tutorial-byok-portal.md)
- [Media Services REST API でカスタマー マネージド キーまたは BYOK を使用する](tutorial-byok-postman.md)。

## <a name="next-steps"></a>次のステップ

マネージド ID がお客様とお客様の Azure アプリケーションにどのように役立つかの詳細については、[Azure AD のマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) に関するページを参照してください。
