---
title: パスワードを Microsoft Authenticator アプリにインポートする - Azure AD
description: 一般的なパスワード マネージャーから Microsoft 認証アプリにパスワードをインポートする方法。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/28/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: ecc6580148dfba92077336a26ff9160fbe88eb2c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99806157"
---
# <a name="import-passwords-into-the-microsoft-authenticator-app"></a>パスワードを Microsoft Authenticator アプリにインポートする

Microsoft Authenticator では、Google Chrome、Firefox、LastPass、Bitwarden、Roboform からのパスワードのインポートがサポートされています。 利用中のパスワード マネージャーが Microsoft でまだサポートされていない場合、[サインイン資格情報をテンプレート CSV に手動で入力する](https://go.microsoft.com/fwlink/?linkid=2134938)ことができます。 既存のパスワードをインポートして Authenticator アプリで管理するには、既存のパスワード マネージャーからコンマ区切り値 (CSV) 形式でパスワードをエクスポートするだけです。 次に、エクスポートされた CSV を Chrome ブラウザー拡張機能で Authenticator にインポートするか、Authenticator アプリ (Android および iOS) に直接インポートします。

## <a name="import-from-google-chrome-or-android-smart-lock"></a>Google Chrome または Android Smart Lock からインポートする

スマートフォンまたはデスクトップ コンピューターで、Google Chrome または Android Smart Lock から Authenticator にパスワードをインポートできます。 次のようにすることができます。

- [Android および iOS の Chrome からインポートする](#import-from-chrome-on-android-and-ios)
- [Chrome デスクトップ ブラウザーからインポートする](#import-from-chrome-desktop-browser)

### <a name="import-from-chrome-on-android-and-ios"></a>Android および iOS の Chrome からインポートする

Android および Apple 携帯電話の Google Chrome ユーザーは、いくつかの簡単な手順で携帯電話から直接パスワードをインポートできます。

1. Authenticator アプリを携帯電話にインストールし、 **[パスワード]** タブを開きます。

1. 携帯電話上で Google Chrome にサインインします。

1. Android 携帯電話では右上、iOS デバイスでは右下にある ![Google Chrome の省略記号メニュー](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png)をタップしてから **[設定]** をタップします。

   プラットフォーム | Link
   ---------- | --------
   Android | ![Google Chrome の [設定] メニューの場所](./media/user-help-authenticator-app-import-passwords/android-settings-menu.png)
   iOS | ![Google Chrome の [設定] メニュー アイコン](./media/user-help-authenticator-app-import-passwords/apple-settings-menu.png)

1. **[設定]** で、 **[パスワード]** を開きます。

   プラットフォーム | Link
   ---------- | --------
   Android | ![Andoid 版 Chrome の [パスワード] コマンドの場所](./media/user-help-authenticator-app-import-passwords/android-passwords-location.png)
   iOS | ![Apple 版 Chrome の [パスワード] コマンドの場所](./media/user-help-authenticator-app-import-passwords/apple-passwords-location.png)

1. Android デバイスでは右上、iOS デバイスでは右下にある ![Google Chrome の省略記号メニュー](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png)をタップしてから **[パスワードをエクスポート]** をタップします。

   プラットフォーム | Link
   ---------- | --------
   Android | ![Android 版 Chrome の [パスワードをエクスポート] の場所](./media/user-help-authenticator-app-import-passwords/android-export-passwords-location.png)
   iOS | ![Apple 版 Chrome の [パスワードをエクスポート] の場所](./media/user-help-authenticator-app-import-passwords/apple-export-passwords-location.png)

   PIN、指紋、または顔認識を提供する必要があります。 本人確認後、 **[パスワードをエクスポート]** をもう一度タップするとエクスポートが始まります。

1. パスワードがエクスポートされると、インポート先のアプリを選択するよう Chrome から求められます。 **Authenticator** を選択してパスワードのインポートを開始します。完了すると、インポートの状態が通知されます。

   プラットフォーム | Link
   ---------- | --------
   Android | ![Chrome のパスワードをインポートする場所 (Android)](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
   iOS | ![Chrome のパスワードをインポートする場所 (Apple)](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

### <a name="import-from-chrome-desktop-browser"></a>Chrome デスクトップ ブラウザーからインポートする

始める前に、[Microsoft オートフィル拡張機能](https://chrome.google.com/webstore/detail/microsoft-autofill/fiedbfgcleddlbcmgdigjgdfcggjcion)を Chrome ブラウザーにインストールしてサインインする必要があります。

1. 任意のブラウザーで [Google パスワード マネージャー](https://passwords.google.com)を開きます。 まだサインインしていない場合、Google アカウントにサインインします。

1. 歯車アイコン ![デスクトップ版パスワード マネージャーの歯車アイコン](./media/user-help-authenticator-app-import-passwords/desktop-password-manager-gear.png) をクリックしてパスワード設定ページを開きます。

1. **[エクスポート]** をクリックし、次のページでも **[エクスポート]** をクリックしてパスワードのエクスポートを開始します。 本人確認を求められたら、Google のパスワードを入力します。 完了すると、インポートの状態が通知されます。

   ![デスクトップ版 Chrome ブラウザーの [パスワードをエクスポート] コマンドの場所](./media/user-help-authenticator-app-import-passwords/desktop-chrome-export-passwords-location.png)

1. [オートフィル] Chrome 拡張機能を開き、 **[設定]** をクリックします。

   ![デスクトップ版 Chrome ブラウザーの [オートフィル] 拡張機能の設定の場所](./media/user-help-authenticator-app-import-passwords/desktop-chrome-autofill-settings.png)

1. **[データのインポート]** をクリックしてダイアログを開きます。 **[ファイルを選択]** をクリックし、CSV ファイルを選択してインポートします。

   ![CSV データをインポートする場所 (デスクトップ Chrome ブラウザー)](./media/user-help-authenticator-app-import-passwords/desktop-chrome-import-csv.png)

## <a name="import-from-firefox"></a>Firefox からインポートする

Firefox ではデスクトップ ブラウザーからしかパスワードをエクスポートできないため、Firefox からパスワードをインポートする前に、Firefox デスクトップ ブラウザーにアクセスできることを確認してください。

1. デスクトップで Firefox の最新バージョンにサインインし、画面右上の ![Firefox の "ハンバーガー" メニュー](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png) を選択します。

1. **[ログインとパスワード]** を選択します。

   ![デスクトップ版 Firefox ブラウザーの [ログインとパスワード] の場所](./media/user-help-authenticator-app-import-passwords/desktop-firefox-passwords-location.png)

1. [Firefox Lockwise] ページで、![Firefox の省略記号メニュー](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png)を選択し、 **[ログイン情報をエクスポート]** を選択し、 **[エクスポート]** を選択して意図を確認します。 PIN またはデバイス パスワードを入力するか、指紋をスキャンして本人確認を行うよう求められます。 本人確認後、選択した場所にパスワードが CSV 形式でエクスポートされます。

   ![デスクトップ版 Firefox ブラウザーのパスワードのエクスポートの場所](./media/user-help-authenticator-app-import-passwords/desktop-firefox-export-passwords-location.png)

1. デスクトップ ブラウザーから、あるいは iOS または Android 携帯電話上でパスワードを Authenticator にインポートできます。 携帯電話上で Authenticator アプリにインポートする方法は次のとおりです。

      1. エクスポートした CSV ファイルを、推奨された安全な方法を使用して Android または iOS 携帯電話に転送し、ダウンロードします。 次に、CSV ファイルを Authenticator アプリと共有してインポートを開始します。

         プラットフォーム | Link
         ---------- | --------
         Android | ![Chrome のパスワードをインポートする場所 (Android)](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Chrome のパスワードをインポートする場所 (Apple)](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. パスワードが Authenticator に正しくインポートされたら、デスクトップまたは携帯電話から CSV ファイルを削除します。

## <a name="import-from-lastpass"></a>LastPass からインポートする

LastPass では、デスクトップ ブラウザーからのパスワードのエクスポートしかサポートされていないため、パスワードのインポートを開始する前に、デスクトップ ブラウザーにアクセスできることを確認してください。

1. [LastPass Web サイト](https://lastpass.com)にサインインし、 **[Advanced Options]** 、 **[Export]** の順に選択します。

   ![デスクトップ版 LastPass のパスワードのエクスポートの場所](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-export-passwords-location.png)

1. 本人確認を求められたら、マスター パスワードを入力して確認を行います。 確認後、エクスポートしたパスワードが Web ページに表示されます。

1. Web ページの内容をコピーします。

1. メモ帳 (または任意のテキスト エディター) を開き、コピーした内容を貼り付けます。

1. **[ファイル]** &gt; **[名前を付けて保存]** を選択して、このメモ帳ファイルを保存します。 デスクトップの安全な場所を選択し、".csv" で終わる名前 (LastPass.csv など) を指定します。

   ![LastPass の CSV ファイルをデスクトップに保存](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-save-import-file.png)

1. デスクトップ ブラウザーで、あるいは iOS または Android 携帯電話上でパスワードを Authenticator にインポートできます。 携帯電話上で Authenticator アプリにインポートする方法は次のとおりです。

      1. エクスポートした CSV ファイルを、推奨された安全な方法を使用してスマートフォンに転送し、ダウンロードします。 次に、CSV ファイルを Authenticator アプリと共有してインポートを開始します。

         プラットフォーム | Link
         ---------- | --------
         Android | ![LastPass のパスワードをインポートする場所 (Android)](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![LastPass のパスワードをインポートする場所 (Apple)](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. パスワードが Authenticator に正しくインポートされたら、デスクトップまたは携帯電話から CSV ファイルを削除します。

## <a name="import-from-bitwarden"></a>Bitwarden からインポートする

Bitwarden では、デスクトップ ブラウザーからのパスワードのエクスポートしかサポートされていないため、パスワードのインポートを開始する前に、デスクトップ ブラウザーにアクセスできることを確認してください。

1. https://vault.bitwarden.com/ にサインインし、 **[Tools]** &gt; **[Export vault]** を選択します。 CSV ファイル形式を選択し、マスター パスワードを入力してから、 **[Export vault]** を選択してエクスポートを開始します。

   ![Bitwarden のコンテナーのエクスポートの場所](./media/user-help-authenticator-app-import-passwords/desktop-bitwarden-export-command-location.png)

1. デスクトップ ブラウザーで、あるいは iOS または Android 携帯電話上でパスワードを Authenticator にインポートできます。 携帯電話上で Authenticator アプリにインポートする方法は次のとおりです。

      1. エクスポートした CSV ファイルを、推奨された安全な方法を使用してスマートフォンに転送し、ダウンロードします。 次に、CSV ファイルを Authenticator アプリと共有してインポートを開始します。

         プラットフォーム | Link
         ---------- | --------
         Android | ![Bitwarden のパスワードをインポートする場所 (Android)](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Bitwarden のパスワードをインポートする場所 (Apple)](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. パスワードが Authenticator に正しくインポートされたら、デスクトップまたは携帯電話から CSV ファイルを削除します。

## <a name="import-from-roboform"></a>Roboform からインポートする

Roboform では、デスクトップ アプリからのパスワードのエクスポートしか許可されていないため、インポートを開始する前に、デスクトップの Roboform アプリにアクセスできることを確認してください。

1. デスクトップ クライアントから RoboForm を起動し、アカウントにログインします。

1. **[Roboform]** メニューから **[Options]** を選択します。

   ![デスクトップ版 Roboform のオプション メニュー](./media/user-help-authenticator-app-import-passwords/desktop-roboform-options.png)

1. **[Account & Data]** &gt; **[Export]** を選択します。

   ![デスクトップ版 Roboform のエクスポート コマンドの場所](./media/user-help-authenticator-app-import-passwords/desktop-roboform-accounts-data.png)

1. エクスポートしたファイルを保存する安全な場所を選択します。 **[Data]** の種類として **[Logins]** を選択し、形式として CSV ファイルを選択してから、 **[Export]** を選択します。

   ![デスクトップ版 Roboform のエクスポート ダイアログ ボックス](./media/user-help-authenticator-app-import-passwords/desktop-roboform-export-dialog.png)

1. 意図を確認すると、選択した場所に CSV ファイルがエクスポートされます。

   ![デスクトップ版 Roboform のエクスポート確認ダイアログ ボックス](./media/user-help-authenticator-app-import-passwords/desktop-roboform-confirmation.png)

1. デスクトップ ブラウザーで、あるいは iOS または Android 携帯電話上でパスワードを Authenticator にインポートできます。 携帯電話上で Authenticator アプリにインポートする方法は次のとおりです。

      1. エクスポートした CSV ファイルを、推奨された安全な方法を使用してスマートフォンに転送し、ダウンロードします。 次に、CSV ファイルを Authenticator アプリと共有してインポートを開始します。

         プラットフォーム | Link
         ---------- | --------
         Android | ![Roboform のパスワードをインポートする場所 (Android)](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Roboform のパスワードをインポートする場所 (Apple)](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. パスワードが Authenticator に正しくインポートされたら、デスクトップまたは携帯電話から CSV ファイルを削除します。

## <a name="import-by-creating-a-csv"></a>CSV を作成してインポートする

利用中のパスワード マネージャーからパスワードをインポートする手順がこの記事に掲載されていない場合、パスワードを Authenticator にインポートするために使用できる CSV を作成できます。 Microsoft では、書式設定を簡単にするために、デスクトップで次の手順を実行することをお勧めします。

1. デスクトップで、[インポート テンプレートをダウンロードして開きます](https://go.microsoft.com/fwlink/?linkid=2134938)。 Apple iPhone、Safari、および Keychain ユーザーは、スキップして手順 4 に進むことができます。

1. 利用中のパスワード マネージャーから、暗号化していない CSV ファイルの形式でパスワードをエクスポートします。

1. エクスポートした CSV から、必要な列をテンプレート CSV にコピーして保存します。

1. エクスポートした CSV がない場合、利用中のパスワード マネージャーから個々のログイン情報をテンプレート CSV にコピーできます。 ヘッダー行を削除または変更しないでください。 完了したら、次の手順を開始する前に、データの整合性を確認します。

1. デスクトップ ブラウザーで、あるいは iOS または Android 携帯電話上でパスワードを Authenticator にインポートできます。 携帯電話上で Authenticator アプリにインポートする方法は次のとおりです。

      1. エクスポートした CSV ファイルを、推奨された安全な方法を使用してスマートフォンに転送し、ダウンロードします。 次に、CSV ファイルを Authenticator アプリと共有してインポートを開始します。

         プラットフォーム | Link
         ---------- | --------
         Android | ![パスワードの CSV をインポートする場所 (Android)](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![パスワードの CSV をインポートする場所 (Apple)](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. パスワードが Authenticator に正しくインポートされたら、デスクトップまたは携帯電話から CSV ファイルを削除します。

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順

インポート失敗の原因として最も多いのは、CSV ファイルの形式が正しくないことです。 この問題をトラブルシューティングするために、次の手順を試すことができます。

- 利用中のパスワード マネージャーからのパスワードのインポートが既にサポートされているかどうか、この記事で確認してください。 サポートされている場合、プロバイダーごとの説明されている手順に従ってインポートを再試行してください。

- 利用中のパスワード マネージャーの形式でのインポートがまだサポートされていない場合、[手動で CSV ファイルを作成](#import-by-creating-a-csv)して再試行することができます。

- 次の推奨事項に従って CSV データの整合性を確認してください。

  - 1 行目は **url**、**username**、**password** の 3 列からなるヘッダーにする必要があります。

  - 各行の **url** 列と **passwords** 列に値が含まれている必要があります。

- 必要な情報を [CSV テンプレート ファイル](https://go.microsoft.com/fwlink/?linkid=2134938)に貼り付けて CSV の作成をやり直してみてください。

- 解決できない場合は、Authenticator アプリの設定メニューにある **[フィードバックの送信]** リンクを使用して問題を報告してください。
