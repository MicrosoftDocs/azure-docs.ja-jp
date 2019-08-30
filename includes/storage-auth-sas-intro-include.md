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
ms.openlocfilehash: 3af8077627d56ce44c5e2959e2c79b967b09d9e5
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011940"
---
Shared Access Signature (SAS) を使用すると、ストレージ アカウント内のコンテナーと BLOB への制限付きアクセスを許可できます。 SAS を作成するときに、クライアントがアクセスできる Azure Storage リソース、それらのリソースに対するアクセス許可、SAS の有効期間などの制約を指定します。

すべての SAS はキーによって署名されます。 次の 2 つの方法のいずれかで SAS に署名できます。

- Azure Active Directory (Azure AD) の資格情報を使用して作成されたキーを使用する。 Azure AD の資格情報を使用して署名された SAS は、*ユーザー委任* SAS です。
- ストレージ アカウント キーを使用する。 *サービス SAS* と*アカウント SAS* は、どちらもストレージ アカウント キーを使用して署名されます。
