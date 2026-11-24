---
title: "Gemini の Gem が使えなくなる前に棚卸しする ── 使っている Gem は、文書を検索しても分からない。持ち主に一覧を見てもらう"
emoji: "📝"
type: "tech"
topics: ["ai", "gemini", "googleworkspace", "業務自動化", "aiエージェント"]
published: true
---

<!-- このファイルは gen-publish.mjs が生成した発行用スケルトン。翻訳(海外媒体)は Claude が en.md 経由で行う。 -->
<!-- gen-publish:src-sha256=6830205f570b349edd0711e8fc40ad6fc2924db76ce87f972d13a2a69dba1fc8 -->

上原正吉（EarthLink Network Co., Ltd.）。Claude Codeを開発の主体に据え、20を超えるプロダクトを1人で同時に開発・運用しています。これは、その現場の実測記です。

<!-- TODO(Claude): この zenn 版は媒体トーン定義（/docs/platform-tone）に合わせてトーン・長さを調整する。方向性: 技術・実装重視。設計判断と数字を厚く。開発者向け。 以下は基となる本文。調整後にこのコメントを消す。 -->

# Gemini の Gem が使えなくなる前に棚卸しする ── 使っている Gem は、文書を検索しても分からない。持ち主に一覧を見てもらう

![Gemini の Gem が使えなくなる前に棚卸しする ── 使っている Gem は、文書を検索しても分からない。持ち主に一覧を見てもらう](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/aico-007/hero-ogp.png)

Google の Gemini で、指示をまとめて保存しておける「Gem」が終わります。仕事向けの Google Workspace では、早ければ 2027 年 3 月 1 日に、Gem を作ることも、直すことも、使うこともできなくなります。

移行の前に、どの Gem を誰が何の仕事に使っているかを数えておきたい。自社の AI 社員（ブログ担当）が、手元の文書から棚卸しを試しました。ところが「Gem」の語で検索して数えると、「Gemini」の話をしている下書きまで数に入り、実際より多く出ました。

## 結論

- **仕事向けの Workspace では、早ければ 2027 年 3 月 1 日に Gem が使えなくなります。** 残った Gem は Gemini アプリの「スキル」の下書きに自動で移りますが、Workspace のアプリでは使えないので、使うなら Workspace でも作り直します。
- **「Gem」の語で文書を検索して数えると、「Gemini」の文書も混ざり、実際より多く出ます。** 検索で出た 11 件の下書きのうち、Gem の話は 2 件でした。その 2 件も、Google が紹介した Gem の事例を読んだもので、自社で使っている Gem は文書からは見つかりませんでした。
- **持ち主と使っている仕事は、文書からは分かりません。** 棚卸しは Gem 1 つごとに、持ち主・アカウントの種類・使っている仕事を組にして数えます。

## 本文（読了 約 5 分）

## 1. Gem はいつ、どうなるのか

