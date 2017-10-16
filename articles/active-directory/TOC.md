# 概要
## [Azure Active Directory とは](active-directory-whatis.md)
## [Azure の ID 管理について](identity-fundamentals.md)
## [Azure ID ソリューションについて](understand-azure-identity-solutions.md)
## [ハイブリッド ID ソリューションの選択](choose-hybrid-identity-solution.md)
## [Azure サブスクリプションを関連付ける](active-directory-how-subscriptions-associated-directory.md)
## [FAQ](active-directory-faq.md)

# 作業開始
## [Azure AD Premium へのサインアップ](active-directory-get-started-premium.md)
## [カスタム ドメイン名の追加](add-custom-domain.md)
## [会社のブランドの構成](customize-branding.md)
## [Azure AD へのユーザーの追加](add-users-azure-active-directory.md)
## [ユーザーへのライセンスの割り当て](license-users-groups.md)
## [セルフサービスによるパスワードのリセットの構成](active-directory-passwords-getting-started.md)


# 方法
## 計画と設計
### [Azure AD のアーキテクチャを理解する](active-directory-architecture.md)
### [ハイブリッド ID ソリューションをデプロイする](active-directory-hybrid-identity-design-considerations-overview.md)
### [Azure Active Directory での要求マッピング](active-directory-claims-mapping.md)
#### 要件を確認する
##### [ID](active-directory-hybrid-identity-design-considerations-business-needs.md)
##### [ディレクトリ同期](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [多要素認証](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [ID のライフサイクル戦略](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [データ セキュリティを計画する](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [データ保護](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [コンテンツ管理](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [アクセス制御](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [インシデント対応](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)
#### ID ライフサイクルを計画する
##### [タスク](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [採用戦略](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [次のステップ](active-directory-hybrid-identity-design-considerations-nextsteps.md)
#### [ツールの比較](active-directory-hybrid-identity-design-considerations-tools-comparison.md)

## ユーザーの管理
### [グループを使用してライセンスを割り当てる](active-directory-licensing-whatis-azure-portal.md)
#### [グループにライセンスを割り当てる](active-directory-licensing-group-assignment-azure-portal.md)
#### [グループでのライセンスに関する問題を特定して解決する](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [個別にライセンスを付与されたユーザーをグループベースのライセンスに移行する](active-directory-licensing-group-migration-azure-portal.md)
#### [グループベースのライセンスのその他のシナリオ](active-directory-licensing-group-advanced.md)
#### [グループベースのライセンスの Azure PowerShell の例](active-directory-licensing-ps-examples.md)
### [ユーザー プロファイルの管理](active-directory-users-profile-azure-portal.md)
### [パスワードをリセットする](active-directory-users-reset-password-azure-portal.md)
### [ユーザーの作業情報を管理する](active-directory-users-work-info-azure-portal.md)
### [アカウントの共有](active-directory-sharing-accounts.md)



## [グループとメンバーの管理](active-directory-manage-groups.md)
### グループの管理
#### [Azure Portal](active-directory-groups-create-azure-portal.md)
#### [クラシック ポータル](active-directory-accessmanagement-manage-groups.md)
#### [Azure PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
### [グループ メンバーを管理する](active-directory-groups-members-azure-portal.md)
### [グループ所有者を管理する](active-directory-accessmanagement-managing-group-owners.md)
### [グループ メンバーシップを管理する](active-directory-groups-membership-azure-portal.md)
### [グループを使用してライセンスを割り当てる](active-directory-licensing-whatis-azure-portal.md)
#### [グループにライセンスを割り当てる](active-directory-licensing-group-assignment-azure-portal.md)
#### [グループでのライセンスに関する問題を特定して解決する](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [個別にライセンスを付与されたユーザーをグループベースのライセンスに移行する](active-directory-licensing-group-migration-azure-portal.md)
#### [グループベースのライセンスのその他のシナリオ](active-directory-licensing-group-advanced.md)
#### [グループベースのライセンスの Azure PowerShell の例](active-directory-licensing-ps-examples.md)
### [Office 365 グループの有効期限を設定する](active-directory-groups-lifecycle-azure-portal.md)
### [グループをすべて表示する](active-directory-groups-view-azure-portal.md)
### [専用グループの有効化](active-directory-accessmanagement-dedicated-groups.md)
### [SaaS アプリへのグループ アクセスの追加](active-directory-accessmanagement-group-saasapps.md)
### [削除された Office 365 グループを復元する](active-directory-groups-restore-azure-portal.md)
### グループ設定の管理
#### [Azure Portal](active-directory-groups-settings-azure-portal.md)
#### [コマンドレット](active-directory-accessmanagement-groups-settings-cmdlets.md)
### 高度なルールの作成
#### [Azure Portal](active-directory-groups-dynamic-membership-azure-portal.md)
#### [クラシック ポータル](active-directory-accessmanagement-groups-with-advanced-rules.md)
### [セルフサービス グループのセットアップ](active-directory-accessmanagement-self-service-group-management.md)
### [トラブルシューティング](active-directory-accessmanagement-troubleshooting.md)

