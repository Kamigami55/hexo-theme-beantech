---
title: "開發藍芽鋼琴APP！使用AppInventor結合Arduino、HC05、與蜂鳴器"
catalog: true
date: 2017-03-05 23:00:36
tags: [AppInventor, HC05, Arduino]
categories: Arduino
header-img: "https://imgur.com/HXjQPNQ.png"
---

今天我們要實作簡易藍芽鋼琴APP！

- 用AppInventor 2開發Android APP作為控制介面
- Arduino UNO作硬體控制
- HC-05藍芽模組接收手機指令
- 一個無源蜂鳴器作為發音設備

<!-- more -->

DEMO影片：

[![](https://img.youtube.com/vi/hr8DXRMLY9s/0.jpg)](https://www.youtube.com/watch?v=hr8DXRMLY9s)

### 先備知識
- 基礎AppInventor操作
- 基礎Arduino程式撰寫
- HC-05藍芽模組使用
- 蜂鳴器使用

---

# Android App撰寫（使用AppInventor）

APP是當作藍芽鋼琴的控制介面，會透過藍芽告訴Arduino應該發出哪個聲音。

下載.aia檔：[keyboard.aia](https://drive.google.com/file/d/0B89iCvlgxOnydm9oaW9mOVdNTzg/view?usp=sharing)

### 外觀編排

- 最上方有**一個清單選擇器**，用來連接藍芽
- 中央**八個按鈕**是琴鍵，從DO,RE,...到DO5
- 下方是**一個按鈕**，用來取消藍芽連接
- 還有一個不可見的**藍芽客戶端**元件

![](https://imgur.com/jv9FN5e.png)


### 程式設計

- **連接藍芽**清單選擇器被按下時，列出所有已配對藍芽
- **連接藍芽**清單選擇器已選擇後，連接藍芽，
- **取消連接按鈕**被按下時，取消連線
- **DO~DO5**琴鍵按鈕被按下時，藍芽送出訊號**'1'~'8'**，告訴Arduino該發聲了
- **DO~DO5**琴鍵按鈕被鬆開時，藍芽送出訊號**'S'**，告訴Arduino停止發聲

![](https://imgur.com/ouu8fbu.png)

---

# 硬體接線

### 需求材料：
- Arduino UNO x1（或其他類UNO開發版都可）
- 無源蜂鳴器 x1
- HC-05藍牙模組 x1（HC-06也可）

### 接線：
- HC-05的RX接pin11、TX接pin10
- 蜂鳴器正極端接pin6（須具有PWM功能）

![](https://imgur.com/SFuIdNX.png)

---

# Arduino程式

- 不斷從藍芽收資料
- 收到'1'~'8'的話，發出對應的音
- 收到'S'的話，停止發音

``` c
#include <SoftwareSerial.h>
SoftwareSerial BTSerial(10, 11); // 藍芽Serial使用的pin腳（Arduino的RX,TX，對應到HC05的TX,RX)

#define DO  262 // 音階頻率table
#define RE  294
#define MI  330
#define FA  349
#define SOL 392
#define LA  440
#define SI  494
#define DO5 523

#define BUZZER 6 // 蜂鳴器pin腳

void setup() {
  BTSerial.begin(9600);
}

void loop() {
  if (BTSerial.available()){
    char note = BTSerial.read();
    switch(note){
      case '1': // 若接收到字元'1'（從手機App傳來的）
        tone(BUZZER, DO); // 就開始發出DO的音
        break;
      case '2': // 以此類推
        tone(BUZZER, RE);
        break;
      case '3':
        tone(BUZZER, MI);
        break;
      case '4':
        tone(BUZZER, FA);
        break;
      case '5':
        tone(BUZZER, SOL);
        break;
      case '6':
        tone(BUZZER, LA);
        break;
      case '7':
        tone(BUZZER, SI);
        break;
      case '8':
        tone(BUZZER, DO5);
        break;
      case 'S': // 若接收到'S'
        noTone(BUZZER); // 停止發音
        break;
    }
  }
}
```

完成！現在你可以用手機彈奏美妙的音樂了！

