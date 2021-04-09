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
ms.openlocfilehash: f014ce55dc40723faf1b60f908814f9fa0428b8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99215295"
---
Azure では、BLOB とキューのデータへの Azure AD と OAuth を使ったアクセスを承認するために、次の Azure 組み込みロールが提供されています。

- [ストレージ BLOB データ所有者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner):Azure Data Lake Storage Gen2 で、所有権の設定と POSIX アクセス制御の管理に使用します。 詳細については、[Azure Data Lake Storage Gen2 でのアクセス制御](../articles/storage/blobs/data-lake-storage-access-control.md)に関するページを参照してください。
- [ストレージ BLOB データ共同作成者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor):BLOB ストレージ リソースの読み取り/書き込み/削除のアクセス許可を付与するために使用します。
- [ストレージ BLOB データ閲覧者](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader):BLOB ストレージ リソースの読み取り専用アクセス許可を付与するために使用します。
- [Storage Blob デリゲータ](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator):Azure AD 資格情報で署名された、コンテナーまたは BLOB 用の共有アクセス署名を作成するために使用するユーザー委任キーを取得します。
- [ストレージ キュー データ共同作成者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor):Azure キューの読み取り/書き込み/削除のアクセス許可を付与するために使用します。
- [ストレージ キュー データ閲覧者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader):Azure キューの読み取り専用アクセス許可を付与するために使用します。
- [ストレージ キュー データのメッセージ プロセッサ](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor):Azure Storage キューのピーク、メッセージの取得と削除のアクセス許可を付与するために使用します。
- [ストレージ キュー データ メッセージ送信者](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender):Azure Storage キューへのメッセージの追加アクセス許可を付与するために使用します。

データ アクセスに対して明示的に定義されたロールによってのみ、セキュリティ プリンシパルによる BLOB データまたはキュー データへのアクセスが許可されます。 **所有者**、**共同作成者**、**ストレージ アカウント共同作成者** などの組み込みロールでは、セキュリティ プリンシパルによるストレージ アカウントの管理は許可されますが、Azure AD を通じたそのアカウント内の BLOB データまたはキュー データへのアクセスは提供されません。 ただし、ロールに **Microsoft.Storage/storageAccounts/listKeys/action** が含まれている場合、そのロールが割り当てられているユーザーは、アカウント アクセス キーを使って共有キー認証を使用してストレージ アカウントのデータにアクセスできます。 詳細については、「[Azure portal を使用した BLOB またはキュー データへのアクセス](../articles/storage/blobs/authorize-data-operations-portal.md)」を参照してください。

データ サービスと管理サービスの両方に対する Azure Storage 用の Azure 組み込みロールの詳細については、「[Azure RBAC の Azure 組み込みロール](../articles/role-based-access-control/built-in-roles.md#storage)」の「**ストレージ**」セクションを参照してください。 さらに、Azure でアクセス許可を提供するさまざまな種類のロールの詳細については、「[従来のサブスクリプション管理者ロール、Azure ロール、および Azure AD ロール](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)」を参照してください。

> [!IMPORTANT]
> Azure ロールの割り当ての反映には最大で 30 分かかる可能性があります。
