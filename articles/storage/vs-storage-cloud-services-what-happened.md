---
title: "クラウド サービス プロジェクトの変更点 | Microsoft Docs"
description: "Visual Studio 接続済みサービスを使用して Azure ストレージ アカウントに接続した後の、クラウド サービス プロジェクトの変更点について説明します。"
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: ca0ea68d-f417-4ce8-9413-40d76f69cdea
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a4fce79c9b4ec245bc4353e08daf309ea97bdb4a


---
# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>クラウド サービス プロジェクトの変更点 (Visual Studio Azure Storage 接続済みサービス)
## <a name="references-added"></a>リファレンスの追加
Visual Studio プロジェクトに Azure Storage の NuGet パッケージが追加されました。  
このパッケージは、次の .NET 参照を追加します。

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Azure Storage の接続文字列の追加
選択されたストレージ アカウントの接続文字列とキーを使用して要素が作成されました。 次のファイルが修正されました。

* **ServiceDefinition.csdef**
* **ServiceConfiguration.Cloud.cscfg**
* **ServiceConfiguration.Local.cscfg**




<!--HONumber=Nov16_HO3-->


