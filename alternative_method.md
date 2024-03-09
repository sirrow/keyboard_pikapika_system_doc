# ピカピカファームウェアを用いる以外の、接続キーボードの用意方法

ピカピカファームウェアを用いる以外の、接続キーボードの用意方法として、下記の 2 つの方法があります。

1. **個別キーマップ追記方式:** 個別のキーマップに対応するためのコードを追記し、コンパイル、書き込みを行う方法
   - 普段ご利用のキーマップに、Raw HID による通信を行うためのコードを追記頂く方法です。
2. **専用キーマップ方式:** Raw HID による通信を行うためだけに用意されたキーボードとキーマップを用いる方法
   - マイコンに対して rgblight で制御される LED が接続されているだけの最低限の定義を持つキーボードとキーマップを用意し、これを用いて頂く方法です。書き込みに当たり、マイコンにから LED へ接続が行われるピン番号を確認し、設定頂く必要があります。



## 個別キーマップ追記方式
個別キーマップ追記方式では、普段ご利用のキーマップに、Raw HID による通信を行うためのコードを追記頂きます。

ご利用のキーボードの rules.mk に下記の行を追加してください。
```
RAW_ENABLE = yes
```

また、普段ご利用のキーマップの keymap.c ファイルに対して、下記のコードを追記してください。
他の設定については前ページのTIPSも参考にしてください。

**via/remap/vialが有効になっているキーマップでは使用できません**

```c
// 上の方のヘッダとか include する部分
#include "raw_hid.h"

// 下の方の keymap の定義とか終わった後ろの部分

enum HID_RGBLED_COMMAND { HID_PING = 0, HID_SET_HSV, HID_SET_RGB };
void raw_hid_receive(uint8_t *data, uint8_t length) {
    switch (data[0]) {
        case HID_PING:
            data[0] = 1;
            data[1] = 'R';
            data[2] = 'G';
            data[3] = 'B';
            break;
        case HID_SET_HSV:
            rgblight_sethsv_noeeprom(data[1], data[2], data[3]);
            data[0] = 1;
            break;
        case HID_SET_RGB:
            rgblight_setrgb(data[1], data[2], data[3]);
            data[0] = 1;
            break;
        default:
            data[1] = data[0];
            data[0] = 0;
            break;
    }
    raw_hid_send(data, length);
}

void keyboard_post_init_user(void) {
    // 起動時にRGBLEDを有効化して消灯する
    rgblight_enable_noeeprom();
    rgblight_mode_noeeprom(RGBLIGHT_MODE_STATIC_LIGHT);
    rgblight_sethsv_noeeprom(0,0,0);
}
```

以降は、通常通りの手順で、キーマップのコンパイル及びマイコンへの書き込みを行ってください。


## 専用キーマップ方式
専用キーマップ方式では、マイコンに対して rgblight で制御される LED が接続されているだけの最低限の定義を持つキーボードとキーマップを用意し、これを用います。
利用の手順は下記の通りです。

1. この専用のキーボード及びキーマップは、[https://github.com/hsgw/qmk_firmware/tree/hid_rgbled](https://github.com/hsgw/qmk_firmware/tree/hid_rgbled) にあるのでこれをcloneします。
2. keyboards/hid_rgbled にディレクトリを追加してinfo.jsonなどを編集します。
3. defaultのキーマップをコンパイルしてキーボードに書き込みます(keymaps/testはLED動作テスト用です)
