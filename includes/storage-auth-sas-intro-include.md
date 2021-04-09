---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/20/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: aec1faa4de1149f08fb6fbc1cc5bf3aa2ab6becd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75371780"
---
Shared Access Signature (SAS) を使用すると、ストレージ アカウント内のコンテナーと BLOB への制限付きアクセスを許可できます。 SAS を作成するときに、クライアントがアクセスできる Azure Storage リソース、それらのリソースに対するアクセス許可、SAS の有効期間などの制約を指定します。

すべての SAS はキーによって署名されます。 次の 2 つの方法のいずれかで SAS に署名できます。

- Azure Active Directory (Azure AD) の資格情報を使用して作成されたキーを使用する。 Azure AD の資格情報を使用して署名された SAS は、"*ユーザー委任*" SAS です。
- ストレージ アカウント キーを使用する。 *サービス SAS* と *アカウント SAS* は、どちらもストレージ アカウント キーを使用して署名されます。

ユーザー委任 SAS により、ストレージ アカウント キーで署名された SAS のセキュリティが向上します。 Microsoft では、ユーザー委任 SAS を可能な限り使用することを推奨しています。 詳細については、「[Shared Access Signatures (SAS) でデータの制限付きアクセスを付与する](../articles/storage/common/storage-sas-overview.md)」を参照してください。
