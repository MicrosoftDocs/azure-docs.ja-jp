---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ba715d510dc296ffa8f9c0ee58841f284416a118
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86027335"
---
## <a name="protect-your-access-keys"></a>アクセス キーの保護

ストレージ アカウントのアクセス キーは、ストレージ アカウントの root パスワードに似ています。 アクセス キーは常に慎重に保護してください。 キーを安全に管理およびローテーションするには、Azure Key Vault を使用します。 アクセス キーを他のユーザーに配布したり、ハードコーディングしたり、他のユーザーがアクセスできるプレーンテキストで保存したりしないでください。 キーが侵害された可能性があると思われる場合は、キーをローテーションしてください。

> [!NOTE]
> BLOB およびキューのデータに対する要求を承認するには、共有キーではなく、可能であれば Azure Active Directory (Azure AD) を使用することをお勧めします。 Azure AD では、共有キーよりも優れたセキュリティと使いやすさが実現されます。 Azure AD を使用したデータへのアクセスの承認の詳細については、「[Azure Active Directory を使用して BLOB とキューへのアクセスを承認する](../articles/storage/common/storage-auth-aad.md)」を参照してください。
