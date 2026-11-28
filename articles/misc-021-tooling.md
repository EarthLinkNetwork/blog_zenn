---
title: "Claude で作ったツールを Mac や Windows のアプリとして入れる ── Install as app の入れ方と消し方"
emoji: "📝"
type: "tech"
topics: ["ai", "claude", "anthropic", "macos", "業務効率化"]
published: true
---

<!-- このファイルは gen-publish.mjs が生成した発行用スケルトン。翻訳(海外媒体)は Claude が en.md 経由で行う。 -->
<!-- gen-publish:src-sha256=df9644e7e1e47be4b1969874225a17de3c93efaac76ac69ec92f362bb60e9535 -->

上原正吉（EarthLink Network Co., Ltd.）。AI会社・AI社員による業務自動化と、人の逐次介入なしに24時間自動開発を回す仕組みを自社で実践しています。開発ノウハウと具体的な設定方法、運用して分かった改善点を公開しています。

<!-- TODO(Claude): この zenn 版は媒体トーン定義（/docs/platform-tone）に合わせてトーン・長さを調整する。方向性: 技術・実装重視。設計判断と数字を厚く。開発者向け。 以下は基となる本文。調整後にこのコメントを消す。 -->

# Claude で作ったツールを Mac や Windows のアプリとして入れる ── Install as app の入れ方と消し方

![Claude で作ったツールを Mac や Windows のアプリとして入れる ── Install as app の入れ方と消し方](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/misc-021/hero-ogp.png)

Claude のデスクトップアプリに、会話で作ったアーティファクトを Mac や Windows のアプリとして入れる「Install as app」がベータ版で入りました。自社の Mac で記事を書く時の集中タイマーを作り、入れて、開いて、閉じて、消すまでを試し、Windows の手順はヘルプ記事から 1 つの節にまとめました。

## 結論

- **会話で作ったツールが、Mac や Windows のアプリになります。** 専用の窓とアイコンを持ち、Mac では Dock、Windows ではスタートメニューから開けます。
- **ファイルやプログラムには、許可した分しか触れません。** 今回のタイマーは、変えられる権限を 1 つも求めませんでした。
- **消す時は、ファイル > アプリ でアプリの名前を選び「アンインストール…」を押すだけです。** Mac ではアプリがゴミ箱に移り、元のアーティファクトは会話に残ります。
- **入れたアプリは、入れた人のパソコンに入ります。** 人に使ってもらう時は、アーティファクトを共有し、相手が自分で入れます。

## 本文（読了 約 10 分）

## 1. Install as app は何をする機能か

