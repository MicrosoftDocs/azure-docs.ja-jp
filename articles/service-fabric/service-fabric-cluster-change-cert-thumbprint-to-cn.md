---
title: 証明書の共通名を使用するようにクラスターを更新する
description: Azure Service Fabric クラスター証明書を拇印ベースの宣言から共通名に変換する方法について説明します。
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: f719b1eb39da776827c6babec61e9e6701bb4602
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97900792"
---
# <a name="convert-cluster-certificates-from-thumbprint-based-declarations-to-common-names"></a>クラスター証明書を拇印ベースの宣言から共通名に変換する

証明書の署名 (一般に拇印と呼ばれます) は一意です。 拇印によって宣言されたクラスター証明書は、証明書の特定のインスタンスを参照しています。 この特異性により、証明書のロールオーバーと、全般的な管理は、困難で明示的になります。 変更するたびに、クラスターと基盤となるコンピューティング ホストのアップグレードを調整する必要があります。

Azure Service Fabric クラスターの証明書の宣言を拇印ベースから証明書のサブジェクト共通名 (CN) に基づく宣言に変換すると、管理が大幅に簡素化されます。 特に、証明書のロールオーバーにクラスターのアップグレードは不要になります。 この記事では、ダウンタイムなしで既存のクラスターを CN ベースの宣言に変換する方法について説明します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-to-certificate-authority-signed-certificates"></a>証明機関によって署名された証明書に移行する

拇印によって証明書が宣言されているクラスターのセキュリティは、別のものと同じ署名を持つ証明書を偽造することは不可能であるか、計算上、実行不可能であるという事実に基づいています。 この場合、証明書の発行元はそれほど重要ではないため、自己署名証明書で十分です。

対照的に、証明書が CN によって宣言されているクラスターのセキュリティは、クラスター所有者がその証明書プロバイダー内に持っている暗黙的な信頼に由来します。 このプロバイダーは、証明書を発行した公開キー基盤 (PKI) サービスです。 信頼は、他の要因の中でも特に、PKI の認証プラクティス、その運用上のセキュリティが他の信頼できるパーティによって監査および承認されているかどうかなどに基づいています。

クラスター所有者は、証明書の発行者である証明機関 (CA) についての詳細な知識も持っている必要があります。これは、サブジェクトによって証明書を検証するための基本的な側面だからです。 これは、自己署名証明書がこの目的にはまったく不適切であることも意味します。 文字どおり、誰でも特定のサブジェクトを使用して証明書を生成できます。

CN によって宣言された証明書は、通常、次の場合に有効と見なされます。

* そのチェーンを正常に構築できる。
* サブジェクトに想定された CN 要素がある。
* 証明書の発行者 (チェーンの直近または上位) は、検証を実行するエージェントによって信頼されている。

Service Fabric は、次の 2 つの方法で CN による証明書の宣言をサポートしています。

* "*暗黙的な*" 発行者 (チェーンはトラスト アンカーで終了する必要があることを意味します)。
* 拇印によって宣言された発行者 (発行者のピン留めと呼ばれます)。

