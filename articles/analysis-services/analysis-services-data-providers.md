---
title: "Azure Analysis Services への接続に必要なクライアント ライブラリ | Microsoft Docs"
description: "Azure Analysis Services に接続するクライアント アプリケーションとツールに必要なクライアント ライブラリについて説明します。"
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
ms.date: 08/15/2017
ms.author: owend
ms.openlocfilehash: a96e7fe671dc051da35168fa7b49ecba53b4c4a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Azure Analysis Services に接続するためのクライアント ライブラリ

クライアント アプリケーションとツールが Analysis Services サーバーに接続するためには、クライアント ライブラリが必要です。 

Analysis Services では、3 つのクライアント ライブラリが利用されています。 ADOMD.NET と Analysis Services 管理オブジェクト (AMO) は、マネージ クライアント ライブラリです。 Analysis Services OLE DB プロバイダー (MSOLAP DLL) は、ネイティブ クライアント ライブラリです。 通常は、3 つすべてが同時にインストールされます。 Azure Analysis Services では、これらの最新バージョンが必要となります。 

Power BI Desktop や Excel などの Microsoft クライアント アプリケーションでは、3 つのクライアント ライブラリがすべてインストールされます。 ただし、更新のバージョンや頻度によっては、クライアント ライブラリが、Azure Analysis Services で必要な最新バージョンに更新されないことがあります。 これは、カスタム アプリケーションまたは AsCmd、TOM、ADOMD.NET など他のインターフェイスでも同様です。 これらのアプリケーションでは、ライブラリを手動でインストールする必要があります。 手動インストール用のクライアント ライブラリは、配布可能なパッケージとして SQL Server Feature Pack に含まれています。 ただし、これらのクライアント ライブラリは SQL Server のバージョンに対応しており、最新版ではない可能性があります。  

クライアント接続用のクライアント ライブラリは、Azure Analysis Services サーバーをデータ ソースに接続するために必要なデータ プロバイダーとは異なります。 データソース接続について詳しくは、「[Datasource connections](analysis-services-datasource.md)」(データソース接続) をご覧ください。

## <a name="download-the-latest-client-libraries"></a>最新のクライアント ライブラリをダウンロード  
運用環境で、完全にリリースおよびサポートされるバージョンが必要な場合は、次のクライアント ライブラリを使用します。

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>次のステップ
[Excel を使用した接続](analysis-services-connect-excel.md)    
[Power BI を使用した接続](analysis-services-connect-pbi.md)
