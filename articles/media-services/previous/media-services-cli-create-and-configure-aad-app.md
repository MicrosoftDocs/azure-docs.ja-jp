---
title: Azure CLI で Azure AD アプリを作成し、Azure Media Services API にアクセスするよう構成する | Microsoft Docs
description: このトピックでは、Azure CLI を使用して Azure AD アプリを作成し、Azure Media Services API にアクセスするよう構成する方法を説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 6517cc8224a1a4e2e3319fde52fb621e4f7ab564
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016935"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Azure CLI を使用して Azure AD アプリを作成し、それを Media Services API にアクセスするように構成します。

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](../latest/index.yml) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-v-2-v-3-migration-introduction.md)を参照してください。

このトピックでは、Azure CLI を使用して、Azure Media Services リソースにアクセスする Azure Active Directory (Azure AD) アプリケーションとサービス プリンシパルを作成する方法について説明します。 

## <a name="prerequisites"></a>前提条件

- Azure アカウント。 詳細については、[Azure の無料評価版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。 
- Media Services アカウント。 詳細については、「[Azure Portal を使用した Azure Media Services アカウントの作成](media-services-portal-create-account.md)」を参照してください。

## <a name="use-the-azure-cloud-shell"></a>Azure Cloud Shell の使用

1. [Azure portal](https://portal.azure.com/) にサインインする
2. ポータルの上部のナビゲーション ウィンドウから Cloud Shell を起動します。

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

詳細については、[Azure Cloud Shell の概要](../../cloud-shell/overview.md)に関するページを参照してください。

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Azure CLI を使用した Azure AD アプリの作成とメディア アカウントへのアクセスの構成
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> 
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

次に例を示します。

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

この例では、**scope** はメディア サービス アカウントの完全なリソース パスです。 しかし、**scope** は任意のレベルに設定できます。

たとえば、次のいずれかのレベルにすることができます。
 
* **サブスクリプション** レベル。
* **リソース グループ** レベル。
* **リソース** レベル (例: メディア アカウント)。

詳細については、「[Azure CLI で Azure サービス プリンシパルを作成する](/cli/azure/create-an-azure-service-principal-azure-cli)」を参照してください

「[Azure CLI を使用して Azure ロールの割り当てを追加または削除する](../../role-based-access-control/role-assignments-cli.md)」も参照してください。 

## <a name="next-steps"></a>次のステップ

[アカウントへのファイルのアップロード](media-services-portal-upload-files.md)を開始します。
