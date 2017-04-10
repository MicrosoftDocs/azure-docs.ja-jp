---
title: "Windows 10 ローミング設定リファレンス | Microsoft Docs"
description: "Windows 10 でローミングまたはバックアップされる全設定の一覧です。"
services: active-directory
keywords: "Enterprise State Roaming, Windows クラウド"
documentationcenter: 
author: tanning
manager: femila
editor: curtand
ms.assetid: 17cffc3e-2928-4235-91f7-a685bd6bdcbf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 9b88b18e32e6a5ef6c0a0195e649fe2862583a0b
ms.openlocfilehash: c2c50ff6e92cf42a7d309afafb01210c4edabbe0
ms.lasthandoff: 01/05/2017


---
# <a name="windows-10-roaming-settings-reference"></a>Windows 10 ローミング設定リファレンス
以下に示したのは、Windows 10 でローミングまたはバックアップされる全設定の一覧です。 

## <a name="devices-and-endpoints"></a>デバイスとエンドポイント
次の表は、Windows 10 における同期、バックアップ、復元のフレームワークでサポートされるデバイスとアカウントの種類をまとめたものです。

| アカウントの種類と操作 | デスクトップ | モバイル |
| --- | --- | --- |
| Azure Active Directory: 同期 |はい |なし |
| Azure Active Directory: バックアップ/復元 |なし |なし |
| Microsoft アカウント: 同期 |はい |はい |
| Microsoft アカウント: バックアップ/復元 |なし |はい |

## <a name="what-is-backup"></a>バックアップとは
一般に Windows の設定は既定で同期されますが、デバイスにインストールされているアプリケーションの一覧など、バックアップにのみ対応している設定もあります。 バックアップはモバイル デバイス向けのみで、Enterprise State Roaming ユーザーは現在使用できません。 バックアップには Microsoft アカウントが使用され、OneDrive に設定とアプリケーション データが保存されます。 ユーザーが設定アプリを使ってデバイス上の同期を無効にした場合、通常であれば同期されるアプリケーション データがバックアップのみになります。 バックアップ データは、新しいデバイスの初回実行時に復元操作を通じてのみアクセスできます。 バックアップは、デバイスの設定で無効にできるほか、ユーザーの OneDrive アカウントを通じて管理したり削除したりすることができます。

## <a name="windows-settings-overview"></a>Windows 設定の概要
Windows 10 デバイス上の設定に対する同期操作は、エンド ユーザーが次の設定グループを使って有効/無効にすることができます。

* テーマ: デスクトップの背景、ユーザー タイル、タスク バーの位置など 
* Internet Explorer の設定: 閲覧の履歴、入力された URL、お気に入りなど 
* パスワード: [Windows 資格情報保管ボックス](https://technet.microsoft.com/library/jj554668.aspx)(Wi-Fi プロファイルを含む) 
* 言語設定: スペル チェック辞書、システムの言語設定 
* コンピューターの簡単操作: ナレーター、スクリーン キーボード、拡大鏡 
* その他の Windows 設定: 「Windows 設定の詳細」を参照

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-individual-sync-settings.png)

Edge ブラウザーの設定 (お気に入り、リーディング リスト) の同期は [Edge ブラウザーの設定] メニュー オプションからエンドユーザーが行うことができます。

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-sync-content.png)

## <a name="windows-settings-details"></a>Windows 設定の詳細
以下の表で "設定" の "グループ" 列に記載した "その他" という項目は、[設定]、[アカウント]、[設定の同期]、[その他の Windows の設定] の順にアクセスして無効にできる設定を表します。 

「設定」の「グループ」列に "内部" と書かれている設定とアプリは、アプリ本体の同期機能から無効にするか、モバイル デバイス管理 (MDM) またはグループ ポリシーの設定を使いデバイス全体の同期を無効にすることによってのみ無効にすることができます。
所属グループの記載がない設定は、ローミングも同期もされない設定です。

