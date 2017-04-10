# 概要
## [Azure Active Directory とは](active-directory-whatis.md)
## [エディションの選択](active-directory-editions.md)
## [Azure の ID 管理について](fundamentals-identity.md)
## [Azure AD ポータルのエクスペリエンスをプレビューする](active-directory-preview-explainer.md)


# 作業開始
## [Azure AD テナントを取得する](active-directory-howto-tenant.md)
## [Azure AD Premium へのサインアップ](active-directory-get-started-premium.md)
## [Azure サブスクリプションを関連付ける](active-directory-how-subscriptions-associated-directory.md)
## Azure AD ライセンスの管理
### [Azure ポータル](active-directory-licensing-get-started-azure-portal.md)
### [クラシック ポータル](active-directory-licensing-what-is.md)
## [組織で Azure を導入する](sign-up-organization.md)
## [FAQ](active-directory-faq.md)
## [SaaS アプリのチュートリアル](active-directory-saas-tutorial-list.md)

# 方法
## 計画と設計
### [ハイブリッド ID ソリューションをデプロイする](active-directory-hybrid-identity-design-considerations-overview.md)
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
### Add users
#### [Azure Portal](active-directory-users-create-azure-portal.md)
#### [クラシック ポータル](active-directory-create-users.md)

### [他のディレクトリからユーザーを追加する (クラシック ポータル)](active-directory-create-users-external.md)
### [ユーザーを削除する](active-directory-users-delete-user-azure-portal.md)
### [ユーザー プロファイルの管理](active-directory-users-profile-azure-portal.md)
### [パスワードをリセットする](active-directory-users-reset-password-azure-portal.md)
### [ユーザーの作業情報を管理する](active-directory-users-work-info-azure-portal.md)
### [アカウントの共有](active-directory-sharing-accounts.md)

## [グループとメンバーの管理](active-directory-manage-groups.md)
### グループの管理
#### [Azure Portal](active-directory-groups-create-azure-portal.md)
#### [クラシック ポータル](active-directory-accessmanagement-manage-groups.md)
#### [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
### [グループ メンバーを管理する](active-directory-groups-members-azure-portal.md)
### [グループ所有者を管理する](active-directory-accessmanagement-managing-group-owners.md)
### [グループ メンバーシップを管理する](active-directory-groups-membership-azure-portal.md)
### [グループをすべて表示する](active-directory-groups-view-azure-portal.md)
### [専用グループの有効化](active-directory-accessmanagement-dedicated-groups.md)
### [SaaS アプリへのグループ アクセスの追加](active-directory-accessmanagement-group-saasapps.md)
### グループ設定の管理
#### [Azure Portal](active-directory-groups-settings-azure-portal.md)
#### [コマンドレット](active-directory-accessmanagement-groups-settings-cmdlets.md)
### 高度なルールの作成
#### [Azure Portal](active-directory-groups-dynamic-membership-azure-portal.md)
#### [クラシック ポータル](active-directory-accessmanagement-groups-with-advanced-rules.md)
### [グループベースのライセンス](active-directory-licensing-whatis-azure-portal.md)
#### [グループにライセンスを割り当てる](active-directory-licensing-group-assignment-azure-portal.md)
#### [グループのライセンスに関する問題を特定して解決する](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [個別にライセンスを付与されたユーザーをグループベースのライセンスに移行する](active-directory-licensing-group-migration-azure-portal.md)
#### [グループベースのライセンスのその他のシナリオ](active-directory-licensing-group-advanced.md)
### [セルフサービス グループのセットアップ](active-directory-accessmanagement-self-service-group-management.md)
### [トラブルシューティング](active-directory-accessmanagement-troubleshooting.md)

