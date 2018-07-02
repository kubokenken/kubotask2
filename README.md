【プログラム】
import numpy as np
import cv2
import matplotlib.pyplot as plt
from skimage import io
from skimage.color import rgb2gray

def realtime_graph(x, y):
    line, = plt.plot(x, y,label="S Curve") # (x,y)のプロット
    line.set_ydata(y)   # y値を更新
    plt.title("S Curve")  # グラフタイトル
    plt.xlabel("x")     # x軸ラベル
    plt.ylabel("y")     # y軸ラベル
    plt.legend()        # 凡例表示
    plt.grid()          # グリッド表示
    plt.xlim([0,255])    # x軸範囲
    plt.ylim([0,255])    # y軸範囲
    plt.draw()          # グラフの描画
    plt.pause(0.01)     # 更新時間間隔
    plt.clf()

def myfunc(i):
    pass # do nothing

def S_curve(x):
    y = (np.sin(np.pi * (x/255 - s/255)) + 1)/2 * 255
    return y

cv2.namedWindow('title') # create win with win name
#RGB trackbar
cv2.createTrackbar('R','title',0,100,myfunc)
cv2.createTrackbar('G','title',0,100,myfunc)
cv2.createTrackbar('B','title',0,100,myfunc)

cv2.createTrackbar('s_curve','title',127,255,myfunc)
cv2.createTrackbar('grayscale','title',0,1,myfunc)
cv2.createTrackbar('S Curve:on/off','title',0,1,myfunc)

cap = cv2.VideoCapture(0)
cap.set(cv2.CAP_PROP_FRAME_WIDTH,  640)
cap.set(cv2.CAP_PROP_FRAME_HEIGHT, 480)



while(True):
    k = cv2.waitKey(1)
    if k == ord('q') or k == 27:
        break
    
    ret, frame = cap.read()
    if not ret: continue

    #frame resize
    frame = cv2.resize(frame,(int(frame.shape[1]/3), int(frame.shape[0]/3)))

    #grayscale
    gray = cv2.getTrackbarPos('grayscale','title')
    if gray == 1:
        frame = rgb2gray(frame)

    r = cv2.getTrackbarPos('R','title')
    g = cv2.getTrackbarPos('G','title')
    b = cv2.getTrackbarPos('B','title')

    #rbg
    if r == 100:
        frame[:,:,2] = 255
    #S Curve
    s = cv2.getTrackbarPos('s_curve','title')
    x = np.linspace(0, 255, 100)
    y = S_curve(x)
    realtime_graph(x,y)

    #S Curve on/off
    sw = cv2.getTrackbarPos('S Curve:on/off','title')
    if sw == 1:
        frame = S_curve(frame)
    cv2.imshow('title', frame)  # show in the win



plt.close()
cap.release()
cv2.destroyAllWindows()
```

以上のプログラムを以下のコードで実行する.
C:\Users\owner\github>python Capture2.py

すると数値を変更するためのバ－を持つカメラ機能が実行された
しかし上記のプログラムではRBGの値がうまく変更できなかった

---

【説明】
カラー画像で考えると,
カラー画像をＲＧＢに分離しする.cv2.split()で出来る.
分離した画像は１チャンネル分のため,そのまま表示すると
グレースケールとしての表示になる.
ＲＧＢに分離したあと,それぞれの色で表示するために
他の色をゼロ埋めの画像で補ったカラー画像を作る必要がある.
ゼロ埋めの画像を作るのはnumpy.zeros()
３チャンネルを１画像にまとめるのはcv2.merge()で行う.

今回RBGがうまくいかなかったのはそれぞれの計算が違うのか
うまく参照できていないかのどちらかだろうと思うが理解できなかった


参考URL:https://qiita.com/hitomatagi/items/35d079648c12b533bcb4
　　　　https://www.tech-tech.xyz/archives/opencv_video.html