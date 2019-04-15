---
title: Azure Analysis Services への接続に必要なクライアント ライブラリ | Microsoft Docs
description: Azure Analysis Services に接続するクライアント アプリケーションとツールに必要なクライアント ライブラリについて説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: bfd488828d9c96461cb371b35bb0a0cb24fff373
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891941"
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Azure Analysis Services に接続するためのクライアント ライブラリ

クライアント アプリケーションとツールが Analysis Services サーバーに接続するためには、クライアント ライブラリが必要です。 Power BI Desktop、Excel、SQL Server Management Studio (SSMS)、SQL Server Data Tools (SSDT) などの Microsoft クライアント アプリケーションでは、3 つのクライアント ライブラリがすべてインストールされ、通常のアプリケーションの更新プログラムと共に更新されます。 場合によっては、新しいバージョンのクライアント ライブラリをインストールする必要があります。 カスタム クライアント アプリケーションでも、クライアント ライブラリがインストールされている必要があります。

## <a name="download-the-latest-client-libraries-windows-installer"></a>最新のクライアント ライブラリのダウンロード (Windows インストーラー)  

|ダウンロード  |製品バージョン  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.0.11.19    |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |    15.0.11.19      |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   15.15.0.0    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    15.15.0.0     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO と ADOMD (NuGet パッケージ)

Analysis Services 管理オブジェクト (AMO) と ADOMD クライアント ライブラリは、インストール可能なパッケージとして [NuGet.org](https://www.nuget.org/) で入手できます。Windows インストーラーを使用する代わりに、NuGet 参照へ移行することをお勧めします。 

|Package  | 製品バージョン  | 
|---------|---------|
|[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    15.15.0.0     |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   15.15.0.0      |

NuGet パッケージ アセンブリの AssemblyVersion は、セマンティック バージョニングであるMAJOR.MINOR.PATCH に従います。 NuGet 参照では、(MSI のインストールの結果として得られる) GAC 内に別のバージョンがある場合でも、必要なバージョンが読み込まれます。 PATCH は、リリースごとにインクリメントされます。 AMO と ADOMD のバージョンは常に同期されます。

## <a name="understanding-client-libraries"></a>クライアント ライブラリについて

Analysis Services では、データ プロバイダーとも呼ばれる、3 つのクライアント ライブラリが利用されています。 ADOMD.NET と Analysis Services 管理オブジェクト (AMO) は、マネージド クライアント ライブラリです。 Analysis Services OLE DB Provider (MSOLAP DLL) は、ネイティブ クライアント ライブラリです。 通常は、3 つすべてが同時にインストールされます。 **Azure Analysis Services では、3 つのライブラリすべての最新バージョンが必要です**。 

Power BI Desktop や Excel などの Microsoft クライアント アプリケーションでは、3 つのクライアント ライブラリがすべてインストールされ、新しいバージョンが使用可能になると更新されます。 更新のバージョンや頻度によっては、一部のクライアント ライブラリが、Azure Analysis Services で必要な最新バージョンに更新されないことがあります。 これは、カスタム アプリケーションまたは AsCmd、TOM、ADOMD.NET など他のインターフェイスでも同様です。 これらのアプリケーションでは、ライブラリを手動かプログラムでインストールする必要があります。 手動インストール用のクライアント ライブラリは、配布可能なパッケージとして SQL Server Feature Pack に含まれています。 ただし、これらのクライアント ライブラリは SQL Server のバージョンに対応しており、最新版ではない可能性があります。  

クライアント接続用のクライアント ライブラリは、Azure Analysis Services サーバーをデータ ソースに接続するために必要なデータ プロバイダーとは異なります。 データソース接続について詳しくは、「[Datasource connections](analysis-services-datasource.md)」(データソース接続) をご覧ください。

## <a name="client-library-types"></a>クライアント ライブラリの種類

### <a name="analysis-services-ole-db-provider-msolap"></a>Analysis Services OLE DB Provider (MSOLAP) 

 Analysis Services OLE DB Provider (MSOLAP) は、Analysis Services データベース接続用のネイティブ クライアント ライブラリです。 ADOMD.NET と AMO の両方に間接的に使用され、接続要求をデータ プロバイダーに委任します。 アプリケーション コードから OLE DB Provider を直接呼び出すこともできます。  
  
 Analysis Services OLE DB Provider は、Analysis Services データベースへのアクセスに使用されるほとんどのツールおよびクライアント アプリケーションによって、自動的にインストールされます。 Analysis Services データへのアクセスに使用されるコンピューターにインストールする必要があります。  
  
 OLE DB プロバイダーは、多くの場合、接続文字列で指定されます。 Analysis Services 接続文字列では、OLE DB Provider を参照するために、MSOLAP.\<version>.dll という別の用語体系が使用されます。

### <a name="amo"></a>AMO  

 AMO は、サーバーの管理とデータの定義に使用されるマネージド クライアント ライブラリです。 ツールおよびクライアント アプリケーションによってインストールされ、使用されます。 たとえば、SQL Server Management Studio (SSMS) は、AMO を使用して Analysis Services に接続します。 AMO を使用した接続は、通常は最小で、`"data source=\<servername>"` で構成されています。 接続の確立後は、API を使用して、データベース コレクションおよび主要なオブジェクトを操作します。 SSDT と SSMS のどちらも、AMO を使用して Analysis Services インスタンスに接続します。  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET は、Analysis Services データの照会に使用されるマネージド データ クライアント ライブラリです。 ツールおよびクライアント アプリケーションによってインストールされ、使用されます。 
  
 データベースに接続する際、3 つのライブラリの接続文字列プロパティは、どれも似ています。 [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](/dotnet/api/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring#Microsoft_AnalysisServices_AdomdClient_AdomdConnection_ConnectionString) を使用して ADOMD.NET 用に定義するほぼすべての接続文字列が、AMO と Analysis Services OLE DB Provider (MSOLAP) でも機能します。 詳細については、「[接続文字列プロパティ &#40;Analysis Services&#41;](https://docs.microsoft.com/sql/analysis-services/instances/connection-string-properties-analysis-services)」を参照してください。  

  
##  <a name="bkmk_LibUpdate"></a>クライアント ライブラリのバージョンを確認する方法   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  `C:\Program Files\Microsoft Analysis Services\AS OLEDB\` にアクセスします。 複数のフォルダーがある場合は、番号の大きい方を選択します。
  
2.  **[msolap.dll]** > **[プロパティ]** > **[詳細]** の順にクリック (最初は右クリック) します。 ファイル名が msolap140.dll である場合は、最新のバージョンよりも古いバージョンであるため、アップグレードする必要があります。
    
    ![クライアント ライブラリの詳細](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\` にアクセスします。 複数のフォルダーがある場合は、番号の大きい方を選択します。
2. **[Microsoft.AnalysisServices]** > **[プロパティ]** > **[詳細]** の順にクリック (最初は右クリック) します。  

### <a name="adomd"></a>ADOMD

1. `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\` にアクセスします。 複数のフォルダーがある場合は、番号の大きい方を選択します。
2. **[Microsoft.AnalysisServices.AdomdClient]** > **[プロパティ]** > **[詳細]** の順にクリック (最初は右クリック) します。  


## <a name="next-steps"></a>次の手順
[Excel を使用した接続](analysis-services-connect-excel.md)    
[Power BI を使用した接続](analysis-services-connect-pbi.md)
