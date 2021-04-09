---
title: デバイスのリダイレクトを構成する - Azure
description: Windows Virtual Desktop でのデバイスのリダイレクトを構成する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 09/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b60a6e53e8fecd71885204690231776ff69fc08f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95018377"
---
# <a name="configure-device-redirections"></a>デバイスのリダイレクトを構成する

Windows Virtual Desktop 環境でのデバイスのリダイレクトを構成すると、プリンター、USB デバイス、マイク、およびその他の周辺機器をリモート セッションで使用できます。 デバイスのリダイレクトによっては、リモート デスクトップ プロトコル (RDP) のプロパティとグループ ポリシー設定の両方に対する変更が必要になる場合があります。

## <a name="supported-device-redirections"></a>サポートされるデバイスのリダイレクト

クライアントごとに異なるデバイス リダイレクトがサポートされます。 各クライアントに対してサポートされるデバイスのリダイレクトの完全な一覧については、「[クライアントの比較](/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare)」をご覧ください。

## <a name="customizing-rdp-properties-for-a-host-pool"></a>ホスト プール用の RDP プロパティのカスタマイズ

PowerShell または Azure portal を使用してホスト プール用の RDP プロパティをカスタマイズする方法の詳細については、「[RDP プロパティ](customize-rdp-properties.md)」を参照してください。 サポートされる RDP プロパティの完全な一覧については、[サポートされる RDP ファイルの設定](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext)に関する記事を参照してください。

## <a name="setup-device-redirections"></a>デバイスのリダイレクトを設定する

次の RDP プロパティとグループ ポリシー設定を使用して、デバイスのリダイレクトを構成できます。

### <a name="audio-input-microphone-redirection"></a>オーディオ入力 (マイク) のリダイレクト

オーディオ入力のリダイレクトを構成するには、次の RDP プロパティを設定します。

- `audiocapturemode:i:1`: オーディオ入力のリダイレクトを有効にします。
- `audiocapturemode:i:0`: オーディオ入力のリダイレクトを無効にします。

### <a name="audio-output-speaker-redirection"></a>オーディオ出力 (スピーカー) のリダイレクト

オーディオ出力のリダイレクトを構成するには、次の RDP プロパティを設定します。

- `audiomode:i:0`: オーディオ出力のリダイレクトを有効にします。
- `audiomode:i:1` または `audiomode:i:2`: オーディオ出力のリダイレクトを無効にします。

### <a name="camera-redirection"></a>カメラのリダイレクト

カメラのリダイレクトを構成するには、次の RDP プロパティを設定します。

- `camerastoredirect:s:*`: すべてのカメラをリダイレクトします。
- `camerastoredirect:s:`: カメラのリダイレクトを無効にします。

>[!NOTE]
>`camerastoredirect:s:` プロパティが無効になっている場合でも、ローカル カメラは `devicestoredirect:s:` プロパティを介してリダイレクトされる可能性があります。 カメラのリダイレクトを完全に無効にするには、`camerastoredirect:s:` を設定し、`devicestoredirect:s:` を設定するか、カメラを含まないプラグ アンド プレイ デバイスのサブセットを定義します。

KSCATEGORY_VIDEO_CAMERA インターフェイスのセミコロンで区切られた一覧 (`camerastoredirect:s:\?\usb#vid_0bda&pid_58b0&mi` など) を使用して、特定のカメラをリダイレクトすることもできます。 

### <a name="clipboard-redirection"></a>クリップボードのリダイレクト

クリップボードのリダイレクトを構成するには、次の RDP プロパティを設定します。

- `redirectclipboard:i:1`: クリップボードのリダイレクトを有効にします。
- `redirectclipboard:i:0`: クリップボードのリダイレクトを無効にします。

### <a name="com-port-redirections"></a>COM ポートのリダイレクト

COM ポートのリダイレクトを構成するには、次の RDP プロパティを設定します。

- `redirectcomports:i:1`: COM ポートのリダイレクトを有効にします。
- `redirectcomports:i:0`: COM ポートのリダイレクトを無効にします。

### <a name="usb-redirection"></a>USB リダイレクト

最初に、次の RDP プロパティを設定して、USB デバイスのリダイレクトを有効にします。

- `usbdevicestoredirect:s:*`: USB デバイスのリダイレクトを有効にします。
- `usbdevicestoredirect:s:`: USB デバイスのリダイレクトを無効にします。

次に、ユーザーのローカル デバイスに次のグループ ポリシーを設定します。

- **[コンピューターの構成]**  >  **[ポリシー]** >  **[管理用テンプレート]**  >  **[Windows コンポーネント]**  >  **[リモート デスクトップ サービス]**  >  **[リモート デスクトップ接続クライアント]**  >  **[RemoteFX USB デバイス リダイレクト]** に移動します。
- **[サポートされている他の RemoteFX USB デバイスのこのコンピューターからの RDP リダイレクトを許可する]** を選択します。
- **[有効]** オプションを選択し、 **[RemoteFX USB リダイレクト アクセス権の管理者とユーザー]** チェックボックスをオンにします。
- **[OK]** を選択します。

### <a name="plug-and-play-device-redirection"></a>プラグ アンド プレイ デバイスのリダイレクト

プラグ アンド プレイ デバイスのリダイレクトを構成するには、次の RDP プロパティを設定します。

- `devicestoredirect:s:*`: すべてのプラグ アンド プレイ デバイスのリダイレクトを有効にします。
- `devicestoredirect:s:`: プラグ アンド プレイ デバイスのリダイレクトを無効にします。

セミコロン区切りの一覧 (`devicestoredirect:s:root\*PNP0F08` など) を使用して、特定のプラグ アンド プレイ デバイスを選択することもできます。

### <a name="local-drive-redirection"></a>ローカル ドライブのリダイレクト

ローカル ドライブのリダイレクトを構成するには、次の RDP プロパティを設定します。

- `drivestoredirect:s:*`: すべてのディスク ドライブのリダイレクトを有効にします。
- `drivestoredirect:s:`: ローカル ドライブのリダイレクトを無効にします。

セミコロン区切りの一覧 (`drivestoredirect:s:C:;E:;` など) を使用して、特定のドライブを選択することもできます。

### <a name="printer-redirection"></a>プリンターのリダイレクト

プリンターのリダイレクトを構成するには、次の RDP プロパティを設定します。

- `redirectprinters:i:1`: プリンターのリダイレクトを有効にします。
- `redirectprinters:i:0`: プリンターのリダイレクトを無効にします。

### <a name="smart-card-redirection"></a>スマート カードのリダイレクト

スマート カードのリダイレクトを構成するには、次の RDP プロパティを設定します。

- `redirectsmartcards:i:1`: スマート カードのリダイレクトを有効にします。
- `redirectsmartcards:i:0`: スマート カードのリダイレクトを無効にします。