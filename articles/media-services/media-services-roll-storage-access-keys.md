---
title: "ストレージ アクセス キーをローリングした後に Media Services を更新する | Microsoft Docs"
description: "この記事は、ストレージ アクセス キーをローリングした後に Media Services を更新する際のガイダンスについて説明します。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2017
ms.author: milanga;cenkdin;juliako
translationtype: Human Translation
ms.sourcegitcommit: 8e0f0905748923177269b6d5df27be900770fece
ms.openlocfilehash: a2802dbf1d8a22c33b20dd4e71fcf26d9780f86a
ms.lasthandoff: 01/27/2017


---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>ストレージ アクセス キーをローリングした後に Media Services を更新する

新しく Azure Media Services (AMS) アカウントを作成すると、メディア コンテンツの保存に使用する Azure Storage アカウントも選択するよう求めるメッセージが表示されます。 Media Services アカウントには複数のストレージ アカウントを追加できます。 このトピックでは、ストレージ キーを入れ換える方法を示します。 また、メディア アカウントにストレージ アカウントを追加する方法も示します。 

このトピックで説明している操作を実行するには、[ARM API](https://docs.microsoft.com/rest/api/media/mediaservice) と [PowerShell](https://docs.microsoft.com/powershell/resourcemanager/azurerm.media/v0.3.2/azurerm.media) を使用する必要があります。  詳細については、[PowerShell とResource Manager を使用して Azure のリソースを管理する方法](../azure-resource-manager/powershell-azure-resource-manager.md)に関するページをご覧ください。

## <a name="overview"></a>概要

新しいストレージ アカウントが作成されると、Azure は、ストレージ アカウントへのアクセス認証に使用される 512 ビット ストレージ アクセス キーを 2 つ生成します。 ストレージの接続の安全性を高めるため、ストレージ アクセス キーを定期的に再生成して入れ換えることをお勧めします。 片方のアクセス キーでストレージ アカウントに接続したまま、もう片方のアクセス キーを再生成できるように、アクセス キーは&2; つ (プライマリとセカンダリ) 提供されます。 この手順は、「アクセス キーのローリング」とも呼ばれます。

Media Services は、指定されたストレージ キーに依存します。 特に、アセットのストリーミングやダウンロードに使用されるロケーターは指定されたアクセス キーに依存します。 AMS アカウントが作成されると、既定ではプライマリ ストレージ アクセス キーに依存しますが、ユーザーは AMS が使用するストレージ キーを更新できます。 このトピックで説明する次の手順に従って、どのキーを使用するかを Media Services に確実に認識させる必要があります。  

>[!NOTE]
> ストレージ アカウントが複数ある場合、この手順をストレージ アカウントごとに実行する必要があります。 ストレージ キーを入れ換える順序は決まっていません。 セカンダリ キー、プライマリ キーの順に入れ換えることも、その逆の順にすることもできます。
>
> このトピックで説明する手順を実稼働アカウントで実行する前に、実稼働前のアカウントでテストするようにしてください。
>

## <a name="steps-to-rotate-storage-keys"></a>ストレージ キーの入れ換え手順 
 
 1. PowerShell コマンドレットまたは [Azure](https://portal.azure.com/) Portal を使ってストレージ アカウントのプライマリ キーを変更します。
 2. 適切なパラメーターを指定して Sync-AzureRmMediaServiceStorageKeys コマンドレッドを呼び出し、メディア アカウントによって強制的にストレージ アカウント キーを選択します。
 
    次の例では、キーをストレージ アカウントに同期する方法を示します。
  
         Sync-AzureRmMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
  
 3. 1 時間ほど待ちます。 ストリーミングのシナリオが機能していることを確認します。
 4. PowerShell コマンドレットまたは Azure Portal を使ってストレージ アカウントのセカンダリ キーを変更します。
 5. 適切なパラメーターを指定して Sync-AzureRmMediaServiceStorageKeys PowerShell コマンドレッドを呼び出し、メディア アカウントによって強制的に新しいストレージ アカウント キーを選択します。 
 6. 1 時間ほど待ちます。 ストリーミングのシナリオが機能していることを確認します。
 
### <a name="a-powershell-cmdlet-example"></a>PowerShell コマンドレットの例 

次の例では、ストレージ アカウントを取得し、これを AMS アカウントと同期する方法を示します。

    $regionName = "West US"
    $resourceGroupName = "SkyMedia-USWest-App"
    $mediaAccountName = "sky"
    $storageAccountName = "skystorage"
    $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

    Sync-AzureRmMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId

 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>AMS アカウントにストレージ アカウントを追加する手順

次のトピックは、AMS アカウントにストレージ アカウントを追加する方法を示します: [複数のストレージ アカウントを Media Services アカウントにアタッチします](meda-services-managing-multiple-storage-accounts.md)。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>謝辞
この文書の作成に協力してくれた Cenk Dingiloglu、Milan Gada、 Seva Titov に感謝します。

