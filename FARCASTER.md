# Farcaster Mini App 対応メモ（ドットバトルVS）

GitHub Pages 公開のWebアプリを **Farcaster Mini App** としても動くように改修済み。

## このアプリ特有の事情：プライバシーCSP
`index.html` は `Content-Security-Policy` で外部オリジンへの通信を全面ブロックしている（`script-src 'self'` 等）。そのため Wise World 群のような **esm.sh からのSDK読込は使えない**。代わりに **SDKをローカルに同梱**（`farcaster-sdk.js`）し同一オリジンから読む方式にした。外部通信ゼロを維持。

## 追加・変更したもの
- `index.html` `<head>`：`fc:miniapp` メタタグ（＋旧互換 `fc:frame`）。
- `index.html` `</body>` 直前：**Farcaster内（iframe/webview）でのみ** `farcaster-sdk.js` を遅延ロードして `FCSDK.sdk.actions.ready()` を呼ぶ。通常ブラウザ/PWAでは読み込まない＝軽さ維持。
- `farcaster-sdk.js`：`@farcaster/miniapp-sdk` を esbuild で IIFE バンドル（global `FCSDK`・約628KB）。**再生成手順**＝scratchpadで `npm i @farcaster/miniapp-sdk esbuild` →`entry.js`に `export { sdk } from '@farcaster/miniapp-sdk';`→`esbuild entry.js --bundle --format=iife --global-name=FCSDK --minify --outfile=farcaster-sdk.js`。
- `icons/farcaster-embed.png`：3:2 埋め込みカード画像。
- `.well-known/farcaster.json`：Mini App身分証（**未署名**）。
- `service-worker.js`：CACHE を v13→v14 に更新（SDKと埋め込み画像はオフライン肥大化を避けるため ASSETS には入れていない）。

## 残作業（ユーザー操作）
1. **manifest署名**：`https://farcaster.xyz/~/developers/mini-apps/manifest` で署名し `accountAssociation` を追記（FID必要）。
2. **ドメイン注意**：farcaster.json はドメイン直下でないと「追加・通知・発見」が無効。現状サブパス配下のためカード表示＆起動は可。
3. **検証**：`https://farcaster.xyz/~/developers/mini-apps/embed` に公開URLを貼って確認。
