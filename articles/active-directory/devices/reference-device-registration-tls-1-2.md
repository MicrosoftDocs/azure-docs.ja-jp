---
title: TLS 1.2 の適用 - Azure Active Directory の登録サービスを有効にする
description: Azure AD Device Registration サービスに対する TLS 1.0 および 1.1 のサポートを削除する
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 07/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bb8c6c64e0a68f5176c4eb0c0177c5220394695
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89268759"
---
# <a name="enforce-tls-12-for-the-azure-ad-registration-service"></a>Azure AD の登録サービスに TLS 1.2 を適用する

Azure Active Directory (Azure AD) Device Registration サービスは、デバイス ID を使用してデバイスをクラウドに接続するために使用されます。 Azure AD Device Registration サービスでは、現在、Azure との通信にはトランスポート層セキュリティ (TLS) 1.2 の使用がサポートされています。 セキュリティとクラス最高の暗号化を確保するには、TLS 1.0 および 1.1 を無効にすることが推奨されます。 このドキュメントでは、Azure AD Device Registration サービスとの間で登録を完了し、通信するために使用されるコンピューターで、TLS 1.2 が確実に使用されるようにする方法について説明します。

TLS プロトコル バージョン 1.2 は、セキュリティで保護された通信を提供するように設計された暗号化プロトコルです。 TLS プロトコルの主な目的は、プライバシーとデータの整合性を提供することです。 TLS のバージョン 1.2 では、[RFC 5246 (外部リンク)](https://tools.ietf.org/html/rfc5246) で定義されている多くの繰り返しが承認されています。

現在の接続の分析では、TLS 1.1 と1.0 の使用はほとんど表示されませんが、TLS 1.1 と 1.0 のサポートが終了する前に、必要に応じて影響を受けるクライアントまたはサーバーを更新できるように、Microsoft がこの情報を提供しようとしています。 ハイブリッド シナリオまたは Active Directory フェデレーションサービス (AD FS) のためにオンプレミスのインフラストラクチャを使用している場合は、そのインフラストラクチャで、TLS 1.2 を使用する受信接続と送信接続の両方をサポートできることを確認してください。

## <a name="update-windows-servers"></a>Windows サーバーを更新する

Azure AD Device Registration サービスを使用しているか、プロキシとして機能する Windows サーバーについては、以下の手順に従って、TLS 1.2 が有効になっていることを確認します。

> [!IMPORTANT]
> レジストリを更新したら、Windows サーバーを再起動して変更を有効にする必要があります。

### <a name="enable-tls-12"></a>TLS 1.2 を有効にする

以下のレジストリ文字列が、下記のように構成されていることを確認します。

- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client
  - "DisabledByDefault"=dword:00000000
  - "Enabled"=dword:00000001
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server
  - "DisabledByDefault"=dword:00000000
  - "Enabled"=dword:00000001
- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
  - "SchUseStrongCrypto"=dword:00000001

## <a name="update-non-windows-proxies"></a>Windows 以外のプロキシを更新する

デバイスと Azure AD Device Registration サービスとの間でプロキシとして機能するすべてのコンピューターで、TLS 1.2 が有効になっていることを確認する必要があります。 ベンダーのガイダンスに従ってサポートを確認してください。

## <a name="update-ad-fs-servers"></a>AD FS サーバーを更新する

Azure AD Device Registration サービスとの通信に使用されるすべての AD FS サーバーで、TLS 1.2 が有効になっていることを確認する必要があります。 この構成を有効にする方法や確認する方法については、「[AD FS の SSL/TLS プロトコルおよび暗号スイートの管理](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)」を参照してください。

## <a name="client-updates"></a>クライアント更新

クライアントとサーバー、およびブラウザーとサーバーのすべての組み合わせで、TLS 1.2 を使用して Azure AD Device Registration サービスと接続する必要があるため、これらのデバイスの更新が必要になる場合があります。

以下のクライアントは TLS 1.2 をサポートできないことが判明しています。 アクセスが中断されないようにクライアントを更新してください。

- Android バージョン 4.3 以前
- Firefox バージョン 5.0 以前
- Windows 7 以前での Internet Explorer バージョン 8 ～ 10
- Windows Phone 8.0 での Internet Explorer 10
- OS X 10.8.4 以前の Safari バージョン 6.0.4

## <a name="next-steps"></a>次のステップ

[TLS/SSL の概要 (Schannel SSP)](/windows-server/security/tls/tls-ssl-schannel-ssp-overview)