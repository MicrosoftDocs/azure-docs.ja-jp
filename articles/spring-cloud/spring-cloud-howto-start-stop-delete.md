---
title: Azure Spring Cloud アプリケーションを開始、停止、および削除する方法 | Microsoft Docs
description: Azure Spring Cloud アプリケーションを開始、停止、および削除する方法
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: f7f76644d13c20704d2c3bd908176ac452df2a20
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038441"
---
# <a name="how-to-start-stop-and-delete-your-azure-spring-cloud-application"></a>Azure Spring Cloud アプリケーションを開始、停止、および削除する方法

このガイドでは、Azure portal または CLI を使用して Azure Spring Cloud のアプリケーションの状態を変更する方法について説明します。

## <a name="using-the-azure-portal"></a>Azure ポータルの使用

アプリケーションをデプロイしたら、Azure portal を使用して、アプリケーションを**開始**、**停止**、および**削除**することができます。

1. Azure portal で、Azure Spring Cloud サービス インスタンスに移動します。
1. **[アプリケーション ダッシュボード]** タブを選択します。
1. 状態を変更するアプリケーションを選択します。
2. そのアプリケーションの **[概要]** ページで、アプリケーションを**開始および停止**、**再開**、および**削除**するためのボタンを見つけます。

## <a name="using-the-azure-cli"></a>Azure CLI の使用

> [!NOTE]
> オプションのパラメーターを使用して、Azure CLI で既定値を構成できます。 詳細については、[リファレンス ドキュメント](spring-cloud-cli-reference.md)を参照してください。

* アプリケーションを開始するには:
    ```Azure CLI
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* アプリケーションを停止するには:
    ```Azure CLI
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* アプリケーションを再開するには:
    ```Azure CLI
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* アプリケーションを削除するには:
    ```Azure CLI
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
