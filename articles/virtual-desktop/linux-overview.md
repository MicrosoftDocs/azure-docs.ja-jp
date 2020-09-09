---
title: Windows Virtual Desktop の Linux Support - Azure
description: Windows Virtual Desktop の Linux Support の簡単な概要。
author: Heidilohr
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: bd3cc6c5220e2e84cbbd30b29b8034f53c813f1e
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008697"
---
# <a name="linux-support"></a>Linux Support

Linux シン クライアント パートナーによって提供される以下のサポートされているクライアントを使用して、Linux デバイスから Windows Virtual Desktop リソースにアクセスできます。 Microsoft は、多くのパートナーと協力して、より多くの Linux ベースのオペレーティング システムとデバイスで、Windows Virtual Desktop クライアントがサポートされるようにしています。 ここに記載されていない Linux プラットフォームで Windows Virtual Desktop のサポートをご希望の場合は、[UserVoice ページ](https://remotedesktop.uservoice.com/forums/923035-remote-desktop-support-on-linux)から連絡してください。

## <a name="connect-with-your-linux-device"></a>Linux デバイスで接続する

Linux デバイス用 Windows Virtual Desktop クライアントは、次のパートナーによって承認されています。

|Partner|パートナー向けドキュメント|パートナー サポート|
|:------|:--------------------|:--------------|
|![IGEL ロゴ](./media/partners/igel.png)|[IGEL クライアントのドキュメント](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[IGEL サポート](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>Linux SDK とは

Linux のシン クライアント パートナーは、Windows Virtual Desktop の Linux SDK API を使用して、リソース フィードの取得、デスクトップまたはリモート アプリケーション セッションへの接続、ファースト パーティのクライアントがサポートする多くのリダイレクトの使用を行うことができます。 SDK は、Ubuntu 18.04 以降に基づくほとんどのオペレーティング システムと互換性があります。

### <a name="feature-support"></a>機能のサポート

SDK では、デスクトップおよびリモート アプリケーション セッションへの複数の接続がサポートされています。 次のリダイレクトがサポートされています。

| リダイレクト       | サポートされています |
| :---------------- | :-------: |
| [キーボード]          | &#10004;  |
| マウス             | &#10004;  |
| オーディオ入力          | &#10004;  |
| オーディオ出力         | &#10004;  |
| クリップボード (テキスト)  | &#10004;  |
| クリップボード (イメージ) | &#10004;  |
| クリップボード (ファイル)  | &#10004;  |
| スマート カード         | &#10004;  |
| ドライブまたはフォルダー      | &#10004;  |

この SDK では、セッション用に選択したモニターが隣接している限り、複数のモニター ディスプレイの構成もサポートされています。

Microsoft は、新しい機能とリダイレクトのサポートを追加するときに、このドキュメントを更新します。 新機能とその他の改善点を提案する場合は、[UserVoice ページ](https://go.microsoft.com/fwlink/?linkid=2116523)にアクセスしてください。

## <a name="next-steps"></a>次のステップ

次のクライアント用のドキュメントを確認してください。

- [Windows デスクトップ クライアント](connect-windows-7-10.md)
- [Web クライアント](connect-web.md)
- [Android クライアント](connect-android.md)
- [macOS クライアント](connect-macos.md)
- [iOS クライアント](connect-ios.md)
