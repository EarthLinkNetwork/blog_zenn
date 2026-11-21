---
title: "新しいメンバーが初日 5 分で pull できるまで"
emoji: "📝"
type: "tech"
topics: ["dotvault", "secret-management", "cli"]
published: true
---

<!-- このファイルは gen-publish.mjs が生成した発行用スケルトン。翻訳(海外媒体)は Claude が en.md 経由で行う。 -->
<!-- gen-publish:src-sha256=cc127f814aba003cecee5d98216782b855110f8c0c0b04db094c2e0816f7737e -->

上原正吉（EarthLink Network Co., Ltd.）。Claude Codeを開発の主体に据え、20を超えるプロダクトを1人で同時に開発・運用しています。これは、その現場の実測記です。

<!-- TODO(Claude): この zenn 版は媒体トーン定義（/docs/platform-tone）に合わせてトーン・長さを調整する。方向性: 技術・実装重視。設計判断と数字を厚く。開発者向け。 以下は基となる本文。調整後にこのコメントを消す。 -->

# 新しいメンバーが初日 5 分で pull できるまで

![新しいメンバーが初日 5 分で pull できるまで](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/dvlt-012/hero-ogp.png)

この記事は、連載「dotvault の使い方 ── .env を手で配るのをやめ、CI・チーム・AI エージェントに 1 コマンドで配るまで」の第 12 回です。dotvault は、誰でもアカウントを作って無料で使えます。

新しいメンバーが入るたびに、`.env` を Slack の DM で送り、足りない値を聞かれてまた送る。初日の午前がそれで終わることがあります。

新しいメンバーの初日を、何も入っていない端末で測りました。1 回目の予定の表で「5 分」とした、CLI のインストールから `dotvault pull` で `.env` を受け取るまでは、20 秒以内でした。あわせて、1〜11 回目の手順を 1 つの表にまとめ、誰でもアカウントを作って無料で使える範囲を、画面に出ている数字で示します。この回で、連載は終わりです。

## 結論

- **dotvault は、誰でも自分でアカウントを作って無料で使えます。** app.dotvault.io の Create an account で、メールアドレスとパスワードを登録します。
- **招待された新しいメンバーが打つのは、install・login・init・pull の 4 つのコマンドです。** デモでは、インストールから pull の完了まで 20 秒以内でした。
- **チームで使う Company の無料プランは、10 人・25 project・key は無制限・pull は 1 日 1,000 回まで**です。
- **初日に出やすい失敗は、終了コードで見分けられます。** 付けられていない環境と project 名の打ち間違いは 4、ログインしていない時は 3 です。

## 本文（読了 約8分）

## 前提

1〜11 回目の手順のまとめです。読んでいなくても、この回だけで初日の手順を追えます。

## 1. アカウントを作る

https://app.dotvault.io/signup を開くと、アカウントを作る画面が出ます。dotvault.io の「無料で始める」のボタンからも、この画面に入れます。

![dotvault のアカウントを作る画面（英語 UI）。見出しは Create an account。説明文は「Register with your email address and a password. We will email you a confirmation code.」。入力欄は Email address、Password（at least 12 characters, including upper and lower case, a digit and a symbol）、Password (confirm) の 3 つで、その下に赤い Create account のボタンがあり、ボタンの下に「By signing up, you agree to the Terms of Service and the Privacy Policy.」（Terms of Service と Privacy Policy はリンク）の 1 行がある。この囲みの外に「Already have an account? Sign in」のリンクがある。](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/dvlt-012/12-1-signup.png)

入力は 3 つです。Email address、Password（12 文字以上で、大文字・小文字・数字・記号を含む）、Password (confirm) です。Create account を押すと、確認のコードを入れる画面に移ります。

![確認のコードを入れる画面（英語 UI）。見出しは Confirm your email address。説明文は「Enter the confirmation code from your email. Once confirmed you can sign in.」。入力欄は Email address（例のアドレスが入っている）と Confirmation code (the 6-digit number from the email) の 2 つで、その下に赤い Confirm email のボタンと「Resend the code」のリンクがある。](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/dvlt-012/12-1b-confirm.png)

