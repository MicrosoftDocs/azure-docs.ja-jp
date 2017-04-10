---
title: "Azure Service Fabric クラスターのアップグレード | Microsoft Docs"
description: "Service Fabric クラスターを実行している Service Fabric コード、構成、またはその両方をアップグレードします。たとえば、クラスター アップグレード モードの設定、証明書のアップグレード、アプリケーション ポートの追加、OS 修正プログラムの適用などを行います。 アップグレードを実行しているときに、どのようなことが起きるでしょうか?"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: 0231c3148d03ffef0a518a68bb79398462da2605
ms.openlocfilehash: 89721efbb9f05871716ca1b16ad0d54eaf1ffd62
ms.lasthandoff: 02/16/2017


---
# <a name="upgrade-an-azure-service-fabric-cluster"></a>Azure Service Fabric クラスターのアップグレード
> [!div class="op_single_selector"]
> * [Azure クラスター](service-fabric-cluster-upgrade.md)
> * [スタンドアロン クラスター](service-fabric-cluster-upgrade-windows-server.md)
> 
> 

最新のシステムでは、アップグレード性を考慮した設計を行うことが、製品の長期的な成功を達成する鍵となります。 Azure Service Fabric クラスターはお客様が所有するリソースですが、一部は Microsoft によって管理されます。 この記事では、何が自動的に管理され、何をお客様が構成できるかについて説明します。

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>クラスター上で動作するファブリック バージョンの制御
クラスターは、新しいバージョンのファブリックがマイクロソフトからリリースされたときに自動アップグレードを適用するように設定できます。また、サポートされているファブリック バージョンから、クラスターで使用するバージョンを選択するように設定することもできます。

そのためには、クラスターの作成時に (または後から稼働中のクラスターに対して) Resource Manager を使用するか、またはポータルで "upgradeMode" クラスター構成を設定します。 