## [レポートの管理](active-directory-reporting-azure-portal.md)
### [サインイン アクティビティ](active-directory-reporting-activity-sign-ins.md)
### [監査アクティビティ](active-directory-reporting-activity-audit-logs.md)
### [危険な状態のユーザー](active-directory-reporting-security-user-at-risk.md)
### [リスクの高いサインイン](active-directory-reporting-security-risky-sign-ins.md)
### [リスク イベント](active-directory-reporting-risk-events.md)
### [名前付きネットワーク](active-directory-known-networks-azure-portal.md)
### [レポートの移行](active-directory-reporting-migration.md)
### [保持](active-directory-reporting-retention.md)
### [FAQ](active-directory-reporting-faq.md)
### トラブルシューティング
#### [見つからない監査データ](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [ダウンロードに見つからないデータ](active-directory-reporting-troubleshoot-missing-data-download.md)
###    プログラムによるアクセス
#### [監査のリファレンス](active-directory-reporting-api-audit-reference.md)
#### [監査のサンプル](active-directory-reporting-api-audit-samples.md)
#### [前提条件](active-directory-reporting-api-prerequisites.md)
#### [サインインの参照](active-directory-reporting-api-sign-in-activity-reference.md)
#### [サインインのサンプル](active-directory-reporting-api-sign-in-activity-samples.md)
### [クラシック ポータル](active-directory-view-access-usage-reports.md)
#### [Azure AD レポート](active-directory-reporting-getting-started.md)
#### [レポート ガイド](active-directory-reporting-guide.md)
#### [既知のネットワーク](active-directory-known-networks.md)
#### [API](active-directory-reporting-api-getting-started.md)
#### [イベントを監査する](active-directory-reporting-audit-events.md)
#### [待機時間](active-directory-reporting-latencies.md)
#### [Notifications](active-directory-reporting-notifications.md)
#### レポートについて
##### [不規則なサインイン](active-directory-reporting-irregular-sign-in-activity.md)
##### [複数のエラー](active-directory-reporting-sign-ins-after-multiple-failures.md)
##### [疑わしい IP アドレス](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
##### [複数の地域](active-directory-reporting-sign-ins-from-multiple-geographies.md)
##### [感染している可能性があるデバイス](active-directory-reporting-sign-ins-from-possibly-infected-devices.md)
##### [不明なソース](active-directory-reporting-sign-ins-from-unknown-sources.md)
##### [異常なサインイン](active-directory-reporting-users-with-anomalous-sign-in-activity.md)

## [パスワードの管理](active-directory-manage-passwords.md)
### [自分のパスワードを更新する](active-directory-passwords-update-your-own-password.md)
### [セルフサービスのパスワード リセットをセットアップする](active-directory-passwords.md)
### [パスワード管理について](active-directory-passwords-how-it-works.md)
### [パスワードに関するポリシーと制限について](active-directory-passwords-policy.md)
### パスワードをリセットする
#### [Azure Portal](active-directory-users-reset-password-azure-portal.md)
#### [クラシック ポータル](active-directory-create-users-reset-password.md)
### [有効期限ポリシーの設定](active-directory-passwords-set-expiration-policy.md)
### パスワード管理を有効にする
#### [作業開始](active-directory-passwords-getting-started.md)
#### [デプロイする](active-directory-passwords-best-practices.md)
#### [カスタマイズ](active-directory-passwords-customize.md)
#### [レポートを表示する](active-directory-passwords-get-insights.md)
#### [詳細情報](active-directory-passwords-learn-more.md)
#### [FAQ](active-directory-passwords-faq.md)
#### [トラブルシューティング](active-directory-passwords-troubleshoot.md)

## デバイスを管理する
### [デバイスを登録する](active-directory-device-registration-overview.md)
#### [セットアップ](active-directory-conditional-access-automatic-device-registration-setup.md)
#### [オンプレミスにデプロイする](active-directory-device-registration-on-premises-setup.md)
#### [FAQ](active-directory-device-registration-faq.md)
#### トラブルシューティング
##### [Windows 10 と Windows Server 2016 のトラブルシューティング](active-directory-device-registration-troubleshoot-windows.md)
##### [Windows ダウンレベル クライアントのトラブルシューティング](active-directory-device-registration-troubleshoot-windows-legacy.md)
### [Azure AD Join](active-directory-azureadjoin-overview.md)
#### [プラン](active-directory-azureadjoin-deployment-aadjoindirect.md)
#### [デバイスの登録の設定](active-directory-azureadjoin-setup.md)
#### [新しいデバイスを登録する](active-directory-azureadjoin-user-frx.md)
#### [デプロイする](active-directory-azureadjoin-devices-group-policy.md)
#### [Windows 10 の統合について](active-directory-azureadjoin-windows10-devices-overview.md)
#### [Windows 10 デバイスの使用](active-directory-azureadjoin-windows10-devices.md)
#### [デバイスを参加させる](active-directory-azureadjoin-personal-device.md)
#### [Windows 10 デバイスを参加させる](active-directory-azureadjoin-user-upgrade.md)

## アプリを管理する
### [概要](active-directory-enable-sso-scenario.md)
### [使用の開始](active-directory-integrating-applications-getting-started.md)

### [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)
#### [レジストリ設定の更新](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
#### [セキュリティとプライバシーについて](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)

### [アプリへのリモート アクセス権の付与](active-directory-application-proxy-get-started.md)
#### [アプリケーション プロキシを有効にする](active-directory-application-proxy-enable.md)
#### [コネクタについて](application-proxy-understand-connectors.md)

#### アプリを発行する
##### [Azure ポータル](application-proxy-publish-azure-portal.md)
##### [クラシック ポータル](active-directory-application-proxy-publish.md)

