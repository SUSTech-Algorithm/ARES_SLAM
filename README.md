# camera_position
Using camera to locate something with the help of the camera's inner parameters
**tips: README編寫可能有誤，如有錯誤請及時指出**
## :smile: Structure of the workspace
```
.
├── README.md
├── resource
└── src
    └── visual_USB
        ├── config          #參數文件
        ├── launch          #launch啓動文件
        ├── package.xml     #包依賴
        ├── pid             #實現pid對準功能
        ├── resource 
        ├── setup.cfg
        ├── setup.py        #包文件
        ├── test            #用於儲存標定照片
        └── visual_USB      #代碼文件
```
## :tent: Principle 原理
通過將禿透鏡成像近似爲**小孔成像**的針孔攝像頭

利用相似關係來進行三位座標與二維座標的轉換

### 座標系
這裏會設計到三個座標系：

1) 世界座標系

2) 相機座標系 ： 以攝像頭光心爲原點 左x 上y 前z 

！[攝像機座標系](./resource/camera_position/真空攝像頭.png)

3) 像素座標系 ： 顯示出像素圖像中的座標系，opencv中默認左上角爲座標原點，右x，下y *(但這裏找到的圖不太一樣)*

！[像素座標系](./resource/camera_position/像素座標系.png)

### 參數

攝像頭有兩個參數：內參、外參

#### **內參**： 
相機自身的固有屬性，**建立了像素座標系與相機座標系之間的關係**
1）$fx$ $fy$ : 單位：像素。 
以$fx$爲例 
$fx = f*k$ 
$f$指的是焦距，單位是mm ，$k$指的是真實世界中水平方向上的1mm對應攝像頭上的k個像素（$fy$ 同理）

2）$cx$ $cy$ : 單位：像素。
指的攝像頭光心在像素座標系下的座標 （一般像素座標系都與Opencv的像素座標系一致）

3）其他的畸變係數：
在處理圖像畸變的時候有用，如果攝像頭畸變不明顯可以不考慮這部分

#### **外參**： 
**建立相機座標續與世界座標系的關係**，由三維旋轉矩陣$R$和平移矩陣$T$組成
$$
\begin{bmatrix}
R &T \\
0 &1
\end{bmatrix}
\tag{1}
$$