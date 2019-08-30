---
title: クラウド サービス プロジェクトの変更点 | Microsoft Docs
description: Visual Studio 接続済みサービスを使用して Azure ストレージ アカウントに接続した後の、クラウド サービス プロジェクトの変更点について説明します。
services: storage
author: ghogen
manager: jillfra
ms.assetid: ca0ea68d-f417-4ce8-9413-40d76f69cdea
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: d96e523b6e4e060eaef33de0f961e7020e5dfb57
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510640"
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

