---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/25/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: f3b9f6c27fb8d423350eac5d286c9859ad6fbd37
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70104270"
---
## <a name="about-the-user-delegation-sas-preview"></a>ユーザー委任 SAS について (プレビュー)

コンテナーまたは BLOB にアクセスするための SAS トークンは、Azure AD 資格情報またはアカウント キーのいずれかを使用してセキュリティ保護することができます。 Azure AD 資格情報でセキュリティ保護された SAS は、ユーザーの代わりに、SAS に署名するために使用される OAuth 2.0 トークンが要求されるため、ユーザー委任 SAS と呼ばれます。

セキュリティのベスト プラクティスとして、より簡単に侵害できるアカウント キーを使用するのではなく、可能な限り Azure AD 資格情報を使用することをお勧めします。 アプリケーション設計で Shared Access Signature が必要な場合は、セキュリティを強化するために、Azure AD 資格情報を使用してユーザー委任 SAS を作成してください。 ユーザー委任 SAS の詳細については、「[ユーザー委任 SAS を作成する](/rest/api/storageservices/create-user-delegation-sas)」を参照してください。

> [!NOTE]
> このユーザー委任 SAS プレビューは、非運用環境でのみの使用を意図しています。

> [!CAUTION]
> 有効な SAS を所有するすべてのクライアントは、その SAS で許可されているストレージ アカウントのデータにアクセスできます。 SAS を悪意のある、または意図しない用途から保護することが重要です。 SAS の配布は慎重に行い、侵害された SAS を失効させるための計画を用意しておいてください。

Shared Access Signature の詳細については、「[Shared Access Signatures (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](../articles/storage/common/storage-sas-overview.md)」を参照してください。
