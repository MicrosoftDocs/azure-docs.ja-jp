---
title: App Service Environment のカスタム設定 - Azure
description: App Service Environment のカスタム構成設定
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2018
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 6463759dbd217cd054f838c09c7cfcf99a06aa2c
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2019
ms.locfileid: "54390815"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>App Service Environment のカスタム構成設定
## <a name="overview"></a>概要
App Service Environment (ASE) は単一の顧客に分離されるため、App Service Environment にのみ適用できる特定の構成設定があります。 この記事では、App Service Environment で使用可能な、固有の各種カスタマイズについて説明します。

App Service 環境がない場合は、 [App Service 環境の作成方法](app-service-web-how-to-create-an-app-service-environment.md)に関するページを参照してください。

App Service Environment のカスタマイズは、新しい **clusterSettings** 属性の配列を使って保存できます。 この属性は、Azure Resource Manager の *hostingEnvironments* エンティティの "Properties" ディクショナリにあります。

次の簡略化された Resource Manager テンプレートのスニペットに、その **clusterSettings** 属性が示されています。

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

## <a name="disable-tls-10-and-tls-11"></a>TLS 1.0 と TLS 1.1 の無効化

TLS の設定をアプリごとに管理したい場合は、[TLS 設定の適用](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl#enforce-tls-versions)に関するドキュメントに記載されたガイダンスが参考になります。 

ASE のすべてのアプリについて、TLS 1.0 と TLS 1.1 のインバウンド トラフィックをすべて無効にしたい場合は、次の **clusterSettings** エントリを設定してください。

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

設定の名前は 1.0 になっていますが、これを構成すると、TLS 1.0 と TLS 1.1 の両方が無効化されます。

## <a name="change-tls-cipher-suite-order"></a>TLS 暗号スイートの順序変更
お客様から寄せられるもう 1 つの質問は、サーバーによってネゴシエートされた暗号のリストを変更できるかどうかということです。これは、以下に示したように **clusterSettings** を変更することで実行できます。 利用できる暗号スイートのリストは、[こちらの MSDN の記事](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx) で確認できます。

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [!WARNING]
> SChannel が認識できない間違った値を暗号スイートに設定すると、ご利用のサーバーに対するすべての TLS 通信が機能しなくなります。 この場合は、 *clusterSettings* から **FrontEndSSLCipherSuiteOrder** エントリを削除し、更新された Resource Manager テンプレートを送信して、既定の暗号スイート設定に戻す必要があります。  この機能は慎重に使用してください。
> 
> 

## <a name="get-started"></a>作業開始
Azure クイック スタート Resource Manager テンプレートのサイトには、 [App Service Environment を作成](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/)するための基本定義を含むテンプレートが用意されています。

<!-- LINKS -->

<!-- IMAGES -->
