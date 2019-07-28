---
title: Azure portal を使用して Azure リソースにマネージド ID アクセスを割り当てる方法
description: Azure portal を使用して、1 つのリソースにマネージド ID を割り当て、別のリソースにアクセスを割り当てる方法について、ステップ バイ ステップで説明します。
services: active-directory
documentationcenter: ''
author: markusvi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 958b3d72a3a8df4a3b67f62e7db788d7142ca667
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66112897"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Azure portal を使用してリソースにマネージド ID アクセスを割り当てる

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

マネージド ID で Azure リソースを構成した後、他のセキュリティ プリンシパルと同じように、別のリソースにマネージド ID アクセスを付与できます。 この記事では、Azure portal を使用して、Azure 仮想マシンまたは仮想マシン スケール セットのマネージド ID アクセスを Azure ストレージ アカウントに付与する方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。 **[システム割り当てマネージド ID とユーザー割り当てマネージド ID の違い](overview.md#how-does-it-work)を必ず確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>RBAC を使用して他のリソースにマネージド ID アクセスを割り当てる

[Azure VM](qs-configure-portal-windows-vm.md) または [Azure VMSS](qs-configure-portal-windows-vmss.md) などの Azure リソース上でマネージド ID を有効にした後、次の手順を実行します。

1. マネージド ID を構成した Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。

2. アクセス制御を変更する目的のリソースに移動します。 この例では、ストレージ アカウントに Azure 仮想マシンへのアクセスを許可するため、ストレージ アカウントに移動します。

3. リソースの **[アクセス制御 (IAM)]** ページを選択し、 **[+ ロール割り当ての追加]** を選択します。 その後、 **[ロール]** 、 **[Assign access to]\(アクセスの割り当て先\)** の順に指定して、該当の **[サブスクリプション]** を指定します。 検索条件領域に、リソースが表示されるはずです。 リソースを選択し、 **[保存]** を選択します。 

   ![アクセス制御 (IAM) のスクリーンショット](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>次の手順

- [Azure リソースのマネージド ID の概要](overview.md)
- Azure 仮想マシン上でマネージド ID を有効にするには、「[Azure portal を使用して Azure VM で Azure リソースのマネージド ID を構成する](qs-configure-portal-windows-vm.md)」を参照してください。
- Azure 仮想マシン スケール セット上でマネージド ID を有効にするには、「[Azure portal を使用して仮想マシン スケール セットで Azure リソースのマネージド ID を構成する](qs-configure-portal-windows-vmss.md)」を参照してください。