## [レポートの管理](active-directory-reporting-azure-portal.md)
### [サインイン アクティビティ](active-directory-reporting-activity-sign-ins.md)
### [監査アクティビティ](active-directory-reporting-activity-audit-logs.md)
### [危険な状態のユーザー](active-directory-reporting-security-user-at-risk.md)
### [リスクの高いサインイン](active-directory-reporting-security-risky-sign-ins.md)
### [リスク イベント](active-directory-reporting-risk-events.md)
### [FAQ](active-directory-reporting-faq.md)
### タスク
#### [名前付きの場所の構成](active-directory-named-locations.md)
#### [アクティビティ レポートの検出](active-directory-reporting-migration.md)
#### [Azure Active Directory Power BI コンテンツ パックの使用](active-directory-reporting-power-bi-content-pack-how-to.md)
### リファレンス
#### [保持](active-directory-reporting-retention.md)
#### [待機時間](active-directory-reporting-latencies-azure-portal.md)
#### [Notifications](active-directory-reporting-notifications.md)
#### [サインイン アクティビティのエラー コード](active-directory-reporting-activity-sign-ins-errors.md)
### トラブルシューティング
#### [見つからない監査データ](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [ダウンロードに見つからないデータ](active-directory-reporting-troubleshoot-missing-data-download.md)
#### [Azure Active Directory アクティビティ ログ コンテンツ パックのエラー](active-directory-reporting-troubleshoot-content-pack.md)
### [プログラムによるアクセス](active-directory-reporting-api-getting-started-azure-portal.md)
#### [監査のリファレンス](active-directory-reporting-api-audit-reference.md)
#### [サインインの参照](active-directory-reporting-api-sign-in-activity-reference.md)
#### [前提条件](active-directory-reporting-api-prerequisites-azure-portal.md)
#### [監査のサンプル](active-directory-reporting-api-audit-samples.md)
#### [サインインのサンプル](active-directory-reporting-api-sign-in-activity-samples.md)
#### [証明書の使用](active-directory-reporting-api-with-certificates.md)

## [パスワードの管理](active-directory-passwords-overview.md)
### ユーザー ドキュメント
#### [パスワードのリセットまたは変更](active-directory-passwords-update-your-own-password.md)
#### [パスワードのベスト プラクティス](active-directory-secure-passwords.md)
#### [セルフサービスのパスワード リセットのための登録](active-directory-passwords-reset-register.md)
### [SSPR のライセンス](active-directory-passwords-licensing.md)
### [SSPR のデプロイ](active-directory-passwords-best-practices.md)
### IT 管理者: パスワードのリセット
#### [Azure Portal](active-directory-users-reset-password-azure-portal.md)
#### [Azure クラシック ポータル](active-directory-create-users-reset-password.md)
### [SSPR ポリシーについて](active-directory-passwords-policy.md)
### [パスワード リセットについて](active-directory-passwords-how-it-works.md)
### [SSPR のカスタマイズ](active-directory-passwords-customize.md)
### [SSPR によって使用されるデータ](active-directory-passwords-data.md)
### [SSPR のレポート](active-directory-passwords-reporting.md)
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### [パスワード ライトバック](active-directory-passwords-writeback.md)
### [パスワード ハッシュの同期](./connect/active-directory-aadconnectsync-implement-password-synchronization.md#how-password-synchronization-works)
### [トラブルシューティング](active-directory-passwords-troubleshoot.md)
### [FAQ](active-directory-passwords-faq.md)


