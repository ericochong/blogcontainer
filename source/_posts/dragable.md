---
title: 结合vuedragable、vuex、elementUI,仿淘宝旺铺装修
tags: Vue,Vuedragable,
grammar_cjkRuby: true
---

在产品大大的天马行空的贪欲之下，他想出的新的折磨人的想法，那就是结合以前的做过的组件，做一个自定义页面组件，简单来说就是仿照淘宝的旺铺装修，就是下图的效果，LZ我脸上笑嘻嘻，心里是万马奔腾，就是下图的效果：
![淘宝旺铺装修](https://imgsa.baidu.com/exp/pic/item/99636c0e0cf3d7cac2635458fe1fbe096a63a9cc.jpg)
在花了一整天的时间在度娘的海洋里遨游了一番后，基本找到了最佳的插件，那就是vuedragable，虽然这个插件很轻量，但已经满足了我所需要的所有功能，那就是拖拽功能，配合vuex就能进行多组件间的数据交流。话不多说，下面简单描述我项目demo书写过程。

## 一、项目准备
整个项目所需的最主要的有vuex、vuedragable、elementUI(因为公司项目是按elementUI构建的，所以小编这里也使用同样的，有需要的也可以用别的组件库)。这边仅贴出各个组件的网址，不知道怎么用的小伙伴请，仔细浏览API文档。
vuedragable：https://www.npmjs.com/package/vuedraggable
vuex: https://vuex.vuejs.org/
elementUI: http://element-cn.eleme.io/#/zh-CN

## 二、页面构建
整个项目包含的就是三部分：
第一部分就是组件预览部分，这部分仅显示组件的缩略图、名字和使用次数以及使用上限。
第二部分是组件的容器，也就是最终成型的页面的样子，在第二部分可以进行组件间的排序和删除不需要的组件。
第三部分是每个组件的编辑窗口，即，各个组件的数据编辑，如图片上传，标题，跳转等的编辑，组件间的数据是相互独立的。
![enter description here](./images/1539419896(1).jpg)

## 三、第一部分

``` html
<draggable class="listContainer" v-model="dragList" :options="dragOption" :move="getData" @end="onEnd">
      <div class="listItem" v-for="v in dragList" :key="v.componentId">
        <div class="componentName">
          <p class="itemPic"></p>
          <p>{{v.componentName}}</p>
          <p>{{v.usedNum?v.usedNum:0}}/{{v.componentMax}}</p>
        </div>
      </div>
 </draggable>
 <div class="dragPlace">
      <dragHere @changeUsedNum="changeUsedNum"></dragHere>
</div>
```	
``` js
//第一部分组件
//数据
data () {
    return {
      dragList:list.dragList,
      dragOption: {//为vuedragable组件的选项，可以设置拖拽各种选项，详情请看组件API
          group: {
              name: 'drag', //这个很重要，其他的与之能产生关联的拖拽框就靠这name 一定要一致
              pull: 'clone', //clone表示拖动时复制目标到目的组件内
              put: false
          },
          sort: false,//默然为true。这里我们只需要他拖拽，无需能拖动排序,
          animation: 150,
      },
      currentDrag:{}//保存当前拖动的对象
    }
  }
  
  //:move和@end为组件自带属性和方法，同时move属性必须配合v-model一起使用，v-model绑定的是组件的列表，move在目标元素拖入目的组件后会触发这个属性的callback方法，@end是当鼠标拖动组件松开是触发。
  //move的callback方法
  getData(evt){
      this.currentDrag = evt.draggedContext.element;
    },
  //@end方法
  onEnd(evt){
      if(evt.from !== evt.to){//出发点与目的地不一样是才执行
        let data = {
          index:evt.newIndex,
          drag:this.currentDrag
        }
        this.$store.commit('newDrag',data);//触发vuex的数据增加方法
        this.dragList.forEach(ele=>{//拖动结束增加使用次数
          if(this.currentDrag.componentId == ele.componentId){
            if(ele.usedNum < ele.componentMax){
              ele.usedNum += 1;
            }else{//超过最大使用上限后，弹窗警告，执行vuex删除方法
              this.$message({ type: 'error', message: '已经超过使用次数' });
              this.$store.commit('deleteThis',evt.newIndex)
            }
          }
        })
        this.$store.state.curIndex = evt.newIndex;
      }
    },
	changeUsedNum(res){//被动触发，组件被删除时候，减少使用次数
      this.dragList.forEach(ele=>{
        if(res.componentId == ele.componentId){
          ele.usedNum -= 1;
        }
      })
    }
	//涉及的vuex
	newDrag(state, res) {//onend后触发 新增组件进新的数组，以及通过addData想当前组件添加默认子数据
            let obj = {};
            for (let k in res.drag) {
                obj[k] = res.drag[k];
                obj.index = state.dragContent.length;
                this.commit('addData', res.drag.dragcomponentName);
                obj.data = state.saveContent;
            }
			//这边在一开始写的时候，小编用的是push，这样会导致所增加的组件添加在尾部，跟随意位置插入这个需求产生冲突，所以改为用slice随意位置插入数组
            state.dragContent.splice(res.index, 0, obj)
        },
		addData(state, name) {//设置各组件的默认自数据结构
            let obj = {};
            switch (name) {
                case 'bannerModel':
                    obj.autoplay = 3000;
                    obj.hideBlank = true;
                    obj.swiperImg = [{
                        showAdd: false,
                        imgRequired: false,
                        srcImg: 'https://gw.alicdn.com/tfs/TB1nkM1SXXXXXXmaXXXXXXXXXXX-750-359.gif',
                        url: '',
                        uploadButtonName: '上传图片', //按钮提示文字
                    }];
                    break;
                case 'singlePicModel':
                    obj = {
                        imgUrl: "http://gw.alicdn.com/tfs/TB1kV3QRVXXXXa.XFXXXXXXXXXX-750-376.png_790x10000.jpg",
                        textContent: "",
                        url: "",
                        isMargin: true,
                        uploadButtonName: '上传图片', //按钮提示文字
                    }
                    break;
            }
            state.saveContent = obj
        },
		deleteThis(state, index) {//删除数组对应位置的组件数据
            state.dragContent.splice(index, 1)
        },
```
## 第二部分
```html
<!-- 第二部分容器部分 -->
<draggable :options="dragOption_2" class="app-sort item">
        <div class="" v-for="(i,v) in dragContent" :key="v">
			<!-- 通过组件名动态加载相应组件 -->
            <div
                :is="i.dragcomponentName"
                :index="v"
                @changeUsedNum="changeUsedNum"
            >
            </div>
        </div>
</draggable>

```
```js
//第二部分容器部分
//数据
data(){
      return {
          dragOption_2: {
              group: {
                  name: 'drag', //这个很重要，其他的与之能产生关联的拖拽框就靠这name 一定要一致
                  pull: false,//设置为false禁止拖动
                  put: true,//允许放下
              },
              sort: false,//禁止排序
              animation: 150,
              filter:'.item'//效果是对应className的组件不允许拖动
          },
      }
    },
	//删除组件被动触发使用次数改变
	changeUsedNum(data){
          this.$emit('changeUsedNum',data);
      },
	  //第二部分组件数据部分（以轮播图组件为例子）
```
```html
<!-- 第二部分各组件部分 -->
<div :class="[{'active':index == curIndex},'bannerContent']" ref="bannerContent" @click="getIndex">
        <div class="picContent">
            <el-carousel class="slider" height="189px">
                <el-carousel-item class="imgItem" v-for="(v,i) in dragContent[index].data.swiperImg" :key="i" >
                    <img :src="v.srcImg" alt="">
                </el-carousel-item>
            </el-carousel>
        </div>
        <div class="editContainer" @click.stop="prevent">
            <div class="modelName" v-show="index !== curIndex">{{dragContent[index].componentName}}</div>
            <div class="editPanel" v-show="index === curIndex">
                <div :class="[{'active':dragContent[index].isShowEdit},'edit']">
                    <i class="iconfont icon-setting" @click.stop="showEdit(index)"></i>
                </div>
                <div class="up">
                    <i @click.stop="goUp" :class="[{'notAllow':index == 0},'iconfont','icon-arrowup']"></i>
                </div>
                <div class="down">
                    <i @click.stop="goDown" :class="[{'notAllow':index == dragContent.length-1},'iconfont','icon-arrowdown']"></i>
                </div>
                <div class="delete">
                    <i @click.stop="deleteThis" class="iconfont icon-trash"></i>
                </div>
				<!-- 编辑窗口 -->
                <bannerModelEdit @whiteSpace="whiteSpace" :index="this.index" v-show="dragContent[index].isShowEdit"></bannerModelEdit>
            </div>
        </div>
    </div>
```
```js、
	//向上移
		goUp(){
            this.$store.commit('goUp',this.index);
        },
		//向下移
        goDown(){
            this.$store.commit('goDown',this.index);
        },
		//删除当前组件
        deleteThis(){
            let data = {
                ccId:this.$store.state.dragContent[this.index].ccId,
                componentId:this.$store.state.dragContent[this.index].componentId
            }
            this.$emit('changeUsedNum',data);
            this.$store.commit('deleteThis',this.index);
        },
		
		
		//vuex部分
		goUp(state, index) {
            let temArr = state.dragContent;
            if (index == 0) {
                return false;
            } else {
                state.dragContent = [];//重置为空，再次赋值能触发视图更新
                let temp = temArr[index];
                temArr[index] = temArr[index - 1];
                temArr[index - 1] = temp;
            }
            state.curIndex = index - 1;
            state.dragContent = temArr;
        },
        goDown(state, index) {
            let temArr = state.dragContent;
            if (index == (temArr.length - 1)) {
                return false;
            } else {
                state.dragContent = [];
                let temp = temArr[index];
                temArr[index] = temArr[index + 1];
                temArr[index + 1] = temp;
            }
            state.curIndex = index + 1;
            state.dragContent = temArr;
        },
```

## 第三部分
以图片轮播组件为例子解析
```html
<!-- 显示的是图片上传部分 -->
<div class="content">
            <div class="tips">
                <p class="tipTitle">
                    <span class="title">上传图片</span>
                    <span class="tipRequired">*</span>
                </p>
                <p class="tipContent">建议图片宽度750,高度275，支持类型:jpg、png。要求一组内的图片高度必须完全一致。</p>
            </div>
            <div class="picContent">
                <div  class="picItem" :index="i" v-for="(v,i) in saveContent" :key="i">
                    <div class="itemMove">
                        <div class="up">
                            <i :class="[{'notAllow':i == 0},'iconfont','icon-arrowup']" @click="toUp(i)"></i>
                        </div>
                        <div class="down">
                            <i :class="[{'notAllow':i == saveContent.length-1},'iconfont','icon-arrowdown']" @click="toDown(i)"></i>
                        </div>
                    </div>
                    <div class="addPic">
                        <div :class="['add',{'alert':v.imgRequired}]" @mouseover="mouseover(i)" @mouseout="mouseout(i)">
                            <span class="addicon" v-show=" v.srcImg===''|| v.srcImg === defaultImg ">
                                <i class="iconfont icon-i-add"></i>
                            </span>
                            <img :class="'showPic'+i" :src="v.srcImg" ref="uploadPic" v-show="v.srcImg && v.srcImg !== defaultImg">
                            <div class="hoverContent" v-show="v.showAdd">
                                <span class="upload" @click="addPic(i)">{{v.uploadButtonName}}</span>
                            </div>
                            <div class="loadingContent" v-show="isShowLoading&&i==nowUpload">
                                <div class="loading">
		  							              <i class="el-icon-loading"></i>
		  						              </div>
                            </div>
                        </div>
                        <div class="picLink">
                            <span class="linkContent">
                                <input class="link text" v-model="v.url" type="text" placeholder="请输入合法的无线连接">
                                <i class="iconfont icon-close" @click="clearItem" v-show="v.url"></i>
                                <i class="iconfont icon-link"></i>
                            </span>
                        </div>
                    </div>
                    <div class="deletePic" @click="deleteThis(i)">
                        <i :class="[{'notAllow': saveContent.length == 1},'iconfont','icon-trash']"></i>
                    </div>
                </div>
                <div class="panelAdd" @click="addContent">
                    <i class="iconfont icon-i-add"></i>
                    添加 {{saveContent.length}} / {{maxPicContainer}}
                </div>
            </div>
        </div>
```
```js
//鼠标移入
        mouseover(i){
            let data = {
                pIndex:this.index,
                index:i
            }
            this.$store.commit('mouseover',data)
        },
        //鼠标移出
        mouseout(i){
            let data = {
                pIndex:this.index,
                index:i
            }
            this.$store.commit('mouseout',data)
        },
        //上传图片触发
        addPic(i){
            this.nowUpload = i;
            this.$refs.file.click();
        },
        //图片上传
        picUpload(){
            this.isShowLoading = true;
            let reader = new FileReader();
            let file = this.$refs.file.files[0];
            let imgUrlBase64;
            if(file){
              imgUrlBase64 = reader.readAsDataURL(file);
              reader.onload = (e)=>{
                let index = this.nowUpload
                this.picSrc[index].srcImg = reader.result;
                this.picSrc[index].imgRequired = false;
                this.picSrc[index].uploadButtonName = "替换图片";
                this.isShowLoading = false;
              }
            }
        },
        //清除商品
        clearItem(){
            this.picSrc[this.popIndex].url = '';
        },
        //增加图片容器
        addContent(){
            if(this.picSrc.length < this.maxPicContainer){
                this.picSrc.push({
                    showAdd:false,
                    imgRequired:false,
                    srcImg:this.defaultImg,
                    url:'',
                    uploadButtonName:'上传图片',//按钮提示文字
                })
            }else{
                this.$message({type:'error',message:'最多只能有'+this.maxPicContainer+'张图片！'})
            }
        },
        //容器上移
        toUp(index){
            let data = {
                pIndex:this.index,
                index:index
            };
            this.$store.commit('bannerToUp',data);
        },
        //容器下移
        toDown(index){
            let data = {
                pIndex:this.index,
                index:index
            };
            this.$store.commit('bannerToDown',data);
        },
		
		
		//vuex
		mouseover(state, res) {
            Vue.set(state.dragContent[res.pIndex].data.swiperImg[res.index], 'showAdd', true)
        },
        mouseout(state, res) {
            state.dragContent[res.pIndex].data.swiperImg[res.index].showAdd = false;
        },
        bannerToUp(state, res) {
            let tempArr = state.dragContent[res.pIndex].data.swiperImg;
            if (res.index == 0) {
                return false;
            } else {
                state.dragContent[res.pIndex].data.swiperImg = [];
                let temp = tempArr[res.index];
                tempArr[res.index] = tempArr[res.index - 1];
                tempArr[res.index - 1] = temp;
            }
            console.log(tempArr)
            state.dragContent[res.pIndex].data.swiperImg = tempArr;
        },
        bannerToDown(state, res) {
            let tempArr = state.dragContent[res.pIndex].data.swiperImg;
            if (res.index == (tempArr.length - 1)) {
                return false;
            } else {
                state.dragContent[res.pIndex].data.swiperImg = [];
                let temp = tempArr[res.index];
                tempArr[res.index] = tempArr[res.index + 1];
                tempArr[res.index + 1] = temp;
            }
            state.dragContent[res.pIndex].data.swiperImg = tempArr;
        }
```


## 结束语
因为小编学习vue还不深刻，可能会有很多冗余的代码存在，大神就别看了，一些需要做这种需要的小伙伴可以借鉴一下，项目完整代码在https://github.com/ericyizhuang/coderepository.git