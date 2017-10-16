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
ms.date: 09/25/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: b8584809b76d6be12a6f489f0bb819081d1803d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="cloud-app-discovery-registry-settings-for-proxy-services"></a>Cloud App Discovery のプロキシ サービス用レジストリ設定
このトピックの目的は、Cloud App Discovery エージェントを実行しているコンピューター上で、必要なポートを設定するために実行する手順について説明することです。 既定では、Cloud App Discovery エージェントはポート 80 または 443 のみを使用するように構成されています。 Cloud App Discovery を、カスタム ポート (80 でも 443 でもない) を使用しているプロキシ サーバーを含む環境にインストールしようと計画している場合は、エージェントもこのポートを使用するように構成する必要があります。 構成は、レジストリ キーに基づきます。

> [!TIP] 
> [Microsoft Cloud App Security との統合](https://portal.cloudappsecurity.com)によって拡張される、Azure Active Directory (Azure AD) の新しいエージェントレスの Cloud App Discovery の機能強化を確認してください。

## <a name="modify-the-port-used-by-the-computer-running-the-cloud-app-discovery-agent"></a>Cloud App Discovery エージェントを実行しているコンピューターによって使用されるポートの変更

1. レジストリ エディターを開きます。
  ![Run](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)
2. レジストリ キー **HKLM_LOCAL_MACHINE\Software\Microsoft\Cloud App Discovery\Endpoint** に移動するか、またはこのレジストリ キーを作成します。
3. **Ports** という名前の新しい**複数行文字列**値を作成します。 
  ![[新規]](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)
4. **[複数行文字列の編集]** ダイアログを開くには、**[ポート]** 値をダブルクリックします。
5. **[値のデータ]** に次の値を入力し、組織によって使用されるすべてのカスタム ポートを追加します。 <br><br>
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

## <a name="next-steps"></a>次のステップ

* [自分の組織内で使用される承認されていないクラウド アプリを検出する方法](active-directory-cloudappdiscovery-whatis.md) 

