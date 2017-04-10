---
title: "Service Fabric アプリケーションのアップグレードの構成 | Microsoft Docs"
description: "Microsoft Visual Studio を使用して、Service Fabric アプリケーションをアップグレードするための設定を構成する方法について説明します。"
services: service-fabric
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: tglee
ms.assetid: 1757ba85-0b7b-4f16-8a23-2ddaa61c86c6
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/18/2016
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 121f1d485e34ba30d3e6c2e5d91da633771e80ab


---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Visual Studio での Service Fabric アプリケーションのアップグレードの構成
Visual Studio Service Fabric ツールは、ローカルまたはリモート クラスターへの発行のアップグレード サポートを提供します。 テストやデバッグ中にアプリケーションを置き換えるのではなく、アプリケーションを新しいバージョンにアップグレードすることには、2 つの利点があります。

* 1 つは、アップグレード中にアプリケーション データが失われないことです。
* もう 1 つは、複数のアップグレード ドメインにわたって十分な数のサービス インスタンスが存在すれば、アップグレード中にサービスの中断が発生しないので、高可用性が実現されることです。

アプリケーションのアップグレード中に、アプリケーションに対してテストを実行できます。

## <a name="parameters-needed-to-upgrade"></a>アップグレードに必要なパラメーター
標準とアップグレードの 2 種類のデプロイメントから選択できます。 標準デプロイメントではクラスター上の以前のデプロイメント情報とデータが消去され、アップグレード デプロイメントではこれらが保持されます。 Visual Studio で Service Fabric アプリケーションをアップグレードするときには、アプリケーション アップグレード パラメーターと正常性チェック ポリシーを提供する必要があります。 アプリケーション アップグレード パラメーターによってアップグレードが制御され、正常性チェック ポリシーによってアップグレードが成功したかどうかが判断されます。 詳細については、「 [アプリケーション アップグレードのパラメーター](service-fabric-application-upgrade-parameters.md) 」を参照してください。

*Monitored*、*UnmonitoredAuto*、*UnmonitoredManual* の 3 種類のアップグレード モードがあります。

* Monitored アップグレードでは、アップグレードとアプリケーションの正常性チェックが自動化されます。
* UnmonitoredAuto アップグレードでは、アップグレードは自動化されますが、アプリケーションの正常性チェックは省略されます。
* UnmonitoredManual アップグレードを実行する場合は、各アップグレード ドメインを手動でアップグレードする必要があります。

各アップグレード モードには、それぞれ異なるパラメーター セットが必要です。 使用できるアップグレード オプションの詳細については、「 [アプリケーション アップグレードのパラメーター](service-fabric-application-upgrade-parameters.md) 」を参照してください。

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Visual Studio での Service Fabric アプリケーションのアップグレード
Visual Studio Service Fabric ツールを使用して Service Fabric アプリケーションをアップグレードする場合、 **[アプリケーションのアップグレード]** チェック ボックスをオンにして、発行プロセスが標準デプロイメントではなくアップグレード デプロイメントであることを指定できます。

### <a name="to-configure-the-upgrade-parameters"></a>アップグレード パラメーターを構成するには
1. チェック ボックスの横の **[設定]** ボタンをクリックします。 **[アップグレード パラメーターの編集]** ダイアログ ボックスが表示されます。 **[アップグレード パラメーターの編集]** ダイアログ ボックスでは、Monitored、UnmonitoredAuto、UnmonitoredManual の各アップグレード モードをサポートしています。
2. 使用するアップグレード モードを選択し、パラメーター グリッドに値を入力します。

    各パラメーターには既定値があります。 オプション パラメーターの *DefaultServiceTypeHealthPolicy* は、ハッシュ テーブルの入力を受け取ります。 *DefaultServiceTypeHealthPolicy*のハッシュ テーブルの入力形式の例を次に示します。

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* は、次の形式のハッシュ テーブルの入力を受け取るもう 1 つのオプション パラメーターです。

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    実際の例を次に示します。

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. UnmonitoredManual アップグレード モードを選択した場合は、PowerShell コンソールを手動で起動してアップグレード プロセスを続行し、プロセスを完了する必要があります。 手動アップグレードのしくみについては、「 [Service Fabric アプリケーションのアップグレード: 高度なトピック](service-fabric-application-upgrade-advanced.md) 」を参照してください。

## <a name="upgrade-an-application-by-using-powershell"></a>PowerShell を使用したアプリケーションのアップグレード
PowerShell コマンドレットを使用して、Service Fabric アプリケーションをアップグレードできます。 詳細については、「[Service Fabric アプリケーションのアップグレード チュートリアル](service-fabric-application-upgrade-tutorial.md)」および「[Start-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/mt125975.aspx)」を参照してください。

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>アプリケーション マニフェスト ファイルでの正常性チェック ポリシーの指定
Service Fabric アプリケーションの各サービスでは、既定値を上書きした独自の正常性ポリシー パラメーターを使用できます。 これらのパラメーター値は、アプリケーション マニフェスト ファイルで指定できます。

次の例は、アプリケーション マニフェストで、サービスごとに独自の正常性チェック ポリシーを適用する方法を示しています。

```xml
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## <a name="next-steps"></a>次のステップ
アプリケーションのデプロイの詳細については、「 [Azure Service Fabric への既存のアプリケーションのデプロイ](service-fabric-deploy-existing-app.md)」をご覧ください。



<!--HONumber=Nov16_HO3-->