## デバイスを管理する
### [はじめに](device-management-introduction.md)
### [Azure Portal の使用](device-management-azure-portal.md)
### [Azure AD Join の計画](active-directory-azureadjoin-deployment-aadjoindirect.md)
### [FAQ](device-management-faq.md)
### タスク
#### [Azure AD 登録済み Windows 10 デバイスの設定](device-management-azuread-registered-devices-windows10-setup.md)
#### [Azure AD 参加済みデバイスの設定](device-management-azuread-joined-devices-setup.md)
#### [ハイブリッド Azure AD 参加済みデバイスの設定](device-management-hybrid-azuread-joined-devices-setup.md) 
#### [オンプレミスにデプロイする](active-directory-device-registration-on-premises-setup.md)
#### [Windows 10 の初回実行時における Azure AD への参加](device-management-azuread-joined-devices-frx.md)
### トラブルシューティング
#### [ハイブリッド Azure AD 参加済みの Windows 10 および Windows Server 2016 デバイス](device-management-troubleshoot-hybrid-join-windows-current.md)
#### [ハイブリッド Azure AD 参加済みの従来の Windows デバイス](device-management-troubleshoot-hybrid-join-windows-legacy.md)

## アプリを管理する
### [概要](active-directory-enable-sso-scenario.md)
### [使用の開始](active-directory-integrating-applications-getting-started.md)
### [SaaS アプリの統合に関するチュートリアル](active-directory-saas-tutorial-list.md)
### [Cloud App Discovery](cloudappdiscovery-overview.md)
#### [エージェントレスの検出の設定](cloudappdiscovery-get-started.md)
#### [スナップショット レポートの作成](cloudappdiscovery-set-up-snapshots.md)
#### [継続的なレポートの構成](https://docs.microsoft.com/cloud-app-security/discovery-docker)
#### [カスタム ログ パーサーの使用](https://docs.microsoft.com/cloud-app-security/custom-log-parser)
#### エージェント ベースの検出
##### [Cloud App Discovery とは](active-directory-cloudappdiscovery-whatis.md)
##### [レジストリ設定の更新](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
##### [セキュリティとプライバシーについて](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)


### [アプリ プロキシを使用してリモートでアプリにアクセスする](active-directory-application-proxy-get-started.md)
#### 作業開始
##### [アプリケーション プロキシを有効にする](active-directory-application-proxy-enable.md)
##### [アプリを発行する](application-proxy-publish-azure-portal.md)
##### [カスタム ドメイン](active-directory-application-proxy-custom-domains.md)
#### [シングル サインオン](application-proxy-sso-overview.md)
##### [KCD を使用した SSO](active-directory-application-proxy-sso-using-kcd.md)
##### [ヘッダーを使用した SSO](application-proxy-ping-access.md)
##### [パスワード保管を使用した SSO](application-proxy-sso-azure-portal.md)
#### 概念
##### [コネクタ](application-proxy-understand-connectors.md)
##### [セキュリティ](application-proxy-security-considerations.md)
##### [ネットワーク](application-proxy-network-topology-considerations.md)


##### [TMG または UAG からのアップグレード](application-proxy-transition-from-uag-tmg.md)

