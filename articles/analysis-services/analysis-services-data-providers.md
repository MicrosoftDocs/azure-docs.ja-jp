---
title: "Azure Analysis Services へのクライアント接続に必要なデータ プロバイダー | Microsoft Docs"
description: "クライアントが Azure Analysis Services に接続するために必要なデータ プロバイダーについて説明します。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 14218a7c2acf6b68025095c1ecb60cb480233044
ms.lasthandoff: 03/23/2017


---

# <a name="data-providers-for-connecting-to-azure-analysis-services"></a>Azure Analysis Services に接続するためのデータ プロバイダー

データ プロバイダーはクライアント ライブラリとも呼ばれ、クライアント アプリケーションが Analysis Services サーバーに接続するために必要です。 

Analysis Services では 3 つのデータ プロバイダーが利用されます。 ADOMD.NET と Analysis Services Management Objects (AMO) はマネージ データ プロバイダーです。 Analysis Services OLE DB プロバイダー (MSOLAP DLL) は、ネイティブ データ プロバイダーです。 通常、3 つのプロバイダーがすべて同時にインストールされます。 Azure Analysis Services では最新バージョンのデータ プロバイダーが必要です。 

Power BI Desktop や Excel などの Microsoft クライアント アプリケーションでは 3 つのデータ プロバイダーがすべてインストールされます。 ただし、Excel のバージョンや、Excel および Power BI Desktop の新バージョンが毎月更新されるかどうかなどによっては、インストールされたデータ プロバイダーが、Azure Analysis Service で必要な最新バージョンに更新されないことがあります。 これは、カスタム アプリケーションまたは AsCmd、TOM、ADOMD.NET など他のインターフェイスでも同様です。 これらのアプリケーションでは、プロバイダーを手動でインストールする必要があります。 手動でインストールするためのデータ プロバイダーは、再頒布可能パッケージとして SQL Server Feature Pack に含まれます。ただし、これらは SQL Server のバージョンに対応しており、最新版ではない可能性があります。  

クライアント接続用のデータ プロバイダーは、Azure Analysis Services サーバーをデータ ソースに接続するために必要なデータ プロバイダーとは異なります。 データソース接続について詳しくは、「[Datasource connections](analysis-services-datasource.md)」(データソース接続) をご覧ください。

## <a name="download-the-latest-preview-data-providers"></a>最新**プレビュー** データ プロバイダーのダウンロード  
次のデータ プロバイダーを使用して、最新のバグ修正と更新プログラムを取得します。 Azure Analysis Services プレビューまたは SQL Server vNext Analysis Services に接続するときには、これらのデータ プロバイダーをお勧めします。

[MSOLAP (amd64) プレビュー](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.304.138/1033/x64/SQL_AS_OLEDB.msi)</br>
[MSOLAP (x86) プレビュー](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.304.138/1033/x86/SQL_AS_OLEDB.msi)</br>
[AMO プレビュー](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.304.138/1033/x64/SQL_AS_AMO.msi)</br>
[ADOMD プレビュー](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.304.138/1033/x64/SQL_AS_ADOMD.msi)</br>

## <a name="download-the-latest-rtm-data-providers"></a>最新 **RTM** データ プロバイダーのダウンロード  
運用環境で、完全にリリースおよびサポートされるバージョンが必要な場合は、次のデータ プロバイダーを使用します。

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>次のステップ
最新のデータ プロバイダーがインストールされていると、クライアント アプリケーションはサーバーに接続する準備ができています。 クライアントからの接続について詳しくは、「[Azure Analysis Services からデータを取得](analysis-services-connect.md)」をご覧ください。

