# spin周りの実装に関して
## spinOnce()
`spinOnce()`はパブリッシュされたメッセージに対するコールバック関数にアクセスする処理を行う。
コールバック関数は`g_global_queue`というグローバル変数に蓄積されている。
**処理が間に合う範囲で**全メッセージを拾うシステムとなっている。
```
 void spinOnce()
 {
   g_global_queue->callAvailable(ros::WallDuration());
 }
```
[ソース](https://docs.ros.org/en/noetic/api/roscpp/html/namespaceros.html#af4afa6f0ad9f903f04a023982a95ff1c)

## spin()
`spinOnce()`を無限ループで繰り返すラッパー関数として`spin()`が用意されている。
```
   ros::WallDuration timeout(0.1f);
   ros::NodeHandle n;
   while (n.ok())
   {
     queue->callAvailable(timeout);
   }
```
[ソース](https://docs.ros.org/en/noetic/api/roscpp/html/classros_1_1SingleThreadedSpinner.html#a8cc46cdbe3a9b2a516021b4220e15c5a)

0.1秒周期でポーリング処理しているように見えるがそうではない。  
```
queue->callAvailable(timeout);
``````
で割り込み待ちをしている。その間処理が止まるが、その停止時間の最大値が`timeout`で設定されている。「割り込みが入る」 or 「timeout以上の時間が経つ」と次のループに入る。

## AsyncSpinner
ROSにはさらに`AsyncSpinner`という、別にスレッドを立ててそこで`spin()`を実行する道具も用意されている。

`spin` または `AsyncSpinner`を使えば、ほぼ即時にコールバックが呼ばれる。ただ`spin`はその処理だけでプログラムをブロッキングしてしまうので、定期的に他の処理をしたいときには向かない。そんな時（例えばサブスクライバには即時処理してほしいけど、パブリッシャの定期的処理も実施したいとき等）に、AsyncSpinnerが便利。