#### 詳細な構成
##### [異なるネットワークに発行する](active-directory-application-proxy-connectors-azure-portal.md)
##### [プロキシ サーバー](application-proxy-working-with-proxy-servers.md)
##### [要求に対応するアプリケーション](active-directory-application-proxy-claims-aware-apps.md)
##### [ネイティブ クライアント アプリ](active-directory-application-proxy-native-client.md)
##### [サイレント インストール](active-directory-application-proxy-silent-installation.md)
##### [カスタム ホーム ページ](application-proxy-office365-app-launcher.md)
##### [インライン リンクの変換](application-proxy-link-translation.md)
#### 発行に関するチュートリアル
##### [リモート デスクトップ](application-proxy-publish-remote-desktop.md)
##### [SharePoint](application-proxy-enable-remote-access-sharepoint.md)
##### [Microsoft Teams](application-proxy-teams.md)
#### [トラブルシューティング](active-directory-application-proxy-troubleshoot.md)
#### クラシック ポータルの使用
##### [コネクタをダウンロードする](application-proxy-enable-classic-portal.md)
##### [アプリを発行する](active-directory-application-proxy-publish.md)
##### [コネクタを使用する](active-directory-application-proxy-connectors.md)
##### [条件付きアクセス](active-directory-application-proxy-conditional-access.md)

### エンタープライズ アプリの管理
#### [ユーザーを割り当てる](active-directory-coreapps-assign-user-azure-portal.md)
#### [ブランドをカスタマイズする](active-directory-coreapps-change-app-logo-user-azure-portal.md)
#### [ユーザーのサインインを無効にする](active-directory-coreapps-disable-app-azure-portal.md)
#### [ユーザーを削除する](active-directory-coreapps-remove-assignment-azure-portal.md)
#### [すべてのアプリを表示する](active-directory-coreapps-view-azure-portal.md)
#### [ユーザー アカウントのプロビジョニング管理](active-directory-enterprise-apps-manage-provisioning.md)
#### [エンタープライズ アプリのシングル サインオンの管理](active-directory-enterprise-apps-manage-sso.md)
#### [SAML アプリの詳細な証明書署名](active-directory-enterprise-apps-advance-certificate-options.md)

### [アプリへのアクセスを管理する](active-directory-managing-access-to-apps.md)
#### [セルフ サービス アクセス](active-directory-self-service-application-access.md)
#### [SSO アクセス](active-directory-appssoaccess-whatis.md)
#### [SSO の証明書](active-directory-sso-certs.md)
#### [テナント制限](active-directory-tenant-restrictions.md)
#### [ユーザーのプロビジョニングに SCIM を使用する](active-directory-scim-provisioning.md)

### [トラブルシューティング](active-directory-application-troubleshoot-content-map.md)
#### [アプリケーション開発](active-directory-application-dev-troubleshoot-content-map.md)
##### [構成と登録](active-directory-application-dev-config-content-map.md)
##### [開発](active-directory-application-dev-development-content-map.md)
#### [アプリケーション管理](active-directory-application-management-troubleshoot-content-map.md)
##### [構成](active-directory-application-config-content-map.md)
##### [サインイン](active-directory-application-sign-in-content-map.md)
##### [プロビジョニング](active-directory-application-provisioning-content-map.md)
##### [アクセスの管理](active-directory-application-access-content-map.md)
##### [アクセス パネル](active-directory-application-access-panel-content-map.md)
##### [アプリケーション プロキシ](active-directory-application-proxy-content-map.md)
##### [条件付きアクセス](active-directory-application-conditional-access-content-map.md)
### [アプリの開発](active-directory-applications-guiding-developers-for-lob-applications.md)
### [ドキュメント ライブラリ](active-directory-apps-index.md)

## ディレクトリの管理
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### カスタム ドメイン名
#### [概要](active-directory-add-domain-concepts.md)
#### ドメイン名を管理する
##### [Azure Portal](active-directory-domains-manage-azure-portal.md)
##### [クラシック ポータル](active-directory-add-manage-domain-names.md)
### [ディレクトリを管理する](active-directory-administer.md)
### [複数のディレクトリ](active-directory-licensing-directory-independence.md)
### [O365 ディレクトリ](active-directory-manage-o365-subscription.md)
### [セルフサービス サインアップ](active-directory-self-service-signup.md)
### [Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
#### [有効にする](active-directory-windows-enterprise-state-roaming-enable.md)
#### [グループ ポリシーの設定](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Windows 10 の設定](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [FAQ](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [トラブルシューティング](active-directory-windows-enterprise-state-roaming-troubleshooting.md)

