---
title: Azure portal でストレージ アカウント設定を管理する - Azure Storage | Microsoft Docs
description: Azure portal でストレージ アカウント設定を管理する方法について説明します。これには、アクセス制御設定の構成、アカウント アクセス キーの再生成、アクセス層の変更、アカウントにより使用されるレプリケーションの種類の変更などが含まれます。 ポータルでストレージ アカウントを削除する方法についても説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 66bdc4bd1e17347419a6eccd7c9532db17b33001
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303495"
---
# <a name="manage-storage-account-settings-in-the-azure-portal"></a>Azure portal でストレージ アカウント設定を管理する

[Azure portal](https://portal.azure.com) では、ストレージ アカウント向けにさまざまな設定が用意されています。 この記事では、これらの設定の一部とその使用方法について説明します。

## <a name="access-control"></a>アクセス制御

Azure Storage では、ロールベースのアクセス制御 (RBAC) を介した Azure Active Directory による BLOB ストレージとキュー ストレージの承認がサポートされています。 Azure AD による承認の詳細については、「[Azure Active Directory を使用して Azure BLOB およびキューへのアクセスを承認する](storage-auth-aad.md)」を参照してください。

Azure portal の**アクセス制御**設定を利用すれば、簡単な方法で RBAC ロールをユーザー、グループ、サービス プリンシパル、マネージド ID に割り当てることができます。 RBAC ロールを割り当てる方法の詳細については、「[Manage access rights to blob and queue data with RBAC](storage-auth-aad-rbac.md)」 (RBAC で BLOB とキュー データにアクセスする権利を管理する) を参照してください。

## <a name="tags"></a>Tags

Azure Storage では、カスタマイズした分類で Azure リソースを整理するための Azure Resource Manager タグがサポートされています。 サブスクリプション内のストレージ アカウントを論理的にグループ化できるように、ストレージ アカウントにタグを適用できます。

ストレージ アカウントについては、タグ名は 128 文字まで、タグ値は 256 文字までに制限されます。

詳細については、[タグを使用した Azure リソースの整理](../../azure-resource-manager/resource-group-using-tags.md)に関するページを参照してください。

## <a name="access-keys"></a>[アクセス キー]

ストレージ アカウントを作成すると、Azure により 512 ビットのストレージ アカウント アクセス キーが 2 つ生成されます。 この 2 つのキーは、共有キー経由でストレージ アカウントにアクセスする際、認証に使用されます。 アプリケーションを中断することなく、キーをローテーションさせ、再生成できます。Microsoft では、キーを定期的に再生成することをお勧めしています。

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

[!INCLUDE [storage-recommend-azure-ad-include](../../../includes/storage-recommend-azure-ad-include.md)]

### <a name="view-account-keys-and-connection-string"></a>アカウント キーと接続文字列を表示する

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

### <a name="regenerate-access-keys"></a>アクセス キーを再生成する

Microsoft では、ストレージ アカウントを保護する目的で、アクセス キーを定期的に再生成することをお勧めしています。 キーをローテーションさせることができるように、2 つのアクセス キーが割り当てられます。 キーをローテーションさせるとき、プロセス全体でアプリケーションの Azure Storage へのアクセスが確実に維持されるようにします。 

> [!WARNING]
> アクセス キーの再生成は、ストレージ アカウント キーに依存するあらゆるアプリケーションまたは Azure サービスに影響を与える可能性があります。 メディア サービス、クラウド、モバイル アプリケーション、Azure Storage 向けのグラフィカル ユーザー インターフェイス アプリケーション ([Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) など) を含む、アクセス キーを使用してストレージ アカウントにアクセスするクライアントは新しいキーを使用するように更新する必要があります。

次のプロセスでストレージ アカウント キーをローテーションさせます。

1. セカンダリ キーを使用するようにアプリケーション コードの接続文字列を更新します。
2. ストレージ アカウントのプライマリ アクセス キーを再生成します。 Azure portal の **[アクセス キー]** ブレードで、 **[Key1 の再生成]** をクリックし、確認画面で **[はい]** をクリックして新しいキーを生成します。
3. 新しいプライマリ アクセス キーを参照するようにアプリケーション コードの接続文字列を更新します。
4. 同様に、セカンダリ アクセス キーを再生成します。

## <a name="account-configuration"></a>アカウント構成

ストレージ アカウントの作成後、その構成を変更できます。 たとえば、データの複製方法を変更したり、アカウントのアクセス層をホットからクールに変更したりすることができます。 [Azure portal](https://portal.azure.com) でお使いのストレージ アカウントに移動し、 **[設定]** で **[構成]** を見つけてクリックすると、アカウント構成を表示したり変更したりできます。

ストレージ アカウントの構成を変更すると、追加コストが発生する場合があります。 詳細については、「[Azure Storage 料金](https://azure.microsoft.com/pricing/details/storage/)」を参照してください。

## <a name="delete-a-storage-account"></a>ストレージ アカウントを削除する

使わなくなったストレージ アカウントを削除するには、 [Azure ポータル](https://portal.azure.com)でストレージ アカウントに移動し、 **[削除]** をクリックします。 ストレージ アカウントを削除すると、アカウント内のすべてのデータを含む、アカウント全体が削除されます。

> [!WARNING]
> 削除したストレージ アカウントを復元することも、削除前にアカウントに含まれていたコンテンツを取得することもできません。 アカウントを削除する前に、保存する必要のあるデータを必ずバックアップしてください。 これはアカウントのどのリソースにも当てはまります。BLOB、テーブル、キュー、またはファイルを削除すると、完全に削除されます。
> 

Azure 仮想マシンに関連付けられているストレージ アカウントを削除しようとすると、まだ使用しているストレージ アカウントに関するエラー メッセージが表示されることがあります。 このエラーのトラブルシューティングについては、[ストレージ アカウントを削除する際のエラーのトラブルシューティング](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

- [Azure ストレージ アカウントの概要](storage-account-overview.md)
- [ストレージ アカウントの作成](storage-quickstart-create-account.md)
