# EasyAudioPlayer Package
EasyAudioPlayerは、Dartで書かれた使いやすいオーディオプレーヤーライブラリです。このパッケージは`audioplayers`をベースに構築され、音楽再生の管理を簡素化し、様々な再生モードと遷移オプションを提供します。

## 特徴
- 🎵 シンプルな音楽再生コントロール（再生、一時停止、再開、シーク）
- 🔄 複数の再生モード（通常、ループ、シャッフル）
- 🔊 音量制御
- 📋 プレイリスト管理と曲の切り替え
- ⏱️ 再生時間とトラック長の追跡
- 🔄 UIの再レンダリングコールバック機能

## インストール
`pubspec.yaml`に依存関係を追加してください：
```yaml
dependencies:
  audioplayers: ^latest_version
  easy_audio_player_package: ^latest_version
```

## 基本的な使い方
### 初期設定
```dart
import 'package:easy_audio_player_package/easy_audio_player.dart';

// 音楽リストの作成
final musicList = [
  Music(name: "曲1", path: "/path/to/song1.mp3", volume: 50),
  Music(name: "曲2", path: "/path/to/song2.mp3", volume: 50),
  Music(name: "曲3", path: "/path/to/song3.mp3", volume: 50),
];

// EasyAudioPlayerのインスタンス作成
final player = EasyAudioPlayer.setMusicList(
  musicList, 
  "マイプレイリスト",
  () => setState(() {}), // UIを更新するためのコールバック
);
```

### 再生コントロール
```dart
// 特定のインデックスから再生開始
player.start(0); // 最初の曲から再生開始

// 一時停止
player.pause();

// 再開
player.resume();

// 特定の位置にシーク（秒単位）
player.seek(30.0); // 30秒の位置にシーク
```

### 音楽モードの切り替え
```dart
// 再生モードの切り替え（NORMAL -> LOOP -> SHUFFLE -> NORMAL）
player.toggleMusicMode();

// 現在の再生モードの取得
MusicMode currentMode = player.musicMode;
```

### 音量制御
```dart
// 音量変更（0-100の範囲）
player.changeVolume(75); // 音量を75%に設定

// 現在の音量を取得
int volume = player.nowVolume;
```

### プレイリストナビゲーション
```dart
// 次の曲へ
player.moveMusicList(Transition.NEXT);

// 前の曲へ
player.moveMusicList(Transition.PREVIOUS);

// ランダムな曲へ
player.moveMusicList(Transition.RANDOM);
```

### 再生状態と情報の取得
```dart
// 現在再生中かどうか
bool isPlaying = player.isPlaying;

// 現在の再生位置（秒）
double currentPosition = player.currentSeconds;

// 現在の曲の長さ（秒）
double duration = player.durationSeconds;

// 現在再生中の曲
Music currentTrack = player.currentMusic;

// プレイリスト名
String playlistName = player.listName;
```

## 高度な使い方
### 異なるインスタンスの作成方法
```dart
// 空のインスタンス
final emptyPlayer = EasyAudioPlayer.getEmptyInstance();

// 再レンダリングコールバック付きのインスタンス
final playerWithCallback = EasyAudioPlayer.getInstanceWithReRender(() {
  // UIを更新するコード
  setState(() {});
});
```

### カスタム音楽リストの作成
```dart
final musicCreator = MusicCreater();

// 個別の曲を作成
final customSong = musicCreator.createMusic("カスタム曲", "/path/to/custom.mp3");

// パスとタイトルのリストから複数の曲を生成
final paths = ["/path/1.mp3", "/path/2.mp3"];
final names = ["曲1", "曲2"];
final customList = musicCreator.generateMusicList(paths, names);
```

## 主要クラスの概要
### EasyAudioPlayer
アプリケーションからのメインインターフェース。音楽再生の管理とコントロールを提供します。

### Music
音楽トラックを表すデータクラス。名前、ファイルパス、デフォルト音量を保持します。

### MusicMode
利用可能な再生モードを定義する列挙型：
- `NORMAL`: 1回再生した後、次の曲へ進む
- `LOOP`: 現在の曲を繰り返し再生
- `SHUFFLE`: 再生完了後、ランダムな曲を選択

### Transition
プレイリスト内の移動タイプを定義する列挙型：
- `NEXT`: 次の曲へ
- `PREVIOUS`: 前の曲へ
- `RANDOM`: ランダムな曲へ

### Index
プレイリスト内の現在の位置を管理するヘルパークラス。インデックスの増加、減少、ランダム選択をサポート。

## 実装詳細
このパッケージは内部で`AudioPlayer`クラスをラップし、シングルトンパターンを使用して音楽再生を管理します。主な実装は以下の通りです：

- `EasyAudioPlayer`: パブリックAPI
- `_AudioPlayerManager`: 内部シングルトンインスタンス
- `MusicModeSetter`: 再生モードの変更ロジック
- `_CurrentListenerResistry`: 現在の再生位置の監視
- `_DurationListenerResistry`: 曲の長さの監視

## 注意点
- デバイス上のファイルを再生するため、適切なファイルパーミッションが必要です
- ファイルパスが正確であることを確認してください
- 短時間に複数回`start()`メソッドを呼び出すと、内部で200msのディレイが発生します

## 例：Flutterウィジェットでの使用
```dart
class EasyAudioPlayerWidget extends StatefulWidget {
  @override
  _EasyAudioPlayerWidgetState createState() => _EasyAudioPlayerWidgetState();
}

class _EasyAudioPlayerWidgetState extends State<EasyAudioPlayerWidget> {
  late EasyAudioPlayer _player;
  
  @override
  void initState() {
    super.initState();
    
    // 音楽リストの設定
    final musicList = [
      Music(name: "曲1", path: "/path/to/song1.mp3", volume: 50),
      Music(name: "曲2", path: "/path/to/song2.mp3", volume: 50),
    ];
    
    // プレーヤーの初期化
    _player = EasyAudioPlayer.setMusicList(
      musicList, 
      "マイプレイリスト",
      () => setState(() {}), // UI更新のコールバック
    );
  }
  
  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Text(_player.currentMusic.name),
        Text("${_player.currentSeconds.toInt()}s / ${_player.durationSeconds.toInt()}s"),
        Row(
          children: [
            IconButton(
              icon: Icon(Icons.skip_previous),
              onPressed: () => _player.moveMusicList(Transition.PREVIOUS),
            ),
            IconButton(
              icon: Icon(_player.isPlaying ? Icons.pause : Icons.play_arrow),
              onPressed: _player.isPlaying ? _player.pause : _player.resume,
            ),
            IconButton(
              icon: Icon(Icons.skip_next),
              onPressed: () => _player.moveMusicList(Transition.NEXT),
            ),
          ],
        ),
        Slider(
          value: _player.currentSeconds,
          max: _player.durationSeconds,
          onChanged: (value) => _player.seek(value),
        ),
      ],
    );
  }
}
```

## ライセンス
このライブラリは[MITライセンス](LICENSE)の下で提供されています。