登録したメールアドレスには、dotvault から「Verify your new account」という件名のメールが届き、本文に 6 桁のコードがあります。新しいアカウントで試すと、コードを入れて Confirm email を押した後はログイン画面に移りました。そこでログインすると、管理コンソールの最初の画面の見出しが Personal で、自分だけで使う Personal の組織ができていました。CLI の `dotvault whoami` も `Personal (personal, org_admin)` の 1 つだけを出します。

チームで使う時は、Personal を Company に切り替えてから、メンバーを招きます（9 回目）。

## 2. 新しいメンバーの初日を測る

デモの新しいメンバー dev-d は、9 回目で招いた人です。acme-shop という project の development だけを、読むだけの役割（reader）で付けてあります。HOME を空のフォルダにして、初めて使う端末と同じ状態から、コマンドの前ごとに時刻を出して進めました。

![新しい端末での初日の端末画面（再構成・内容は実出力）。13:00:37 UTC に npm i -g @earthlink/dotvault、10 秒後に dotvault --version が 0.19.1、11 秒後に dotvault login で Email と Password を聞かれ「Logged in as <デモのメールアドレス>. Session valid until … (365 days); the access token refreshes automatically.」、13 秒後に whoami で Acme Shop (company, guest) と Personal (personal, org_admin) の 2 つの組織、17 秒後に dotvault init --name acme-shop で「Created project-config.json (project=acme-shop).」、同じく 17 秒後に dotvault pull で「✓ Wrote 2 keys to /work/day1/acme-shop/.env (project=acme-shop, env=development, file=.env). Markers preserved.」、19 秒後に ls -l .env が -rw-------。cut で先頭 24 文字に切った .env には BEGIN と END の行の間に DATABASE_URL と STRIPE_SECRET_KEY が並ぶ。](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/dvlt-012/12-2-day-one.png)

| 経過 | コマンド | 出たもの |
|---|---|---|
| 0 秒 | `npm i -g @earthlink/dotvault` | CLI のインストール（10 秒） |
| 11 秒 | `dotvault login` | Email と Password を聞かれ、「Logged in as …」 |
| 13 秒 | `dotvault whoami` | 招かれた Acme Shop と、自分の Personal の 2 つの組織 |
| 17 秒 | `dotvault init --name acme-shop` | `project-config.json` ができる |
| 17 秒 | `dotvault pull` | 「Wrote 2 keys … env=development」 |
| 19 秒 | `ls -l .env` | 自分だけが読める `-rw-------` |

ログインの表示は「Session valid until …（365 days）」で、アクセス用の token は自動で更新されると出ます。

init は、project のフォルダに `project-config.json` を作るだけです。チームのリポジトリに `project-config.json` を commit しておけば、clone した後の init は要りません。その時に新しいメンバーが打つのは、install・login・pull の 3 つです（2 回目）。

パスワードを忘れた時は、ログイン画面の「Forgot your password?」から設定し直します。

## 3. 初日につまずく 3 つの失敗

同じ端末で、初日に出やすい失敗を 3 つ打ちました。

![初日に出やすい失敗 3 つの端末画面（再構成・内容は実出力）。上から、付けられていない production を指定した dotvault pull -E production が「✖ PERMISSION_DENIED: You do not have permission for project 'acme-shop' env 'production' …」で終了コード 4、project 名を acme-shopp と打ち間違えた dotvault pull acme-shopp も PERMISSION_DENIED で 4、dotvault logout の後の dotvault pull が「✖ AUTH_REQUIRED: Not logged in. Run `dotvault login` first.」で 3。](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/dvlt-012/12-3-errors.png)

