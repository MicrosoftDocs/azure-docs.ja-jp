---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/20/2020
ms.author: cephalin
ms.openlocfilehash: c3fa57dd162fbbfbf0d46f73bffc78f279ef2968
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "83649134"
---
* **ユーザー レベルの資格情報**: Azure アカウント全体の資格情報セットです。 これを使用して、Azure アカウントがアクセス許可を持っているすべてのアプリをサブスクリプションに関係なく App Service にデプロイできます。 これは、ポータルの GUI (アプリの [リソース ページ](../articles/azure-resource-manager/management/manage-resources-portal.md#manage-resources)の **[概要]** や **[プロパティ]** など) に表示される既定のセットです。 ユーザーがロールベースのアクセス制御 (RBAC) または共同管理者のアクセス許可を使用してアプリのアクセス権を付与されると、そのユーザーは、アクセス権が取り消されるまで自分のユーザーレベル資格情報を使用できます。 これらの資格情報は他の Azure ユーザーと共有しないでください。

* **アプリ レベルの資格情報**: アプリごとの資格情報セットです。 そのアプリのみにデプロイするために使用できます。 各アプリの資格情報は、アプリの作成時に自動的に生成されます。 これらは手動で構成できませんが、いつでもリセットできます。 RBAC を使用してアプリ レベルの資格情報へのアクセス権がユーザーに付与される場合、そのユーザーはアプリに対して共同作成者以上の権限 (Web サイトの共同作成者の組み込みロールを含む) を持つ必要があります。 閲覧者は発行を許可されていないため、この資格情報にアクセスできません。