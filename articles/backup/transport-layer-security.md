---
title: Azure Backup でのトランスポート層セキュリティ
description: Azure Backup を有効にすることで、暗号化プロトコルであるトランスポート層セキュリティ (TLS) を使用して、ネットワーク経由で転送される際のデータのセキュリティを維持する方法について説明します。
ms.topic: conceptual
ms.date: 11/01/2020
ms.openlocfilehash: ba9c9d91f562f54695a0739908c8a409d14d5852
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96327119"
---
# <a name="transport-layer-security-in-azure-backup"></a>Azure Backup でのトランスポート層セキュリティ

トランスポート層セキュリティ (TLS) は、ネットワーク経由で転送される際のデータのセキュリティを維持する暗号化プロトコルです。 Azure Backup は、トランスポート層セキュリティを使用して、転送されるバックアップ データのプライバシーを保護します。 この記事では、TLS 1.2 プロトコルを有効にする手順について説明します。これにより、以前のバージョンよりセキュリティが強化されます。

## <a name="earlier-versions-of-windows"></a>以前のバージョンの Windows

お使いのコンピューターが以前のバージョンの Windows で実行されている場合は、下に示されている対応する更新プログラムをインストールし、サポート技術情報の記事に記載されているレジストリ変更を適用する必要があります。

|オペレーティング システム  |KB article |
|---------|---------|
|Windows Server 2008 SP2   |   <https://support.microsoft.com/help/4019276>      |
|Windows Server 2008 R2、Windows 7、Windows Server 2012   | <https://support.microsoft.com/help/3140245>         |

>[!NOTE]
>更新プログラムによって、必要なプロトコル コンポーネントがインストールされます。 インストール後、上のサポート技術情報の記事に記載されているレジストリ キーの変更を行って、必要なプロトコルを適切に有効にする必要があります。

## <a name="verify-windows-registry"></a>Windows レジストリの確認

### <a name="configuring-schannel-protocols"></a>SChannel プロトコルの構成

次のレジストリ キーを使用して、TLS 1.2 プロトコルが SChannel コンポーネント レベルで 有効になるようにします。

```reg
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "Enabled"=dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "DisabledByDefault"=dword:00000000
```

>[!NOTE]
>ここに示す値は、Windows Server 2012 R2 以降のバージョンでは既定で設定されています。 これらのバージョンの Windows では、レジストリ キーが存在しない場合、作成する必要はありません。

### <a name="configuring-net-framework"></a>.NET Framework の構成

次のレジストリ キーを使用して、強力な暗号化をサポートするように .NET Framework を構成します。 [.NET Framework の構成の詳細については、こちら](/dotnet/framework/network-programming/tls#configuring-schannel-protocols-in-the-windows-registry)を参照してください。

```reg
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001
```

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="why-enable-tls-12"></a>なぜ TLS 1.2 を有効にするのですか?

TLS 1.2 は、SSL 2.0、SSL 3.0、TLS 1.0、TLS 1.1 などの以前の暗号化プロトコルより安全性が向上しています。 Azure Backup サービスでは既に、TLS 1.2 が完全にサポートされています。

### <a name="what-determines-the-encryption-protocol-used"></a>使用される暗号化プロトコルは何によって決まりますか?

暗号化された会話を確立するために、クライアントとサーバーの両方でサポートされている最上位のプロトコル バージョンがネゴシエートされます。 TLS ハンドシェイク プロトコルの詳細については、「[TLS を使用してセキュリティで保護されたセッションを確立する](/windows/win32/secauthn/tls-handshake-protocol#establishing-a-secure-session-by-using-tls)」を参照してください。

### <a name="what-is-the-impact-of-not-enabling-tls-12"></a>TLS 1.2 を有効にしないと、どのような影響がありますか?

プロトコル ダウングレード攻撃に対するセキュリティを強化するため、Azure Backup では 1.2 より前のバージョンの TLS を段階的に無効化し始めています。 これは、レガシ プロトコルと暗号スイートの接続を許可しないようにする、サービス全体での長期的な移行の一環です。 Azure Backup のサービスとコンポーネントでは、TLS 1.2 が完全にサポートされています。 ただし、必要な更新プログラムのない Windows バージョンや特定のカスタマイズされた構成では、TLS 1.2 プロトコルを提供できない場合があります。 この場合、次のうち 1 つ以上の障害が発生する可能性がありますが、これらに限定されるものではありません。

- バックアップと復元の操作が失敗する場合があります。
- バックアップ コンポーネントの接続がエラー10054 (既存の接続がリモート ホストによって強制的に切断された) によって失敗します。
- Azure Backup に関連するサービスが、通常どおりに停止または開始されません。

## <a name="additional-resources"></a>その他のリソース

- [トランスポート層セキュリティ プロトコル](/windows/win32/secauthn/transport-layer-security-protocol)
- [展開されたオペレーティング システム全体で TLS 1.2 のサポートを確保する](/security/engineering/solving-tls1-problem#ensuring-support-for-tls-12-across-deployed-operating-systems)
- [.NET Framework でのトランスポート層セキュリティ (TLS) のベスト プラクティス](/dotnet/framework/network-programming/tls)