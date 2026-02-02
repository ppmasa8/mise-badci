# ワークショップ用CI変更点（意図的な遅延）

このリポジトリの `test.yml` は、CIパフォーマンス改善の題材として意図的に重くしています。

## 追加した「遅くする」変更

- Rustのビルドキャッシュを削除（`Swatinem/rust-cache` を全ジョブから除去）
- `.github/actions/mise-tools` の `actions/cache` を削除
- 全ジョブでフルクローン（`fetch-depth: 0`）
- 全ジョブに `cargo clean` を追加して増分ビルドを無効化
- `lint` と `windows-e2e` でアーティファクト再利用をやめ、毎回ビルド
- 全ジョブで `npm install` を実行
- Linuxジョブで Lua 5.1 の開発パッケージを追加インストール
- 並列数を減らすため、2レーン並列に変更
  - Linuxレーン: `build-ubuntu → unit → lint`
  - Windowsレーン: `build-windows → windows-unit → windows-e2e`

## 触ったファイル

- `.github/workflows/test.yml`
- `.github/actions/mise-tools/action.yml`

## 改善案（ワークショップで戻す候補）

- キャッシュの復活
  - `.github/actions/mise-tools/action.yml` の `actions/cache` を戻す
  - `test.yml` の `Swatinem/rust-cache` を戻す
- フルクローンの廃止
  - `fetch-depth: 0` を削除して浅いクローンに戻す
- `cargo clean` の削除
  - 増分ビルドを活かす
- アーティファクト再利用の復活
  - `lint` と `windows-e2e` でビルド成果物を使う
- `npm install` の削減
  - 必要なジョブに限定し、npmキャッシュも導入
- 並列数の見直し
  - 2レーン構成を見直し、必要に応じて同時実行を増やす
