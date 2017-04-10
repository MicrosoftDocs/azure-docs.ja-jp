---
title: "Visual Studio での Azure クラウド サービスのロールの管理 | Microsoft Docs"
description: "Visual Studio で Azure クラウド サービスのロールを追加または削除する方法について説明します。"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 5ec9ae2e-8579-4e5d-999e-8ae05b629bd1
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/21/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: d46c2b846f5790db1e1b0e06a12184fe7bed7c34
ms.lasthandoff: 03/22/2017


---
# <a name="managing-roles-in-azure-cloud-services-with-visual-studio"></a>Visual Studio での Azure クラウド サービスのロールの管理
Azure クラウド サービスを作成した後、サービスに新しいロールを追加したり、サービスから既存のロールを削除したりできます。 既存のプロジェクトをインポートし、ロールに変換することもできます。 たとえば、ASP.NET Web アプリケーションをインポートし、そのアプリケーションを Web ロールとして指定できます。

## <a name="adding-a-role-to-an-azure-cloud-service"></a>Azure クラウド サービスにロールを追加する
Visual Studio で Azure クラウド サービス プロジェクトに Web ロールまたは worker ロールを追加するには、次の手順に従います。

1. Visual Studio で Azure クラウド サービス プロジェクトを開くか新たに作成します。

1. **ソリューション エクスプローラー**でプロジェクト ノードを展開します。

1. **[ロール]** ノードを右クリックして、コンテキスト メニューを表示します。 コンテキスト メニューの **[追加]** を選択し、現在のソリューションから既存の Web ロールまたは worker ロールを選択するか、Web ロール プロジェクトまたは worker ロール プロジェクトを作成します。 ASP.NET Web アプリケーション プロジェクトなどの適切なプロジェクトを選択し、ロール プロジェクトに関連付けることもできます。

    ![Azure クラウド サービス プロジェクトにロールを追加するメニュー オプション](media/vs-azure-tools-cloud-service-project-managing-roles/add-role.png)

## <a name="removing-a-role-from-an-azure-cloud-service"></a>Azure クラウド サービスからロールを削除する
Visual Studio で Azure クラウド サービス プロジェクトから Web ロールまたは worker ロールを削除するには、次の手順に従います。

1. Visual Studio で Azure クラウド サービス プロジェクトを開くか新たに作成します。

1. **ソリューション エクスプローラー**でプロジェクト ノードを展開します。

1. **[ロール]** ノードを展開します。

1. 削除するノードを右クリックし、コンテキスト メニューの **[削除]** を選択します。 

    ![Azure クラウド サービスにロールを追加するメニュー オプション](media/vs-azure-tools-cloud-service-project-managing-roles/remove-role.png)

## <a name="readding-a-role-to-an-azure-cloud-service-project"></a>Azure クラウド サービス プロジェクトにロールを再度追加する
クラウド サービス プロジェクトからロールを削除し、後からそのロールをプロジェクトに戻す場合は、エンドポイントや診断情報など、ロールの宣言と基本属性のみが追加されます。 `ServiceDefinition.csdef` ファイルまたは `ServiceConfiguration.cscfg` ファイルにリソースや参照は追加されません。 この情報を追加する場合は、これらのファイルに手動で情報を追加し直す必要があります。

たとえば、Web サービス ロールを削除した後、このロールを再度ソリューションに追加することになったとします。 これを実行すると、エラーが発生します。 このエラーを回避するには、次の XML に示す `<LocalResources>` 要素を `ServiceDefinition.csdef` ファイルに追加し直す必要があります。 プロジェクトに追加し直した Web サービス ロールの名前を **<LocalStorage>** 要素の名前属性の一部として使用します。 次の例では、Web サービス ロールの名前は **WCFServiceWebRole1** です。

    <WebRole name="WCFServiceWebRole1">
        <Sites>
          <Site name="Web">
            <Bindings>
              <Binding name="Endpoint1" endpointName="Endpoint1" />
            </Bindings>
          </Site>
        </Sites>
        <Endpoints>
          <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
          <Import moduleName="Diagnostics" />
        </Imports>
       <LocalResources>
          <LocalStorage name="WCFServiceWebRole1.svclog" sizeInMB="1000" cleanOnRoleRecycle="false" />
       </LocalResources>
    </WebRole>

## <a name="next-steps"></a>次のステップ
- [Visual Studio で Azure クラウド サービスのロールを構成する](vs-azure-tools-configure-roles-for-cloud-service.md)

