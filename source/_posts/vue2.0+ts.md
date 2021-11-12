---
title: Vue组件的Ts写法
tags:
- ts
- vue2.0
comments: true
categories: '前端'
password: hello
abstract: 有东西被加密了, 请输入密码查看.
message: 您好, 这里需要密码.
wrong_pass_message: 抱歉, 这个密码看着不太对, 请再试试.
wrong_hash_message: 抱歉, 这个文章不能被校验, 不过您还是能看看解密后的内容.
---

#### Vue组件的Ts写法
---
```
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <!-- 省略 -->
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';

@Component
export default class HelloWorld extends Vue {
  @Prop() private msg!: string;
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
```
```
//vue 组件模板原始写法：
import {componentA,componentB} from '@/components';

export default {
	components: { componentA, componentB},
	props: {
    propA: { type: Number },
    propB: { default: 'default value' },
    propC: { type: [String, Boolean] },
  }
  // 组件数据
  data () {
    return {
      message: 'Hello'
    }
  },
  // 计算属性
  computed: {
    reversedMessage () {
      return this.message.split('').reverse().join('')
    }
    // Vuex数据
    step() {
    	return this.$store.state.count
    }
  },
  methods: {
    changeMessage () {
      this.message = "Good bye"
    },
    getName() {
    	let name = this.$store.getters['person/name']
    	return name
    }
  },
  // 生命周期
  created () { },
  mounted () { },
  updated () { },
  destroyed () { }
}
```
```
//vue组件模板修饰器写法:
import { Component, Vue, Prop } from 'vue-property-decorator';
import { State, Getter } from 'vuex-class';
import { count, name } from '@/person'
import { componentA, componentB } from '@/components';

@Component({
    components:{ componentA, componentB},
})
export default class HelloWorld extends Vue{
  @Prop(Number) readonly propA!: number | undefined
  @Prop({ default: 'default value' }) readonly propB!: string
  @Prop([String, Boolean]) readonly propC!: string | boolean | undefined
  
  // 原data
  message = 'Hello'
  
  // 计算属性
	private get reversedMessage (): string[] {
  	return this.message.split('').reverse().join('')
  }
  // Vuex 数据
  @State((state: IRootState) => state . booking. currentStep) step!: number
	@Getter( 'person/name') name!: name
  
  // method
  public changeMessage (): void {
    this.message = 'Good bye'
  },
  public getName(): string {
    let storeName = name
    return storeName
  }
  // 生命周期,不公开其他组件，但method可能@emit向父组件传值，所以前者加private后者不加
  private created ()：void { },
  private mounted ()：void { },
  private updated ()：void { },
  private destroyed ()：void { }
}
```
#### 引入全局模块
```
// npm i VueI18n

import Vue from 'vue';
import App from './App.vue';
import router from './router';
import store from './store';
// 新模块
import i18n from './i18n';

Vue.config.productionTip = false;

new Vue({
    router, 
    store, 
    i18n, // 新模块
    render: (h) => h(App),
}).$mount('#app');
```
```
// 还需要动src/vue-shim.d.ts
// 声明全局方法
declare module 'vue/types/vue' {
  interface Vue {
        readonly $i18n: VueI18Next; // 这表示this下有这个东西
        $t: TranslationFunction;
    }
}
//之后使用this.$i18n()的话就不会报错了。
```
##### 安装vue-axios 简单使用Axios
```
/* $ npm i axios vue-axios
在main.ts中引入 */
import Vue from 'vue'
import axios from 'axios'
import VueAxios from 'vue-axios'

Vue.use(VueAxios, axios)

//然后在组件内使用：
this.axios.get(api).then((response) => {
  console.log(response.data)
})
```
###### 封装劫持请求与返回
```
// 目录
-api
    - main.ts   // 实际调用
-utils
    - request.ts  // 接口封装
```
```
// request.ts
import * as axios from 'axios';
import store from '@/store';
// 这里可根据具体使用的UI组件库进行替换
import { Toast } from 'vant';
import { AxiosResponse, AxiosRequestConfig } from 'axios';
 
 /* baseURL 按实际项目来定义 */
const baseURL = process.env.VUE_APP_URL;

 /* 创建axios实例 */
const service = axios.default.create({
    baseURL,
    timeout: 0, // 请求超时时间
    maxContentLength: 4000,
});

service.interceptors.request.use((config: AxiosRequestConfig) => {
    return config;
}, (error: any) => {
    Promise.reject(error);
});

service.interceptors.response.use(
    (response: AxiosResponse) => {
        if (response.status !== 200) {
            Toast.fail('请求错误！');
        } else {
            return response.data;
        }
    },
    (error: any) => {
        return Promise.reject(error);
    });
    
export default service;
```
```
// main.ts
// api/main.ts
import request from '../utils/request';

// get
export function getSomeThings(params:any) {
    return request({
        url: '/api/getSomethings',
    });
}

// post
export function postSomeThings(params:any) {
    return request({
        url: '/api/postSomethings',
        methods: 'post',
        data: params
    });
}

```



