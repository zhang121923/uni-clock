### uniMultipleSelector 多选项列表弹出框

弹框进行对象选择，组件名：``uni-multiple-selector``，代码块： uniMultipleSelector。

**使用方式：**

在 ``script`` 中引用组件 

```javascript
import uniMultipleSelector from '@/components/uni-multiple-selector/uni-multiple-selector.vue';
export default {
    components: {uniMultipleSelector}
}
```

在 ``template`` 中使用组件

```html
<uniMultipleSelector 
	:formData='receivePointArr'
	@cancel='cancel'
	@comfirm="comfirm"
	:showBox='false'>
</uniMultipleSelector>
```

**uniMultipleSelector 属性说明：**

|属性名		|类型			|默认值	       |说明						|
|---		|----	    	|---	       |---						|
|formData	|Array[Object]	|[]		       |渲染的数据				|
|showBox	|Boolean		|true		   |是否显示checkbox的勾选框	|



**事件说明：**

|事件名称	|说明							|
|-----------|-------------------------------|
|comfirm	|多选选择器点击【确定】按钮触发事件|
|cancel		|多选选择器点击【取消】按钮触发事件|

**其余详细说明：**
1.在插件市场没有搜索到类似的组件，所以自己用的时候自己写的，没有做特别的处理，但是简单，使用的童鞋修改为自己需要的即可；
2.showBox属性，主要是设置有没有勾选框，若没有勾选框，则添加背景色以区别已选和未选；

**感谢：**
