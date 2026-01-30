[![Jitpack package repository - ESCPOS-ThermalPrinter-Android v3.3.0](https://jitpack.io/v/DantSu/ESCPOS-ThermalPrinter-Android.svg)](https://jitpack.io/#DantSu/ESCPOS-ThermalPrinter-Android/3.3.0)
[![](https://jitpack.io/v/DantSu/ESCPOS-ThermalPrinter-Android/month.svg)](https://jitpack.io/#DantSu/ESCPOS-ThermalPrinter-Android/3.3.0)
[![](https://jitpack.io/v/DantSu/ESCPOS-ThermalPrinter-Android/week.svg)](https://jitpack.io/#DantSu/ESCPOS-ThermalPrinter-Android/3.3.0)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

# ESC/POS サーマルプリンタ用 Android ライブラリ

Bluetooth・TCP・USB 対応の ESC/POS サーマルプリンタで印刷するための Android 用ライブラリです。

## ✨ プロジェクトの支援

⭐ このリポジトリにスターを付けると、ライブラリの認知度向上にご協力いただけます。

## 目次

- [対応 Android バージョン](#対応-android-バージョン)
- [動作確認済みプリンタ](#動作確認済みプリンタ)
- [試してみる](#試してみる)
- [インストール](#インストール)
- [Bluetooth](#bluetooth)
  - [Bluetooth の権限](#bluetooth-の権限)
  - [Bluetooth のコード例](#bluetooth-のコード例)
- [TCP](#tcp)
  - [TCP の権限](#tcp-の権限)
  - [TCP のコード例](#tcp-のコード例)
- [USB](#usb)
  - [USB の権限](#usb-の権限)
  - [USB のコード例](#usb-のコード例)
- [文字コード](#文字コード)
- [フォーマット済みテキスト：構文ガイド](#フォーマット済みテキスト構文ガイド)
- [クラス一覧](#クラス一覧)
  - [BluetoothPrintersConnections](#class--comdantsuescposprinterconnectionbluetoothbluetoothprintersconnections)
  - [UsbPrintersConnections](#class--comdantsuescposprinterconnectionusbusbprintersconnections)
  - [EscPosPrinter](#class--comdantsuescposprinterescposprinter)
  - [PrinterTextParserImg](#class--comdantsuescposprintertextparserprintertextparserimg)
  - [EscPosCharsetEncoding](#class--comdantsuescposprinterescposcharsetencoding)
- [本ライブラリを利用しているプロジェクト](#本ライブラリを利用しているプロジェクト)
- [コントリビューション](#コントリビューション)


## 対応 Android バージョン

SDK バージョン 16（Android 4.1 Jelly Bean）以上を対象としています。


## 動作確認済みプリンタ

1. [HOIN HOP H58 Thermal Printer ESC/POS](https://www.gearbest.com/printers/pp_662658.html)
2. [XPRINTER XP-P300](https://xprinter.vn/xprinter-xp-p300-may-in-hoa-don-di-dong-bluetooth/)
3. [MUNBYN IMP001](https://www.munbyn.com/collections/portable-receipt-printer/products/58mm-bluetooth-thermal-printer-imp001)
4. [JP-Q2 POS Terminal PDA](https://www.aliexpress.com/item/32971775060.html)（内蔵プリンタは Bluetooth デバイスとして設定）
5. [MUNBYN ITPP047](https://www.munbyn.com/products/munbyn-itpp047-wifi-thermal-printer)（USB 接続で動作確認済み）

## 試してみる

次の手順で簡単に動作確認できます。

- 任意のディレクトリを作成し、その中でターミナルを開く
- `git clone https://github.com/DantSu/ESCPOS-ThermalPrinter-Android.git .` を実行
- Android Studio でそのディレクトリを開く
- ビルドして実行する

## インストール

**Step 1.** ビルドファイルに [JitPack](https://jitpack.io/#DantSu/ESCPOS-ThermalPrinter-Android/3.3.0) のリポジトリを追加します。ルートの `/build.gradle` の `repositories` の末尾に次を追加してください。

```
allprojects {
    repositories {
        ...
        maven { url 'https://jitpack.io' }
    }
}
```

**Step 2.** `/app/build.gradle` に依存関係を追加します。

```
dependencies {
    ...
    implementation 'com.github.DantSu:ESCPOS-ThermalPrinter-Android:3.3.0'
}
```

## Bluetooth

### Bluetooth の権限

`AndroidManifest.xml` に次の権限を記述してください。  
`<uses-permission android:name="android.permission.BLUETOOTH" />`、`<uses-permission android:name="android.permission.BLUETOOTH_ADMIN" />`、`<uses-permission android:name="android.permission.BLUETOOTH_CONNECT" />`、`<uses-permission android:name="android.permission.BLUETOOTH_SCAN" />`

アプリ側では、次のように Bluetooth の権限を確認してください。

```java
if (android.os.Build.VERSION.SDK_INT < android.os.Build.VERSION_CODES.S && ContextCompat.checkSelfPermission(this, Manifest.permission.BLUETOOTH) != PackageManager.PERMISSION_GRANTED) {
    ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.BLUETOOTH}, MainActivity.PERMISSION_BLUETOOTH);
} else if (android.os.Build.VERSION.SDK_INT < android.os.Build.VERSION_CODES.S && ContextCompat.checkSelfPermission(this, Manifest.permission.BLUETOOTH_ADMIN) != PackageManager.PERMISSION_GRANTED) {
    ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.BLUETOOTH_ADMIN}, MainActivity.PERMISSION_BLUETOOTH_ADMIN);
} else if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.S && ContextCompat.checkSelfPermission(this, Manifest.permission.BLUETOOTH_CONNECT) != PackageManager.PERMISSION_GRANTED) {
    ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.BLUETOOTH_CONNECT}, MainActivity.PERMISSION_BLUETOOTH_CONNECT);
} else if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.S && ContextCompat.checkSelfPermission(this, Manifest.permission.BLUETOOTH_SCAN) != PackageManager.PERMISSION_GRANTED) {
    ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.BLUETOOTH_SCAN}, MainActivity.PERMISSION_BLUETOOTH_SCAN);
} else {
    // ここに処理を記述
}
```

### Bluetooth のコード例

Activity に記述する例は以下のとおりです。

```java
EscPosPrinter printer = new EscPosPrinter(BluetoothPrintersConnections.selectFirstPaired(), 203, 48f, 32);
printer
    .printFormattedText(
        "[C]<img>" + PrinterTextParserImg.bitmapToHexadecimalString(printer, this.getApplicationContext().getResources().getDrawableForDensity(R.drawable.logo, DisplayMetrics.DENSITY_MEDIUM))+"</img>\n" +
        "[L]\n" +
        "[C]<u><font size='big'>ORDER N°045</font></u>\n" +
        "[L]\n" +
        "[C]================================\n" +
        "[L]\n" +
        "[L]<b>BEAUTIFUL SHIRT</b>[R]9.99e\n" +
        "[L]  + Size : S\n" +
        "[L]\n" +
        "[L]<b>AWESOME HAT</b>[R]24.99e\n" +
        "[L]  + Size : 57/58\n" +
        "[L]\n" +
        "[C]--------------------------------\n" +
        "[R]TOTAL PRICE :[R]34.98e\n" +
        "[R]TAX :[R]4.23e\n" +
        "[L]\n" +
        "[C]================================\n" +
        "[L]\n" +
        "[L]<font size='tall'>Customer :</font>\n" +
        "[L]Raymond DUPONT\n" +
        "[L]5 rue des girafes\n" +
        "[L]31547 PERPETES\n" +
        "[L]Tel : +33801201456\n" +
        "[L]\n" +
        "[C]<barcode type='ean13' height='10'>831254784551</barcode>\n" +
        "[C]<qrcode size='20'>https://dantsu.com/</qrcode>"
    );
```

上記コードで印刷したレシートの例です。

![印刷レシートの例](https://dantsu.com/files/receipt-thermal-printer.png?1)

## TCP

### TCP の権限

`AndroidManifest.xml` に `<uses-permission android:name="android.permission.INTERNET"/>` を追加してください。

### TCP のコード例

Activity に記述する例は以下のとおりです。

```java
new Thread(new Runnable() {
    public void run() {
        try {
            EscPosPrinter printer = new EscPosPrinter(new TcpConnection("192.168.1.3", 9300, 15), 203, 48f, 32);
            printer
                .printFormattedText(
                    "[C]<img>" + PrinterTextParserImg.bitmapToHexadecimalString(printer, getApplicationContext().getResources().getDrawableForDensity(R.drawable.logo, DisplayMetrics.DENSITY_MEDIUM)) + "</img>\n" +
                    "[L]\n" +
                    "[C]<u><font size='big'>ORDER N°045</font></u>\n" +
                    "[L]\n" +
                    "[C]================================\n" +
                    "[L]\n" +
                    "[L]<b>BEAUTIFUL SHIRT</b>[R]9.99e\n" +
                    "[L]  + Size : S\n" +
                    "[L]\n" +
                    "[L]<b>AWESOME HAT</b>[R]24.99e\n" +
                    "[L]  + Size : 57/58\n" +
                    "[L]\n" +
                    "[C]--------------------------------\n" +
                    "[R]TOTAL PRICE :[R]34.98e\n" +
                    "[R]TAX :[R]4.23e\n" +
                    "[L]\n" +
                    "[C]================================\n" +
                    "[L]\n" +
                    "[L]<font size='tall'>Customer :</font>\n" +
                    "[L]Raymond DUPONT\n" +
                    "[L]5 rue des girafes\n" +
                    "[L]31547 PERPETES\n" +
                    "[L]Tel : +33801201456\n" +
                    "[L]\n" +
                    "[C]<barcode type='ean13' height='10'>831254784551</barcode>\n" +
                    "[C]<qrcode size='20'>https://dantsu.com/</qrcode>"
                );
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}).start();
```

## USB

### USB の権限

`AndroidManifest.xml` に `<uses-feature android:name="android.hardware.usb.host" />` を追加してください。

アプリ側では、次のように USB の権限を処理してください。

```java
private static final String ACTION_USB_PERMISSION = "com.android.example.USB_PERMISSION";
private final BroadcastReceiver usbReceiver = new BroadcastReceiver() {
    public void onReceive(Context context, Intent intent) {
        String action = intent.getAction();
        if (MainActivity.ACTION_USB_PERMISSION.equals(action)) {
            synchronized (this) {
                UsbManager usbManager = (UsbManager) getSystemService(Context.USB_SERVICE);
                UsbDevice usbDevice = (UsbDevice) intent.getParcelableExtra(UsbManager.EXTRA_DEVICE);
                if (intent.getBooleanExtra(UsbManager.EXTRA_PERMISSION_GRANTED, false)) {
                    if (usbManager != null && usbDevice != null) {
                        // ここに印刷処理を記述
                    }
                }
            }
        }
    }
};

public void printUsb() {
    UsbConnection usbConnection = UsbPrintersConnections.selectFirstConnected(this);
    UsbManager usbManager = (UsbManager) this.getSystemService(Context.USB_SERVICE);
    if (usbConnection != null && usbManager != null) {
        PendingIntent permissionIntent = PendingIntent.getBroadcast(
            this,
            0,
            new Intent(MainActivity.ACTION_USB_PERMISSION),
            android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.S ? PendingIntent.FLAG_MUTABLE : 0
        );
        IntentFilter filter = new IntentFilter(MainActivity.ACTION_USB_PERMISSION);
        registerReceiver(this.usbReceiver, filter);
        usbManager.requestPermission(usbConnection.getDevice(), permissionIntent);
    }
}
```

### USB のコード例

Activity に記述する例は以下のとおりです。

```java
EscPosPrinter printer = new EscPosPrinter(new UsbConnection(usbManager, usbDevice), 203, 48f, 32);
printer
    .printFormattedText(
        "[C]<img>" + PrinterTextParserImg.bitmapToHexadecimalString(printer, this.getApplicationContext().getResources().getDrawableForDensity(R.drawable.logo, DisplayMetrics.DENSITY_MEDIUM))+"</img>\n" +
        "[L]\n" +
        "[C]<u><font size='big'>ORDER N°045</font></u>\n" +
        "[L]\n" +
        "[C]================================\n" +
        "[L]\n" +
        "[L]<b>BEAUTIFUL SHIRT</b>[R]9.99e\n" +
        "[L]  + Size : S\n" +
        "[L]\n" +
        "[L]<b>AWESOME HAT</b>[R]24.99e\n" +
        "[L]  + Size : 57/58\n" +
        "[L]\n" +
        "[C]--------------------------------\n" +
        "[R]TOTAL PRICE :[R]34.98e\n" +
        "[R]TAX :[R]4.23e\n" +
        "[L]\n" +
        "[C]================================\n" +
        "[L]\n" +
        "[L]<font size='tall'>Customer :</font>\n" +
        "[L]Raymond DUPONT\n" +
        "[L]5 rue des girafes\n" +
        "[L]31547 PERPETES\n" +
        "[L]Tel : +33801201456\n" +
        "[L]\n" +
        "[C]<barcode type='ean13' height='10'>831254784551</barcode>\n" +
        "[C]<qrcode size='20'>https://dantsu.com/</qrcode>"
    );
```


## 文字コード

プリンタの文字コードを変更するには、`EscPosCharsetEncoding` クラスを使用します。

```java
EscPosPrinter printer = new EscPosPrinter(deviceConnection, 203, 48f, 32, new EscPosCharsetEncoding("windows-1252", 16));
```

`escPosCharsetId` はプリンタ機種によって異なる場合があります。  
多くのプリンタで使える `escPosCharsetId` は[こちらのリンク](https://www.epson-biz.com/modules/ref_escpos/index.php?content_id=32)で確認できます。

## フォーマット済みテキスト：構文ガイド

### 改行

`\n` で改行します。

### 文字揃えとカラム分割

同じ行に複数の揃えタグを書くと、その行がカラムに分割されます。

揃えタグは次のとおりです。

- `[L]` : 左揃え
- `[C]` : 中央揃え
- `[R]` : 右揃え

例：

- `[L]テキスト` : 左揃え 1 カラム
- `[C]テキスト` : 中央揃え 1 カラム
- `[R]テキスト` : 右揃え 1 カラム
- `[L]テキスト[L]別のテキスト` : 左揃え 2 カラム。`別のテキスト` は用紙の中央付近から印字されます。
- `[L]テキスト[R]別のテキスト` : 2 カラム（左・右）。`別のテキスト` は用紙の右端に印字されます。
- `[L]テキスト[R]文字[R]ここ` : 3 カラム
- `[L][R]文字[R]ここ` : 3 カラム。1 つ目は空ですが、幅の 1/3 を占めます。

### フォント

#### サイズ

`<font></font>` タグでフォントサイズと色を指定できます。既定は `normal` / `black` です。

- `<font size='normal'>テキスト</font>` : 通常サイズ
- `<font size='wide'>テキスト</font>` : 横 2 倍
- `<font size='tall'>テキスト</font>` : 縦 2 倍
- `<font size='big'>テキスト</font>` : 横・縦 2 倍
- `<font size='big-2'>テキスト</font>` : 横・縦 3 倍
- `<font size='big-3'>テキスト</font>` : 横・縦 4 倍
- `<font size='big-4'>テキスト</font>` : 横・縦 5 倍
- `<font size='big-5'>テキスト</font>` : 横・縦 6 倍
- `<font size='big-6'>テキスト</font>` : 横・縦 7 倍

- `<font color='black'>テキスト</font>` : 黒文字・白背景
- `<font color='bg-black'>テキスト</font>` : 白文字・黒背景
- `<font color='red'>テキスト</font>` : 赤文字・白背景（機種により非対応）
- `<font color='bg-red'>テキスト</font>` : 白文字・赤背景（機種により非対応）

#### 太字

`<b></b>` で太字にします。

- `<b>テキスト</b>`

#### 下線

`<u></u>` で下線を付けます。

- `<u>テキスト</u>` : 下線付き
- `<u type='double'>テキスト</u>` : 二重線（機種により非対応）

### 画像

`<img></img>` で画像を印字できます。タグ内には画像データの十六進文字列を指定します。

`Drawable`・`BitmapDrawable`・`Bitmap` を十六進文字列に変換するには `PrinterTextParserImg.bitmapToHexadecimalString` を使用してください。

- `<img>`画像の十六進文字列`</img>`

**⚠ 注意 ⚠** このタグには次の制約があります。

- `<img></img>` を含む行には、揃えタグは 1 つだけ付けられ、かつ行頭である必要があります。
- `<img>` の直前に書けるのは揃えタグ（`[L][C][R]`）のみ、または何もなしです。
- `</img>` の直後は改行 `\n` である必要があります。
- `<img></img>` がある行に他のテキストは書けません。
- 印字可能な画像の高さは最大 256px です。より大きいビットマップを印字する方法は[#70](https://github.com/DantSu/ESCPOS-ThermalPrinter-Android/issues/70#issuecomment-714390014)を参照してください。

### バーコード

`<barcode></barcode>` でバーコードを印字できます。タグ内に印字するコードを指定します。

- `<barcode>451278452159</barcode>` : **（12 桁）**  
  EAN13 バーコード（高さ 10mm、幅はプリンタ幅の約 70%、文字は下に表示）
- `<barcode type='ean8'>4512784</barcode>` : **（7 桁）**  
  EAN8 バーコード（高さ 10mm、幅はプリンタ幅の約 70%、文字は下に表示）
- `<barcode type='upca' height='20'>4512784521</barcode>` : **（11 桁）**  
  UPC-A バーコード（高さ 20mm、幅はプリンタ幅の約 70%、文字は下に表示）
- `<barcode type='upce' height='25' width='50' text='none'>512789</barcode>` : **（6 桁）**  
  UPC-E バーコード（高さ 25mm、幅約 50mm、文字非表示）
- `<barcode type='128' width='40' text='above'>DantSu</barcode>` : **（文字列）**  
  バーコード 128（高さ 10mm、幅約 40mm、文字は上に表示）

**⚠ 注意 ⚠** このタグには次の制約があります。

- `<barcode></barcode>` を含む行には、揃えタグは 1 つだけ付けられ、かつ行頭である必要があります。
- `<barcode>` の直前に書けるのは揃えタグ（`[L][C][R]`）のみ、または何もなしです。
- `</barcode>` の直後は改行 `\n` である必要があります。
- `<barcode></barcode>` がある行に他のテキストは書けません。

### QR コード

`<qrcode></qrcode>` で QR コードを印字できます。タグ内に QR コードのデータを指定します。

- `<qrcode>https://dantsu.com/</qrcode>` :  
  幅・高さ 20mm の QR コードを印字
- `<qrcode size='25'>123456789</qrcode>` :  
  幅・高さ 25mm の QR コードを印字

**⚠ 注意 ⚠** このタグには次の制約があります。

- `<qrcode></qrcode>` を含む行には、揃えタグは 1 つだけ付けられ、かつ行頭である必要があります。
- `<qrcode>` の直前に書けるのは揃えタグ（`[L][C][R]`）のみ、または何もなしです。
- `</qrcode>` の直後は改行 `\n` である必要があります。
- `<qrcode></qrcode>` がある行に他のテキストは書けません。

## クラス一覧

### Class : `com.dantsu.escposprinter.connection.bluetooth.BluetoothPrintersConnections`

#### **static** メソッド : `selectFirstPaired()`
ペアリング済み／接続済みの Bluetooth プリンタのうち、最初の 1 台を取得します。
- **戻り値** `BluetoothConnection`

#### メソッド : `getList()`
Bluetooth プリンタの一覧を取得します。
- **戻り値** `BluetoothConnection[]`

⚠️ `getList()` の戻り値に目的のプリンタが含まれない、または `selectFirstPaired()` で取得できない場合は、[こちらの issue](https://github.com/DantSu/ESCPOS-ThermalPrinter-Android/issues/80#issuecomment-729759832) を参照してください。

### Class : `com.dantsu.escposprinter.connection.tcp.TcpConnection`

#### コンストラクタ : `TcpConnection(String address, int port[, int timeout])`
- **param** `String address` : 接続先 IP アドレス
- **param** `int port` : 接続先 TCP ポート
- **param** `int timeout` *（省略可）* : 接続タイムアウト秒（既定値 : 30）

### Class : `com.dantsu.escposprinter.connection.usb.UsbPrintersConnections`

#### **static** メソッド : `selectFirstConnected()`
接続されている USB プリンタのうち、最初の 1 台を取得します。
- **戻り値** `UsbConnection`

#### メソッド : `getList()`
USB プリンタの一覧を取得します。
- **戻り値** `UsbConnection[]`

### Class : `com.dantsu.escposprinter.EscPosPrinter`

#### コンストラクタ : `EscPosPrinter(DeviceConnection printer, int printerDpi, float printingWidthMM, int nbrCharactersPerLine [, EscPosCharsetEncoding charsetEncoding])`
- **param** `DeviceConnection printer` : 接続済みプリンタのインスタンス
- **param** `int printerDpi` : プリンタの DPI
- **param** `float printerWidthMM` : 印字幅（ミリメートル）
- **param** `int printerNbrCharactersPerLine` : 1 行に印字できる中サイズ文字の最大数
- **param** `EscPosCharsetEncoding charsetEncoding` *（省略可）* : 文字コードの指定

#### メソッド : `disconnectPrinter()`
プリンタとの接続を閉じます。
- **戻り値** `Printer` : フルーエントインターフェース

#### メソッド : `getNbrCharactersPerLine()`
1 行に印字できる文字数の最大値を取得します。
- **戻り値** `int`

#### メソッド : `getPrinterWidthMM()`
印字幅（ミリメートル）を取得します。
- **戻り値** `float`

#### メソッド : `getPrinterDpi()`
プリンタの DPI を取得します。
- **戻り値** `int`

#### メソッド : `getPrinterWidthPx()`
印字幅（ドット数）を取得します。
- **戻り値** `int`

#### メソッド : `getPrinterCharSizeWidthPx()`
1 文字分の幅（ドット数）を取得します。
- **戻り値** `int`

#### メソッド : `mmToPx(float mmSize)`
ミリメートルをドット数に変換します。
- **param** `float mmSize` : 変換する長さ（ミリメートル）
- **戻り値** `int` : ドット数

#### メソッド : `useEscAsteriskCommand(boolean enable)`
画像印字で「ESC *」コマンドを使うかどうかを指定します。
- **param** `boolean enable` : `true` で「ESC *」、`false` で「GS v 0」を使用
- **戻り値** `Printer` : フルーエントインターフェース

#### メソッド : `printFormattedText(String text)`
フォーマット済みテキストを印字し、用紙を 20mm 送ります。書式の詳細は[「フォーマット済みテキスト：構文ガイド」](#フォーマット済みテキスト構文ガイド)を参照してください。
- **param** `String text` : 印字するフォーマット済みテキスト
- **戻り値** `Printer` : フルーエントインターフェース

#### メソッド : `printFormattedTextAndCut(String text)`
フォーマット済みテキストを印字し、用紙を 20mm 送ってからカットします。
- **param** `String text` : 印字するフォーマット済みテキスト
- **戻り値** `Printer` : フルーエントインターフェース

#### メソッド : `printFormattedText(String text, float mmFeedPaper)`
フォーマット済みテキストを印字し、用紙を指定ミリメートル分送ります。
- **param** `String text` : 印字するフォーマット済みテキスト
- **param** `float mmFeedPaper` : 印字後の送り量（ミリメートル）
- **戻り値** `Printer` : フルーエントインターフェース

#### メソッド : `printFormattedTextAndCut(String text, float mmFeedPaper)`
フォーマット済みテキストを印字し、用紙を指定ミリメートル分送ってからカットします。
- **param** `String text` : 印字するフォーマット済みテキスト
- **param** `float mmFeedPaper` : 印字後の送り量（ミリメートル）
- **戻り値** `Printer` : フルーエントインターフェース

#### メソッド : `printFormattedTextAndOpenCashBox(String text, float mmFeedPaper)`
フォーマット済みテキストを印字し、用紙を送り、カットしてからキャッシュドロアを開きます。
- **param** `String text` : 印字するフォーマット済みテキスト
- **param** `float mmFeedPaper` : 印字後の送り量（ミリメートル）
- **戻り値** `Printer` : フルーエントインターフェース

#### メソッド : `printFormattedText(String text, int dotsFeedPaper)`
フォーマット済みテキストを印字し、用紙を指定ドット数分送ります。
- **param** `String text` : 印字するフォーマット済みテキスト
- **param** `int dotsFeedPaper` : 印字後の送り量（ドット）
- **戻り値** `Printer` : フルーエントインターフェース

#### メソッド : `printFormattedTextAndCut(String text, int dotsFeedPaper)`
フォーマット済みテキストを印字し、用紙を指定ドット数分送ってからカットします。
- **param** `String text` : 印字するフォーマット済みテキスト
- **param** `int dotsFeedPaper` : 印字後の送り量（ドット）
- **戻り値** `Printer` : フルーエントインターフェース

#### メソッド : `printFormattedTextAndOpenCashBox(String text, int dotsFeedPaper)`
フォーマット済みテキストを印字し、用紙を送り、カットしてからキャッシュドロアを開きます。
- **param** `String text` : 印字するフォーマット済みテキスト
- **param** `int dotsFeedPaper` : 印字後の送り量（ドット）
- **戻り値** `Printer` : フルーエントインターフェース

#### メソッド : `bitmapToBytes(Bitmap bitmap, boolean gradient)`
Bitmap を ESC/POS 画像データに変換します。
- **param** `Bitmap bitmap` : Bitmap のインスタンス
- **param** `boolean gradient` : `false` で白黒、`true` でグレースケール
- **戻り値** `byte[]` : ESC/POS コマンドとしての画像データ

### Class : `com.dantsu.escposprinter.textparser.PrinterTextParserImg`

#### **static** メソッド : `bitmapToHexadecimalString(Printer printer, Drawable drawable [, boolean gradient])`
Drawable を画像データの十六進文字列に変換します。
- **param** `Printer printer` : 画像を印字する Printer のインスタンス
- **param** `Drawable drawable` : 変換する Drawable
- **param** `boolean gradient` *（省略可）* : `false` で白黒、`true` でグレースケール（既定 : `true`）
- **戻り値** `String` : 画像データの十六進文字列。BitmapDrawable に変換できない場合は空文字列

#### **static** メソッド : `bitmapToHexadecimalString(Printer printer, BitmapDrawable bitmapDrawable [, boolean gradient])`
BitmapDrawable を画像データの十六進文字列に変換します。
- **param** `Printer printer` : 画像を印字する Printer のインスタンス
- **param** `BitmapDrawable bitmapDrawable` : 変換する BitmapDrawable
- **param** `boolean gradient` *（省略可）* : `false` で白黒、`true` でグレースケール（既定 : `true`）
- **戻り値** `String` : 画像データの十六進文字列

#### **static** メソッド : `bitmapToHexadecimalString(Printer printer, Bitmap bitmap [, boolean gradient])`
Bitmap を画像データの十六進文字列に変換します。
- **param** `Printer printer` : 画像を印字する Printer のインスタンス
- **param** `Bitmap bitmap` : 変換する Bitmap
- **param** `boolean gradient` *（省略可）* : `false` で白黒、`true` でグレースケール（既定 : `true`）
- **戻り値** `String` : 画像データの十六進文字列

#### **static** メソッド : `bytesToHexadecimalString(byte[] bytes)`
ESC/POS 画像データのバイト配列を十六進文字列に変換します。
- **param** `byte[] bytes` : ESC/POS コマンドの画像データ
- **戻り値** `String` : 画像データの十六進文字列

#### **static** メソッド : `hexadecimalStringToBytes(String hexString)`
画像データの十六進文字列を ESC/POS のバイト列に変換します。
- **param** `String hexString` : 画像データの十六進文字列
- **戻り値** `byte[]` : ESC/POS コマンドの画像データ

### Class : `com.dantsu.escposprinter.EscPosCharsetEncoding`

#### コンストラクタ : `EscPosCharsetEncoding(String charsetName, int escPosCharsetId)`
- **param** `charsetName` : 文字コード名（例 : ISO-8859-1）
- **param** `escPosCharsetId` : プリンタ用の ESC/POS 文字コード ID（例 : 6）

## 本ライブラリを利用しているプロジェクト

- [AllInOneYT/react-native-thermal-printer : React Native ブリッジ](https://github.com/AllInOneYT/react-native-thermal-printer)
- [paystory-de/thermal-printer-cordova-plugin : Cordova / Ionic ブリッジ](https://github.com/paystory-de/thermal-printer-cordova-plugin)
- [asukiaaa/react-native-escpos-android : React Native ブリッジ](https://github.com/asukiaaa/react-native-escpos-android)
- [android_bluetooth_printer : Flutter ブリッジ](https://pub.dev/packages/android_bluetooth_printer)

## コントリビューション

このリポジトリをフォークし、プルリクエストで変更を送っていただけると幸いです。

バグ修正から新機能まで、規模の大小を問わず歓迎します。内容に応じてレビューさせていただきます。