詳細については、「[共通名ベースの証明書検証の宣言](cluster-security-certificates.md#common-name-based-certificate-validation-declarations)」を参照してください。

拇印によって宣言された自己署名証明書を使用してクラスターを CN に変換するには、まずターゲットの CA 署名付き証明書を拇印によってクラスターに導入する必要があります。 そうして初めて、拇印から CN への変換が可能になります。

テスト目的であれば、自己署名証明書を CN によって宣言することが "*できます*" が、発行者が独自の拇印にピン留めされている場合に限ります。 セキュリティの観点から見ると、このアクションは、拇印によって同じ証明書を宣言することとほぼ同じです。 この種の変換が成功したからといって、拇印から CA 署名証明書を使用した CN に正常に変換されることは保証されません。 適切な CA 署名証明書を使用して変換をテストすることをお勧めします。 このテストには無料のオプションがあります。

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>証明書をアップロードしてそれをスケールセットにインストールする

Azure では、証明書を取得してプロビジョニングするために推奨されるメカニズムとして、Azure Key Vault とそのツールがあります。 クラスター証明書の宣言に一致する証明書は、クラスターを構成する仮想マシン スケール セットのすべてのノードにプロビジョニングする必要があります。 詳細については、[仮想マシン スケール セットのシークレット](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-securely-ship-a-certificate-to-the-vm)に関する記事を参照してください。

クラスターの証明書の宣言を変更する前に、クラスターのすべてのノードの種類の仮想マシンに現在のクラスター証明書とターゲット クラスター証明書の両方をインストールすることが重要です。 証明書の発行から Service Fabric ノードへのプロビジョニングまでのプロセスについては、「[証明書のプロセス](cluster-security-certificate-management.md#the-journey-of-a-certificate)」を参照してください。

## <a name="bring-the-cluster-to-an-optimal-starting-state"></a>クラスターを最適な開始状態にする

証明書の宣言を拇印ベースから CN ベースに変換すると、次の影響があります。

- クラスター内の各ノードによってその資格情報が検出され、他のノードに提示される方法。
- セキュリティで保護された接続が確立される際に、各ノードによって対応するノードの資格情報が検証される方法。

続行する前に、[両方の構成についてのプレゼンテーションと検証の規則](cluster-security-certificates.md#certificate-configuration-rules)を確認してください。 拇印から CN への変換を実行する際の最も重要な考慮事項は、アップグレードされたノードとまだアップグレードされていないノード (つまり、異なるアップグレード ドメインに属するノード) は、アップグレード中にいつでも相互認証を正常に実行できる必要があるということです。 この動作を実現するための推奨される方法は、初期のアップグレードで拇印によってターゲットまたは目標の証明書を宣言することです。 その後、次の CN への移行を完了します。 クラスターが既に推奨される開始状態にある場合は、このセクションをスキップできます。

変換には有効な開始状態が複数あります。 不変とは、CN へのアップグレードの開始時に、クラスターが既にターゲット証明書 (拇印による宣言) を使用していることです。 この記事では、`GoalCert`、`OldCert1`、および `OldCert2` について説明します。

#### <a name="valid-starting-states"></a>有効な開始状態

- `Thumbprint: GoalCert, ThumbprintSecondary: None`
- `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` (`GoalCert` の `NotBefore` の日付は `OldCert1` よりも後です)
- `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` (`GoalCert` の `NotBefore` の日付は `OldCert1` よりも後です)

> [!NOTE]
> バージョン 7.2.445 (7.2 CU4) より前は、Service Fabric では、有効期限が最も遠い証明書 ("NotAfter" プロパティが最も遠い証明書) が選択されるので、7.2 CU4 より前の上記の開始状態では、GoalCert の `NotAfter` の日付が `OldCert1` よりも後である必要があります。

クラスターが前述の有効な状態のいずれでもない場合は、この記事の最後にあるセクションで、その状態を達成する方法に関する情報を参照してください。

## <a name="select-the-desired-cn-based-certificate-validation-scheme"></a>目的の CN ベースの証明書検証スキームを選択する

前述のように、Service Fabric は、暗黙的なトラスト アンカーまたは発行者の拇印の明示的ピン留めを使用した CN による証明書の宣言をサポートしています。 詳細については、「[共通名ベースの証明書検証の宣言](cluster-security-certificates.md#common-name-based-certificate-validation-declarations)」を参照してください。

いずれのメカニズムを選択するかについて、違いと影響を十分に理解していることを確認してください。 この違いまたは選択は、構文的には `certificateIssuerThumbprintList` パラメーターの値によって決まります。 空とは、信頼されたルート CA (トラスト アンカー) に依存することを意味しますが、拇印のセットによって、クラスター証明書の許可される直接の発行者は制限されます。

   > [!NOTE]
   > `certificateIssuerThumbprint` フィールドを使用すると、サブジェクト CN によって宣言された証明書の想定される直接の発行者を指定できます。 使用できる値は、1 つ以上のコンマ区切りの SHA1 拇印です。 このアクションにより、証明書の検証が強化されます。
   >
   > 発行者が指定されていないか、リストが空の場合、そのチェーンを構築できれば、証明書は認証のために受け入れられます。 これで、この証明書は最終的に検証機関によって信頼されたルートになります。 1 つ以上の発行者の拇印が指定されている場合、チェーンから抽出された直接の発行者の拇印がこのフィールドで指定された値のいずれかに一致していれば、証明書は受け入れられます。 ルートが信頼されているかどうかに関係なく、証明書は受け入れられます。
   >
   > 特定のサブジェクトで証明書に署名するために、PKI にはさまざまな証明機関 ("*発行者*" とも呼ばれます) が使用される場合があります。 このため、そのサブジェクトについて想定される発行者の拇印をすべて指定することが重要です。 つまり、証明書の更新は、更新される証明書と同じ発行者によって署名されることが保証されていません。
   >
   > 発行者を指定することがベスト プラクティスと考えられます。 発行者を省略すると、信頼されたルートにチェーンされる証明書に対しては引き続き機能しますが、この動作には制限があり、近い将来、段階的に廃止される可能性があります。 Azure にデプロイされ、プライベート PKI によって発行された X509 証明書を使用して保護され、サブジェクトによって宣言されたクラスターは、(クラスターとサービス間の通信の場合) Service Fabric によって検証できない場合があります。 検証を行うには、PKI の証明書ポリシーが検出可能であり、使用可能であり、アクセス可能である必要があります。

## <a name="update-the-clusters-azure-resource-manager-template-and-deploy"></a>クラスターの Azure Resource Manager テンプレートを更新してデプロイする

Azure Resource Manager (ARM) テンプレートを使用して Service Fabric クラスターを管理します。 JSON アーティファクトも使用する別の方法として、[Azure Resource Explorer (プレビュー)](https://resources.azure.com) があります。 現時点では、同等のエクスペリエンスを Azure portal で使用することはできません。

既存のクラスターに対応する元のテンプレートが使用できない場合は、Azure portal で同等のテンプレートを取得できます。 クラスターを含むリソース グループに移動し、左側の **[オートメーション]** メニューから **[テンプレートのエクスポート]** を選択します。 次に、目的のリソースを選択します。 少なくとも、仮想マシン スケール セットとクラスター リソースをそれぞれエクスポートする必要があります。 生成されたテンプレートをダウンロードすることもできます。 このテンプレートは、完全にデプロイできるようになる前に変更が必要になる場合があります。 また、テンプレートは、元のものと正確に一致しない場合もあります。 これは、クラスター リソースの現在の状態を反映したものです。

必要な変更は次のとおりです。

- (仮想マシン リソースの下にある) Service Fabric ノード拡張の定義を更新します。 クラスターによって複数のノードの種類が定義されている場合、対応する各仮想マシン スケール セットの定義を更新する必要があります。
- クラスター リソース定義を更新します。

詳細な例については、ここに記載されています。

### <a name="update-the-virtual-machine-scale-set-resources"></a>仮想マシン スケール セットのリソースを更新する
差出人:
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "thumbprint": "[parameters('certificateThumbprint')]",
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```
移動先:
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```

### <a name="update-the-cluster-resource"></a>クラスター リソースを更新する

**Microsoft.ServiceFabric/clusters** リソースで、**commonNames** 設定を使用して **certificateCommonNames** プロパティを追加し、**certificate** プロパティ (とその設定) を完全に削除します。

差出人:
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```
移動先:
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprintList')]"
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```

詳細については、「[拇印ではなく証明書共通名を使用する Service Fabric クラスターをデプロイする](./service-fabric-create-cluster-using-cert-cn.md)」を参照してください。

## <a name="deploy-the-updated-template"></a>更新したテンプレートをデプロイする

変更を加えた後、更新したテンプレートを再度デプロイします。

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="achieve-a-valid-starting-state-for-converting-a-cluster-to-cn-based-certificate-declarations"></a>クラスターを CN ベースの証明書宣言に変換するための有効な開始状態を実現する

| 開始時の状態 | アップグレード 1 | アップグレード 2 |
| :--- | :--- | :--- |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` と `GoalCert` の `NotBefore` の日付は `OldCert1` よりも後です | `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` と `OldCert1` の `NotBefore` の日付は `GoalCert` よりも後です | `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` | `Thumbprint: GoalCert, ThumbprintSecondary: None` |
| `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` (`OldCert1` の `NotBefore` の日付は `GoalCert` よりも後です) | `Thumbprint: GoalCert, ThumbprintSecondary: None` へのアップグレード | - |
| `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` (`OldCert1` の `NotBefore` の日付は `GoalCert` よりも後です) | `Thumbprint: GoalCert, ThumbprintSecondary: None` へのアップグレード | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: OldCert2` | 状態 `Thumbprint: OldCertx, ThumbprintSecondary: None` にするには、`OldCert1` または `OldCert2` のいずれかを削除します | 新しい開始状態から続行します |

> [!NOTE]
> バージョン 7.2.445 (7.2 CU4) より前のバージョンのクラスターの場合、上記の状態で `NotBefore` を `NotAfter` に置き換えます。

これらのアップグレードを実行する方法については、[Azure Service Fabric クラスターでの証明書の管理](service-fabric-cluster-security-update-certs-azure.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* [クラスター セキュリティ](service-fabric-cluster-security.md)について学習します。
* [共通名でクラスター証明書をロールオーバーする](service-fabric-cluster-rollover-cert-cn.md)方法について学習します。
* [タッチレス自動ロールオーバー用にクラスターを構成する](cluster-security-certificate-management.md)方法について学習します。

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