### [Azure AD B2B コラボレーションを使用してパートナーを統合する](active-directory-b2b-what-is-azure-ad-b2b.md)
#### [管理者による B2B ユーザーの追加](active-directory-b2b-admin-add-users.md)
#### [インフォメーション ワーカーによる B2B ユーザーの追加](active-directory-b2b-iw-add-users.md)
#### [API とカスタマイズ](active-directory-b2b-api.md)
#### [コードと Azure PowerShell のサンプル](active-directory-b2b-code-samples.md)
#### [セルフサービス サインアップ ポータルのサンプル](active-directory-b2b-self-service-portal.md)
#### [招待メール](active-directory-b2b-invitation-email.md)
#### [招待の利用](active-directory-b2b-redemption-experience.md)
#### [招待を使用せずに B2B ユーザーを追加する](active-directory-b2b-add-user-without-invite.md)
#### [B2B 向けの条件付きアクセス](active-directory-b2b-mfa-instructions.md)
#### [B2B の共有ポリシー](active-directory-b2b-delegate-invitations.md)
#### [B2B ユーザーをロールに追加する](active-directory-b2b-add-guest-to-role.md)
#### [動的グループと B2B ユーザー](active-directory-b2b-dynamic-groups.md)
#### [監査とレポート](active-directory-b2b-auditing-and-reporting.md)
#### [B2B と Office 365 の外部共有](active-directory-b2b-o365-external-user.md)
#### [ライセンス](active-directory-b2b-licensing.md)
#### [現時点での制限事項](active-directory-b2b-current-limitations.md)
#### [FAQ](active-directory-b2b-faq.md)
#### [B2B のトラブルシューティング](active-directory-b2b-troubleshooting.md)
#### [B2B ユーザーについて](active-directory-b2b-user-properties.md)
#### [B2B ユーザー トークン](active-directory-b2b-user-token.md)
#### [Azure AD 統合アプリ向けの B2B](active-directory-b2b-configure-saas-apps.md)
#### [B2B ユーザー要求のマッピング](active-directory-b2b-claims-mapping.md)
#### [B2B コラボレーションと B2C を比較する](active-directory-b2b-compare-b2c.md)
#### [B2B のサポートの利用](active-directory-b2b-support.md)

### [Azure AD Connect を使用してオンプレミスの ID を統合する](./connect/active-directory-aadconnect.md)

## [Azure へのアクセスの管理](toc.yml)

## リソースへのアクセスを委任する
### [管理者ロール](active-directory-assign-admin-roles.md)
#### [管理者ロールを割り当てる](active-directory-users-assign-role-azure-portal.md)
### [管理単位](active-directory-administrative-units-management.md)
### [トークンの有効期間を構成する](active-directory-configurable-token-lifetimes.md)

## アクセス レビュー
### [アクセス レビューの概要](active-directory-azure-ad-controls-access-reviews-overview.md)
### [アクセス レビューの完了](active-directory-azure-ad-controls-complete-access-review.md)
### [アクセス レビューの作成](active-directory-azure-ad-controls-create-access-review.md)
### [アクセス レビューを実行する方法](active-directory-azure-ad-controls-perform-access-review.md)
### [アクセスをレビューする方法](active-directory-azure-ad-controls-how-to-review-your-access.md)
### [アクセス レビューでのゲスト アクセス](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
### [レビューでのユーザー アクセスの管理](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
### [プログラムとコントロールの管理](active-directory-azure-ad-controls-manage-programs-controls.md)


