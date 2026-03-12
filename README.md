# 2D 動畫渲染 library

## 相關技術
- systems programming
- graphics fundamentals
- data structure design

## 相關背景知識

1. 精靈圖 Sprite，代表一個圖片資料

2. Canvas 代表整個場景

3. Frame Buffer 代表在輸出裝置上的資訊

## 需求

設計一個記憶體效率高的架構，完成canvas、sprite相關的TODO函數
需有animation以及layer功能。

## 測試
需使用講義第六點的範例

## 結構設計稿

Sprites (圖片資源)
        ↓
Sprite Placements (場景物件)
        ↓
Canvas (場景)
        ↓
Animation System (運動)
        ↓
Renderer : generate frame(畫圖)
        ↓
Frame Buffer
        ↓
輸出畫面

## API說明

控制圖層順序：
```
animate_placement_up()
animate_placement_down()
animate_placement_top()
animate_placement_bottom()
```

生成畫面
```
animate_generate_frame()
```
## 實作順序
### 1. Canvas 畫布資料結構
```C
struct canvas { //整個畫布

    size_t width;
    size_t height;

    color_t background;

    struct sprite_placement *head;
    struct sprite_placement *tail;
};
// API
animate_create_canvas()
animate_destroy_canvas()
```
### 2. Sprite 圖片資料結構
```C
   struct sprite {

    size_t width;
    size_t height;

    color_t *pixels;
};
```
來源： - bitmap file  
      - rectangle  
      - circle  
```
animate_create_sprite()
animate_create_rectangle()
animate_create_circle()
animate_destroy_sprite()
```
### 3. 建立 Sprite Placement
```C
struct sprite_placement { // 場景中的物件

    struct sprite *sprite;

    ssize_t x;
    ssize_t y;

    ssize_t vx;
    ssize_t vy;

    ssize_t ax;
    ssize_t ay;

    struct sprite_placement *prev;
    struct sprite_placement *next;
};
```
用途：控制 sprite 在場景中的位置與動畫  
API：
```
animate_place_sprite
animate_destroy_placement
```
### 4. Layer 系統
Canvas 需要知道：
```
sprite rendering order
```

最好的資料結構是：
```
doubly linked list
```
API
```
animate_placement_up
animate_placement_down
animate_placement_top
animate_placement_bottom
```
### 5. Animation System
設定速度與加速度：

vx vy
ax ay
```
animate_set_animation_params
```
位置計算：

x = x0 + v*t + ½a*t²

時間：

t = frame / frame_rate

### 6. Frame Buffer
一幀畫面是：

pixel array

型別：

color_t

也就是：

uint32_t
大小：

width × height × 4

API：

animate_frame_size_bytes()

### 7. Render (generate frame)
用途：生成一幀畫面

API：

animate_generate_frame()

流程：

1 清空 frame buffer
2 填背景
3 依 layer 順序畫 sprite

### 8. Sprite drawing

畫 sprite：

for each pixel

計算：
```

screen_x = sprite_x + i
screen_y = sprite_y + j
```

然後：
```

frame[screen] = sprite[pixel]
```

如果：

alpha == 0，代表透明，不要畫。


### 9. Frame 輸出