Anthropic のヘルプ記事「[Install an artifact as an app](https://support.claude.com/en/articles/18516354-install-an-artifact-as-an-app)」によると、Claude が作ったアーティファクトを、Mac や Windows のアプリとして入れられます。入れたアプリは専用の窓とアイコンを持ち、ほかのアプリと同じように開けます。

発表の記事「[Install as app](https://claude.com/resources/articles/install-as-app)」には、パッケージにまとめる作業も署名も要らず、Claude のアカウントで動くので API キー（ほかのプログラムから Claude を呼ぶための文字列）も別のログインも要らない、と書かれています。

ブラウザにも「Add to Dock」「Add to Home Screen」のような、ページをアイコンにする機能があります。ヘルプ記事は、これらはショートカットを足すだけで、Install as app で入れたアプリが持つ専用の窓・権限・メニューバーの項目などは付かないと書いて、はっきり分けています。

使うための条件は次のとおりです（ヘルプ記事から）。

- **アプリのバージョン**: Claude for Mac か Claude for Windows の 6.28899.0 以降。今回は Mac の 6.28899.6 で試しました。
- **プラン**: Free・Pro・Max・Ultra・Team・Enterprise が対象です。Team では最初から使えますが、コンピュータへの全部のアクセスは管理者が有効にするまで切れています。Enterprise では、組織のオーナー（Owner）が Organization settings > Artifacts で有効にするまで機能ごと使えません。
- **使えない所**: Linux・web・モバイルでは使えません。

## 2. 会話でタイマーを作る

最初に、入れるためのアーティファクトを作ります。Claude に英語で次のように頼みました。

```text
Build me a small single-page focus timer for writing blog articles. Preset buttons for 25 and 50 minutes, Start / Pause / Reset, a big countdown, and a list of the blocks I finished today with what I worked on (a short text field). Keep it simple and clean.
```

日本語にすると「記事を書くための小さな集中タイマーを 1 ページで作って。25 分と 50 分のボタン、開始・一時停止・やり直し、大きな残り時間、今日終わった区切りとその時に何をしていたか（短い入力欄）の一覧。簡単ですっきりした形で」です。

返事の中で、会話の右側にアーティファクト「Writing focus timer」ができました。この時点では、会話の中で動くページです。

![Claude のデスクトップアプリ。左に集中タイマーを頼んだ会話、右にできたアーティファクト「Writing focus timer」が開いていて、25:00 の残り時間と 25 min・50 min・Start・Reset のボタンが並んでいる](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/misc-021/01-1-artifact-ja.png)

## 3. アーティファクトをアプリとして入れる

ヘルプ記事の手順は、アーティファクトを Claude のデスクトップアプリのメインの窓で開き、タイトルのメニューを開くか右クリックして、「Install as app」を選ぶ、というものです。共有のメニューの下にもあります。この手順は Mac と Windows で同じです。

タイトルのメニューを開くと、「アプリとしてインストール」の右に「ベータ」と表示されています。

![アーティファクトのタイトル「Writing focus timer」のメニュー。共有・エクスポート・ポップアウト・ウェブで開く・ピン留めの下に「アプリとしてインストール」があり、右に「ベータ」と表示されている](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/misc-021/01-2-install-menu-ja.png)

選ぶと確認の画面が出ます。「許可しない限り、ファイルにアクセスしたりプログラムを実行したりすることはできません。」と書かれていて、「インストール」を押すと入ります。

![確認の画面「“Writing focus timer”をアプリとしてインストールしますか？」。独立したプログラムとしてインストールされ、専用のウィンドウとアイコンを持つこと、許可しない限りファイルにアクセスしたりプログラムを実行したりできないことが書かれ、キャンセルとインストールのボタンがある](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/misc-021/01-3-install-dialog-ja.png)

「インストール」を押してから窓が開くまでは数秒でした。途中で何かを選んだり、場所を決めたりする画面はありません。メニューに「アプリとしてインストール」が出ない時は、Claude のアプリを更新します（ヘルプ記事の英語の表記では、Mac は Claude > Check for Updates）。

## 4. 入れたアプリを開く・閉じる

入れるとすぐに、タイマーが専用の窓で開きました。窓のタイトルは「Claude · Writing focus timer」で、ブラウザの画面ではありません。アプリの本体は、ホームフォルダの Applications の中の「Claude Apps」フォルダに「Writing focus timer.app」として入っていました。

![入れたアプリの窓。タイトルは「Claude · Writing focus timer」で、25:00 の残り時間と Ready の表示、25 min・50 min・Start・Reset のボタン、その下に「What are you working on?」の入力欄があり、前に入れた作業の名前「draft: Install as app article」が入っている](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/misc-021/02-1-app-window-ja.png)

この画面の入力欄には、前に入れた作業の名前「draft: Install as app article」が入っています。入力した内容がアプリに残る点は 8 節で書きます。

入力欄に書いている作業の名前を入れて開始すると、残り時間が減り始め、表示が「Writing...」に変わりました。会話の中のアーティファクトと同じ動きです。

動いている間は Dock にアイコンが出ます。Dock のアイコンのメニューの Options に「Keep in Dock」があり、選べば閉じたあとも Dock に残せます（この Mac は英語の表示なので、Dock のメニューは英語です）。ヘルプ記事では、Spotlight や Apps launcher からも開けます。

![Dock のアイコンのメニュー。「Claude · Writing focus timer」の下に Options があり、その中に Keep in Dock・Open at Login・Show in Finder が並んでいる](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/misc-021/02-2-dock-options-en.png)

窓を閉じると、タイマーはそのまま終わりました。Dock のアイコンも消え、アプリの処理も残っていませんでした。ヘルプ記事によると、窓を閉じたあとも動き続けるのは、メニューバーの項目（Windows ではシステムトレイのアイコン）を持つアプリだけで、その権限は利用者が許可した時だけ付きます。止める時は、Dock から終了するか、アプリを前に出して command キー（⌘）を押しながら Q を押します。

タイマーを残り時間ごと動かし続けたいなら、メニューバーに出すタイマーとして作り、その権限を許可します。ヘルプ記事にも、例として「Make a Pomodoro timer that lives in my menu bar.」という頼み方が載っています。

## 5. 権限は、許可した分しか増えない

入れたアプリのメニューバーで、アプリの名前のメニューを開くと「権限…」があります。今回のタイマーは、変えられる権限を 1 つも求めていませんでした。

![「“Writing focus timer”の権限」の画面。「このアプリは、ここで変更できる権限をリクエストしていません。」と書かれ、OK のボタンだけがある](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/misc-021/03-1-permissions-ja.png)

発表の記事によると、権限は 3 段階です。

| 段階 | できること |
|---|---|
| 何も許可しない（Default） | ブラウザでできることだけ |
| アプリの権限（App permissions） | いつも前に出る窓・どこからでも効くショートカット・通知など |
| コンピュータへの全部のアクセス（Full computer access） | 毎回聞かずにプログラムを動かし、ファイルを読む・変える・消す |

ヘルプ記事では、権限を求められたら「Allow」か「Don't allow」で答えます。「Allow」は今の中身のアプリにだけ効き、自分で直した時も含めて、アーティファクトを変えるたびに聞き直されます。

Anthropic は入れる前にアプリを審査しません。発表の記事によると、全部のアクセスを渡せるのは自分が作ったアプリ（Team と Enterprise では組織の人が作ったアプリ）だけです。

権限を変える・取り消すのは、アプリが開いている時です。Mac ではアプリのメニューバーのアプリの名前 > 「権限…」、または Claude のアプリの ファイル > アプリ > 「権限…」です。

## 6. 消す時は ファイル > アプリ > アンインストール…

消す時は、Claude のアプリのメニューで ファイル > アプリ を開き、アプリの名前（今回は「Writing focus timer」）を選んで「アンインストール…」を押します。確認の画面で「アンインストール」を押せば終わりです。

![Claude のファイルのメニュー。アプリ > Writing focus timer の下に、開く・Finder に表示・Writing focus timer について・権限…・Artifacts を報告…・アンインストール… が並んでいる](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/misc-021/04-1-file-apps-ja.png)

確認の画面には、アプリがゴミ箱に移動されることと、Dock に追加している場合は Dock からもドラッグして取り除くことが書かれています。

![確認の画面「“Writing focus timer”をアンインストールしますか？」。このアプリはゴミ箱に移動されること、Dock に追加している場合は Dock からもドラッグして取り除くことが書かれ、キャンセルとアンインストールのボタンがある](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/misc-021/04-2-uninstall-ja.png)

アンインストールすると、アプリはゴミ箱に移り、ファイルのメニューから「アプリ」の項目が消えました。ヘルプ記事によると、アンインストールで消えるのは許可した権限で、アーティファクトは消えません。

![アンインストールした後の Claude のファイルのメニュー。新規チャット・新規プロジェクト・ファイルを開く・フォルダを開く・チャットを閉じる・ウィンドウを閉じるだけが並び、アプリの項目は無い](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/misc-021/04-3-file-after-uninstall-ja.png)

Mac での操作と場所をまとめると、次のとおりです。

| したいこと | 場所（Mac・日本語の表示） |
|---|---|
| 入れる | アーティファクトのタイトルのメニュー > アプリとしてインストール |
| 開く | Dock・Spotlight・ファイル > アプリ > アプリの名前 > 開く |
| Dock に残す | Dock のアイコンのメニュー > Options > Keep in Dock |
| 権限を見る・変える（開いている時） | アプリのメニューバーのアプリの名前 > 権限…、または ファイル > アプリ > アプリの名前 > 権限… |
| 消す | ファイル > アプリ > アプリの名前 > アンインストール… |

## 7. Windows での手順（ヘルプ記事から）

Windows は実機で試していません。次の表は、ヘルプ記事の手順を英語の表記のまま並べたものです。Windows では File のメニューが、メニューのボタン（☰）の中にあります。

| したいこと | 場所（Windows・ヘルプ記事の英語の表記） |
|---|---|
| 入れる | Mac と同じ。アーティファクトのタイトルのメニュー（または右クリック）> Install as app > Install |
| メニューに出ない時の更新 | ☰ > Help > Check for Updates |
| 開く | スタートメニューの Claude Apps フォルダ、デスクトップのショートカット、または ☰ > File > Apps > アプリの名前 > Open |
| 止める（トレイのアイコンを持つアプリ） | トレイのアイコンのメニュー > Exit |
| 権限を見る・変える（開いている時） | アプリのタイトルバーを右クリック（または Alt+Space）> Permissions、または ☰ > File > Apps > アプリの名前 > Permissions |
| 消す | ☰ > File > Apps > アプリの名前 > Uninstall |

Windows でアンインストールすると、ショートカットが消えます。タスクバーやスタートにピン留めしていた場合は、そこからも自分で外します。

## 8. 入れ直すと、前に入力した中身が戻る

6 節でアンインストールした後、同じアーティファクトをもう一度入れ直しました。手順は 3 節と同じで、会話のアーティファクトのメニューから入れるだけです。

入れ直したアプリの入力欄には、前に入れた作業の名前が残っていました。今回は、入力した内容がアンインストールの後も残っていたということです（ヘルプ記事には、入力した内容が残るかどうかは書かれていません）。消したい内容があるなら、アプリの中で消してからアンインストールします。

アプリの中身を直したい時は、会話でアーティファクトを直します。ヘルプ記事によると、自分か他の編集者がアーティファクトを変えると、入れたアプリも変わります。アプリ側で作り直す作業はありません。

## 9. 自分専用か、人に配れるか

入れたアプリは、入れた人の Mac や Windows に入ります。ヘルプ記事にも管理者向けのガイドにも、入れたアプリを人のパソコンへ配る仕組みは書かれていません。人に使ってもらう時は、アーティファクトをいつもの共有（Share）で渡し、受け取った人が自分のパソコンで「Install as app」を選んで入れます。ヘルプ記事にも、持ち主が共有をやめると、アプリが始めたプログラムが止まり、その後にアプリを使う時に多くの場合アンインストールを勧められること、他の編集者が直すとアプリも変わることが書かれていて、人から共有されたアーティファクトも入れられる前提です。

共有できる相手は、プランで決まります（共有のヘルプ記事「[Share artifacts](https://support.claude.com/en/articles/9547008-share-artifacts)」から）。

- **Team・Enterprise**: 招待した人だけ・組織の全員・リンクを知っている人。最初は組織の中だけで、組織の外へ共有してよいかはオーナーが決めます。
- **Pro・Max・Ultra**: 自分だけ・リンクを知っている人。決まった人にはメールで招待します。

配る時に知っておく点は次のとおりです。

- **全部のアクセスは、自分か組織の人が作ったアプリにだけ渡せます。** Free・Pro・Max・Ultra では、自分以外の人は全員「組織の外」です。組織の外の人が作ったアプリが権限を求める時は、警告が出ます（ヘルプ記事）。
- **権限は、使う人がそれぞれ自分で答えます。** 作った人がアーティファクトを直すと、使う人の側でも許可を聞き直されます。
- **Claude やつないだサービスを使う部分は、使う人自身のアカウントで動きます。** リンクから見る組織の外の人やサインインしていない人には、その部分が動きません（共有のヘルプ記事）。

組織の管理者向けのガイド「[Artifacts admin guide](https://support.claude.com/en/articles/16994751-artifacts-admin-guide-for-team-and-enterprise-plans)」に書かれている設定は、Install as app・アプリの権限・コンピュータへの全部のアクセスの 3 つを、使えるようにするか止めるかです。

## 10. 入れる前に知っておく点

ヘルプ記事に書かれている注意は次のとおりです。

- 開く時は、インターネットにつながっていて Claude のデスクトップアプリにサインインしている必要があります。
- アプリを作ることも、アプリが Claude に出す要求も、プランの使用量に数えられます。
- 入れられないアーティファクトもあります。テンプレートから作ったもの（Docs・Slides・Sheets）、古い形式のもの、Claude Cowork で動いているアーティファクトは入れられません。
- HIPAA に対応した設定の組織では使えません（発表の記事では、hybrid deployments も使えないとしています）。
- Claude Code のゼロデータ保持（ZDR）を使っている組織でも、オーナーがアーティファクトと Install as app を有効にすれば入れられますが、入れたアプリはゼロデータ保持の対象外です。
- Claude for Teachers と K-12（小学校から高校まで）の組織では、全部のアクセスは使えません。
- Mac では、Claude がコンピュータを操作している時、入れたアプリは見えますが、Claude からはクリックも入力もできません。

社内のファイルを読ませたいツールは、全部のアクセスが要るかを先に考えます。全部のアクセスは、毎回聞かずにファイルを変えたり消したりできる段階で、アーティファクトを直すたびに聞き直されます。誰がアーティファクトを直し、誰が許可を出すかを決めておきます。

## まとめ

チャットは、聞いたことに答えるのは得意ですが、毎回同じ決まった作業を同じようにやるのは苦手です。Install as app でアプリにすれば、決まった作業が毎回同じ画面と同じ手順で動き、自動で、確実に繰り返せます。今回の集中タイマーも、会話を探して頼み直さなくても、Dock から開けばいつも同じように動きます。

入れるのも消すのも、Claude のアプリのメニューから数回の操作です。人に使ってもらう時は、アーティファクトを共有して、相手が自分のパソコンに入れます。

自分の業務のためのツールを、システム会社に頼んで作ってもらうのではなく、自分で作って、自分で入れられる時代になってきました。毎日くり返している決まった作業を 1 つ選び、アプリにして入れてみてください。

## 用語

- **アーティファクト（Artifact）**: Claude が会話の中で作る、画面つきのツールや文書です。会話の横に開き、そのまま動かせます。
- **Install as app**: アーティファクトを、Mac や Windows のアプリとして入れる Claude のデスクトップアプリの機能です。日本語の表示では「アプリとしてインストール」です。
- **Dock**: Mac の画面の端に並ぶ、アプリのアイコンの列です。
- **メニューバーの項目**: Mac の画面のいちばん上の帯の右側に出る、アプリの小さなアイコンです。Windows ではシステムトレイのアイコンにあたります。
- **システムトレイ**: Windows の画面の右下に並ぶ、動いているアプリの小さなアイコンの置き場です。

---

このシリーズでは、旗艦SaaSの Prompt Flow Studio ほか、AI開発・運用の現場で得た知見を順次公開していきます。
興味のある方は、ぜひ「いいね」と記事の購読をお願いいたします。

Prompt Flow Studio は、複数のAIを1画面で使い、比べ、議論させるマルチAIワークスペースです。詳しくは https://prompt-flow.studio をご覧ください。
そのほかの自社プロダクトは https://www.eln.ne.jp/products にまとめています。

## 筆者について

上原正吉。EarthLink Network Co., Ltd. 代表。AI社員に日々の業務を任せ、開発では人が逐一指示しなくても作業が進むHOOTL（Human Out Of The Loop）の仕組みを自社で実践しています。このブログでは、業務自動化と24時間自動開発のノウハウ、具体的な設定方法、日々の運用で見つかった問題と改善を共有します。

また、AIで業務や開発を組み替えたい会社・チーム向けに、AI活用のコンサルティングも受け付けています。ご相談は [www.eln.ne.jp](https://www.eln.ne.jp) からどうぞ。

---

**EarthLink Network** は、会社の全業務を AI で回すために、必要になったものを自社で作っています。いま作っているプロダクトの一覧と概要は、こちらにまとめています。

→ [EarthLink Network が自社でつくっている18のプロダクト](https://zenn.dev/chooser/articles/in-house-products)

会社と各プロダクトの詳細は、公式サイト [www.eln.ne.jp](https://www.eln.ne.jp) をご覧ください。
