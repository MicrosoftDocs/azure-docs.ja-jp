---
title: Azure AD Connect とユーザー プライバシー | Microsoft Docs
description: このドキュメントでは、Azure AD Connect で GDPR コンプライアンスを取得する方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f1d1bf35d03f0f7c4d11e4cc7e9b6f98f38d8ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997700"
---
# <a name="user-privacy-and-azure-ad-connect"></a>ユーザー プライバシーと Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>この記事では、Azure AD Connect とユーザー プライバシーについて説明します。  Azure AD Connect Health とユーザー プライバシーについては、[こちら](reference-connect-health-user-privacy.md)の記事をご覧ください。

2つの方法で、Azure AD Connect をインストールするためのユーザープライバシーを改善します。

1.  要請を受けた際、個人のデータを抽出し、その個人のデータを環境から削除する
2.  すべてのデータが 48 時間しか保持されないようにする。

Azure AD Connect チームは、実装と保守がはるかに簡単な 2 番目のオプションをお勧めします。

Azure AD Connect 同期サーバーは、次のユーザーのプライバシーに関するデータを格納します。
1.  個人に関するデータ (**Azure AD Connect データベース** に格納されます)
2.  **Windows イベント ログ** ファイル内のデータ (個人に関する情報が含まれる可能性のあります)
3.  **Azure AD Connect のインストール ログ ファイル** 内のデータ (個人に関するデータが含まれる可能性があります)

Azure AD Connect のお客様は、ユーザー データを削除するときに次のガイドラインに従ってください。
1.  Azure AD Connect のインストール ログ ファイルが置かれているフォルダーの内容を、定期的に (少なくとも 48 時間おきに) 削除する
2.  この製品では、イベント ログが作成されることもあります。  イベント ログについて詳しくは、[こちらのドキュメント](/windows/win32/wes/windows-event-log)をご覧ください。

個人に関するデータは、その個人のデータが元々置かれていたソース システムから削除されると、Azure AD Connect データベースからも自動的に削除されます。 GDPR に準拠するために、管理者が特定の操作を行う必要はありません。  ただし、Azure AD Connect のデータは、少なくとも 2 日おきにデータ ソースと同期される必要があります。

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Azure AD Connect のインストール ログ ファイル フォルダーの内容を削除する
**c:\programdata\aadconnect** フォルダーの内容は定期的にチェックし、削除するようにしてください。ただし、**PersistedState.Xml** ファイルは例外です。 このファイルには Azure AD Connect の以前のインストールの状態が保持されるため、アップグレード インストールの実行時に必要となります。 このファイルには個人に関するデータは含まれないので、削除しないでください。

>[!IMPORTANT]
>PersistedState.xml ファイルは削除しないでください。  このファイルにはユーザー情報は含まれず、以前のインストール状態が保持されます。

これらのファイルの確認と削除には Windows エクスプ ローラーを使用することもできますし、次のようなスクリプトを使用して、必要なアクションを実行することもできます。


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>このスクリプトを 48 時間おきに実行するようにスケジュールする
スクリプトを 48 時間おきに実行するようスケジュールするには、次の手順に従います。

1.  スクリプトを **&#46;PS1** という拡張子のファイルに保存した後、コントロール パネルを開き、**[システムとセキュリティ]** をクリックします。
    ![システム](./media/reference-connect-user-privacy/gdpr2.png)

2.  [管理ツール] で、 **[タスクのスケジュール]** をクリックします。
    ![タスク](./media/reference-connect-user-privacy/gdpr3.png)
3.  タスク スケジューラで、**[Task Schedule Library]\(タスク スケジュール ライブラリ\)** を右クリックし、**[基本タスクの作成...]** をクリックします
4.  新しいタスクの名前を入力し、**[次へ]** をクリックします。
5.  タスク トリガーとして **[毎日]** を選択し、**[次へ]** をクリックします。
6.  繰り返しを **[2 日]** に設定し、**[次へ]** をクリックします。
7.  アクションとして **[プログラムを起動する]** を選択し、**[次へ]** をクリックします。
8.  プログラム/スクリプトのボックスに「**PowerShell**」と入力し、 **[引数の追加 (オプション)]** というラベルの付いたボックスに、先ほど作成したスクリプトへの完全なパスを入力して、 **[次へ]** をクリックします。
9.  次の画面に、作成しようとしているタスクの概要が表示されます。 値を確認し、 **[完了]** をクリックしてタスクを作成します。



## <a name="next-steps"></a>次のステップ
* [セキュリティ センターで Microsoft のプライバシー ポリシーを確認する](https://www.microsoft.com/trustcenter)
* [Azure AD Connect Health とユーザー プライバシー](reference-connect-health-user-privacy.md)