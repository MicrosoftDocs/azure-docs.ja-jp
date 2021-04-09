---
title: FIDO2 パスワードレス認証のブラウザー サポート | Azure Active Directory
description: ブラウザーとオペレーティング システムの組み合わせにより、Azure Active Directory を使用したアプリの FIDO2 パスワードレス認証がサポートされます。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: nichola
author: knicholasa
manager: martinco
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f90edd5729ff5229be09bc3798082c33bdeead2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632103"
---
# <a name="browser-support-of-fido2-passwordless-authentication"></a>FIDO2 パスワードレス認証のブラウザー サポート

Azure Active Directory では、[FIDO2 セキュリティ キー](./concept-authentication-passwordless.md#fido2-security-keys)をパスワードレス デバイスとして使用できます。 Microsoft アカウントでの FIDO2 認証のサポートは、[2018 年に発表されました](https://techcommunity.microsoft.com/t5/identity-standards-blog/all-about-fido2-ctap2-and-webauthn/ba-p/288910)。 この発表で説明されているように、Microsoft および Azure Active Directory の各アカウントでのセキュリティで保護された認証をサポートするためには、特定のオプション機能と、FIDO2 CTAP 仕様への拡張機能を実装する必要があります。 次の図は、Azure Active Directory で FIDO2 認証キーを使用したパスワードレス認証をサポートしているブラウザーとオペレーティング システムの組み合わせを示しています。

## <a name="supported-browsers"></a>サポートされているブラウザー

この表は、Azure Active Directory (Azure AD) および Microsoft アカウント (MSA) の認証のサポートを示しています。 Microsoft アカウントは、Xbox、Skype、Outlook.com などのサービスのコンシューマーによって作成されます。 サポートされているデバイスの種類には、**USB**、近距離無線通信 (**NFC**)、Bluetooth Low Energy (**BLE**) などがあります。

| OS | Chrome | Chrome  | Chrome | Edge | Edge | Edge | Firefox | Firefox | Firefox |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| | USB | NFC | BLE | USB | NFC | BLE | USB | NFC | BLE |
| **Windows**  | ![Chrome では、AAD アカウント用に Windows で USB がサポートされます。][y] | ![Chrome では、AAD アカウント用に Windows で NFC がサポートされます。][y] | ![Chrome では、AAD アカウント用に Windows で BLE がサポートされます。][y] | ![Edge では、AAD アカウント用に Windows で USB がサポートされます。][y] | ![Edge では、AAD アカウント用に Windows で NFC がサポートされます。][y] | ![Edge では、AAD アカウント用に Windows で BLE がサポートされます。][y] | ![Firefox では、AAD アカウント用に Windows で USB がサポートされます。][y] | ![Firefox では、AAD アカウント用に Windows で NFC がサポートされます。][y] | ![Firefox では、AAD アカウント用に Windows で BLE がサポートされます。][y] |
| **macOS**  | ![Chrome では、AAD アカウント用に macOS で USB がサポートされます。][y] | ![Chrome では、AAD アカウント用に macOS で NFC はサポートされません。][n] | ![Chrome では、AAD アカウント用に macOS で BLE はサポートされません。][n] | ![Edge では、AAD アカウント用に macOS で USB がサポートされます。][y] | ![Edge では、AAD アカウント用に macOS で NFC はサポートされません。][n] | ![Edge では、AAD アカウント用に macOS で BLE はサポートされません。][n] | ![Firefox では、AAD アカウント用に macOS で USB はサポートされません。][n] | ![Firefox では、AAD アカウント用に macOS で NFC はサポートされません。][n] | ![Firefox では、AAD アカウント用に macOS で BLE はサポートされません。][n] |
| **Linux**  | ![Chrome では、AAD アカウント用に Linux で USB がサポートされます。][y] | ![Chrome では、AAD アカウント用に Linux で NFC はサポートされません。][n] | ![Chrome では、AAD アカウント用に Linux で BLE はサポートされません。][n] | ![Edge では、AAD アカウント用に Linux で USB はサポートされません。][n] | ![Edge では、AAD アカウント用に Linux で NFC はサポートされません。][n] | ![Edge では、AAD アカウント用に Linux で BLE はサポートされません。][n] | ![Firefox では、AAD アカウント用に Linux で USB はサポートされません。][n] | ![Firefox では、AAD アカウント用に Linux で NFC はサポートされません。][n] | ![Firefox では、AAD アカウント用に Linux で BLE はサポートされません。][n] |



## <a name="unsupported-browsers"></a>サポートされていないブラウザー

次のオペレーティング システムとブラウザーの組み合わせはサポートされていませんが、将来のサポートとテストは調査中です。 その他のオペレーティング システムとブラウザーのサポートをご希望の場合は、このページの下部にある製品フィードバック ツールを使用してフィードバックをお寄せください。

| オペレーティング システム | Browser |
| ---- | ---- |
| iOS | Safari、Brave |
| macOS | Safari |
| Android | Chrome |
| ChromeOS | Chrome |

## <a name="minimum-browser-version"></a>ブラウザーの最小バージョン

ブラウザーの最小バージョン要件は次のとおりです。 

| Browser | 最小バージョン |
| ---- | ---- |
| Chrome | 76 |
| Edge | Windows 10 バージョン 1903<sup>1</sup> |
| Firefox | 66 |

<sup>1</sup>Chromium ベースの新しい Microsoft Edge では、すべてのバージョンが FIDO2 をサポートしています。 Microsoft Edge レガシでのサポートは 1903 で追加されました。

## <a name="next-steps"></a>次のステップ
[パスワードなしのセキュリティ キー サインインを有効にする (プレビュー)](./howto-authentication-passwordless-security-key.md)

<!--Image references-->
[y]: ./media/fido2-compatibility/yes.png
[n]: ./media/fido2-compatibility/no.png
