---
title: Windows Virtual Desktop で組み込みアプリを発行する - Azure
description: Windows Virtual Desktop で組み込みアプリを発行する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 32441a7da0e079a2de2fbd6906184c518d60e4a5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562862"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Windows Virtual Desktop で組み込みアプリを発行する

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトを含む Windows Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトを使用しない Windows Virtual Desktop (クラシック) を使用している場合は、[こちらの記事](./virtual-desktop-fall-2019/publish-apps-2019.md)を参照してください。

この記事では、Windows Virtual Desktop 環境でアプリを発行する方法について説明します。

## <a name="publish-built-in-apps"></a>組み込みアプリの公開

組み込みアプリを発行するには:

1. ホスト プール内のいずれかの仮想マシンに接続します。
2. [この記事](/powershell/module/appx/get-appxpackage)の手順に従って、発行するアプリの **PackageFamilyName** を取得します。
3. 最後に、`<PackageFamilyName>` を前の手順で確認した **PackageFamilyName** に置き換えて、次のコマンドレットを実行します。

   ```powershell
   New-AzWvdApplication -Name <applicationname> -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -FilePath "shell:appsFolder\<PackageFamilyName>!App" -CommandLineSetting <Allow|Require|DoNotAllow> -IconIndex 0 -IconPath <iconpath> -ShowInPortal:$true
   ```

>[!NOTE]
> Windows Virtual Desktop は、インストール場所が `C:\Program Files\WindowsApps` で始まるアプリの発行のみをサポートします。

## <a name="update-app-icons"></a>アプリ アイコンの更新

アプリを発行すると、通常のアイコン画像ではなく、既定の Windows アプリ アイコンが表示されます。 アイコンを通常のアイコンに変更するには、目的のアイコンの画像をネットワーク共有に配置します。 サポートされているイメージ形式は、PNG、BMP、GIF、JPG、JPEG、および ICO です。

## <a name="publish-microsoft-edge"></a>Microsoft Edge の発行

Microsoft Edge の発行に使用するプロセスは、他のアプリの発行プロセスとは少し異なります。 既定のホーム ページを使用して Microsoft Edge を発行するには、次のコマンドレットを実行します。

```powershell
New-AzWvdApplication -Name -ResourceGroupName -ApplicationGroupName -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" -CommandLineSetting <Allow|Require|DoNotAllow> -iconPath "C:\Windows\SystemApps\Microsoft.MicrosoftEdge_8wekyb3d8bbwe\microsoftedge.exe" -iconIndex 0 -ShowInPortal:$true
```

## <a name="next-steps"></a>次のステップ

- フィードを構成して、ユーザーのためにアプリを表示する方法を整理する方法について、「[Windows Virtual Desktop ユーザー用のフィードをカスタマイズする](customize-feed-for-virtual-desktop-users.md)」を参照してください。
- MSIX アプリのアタッチ機能について、「[MSIX アプリのアタッチを設定する](app-attach.md)」を参照してください。

