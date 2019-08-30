---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 825a9f5668cc80f1306d74623db2ea54614ba4a3
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985380"
---
> [!TIP]
> Azure Storage では、Azure Active Directory (Azure AD) を使用して BLOB および Queue ストレージへの要求を承認することができます。 Azure AD から返された OAuth 2.0 トークンを使用してユーザーまたはアプリケーションを承認する方法は、セキュリティと使いやすさの面で、共有キー承認よりも優位です。 Azure AD を使用すれば、アカウント アクセス キーをコードに保存する必要がないため、潜在的なセキュリティ脆弱性のリスクを排除できます。
>
> さらに Azure Storage は、BLOB ストレージのユーザー委任 Shared Access Signature (SAS) をサポートしています。 ユーザー委任 SAS は Azure AD の資格情報を使用して署名されます。 アプリケーション設計で、BLOB ストレージへのアクセスのため、Shared Access Signature が必要な場合は、セキュリティを強化するために、Azure AD 資格情報を使用してユーザー委任 SAS を作成してください。
>
> Azure Storage アプリケーションでは、できる限り Azure AD を使用することをお勧めします。 詳細については、[Azure Active Directory を使用して Azure BLOB およびキューへのアクセスを承認する](../articles/storage/common/storage-auth-aad.md)方法に関するページを参照してください。
