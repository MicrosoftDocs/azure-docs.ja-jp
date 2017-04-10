---
title: "Visual Studio を使用した Azure Government への接続 | Microsoft Docs"
description: "Visual Studio を使用して Azure Government に接続し、サブスクリプションを管理する方法"
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: faf269aa-e879-4b0e-a5ba-d4110684616a
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: d49b2c6afc48cda01045cddb3844c5234e72de37
ms.lasthandoff: 02/21/2017


---


# <a name="connecting-via-visual-studio"></a>Visual Studio 経由の接続
開発者は、Visual Studio を使用して、ソリューションの構築時に Azure サブスクリプションを簡単に管理することができます。  現在 Visual Studio では、ユーザー インターフェイスで Azure Government への接続を構成することはできません。  

### <a name="updating-visual-studio-for-azure-government"></a>Visual Studio を Azure Government 用に更新する
Visual Studio の Azure Government への接続を有効にするには、レジストリを更新する必要があります。

1. Visual Studio を閉じます。
2. **VisualStudioForAzureGov.reg** という名前のテキスト ファイルを作成します。
3. 次のテキストをコピーして **VisualStudioForAzureGov.reg** に貼り付けます。
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"="https://login-us.microsoftonline.com/"
        "adaluri"="https://management.core.usgovcloudapi.net"
        "AzureRMEndpoint"="https://management.usgovcloudapi.net"
        "AzureRMAudienceEndpoint"="https://management.core.usgovcloudapi.net"
        "EnableAzureRMIdentity"="true"
        "GraphUrl"="graph.windows.net"
4. 保存してからファイルをダブルクリックして実行します。  ファイルをレジストリにマージするよう求めるメッセージが表示されます。
5. Visual Studio を起動して、[Cloud Explorer](../vs-azure-tools-resources-managing-with-cloud-explorer.md) の使用を開始します。

> [!NOTE]
> このレジストリ キーが設定されて初めて、Azure Government サブスクリプションにアクセスできるようになります。  Visual Studio は Azure パブリックではなく Azure Government に接続しているため、以前構成したサブスクリプションは表示されますが動作しません。  変更を元に戻す手順については、次のセクションを参照してください。
> 
> 

### <a name="reverting-visual-studio-connection-to-azure-government"></a>Visual Studio の Azure Government への接続を元に戻す
Visual Studio の Azure パブリックへの接続を有効にするには、Azure Government への接続を有効にしたレジストリ設定を削除する必要があります。

1. Visual Studio を閉じます。
2. **VisualStudioForAzureGov_Remove.reg** という名前のテキスト ファイルを作成します。
3. 次のテキストをコピーして **VisualStudioForAzureGov_Remove.reg** に貼り付けます。
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"=-
        "adaluri"=-
        "AzureRMEndpoint"=-
        "AzureRMAudienceEndpoint"=-
        "EnableAzureRMIdentity"=-
        "GraphUrl"=-
4. 保存してからファイルをダブルクリックして実行します。  ファイルをレジストリにマージするよう求めるメッセージが表示されます。
5. Visual Studio を起動します。

> [!NOTE]
> このレジストリ キーが元に戻ると、Azure Government サブスクリプションは表示されてもアクセスできなくなります。  安全に削除できます。
> 
> 

