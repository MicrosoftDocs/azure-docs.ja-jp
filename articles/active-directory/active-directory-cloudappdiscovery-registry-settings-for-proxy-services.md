---
title: "Cloud App Discovery のプロキシ サービス用レジストリ設定 | Microsoft Docs"
description: "このトピックでは、Cloud App Discovery エージェントを実行しているコンピューターで、必要なポートを設定するために実行する必要がある手順を説明します。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 8d78e925-e331-40ba-904a-e4ef14260cac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 6adb1dd25c24b18b834dd921c2586ef29d56dc81
ms.openlocfilehash: 7f2a3c4bf8fda9dd235986be8b0ceaa8eb8313e0
ms.lasthandoff: 12/29/2016


---
# <a name="cloud-app-discovery-registry-settings-for-proxy-services"></a>Cloud App Discovery のプロキシ サービス用レジストリ設定
既定では、Cloud App Discovery エージェントはポート 80 または 443 のみを使用するように構成されています。 Cloud App Discovery を、カスタム ポート (80 でも 443 でもない) を使用しているプロキシ サーバーを含む環境にインストールしようと計画している場合は、エージェントもこのポートを使用するように構成する必要があります。 構成は、レジストリ キーに基づきます。

このトピックでは、Cloud App Discovery エージェントを実行しているコンピューターで、必要なポートを設定するために実行する必要がある手順を説明します。

**Cloud App Discovery エージェントを実行しているコンピューターで使用されるポートを変更するには、次の手順を実行します。**

1. レジストリ エディターを開きます。 <br> ![実行](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)
2. 次のレジストリ キーに移動するか作成します。 <br> **HKLM_LOCAL_MACHINE\Software\Microsoft\Cloud App Discovery\Endpoint** 
3. **Ports** という名前の新しい**複数行文字列**値を作成します。 ![新規](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)
4. **[複数行文字列の編集]** ダイアログを開いて、[Ports] の値をダブルクリックします。
5. [値データ] テキストボックスで、次の値を入力し、組織で使用されるすべてのカスタム ポートを追加します。 <br><br>
   **80** <br>
   **8080** <br>
   **8118** <br>
   **8888** <br>
   **81** <br>
   **12080** <br>
   **6999** <br>
   **30606** <br>
   **31595** <br>
   **4080** <br>
   **443** <br>
   **1110** <br><br>
   ![複数行文字列の編集](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy03.png)
6. **[OK]** をクリックして、**[複数行文字列の編集]** ダイアログを閉じます。

**その他のリソース**

* [自分の組織内で使用される承認されていないクラウド アプリを検出する方法](active-directory-cloudappdiscovery-whatis.md) 


