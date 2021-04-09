---
title: PowerShell を使用した RDP プロパティのカスタマイズ - Azure
description: PowerShell コマンドレットを使用して Windows Virtual Desktop 用の RDP プロパティをカスタマイズする方法。
author: Heidilohr
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cc3a08f383368b189e41bebd204707f2483e77c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95018309"
---
# <a name="customize-remote-desktop-protocol-rdp-properties-for-a-host-pool"></a>ホスト プールのリモート デスクトップ プロトコル (RDP) プロパティをカスタマイズする

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトを含む Windows Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトを使用しない Windows Virtual Desktop (クラシック) を使用している場合は、[こちらの記事](./virtual-desktop-fall-2019/customize-rdp-properties-2019.md)を参照してください。

マルチ モニター エクスペリエンスやオーディオ リダイレクトなど、ホスト プールのリモート デスクトップ プロトコル (RDP) のプロパティをカスタマイズすると、ニーズに基づいてユーザーに最適なエクスペリエンスを提供できます。 Azure portal を使用するか、**Update-AzWvdHostPool** コマンドレットで *-CustomRdpProperty* パラメーターを使用して、Windows Virtual Desktop の RDP プロパティをカスタマイズできます。

サポートされているプロパティとその既定値の全リストについては、「[サポートされるリモート デスクトップ RDP ファイルの設定](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext)」を参照してください。

## <a name="default-rdp-file-properties"></a>既定の RDP ファイルのプロパティ

RDP ファイルには、既定で次のプロパティがあります。

|RDP プロパティ|デスクトップ上|RemoteApp として|
|---|---|---|
|マルチモニター モード|無効|Enabled|
|ドライブ リダイレクト有効|ドライブ、クリップボード、プリンター、COM ポート、およびスマート カード|ドライブ、クリップボード、プリンター|
|リモート オーディオ モード|ローカルで再生|ローカルで再生|

## <a name="prerequisites"></a>前提条件

作業を開始する前に、[Windows Virtual Desktop PowerShell モジュールの設定](powershell-module.md)に関するページの手順に従って、PowerShell モジュールを設定し、Azure にサインインしてください。

## <a name="configure-rdp-properties-in-the-azure-portal"></a>Azure portal で RDP プロパティを構成する

Azure portal で RDP プロパティを構成するには

1. <https://portal.azure.com> で Azure にサインインします。
2. 検索バーに「**windows virtual desktop**」と入力します。
3. サービスの下で **[Windows Virtual Desktop]** を選択します。
4. [Windows Virtual Desktop] ページで、画面の左側にあるメニューの **[ホスト プール]** を選択します。
5. 更新する **ホスト プールの名前** を選択します。
6. 画面の左側にあるメニューで **[RDP プロパティ]** を選択します。
7. 必要なプロパティを設定します。
   - または、 **[詳細]** タブを開き、次のセクションの PowerShell の例のように、セミコロンで区切られた形式で RDP プロパティを追加できます。
8. 完了したら、 **[保存]** を選択して変更を保存します。

次のセクションでは、PowerShell でカスタム RDP プロパティを手動で編集する方法について説明します。

## <a name="add-or-edit-a-single-custom-rdp-property"></a>単一のカスタム RDP プロパティを追加または編集する

単一のカスタム RDP プロパティを追加または編集するには、次の PowerShell コマンドレットを実行します。

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty <property>
```

実行したコマンドレットでプロパティを更新したかどうかを確認するには、次のコマンドレットを実行します。

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

たとえば、0301HP という名前のホスト プールで "audiocapturemode" プロパティを確認している場合は、次のコマンドレットを入力します。

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;
```

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>複数のカスタム RDP プロパティを追加または編集する

複数のカスタム RDP プロパティを追加または編集するには、カスタム RDP プロパティをセミコロンで区切った文字列として指定して、次の PowerShell コマンドレットを実行します。

```powershell
$properties="<property1>;<property2>;<property3>"
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty $properties
```

次のコマンドレットを実行して、RDP プロパティが追加されたことを確認できます。

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

前のコマンドレットの例に基づいて、0301HP ホスト プールに複数の RDP プロパティを設定した場合、コマンドレットは次のようになります。

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;audiomode:i:0;
```

## <a name="reset-all-custom-rdp-properties"></a>すべてのカスタム RDP プロパティをリセットする

「[単一のカスタム RDP プロパティを追加または編集する](#add-or-edit-a-single-custom-rdp-property)」の手順に従って個々のカスタム RDP プロパティを既定値にリセットすることも、次の PowerShell コマンドレットを実行してホスト プールのすべてのカスタム RDP プロパティをリセットすることもできます。

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty ""
```

設定が正常に削除されたことを確認するには、次のコマンドレットを入力します。

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <CustomRDPpropertystring>
```

## <a name="next-steps"></a>次のステップ

特定のホスト プールの RDP プロパティをカスタマイズしたので、Windows Virtual Desktop クライアントにサインインして、それらをユーザー セッションの一部としてテストできます。 次の攻略ガイドでは、選択したクライアントを使用してセッションに接続する方法を説明します。

- [Windows デスクトップ クライアントを使用して接続する](connect-windows-7-10.md)
- [Web クライアントに接続する](connect-web.md)
- [Android クライアントに接続する](connect-android.md)
- [macOS クライアントに接続する](connect-macos.md)
- [iOS クライアントに接続する](connect-ios.md)