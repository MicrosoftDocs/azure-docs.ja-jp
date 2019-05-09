---
title: IE 用 Azure アクセス パネル拡張機能のトラブルシューティング | Microsoft Docs
description: グループ ポリシーを使用してマイ アプリ ポータル用の Internet Explorer アドオンをデプロイする方法。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: celested
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63dfece713657df4450f18b8a7ce212ce2c41687
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565029"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Internet Explorer 用アクセス パネル拡張機能のトラブルシューティング

この記事は、次の問題をトラブルシューティングする際に役立ちます。

* Internet Explorer を使用中にマイ アプリ ポータルからアプリにアクセスできない。
* ソフトウェアをインストール済みでも "ソフトウェアをインストールしてください" というメッセージが表示される。

管理者の方は、「[グループ ポリシーを使用して Internet Explorer 用アクセス パネル拡張機能をデプロイする方法](deploy-access-panel-browser-extension.md)」を参照してください。

## <a name="run-the-diagnostic-tool"></a>診断ツールを実行する

アクセス パネルの診断ツールをダウンロードし実行することで、アクセス パネル拡張機能のインストールに関する問題を診断することができます。 

診断ツールをダウンロードしてインストールするには、次の手順を実行します。

1. [このリンクを選択して診断ツールをダウンロードします。](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)

2. ファイルを開き、コンピューターにコンテンツを抽出します。
   
3. ツールを実行するには、*AccessPanelExtensionDiagnosticTool.js* という名前のファイルを右クリックし、**[プログラムから開く]** > **[Microsoft Windows Based Script Host]** の順に選択します。
   
    ![[プログラムから開く] > [Microsoft Windows Based Script Host]](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

4. 表示された診断結果を確認し、**[はい]** を選択して問題を修正します。 拡張機能が動作しない場合は、対処方法に関する情報を含む **[検査結果]** ダイアログ ボックスが表示されます。  

5. メッセージを確認し、**[OK]** を選択します。

## <a name="check-that-the-access-panel-extension-is-enabled"></a>アクセス パネル拡張機能が有効になっていることを確認する

Internet Explorer でアクセス パネル拡張機能が有効になっていることを確認するには、次の手順を実行します。

1. Internet Explorer で、ウィンドウの右上隅の**歯車アイコン**を選択し、**[インターネット オプション]** を選択します。
   
2. **[プログラム]** タブに移動して **[アドオンの管理]** を選択します。
   
3. **[Microsoft Corporation]** セクションで **[アクセス パネル拡張機能]** を選択し、**[有効にする]** を選択します。
   
4. 変更を保存するには、開いているすべての Internet Explorer ブラウザーのウィンドウを閉じます。 変更は、次回 Internet Explorer を開いたときに有効になります。

## <a name="enable-extensions-for-inprivate-browsing"></a>InPrivate ブラウズで拡張機能を有効にする

InPrivate ブラウズで拡張機能を有効にするには、次の手順を実行します。

1. Internet Explorer で、ウィンドウの右上隅の**歯車アイコン**を選択し、**[インターネット オプション]** を選択します。
   
2. **[プライバシー]** タブに移動し、**[InPrivate ブラウズの開始時に、ツール バーと拡張機能を無効にする]** がオフになっていることを確認します。
   
3.  変更を保存するには、開いているすべての Internet Explorer ブラウザーのウィンドウを閉じます。 変更は、次回 Internet Explorer を開いたときに有効になります。

## <a name="uninstall-the-access-panel-extension"></a>アクセス パネル拡張機能のアンインストール

アクセス パネル拡張機能をコンピューターからアンインストールするには、次の手順を実行します。

1. コントロール パネルで *[アンインストール]* を検索します。 

2. 検索結果で、**[プログラムのアンインストール]** を選択します。
   
    ![アンインストール プログラムを検索します。](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

3. 一覧から **[アクセス パネル拡張機能]** を選択し、**[アンインストール]** を選択します。

    ![アクセス パネル拡張機能をアンインストールする。](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)
   
4. 拡張機能をもう一度インストールして、問題が解決されたかどうかを確認することができます。

拡張機能のアンインストールで問題が発生する場合は、[Microsoft Fix It](https://go.microsoft.com/?linkid=9779673) ツールを使用して削除することもできます。

## <a name="related-articles"></a>関連記事
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](what-is-single-sign-on.md)
* [グループ ポリシーを使用して Internet Explorer 用アクセス パネル拡張機能をデプロイする方法](deploy-access-panel-browser-extension.md)

