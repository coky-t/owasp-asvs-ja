# V11: HTTP セキュリティ構成検証要件

## 管理目標

検証されるアプリケーションが以下の上位レベルの要件を満たしていることを確認します。

* アプリケーションサーバーはデフォルト構成よりも適切に堅牢化されています。
* HTTP レスポンスはコンテンツタイプヘッダに安全な文字セットを含んでいます。

## セキュリティ検証要件

| # | 説明 | L1 | L2 | L3 | 導入バージョン |
| --- | --- | --- | --- | -- | -- |
| **11.1** | アプリケーションサーバーは、事前チェック用の OPTIONS を含む、アプリケーションや API で使用する HTTP メソッドを受け入れていることを検証します。 | ✓ | ✓ | ✓ | 1.0 |
| **11.2** | すべての HTTP レスポンスは安全な文字セット (UTF-8, ISO 8859-1 など) を指定したコンテンツタイプヘッダを含んでいることを検証します。 | ✓ | ✓ | ✓ | 1.0 |
| **11.3** | ベアラトークンなど、信頼できるプロキシや SSO デバイスにより追加された HTTP ヘッダがアプリケーションにより認証されていることを検証します。 |  | ✓ | ✓ | 2.0 |
| **11.4** | コンテンツが第三者のサイトに組み込まれるべきではないサイトに対して、適切な X-Frame-Options や Content-Security-Policy: frame-ancestors ヘッダが使用されていることを検証します。 |  | ✓ | ✓ | 3.0.1 |
| **11.5** | HTTP ヘッダや HTTP レスポンスの一部がシステムコンポーネントの詳細なバージョン情報を開示していないことを検証します。 | ✓ | ✓ | ✓ | 2.0 |
| **11.6** | すべての API レスポンスには X-Content-Type-Options: nosniff および Content-Disposition: attachment; filename="api.json" (またはコンテンツタイプに適したファイル名) を含んでいることを検証します。 | ✓ | ✓ | ✓ | 3.0 |
| **11.7** | コンテンツセキュリティポリシー (CSPv2) があり、一般的な DOM, XSS, JSON, JavaScript インジェクション脆弱性を低減していることを検証します。 | ✓ | ✓ | ✓ | 3.0.1 |
| **11.8** | X-XSS-Protection: 1; mode=block ヘッダがあり、ブラウザの反射型 XSS フィルタを有効化していることを検証します。 | ✓ | ✓ | ✓ | 3.0 |
| **11.9** | オリジンヘッダは攻撃者により簡単に変更できるため、提供されたオリジンヘッダは認証やアクセス制御判定に使用されていないことを検証します。 | ✓ | ✓ | ✓ | 4.0 |

## 参考情報

詳細については、以下を参照してください。

* [OWASP Testing Guide 4.0: Testing for HTTP Verb Tampering]( https://www.owasp.org/index.php/Testing_for_HTTP_Verb_Tampering_%28OTG-INPVAL-003%29)
* API レスポンスに Content-Disposition を追加することで、クライアントとサーバーの間の MIME タイプの勘違いに基づく多くの攻撃を防ぐことができます。また、"filename" オプションは [反射型ファイルダウンロード攻撃](https://www.blackhat.com/docs/eu-14/materials/eu-14-Hafif-Reflected-File-Download-A-New-Web-Attack-Vector.pdf) を防ぐのに役立ちます。
* [Content Security Policy Cheat Sheet](https://www.owasp.org/index.php?title=Content_Security_Policy_Cheat_Sheet)
* [Exploiting CORS misconfiguration for BitCoins and Bounties](https://portswigger.net/blog/exploiting-cors-misconfigurations-for-bitcoins-and-bounties)