| 出たもの | 終了コード | 原因 | すること |
|---|---|---|---|
| `PERMISSION_DENIED`（production） | 4 | 付けてもらっていない環境を指定した | 管理する人に環境を付けてもらう（9 回目） |
| `PERMISSION_DENIED`（acme-shopp） | 4 | project 名の打ち間違い | 名前を確かめる（大文字が混じった名前は別の失敗 `BAD_PROJECT`・終了コード 1。2 回目） |
| `AUTH_REQUIRED: Not logged in` | 3 | ログインしていない | `dotvault login` を打つ |

権限が無い時と名前を打ち間違えた時は、同じ `PERMISSION_DENIED` です。メッセージにある「The project may not exist, or you may not have been granted access」のとおり、まず名前を確かめ、合っていれば管理する人に頼みます。ほかの失敗（`NETWORK_ERROR` など）は、2 回目の「失敗の読み方」にまとめています。

## 4. 2 日目からは pull だけ

初日の後に新しいメンバーが打つのは、`dotvault pull` だけです。管理する人が管理コンソールや `dotvault set` で値を直したら、同じフォルダで `dotvault pull` をもう一度打てば、`.env` の中の BEGIN と END の行に挟まれた区間が今の値に置き換わります（2 回目・4 回目）。その区間の外に自分で書いた行は、そのまま残ります。

`.env` をディスクに書きたくない時は、`dotvault run -- npm run dev` のように、値を子のプロセスに渡して起動できます（2 回目）。

新しいメンバーの初日が 4 つのコマンドで済むのは、管理する人が先に次の 3 つを済ませているからです。

| 先に済ませておくこと | 初日に起きること | 扱った回 |
|---|---|---|
| メールアドレスで招待し、project と環境を付ける | 付けた環境だけを pull できる。付けていない環境は `PERMISSION_DENIED` | 9 回目 |
| 値を project の環境に置く | pull で `.env` に書かれる | 3 回目・4 回目 |
| `project-config.json` をリポジトリに commit する | clone した後の init が要らない | 2 回目 |

人に `.env` を渡す作業は、招待で環境を付ける操作に置き換わります。辞める人を組織から外すと、その後の pull は止まります。手で配った `.env` を探して回る必要はありません。ただし、その人がすでに読めた値は手元に残るので、新しい値に取り替えます（9 回目・11 回目）。

## 5. この連載の手順を 1 つの表にまとめる

1〜10 は管理する人が最初に 1 回用意すること、11 は新しいメンバーがそれぞれ最初にすることです。

| 順 | すること | 使う画面・コマンド | 回 |
|---|---|---|---|
| 1 | アカウントを作り、CLI を入れてログインする | Create an account・`npm i -g`・`dotvault login` | この回・2 回目 |
| 2 | 環境ごとの値を 1 つの project にまとめる | Projects・Common | 3 回目 |
| 3 | key を書き、間違えたら前の値に戻す | `dotvault set`・`versions`・`rollback` | 4 回目 |
| 4 | `.npmrc` や kubeconfig をファイルごと配り、commit の前に混入を止める | `dotvault push`・`scan` | 5 回目 |
| 5 | 複数の project で使う値を 1 か所に置く | Global Keys・bind | 6 回目 |
| 6 | CI に token を渡して pull する | Service Tokens・`DOTVAULT_TOKEN` | 7 回目 |
| 7 | AI エージェントに token を 1 つだけ渡す | Service Tokens | 8 回目 |
| 8 | 人を招き、見せる範囲を決め、辞めた人を止める | Company・+ Invite member・Teams | 9 回目 |
| 9 | 誰が何をしたかを後から追う | Audit Log | 10 回目 |
| 10 | 漏れたキーを、止めずに取り替える | `dotvault rotate` | 11 回目 |
| 11 | 新しいメンバーが受け取る | `dotvault login`・`init`・`pull` | この回・2 回目 |

## 6. 無料で使える範囲

