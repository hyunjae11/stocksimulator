# 주식 시뮬레이터

모의 주식 시뮬레이터입니다!

아래는 소스코드입니다
```
#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <windows.h>

#define STOCK_COUNT 3

// ================== 변수 ==================
int money = 500000;

int price[STOCK_COUNT]     = {30000, 150000, 400000};  
int prev_price[STOCK_COUNT]= {30000, 150000, 400000};  

int min_price[STOCK_COUNT] = {5000, 50000, 180000};  
int max_price[STOCK_COUNT] = {60000, 300000, 800000};  

int owned[STOCK_COUNT] = {0, 0, 0};

// -1 하락, 0 변동 X, 1 상승
int last_state[STOCK_COUNT] = {0, 0, 0};

char stock_name[STOCK_COUNT][10] = {
    "Netsla", "Gvidia", "BotCoin"
};

time_t last_update;

// ================== 함수 선언 ==================
void update_stock();
void show_stock();
void buy_sell();
void bankrupt();

// ================== 주가 변동 ==================
void update_stock() {

    time_t now = time(NULL);
    if (difftime(now, last_update) < 30) return;
    last_update = now;

    for (int i = 0; i < STOCK_COUNT; i++) {

        prev_price[i] = price[i];   // 이전 가격 저장

        if (price[i] <= min_price[i] || price[i] >= max_price[i])
            continue;

        int r = rand() % 10 + 1;
        int dir = 0;

        // 상승 / 하락 확률
        if (last_state[i] == 0) {
            if (r <= 4) dir = 1;
            else if (r <= 8) dir = -1;
        } 
        else if (last_state[i] == 1) {
            if (r <= 6) dir = 1;
            else if (r <= 9) dir = -1;
        } 
        else {
            if (r <= 3) dir = 1;
            else if (r <= 9) dir = -1;
        }

        // 변동 폭
        int event = rand() % 10 + 1;
        double rate = 0.0;

        if (event <= 6)
            rate = (rand() % 5 + 3) / 100.0;
        else if (event <= 9)
            rate = (rand() % 11 + 10) / 100.0;
        else
            rate = (rand() % 21 + 30) / 100.0;

        if (dir != 0) {

            int diff = (int)(price[i] * rate);
            price[i] += dir * diff;
            last_state[i] = dir;

        } 
        else {
            last_state[i] = 0;
        }

        if (price[i] < min_price[i]) price[i] = min_price[i];
        if (price[i] > max_price[i]) price[i] = max_price[i];
    }
}

// ================== 주가 보기 ==================
void show_stock() {

    system("cls");

    printf("보유 자금: %d원\n\n", money);

    printf("=====================================================\n");
    printf("|  주종   |   주가   | 변동률(%%) | 보유 수량 |\n");
    printf("=====================================================\n");

    for (int i = 0; i < STOCK_COUNT; i++) {

        double percent = ((double)(price[i] - prev_price[i]) / prev_price[i]) * 100.0;

        printf("| %-7s | %7d | %+7.2f%% | %8d |\n",
               stock_name[i], price[i], percent, owned[i]);
    }

    printf("=====================================================\n");

    system("pause");
}

// ================== 구매 / 판매 ==================
void buy_sell() {

    int sel, cnt;

    show_stock();

    printf("\n주식 선택 (1~3): ");
    scanf("%d", &sel);
    sel--;

    if (sel < 0 || sel >= STOCK_COUNT) return;

    printf("수량 입력 (양수=구매 / 음수=판매): ");
    scanf("%d", &cnt);

    if (cnt > 0) {

        int cost = cnt * price[sel];

        if (money >= cost) {

            money -= cost;
            owned[sel] += cnt;

        } 
        else {

            printf("자금 부족!\n");
            Sleep(1000);

        }

    } 
    else if (cnt < 0) {

        cnt = -cnt;

        if (owned[sel] >= cnt) {

            owned[sel] -= cnt;
            money += cnt * price[sel];

        } 
        else {

            printf("보유 수량 부족!\n");
            Sleep(1000);

        }
    }
}

// ================== 파산 ==================
void bankrupt() {

    printf("\n파산했습니다...\n");
    system("pause");
    exit(0);

}

// ================== 메인 ==================
int main() {

    srand((unsigned)time(NULL));
    last_update = time(NULL);

    while (1) {

        update_stock();

        system("cls");

        printf("===== 주식장 =====\n");
        printf("보유 자금: %d원\n\n", money);

        printf("1. 주가 보기\n");
        printf("2. 주식 구매/판매\n");
        printf("3. 파산\n");
        printf("0. 종료\n\n");

        int menu;
        scanf("%d", &menu);

        switch (menu) {

            case 1: show_stock(); break;
            case 2: buy_sell(); break;
            case 3: bankrupt(); break;
            case 0: return 0;

        }
    }
}
```
Made by hyunjae11
