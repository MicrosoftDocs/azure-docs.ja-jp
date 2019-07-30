---
title: Windows Virtual Desktop プレビューのアプリ グループを管理する - Azure
description: Windows Virtual Desktop プレビューのテナントを Azure Active Directory に設定する方法を説明します。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 73425df1f0cfedd2a681650fc2b536a652b621d5
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206671"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-preview"></a>チュートリアル:Windows Virtual Desktop プレビューのアプリ グループを管理する

Windows Virtual Desktop プレビューの新しいホスト プール向けに作成される既定のアプリ グループには、完全なデスクトップも公開されています。 加えて、ホスト プールには RemoteApp アプリケーション グループ (複数可) を作成することができます。 このチュートリアルに沿って作業すれば、RemoteApp アプリ グループを作成して、独自の **[スタート]** メニュー アプリを公開することができます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * RemoteApp グループを作成する。
> * RemoteApp プログラムへのアクセスを許可する。

作業を開始する前に、PowerShell セッションで使用する [Windows Virtual Desktop PowerShell モジュールをダウンロードしてインポート](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)します (まだの場合のみ)。

## <a name="create-a-remoteapp-group"></a>RemoteApp グループを作成する

1. 次の PowerShell コマンドレットを実行して、新しい空の RemoteApp アプリ グループを作成します。

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (省略可) アプリ グループが作成されたことを確認したければ、次のコマンドレットを実行すると、ホスト プールのすべてのアプリ グループが一覧表示されます。

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. 次のコマンドレットを実行して、ホスト プールの仮想マシン イメージにある **[スタート]** メニュー アプリの一覧を取得します。 **FilePath**、**IconPath**、**IconIndex** など、公開したいアプリケーションの重要な情報の値を書き留めます。

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. 次のコマンドレットを実行して、`AppAlias` に基づくアプリケーションをインストールします。 `AppAlias` は、手順 3. の出力を実行すると利用できるようになります。

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (省略可) 次のコマンドレットを実行して、手順 1. で作成したアプリケーション グループに新しい RemoteApp プログラムを発行します。

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. アプリが公開されたことを確認するために、次のコマンドレットを実行します。

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. このアプリ グループに発行するアプリケーションごとに手順 1. から手順 5. を繰り返します。
8. 次のコマンドレットを実行して、アプリ グループ内の RemoteApp プログラムへのアクセスをユーザーに許可します。

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、アプリ グループを作成して、RemoteApp プログラムによりそれに値を設定し、アプリ グループにユーザーを割り当てる方法について説明しました。 検証ホスト プールを作成する方法については、次のチュートリアルを参照してください。 運用環境に展開する前に、検証ホスト プールを使用してサービスの更新プログラムを監視できます。

> [!div class="nextstepaction"]
> [サービスの更新プログラムを検証するためのホスト プールを作成する](./create-validation-host-pool.md)
