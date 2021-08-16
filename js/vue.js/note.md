<style>
    .important {color: red;}
</style>
# Vue.js
updated 2021.08.04

## 개요
vue.js 학습노트

## 시작하기
```sh
// vue-cli 번들러 설치
npm install -g @vue/cli
// 또는  
yarn global add @vue/cli

// 프로젝트 생성
vue create 프로젝트명
```
<p>프로젝트가 생성되면 src 폴더가 소스폴더이고 진입점은 main.js 가 초기 진입점이다.</p>

```js
// main.js
import { createApp } from 'vue';
import App from 'App.vue';
const app = createApp(App); 
app.mount('#app');

// 만약 vue-2.x 와 같은 구버전이라면 ...
var vue = new Vue({
    el: '#app',
    ...
});
```
## computed
> 데이터를 바인딩 할 때 사용
```html
<template>    
    <div>
        <label>당신의 이름은?</label>
        <input v-model="myname"/>
        <h1>{{ myname }}</h1>
    </div>
    <div>
        <label>레퍼런스 찾아보면 되는데 왜 필기하나요?</label>
        <input v-model="answer"/>
        <h1>{{ myanswer }}</h1>
    </div>
</template>
<script>
export default {
    data() {
        myname: '유민상',
        answer: '',
    },
    computed: {
        myanswer() {
            return '제가 입력한 값은: ' + this.answer;
        }
    }
}
</script>
```
<p>computed 프로퍼티는 methods 와 같은 듯 보이나 다른 점이 있음</p>
<p><b>의존하고 있는 데이터가 변경되는 경우에만 재연산</b>된다는 점이다.</p>
<p>위 예제에서는 <i>this.answer</i> 를 의존하고 있는 데 이 값이 변경되는 경우에만 <i>myanswer</i>가 실행된다.</p>
<p>computed 프로퍼티를 사용 할 때에는 괄호를 붙여서는 안된다. 마치 데이터인것 처럼 사용해야한다. (괄호를 붙이지 말 것)</p>
<p>무언가를 출력하고자 할 때 주로 사용한다. 왜냐하면 캐싱되어 필요할 때에만 동작하기 때문에 methods 보다 성능상 낫기 때문이다.</p>

## <b class="important"> 생명주기 (LifeCycle) </b>
> vue3에서 뷰 객체 생성부터 소멸까지의 생명주기
```
createApp({...})
    |
beforeCreate()
    |
created() ----------                        updated()               unmounted()
    |               |                           |                       |
beforeMount()   [템플릿 컴파일]             beforeUpdate()          beforeUnmount()
    |               |                           |                       |
mounted()-----------> [뷰 객체 생성] ----> [데이터 변경시] -------> 뷰 객체 소멸

```


## 컴포넌트 (Component)
> 재사용 가능한 스크립트

https://kr.vuejs.org/v2/guide/components-slots.html

### 전역 컴포넌트
```js
// main.js
import ExpComponent from './components/ExpComponent.vue';

app.component('exp-component' ExpComponent);
app.mount('#app');
```

### 지역 컴포넌트
> 인스턴스 범위에서만 사용가능한 컴포넌트
```js
import ExpComponent from './components/ExpComponent';

export default () {
    components: {
        ExpComponent
    }, 
}
```
### 동적 컴포넌트
> 분기에 따른 컴포넌트 렌더링이 필요 할 때 
```html
<template>
    <button @click="setSelected('A')">A</button>
    <button @click="setSelected('B')">B</button>
    <!-- 동적으로 A 또는 B 컴포넌트를 렌더링 -->
    <component :is="selected"></component>
</template>

<script>
    import A from './components/A.vue';
    import B from './components/A.vue';
    export default () {
        components: {
            A, B
        },
        data() {
            selected: null,
        },
        methods: {
            setSelected(selected) {
                this.selected = selected;
            }
        }
    }
</script>
```