Google は、Gem の代わりに「スキル」を用意し、Gem を少しずつ終わらせると発表しています。Workspace のアップデートを知らせる公式ブログの記事「[Introducing skills in the Gemini app and Workspace, plus what's next for Gems](https://workspaceupdates.googleblog.com/2026/09/skills-gemini-app-workspace.html)」の「Key dates」の節が、次の画面です。日付は変わることがあるので、移行の予定を決める時は、この記事を開いて確かめてください。

![Google Workspace Updates の公式ブログ「Introducing skills in the Gemini app and Workspace, plus what's next for Gems」の「Key dates」の節。November 17, 2026 に Gem は Gemini アプリの設定の画面へ移るが作成・編集・利用は続けられること、No sooner than March 1, 2027（business and enterprise only）に Gem は作成・編集・利用ができなくなり、Workspace Studio の Ask a Gem のステップも止まり、残った Gem は Gemini アプリのスキルの下書きに自動で移ること、教育向けは No sooner than June 1, 2027 であること、自動の移行の詳細は少なくとも 30 日前に管理者へ知らせることが書かれている。](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/aico-007/google-key-dates.png)

要点は次の 3 つです。

- **2026 年 11 月 17 日から、Gem は Gemini アプリの設定の画面に置かれます。** そのあとも、しばらくは作ることも使うこともできます。
- **仕事向け（Business・Enterprise）は 2027 年 3 月 1 日より前には止まりません。** 原文は「No sooner than」で、「この日より早くはない」という書き方です。この日に止まると決まったわけではありませんが、早ければこの日からです。教育向けは 2027 年 6 月 1 日より前には止まりません。
- **自動の移行の細かい内容は、少なくとも 30 日前に管理者に知らされます。**

移ったあとの扱いは、同じ記事の「What happens to Gems?」の節に書かれています。

![同じ公式ブログの「What happens to Gems?」の節。今は Gemini アプリで Gem を作って使い続けられるが、Workspace Studio で「Ask a Gem」のステップを持つフローは新しく作れないこと（既存のフローは動き続ける）、今後数か月で Gem の対応を順に外し、Gemini アプリに残った Gem はスキルの下書きに自動で移すこと、早く移したい場合は自分で Gem をスキルとして作り直せること、Gemini アプリのスキルは Workspace に自動では出てこないので、Workspace のアプリで使うなら Workspace でも作り直す必要があることが書かれている。](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/aico-007/google-what-happens.png)

ここで注意したいのは、自動で移るのは「Gemini アプリのスキルの下書き」だという点です。

- **Workspace のアプリには出てきません。** Gmail や Docs などの Workspace のアプリで使うなら、Workspace 側でも作り直します。
- **Workspace Studio の「Ask a Gem」のステップは止まります。** Gem を呼んでいる自動の流れ（フロー）は、新しく作れなくなっていて、止まる日からは動かなくなります。
- **移るのは下書きです。** そのまま前と同じ結果が出るかは、移したあとに人が確かめます。

つまり、名前と指示が移ることと、その Gem を使っていた仕事がそのまま続くことは別です。だから、どの Gem をどの仕事で使っているかを、先に数えておく必要があります。

## 2. 「Gem」の語で文書を検索して数えると、「Gemini」の文書も混ざって多く出る

棚卸しを試したのは、自社の AI 社員のうちブログ担当です。Gem の一覧は開かずに、担当の作業場所にある記事の下書き（Markdown の文書）から、Gem に触れている文書を拾う作業です。文書を探すのは、手順書や社内のメモに Gem を使う仕事が書かれていれば、Gem の一覧だけでは分からない「どの仕事で使っているか」の手がかりになるからです。

最初は「Gem」という文字を含む文書を探しました。当たったのは 11 件です。ところが、「Gemini」という言葉にも「Gem」の 3 文字が入っています。この 11 件を「移行が必要な Gem は 11 個」と報告すると、文字の件数を Gem の数として扱うことになります。

そこで、前後に英字が続かない「Gem」か「Gems」だけを探す条件に変えました。当たったのは 2 件です。担当が残した結果をもとに、同じ 11 件を私たちの側でもう一度数え直しても、同じ数でした。

![端末で recount.py を実行した画面。最初の検索で当たった 11 件の下書きを 1 行ずつ並べ、「Gem を含む」の列は 11 件すべて ○、「単語の Gem」の列は care-record.md と care-time.md の 2 件だけが ○で、残り 9 件は ×。最後の行は「「Gem」を含む: 11 件 / 単語の「Gem」「Gems」: 2 件」。](https://raw.githubusercontent.com/EarthLinkNetwork/blog_public/main/images/aico-007/recount-ja.png)

残りの 9 件は、「Gemini」という言葉の中の「Gem」で当たっただけでした。では、2 件は自社の Gem だったのか。中を読むと、2 件とも、Google が介護の現場向けに紹介した Gem の事例を読んで書いた下書きでした。自社で作って仕事に使っている Gem ではありません。

11 件から 2 件に減ったのは、探す条件を狭めたからです。数え方が正しくなったわけではありません。「Gemini で作った自分用のアシスタント」のように別の言い方で書かれた文書は、この条件では拾えません。文書の中の言葉を数えても、Gem の数にはならないのです。

## 3. 誰の Gem で、どの仕事に使っているかは、文書には書かれていない

担当は、棚卸しの結果に「Gem の持ち主」「アカウントの種類」「移行の状態」の欄を作りました。そして、どの欄も「未確認」のまま残しました。

```json
{
  "broad_match": true,
  "gem_word": true,
  "owner_of_live_gem": "未確認",
  "account_type": "未確認",
  "migration_state": "実資産未照合"
}
```

（care-record.md の行の一部の項目です。broad_match は「Gem」の文字を含むか、gem_word は単語の「Gem」「Gems」を含むか、owner_of_live_gem は Gem の持ち主、account_type はアカウントの種類、migration_state は移行の状態です。「実資産未照合」は、実物の Gem と照らし合わせていないという意味です）

下書きのファイル名や書いた人から、持ち主を推測して埋めることはしませんでした。ここを埋めないことが大事です。止まる日は、仕事向けか教育向けかで違います。どのアカウントの Gem かが分からなければ、いつまでに移すかも決められません。

推測で埋めた表を渡されると、確かめる人は 1 行ずつ疑って調べ直すことになります。空いた欄が残っていれば、人に聞くことが「移行してよいですか」から「この文書が指している Gem はどれですか」に変わります。答える側も、何を調べればよいかが分かります。

## 4. 棚卸しは、Gem 1 つごとに組にして数える

Gem の実物の一覧は、持ち主がそれぞれ Gemini アプリで見ます。Google のヘルプ「[Use Gems in Gemini Apps](https://support.google.com/gemini/answer/15146780?hl=en)」では、左のメニューの「Gems」を開くと「My Gems」に自分の Gem が並びます（2026 年 11 月 17 日からは、設定の画面から開きます）。管理者向けの移行の案内「[About the transition from Gems to skills](https://knowledge.workspace.google.com/admin/generative-ai/gemini-app/about-the-transition-to-skills)」も、試しに作った Gem や使っていない Gem を、移行の前に利用者に消してもらうよう勧めています。

そこで、持ち主に自分の Gem の一覧を見てもらい、Gem 1 つごとに次を組にして書きます。

| 書くこと | 理由 |
|---|---|
| Gem の名前と持ち主 | 移す作業をする人を決めるため |
| アカウントの種類（仕事向け・教育向け） | 止まる日が違うため |
| 使っている仕事と、使う人 | 移したあとに、同じ仕事ができるかを試すため |
| どこから使っているか（Gemini アプリ・Workspace のアプリ・Workspace Studio のフロー） | Workspace で使うなら作り直しが要り、Ask a Gem のフローは止まるため |
| 移したあとに確かめる例（入力と、期待する結果） | 下書きのスキルが前と同じ結果を出すかを比べるため |

1 つの Gem を、いくつもの仕事で使っていることもあります。Gem の数と、使っている仕事の数は違います。進み具合を数える時は、どちらを数えているかを書いておきます。

## まとめ

Gemini の Gem は、仕事向けの Workspace では早ければ 2027 年 3 月に使えなくなり、残った Gem は Gemini アプリのスキルの下書きに移ります。Workspace のアプリや Workspace Studio のフローで使っているなら、自動の移行だけでは続きません。

棚卸しを AI に任せる時は、文書の文字の件数を Gem の数として受け取らないこと。持ち主・アカウントの種類・使っている仕事が埋まらない欄は、推測で埋めずに空けたまま渡させること。そうすれば、人が答えるべき質問がはっきりします。

## 用語

- **AI 社員**: 自社で、役割ごとに仕事を任せている AI の担当です。この記事のブログ担当は、記事の下書きを書く AI 社員です。
- **Gem（ジェム）**: Gemini に、決まった仕事の指示をまとめて保存しておく機能です。毎回同じ指示を書かずに使えます。
- **スキル**: Gem の代わりに Google が用意した機能です。指示を保存しておく点は同じで、1 回の依頼で複数のスキルを組み合わせて使えます。
- **Google Workspace**: Gmail・Docs などを会社や学校で使うための Google のサービスです。
- **Workspace Studio**: Workspace の中で、決まった流れの作業（フロー）を自動で動かす機能です。「Ask a Gem」は、そのフローの途中で Gem を呼ぶステップです。

---

このシリーズでは、個人事業主や小さな会社が、普段の仕事を AI の担当（AI 社員）に分けて任せ、人は確認と承認だけをする形を、順に作っていきます。始めはサブスクの範囲で、費用を式で見積もりながら進めます。Paperclip のように担当と承認で AI を束ねると費用は大きくなりますが、AI を直接使う値段に近づける方法も示します。シリーズの最後には、私が使っている Paperclip の設定・指示書・おすすめの形を無料で配布する予定です。
興味のある方は、ぜひ「いいね」と記事の購読をお願いいたします。

自社プロダクトは https://www.eln.ne.jp/products にまとめています。

## 筆者について

上原正吉。EarthLink Network Co., Ltd. でAI開発をしています。2025年からClaude Codeを開発の主体に据え、今は20を超えるプロダクトを1人で同時に開発・運用しています。この連載では、その現場で実際に起きたこと（うまくいったことも、失敗も）を、数字と一緒に書いていきます。

また、AIで業務や開発を組み替えたい会社・チーム向けに、AI活用のコンサルティングも受け付けています。ご相談は [www.eln.ne.jp](https://www.eln.ne.jp) からどうぞ。

---

**EarthLink Network** は、会社の全業務を AI で回すために、必要になったものを自社で作っています。いま作っているプロダクトの一覧と概要は、こちらにまとめています。

→ [EarthLink Network が自社でつくっている18のプロダクト](https://zenn.dev/chooser/articles/in-house-products)

会社と各プロダクトの詳細は、公式サイト [www.eln.ne.jp](https://www.eln.ne.jp) をご覧ください。
