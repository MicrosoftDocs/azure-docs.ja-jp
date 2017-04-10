---
title: "Marketplace 用データ サービスの作成ガイド | Microsoft Docs"
description: "Azure Marketplace で購入できるデータ サービスを作成、認定、デプロイする方法について詳しく説明します。"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 148f8638-ee80-4100-8d63-5afa4167ca1b
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f479602647d67e7048371df76b128e33b27c1014


---
# <a name="examples-of-mapping-an-existing-web-service-to-odata-through-csdls"></a>CSDL を使用した既存の Web サービスの OData へのマッピングの例
> [!IMPORTANT]
> **現時点では、新しいデータ サービスの発行元はオンボードされなくなりました。一覧への新しいデータ サービスの追加は承認されません。** SaaS ビジネス アプリケーションを AppSource で発行する場合、詳細については[こちら](https://appsource.microsoft.com/partners)をご覧ください。 IaaS アプリケーションまたは開発者サービスを Azure Marketplace で発行する場合、詳細については[こちら](https://azure.microsoft.com/marketplace/programs/certified/)をご覧ください。
> 
> 

## <a name="example-functionimport-for-raw-data-returned-using-post"></a>例： "POST" を使用して返される "生" データのための FunctionImport
POST の生データを使用して、新しい従属要素を作成し、サーバーによって定義された URL (場所) を返したり、サーバーによって定義された URL で従属要素の一部を更新したりします。  ここでは従属要素はストリームです。つまり、 テキスト ファイルなどの 非構造化データです。  場所がないと、POST はべき等ではない状態になることに注意してください。

        <!--  No EntitySet or EntityType nodes required for Raw output-->
        <FunctionImport Name="AddUsageEvent" ReturnType="Raw(text/plain)" d:EncodeParameterValues="true" d:AllowedHttpMethods="POST" d:BaseUri="http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <d:Title d:Map="" />
        <d:Rights d:Map="" />
        <d:Description>Add usage event (data acquisition)</d:Description>
        <d:Headers>
        <d:Header d:Name="Content-Type" d:Value="application/xml;charset=UTF-8" />
        </d:Headers>
        <Parameter Name="name" Nullable="false" Mode="In" Type="String" d:Description="first name" d:SampleValues="John|Joe|Bill"  d:EncodeParameterValue="true" />
        <d:Namespaces>
        <d:Namespace d:Prefix="p" d:Uri="http://schemas.organization.net/2010/04/myNamespace " />
        <d:Namespace d:Prefix="p2" d:Uri=" http://schemas.organization.net/2010/04/myNamespace2 " />
        </d:Namespaces>
        </FunctionImport>

## <a name="example-functionimport-using-delete"></a>例: "DELETE" を使用する FunctionImport
DELETE を使用して、指定した URI を削除します。

        <EntitySet Name="DeleteUsageFileEntitySet" EntityType="MyOffer.DeleteUsageFileEntity" />
        <FunctionImport Name="DeleteUsageFile" EntitySet="DeleteUsageFileEntitySet" ReturnType="Collection(MyOffer.DeleteUsageFileEntity)"  d:AllowedHttpMethods="DELETE" d:EncodeParameterValues="true” d:BaseUri=”http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643" >
        <d:Title d:Map="" />
        <d:Rights d:Map="" />
        <d:Description>Delete usage File</d:Description>
        <d:Headers>
        <d:Header d:Name="Content-Type" d:Value="application/xml;charset=UTF-8" />
        </d:Headers>
        <Parameter Name="name" Nullable="false" Mode="In" Type="String" d:Description="first name" d:SampleValues="John|Joe|Bill"  d:EncodeParameterValue="true" />
        <d:Namespaces>
        <d:Namespace d:Prefix="p" d:Uri="http://schemas.organization.net/2010/04/myNamespace " />
        <d:Namespace d:Prefix="p2" d:Uri=" http://schemas.organization.net/2010/04/myNamespace2 " />
        </d:Namespaces>
        </FunctionImport>
        <EntityType Name="DeleteUsageFileEntity" d:Map="//boolean">
        <Property Name="boolean" Type="String" Nullable="true" d:Map="./boolean" />
        </EntityType>

## <a name="example-functionimport-using-post"></a>例: "POST" を使用する FunctionImport
POST の生データを使用して、新しい従属要素を作成し、サーバーによって定義された URL (場所) を返したり、サーバーによって定義された URL で従属要素の一部を更新したりします。  ここでは従属要素は構造化データです。 場所がない場合、POST はべき等ではないことに注意してください。

        <EntitySet Name="CreateANewModelEntitySet2" EntityType=" MyOffer.CreateANewModelEntity2" />
        <FunctionImport Name="CreateModel" EntitySet="CreateANewModelEntitySet2" ReturnType="Collection(MyOffer.CreateANewModelEntity2)" d:EncodeParameterValues="true" d:AllowedHttpMethods="POST" d:BaseUri=”http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <d:Title d:Map="" />
        <d:Rights d:Map="" />
        <d:Description>Create A New Model</d:Description>
        <d:Headers>
        <d:Header d:Name="Content-Type" d:Value="application/xml;charset=UTF-8" />
        </d:Headers>
        <Parameter name="name" Nullable="false" Mode="In" Type="String" d:Description="first name" d:SampleValues="John|Joe|Bill"  d:EncodeParameterValue="true" />
        <d:Namespaces>
        <d:Namespace d:Prefix="p" d:Uri="http://schemas.organization.net/2010/04/myNamespace " />
        <d:Namespace d:Prefix="p2" d:Uri=" http://schemas.organization.net/2010/04/myNamespace2 " />
        </d:Namespaces>
        </FunctionImport>

## <a name="example-functionimport-using-put"></a>例: "PUT" を使用する FunctionImport
PUT を使用して、新しい従属要素を作成したり、サーバーによって定義された URL で従属要素全体を更新したりします。  ここでは従属要素は構造化データであり、PUT はべき等であるため、複数回発生しても結果は同じ状態になります  (x=5)。  PUT は、指定したリソースの全内容と共に使用する必要があります。

        <EntitySet Name="UpdateAnExistingModelEntitySet" EntityType="MyOffer.UpdateAnExistingModelEntity" />
        <FunctionImport Name="UpdateModel" EntitySet="UpdateAnExistingModelEntitySet" ReturnType="Collection(MyOffer.UpdateAnExistingModelEntity)" d:EncodeParameterValues="true" d:AllowedHttpMethods="PUT" d:BaseUri=”http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <d:Title d:Map="" />
        <d:Rights d:Map="" />
        <d:Description>Update an Existing Model</d:Description>
        <d:Headers>
        <d:Header d:Name="Content-Type" d:Value="application/xml;charset=UTF-8" />
        </d:Headers>
        <Parameter Name="name" Nullable="false" Mode="In" Type="String" d:Description="first name" d:SampleValues="John|Joe|Bill"  d:EncodeParameterValue="true" />
        <d:Namespaces>
        <d:Namespace d:Prefix="p" d:Uri="http://schemas.organization.net/2010/04/myNamespace " />
        <d:Namespace d:Prefix="p2" d:Uri=" http://schemas.organization.net/2010/04/myNamespace2 " />
        </d:Namespaces>
        </FunctionImport>
        <EntityType Name="UpdateAnExistingModelEntity" d:Map="//string">
        <Property Name="string"     Type="String" Nullable="true" d:Map="./string" />
        </EntityType>


## <a name="example-functionimport-for-raw-data-returned-using-put"></a>例： "PUT" を使用して返される "生" データのための FunctionImport
PUT の生データを使用して、新しい従属要素を作成したり、サーバーによって定義された URL で従属要素全体を更新したりします。  ここでは従属要素はストリームです。つまり、 テキスト ファイルなどの 非構造化データです。  PUT はべき等であるため、複数回発生しても結果は同じ状態になります  (x=5)。  PUT は、指定したリソースの全内容と共に使用する必要があります。

        <!--  No EntitySet or EntityType nodes required for Raw output-->
        <FunctionImport Name="CancelBuild” ReturnType="Raw(text/plain)" d:AllowedHttpMethods="PUT" d:EncodeParameterValues="true" d:BaseUri=” http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <d:Title d:Map="" />
        <d:Rights d:Map="" />
        <d:Description>Cancel Build</d:Description>
        <d:Headers>
        <d:Header d:Name="Content-Type" d:Value="application/xml;charset=UTF-8" />
        </d:Headers>
        <Parameter Name="name" Nullable="false" Mode="In" Type="String" d:Description="first name" d:SampleValues="John|Joe|Bill"  d:EncodeParameterValue="true" />
        <d:Namespaces>
        <d:Namespace d:Prefix="p" d:Uri="http://schemas.organization.net/2010/04/myNamespace " />
        <d:Namespace d:Prefix="p2" d:Uri=" http://schemas.organization.net/2010/04/myNamespace2 " />
        </d:Namespaces>
        </FunctionImport>


## <a name="example-functionimport-for-raw-data-returned-using-get"></a>例： "GET" を使用して返される "生" データのための FunctionImport
GET の生データを使用して、構造化されていない従属要素 (テキストなど) を返します。

        <!--  No EntitySet or EntityType nodes required for Raw output-->
        <FunctionImport Name="GetModelUsageFile" ReturnType="Raw(text/plain)" d:EncodeParameterValues="true" d:AllowedHttpMethods="GET" d:BaseUri="https://cmla.cloudapp.net/api2/model/builder/build?buildId={buildId}&amp;apiVersion={apiVersion}">
        <d:Title d:Map="" />
        <d:Rights d:Map="" />
        <d:Description>Download A Models Usage File</d:Description>
        <d:Headers>
        <d:Header d:Name="Accept" d:Value="application/xml,application/xhtml+xml,text/html;" />
        <d:Header d:Name="Content-Type" d:Value="application/xml;charset=UTF-8" />
        </d:Headers>
        <Parameter Name="name" Nullable="false" Mode="In" Type="String" d:Description="first name" d:SampleValues="John|Joe|Bill"  d:EncodeParameterValue="true" />
        <d:Namespaces>
        <d:Namespace d:Prefix="p" d:Uri="http://schemas.organization.net/2010/04/myNamespace " />
        <d:Namespace d:Prefix="p2" d:Uri=" http://schemas.organization.net/2010/04/myNamespace2 " />
        </d:Namespaces>
        </FunctionImport>

## <a name="example-functionimport-for-paging-through-returned-data"></a>例: 返されたデータの "ページング" のための FunctionImport
GET で取得したデータの RESTful なページングを実装します。  既定のページングは、データ ページあたり 100 行に設定されています。

        <EntitySet Name=”CropEntitySet" EntityType="MyOffer.CropEntity" />
        <FunctionImport    Name="GetCropReport" EntitySet="CropEntitySet” ReturnType="Collection(MyOffer.CropEntity)" d:EmitSelfLink="false" d:EncodeParameterValues="true" d:Paging="SkipTake" d:MaxPageSize="100" d:BaseUri="http://api.mydata.org/Crop? report={report}&amp;series={series}&amp;start={$skip}&amp;size=100">
        <Parameter Name="report" Type="Int32" Mode="In" Nullable="false" d:SampleValues="4"  d:enum="1|2|3|4|5|6|7|8|9|10|11|12|13|14|15|16|17|18|19"  />
        <Parameter Name="series"    Type="String"    Mode="In" Nullable="false" d:SampleValues="FARM" />
        <d:Headers>
        <d:Header d:Name="Content-Type" d:Value="text/xml;charset=UTF-8" />
        </d:Headers>
        <d:Namespaces>
        <d:Namespace d:Prefix="diffgr" d:Uri="urn:schemas-microsoft-com:xml-diffgram-v1" />
        </d:Namespaces>
        </FunctionImport>

## <a name="see-also"></a>関連項目
* 全体的な OData マッピング プロセスと目的を理解したい場合は、この [データ サービスの OData マッピング](marketplace-publishing-data-service-creation-odata-mapping.md) に関する記事を読んで、定義、構造、手順を確認してください。
* 特定のノードとそのパラメーターについて知りたい場合は、定義と説明、例、ユース ケースのコンテキストなどが記載された、この [データ サービスの OData マッピング ノード](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) に関する記事をご覧ください。
* データ サービスを Azure Marketplace に発行するための指定のパスに戻る場合は、この [データ サービスの発行ガイド](marketplace-publishing-data-service-creation.md)をご覧ください。




<!--HONumber=Nov16_HO3-->