#### keep-alive
> 컴포넌트를 분기했더니 데이터가 사라졌을 때
<p>기본적으로 컴포넌트가 교체 될 때 (updated) DOM의 노드가 제거된다.</p>
<p>vue 는 DOM 노드를 기억하고 싶을 때 'keep-alive' 태그를 이용하여 캐싱하도록 권장한다.</p>

```js
// App.vue
<template>
    <keep-alive>
        <!-- A 컴포넌트의 데이터는 캐싱 될 것-->
        <A />
    </keep-alive>
</template>

// A.vue
// 동적 컴포넌트를 통해 B 컴포넌트로 교체하였을 경우 A 컴포넌트가 제거되므로
// B -> A 로 다시 이동할 때 모든 노드는 새로이 생성되어 사실상 필드의 값은 사라짐
// 허나 keep-alive 태그 아래의 입력 값은 캐싱되게금 할 수 있다!
<template>
    <div>
        <input type="text" />
    </div>
</template>
```
#### teleport
> 컴포넌트지만 렌더링 할 때는 특정 DOM에서 렌더링 하고 싶을 때

```js
// teleport 태그는 DOM 이 렌더링 될 때 특정 선택자의 자식태그로 생성되게 한다.
<teleport to="#app">
    <template>
        선택자 아래에 렌더링 될 거야 #app 이니까 id가 app인 html 태그 하에 출력 될 것
    </template>
</teleport>
```

### scoped style
> 컴포넌트에서만 적용되는 스타일시트
```html
<style scoped>
    ...
</style>
```

### <b class="important">slot</b>
> 컴포넌트에 템플릿 끼워넣기
```js
// A.vue
// 만약 slot이 하나라면 default 슬롯이므로 v-slot 지시자를 생략해도 좋다.
<B>
    <template v-slot:default>
        <h1>슬롯1</h1>
    </template>
</B>

// B.vue
// (!) 이름 없는 슬롯은 자동으로 default 슬롯이 된다.
// (!) v-slot 지시자는 # 으로 축약 할 수 도 있다. (v-slot:default -> #default)
<template>
    <slot></slot>
</template>
```
> 끼워넣을 템플릿에 이름 붙이기
```js
// v-slot 지시자를 통해서 어느 슬롯에 노드 앨리먼트를 넣을지 정할 수 있다.
// A.vue
<B>
    <template v-slot:named1>
        <h1>슬롯1</h1>
    </template>
    <template v-slot:named2>
        <h1>슬롯2</h1>
    </template>
</B>
// B.vue
<template>
    <slot name="named1"></slot>
    <slot name="named2"></slot>
</template>
```
### slotProps
> 끼워넣을 템플릿에 '동적인 데이터'가 포함되어 있을 경우

```html
// A.vue
<B>
    <template v-slot:default="slotProps">
        <h1>{{ slotProps.title }}</h1>
    </template>
</B>
// B.vue
<template>
    <ul>
        <li v-for="book in boks" :key="book.id">
            <slot></slot>
        </li>
    </ul>
</template>

<script>
export default {
    data() {
        return {
            books: [
                {id: 0, title: '미움받을 용기'},
                {id: 1, title: '손자병법'},
            ]
        }
    }
}
</script>
```

### $slot
> 슬롯 객체
```html
// B.vue
<template>
    <slot name="named1"></slot>
    <slot name="named2"></slot>
    <slot></slot>
</template>
<script>
    export default {
        mounted() {
            // 컴포넌트가 마운트 될 때마다 콘솔을 찍게 될건데
            // 만약, 해당 슬롯에 아무런 템플릿이 안꽂히게 된다면
            // undefined 로 처리된다.
            console.log(this.$slots.named1);
            console.log(this.$slots.default);
        }
    }
</script>

```


## 참조
```
https://kr.vuejs.org/v2/style-guide/index.html (vue.js 표준코딩 가이드)
```