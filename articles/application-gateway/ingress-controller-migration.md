---
title: Azure Application Gateway イングレス コントローラー Helm から AGIC アドオンに移行する方法
description: この記事では、Helm を介してデプロイされた AGIC から、AKS アドオンとしてデプロイされた AGIC に移行する方法について説明します
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: e83834fd5f8ca95826118c952f7884a494c7abbb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102050836"
---
# <a name="migrate-from-agic-helm-to-agic-add-on"></a>AGIC Helm から AGIC アドオンに移行する 

Helm を介して AGIC が既にデプロイされているが、AKS アドオンとしてデプロイされた AGIC に移行する必要がある場合、次の手順に従って移行プロセスを実行できます。 

## <a name="prerequisites"></a>前提条件 
移行プロセスを開始する前に、いくつかの点を確認する必要があります。 
  - [AGIC アドオンで現在サポートされていない](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) AGIC Helm の機能を使用していますか?
  - 1 つの AKS クラスターにつき複数の AGIC Helm デプロイを使用していますか? 
  - 1 つの Application Gateway をターゲットとして複数の AGIC Helm デプロイを使用していますか? 

上記のいずれかの質問に対して「はい」と答えた場合、ご使用のユース ケースでは AGIC アドオンがまだサポートされていないため、AGIC Helm を当面使い続けることをお勧めします。 そうでない場合、下の移行プロセスを営業時間外に続行してください。 

## <a name="find-the-application-gateway-resource-id-that-agic-helm-is-currently-targeting"></a>AGIC Helm が現在ターゲットに設定している Application Gateway リソース ID を見つける 
AGIC Helm デプロイがターゲットに設定している Application Gateway に移動します。 その Application Gateway のリソース ID をコピーして保存します。 このリソース ID は、後の手順で必要になります。 リソース ID は、ポータルの Application Gateway の [プロパティ] タブで、または Azure CLI を使用して見つけることができます。 次の例では、リソース グループ *myResourceGroup* の *myApplicationGateway* という名前のゲートウェイについて、Application Gateway リソース ID を *appgwId* に保存します。

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
```

## <a name="delete-agic-helm-from-your-aks-cluster"></a>AKS クラスターから AGIC Helm を削除する
Azure CLI を使用して、クラスターから AGIC Helm のデプロイを削除します。 AGIC AKS アドオンを有効にするには、最初に AGIC Helm のデプロイを削除する必要があります。 AGIC Helm のデプロイを削除してから AGIC アドオンを有効にするまでの間に AKS クラスター内で発生したすべての変更は、Application Gateway に反映されないことに注意してください。そのため、影響を最小限に抑えるために、この移行プロセスは営業時間外に行う必要があります。 Application Gateway では、AGIC によって適用された最後の構成が引き続き保持されるため、既存のルーティング規則は影響を受けません。 

## <a name="enable-agic-add-on-using-your-existing-application-gateway"></a>既存の Application Gateway を使用して AGIC アドオンを有効にする 
Azure CLI または Portal を使用して、AKS クラスターで AGIC アドオンを有効にして既存の Application Gateway をターゲットに設定できるようになりました。 次の Azure CLI コマンドを実行して、AKS クラスターで AGIC アドオンを有効にします。 この例では、前の手順で保存した Application Gateway リソース ID *appgwId* を使用して、*myResourceGroup* という名前のリソース グループにある *myCluster* という名前のクラスターでアドオンを有効にします。 


```azurecli-interactive
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

または、こちらの[リンク](https://portal.azure.com/?feature.aksagic=true)を使用して Portal で AKS クラスターに移動し、クラスターの [ネットワーク] タブで AGIC アドオンを有効にすることもできます。 アドオンでターゲットに設定する Application Gateway を選択するときは、ドロップダウン メニューから既存の Application Gateway を選択します。 

![Application Gateway イングレス コントローラー - ポータル](./media/tutorial-ingress-controller-add-on-existing/portal-ingress-controller-add-on.png)

## <a name="next-steps"></a>次の手順
- [**Application Gateway イングレス コントローラーのトラブルシューティング**](ingress-controller-troubleshoot.md): AGIC のトラブルシューティング ガイド 
- [**Application Gateway イングレス コントローラーの注釈**](ingress-controller-annotations.md): AGIC の注釈の一覧 