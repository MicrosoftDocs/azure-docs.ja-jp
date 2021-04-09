---
title: Windows 10 ローミング設定リファレンス - Azure Active Directory
description: ESR 対応の Windows 10 でローミングまたはバックアップされる設定
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6048ee9237640799b7bec37083e607fc74ffb8e4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "85252969"
---
# <a name="windows-10-roaming-settings-reference"></a>Windows 10 ローミング設定リファレンス

Windows 10 でローミングまたはバックアップされる設定の一覧を次に示します。 

## <a name="devices-and-endpoints"></a>デバイスとエンドポイント

次の表は、Windows 10 における同期、バックアップ、復元のフレームワークでサポートされるデバイスとアカウントの種類をまとめたものです。

| アカウントの種類と操作 | デスクトップ | モバイル |
| --- | --- | --- |
| Azure Active Directory: 同期 |はい |いいえ |
| Azure Active Directory: バックアップ/復元 |いいえ |いいえ |
| Microsoft アカウント: 同期 |はい |はい |
| Microsoft アカウント: バックアップ/復元 |いいえ |はい |

## <a name="what-is-backup"></a>バックアップとは

一般に Windows の設定は既定で同期されますが、デバイスにインストールされているアプリケーションの一覧など、バックアップにのみ対応している設定もあります。 バックアップはモバイル デバイス向けのみで、Enterprise State Roaming ユーザーは現在使用できません。 バックアップには Microsoft アカウントが使用され、OneDrive に設定とアプリケーション データが保存されます。 ユーザーが設定アプリを使ってデバイス上の同期を無効にした場合、通常であれば同期されるアプリケーション データがバックアップのみになります。 バックアップ データは、新しいデバイスの初回実行時に復元操作を通じてのみアクセスできます。 バックアップは、デバイスの設定で無効にできるほか、ユーザーの OneDrive アカウントを通じて管理したり削除したりすることができます。

## <a name="windows-settings-overview"></a>Windows 設定の概要

エンドユーザーは、次の設定グループを使用して、Windows 10 デバイスでの設定の同期を有効または無効にすることができます。

* テーマ: デスクトップの背景、ユーザー タイル、タスク バーの位置など 
* Internet Explorer の設定: 閲覧の履歴、入力された URL、お気に入りなど 
* パスワード: Windows 資格情報マネージャー (Wi-Fi プロファイルを含む) 
* 言語設定: スペル チェック辞書、システムの言語設定 
* コンピューターの簡単操作: ナレーター、スクリーン キーボード、拡大鏡 
* その他の Windows 設定: 「Windows 設定の詳細」を参照
* Microsoft Edge ブラウザーの設定: Microsoft Edge のお気に入り、リーディング リスト、その他の設定

![設定の同期](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-syncyoursettings.png)

> [!NOTE]
> この記事は、2015年7月に Windows 10 で起動された Microsoft Edge レガシ HTML ベースのブラウザーに適用されます。 この記事は、2020 年 1 月 15 日にリリースされた新しい Microsoft Edge Chromium ベースのブラウザーには適用されません。 新しい Microsoft Edge の同期動作の詳細については、「[Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync)」を参照してください。

Microsoft Edge ブラウザーの設定グループ (お気に入り、リーディング リスト) の同期は、Microsoft Edge ブラウザーの [設定] メニュー オプションを通じてエンド ユーザーが有効/無効にできます。

![Account](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-edge.png)

Windows 10 バージョン 1803 またはそれ以降では、Internet Explorer の設定グループ (お気に入り、入力された URL) の同期を、Internet Explorer の [設定] メニュー オプションを使ってエンドユーザーが有効または無効にすることができます。 

![設定](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-ie.png)

## <a name="windows-settings-details"></a>Windows 設定の詳細

以下の表で "設定グループ" 列の "その他" という項目は、[設定]、[アカウント]、[設定の同期]、[その他の Windows の設定] の順にアクセスして無効にできる設定を表します。 

