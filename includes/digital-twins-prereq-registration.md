---
author: baanders
description: Azure Digital Twins のインクルード ファイル - アプリの登録を設定するための前提条件
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 2ce534972cf6509cdc1ca026f4b29efd3df91afd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96303575"
---
この記事で使用されているすべてのリソースを認証するには、**Azure Active Directory (Azure AD)[ の](../articles/active-directory/fundamentals/active-directory-whatis.md)アプリの登録** を設定する必要があります。 ["*AutoRest を使用して アプリの登録の作成方法*](../articles/digital-twins/how-to-create-app-registration.md)に関するページでの指示に従って設定します。 

アプリの登録が完了したら、登録の "**_アプリケーション (クライアント) ID_**" および "**_ディレクトリ (テナント) ID_**" ([Azure portal で確認してください](../articles/digital-twins/how-to-create-app-registration.md#collect-client-id-and-tenant-id)) が必要になります。 これらの値をメモしておき、後で Azure Digital Twins API へのアクセス許可を付与するために使用します。