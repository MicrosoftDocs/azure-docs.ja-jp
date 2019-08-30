---
title: ASP.NET プロジェクトの変更点 | Microsoft Docs
description: Visual Studio 接続済みサービスを使用して ASP.NET プロジェクトに Azure Storage を追加した後の変更点について説明します。
services: storage
author: ghogen
manager: jillfra
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 74db2e1e15c282f4393715c67561b2ed61bbcdfb
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69512072"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>ASP.NET プロジェクトの変更点 (Visual Studio Azure Storage 接続済みサービス)
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
選択されたストレージ アカウントの接続文字列とキーを使用して、プロジェクトの web.config ファイル内に要素が作成されました。

詳細については、「 [ASP.NET](https://www.asp.net)」を参照してください。