「設定」の「グループ」列に "内部" と書かれている設定とアプリは、アプリ本体の同期機能から無効にするか、モバイル デバイス管理 (MDM) またはグループ ポリシーの設定を使いデバイス全体の同期を無効にすることによってのみ無効にすることができます。
所属グループの記載がない設定は、ローミングも同期もされない設定です。

| 設定 | デスクトップ | モバイル | グループ |
| --- | --- | --- | --- |
| **アカウント**: アカウントの画像 |sync |X |テーマ |
| **アカウント**: その他のアカウントの設定 |X |X | |
| **高度なモバイル ブロードバンド**: インターネット接続の共有のネットワーク名 (Bluetooth 経由のモバイル Wi-Fi ホットスポットの自動検出に必要) |X |X |パスワード |
| **アプリ データ**: 個々のアプリでデータを同期可能 |同期バックアップ |同期バックアップ |internal |
| **アプリの一覧**: インストールされているアプリの一覧 |X |バックアップ (backup) |その他 |
| **Bluetooth**: Bluetooth のすべての設定 |X |X | |
| **コマンド プロンプト**: コマンド プロンプトの "既定" 設定 |sync |X |internal |
| **資格情報**: 資格情報保管ボックス |sync |sync |password |
| **日付、時刻、地域**: 時刻の自動同期 (インターネット時刻同期) |sync |sync |language |
| **日付、時刻、地域**: 24 時間形式 |sync |X |language |
| **日付、時刻、地域**: 日付と時刻 |sync |X |language |
| **日付、時刻、地域**: タイム ゾーン | |X |language |
| **日付、時刻、地域**: 夏時間 |sync |X |language |
| **日付、時刻、地域**: 国/地域 |sync |X |language |
| **日付、時刻、地域**: 週の最初の曜日 |sync |X |language |
| **日付、時刻、地域**: 地域設定 (ロケール) |sync |X |language |
| **日付、時刻、地域**: 日付 (短い形式) |sync |X |language |
| **日付、時刻、地域**: 日付 (長い形式) |sync |X |language |
| **日付、時刻、地域**: 時刻 (短い形式) |sync |X |language |
| **日付、時刻、地域**: 時刻 (長い形式) |sync |X |language |
| **デスクトップの個人設定**: デスクトップ テーマ (背景、システム カラー、既定のシステム音、スクリーン セーバー) |sync |X |テーマ |
| **デスクトップの個人設定**: スライドショーの壁紙 |sync |X |テーマ |
| **デスクトップの個人設定**: タスク バーの設定 (位置、自動的に隠す、など) |sync |X |テーマ |
| **デスクトップの個人設定**: スタート画面のレイアウト |X |バックアップ (backup) | |
| **デバイス**: 接続済みの共有プリンター |X |X |その他 |
| **Microsoft Edge ブラウザー**: リーディング リスト |sync |sync |internal |
| **Microsoft Edge ブラウザー**: お気に入り |sync |sync |internal |
| **Microsoft Edge ブラウザー**: サイトのトップ <sup>[[1]](#footnote-1)</sup> |sync |sync |internal |
| **Microsoft Edge ブラウザー**: 入力されたURL <sup>[[1]](#footnote-1)</sup> |sync |sync |internal |
| **Microsoft Edge ブラウザー**: お気に入りバーの設定 <sup>[[1]](#footnote-1)</sup> |sync |sync |internal |
| **Microsoft Edge ブラウザー**: [ホーム] ボタンを表示する <sup>[[1]](#footnote-1)</sup> |sync |sync |internal |
| **Microsoft Edge ブラウザー**: ポップアップをブロックする <sup>[[1]](#footnote-1)</sup> |sync |sync |internal |
| **Microsoft Edge ブラウザー**: ダウンロード時の動作を毎回確認する <sup>[[1]](#footnote-1)</sup> |sync |sync |internal |
| **Microsoft Edge ブラウザー**: パスワードを保存する <sup>[[1]](#footnote-1)</sup> |sync |sync |internal |
| **Microsoft Edge ブラウザー**: トラッキング拒否要求を送信する <sup>[[1]](#footnote-1)</sup> |sync |sync |internal |
| **Microsoft Edge ブラウザー**: フォームへの入力を保存する <sup>[[1]](#footnote-1)</sup> |sync |sync |internal |
| **Microsoft Edge ブラウザー**: 入力時に検索候補とおすすめサイトを表示する <sup>[[1]](#footnote-1)</sup> |sync |sync |internal |
| **Microsoft Edge ブラウザー**: Cookie の基本設定 <sup>[[1]](#footnote-1)</sup> |sync |sync |internal |
| **Microsoft Edge ブラウザー**: サイトでデバイスに保護されたメディア ライセンスを保存できるようにする <sup>[[1]](#footnote-1)</sup> |sync |sync |internal |
| **Microsoft Edge ブラウザー**: スクリーン リーダー設定 <sup>[[1]](#footnote-1)</sup> |sync |sync |internal |
| **ハイ コントラスト**: オン/オフ |sync |X |簡単操作 |
| **ハイ コントラスト**: テーマの設定 |sync |X |簡単操作 |
| **Internet Explorer**: 開いているタブ (URL とタイトル) |sync |sync |Internet Explorer |
| **Internet Explorer**: リーディング リスト |sync |sync |Internet Explorer |
| **Internet Explorer**: 入力された URL |sync |sync |Internet Explorer |
| **Internet Explorer**: 閲覧の履歴 |sync |sync |Internet Explorer |
| **Internet Explorer**: お気に入り |sync |sync |Internet Explorer |
| **Internet Explorer**: 除外された URL |sync |sync |Internet Explorer |
| **Internet Explorer**: ホーム ページ |sync |sync |Internet Explorer |
| **Internet Explorer**: ドメイン候補 |sync |sync |Internet Explorer |
| **キーボード**: スクリーン キーボードのオン/オフをユーザーが切り替え可能 |sync |X |簡単操作 |
| **キーボード**: 固定キー機能を有効にする (既定ではオフ) |sync |X |簡単操作 |
| **キーボード**: フィルター キー機能を有効にする (既定ではオフ) |sync |X |簡単操作 |
| **キーボード**: 切り替えキー機能を有効にする (既定ではオフ) |sync |X |簡単操作 |
| **Internet Explorer**: ドメイン言語: 中国語 (CHS) QWERTY - 自己学習を有効にする |sync |X |Language |
| **言語**: CHS QWERTY - Dynamic candidate ranking を有効にする |sync |X |Language |
| **言語**: CHS QWERTY - 文字セット簡体中国語 |sync |X |Language |
| **言語**: CHS QWERTY - 文字セット繁体字中国語 |sync |X |Language |
| **言語**: CHS QWERTY - Fuzzy Pinyin |sync |バックアップ (backup) |Language |
| **言語**: CHS QWERTY - Fuzzy Pairs |sync |バックアップ (backup) |Language |
| **言語**: CHS QWERTY - Full Pinyin |sync |X |Language |
| **言語**: CHS QWERTY - Double Pinyin |sync |X |Language |
| **言語**: CHS QWERTY - 読みの自動修正 |sync |X |Language |
| **言語**: CHS QWERTY - C/E 切り替えキー、Shift |sync |X |Language |
| **言語**: CHS QWERTY - C/E 切り替えキー、Ctrl |sync |X |Language |
| **言語**: CHS WUBI - 1 文字入力モード |sync |X |Language |
| **言語**: CHS WUBI - candidate の残りのコーディングを表示 |sync |X |Language |
| **言語**: CHS WUBI - 4 コーディングが無効な場合に音を鳴らす |sync |X |Language |
| **言語**: CHT Bopomofo - CJK 統合漢字拡張 A を含める |sync |X |Language |
| **言語**: 日本語 IME - 予測入力とカスタムの単語 |sync |sync |Language |
| **言語**: 韓国語 (KOR) IME |X |X |Language |
| **言語**: 手書き認識 |X |X |Language |
| **言語**: 言語プロファイル |sync |バックアップ (backup) |Language |
| **言語**: スペル チェック - スペルミスの自動修正と強調表示 |sync |バックアップ (backup) |Language |
| **言語**: キーボードの一覧 |sync |バックアップ (backup) |Language |
| **ロック画面**: すべてのロック画面設定 |X |X | |
| **拡大鏡**: オン/オフ (マスター設定の切り替え) |X |X |簡単操作 |
| **拡大鏡**: 色反転のオン/オフ (既定ではオフ) |sync |X |簡単操作 |
| **拡大鏡**: 追跡 - キーボード フォーカスを拡大する |sync |X |簡単操作 |
| **拡大鏡**: 追跡 - マウス カーソルの動きを追う |sync |X |簡単操作 |
| **拡大鏡**: ユーザーがサインインしたときに起動 (既定ではオフ) |sync |X |簡単操作 |
| **マウス**: マウス カーソルのサイズ変更 |sync |X |その他 |
| **マウス**: マウス カーソルの色の変更 |sync |X |その他 |
| **マウス**: その他すべての設定 |X |X | |
| **ナレーター**: クイック起動 |sync |X |簡単操作 |
| **ナレーター**: ナレーターの読み上げ速度をユーザーが変更可能 |sync |X |簡単操作 |
| **ナレーター**: 一般的な操作項目に関するヒントの読み上げのオン/オフをユーザーが切り替え可能 (既定ではオン) |sync |X |簡単操作 |
| **ナレーター**: 入力した文字の読み上げのオン/オフをユーザーが切り替え可能 (既定ではオン) |sync |X |簡単操作 |
| **ナレーター**: 入力した文字の単語の読み上げのオン/オフをユーザーが切り替え可能 (既定ではオン) |sync |X |簡単操作 |
| **ナレーター**: 挿入カーソルがナレーターを追うようにする (既定ではオン) |sync |X |簡単操作 |
| **ナレーター**: ナレーター カーソルの視覚的な強調表示を有効にする (既定ではオン) |sync |X |簡単操作 |
| **ナレーター**: オーディオ キューの再生 (既定ではオン) |sync |X |簡単操作 |
| **ナレーター**: タッチ キーボードから指を離したときにキーを有効にする (既定ではオフ) |sync |X |簡単操作 |
| **簡単操作**: 点滅カーソルの太さを設定 |sync |X |簡単操作 |
| **簡単操作**: 背景のイメージを削除 (既定ではオフ) |sync |X |簡単操作 |
| **電源とスリープ**: すべての設定 |X |X | |
| **スタート画面の個人用設定**: アクセント カラー (電話のみ) |X |sync |テーマ |
| **入力**: スペル チェック辞書 |sync |バックアップ (backup) |Language |
| **入力**: スペル ミスの語句を自動修正する |sync |バックアップ (backup) |Language |
| **入力**: スペル ミスの語句を強調表示する |sync |バックアップ (backup) |Language |
| **入力**: 入力時に入力ヒントを表示する |sync |バックアップ (backup) |Language |
| **入力**: 入力ヒントを選んだ後にスペースを追加する |sync |バックアップ (backup) |Language |
| **入力**: Space キーをダブルタップした後にピリオドを追加する |sync |バックアップ (backup) |Language |
| **入力**: 各文の最初の文字を大文字にする |sync |バックアップ (backup) |Language |
| **入力**: Shift キーをダブルタップしたときにすべて大文字の文字を使う |sync |バックアップ (backup) |Language |
| **入力**: 入力時にキー音を鳴らす |sync |バックアップ (backup) |Language |
| **入力**: タッチ キーボードの個人用設定データ |sync |バックアップ (backup) |Language |
| **Wi-Fi**: Wi-Fi プロファイル (WPA のみ) |sync |sync |パスワード |

###### <a name="footnote-1"></a>脚注 1

サポートされる Windows Creators Update の最小 OS バージョン (ビルド 15063)。 

## <a name="next-steps"></a>次のステップ

概要については、[Enterprise State Roaming の概要](enterprise-state-roaming-overview.md)に関するページを参照してください。