## ID をセキュリティで保護する
### [条件付きアクセス](active-directory-conditional-access-azure-portal.md)
#### [コントロール](active-directory-conditional-access-controls.md)
#### [作業の開始](active-directory-conditional-access-azure-portal-get-started.md)
#### [ベスト プラクティス](active-directory-conditional-access-best-practices.md)
#### [Office 365 サービスのデバイス ポリシーについて](active-directory-conditional-access-device-policies.md)
#### タスク
##### [デバイスベースの条件付きアクセスの設定](active-directory-conditional-access-policy-connected-applications.md)
##### [アプリベースの条件付きアクセスの設定](active-directory-conditional-access-mam.md)
##### [ユーザーとアプリの使用条件を指定する](active-directory-tou.md)
##### [VPN 接続の設定](active-directory-conditional-access-vpn-connectivity-windows10.md)
##### [SharePoint Online と Exchange Online の設定](active-directory-conditional-access-no-modern-authentication.md)
##### [修復](active-directory-conditional-access-device-remediation.md)
#### [テクニカル リファレンス](active-directory-conditional-access-technical-reference.md)
#### [FAQ](active-directory-conditional-faqs.md)
#### [クラシック ポータル](active-directory-conditional-access.md)
##### [作業開始](active-directory-conditional-access-azuread-connected-apps.md)

### Windows Hello
#### [パスワードを使用せずに認証する](active-directory-azureadjoin-passport.md)
#### [Windows Hello for Business の有効化](active-directory-azureadjoin-passport-deployment.md)
### 証明書ベースの認証
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
#### [作業開始](active-directory-certificate-based-authentication-get-started.md)

### [Azure AD Identity Protection](active-directory-identityprotection.md)
#### [有効にする](active-directory-identityprotection-enable.md)
#### [脆弱性を検出する](active-directory-identityprotection-vulnerabilities.md)
#### [リスク イベント](active-directory-identity-protection-risk-events.md)
#### [Notifications](active-directory-identityprotection-notifications.md)
#### [サインイン エクスペリエンス](active-directory-identityprotection-flows.md)
#### [リスク イベントをシミュレートする](active-directory-identityprotection-playbook.md)
#### [ユーザーのブロックを解除する](active-directory-identityprotection-unblock-howto.md)
#### [FAQ](active-directory-identity-protection-faqs.md)
#### [用語集](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

## [他のサービスを Azure AD と統合する]()
### [LinkedIn の統合を有効にする](linkedin-integration.md)

## [Azure VM に AD DS をデプロイする](virtual-networks-windows-server-active-directory-virtual-machines.md)
### [Azure VM 上の Windows Server Active Directory](active-directory-deploying-ws-ad-guidelines.md)
### [Azure 仮想ネットワークのレプリカ ドメイン コントローラー](active-directory-install-replica-active-directory-domain-controller.md)
### [Azure 仮想ネットワークの新しいフォレスト](active-directory-new-forest-virtual-machine.md)



## [Azure に AD FS をデプロイする](active-directory-aadconnect-azure-adfs.md)
### [高可用性](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [署名ハッシュ アルゴリズムを変更する](active-directory-federation-sha256-guidance.md)

## [トラブルシューティング](active-directory-troubleshooting-support-howto.md)
### [Active Directory 項目が不足しているか使用できない場合のトラブルシューティング](active-directory-troubleshooting.md)

## Azure AD の概念実証 (PoC) のデプロイ
### [PoC プレイブック: はじめに](active-directory-playbook-intro.md)
### [PoC プレイブック: 主な要素](active-directory-playbook-ingredients.md)
### [PoC プレイブック: 実装](active-directory-playbook-implementation.md)
### [PoC プレイブック: 構成要素](active-directory-playbook-building-blocks.md)


# リファレンス
## [コード サンプル](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory)
## [Azure PowerShell コマンドレット](/powershell/azure/overview)
## [Java API リファレンス](/java/api)
## [.NET API](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)
## [サービスの制限と制約](active-directory-service-limits-restrictions.md)

# 関連項目
## [Multi-Factor Authentication](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [開発者向け Azure AD](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

# リソース
## [Azure フィードバック フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Azure のロードマップ](https://azure.microsoft.com/roadmap/?category=security-identity)
## [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [料金](https://azure.microsoft.com/pricing/details/active-directory/)
## [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)
## [サービスの更新情報](https://azure.microsoft.com/updates/?product=active-directory)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