#### [セキュリティ](application-proxy-security-considerations.md)
#### [ネットワーク](application-proxy-network-topology-considerations.md)
#### [リモート デスクトップ](application-proxy-publish-remote-desktop.md)
#### [SharePoint](application-proxy-enable-remote-access-sharepoint.md)

#### 異なるネットワークに発行する
##### [Azure Portal](active-directory-application-proxy-connectors-azure-portal.md)
##### [クラシック ポータル](active-directory-application-proxy-connectors.md)
#### [プロキシ サーバー](application-proxy-working-with-proxy-servers.md)
#### [カスタム ドメイン](active-directory-application-proxy-custom-domains.md)
#### [アプリへのアクセス](active-directory-appssoaccess-whatis.md)
##### [Azure ポータル](application-proxy-sso-azure-portal.md)
#### [KCD を使用した SSO](active-directory-application-proxy-sso-using-kcd.md)
#### [ヘッダーを使用した SSO](application-proxy-ping-access.md)
#### [要求に対応するアプリケーション](active-directory-application-proxy-claims-aware-apps.md)
#### [ネイティブ クライアント アプリ](active-directory-application-proxy-native-client.md)
#### [カスタム ホーム ページ](application-proxy-office365-app-launcher.md)
#### [条件付きアクセス](active-directory-application-proxy-conditional-access.md)
#### [サイレント インストール](active-directory-application-proxy-silent-installation.md)
#### [Microsoft Forefront](application-proxy-transition-from-uag-tmg.md)
#### [トラブルシューティング](active-directory-application-proxy-troubleshoot.md)

### エンタープライズ アプリの管理
#### [ユーザーを割り当てる](active-directory-coreapps-assign-user-azure-portal.md)
#### [ブランドをカスタマイズする](active-directory-coreapps-change-app-logo-user-azure-portal.md)
#### [ユーザーのサインインを無効にする](active-directory-coreapps-disable-app-azure-portal.md)
#### [ユーザーを削除する](active-directory-coreapps-remove-assignment-azure-portal.md)
#### [すべてのアプリを表示する](active-directory-coreapps-view-azure-portal.md)
#### [ユーザー アカウントのプロビジョニング管理](active-directory-enterprise-apps-manage-provisioning.md)

### 開発
#### [ユーザーを割り当てる](active-directory-applications-guiding-developers-assigning-users.md)
#### [グループを割り当てる](active-directory-applications-guiding-developers-assigning-groups.md)
#### [必要な割り当て](active-directory-applications-guiding-developers-requiring-user-assignment.md)
#### [LoB アプリの開発](active-directory-applications-guiding-developers-for-lob-applications.md)

### [アプリへのアクセスを管理する](active-directory-managing-access-to-apps.md)
#### [セルフ サービス アクセス](active-directory-self-service-application-access.md)
#### [SSO の証明書](active-directory-sso-certs.md)
#### [テナント制限](active-directory-tenant-restrictions.md)

### [ユーザーのプロビジョニングに SCIM を使用する](active-directory-scim-provisioning.md)
### [ドキュメント ライブラリ](active-directory-apps-index.md)

## ディレクトリの管理
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### カスタム ドメイン名
#### [概要](active-directory-add-domain-concepts.md)
#### ドメイン名を追加する
##### [Azure Portal](active-directory-domains-add-azure-portal.md)
##### [クラシック ポータル](active-directory-add-domain.md)
##### [AD FS を使用](active-directory-add-domain-federated.md)
#### [ユーザーを割り当てる](active-directory-add-domain-add-users.md)
#### ドメイン名を管理する
##### [Azure Portal](active-directory-domains-manage-azure-portal.md)
##### [クラシック ポータル](active-directory-add-manage-domain-names.md)
### サインイン ページをカスタマイズする
#### [Azure Portal](active-directory-branding-custom-signon-azure-portal.md)
#### [言語固有](active-directory-branding-localize-azure-portal.md)
#### [クラシック ポータル](active-directory-add-company-branding.md)
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
#### [招待メール](active-directory-b2b-invitation-email.md)
#### [招待の利用](active-directory-b2b-redemption-experience.md)
#### [B2B ユーザー プロパティ](active-directory-b2b-user-properties.md)
#### [ゲスト ユーザーをロールに追加する](active-directory-b2b-add-guest-to-role.md)
#### [監査とレポート](active-directory-b2b-auditing-and-reporting.md)
#### [API とカスタマイズ](active-directory-b2b-api.md)
#### [招待を委任する](active-directory-b2b-delegate-invitations.md)
#### [動的グループと B2B](active-directory-b2b-dynamic-groups.md)
#### [B2B の多要素認証](active-directory-b2b-mfa-instructions.md)
#### [B2B ユーザー トークン](active-directory-b2b-user-token.md)
#### [B2B ユーザー要求のマッピング](active-directory-b2b-claims-mapping.md)
#### [Office 365 の外部共有](active-directory-b2b-o365-external-user.md)
#### [B2B 用に SaaS アプリを構成する](active-directory-b2b-configure-saas-apps.md)
#### [コードと PowerShell のサンプル](active-directory-b2b-code-samples.md)
#### [現時点での制限事項](active-directory-b2b-current-limitations.md)
#### [ライセンス](active-directory-b2b-licensing.md)
#### [B2B のトラブルシューティング](active-directory-b2b-troubleshooting.md)
#### [B2B コラボレーションと B2C を比較する](active-directory-b2b-compare-b2c.md)
#### [B2B のサポートの利用](active-directory-b2b-support.md)
#### [FAQ](active-directory-b2b-faq.md)
### [Azure AD Connect を使用してオンプレミスの ID を統合する](./connect/active-directory-aadconnect.md)


