---
title: "Azure ポータルを使用して、Azure リソースに MSI アクセスを割り当てる方法"
description: "Azure ポータルを使用して、1 つのリソースに MSI を割り当て、別のリソースにアクセスを割り当てる方法について、ステップ バイ ステップで説明します。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 9dd02c8d7580cd9233e192f807686b7857ccf696
ms.contentlocale: ja-jp
ms.lasthandoff: 09/14/2017

---

# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-the-azure-portal"></a>Azure ポータルを使用して、リソースに管理対象サービス ID (MSI) アクセスを割り当てる

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

MSI で Azure リソースを構成したら、他のセキュリティ プリンシパルと同じように、別のリソースに MSI アクセスを付与できます。 この例では、Azure ポータルを使用して、Azure 仮想マシンの MSI アクセスを Azure ストレージ アカウントに付与する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>RBAC を使用して、別のリソースに MSI アクセスを割り当てる

[Azure VM などの](msi-qs-configure-portal-windows-vm.md) Azure リソースで MSI を有効にした後、次のようにします。

1. MSI ファイルを構成した Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure ポータル](https://portal.azure.com)にサインインします。

2. アクセス制御を変更する目的のリソースに移動します。 この例では、ストレージ アカウントに Azure VM アクセスを許可しているため、ストレージ アカウントに移動します。

3. リソースの [アクセス制御 (IAM)] ページをクリックし、[+ 追加] をクリックします。 [ロール] を指定し、、"仮想マシン" にアクセスを割り当てて、対応するサブスクリプションとリソースが存在するリソース グループを指定します。 検索条件領域に、リソースが表示されるはずです。 リソースを選択し、[保存] をクリックします。 

   ![アクセス制御 (IAM) のスクリーンショット](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  

4. [アクセス制御 (IAM)] のメイン ページに戻ると、リソースの MSI の新しいエントリが表示されます。 この例では、デモ リソース グループの "SimpleWinVM" VM には、ストレージ アカウントへの「共同作成者」アクセスが許可されています。

   ![アクセス制御 (IAM) のスクリーンショット](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

## <a name="troubleshooting"></a>トラブルシューティング

リソースの MSI が使用可能な ID の一覧に表示されない場合は、MSI が正しく有効になっていることを確認します。 この場合、 Azure VM に戻り、次の操作を行うことができます。

- [構成] ページを参照し、MSI の有効が [はい] になっていることを確認します。
- [拡張] ページを参照して、MSI 拡張機能が正常に展開されていることを確認します。

いずれかが正しくない場合は、リソース上で MSI を再デプロイするか、デプロイ エラーのトラブルシューティングを行う必要があります。

## <a name="related-content"></a>関連コンテンツ

- MSI の概要については、[管理対象サービス ID の概要](msi-overview.md)に関する記事をご覧ください。
- Azure VM で MSI を有効にするには、「[Azure ポータルを使用して Azure VM 管理対象サービスID (MSI) を構成する](msi-qs-configure-portal-windows-vm.md)」を参照してください。