dotvault.io のよくある質問には、「無料で使えますか？」に「はい。dotvault は開発者向けに無料で公開されているツールです。」とあります。無料プランで使える数は、管理コンソールの Usage 画面（`https://app.dotvault.io/admin/orgs/<組織の ID>/usage`。組織の ID は `dotvault whoami` の各行に、組織の名前と並んで出ます）で見られます。

![Company の組織 Acme Shop の Usage 画面（英語 UI）。見出しの横に free。RESOURCE COUNTERS に USERS 2 / 10、TEAMS 1 / 1、PROJECTS 3 / 25、KEYS (TOTAL) 2 / unlimited。DAILY USAGE に SYNCS TODAY 0 / 1000 と、「When the daily sync cap is reached, pulls for this organization are refused (429) until UTC midnight.」の注記。](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/dvlt-012/12-4-usage-company-free.png)

- **Company の無料プラン（チームで使う）。** 10 人まで、Team は 1 つ、project は 25 まで、key は無制限、pull は 1 日 1,000 回までです（`dotvault run` で値を取り出した回も数えます）。1,000 回に達すると、その組織の pull は UTC の 0 時（日本時間の午前 9 時）まで 429 で断られます。
- **Personal（自分だけで使う）。** project と key は無制限です。Usage 画面でも、PROJECTS と KEYS (TOTAL) が unlimited と出ます。

どちらも、環境は 1 つの project に 10 まで、監査の記録は 14 日間です。

画面にある Upgrade plan のボタンは押せない状態で、カーソルを置くと「Contact a Platform Admin to upgrade your plan」と出ます。

## 7. 困った時の問い合わせ先

dotvault は EarthLink Network が運営しています（dotvault.io の「運営会社」のリンク先）。使い方の質問は、dotvault のお問い合わせのページ（https://www.dotvault.io/contact ）から送れます。

## まとめ

招待された新しいメンバーは、何も入っていない端末から install・login・init・pull の 4 つのコマンドで `.env` を受け取れます。デモでは 20 秒以内でした。そのために管理する人がすることは、この連載の 1〜10 回目の手順です。dotvault は誰でもアカウントを作って無料で使えるので、まず自分の Personal の組織で試してみてください。

## 用語

- **Personal / Company**: dotvault の組織の種類。Personal は自分だけで使う組織で、アカウントを作って初めてログインした時にできています。Company は人を招いて使う組織です。
- **init**: project のフォルダに `project-config.json` を作り、そのフォルダを project に結び付けるコマンド。
- **終了コード**: コマンドが終わった時に返す数。0 が成功で、dotvault は失敗の種類ごとに違う数を返します。`echo $?` で見られます。

---

dotvault は、.env や設定ファイルの secret をサーバー側に置き、権限のある人・CI・AI エージェントが 1 コマンドで受け取るサービスです（https://dotvault.io ）。誰でもアカウントを作って無料で使えます。この連載の手順どおりに使い始められます。

興味のある方は、ぜひ「いいね」と記事の購読をお願いいたします。

自社プロダクトは https://www.eln.ne.jp/products にまとめています。

## 筆者について

上原正吉。EarthLink Network Co., Ltd. でAI開発をしています。2025年からClaude Codeを開発の主体に据え、今は20を超えるプロダクトを1人で同時に開発・運用しています。この連載では、その現場で実際に起きたこと（うまくいったことも、失敗も）を、数字と一緒に書いていきます。

また、AIで業務や開発を組み替えたい会社・チーム向けに、AI活用のコンサルティングも受け付けています。ご相談は [www.eln.ne.jp](https://www.eln.ne.jp) からどうぞ。

---

**EarthLink Network** は、会社の全業務を AI で回すために、必要になったものを自社で作っています。いま作っているプロダクトの一覧と概要は、こちらにまとめています。

→ [EarthLink Network が自社でつくっている18のプロダクト](https://zenn.dev/chooser/articles/in-house-products)

会社と各プロダクトの詳細は、公式サイト [www.eln.ne.jp](https://www.eln.ne.jp) をご覧ください。
