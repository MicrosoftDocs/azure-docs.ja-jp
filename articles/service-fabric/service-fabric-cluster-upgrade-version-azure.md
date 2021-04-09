---
title: クラスターの Azure Service Fabric のバージョンをアップグレードする
description: Service Fabric クラスターを実行している Service Fabric コード、構成、またはその両方をアップグレードします。たとえば、クラスター アップグレード モードの設定、証明書のアップグレード、アプリケーション ポートの追加、OS 修正プログラムの適用などを行います。 アップグレードを実行しているときに、どのようなことが起きるでしょうか?
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 01fe916f0ee78c8481ac6b17b8f7409b47c852ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "90564289"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>クラスターの Service Fabric バージョンをアップグレードする

最新のシステムでは、アップグレード性を考慮した設計を行うことが、製品の長期的な成功を達成する鍵となります。 Azure Service Fabric クラスターはお客様が所有するリソースですが、一部は Microsoft によって管理されます。 この記事では、Azure クラスター内で実行されている Service Fabric のバージョンをアップグレードする方法について説明します。

Microsoft からのリリース時に自動ファブリック アップグレードを受信するようにクラスターを設定するか、有効にするクラスターのサポートされるファブリック バージョンを選択することもできます。

そのためには、クラスターの作成時に (または後から稼働中のクラスターに対して) Resource Manager を使用するか、またはポータルで "upgradeMode" クラスター構成を設定します。 

> [!NOTE]
> クラスターには必ず、サポートされているバージョンのファブリックを使用してください。 Microsoft が Service Fabric の新バージョン リリースをアナウンスした日から最短で 60 日後には、以前のバージョンがサポート期間の終了として指定されます。 新バージョンのリリースは、 [Service Fabric チーム ブログ](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)でお知らせします。 その後間もなく新しいバージョンが利用できるようになります。 
> 
> 

現在クラスターで実行されているバージョンの有効期間が終了する 14 日前に、正常性に関するイベントが生成され、クラスターの正常性が警告状態に移行します。 サポートされているバージョンのファブリックにアップグレードするまで、クラスターは警告状態のままとなります。

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>Azure portal でアップグレード モードを設定する
ファブリックのアップグレード モードは、クラスターの作成時に自動または手動に設定することができます。

![スクリーンショットは、オプション 2 の [クラスター構成] が選択された [Service Fabric クラスターの作成] ペインと、[クラスター構成] ペインが開いた状態を示しています。][Create_Manualmode]

稼働中のクラスターに対しては、管理操作からファブリックのアップグレード モードを自動または手動に設定することができます。 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>ポータルで手動モードに設定されたクラスターを新バージョンにアップグレードする
新しいバージョンにアップグレードするために必要な操作は、ドロップダウンに表示されたバージョンの中からいずれかを選択し、保存するだけです。 ファブリックのアップグレードが自動的に開始されます。 クラスター正常性ポリシー (ノードの正常性と、クラスターで実行されているすべてのアプリケーションの正常性の組み合わせ) は、アップグレードの実行中、遵守されます。

クラスター正常性ポリシーが満たされていない場合は、アップグレードがロールバックされます。 正常性ポリシーのカスタマイズ方法については、このドキュメントの後半で詳しく取り上げています。下へスクロールして該当セクションを参照してください。 

ロールバックの原因となった問題を解決した後、前述の手順に従ってもう一度アップグレードを実行してください。

![スクリーンショットは、[ファブリックのアップグレード] ペインが開き、自動、手動などのアップグレード オプションが強調表示された [Service Fabric クラスター] ウィンドウを示しています。][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>Resource Manager テンプレートを使用してアップグレード モードを設定する
Microsoft.ServiceFabric/クラスター リソース定義に "upgradeMode" 構成を追加し、サポートされているいずれかのファブリック バージョンを "clusterCodeVersion" に指定して (下記)、テンプレートをデプロイします。 "upgradeMode" には "Manual" または "Automatic" のいずれかを値として指定できます。

![スクリーンショットはテンプレートを示しています。これは、構造を反映するようにインデントされたプレーンテキストで、clusterCodeVersion と upgradeMode が強調表示されています。][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Resource Manager テンプレートで手動モードに設定されたクラスターを新バージョンにアップグレードする
手動モードのクラスターを新バージョンにアップグレードするには、"clusterCodeVersion" に指定されているバージョンを、サポートされているいずれかのバージョンに変更してデプロイします。 テンプレートをデプロイすると、ファブリックのアップグレードが自動的に開始されます。 クラスター正常性ポリシー (ノードの正常性と、クラスターで実行されているすべてのアプリケーションの正常性の組み合わせ) は、アップグレードの実行中、遵守されます。

クラスター正常性ポリシーが満たされていない場合は、アップグレードがロールバックされます。  

ロールバックの原因となった問題を解決した後、前述の手順に従ってもう一度アップグレードを実行してください。

## <a name="set-custom-health-polices-for-upgrades"></a>アップグレードに対するカスタム正常性ポリシーを設定する
ファブリックのアップグレードには、カスタム正常性ポリシーを指定できます。 指定したポリシーは、クラスターのファブリック アップグレードが Automatic に設定されている場合、[自動ファブリック アップグレードのフェーズ 1](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades) に適用されます。
クラスターのファブリック アップグレードを Manual に設定した場合は、新しいバージョンを選択するたびにこれらのポリシーが適用され、クラスターのファブリック アップグレードが開始されます。 ポリシーをオーバーライドしていない場合、既定の設定が使用されます。

カスタム正常性ポリシーを指定したり、現在の設定を確認したりするには、[ファブリック アップグレード] ブレードでアップグレードの詳細設定を選択します。 具体的な方法については、次の図を参照してください。 

![Manage custom health policies][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>特定のサブスクリプションのすべての環境を対象に利用できる全バージョンの一覧を表示する
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

## <a name="next-steps"></a>次のステップ
* [Service Fabric クラスターのファブリック設定](service-fabric-cluster-fabric-settings.md)
* [クラスターのスケールアップとスケールダウン](service-fabric-cluster-scale-in-out.md)
* [アプリケーションのアップグレード](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
