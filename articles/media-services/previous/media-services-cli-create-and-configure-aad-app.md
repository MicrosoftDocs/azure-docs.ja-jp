---
title: Azure CLI で Azure AD アプリを作成し、Azure Media Services API にアクセスするよう構成する | Microsoft Docs
description: このトピックでは、Azure CLI を使用して Azure AD アプリを作成し、Azure Media Services API にアクセスするよう構成する方法を説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 93a77c64e82c39f8f967a3549fafecfae59b194b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57993543"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Azure CLI を使用して Azure AD アプリを作成し、それを Media Services API にアクセスするように構成します。 

このトピックでは、Azure CLI を使用して、Azure Media Services リソースにアクセスする Azure Active Directory (Azure AD) アプリケーションとサービス プリンシパルを作成する方法について説明します。 

## <a name="prerequisites"></a>前提条件

- Azure アカウント。 詳細については、[Azure の無料評価版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。 
- Media Services アカウント。 詳細については、「[Azure Portal を使用した Azure Media Services アカウントの作成](media-services-portal-create-account.md)」を参照してください。

## <a name="use-the-azure-cloud-shell"></a>Azure Cloud Shell の使用

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. ポータルの上部のナビゲーション ウィンドウから Cloud Shell を起動します。

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

詳細については、[Azure Cloud Shell の概要](../../cloud-shell/overview.md)に関するページを参照してください。

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Azure CLI を使用した Azure AD アプリの作成とメディア アカウントへのアクセスの構成
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> --password <strong password>
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

例: 

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

この例では、**scope** はメディア サービス アカウントの完全なリソース パスです。 しかし、**scope** は任意のレベルに設定できます。

たとえば、次のいずれかのレベルにすることができます。
 
* **サブスクリプション** レベル。
* **リソース グループ** レベル。
* **リソース** レベル (例: メディア アカウント)。

詳細については、「[Azure CLI で Azure サービス プリンシパルを作成する](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)」を参照してください

また、「[Azure コマンド ライン インターフェイスを使用したロールベースの Access Control の管理](../../role-based-access-control/role-assignments-cli.md)」も参照してください。 

## <a name="next-steps"></a>次の手順

[アカウントへのファイルのアップロード](media-services-portal-upload-files.md)を開始します。
