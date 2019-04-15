---
title: Azure portal でストレージ アカウント設定を管理する - Azure Storage | Microsoft Docs
description: Azure portal でストレージ アカウント設定を管理する方法について説明します。これには、アクセス制御設定の構成、アカウント アクセス キーの再生成、アクセス層の変更、アカウントにより使用されるレプリケーションの種類の変更などが含まれます。 ポータルでストレージ アカウントを削除する方法についても説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/05/2019
ms.author: tamram
ms.openlocfilehash: fa574558afeec5a7706482a142c0187e6a34bdb3
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370399"
---
# <a name="manage-storage-account-settings-in-the-azure-portal"></a>Azure portal でストレージ アカウント設定を管理する

[Azure portal](https://portal.azure.com) では、ストレージ アカウント向けにさまざまな設定が用意されています。 この記事では、これらの設定の一部とその使用方法について説明します。

## <a name="access-control"></a>アクセス制御

Azure Storage では、ロールベースのアクセス制御 (RBAC) を介して、BLOB ストレージとキュー ストレージを Azure Active Directory で認証できます。 Azure AD による認証に関する詳細については、[Azure Active Directory を利用して Azure BLOB およびキューへのアクセスを認証する](storage-auth-aad.md)方法に関するページを参照してください。

Azure portal の**アクセス制御**設定を利用すれば、簡単な方法で RBAC ロールをユーザー、グループ、サービス プリンシパル、マネージド ID に割り当てることができます。 RBAC ロールを割り当てる方法の詳細については、「[Manage access rights to blob and queue data with RBAC](storage-auth-aad-rbac.md)」 (RBAC で BLOB とキュー データにアクセスする権利を管理する) を参照してください。

> [!NOTE]
> Azure AD の資格情報を使用したユーザーまたはアプリケーションの認証は、セキュリティと使いやすさで他の承認手段よりも優れています。 アプリケーションで共有キー承認を引き続き使うことはできますが、Azure AD を使うと、コードでアカウント アクセス キーを保存する必要がなくなります。 Shared Access Signature (SAS) を使ってストレージ アカウント内のリソースに対するきめ細かいアクセスの許可を続けることもできますが、Azure AD は、SAS トークンを管理したり侵害された SAS の取り消しを心配したりする必要なしに、同様の機能を提供します。 

## <a name="tags"></a>Tags

Azure Storage では、カスタマイズした分類で Azure リソースを整理するための Azure Resource Manager タグがサポートされています。 サブスクリプション内のストレージ アカウントを論理的にグループ化できるように、ストレージ アカウントにタグを適用できます。 

ストレージ アカウントについては、タグ名は 128 文字まで、タグ値は 256 文字までに制限されます。

詳細については、[タグを使用した Azure リソースの整理](../../azure-resource-manager/resource-group-using-tags.md)に関するページを参照してください。

## <a name="access-keys"></a>[アクセス キー]

ストレージ アカウントを作成すると、Azure により 512 ビットのストレージ アカウント アクセス キーが 2 つ生成されます。 この 2 つのキーは、共有キー経由でストレージ アカウントにアクセスする際、認証に使用されます。 アプリケーションを中断することなく、キーをローテーションさせ、再生成できます。Microsoft では、キーを定期的に再生成することをお勧めしています。

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

### <a name="view-and-copy-access-keys"></a>アクセス キーの表示とコピー

ストレージ アカウントの資格情報を表示するには、次のように操作します。

1. [Azure Portal](https://portal.azure.com) に移動します。
2. 自分のストレージ アカウントを探します。
3. ストレージ アカウントの概要の **[設定]** セクションで、**[アクセス キー]** を選択します。 アカウント アクセス キーと、各キーの完全な接続文字列が表示されます。
4. **[key1]** で **[キー]** 値を見つけ、**[コピー]** ボタンをクリックしてアカウント キーをコピーします。
5. あるいは、接続文字列全体をコピーできます。 **[Key1]** の **[接続文字列]** の値を見つけて **[コピー]** ボタンをクリックし、接続文字列をコピーします。

    ![Azure portal でアクセス キーを表示する方法を示したスクリーンショット](media/storage-manage-account/portal-connection-string.png)

### <a name="regenerate-access-keys"></a>アクセス キーを再生成する

Microsoft では、ストレージ アカウントを保護する目的で、アクセス キーを定期的に再生成することをお勧めしています。 キーをローテーションさせることができるように、2 つのアクセス キーが割り当てられます。 キーをローテーションさせるとき、プロセス全体でアプリケーションの Azure Storage へのアクセスが確実に維持されるようにします。 

> [!WARNING]
> アクセス キーの再生成は、ストレージ アカウント キーに依存するあらゆるアプリケーションまたは Azure サービスに影響を与える可能性があります。 メディア サービス、クラウド、モバイル アプリケーション、Azure Storage 向けのグラフィカル ユーザー インターフェイス アプリケーション ([Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) など) を含む、アクセス キーを使用してストレージ アカウントにアクセスするクライアントは新しいキーを使用するように更新する必要があります。 

次のプロセスでストレージ アカウント キーをローテーションさせます。

1. セカンダリ キーを使用するようにアプリケーション コードの接続文字列を更新します。
2. ストレージ アカウントのプライマリ アクセス キーを再生成します。 Azure portal の **[アクセス キー]** ブレードで、**[Key1 の再生成]** をクリックし、確認画面で **[はい]** をクリックして新しいキーを生成します。
3. 新しいプライマリ アクセス キーを参照するようにアプリケーション コードの接続文字列を更新します。
4. 同様に、セカンダリ アクセス キーを再生成します。

## <a name="account-configuration"></a>アカウント構成

ストレージ アカウントの作成後、その構成を変更できます。 たとえば、データの複製方法を変更したり、アカウントのアクセス層をホットからクールに変更したりすることができます。 [Azure portal](https://portal.azure.com) でお使いのストレージ アカウントに移動し、**[設定]** で **[構成]** を見つけてクリックすると、アカウント構成を表示したり変更したりできます。

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
