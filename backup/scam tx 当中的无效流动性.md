今天看到了一笔scam tx ，一时兴起想要研究下怎么把这种tx给过滤出来：

https://etherscan.io/tx/0x975bcbd825c014c925c3513d0ccd2ba3b77c1d1dc7f5af7fb612c440a9cb3d2d

首先分析其tx特征的话，可以看到是批量的发送。
但是这个并不为决定的一个特征去识别，因为有的时候项目方发放空头等也会采取类似的操作。
<img width="1267" alt="image" src="https://github.com/user-attachments/assets/1d6b7284-9890-478a-b692-ac54210bd571">


另外，还可以围绕着token进行一系列的分析

1、token的价格
2、token是否真实有效。

可以使用相应的价格查看工具来查看价格
<img width="899" alt="image" src="https://github.com/user-attachments/assets/801d6071-d0ee-4d5d-bbfa-4cd31c2b8a0e">

另外也可以使用一些 token 识别工具。
<img width="880" alt="image" src="https://github.com/user-attachments/assets/0636cda3-8cf4-47c3-98b0-327996d13383">

到了这里，很显然了，可以通过黑名单的方式识别scam tx。

不过，如果对于业务的范围比较确定的话，也可以采取白名单的方式仅收录目标token即可，这种方式反而更精准。




