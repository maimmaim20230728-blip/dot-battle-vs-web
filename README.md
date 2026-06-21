# ドットバトルVS — Web版（PWA）

ネイティブ版 `dot_battle_vs`（Google Play）と**同一機能**のブラウザ版。サーバー不要・端末内処理・完全無料・広告なし・オフライン動作。

- 写真／絵を 16×16 ドット絵の戦士に変換してバトル（カメラ・画像処理はすべてブラウザ内で完結）
- モード：①ひとりで（ソロ5連戦）②ふたりで（1台ホットシート）③2台で（QR対戦）
- PWA：ホーム画面に追加可・オフライン起動可

## 構成
```
dot_battle_vs_web/
├── index.html            # ゲーム本体（自己完結。QR/音/i18n/全機能インライン）＋PWA登録
├── manifest.webmanifest  # PWA設定（相対パス）
├── service-worker.js     # オフラインキャッシュ（cache-first）
├── icons/                # 192 / 512 / maskable-512
└── .nojekyll             # GitHub Pages の Jekyll 処理を無効化
```
※ すべて**相対パス**。GitHub Pages のサブパス（`https://<user>.github.io/<repo>/`）でもそのまま動作します。

## GitHub Pages へのデプロイ
1. このフォルダの中身をリポジトリに push。
2. リポジトリ Settings → Pages → Source を「Deploy from a branch」、対象ブランチ／ルート（または `/docs`）を指定。
3. 数分後に公開 URL で起動。HTTPS なので Service Worker・カメラとも有効。

## 動作確認（ローカル）
`file://` 直開きでは Service Worker が動きません。簡易サーバーで開いてください。
```
npx serve .        # または: python -m http.server 8080
```
`http://localhost:****/` を開く。

## 更新手順
1. ネイティブ版でゲームを更新したら、`dot_battle_vs/index.html`（`node build.js` の成果物）を本フォルダの `index.html` に上書きコピーし、`<head>` の manifest 行と `</body>` 直前の Service Worker 登録の2か所を再付与（既存を残したまま上書きするだけ）。
2. `service-worker.js` の `CACHE`（例 `dbvs-web-v1`）を **+1**（`v2`…）。これで利用者の端末に自動で新版が反映されます。

## 備考
- 既存の `dot_battle_vs`（ネイティブ／Capacitor）には一切影響しません（本フォルダは独立）。
- 機能はストア版と同一。ローカル保存などストア版に無い機能は入れていません。