> [!NOTE]
> クラスターには必ず、サポートされているバージョンのファブリックを使用してください。 Microsoft が Service Fabric の新バージョン リリースをアナウンスした日から最短で 60 日後には、以前のバージョンがサポート期間の終了として指定されます。 新バージョンのリリースは、 [Service Fabric チーム ブログ](https://blogs.msdn.microsoft.com/azureservicefabric/) でお知らせします。 その後間もなく新しいバージョンが利用できるようになります。 
> 
> 

現在クラスターで実行されているバージョンの有効期間が終了する 14 日前に、正常性に関するイベントが生成され、クラスターの正常性が警告状態に移行します。 サポートされているバージョンのファブリックにアップグレードするまで、クラスターは警告状態のままとなります。

### <a name="setting-the-upgrade-mode-via-portal"></a>ポータルでのアップグレード モードの設定
ファブリックのアップグレード モードは、クラスターの作成時に自動または手動に設定することができます。

![Create_Manualmode][Create_Manualmode]

稼働中のクラスターに対しては、管理操作からファブリックのアップグレード モードを自動または手動に設定することができます。 

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>ポータルで手動モードに設定されたクラスターを新バージョンにアップグレードする
新しいバージョンにアップグレードするために必要な操作は、ドロップダウンに表示されたバージョンの中からいずれかを選択し、保存するだけです。 ファブリックのアップグレードが自動的に開始されます。 クラスター正常性ポリシー (ノードの正常性と、クラスターで実行されているすべてのアプリケーションの正常性の組み合わせ) は、アップグレードの実行中、遵守されます。

クラスター正常性ポリシーが満たされていない場合は、アップグレードがロールバックされます。 正常性ポリシーのカスタマイズ方法については、このドキュメントの後半で詳しく取り上げています。下へスクロールして該当セクションを参照してください。 

ロールバックの原因となった問題を解決した後、前述の手順に従ってもう一度アップグレードを実行してください。

![Manage_Automaticmode][Manage_Automaticmode]

### <a name="setting-the-upgrade-mode-via-a-resource-manager-template"></a>Resource Manager テンプレートでのアップグレード モードの設定
Microsoft.ServiceFabric/クラスター リソース定義に "upgradeMode" 構成を追加し、サポートされているいずれかのファブリック バージョンを "clusterCodeVersion" に指定して (下記)、テンプレートをデプロイします。 "upgradeMode" には "Manual" または "Automatic" のいずれかを値として指定できます。

![ARMUpgradeMode][ARMUpgradeMode]

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Resource Manager テンプレートで手動モードに設定されたクラスターを新バージョンにアップグレードする
手動モードのクラスターを新バージョンにアップグレードするには、"clusterCodeVersion" に指定されているバージョンを、サポートされているいずれかのバージョンに変更してデプロイします。 テンプレートをデプロイすると、ファブリックのアップグレードが自動的に開始されます。 クラスター正常性ポリシー (ノードの正常性と、クラスターで実行されているすべてのアプリケーションの正常性の組み合わせ) は、アップグレードの実行中、遵守されます。

クラスター正常性ポリシーが満たされていない場合は、アップグレードがロールバックされます。 正常性ポリシーのカスタマイズ方法については、このドキュメントの後半で詳しく取り上げています。下へスクロールして該当セクションを参照してください。 

ロールバックの原因となった問題を解決した後、前述の手順に従ってもう一度アップグレードを実行してください。

### <a name="get-list-of-all-available-version-for-all-environments-for-a-given-subscription"></a>特定のサブスクリプションのすべての環境を対象に利用できる全バージョンの一覧を取得する
以下のコマンドを実行すると、次のような結果が出力されます。

特定のリリースの有効期限が近づいている (または終了している) ことは "supportExpiryUtc" で確認できます。 最新リリースに有効な日付はありません。"9999-12-31T23:59:59.9999999" という値が設定されていますが、これは単に有効期限が設定されていないことを意味するものです。

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2016-09-01

Example: https://management.azure.com/subscriptions/1857f442-3bce-4b96-ad95-627f76437a67/providers/Microsoft.ServiceFabric/locations/eastus/clusterVersions?api-version=2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }


```

## <a name="fabric-upgrade-behavior-when-the-cluster-upgrade-mode-is-automatic"></a>クラスターのアップグレード モードが Automatic であるときのファブリックのアップグレード動作
Microsoft は、Azure クラスターで実行されるファブリック コードと構成を管理します。 必要に応じて、ソフトウェアに対して自動的な監視付きアップグレードを実行します。 これらのアップグレードは、コード、構成、またはその両方で行うことができます。 これらのアップグレードからアプリケーションが影響を受けない、またはその影響を最小限にするために、アップグレードは次のフェーズで行われます。

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>フェーズ 1: アップグレードが、すべてのクラスター正常性ポリシーを使用して実行される
このフェーズ中、アップグレード ドメインは 1 つずつ処理され、クラスターで実行されていたアプリケーションはダウンタイムなしで実行され続けます。 クラスター正常性ポリシー (ノードの正常性と、クラスターで実行されているすべてのアプリケーションの正常性の組み合わせ) は、アップグレードの実行中、遵守されます。

クラスター正常性ポリシーが満たされていない場合は、アップグレードがロールバックされます。 その後、サブスクリプションの所有者に電子メールが送信されます。 この電子メールには以下の情報が含まれています。

* クラスターのアップグレードをロールバックする必要があるという通知
* 推奨される対応策 (ある場合)
* フェーズ 2 を実行するまでの日数 (n)。

インフラストラクチャに関する理由でアップグレードに失敗した場合は、同じアップグレードが数回実行されます。 電子メールの送信日から n 日後に、フェーズ 2 に進みます。

クラスター正常性ポリシーが満たされた場合は、アップグレードが成功したと見なされ、完了としてマークされます。 このフェーズの最初のアップグレードで成功することも、何回目かの再実行で成功することもあります。 実行が成功した場合、電子メールでの確認はありません。 これは、送信される電子メールが多くなりすぎないようにするためです。電子メールを受信するのは、正常でないことが起きた場合だけです。 クラスターのアップグレードの大半は、アプリケーションの可用性に影響することなく、成功すると思われます。

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>フェーズ 2: アップグレードが、既定の正常性ポリシーのみを使用して実行される
このフェーズでの正常性ポリシーは、アップグレードの開始時に正常だったアプリケーションの数が、アップグレード プロセス中も同じ数であり続けるように設定されています。 フェーズ 1 のように、フェーズ 2 のアップグレードではアップグレード ドメインが 1 つずつ処理され、クラスターで実行されていたアプリケーションはダウンタイムなしで実行され続けます。 クラスター正常性ポリシー (ノードの正常性と、クラスターで実行されているすべてのアプリケーションの正常性の組み合わせ) は、アップグレードの実行中、遵守されます。

有効なクラスター正常性ポリシーが満たされていない場合は、アップグレードがロールバックされます。 その後、サブスクリプションの所有者に電子メールが送信されます。 この電子メールには以下の情報が含まれています。

* クラスターのアップグレードをロールバックする必要があるという通知
* 推奨される対応策 (ある場合)
* フェーズ 3 を実行するまでの日数 (n)。

インフラストラクチャに関する理由でアップグレードに失敗した場合は、同じアップグレードが数回実行されます。 リマインダーの電子メールが、n 日の数日前に送信されます。 電子メールの送信日から n 日後に、フェーズ 3 に進みます。 フェーズ 2 で送信された電子メールは、真剣に受け止め、修復操作を実行する必要があります。

クラスター正常性ポリシーが満たされた場合は、アップグレードが成功したと見なされ、完了としてマークされます。 このフェーズの最初のアップグレードで成功することも、何回目かの再実行で成功することもあります。 実行が成功した場合、電子メールでの確認はありません。

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>フェーズ 3: アップグレードが、アグレッシブな正常性ポリシーを使用して実行される
このフェーズでのこれらの正常性ポリシーは、アプリケーションの正常性よりもアップグレードの完了のために調整されています。 このフェーズで終了するクラスター アップグレードは、ほとんどありません。 クラスターがこのフェーズに達すると、アプリケーションが正常な状態でなくなるか、可用性が失われたりする確率が高くなります。

他の 2 つのフェーズと同様に、フェーズ 3 でもアップグレード ドメインが 1 つずつ処理されます。

クラスター正常性ポリシーが満たされていない場合は、アップグレードがロールバックされます。 インフラストラクチャに関する理由でアップグレードに失敗した場合は、同じアップグレードが数回実行されます。 その後、そのクラスターはサポートやアップグレードを受信しなくなるように固定されます。

この情報と修復操作が記載された電子メールが、サブスクリプションの所有者に送信されます。 クラスターがフェーズ 3 でも失敗する状態になることは、想定していません。

クラスター正常性ポリシーが満たされた場合は、アップグレードが成功したと見なされ、完了としてマークされます。 このフェーズの最初のアップグレードで成功することも、何回目かの再実行で成功することもあります。 実行が成功した場合、電子メールでの確認はありません。

## <a name="cluster-configurations-that-you-control"></a>お客様が制御するクラスター構成
ここでは、稼働中のクラスターに関して、アップグレード モードの設定以外に変更できる構成について取り上げます。

### <a name="certificates"></a>証明書
ポータルを使用すると、クラスターの新しい証明書の追加、または削除を簡単に行うことができます。  [詳細な手順については、こちらのドキュメント](service-fabric-cluster-security-update-certs-azure.md)

![スクリーンショットは、Azure ポータルの証明書の拇印を示しています。][CertificateUpgrade]

### <a name="application-ports"></a>アプリケーション ポート
アプリケーション ポートは、ノードの種類に関連付けられた Load Balancer リソースのプロパティを変更することで変更できます。 ポータルを使用することも、リソース マネージャーの PowerShell を直接使用することもできます。

ノードの種類内のすべての VM で新しいポートを開くには、次の手順を行います。

1. 適切なロード バランサーに新しいプローブを追加します。
   
    ポータルを使用してクラスターをデプロイした場合、ロード バランサーには、ノードの種類ごとに "Resource group-NodeTypename の LB-name" という名前が付けられます。 ロード バランサー名はリソース グループ内でのみ一意であるため、検索は特定のリソース グループの下で行うことをお勧めします。
   
    ![スクリーンショットでは、ポータルのロード バランサーにプローブを追加しています。][AddingProbes]
2. ロード バランサーに新しい規則を追加します。
   
    前の手順で作成したプローブを使用して、同じロード バランサーに新しい規則を追加します。
   
    ![Adding a new rule to a load balancer in the portal.][AddingLBRules]

### <a name="placement-properties"></a>配置プロパティ
ノードの種類ごとに、アプリケーションで使用するカスタム配置プロパティを追加できます。 NodeType は、明示的に追加せずに使用できる、既定のプロパティです。

> [!NOTE]
> 配置の制約の使用方法、ノードのプロパティ、プロパティの定義方法については、Service Fabric クラスター リソース マネージャー ドキュメント ドキュメントの [クラスターの説明](service-fabric-cluster-resource-manager-cluster-description.md)に関するページの「配置の制約とノードのプロパティ」を参照してください。
> 
> 

### <a name="capacity-metrics"></a>容量メトリック
ノードの種類ごとに、アプリケーションで負荷をレポートするために使用するカスタム容量メトリックを追加できます。 負荷をレポートする容量メトリックの使用方法については、Service Fabric クラスター リソース マネージャー ドキュメントの [クラスターの説明](service-fabric-cluster-resource-manager-cluster-description.md) および [メトリックと負荷](service-fabric-cluster-resource-manager-metrics.md) に関するページをご覧ください。

### <a name="fabric-upgrade-settings---health-polices"></a>ファブリック アップグレード設定 - 正常性ポリシー
ファブリックのアップグレードには、カスタム正常性ポリシーを指定できます。 指定したポリシーは、クラスターのファブリック アップグレードが Automatic に設定されている場合、自動ファブリック アップグレードのフェーズ 1 に適用されます。
クラスターのファブリック アップグレードを Manual に設定した場合は、新しいバージョンを選択するたびにこれらのポリシーが適用され、クラスターのファブリック アップグレードが開始されます。 ポリシーを上書きしていない場合、既定の設定が使用されます。

カスタム正常性ポリシーを指定したり、現在の設定を確認したりするには、[ファブリック アップグレード] ブレードでアップグレードの詳細設定を選択します。 具体的な方法については、次の図を参照してください。 

![Manage custom health policies][HealthPolices]

### <a name="customize-fabric-settings-for-your-cluster"></a>クラスターのファブリック設定のカスタマイズ
カスタマイズできる設定とその方法については、 [Service Fabric クラスターのファブリック設定](service-fabric-cluster-fabric-settings.md) に関するページをご覧ください。

### <a name="os-patches-on-the-vms-that-make-up-the-cluster"></a>クラスターを構成する VM の OS 修正プログラム
この機能は、今後自動化される予定です。 ただし、現時点では、お客様が VM に修正プログラムを適用する必要があります。 同時に複数の VM を停止しないように、この操作は、VM 1 つずつで実行する必要があります。

### <a name="os-upgrades-on-the-vms-that-make-up-the-cluster"></a>クラスターを構成する VM の OS アップグレード
クラスターの仮想マシンで OS イメージをアップグレードする必要がある場合は、一度に 1 つの VM で行う必要があります。 お客様がこのアップグレードを実行する必要があります。現時点では自動化はされていません。

## <a name="next-steps"></a>次のステップ
*  [Service Fabric クラスターのファブリック設定](service-fabric-cluster-fabric-settings.md)
*  [クラスターのスケールアップとスケールダウン](service-fabric-cluster-scale-up-down.md)
*  [アプリケーションのアップグレード](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG

