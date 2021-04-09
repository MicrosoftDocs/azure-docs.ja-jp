---
title: MSIXMGR ツールの使用 - Azure
description: Windows Virtual Desktop 用の MSIXMGR ツールを使用する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 02/23/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4b34fb0d3bb2d49255007b9722a0a636c1441b8c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743656"
---
# <a name="using-the-msixmgr-tool"></a>MSIXMGR ツールの使用

MSIXMGR ツールは、MSIX でパッケージ化されたアプリケーションを MSIX イメージに展開するためのものです。 このツールは、MSIX でパッケージ化されたアプリケーション (.MSIX) を取り込み、VHD、VHDx、または CIM ファイルに展開します。 結果の MSIX イメージは、Windows Virtual Desktop のデプロイで使用される Azure ストレージ アカウントに格納されます。この記事では、MSIXMGR ツールの使用方法について説明します。

>[!NOTE]
>互換性を確保するため、MSIX イメージを格納する CIM が、Windows Virtual Desktop ホスト プールで実行されている OS バージョンで生成されていることを確認してください。 MSIXMGR で CIM ファイルを作成することはできますが、それらのファイルを使用できるのは、Windows 10 20H2 が実行されているホスト プールのみです。

## <a name="requirements"></a>要件

この記事の手順を実行する前に、次のことを行う必要があります。

- [MSIXMGR ツールをダウンロードします](https://aka.ms/msixmgr)
- MSIX でパッケージ化されたアプリケーションを取得します (.MSIX ファイル)
- MSIX イメージを作成するマシンでの管理アクセス許可を取得します

## <a name="create-an-msix-image"></a>MSIX イメージを作成する

展開のプロセスでは、MSIX でパッケージ化されたアプリケーション (.MSIX) が取得されて、MSIX イメージ (.VHD(x) または .CIM ファイル) に解凍されます。

MSIX ファイルを展開するには:

1. まだ行っていない場合は、[MSIXMGR ツールをダウンロード](https://aka.ms/msixmgr)します。

2. MSIXMGR.zip をローカル フォルダーに解凍します。

3. 管理者特権モードでコマンド プロンプトを開きます。

4. ステップ 2 のローカル フォルダーを見つけます。

5. コマンド プロンプトで次のコマンドを実行して、MSIX イメージを作成します。

    ```cmd
    msixmgr.exe -Unpack -packagePath <path to package> -destination <output folder> [-applyacls] [-create] [-vhdSize <size in MB>] [-filetype <CIM | VHD | VHDX>] [-rootDirectory <rootDirectory>]
    ```

    プレースホルダーの値は、関連する値に置き換えてください。 次に例を示します。

    ```cmd
    msixmgr.exe -Unpack -packagePath "C:\Users\%username%\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
    ```

6. イメージを作成したので、展開先のフォルダーに移動し、MSIX イメージ (.VHDX) が正常に作成されたことを確認します。

## <a name="create-an-msix-image-in-a-cim-file"></a>CIM ファイルに MSIX イメージを作成する

[ステップ 5](#create-an-msix-image) のコマンドを使用して、ファイルの種類と展開先のパスを置き換えることで、CIM ファイルと VHDX ファイルを作成することもできます。

たとえば、そのコマンドを使用して CIM ファイルを作成する方法を次に示します。

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.cim" -applyacls -create -vhdSize 200 -filetype "cim" -rootDirectory apps
```

次に示すのは、そのコマンドを使用して VHDX を作成する方法です。

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
```

## <a name="next-steps"></a>次のステップ

MSIX アプリのアタッチの詳細については、「[MSIX アプリのアタッチとは](what-is-app-attach.md)」を参照してください。

アプリのアタッチを設定する方法については、次の記事を確認してください。

- [Azure portal で MSIX アプリのアタッチを設定する](app-attach-azure-portal.md)
- [PowerShell を使用して MSIX アプリのアタッチを設定する](app-attach-powershell.md)
- [MSIX アプリのアタッチ用の PowerShell スクリプトを作成する](app-attach.md)
- [Windows Virtual Desktop 用の MSIX イメージを準備する](app-attach-image-prep.md)
- [MSIX アプリのアタッチ用にファイル共有を設定する](app-attach-file-share.md)

MSIX アプリのアタッチについて不明な点がある場合は、[アプリのアタッチのよくあるご質問](app-attach-faq.md)および[アプリのアタッチの用語集](app-attach-glossary.md)に関する記事を参照してください。
