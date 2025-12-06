# AI Godot Assistant

Gemini APIを使用してGodotエディタを操作できるAIアシスタントプラグインです。



## 機能

- **自然言語でGodotを操作**: AIに自然な日本語で指示を出すことで、Godotエディタの操作を自動化できます
- **ノード操作**: ノードの作成、削除、プロパティ変更
- **スクリプト管理**: スクリプトの作成とアタッチ
- **コンテキスト認識**: 現在のシーンツリーや選択中のノードを考慮した操作
- **Polyhaven統合**: 高品質な無料3Dアセット、テクスチャ、HDRIをダウンロード（実験的機能）

## セットアップ<img width="1919" height="1078" alt="3656" src="https://github.com/user-attachments/assets/027e529c-f4ce-477f-969d-cdaea0faa996" />
<img width="1160" height="717" alt="111" src="https://github.com/user-attachments/assets/33c02d7d-0cba-4e21-92d9-3b49caa9b8b5" />


1. **プラグインのインストール**
   - `addons/ai_godot_assistant`フォルダをプロジェクトの`addons`ディレクトリにコピー

2. **プラグインの有効化**
   - Godotエディタで「プロジェクト」→「プロジェクト設定」→「プラグイン」を開く
   - "AI Godot Assistant"を有効化

3. **Gemini APIキーの設定**
   - [Google AI Studio](https://makersuite.google.com/app/apikey)でAPIキーを取得
   - エディタ右上に表示される「AI Godot Assistant」パネルでAPIキーを入力して保存

## 使い方

### 基本的な使用方法

1. エディタ右上の「AI Godot Assistant」パネルを開く
2. プロンプト欄に自然な日本語で指示を入力
3. 「送信」ボタンをクリック

### プロンプトの例

- **ノード作成**: 「Playerという名前のNode2Dを作成して」
- **複数ノード作成**: 「Enemyという名前のCharacterBody2Dを3つ作成して」
- **プロパティ設定**: 「Playerの位置を(100, 200)に設定して」
- **スクリプト作成**: 「Playerに移動スクリプトを追加して」
- **複雑な操作**: 「ゲームシーンを作成して。Playerノード、Enemyノード、背景ノードを追加してください」
- **テーマ重視**: 「宇宙サバイバルゲームを作成。背景は宇宙（Polyhaven HDRI）、敵はタコ型（複数のSphere）、攻撃は青いレーザー（細長いCylinder）」

**重要**: テーマや見た目を詳しく説明すると、AIはより忠実に実装します！

## サポートされているコマンド

AIは以下のコマンドを使用してGodotを操作します：

### CREATE_NODE
新しいノードを作成します。
```
CREATE_NODE|ノードタイプ|ノード名|親ノードパス
```

### DELETE_NODE
ノードを削除します。
```
DELETE_NODE|ノードパス
```

### SET_PROPERTY
ノードのプロパティを設定します。
```
SET_PROPERTY|ノードパス|プロパティ名|値
```

### ADD_SCRIPT
既存のスクリプトをノードにアタッチします。
```
ADD_SCRIPT|ノードパス|スクリプトパス
```

### DOWNLOAD_POLYHAVEN
Polyhaven（旧Poly Haven）から高品質な無料アセットをダウンロードします。
```
DOWNLOAD_POLYHAVEN|アセットタイプ|アセット名|解像度
```
例：
- `DOWNLOAD_POLYHAVEN|hdri|industrial_sunset_puresky|2k`
- `DOWNLOAD_POLYHAVEN|texture|rock_face|2k`
- `DOWNLOAD_POLYHAVEN|model|potted_plant|1k`

**アセットタイプ**：
- `hdri`: 環境マップ（WorldEnvironment用）
- `texture`: PBRテクスチャ
- `model`: 3Dモデル（GLB形式）

**解像度**: `1k`, `2k`, `4k`（推奨: 2k）

**動作**:
1. Polyhaven APIからアセット情報を取得
2. 適切なファイル（HDRI: .exr、テクスチャ: .jpg、モデル: .glb）を選択
3. ファイルを自動的にダウンロード
4. `res://assets/polyhaven/`フォルダに保存
5. Godotエディタが自動的にファイルを認識してインポート

**注意**: 大きなファイル（数MB〜数十MB）のダウンロードには時間がかかります。

### APPLY_HDRI
WorldEnvironmentノードにHDRIファイルを自動的に設定します。
```
APPLY_HDRI|WorldEnvironmentノードパス|HDRIファイルパス
```
例：
- `APPLY_HDRI|WorldEnvironment|res://assets/polyhaven/industrial_sunset_puresky_2k.exr`

**動作**:
1. Environmentリソースを自動作成
2. Sky、PanoramaSkyMaterialを自動作成
3. 指定されたHDRIファイルを環境マップとして設定
4. 背景モードを自動的にSkyに変更

**使用例（完全な流れ）**:
```
DOWNLOAD_POLYHAVEN|hdri|urban_street_01|2k
CREATE_NODE|WorldEnvironment|WorldEnvironment|.
APPLY_HDRI|WorldEnvironment|res://assets/polyhaven/urban_street_01_2k.exr
```

### CREATE_SCRIPT
新しいスクリプトを作成します。
```
CREATE_SCRIPT|スクリプトパス|スクリプト内容
```

### SET_RESOURCE
ノードのリソースプロパティを設定します（CollisionShape、MeshInstanceなど）。
```
SET_RESOURCE|ノードパス|プロパティ名|リソースタイプ|パラメータ
```
例：
- `SET_RESOURCE|Player/CollisionShape|shape|BoxShape3D|size=Vector3(1,2,1)`
- `SET_RESOURCE|Player/Mesh|mesh|BoxMesh|size=Vector3(1,2,1)`
- `SET_RESOURCE|Ground/CollisionShape|shape|BoxShape3D|size=Vector3(50,1,50)`

## サポートされている値の型

- **数値**: `123`, `45.67`
- **真偽値**: `true`, `false`
- **Vector2**: `Vector2(100, 200)`
- **Vector3**: `Vector3(1, 2, 3)`
- **Color**: `Color(1, 0, 0)` (RGB) または `Color(1, 0, 0, 0.5)` (RGBA)
- **文字列**: その他すべて

## 📚 参考ドキュメント

AI Godot Assistantは、Godot 4.x公式ドキュメントのベストプラクティスに従ってコードを生成します：

- [Godot 4.x 公式ドキュメント（日本語）](https://docs.godotengine.org/ja/4.x/)
- [はじめてのGodot](https://docs.godotengine.org/ja/4.x/getting_started/introduction/introduction_to_godot.html)
- [最初の2Dゲーム](https://docs.godotengine.org/ja/4.x/getting_started/first_2d_game/)
- [最初の3Dゲーム](https://docs.godotengine.org/ja/4.x/getting_started/first_3d_game/)
- [GDScriptリファレンス](https://docs.godotengine.org/ja/4.x/tutorials/scripting/gdscript/)

**推奨プロンプト例**: `PROMPT_EXAMPLES.md`を参照してください。

## トラブルシューティング

### APIキーエラー
- APIキーが正しく設定されているか確認してください
- Google AI Studioでプロジェクトが有効になっているか確認してください

### ノードが作成されない
- シーンが開いているか確認してください
- ノードタイプ名が正しいか確認してください（例：`Node2D`, `Sprite2D`, `CharacterBody2D`）

### スクリプトの問題
- スクリプトパスは`res://`から始まる完全パスを使用してください
- ファイルの拡張子は`.gd`にしてください

### 壁抜け・床抜けが発生する
Geminiに以下のように指示してください：
```
物理設定を追加してください。
プレイヤー、地面、敵、弾のcollision_layerとcollision_maskを設定して、
正しく衝突するようにしてください。
```

AIが自動的に適切な物理レイヤーを設定します。

## 対応モデル（2025年12月版）

### デフォルトで選択可能なモデル
- **Gemini 2.0 Flash (実験版)**: デフォルト、動作確認済み
- **Gemini Exp 1206**: 実験モデル
- **Gemini 1.5 Flash**: 安定版、推奨
- **Gemini 1.5 Pro**: 高性能安定版
- **Gemini 1.5 Flash-8B**: 軽量版
- **Gemini Pro (レガシー)**: 旧バージョン

**注意**: Gemini 2.5、Gemini 3のAPI名は2025年12月時点で不明確です。404エラーが出た場合、カスタムモデル入力で別の名前を試してください。

### カスタムモデルの使用
新しいモデルが利用可能になった場合、UIの「カスタムモデル名」欄に入力して試すことができます。
試す価値のある候補: `gemini-2.0-flash-thinking-exp`, `gemini-exp-1206`, `gemini-pro`, `gemini-1.5-pro-latest`

## 制限事項

- モデル名は予告なく変更される可能性があります
- 非常に複雑な操作は複数のステップに分けて実行する必要があります
- エディタプラグインとして動作するため、ゲーム実行中は使用できません

## ライセンス

MIT License

## 開発者向け情報

### プロジェクト構造
```
addons/ai_godot_assistant/
├── plugin.cfg              # プラグイン設定
├── plugin.gd               # EditorPluginメインクラス
├── ai_controller.gd        # Gemini API通信管理
├── godot_operator.gd       # Godot操作実行クラス
├── config.cfg              # APIキー設定（自動生成）
├── config.cfg.example      # 設定テンプレート
├── ui/
│   ├── ai_dock.tscn        # UIシーン
│   └── ai_dock.gd          # UIスクリプト
└── README.md               # このファイル
```

### 拡張方法

新しいコマンドを追加するには：

1. `ai_controller.gd`の`_build_prompt_with_context()`にコマンド説明を追加
2. `ai_controller.gd`の`_execute_ai_commands()`にコマンド処理を追加
3. `godot_operator.gd`に実際の操作関数を実装

## 貢献

バグ報告や機能リクエストは大歓迎です！

## 更新履歴

### v1.1 (2025-12-04)
- Gemini 3対応（Gemini 2.5 Flash、Gemini 3 Flashなど複数モデル対応）
- カスタムモデル名入力機能
- SET_RESOURCEコマンド追加（CollisionShape、Mesh等のリソース設定）
- Polyhaven API統合（実験的機能）
- 子ノードパス参照の改善
- スクリプト改行処理の強化
- Godot公式ドキュメント参照をシステムプロンプトに追加
- 操作説明の自動表示
- デバッグログの強化

### v1.0 (2025-12-03)
- 初版リリース
- 基本的なノード操作機能
- Gemini API統合
- 自然言語プロンプト処理
