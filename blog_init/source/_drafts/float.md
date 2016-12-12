float 元素

影响 inline 元素，
不影响 block 元素

![](http://img.jackon.me/float.png)

float 阻挡了 inline 但却没有阻挡 block

```
<div style="float:left; width: 80px; height: 100px; background-color: lightcoral; margin: 40px 0; font-size: 20px;">
  float
</div>

<!--   Box-3 -->
<div style="width: 100px; height: 100px; background-color: lightblue; margin: 40px 0; font-size: 20px;" >
  <div style="background-color: teal; width: 90px; height: 50px; color: #fff; " > </div>
  <span style="background-color: #47cf73; padding: 5px; font-size: 12px;" >Box-3 inner inline span</span>
</div>

```