## リソースへのアクセスを委任する
### [管理者ロール](active-directory-assign-admin-roles.md)
#### [管理者ロールを割り当てる](active-directory-users-assign-role-azure-portal.md)
### [管理単位](active-directory-administrative-units-management.md)
### [Azure でのリソース アクセス](active-directory-understanding-resource-access.md)
### [ロールベースのアクセス制御](role-based-access-control-what-is.md)
#### アクセス権の割り当てを管理する
##### [ユーザー別](role-based-access-control-manage-assignments.md)
##### [リソース別](role-based-access-control-configure.md)
#### [組み込みのロール](role-based-access-built-in-roles.md)
#### [カスタム ロール](role-based-access-control-custom-roles.md)
#### [レポート](role-based-access-control-access-change-history-report.md)
#### その他のロール管理方法
##### [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
##### [PowerShell](role-based-access-control-manage-access-powershell.md)
##### [REST](role-based-access-control-manage-access-rest.md)
#### [トラブルシューティング](role-based-access-control-troubleshooting.md)
### [トークンの有効期間を構成する](active-directory-configurable-token-lifetimes.md)

## ID をセキュリティで保護する
### [条件付きアクセス](active-directory-conditional-access.md)
#### [作業開始](active-directory-conditional-access-azuread-connected-apps.md)
#### [サポート対象アプリ](active-directory-conditional-access-supported-apps.md)
#### [デバイス ポリシーについて](active-directory-conditional-access-device-policies.md)
#### [接続されているアプリに対するアクセスのセットアップ](active-directory-conditional-access-policy-connected-applications.md)
#### [FAQ](active-directory-conditional-faqs.md)
#### [トラブルシューティング](active-directory-conditional-access-device-remediation.md)
#### [リファレンス](active-directory-conditional-access-technical-reference.md)
### Windows Hello
#### [パスワードを使用せずに認証する](active-directory-azureadjoin-passport.md)
#### [Windows Hello for Business の有効化](active-directory-azureadjoin-passport-deployment.md)
### 証明書ベースの認証
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
### [Azure AD Identity Protection](active-directory-identityprotection.md)
#### [有効にする](active-directory-identityprotection-enable.md)
#### [脆弱性を検出する](active-directory-identityprotection-vulnerabilities.md)
#### [リスク イベント](active-directory-identity-protection-risk-events.md)
#### [Notifications](active-directory-identityprotection-notifications.md)
#### [サインイン エクスペリエンス](active-directory-identityprotection-flows.md)
#### [リスク イベントをシミュレートする](active-directory-identityprotection-playbook.md)
#### [ユーザーのブロックを解除する](active-directory-identityprotection-unblock-howto.md)
#### [用語集](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

## [Azure VM に AD DS をデプロイする](virtual-networks-windows-server-active-directory-virtual-machines.md)
### [Azure VM 上の Windows Server Active Directory](active-directory-deploying-ws-ad-guidelines.md)
### [Azure 仮想ネットワークのレプリカ ドメイン コントローラー](active-directory-install-replica-active-directory-domain-controller.md)
### [Azure 仮想ネットワークの新しいフォレスト](active-directory-new-forest-virtual-machine.md)



## [Azure に AD FS をデプロイする](active-directory-aadconnect-azure-adfs.md)
### [高可用性](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [署名ハッシュ アルゴリズムを変更する](active-directory-federation-sha256-guidance.md)

## [トラブルシューティング](active-directory-troubleshooting.md)


# リファレンス
## [PowerShell コマンドレット](/powershell/ )
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
## [料金](https://azure.microsoft.com/pricing/details/active-directory/)
## [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
## [サービスの更新情報](https://azure.microsoft.com/updates/?product=active-directory)
## [Azure フィードバック フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory)
