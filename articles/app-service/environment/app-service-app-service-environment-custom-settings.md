---
title: カスタム設定の構成
description: Azure App Service Environment 全体に適用する設定を構成します。 その作業を Azure Resource Manager テンプレートで行う方法について説明します。
author: ccompy
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.topic: tutorial
ms.date: 01/29/2021
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 5c1e81d02aa35a40a296f04e456be09eeed10331
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99226391"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>App Service Environment のカスタム構成設定
## <a name="overview"></a>概要
App Service Environment (ASE) は単一の顧客に分離されるため、App Service Environment にのみ適用できる特定の構成設定があります。 この記事では、App Service Environment で使用可能な、固有の各種カスタマイズについて説明します。

App Service Environment がない場合は、 [App Service Environment の作成方法](app-service-web-how-to-create-an-app-service-environment.md)に関するページを参照してください。

App Service Environment のカスタマイズは、新しい **clusterSettings** 属性の配列を使って保存できます。 この属性は、Azure Resource Manager の *hostingEnvironments* エンティティの "Properties" ディクショナリにあります。

次の簡略化された Resource Manager テンプレートのスニペットに、その **clusterSettings** 属性が示されています。

```json
"resources": [
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/hostingEnvironments",
    "name": ...,
    "location": ...,
    "properties": {
        "clusterSettings": [
            {
                "name": "nameOfCustomSetting",
                "value": "valueOfCustomSetting"
            }
        ],
        "workerPools": [ ...],
        etc...
    }
}
```

Resource Manager テンプレートに **clusterSettings** 属性を含めて、App Service Environment を更新することができます。

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Azure リソース エクスプローラーを使った App Service Environment の更新
App Service Environment は、 [Azure リソース エクスプローラー](https://resources.azure.com)を使って更新することもできます。  

1. Resource Explorer で、App Service Environment のノードに移動します (**subscriptions** > **resourceGroups** > **providers** > **Microsoft.Web** > **hostingEnvironments** の順に移動)。 次に、更新する App Service Environment をクリックします。
2. 右側のペインで、上部のツールバーの **[読み取り/書き込み]** をクリックし、リソース エクスプローラーでの対話型の編集を許可します。  
3. 青色の **[編集]** ボタンをクリックし、Resource Manager テンプレートを編集可能にします。
4. 右側のペインの一番下までスクロールします。 **clusterSettings** 属性は一番下にあります。ここで、値の入力または更新ができます。
5. **clusterSettings** 属性に、必要な構成値の配列を入力します (またはコピーして貼り付けます)。  
6. 右側のペインの上部にある緑色の **[PUT]** ボタンをクリックし、App Service Environment に変更をコミットします。

ただし、変更を送信してから反映されるまでに、App Service Environment 内のフロント エンドの数に 30 分をかけた程度の時間がかかります。
たとえば、App Service Environment に 4 つのフロント エンドがある場合、構成の更新が完了するまでに約 2 時間かかります。 構成の変更がロールアウトされている間は、App Service Environment で他のスケーリング操作や構成の変更操作は実行できません。

## <a name="enable-internal-encryption"></a>内部暗号化を有効にする

App Service Environment は、内部コンポーネントまたはシステム内の通信を表示できないブラック ボックス システムとして動作します。 より高いスループットを実現するために、内部コンポーネント間の暗号化は既定では有効になっていません。 監視またはアクセスの対象としてトラフィックにアクセスすることはできないため、システムの安全性は確保されています。 それにもかかわらず、データ パスを端から端まで完全に暗号化する必要があるコンプライアンス要件が存在する場合は、clusterSetting を使用して完全なデータ パスの暗号化を有効にする方法があります。  

```json
"clusterSettings": [
    {
        "name": "InternalEncryption",
        "value": "true"
    }
],
```
InternalEncryption を true に設定すると、ASE のフロントエンドとワーカーとの間の内部ネットワーク トラフィックが暗号化され、ページ ファイルが暗号化されるほか、ワーカーのディスクも暗号化されます。 InternalEncryption clusterSetting を有効にすると、システムのパフォーマンスに影響する可能性があります。 InternalEncryption を有効にするように変更を加えると、変更が完全に反映されるまで ASE が不安定な状態になります。 ASE 上に存在するインスタンスの数によっては、変更の反映が完了するまで数時間かかる可能性があります。 ASE の使用中は、ASE で InternalEncryption を有効にしないことを強くお勧めします。 アクティブに使用されている ASE で InternalEncryption を有効にする必要がある場合は、操作が完了するまで、トラフィックをバックアップ環境に転送することを強くお勧めします。 


## <a name="disable-tls-10-and-tls-11"></a>TLS 1.0 と TLS 1.1 の無効化

TLS の設定をアプリごとに管理したい場合は、[TLS 設定の適用](../configure-ssl-bindings.md#enforce-tls-versions)に関するドキュメントに記載されたガイダンスが参考になります。 

ASE のすべてのアプリについて、TLS 1.0 と TLS 1.1 のインバウンド トラフィックをすべて無効にしたい場合は、次の **clusterSettings** エントリを設定してください。

```json
"clusterSettings": [
    {
        "name": "DisableTls1.0",
        "value": "1"
    }
],
```

設定の名前は 1.0 になっていますが、これを構成すると、TLS 1.0 と TLS 1.1 の両方が無効化されます。

## <a name="change-tls-cipher-suite-order"></a>TLS 暗号スイートの順序変更
ASE では、既定の暗号スイートの変更がサポートされています。 既定の暗号セットは、マルチテナント サービスで使用されるものと同じセットです。 暗号スイートの変更は App Service デプロイ全体に影響します。これは、シングルテナント ASE でのみ可能です。 ASE には、TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 と TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 の 2 つの暗号スイートが必要です。 最も強力で最小限の暗号スイートのセットを使用して ASE を操作する場合は、必要な 2 つの暗号のみを使用します。 必要な暗号のみを使用するように ASE を構成するには、次に示すように **clusterSettings** を変更します。 

```json
"clusterSettings": [
    {
        "name": "FrontEndSSLCipherSuiteOrder",
        "value": "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256"
    }
],
```

> [!WARNING]
> SChannel が認識できない間違った値を暗号スイートに設定すると、ご利用のサーバーに対するすべての TLS 通信が機能しなくなります。 この場合は、 *clusterSettings* から **FrontEndSSLCipherSuiteOrder** エントリを削除し、更新された Resource Manager テンプレートを送信して、既定の暗号スイート設定に戻す必要があります。  この機能は慎重に使用してください。

## <a name="get-started"></a>作業開始
Azure クイック スタート Resource Manager テンプレートのサイトには、 [App Service Environment を作成](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/)するための基本定義を含むテンプレートが用意されています。

<!-- LINKS -->

<!-- IMAGES -->
