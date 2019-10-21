---
title: Azure Spring Cloud で永続的ストレージを使用する方法 | Microsoft Docs
description: Azure Spring Cloud で永続的ストレージを使用する方法
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: 8c57698471d1363438c10e5806f9ed6f1da5333f
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038451"
---
# <a name="how-to-use-persistent-storage-in-azure-spring-cloud"></a>Azure Spring Cloud で永続的ストレージを使用する方法

Azure Spring Cloud には、アプリケーション用に永続的ストレージと一時ストレージの 2 種類のストレージが用意されています。  Azure Spring Cloud では、各アプリケーション インスタンスに対して既定で一時ストレージが有効になります。 一時ストレージは、上限が 5 GB、既定のマウント パスが `/tmp` となっています。

> [!WARNING]
> アプリケーション インスタンスに関連付けられている一時ストレージは、そのインスタンスを再起動すると完全に削除されます。

永続的ストレージは、Azure によって管理されるファイル共有コンテナーの一種であり、アプリケーションのスコープごとに割り当てられます。 永続的ストレージに格納されているデータは、アプリケーションの全インスタンスが共有します。 永続的ディスクを有効にすることができるアプリケーションの数は、Azure Spring Cloud サービス インスタンス 1 つにつき 10 件までです。それぞれのアプリケーションに与えられる永続的ストレージの容量は 50 GB です。 永続的ストレージの既定のマウント パスは、`/persistent` です。

## <a name="enable-persistent-storage-using-the-azure-portal"></a>Azure portal を使用して永続的ストレージを有効にする

1. Azure Spring Cloud サービスのページで、 **[アプリケーション ダッシュボード]** を選択し、永続的ストレージを必要とするアプリケーションを選択します。
1. **[概要]** タブで **[Persistent Storage]\(永続的ストレージ\)** 属性を探して、 **[無効]** を選択します。
1. **[有効化]** をクリックして永続的ストレージを有効にしたら、 **[OK]** ボタンを押して変更を適用します。

永続的ストレージが有効になっていると、 **[概要]** ページの **[Persistent Storage]\(永続的ストレージ\)** 属性にそのサイズとパスが表示されます。

> [!WARNING]
> 永続的ストレージを "*無効にする*" と、そのアプリケーションに対するストレージの割り当てが解除されます。  そのストレージ アカウント内のデータはすべて失われます。 

## <a name="enable-persistent-storage-using-the-azure-cli"></a>Azure CLI を使用して永続的ストレージを有効にする

永続的ディスクが有効になった状態のアプリを作成します。
 
```azurecli
az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
```

既存のアプリで永続的ストレージを有効にします。

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
```

既存のアプリで永続的ストレージを無効にします。

> [!WARNING]
> 永続的ストレージを無効にすると、そのアプリケーションに関するストレージの割り当てが解除されます。そのストレージに保存されていたデータはいずれも、完全に失われます。 

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
```

## <a name="next-steps"></a>次の手順

[アプリケーションとサービスのクォータ](spring-cloud-quotas.md)または[アプリケーションを手動でスケーリングする方法](spring-cloud-tutorial-scale-manual.md)を確認してください。