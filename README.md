# UART Driver
STM32向けUARTデバイスドライバ

---

## 概要
HALドライバを再抽象化したUARTモジュールを定義したプログラムとファイル一式。
main()関数内に、サンプル実装を記載。

---

## 動作環境
* MCU: STM32F446RE
* Board: NUCLEO-F446RE

* IDE: STM32CubeIDE(ver.2.1.0)
* OS: Windows 11
* ターミナルソフト:Tera Term

※HALドライバを定義したフォルダ「STM32F4xx_HAL_Driver」配下に、STM32F4xx_HAL_Driver.cと、関連ファイルが生成されていること。

---

## ファイル構成
本プロジェクト用に定義したファイル構成を以下に示す。
```
UART
  ├── uart_config.h   <- 構成用ファイル
  ├── uart_hal.h      <- HAL依存のプロトタイプ宣言を定義
  ├── uart.c          <- HALの抽象化とcoreとなる機能を定義
  └── uart.h          <- 外部公開するAPIを定義
```

---

## 使用手順
Cube MX内でUSART2を設定(通常はプロジェクト作成時にdefaultで設定されている)し、以下に記載する自動生成された機能を編集する。

main.c
* MX_USART2_UART_Init   --> 関数内の処理をコメントアウト

stm32f4xx_hal_msp.c
* HAL_UART_MspInit      --> すべてコメントアウト
* HAL_UART_MspDeInit    --> すべてコメントアウト

またはSTマイクロ公式のHALドライバをgit hubより取得し、ファイル一式を自プロジェクト内にコピーする。
[stm32f4xx-hal-driver](https://github.com/STMicroelectronics/stm32f4xx-hal-driver.git)

---

## 使用例
以下の使用例では、Tera Term上に「[DEBUG] COM PORT OPEN」のメッセージを表示する。
使用例のほかに、サンプル実装例をmain.c内のユーザー定義エリアに記述している。

```c
#define UART_STATE_IDOL     0
#define UART_STATE_TX       1
#define UART_STATE_RX       2

int uart_id1 = UART_STATE_IDOL;

void uart_example(void)
{
    /* デバッグ用ポートOPEN */
    uart_open(UART_COM_DEBUG, &uart_id1, 115200);

    /* 送信コールバック登録 */
    uart_set_tx_callback(&uart_id1, uart_example_tx_cb);

    /* 受信コールバック登録 */
    uart_set_rx_callback(&uart_id1, uart_example_rx_cb);

    uart_tx_start(&uart_id1, (unsigned char *)"[DEBUG] COM PORT OPEN\r\n", 24);
    uart_id1 = UART_STATE_TX;
    while (uart_id1 != UART_STATE_IDOL);    /* 送信完了待ち */

    while (1);
}
```