| Settings | デスクトップ | モバイル | グループ |
| --- | --- | --- | --- |
| **アカウント**: アカウントの画像 |sync |○ |テーマ |
| **アカウント**: その他のアカウントの設定 |○ |○ | |
| **高度なモバイル ブロードバンド**: インターネット接続の共有のネットワーク名 (Bluetooth を介したモバイル Wi-Fi ホットスポットの自動検出に必要) |○ |○ |パスワード |
| **アプリ データ**: 個々のアプリでデータを同期可能 |同期バックアップ |同期バックアップ |内部 |
| **アプリの一覧**: インストールされているアプリの一覧 |○ |backup |その他 |
| **Bluetooth**: Bluetooth のすべての設定 |○ |○ | |
| **コマンド プロンプト**: コマンド プロンプトの "既定" 設定 |sync |○ | |
| **資格情報**: 資格情報保管ボックス |sync |sync |パスワード |
| **日付、時刻、地域**: 時刻の自動同期 (インターネット時刻同期) |sync |sync |言語 |
| **日付、時刻、地域**: 24 時間形式 |sync |○ |言語 |
| **日付、時刻、地域**: 日付と時刻 |sync |○ |言語 |
| **日付、時刻、地域**: タイム ゾーン | |○ |言語 |
| **日付、時刻、地域**: 夏時間 |sync |○ |言語 |
| **日付、時刻、地域**: 国/地域 |sync |○ |言語 |
| **日付、時刻、地域**: 週の最初の曜日 |sync |○ |言語 |
| **日付、時刻、地域**: 地域設定 (ロケール) |sync |○ |言語 |
| **日付、時刻、地域**: 日付 (短い形式) |sync |○ |言語 |
| **日付、時刻、地域**: 日付 (長い形式) |sync |○ |言語 |
| **日付、時刻、地域**: 時刻 (短い形式) |sync |○ |言語 |
| **日付、時刻、地域**: 時刻 (長い形式) |sync |○ |言語 |
| **デスクトップの個人設定**: デスクトップ テーマ (背景、システム カラー、既定のシステム音、スクリーン セーバー) |sync |○ |テーマ |
| **デスクトップの個人設定**: スライドショーの壁紙 |sync |○ |テーマ |
| **デスクトップの個人設定**: タスク バーの設定 (位置、自動的に隠す、など) |sync |○ |テーマ |
| **デスクトップの個人設定**: スタート画面のレイアウト |○ |backup | |
| **デバイス**: 接続済みの共有プリンター |○ |○ |その他 |
| **Edge ブラウザー**: リーディング リスト |sync |sync |内部 |
| **Edge ブラウザー**: お気に入り |sync |sync |内部 |
| **Edge ブラウザー**: Edge におけるその他すべての設定 |○ |○ | |
| **ハイ コントラスト**: オン/オフ |sync |○ |簡単操作 |
| **ハイ コントラスト**: テーマの設定 |sync |○ |簡単操作 |
| **Internet Explorer**: 開いているタブ (URL とタイトル) |sync |sync |Internet Explorer |
| **Internet Explorer**: リーディング リスト |sync |sync |Internet Explorer |
| **Internet Explorer**: 入力された URL |sync |sync |Internet Explorer |
| **Internet Explorer**: 閲覧の履歴 |sync |sync |Internet Explorer |
| **Internet Explorer**: お気に入り |sync |sync |Internet Explorer |
| **Internet Explorer**: 除外された URL |sync |sync |Internet Explorer |
| **Internet Explorer**: ホーム ページ |sync |sync |Internet Explorer |
| **Internet Explorer**: ドメイン候補 |sync |sync |Internet Explorer |
| **キーボード**: スクリーン キーボードのオン/オフをユーザーが切り替え可能 |sync |○ |簡単操作 |
| **キーボード**: 固定キー機能を有効にする (既定ではオフ) |sync |○ |簡単操作 |
| **キーボード**: フィルター キー機能を有効にする (既定ではオフ) |sync |○ |簡単操作 |
| **キーボード**: 切り替えキー機能を有効にする (既定ではオフ) |sync |○ |簡単操作 |
| **Internet Explorer**: ドメイン言語: 中国語 (CHS) QWERTY - 自己学習を有効にする |sync |○ |言語 |
| **言語**: CHS QWERTY - Dynamic candidate ranking を有効にする |sync |○ |言語 |
| **言語**: CHS QWERTY - 文字セット簡体字中国語 |sync |○ |言語 |
| **言語**: CHS QWERTY - 文字セット繁体字中国語 |sync |○ |言語 |
| **言語**: CHS QWERTY - Fuzzy Pinyin |sync |backup |言語 |
| **言語**: CHS QWERTY - Fuzzy Pairs |sync |backup |言語 |
| **言語**: CHS QWERTY - Full Pinyin |sync |○ |言語 |
| **言語**: CHS QWERTY - Double Pinyin |sync |○ |言語 |
| **言語**: CHS QWERTY - 読みの自動修正 |sync |○ |言語 |
| **言語**: CHS QWERTY - C/E 切り替えキー、Shift |sync |○ |言語 |
| **言語**: CHS QWERTY - C/E 切り替えキー、Ctrl |sync |○ |言語 |
| **言語**: CHS WUBI -&1; 文字入力モード |sync |○ |言語 |
| **言語**: CHS WUBI - candidate の残りのコーディングを表示 |sync |○ |言語 |
| **言語**: CHS WUBI - 4 コーディングが無効な場合に音を鳴らす |sync |○ |言語 |
| **言語**: CHT Bopomofo - CJK 統合漢字拡張 A を含める |sync |○ |言語 |
| **言語**: 日本語 IME - 予測入力とカスタムの単語 |sync |sync |言語 |
| **言語**: 韓国語 (KOR) IME |○ |○ |言語 |
| **言語**: 手書き認識 |○ |○ |言語 |
| **言語**: 言語プロファイル |sync |backup |言語 |
| **言語**: スペル チェック - スペルミスの自動修正と強調表示 |sync |backup |言語 |
| **言語**: キーボードの一覧 |sync |backup |言語 |
| **ロック画面**: すべてのロック画面設定 |○ |○ | |
| **拡大鏡**: オン/オフ (マスター設定の切り替え) |○ |○ |簡単操作 |
| **拡大鏡**: 色反転のオン/オフ (既定ではオフ) |sync |○ |簡単操作 |
| **拡大鏡**: 追跡 - キーボード フォーカスを拡大する |sync |○ |簡単操作 |
| **拡大鏡**: 追跡 - マウス カーソルの動きを追う |sync |○ |簡単操作 |
| **拡大鏡**: ユーザーがサインインしたときに起動 (既定ではオフ) |sync |○ |簡単操作 |
| **マウス**: マウス カーソルのサイズ変更 |sync |○ |その他 |
| **マウス**: マウス カーソルの色の変更 |sync |○ |その他 |
| **マウス**: その他すべての設定 |○ |○ | |
| **ナレーター**: クイック起動 |sync |○ |簡単操作 |
| **ナレーター**: ナレーターの読み上げ速度をユーザーが変更可能 |sync |○ |簡単操作 |
| **ナレーター**: 一般的な操作項目に関するヒントの読み上げのオン/オフをユーザーが切り替え可能 (既定ではオン) |sync |○ |簡単操作 |
| **ナレーター**: 入力した文字の読み上げのオン/オフをユーザーが切り替え可能 (既定ではオン) |sync |○ |簡単操作 |
| **ナレーター**: 入力した文字の単語の読み上げのオン/オフをユーザーが切り替え可能 (既定ではオン) |sync |○ |簡単操作 |
| **ナレーター**: 挿入カーソルがナレーターを追うようにする (既定ではオン) |sync |○ |簡単操作 |
| **ナレーター**: ナレーター カーソルの視覚的な強調表示を有効にする (既定ではオン) |sync |○ |簡単操作 |
| **ナレーター**: オーディオ キューの再生 (既定ではオン) |sync |○ |簡単操作 |
| **ナレーター**: タッチ キーボードから指を離したときにキーを有効にする (既定ではオフ) |sync |○ |簡単操作 |
| **簡単操作**: 点滅カーソルの太さを設定 |sync |○ |簡単操作 |
| **簡単操作**: 背景のイメージを削除 (既定ではオフ) |sync |○ |簡単操作 |
| **電源とスリープ**: すべての設定 |○ |○ | |
| **スタート画面の個人用設定**: アクセント カラー (電話のみ) |○ |sync |テーマ |
| **入力**: スペル チェック辞書 |sync |backup |言語 |
| **入力**: スペル ミスの語句を自動修正する |sync |backup |言語 |
| **入力**: スペル ミスの語句を強調表示する |sync |backup |言語 |
| **入力**: 入力時に入力ヒントを表示する |sync |backup |言語 |
| **入力**: 入力ヒントを選んだ後にスペースを追加する |sync |backup |言語 |
| **入力**: Space キーをダブルタップした後にピリオドを追加する |sync |backup |言語 |
| **入力**: 各文の最初の文字を大文字にする |sync |backup |言語 |
| **入力**: Shift キーをダブルタップしたときにすべて大文字の文字を使う |sync |backup |言語 |
| **入力**: 入力時にキー音を鳴らす |sync |backup |言語 |
| **入力**: タッチ キーボードの個人用設定データ |sync |backup |言語 |
| **Wi-Fi**: Wi-Fi プロファイル (WPA のみ) |sync |sync |パスワード |

## <a name="related-topics"></a>関連トピック
* [Enterprise State Roaming の概要](active-directory-windows-enterprise-state-roaming-overview.md)
* [Azure Active Directory の Enterprise State Roaming を有効にする](active-directory-windows-enterprise-state-roaming-enable.md)
* [設定とデータのローミングに関する FAQ](active-directory-windows-enterprise-state-roaming-faqs.md)
* [設定を同期させるためのグループ ポリシーと MDM の設定](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [トラブルシューティング](active-directory-windows-enterprise-state-roaming-troubleshooting.md)

