---
title: ポータルを使用してマネージド アプリを発行する
description: 組織のメンバーを対象とする Azure マネージド アプリケーションを Azure ポータルを使用して作成する方法について説明します。
author: tfitzmac
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: 05302d92f2304be35a7b88fac6fabfc17b13c63e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75649587"
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Azure ポータル経由でサービス カタログ アプリケーションを発行する

組織のメンバーを対象とする[マネージド アプリケーション](overview.md)を、Azure ポータルを使用して発行できます。 たとえば、IT 部門が、組織標準に確実に準拠するマネージド アプリケーションを発行できます。 これらのマネージド アプリケーションは、Azure Marketplace ではなく、サービス カタログを利用して入手できます。

## <a name="prerequisites"></a>前提条件

マネージド アプリケーションを発行するときに、リソースを管理するための ID を指定します。 Azure Active Directory ユーザー グループを指定することをお勧めします。 Azure Active Directory ユーザー グループを作成するには、「[Azure Active Directory でグループを作成し、メンバーを追加する](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」を参照してください。 

マネージド アプリケーション定義を含む .zip ファイルを、URI を通して使用できる必要があります。 .zip ファイルをストレージ BLOB にアップロードしておくことをお勧めします。 

## <a name="create-managed-application-with-portal"></a>ポータルを使用してマネージド アプリケーションを作成する

1. 左上にある **[+ 新規]** を選択します。

   ![新しいサービス](./media/publish-portal/new.png)

1. **サービス カタログ** を検索します。

1. 結果から、 **[Service Catalog Managed Application Definition]\(サービス カタログのマネージド アプリケーション定義\)** までスクロールします。 それを選択します。

   ![マネージド アプリケーション定義を検索する](./media/publish-portal/select-managed-apps-definition.png)

1. **[Create]\(作成\)** を選択し、マネージド アプリケーション定義を作成するプロセスを開始します。

   ![マネージド アプリケーション定義を作成する](./media/publish-portal/create-definition.png)

1. 名前、表示名、説明、場所、サブスクリプション、リソース グループの値を指定します。 パッケージ ファイルの URI については、作成した zip ファイルのパスを指定します。

   ![値を指定する](./media/publish-portal/fill-application-values.png)

1. [Authentication and Lock Level]\(認証とロックのレベル\) セクションで、 **[Add Authorization]\(承認の追加\)** を選択します。

   ![承認を追加する](./media/publish-portal/add-authorization.png)

1. リソースを管理する Azure Active Directory グループを選択し、 **[OK]** を選択します。

   ![承認グループを追加する](./media/publish-portal/add-auth-group.png)

1. すべての値を指定したら、 **[Create]\(作成\)** を選択します。

   ![マネージド アプリケーションを作成する](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>次のステップ

* マネージド アプリケーションの概要については、[マネージド アプリケーションの概要](overview.md)に関するページをご覧ください。
* マネージド アプリケーションの例については、[Azure マネージド アプリケーションのサンプル プロジェクト](sample-projects.md)に関する記事を参照してください。
* マネージド アプリケーションの UI 定義ファイルの作成する方法については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。