---
title: Azure Service Fabric で構成ファイルをパラメーター化する | Microsoft Docs
description: Service Fabric で構成ファイルをパラメーター化する方法を学習します。
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/09/2018
ms.author: mikhegn
ms.openlocfilehash: dad497978de7187177998524db3b2f2ee448c717
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464778"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Service Fabric で構成ファイルをパラメーター化する方法

この記事では、Service Fabric で構成ファイルをパラメーター化する方法について説明します。  複数の環境向けにアプリケーションを管理する基本的な概念にまだ精通していない場合は、「[複数の環境向けのアプリケーションの管理](service-fabric-manage-multiple-environment-app-configuration.md)」を参照してください。

## <a name="procedure-for-parameterizing-configuration-files"></a>構成ファイルをパラメーター化する手順

この例では、アプリケーションの展開でパラメーターを使って構成値をオーバーライドします。

1. サービス プロジェクト内の *\<MyService>\PackageRoot\Config\Settings.xml* ファイルを開きます。
1. 次の XML を追加して、構成パラメーターの名前と値を設定します (たとえば、キャッシュ サイズ = 25)。

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. ファイルを保存して閉じます。
1. *\<MyApplication>\ApplicationPackageRoot\ApplicationManifest.xml* ファイルを開きます。
1. ApplicationManifest.xml ファイルで、`Parameters` 要素にパラメーターと既定値を宣言します。  パラメーター名にはサービスの名前 (たとえば "MyService") を含めることをお勧めします。

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. ApplicationManifest.xml ファイルの `ServiceManifestImport` セクションに `ConfigOverrides` と `ConfigOverride` 要素を追加し、構成パッケージ、セクション、およびパラメーターを参照します。

   ```xml
    <ConfigOverrides>
      <ConfigOverride Name="Config">
          <Settings>
            <Section Name="MyConfigSection">
                <Parameter Name="CacheSize" Value="[MyService_CacheSize]" />
            </Section>
          </Settings>
      </ConfigOverride>
    </ConfigOverrides>
   ```

> [!NOTE]
> ConfigOverride を追加すると、Service Fabric は常に、アプリケーション パラメーターか、アプリケーション マニフェストで指定されている既定値を選びます。
>
>

## <a name="next-steps"></a>次の手順
Visual Studio で使用可能なその他のアプリケーション管理機能については、 [Visual Studio での Service Fabric アプリケーションの管理](service-fabric-manage-application-in-visual-studio.md)に関する記事をご覧ください。
