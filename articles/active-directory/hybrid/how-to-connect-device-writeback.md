---
title: Azure AD Connect:デバイス ライトバックの有効化 | Microsoft Docs
description: Azure AD Connect を使用するデバイスの書き戻しを有効にする方法について詳しく説明します
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: curtand
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 632f6f80184c6ba3409bd30ae070cbaefc77f036
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109500"
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect:デバイス ライトバックの有効化
> [!NOTE]
> デバイスの書き戻しには、Azure AD Premium に対するサブスクリプションが必要です。
> 
> 

ここでは、Azure AD Connect においてデバイスの書き戻し機能を有効にする方法について説明します。 デバイスの書き戻しは、次のシナリオで使用されます。

* [信頼証明書のハイブリッド展開を使用して Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust-prereqs#device-registration) を有効にします。
* ADFS (2012 R2 以降) で保護されたアプリケーション (証明書利用者の信頼) へのデバイスに基づく条件付きアクセスを有効にします。

これにより、セキュリティが強化され、アプリケーションへのアクセスが信頼されたデバイスに対してのみ許可されることが保証されます。 条件付きアクセスの詳細については、「[条件付きアクセス ポリシーを使用したリスクの管理](../active-directory-conditional-access-azure-portal.md)」および「[Azure Active Directory Device Registration を使用したオンプレミスの条件付きアクセスの設定](../../active-directory/active-directory-device-registration-on-premises-setup.md)」を参照してください。

> [!IMPORTANT]
> <li>デバイスは、ユーザーと同じフォレスト内にある必要があります。 デバイスは単一のフォレストに書き戻される必要があるため、この機能では現在、複数のユーザー フォレストでのデプロイはサポートされていません。</li>
> <li>オンプレミスの Active Directory フォレストに追加できるのは、1 つのデバイス登録構成オブジェクトのみです。 この機能は、オンプレミスの Active Directory が複数の Azure AD ディレクトリに同期されるトポロジと互換性がありません。</li>

## <a name="part-1-install-azure-ad-connect"></a>パート 1:Azure AD Connect のインストール
カスタム設定または簡単設定を使用して Azure AD Connect をインストールします。 すべてのユーザーとグループの同期に成功してから、デバイスの書き戻しを有効にすることをお勧めします。

## <a name="part-2-enable-device-writeback-in-azure-ad-connect"></a>パート 2:Azure AD Connect でのデバイス ライトバックの有効化
1. インストール ウィザードをもう一度実行します。 [追加のタスク] ページで **[デバイス オプションの構成]** を選び、 **[次へ]** をクリックします。 

    ![デバイス オプションの構成](./media/how-to-connect-device-writeback/deviceoptions.png)

    >[!NOTE]
    > 新しい [デバイス オプションの構成] は、バージョン 1.1.819.0 以降でのみ使うことができます。

2. デバイス オプション ページで、 **[デバイス ライトバックの構成]** を選びます。 **[デバイス ライトバックの無効化]** オプションは、デバイス ライトバックを有効にするまで使用できません。 **[次へ]** をクリックして、ウィザードの次のページに移動します。
    ![デバイスの操作の選択](./media/how-to-connect-device-writeback/configuredevicewriteback1.png)

3. [書き戻し] ページでは、指定したドメインが既定の [デバイスの書き戻しフォレスト] として表示されます。
   ![カスタム インストール デバイスの書き戻し先フォレスト](./media/how-to-connect-device-writeback/writebackforest.png)

4. **[デバイス コンテナー]** ページでは、使用可能な 2 つのオプションのどちらかを使って、Active Directory を準備できます。

    a. **エンタープライズ管理者の資格情報を提供する**: デバイスをライトバックする必要があるフォレストのエンタープライズ管理者の資格情報を提供すると、Azure AD Connect はデバイス ライトバックの構成の間にフォレストを自動的に準備します。

    b. **PowerShell スクリプトをダウンロードする**: Azure AD Connect は、デバイス ライトバック用に Active Directory を準備できる PowerShell スクリプトを自動生成します。 Azure AD Connect にエンタープライズ管理者の資格情報を提供できない場合、PowerShell スクリプトをダウンロードすることをお勧めします。 ダウンロードした PowerShell スクリプト **CreateDeviceContainer.psq** を、デバイスがライトバックされるフォレストのエンタープライズ管理者に提供します。
    ![Active Directory フォレストを準備する](./media/how-to-connect-device-writeback/devicecontainercreds.png)
    
    Active Directory フォレストを準備するため、次の操作が実行されます。
    * CN=Device Registration Configuration,CN=Services,CN=Configuration,[forest-dn] の下にコンテナーとオブジェクトが存在しない場合は、新しく作成されて構成されます。
    * CN=RegisteredDevices,[domain-dn] の下にコンテナーとオブジェクトが存在しない場合は、新しく作成されて構成されます。 このコンテナーにデバイス オブジェクトが作成されます。
    * Active Directory でデバイスを管理するために必要なアクセス許可を Azure AD コネクタ アカウントに設定します。
    * Azure AD Connect が複数のフォレストにインストールされている場合であっても、1 つのフォレストで実行すれば十分です。

## <a name="verify-devices-are-synchronized-to-active-directory"></a>デバイスが Active Directory に同期されていることを確認する
デバイスの書き戻しは正常に動作するようになっています。 デバイス オブジェクトを AD に書き戻すには、最大 3 時間かかる可能性があります。  デバイスが正しく同期されていることを確認するには、同期規則が完了した後で次のようにします。

1. Active Directory 管理センターを起動します。
2. フェデレーションされているドメイン内の RegisteredDevices を展開します。

   ![Active Directory 管理センター登録済みのデバイス](./media/how-to-connect-device-writeback/devicewriteback5.png)

3. 現在登録されているデバイスが一覧表示されます。

   ![Active Directory 管理センター登録済みのデバイス一覧](./media/how-to-connect-device-writeback/devicewriteback6.png)

## <a name="enable-conditional-access"></a>条件付きアクセスを有効にする
このシナリオを有効にする詳細な手順については、「 [Azure Active Directory Device Registration を使用したオンプレミスの条件付きアクセスの設定](../../active-directory/active-directory-device-registration-on-premises-setup.md)」をご覧ください。

## <a name="troubleshooting"></a>トラブルシューティング
### <a name="the-writeback-checkbox-is-still-disabled"></a>書き戻しのチェックボックスがオフのままです。
前述の手順を実行しても、デバイスの書き戻しのチェックボックスがオンにならない場合は、次の手順を実行し、ボックスがオンになる前にインストール ウィザードで検証が実行されるようにします。

まず次の手順を実行します。

* デバイスが存在するフォレストには、デバイス オブジェクトと関連属性が存在するように、Windows 2012 R2 レベルにアップグレードされたフォレスト スキーマが必要です。
* インストール ウィザードが既に実行中の場合、変更があっても検出されません。 この場合は、インストール ウィザードを完了してから、再実行してください。
* 初期化スクリプトで指定したアカウントが、Active Directory Connector に使用されている正しいユーザーであることを確認します。 確認する手順は次のとおりです。
  * [スタート] メニューから **[同期サービス]** を開きます。
  * **[コネクタ]** タブを開きます。
  * 種類が Active Directory Domain Services のコネクタを探して選択します。
  * **[アクション]** の **[プロパティ]** を選択します。
  * **[Active Directory フォレストに接続]** を選択します。 この画面で指定されているドメインとユーザー名と、スクリプトに指定したアカウントが一致することを確認します。
    ![Synchronization Service Manager のコネクタ アカウント](./media/how-to-connect-device-writeback/connectoraccount.png)

Active Directory の構成を確認します。

* つまり、configuration 名前付けコンテキストの (CN=DeviceRegistrationService,CN=Device Registration Services,CN=Device Registration Configuration,CN=Services,CN=Configuration) の下に Device Registration Service があることを確認します。

![トラブルシューティング、configuration 名前空間の DeviceRegistrationService](./media/how-to-connect-device-writeback/troubleshoot1.png)

* configuration 名前空間を検索して、configuration オブジェクトが 1 つのみであることを確認します。 複数ある場合は、重複するオブジェクトを削除します。

![トラブルシューティング、重複するオブジェクトの検索](./media/how-to-connect-device-writeback/troubleshoot2.png)

* Device Registration Service オブジェクトに、属性 msDS-DeviceLocation が存在し、値が指定されていることを確認します。 その場所を参照し、その場所と objectType msDS-DeviceContainer が存在することを確認します。

![トラブルシューティング、msDS-DeviceLocation](./media/how-to-connect-device-writeback/troubleshoot3.png)

![トラブルシューティング、RegisteredDevices オブジェクト クラス](./media/how-to-connect-device-writeback/troubleshoot4.png)

* Active Directory Connector には、前の手順で検索した Registered Devices コンテナーに対するアクセス許可が必要です。 このコンテナーには、次のようなアクセス許可があります。

![トラブルシューティング、コンテナーに対するアクセス許可の確認](./media/how-to-connect-device-writeback/troubleshoot5.png)

* Active Directory アカウントに、CN=Device Registration Configuration,CN=Services,CN=Configuration オブジェクトに対するアクセス許可があることを確認します。

![トラブルシューティング、デバイス登録構成に対するアクセス許可の確認](./media/how-to-connect-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>追加情報
* [条件付きアクセス ポリシーを使用したリスクの管理](../active-directory-conditional-access-azure-portal.md)
* [Azure Active Directory Device Registration を使用したオンプレミスの条件付きアクセスの設定](../../active-directory/active-directory-device-registration-on-premises-setup.md)

## <a name="next-steps"></a>次の手順
「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。

