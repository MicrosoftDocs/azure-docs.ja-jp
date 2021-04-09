---
title: Windows ユーザー用 Azure Cloud Shell | Microsoft Docs
description: Linux システムに慣れていないユーザー向けのガイド
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/03/2018
ms.author: damaerte
ms.openlocfilehash: 8cc1934cc97ecf821de8644dda45d867b3ca3e85
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102508948"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>Windows ユーザー用 Azure Cloud Shell 内の PowerShell

2018 年 5 月、Azure Cloud Shell 内の PowerShell の変更が[発表](https://azure.microsoft.com/blog/pscloudshellrefresh/)されました。
Azure Cloud Shell の PowerShell エクスペリエンスは、Linux 環境で [PowerShell Core 6](https://github.com/powershell/powershell) として利用できるようになりました。
この変更により、Cloud Shell の PowerShell エクスペリエンスと、Windows PowerShell エクスペリエンスで期待される機能を比較すると、いくつか違いが生じる場合があります。

## <a name="file-system-case-sensitivity"></a>ファイル システムの大文字と小文字の区別

Windows ファイル システムでは大文字と小文字が区別されませんが、Linux ファイル システムでは大文字と小文字が区別されます。
`file.txt` と `FILE.txt` は以前は同じファイルとみなされていましたが、今後は別々のファイルとみなされるようになります。
ファイル システムで `tab-completing` の実行時は、大文字と小文字を適切に区別する必要があります。
`tab-completing` コマンドレット名、パラメーター、値など、PowerShell 固有のエクスペリエンスでは大文字と小文字は区別されません。

## <a name="windows-powershell-aliases-vs-linux-utilities"></a>Windows PowerShell エイリアスと Linux ユーティリティ

既存の PowerShell エイリアスの一部は、`cat`、`ls`、`sort`、`sleep` などの組み込みの Linux コマンドと名前が同じです。PowerShell Core 6 では、組み込みの Linux コマンドと衝突するエイリアスは削除されました。
以下は、削除された共通のエイリアスと、その同等のコマンドです。  

|削除されるエイリアス   |同等のコマンド   |
|---|---|
|`cat`    | `Get-Content` |
|`curl`   | `Invoke-WebRequest` |
|`diff`   | `Compare-Object` |
|`ls`     | `dir` <br> `Get-ChildItem` |
|`mv`     | `Move-Item`   |
|`rm`     | `Remove-Item` |
|`sleep`  | `Start-Sleep` |
|`sort`   | `Sort-Object` |
|`wget`   | `Invoke-WebRequest` |

## <a name="persisting-home"></a>$HOME の永続化

以前のユーザーは、スクリプトやその他のファイルを永続化するには、クラウド ドライブを使用する必要がありました。
ユーザーの $HOME ディレクトリもセッション全体で永続化されるようになりました。

## <a name="powershell-profile"></a>PowerShell プロファイル

既定では、ユーザーの PowerShell プロファイルは作成されません。
プロファイルを作成するには、`$HOME/.config` 下に `PowerShell` ディレクトリを作成します。

```azurepowershell-interactive
mkdir (Split-Path $profile.CurrentUserAllHosts)
```

`$HOME/.config/PowerShell` 以下にプロファイル ファイル (`profile.ps1`、`Microsoft.PowerShell_profile.ps1`、またはその両方) を作成できます。

## <a name="whats-new-in-powershell-core-6"></a>PowerShell Core 6 の新機能

PowerShell Core 6 の新機能の詳細については、[PowerShell ドキュメント](/powershell/scripting/whats-new/what-s-new-in-powershell-70)および「[Getting Started with PowerShell Core](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/)」(PowerShell Core の概要) のブログ記事を参照してください。