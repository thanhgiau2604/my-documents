---
sidebar_position: 2
---

# VUE JS

> Vue (phát âm là /vjuː/, giống view) là một progressive framework dùng để xây dựng giao diện người dùng ( UI ).

### 1. Chương trình VueJS đầu tiên

- index.html

```html
  <!DOCTYPE html>
  <html>
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>test Vuejs</title>
    <link rel="stylesheet" href="">
  </head>
  <body>
  <div id="app">
    {{ message }}
  </div>
  </body>
  <script ></script>
  <script ></script>
  </html>
```

- app.js
```js
  var app = new Vue({
    el: '#app',
    data: {
        message: 'helloworld!'
    }
  });
```

### 2. Instance trong VueJS

#### 2.1. Khởi tạo

```js
var vm = new Vue({
  // options
})
```

#### 2.2. Constructor 
> Khi bạn khởi tạo một instance Vue, bạn cần phải vượt qua options có thể chứa các tùy chọn cho data, template, element, methods, callback,...

Ví dụ:
```js
var app = new Vue({
    el: '#app',
    data: {
        message: 'hello world'
    },
    methods: {
        getSite : function () {
            return 'Toidicode.com'
        }
    }
});
```

#### 2.3 Thuộc tính và phương thức

Ví dụ:
```js
var app = new Vue({
    el: '#app',
    data: {
        //thuộc tính
        message: 'hello world',
        // phương thức
        getSite : function () {
            return 'Toidicode.com'
        }
    },
});
```

> Để khai báo một phương thức ( được khuyên dùng) trong vue.js thì chúng ta sẽ khai báo trong scope methods với cú pháp tương tự như khai báo thuộc tính.

```js
    <div id="app">
        <p>Message = {{ message }}</p>
        <p>Phương thức initialVue trả về: {{ initialVue() }}</p>
    </div>
    <script  type="text/javascript"></script>
    <script type="text/javascript">
        var app = new Vue({
            el: '#app',
            data: {
                message: 'hello world'
            },
            methods: {
                initialVue : function () {
                    return 'Function initial Vuew: ' + this.message;
                }
            }
        });
    </script>
```

#### 2.4 Instance Lyfecycle Hook
- Trong vue.js có các scope event sau:
    - `beforeCreate` - được gọi khi chúng ta khởi tạo vue.js và trước khi thực hiện tiến trình observe Data và init Events.
    - `created` - được gọi khi tiến trình observe Data và init Events hoàn thành.
    - `beforeMount` - được gọi ngay sau khi tiến trình render function hoàn tất.
    - `mounted` - được gọi khi tiến trình replace el hoàn tất.
    - `beforeUpdate` - được gọi khi data có sự thay đổi, và trước khi visualDOM re-rendered.
    - `updated` - được gọi khi data đã được thay đổi.
    - `beforeDestroy` - được gọi ngay trước khi vue instance được destroy.
    - `destroyed` - được gọi khi vue instance đã được destroy.

- Để hiểu rõ hơn thì bạn có thể tham khảo thêm mô hình hoạt động của 1 Vue Instance và thời điểm các scope event được gọi.

### 3. Template Syntax trong Vuejs

#### 3.1 Interpolation
> `Intercalation` là một thuật ngữ trong Vue.js. Đây là quá trình thêm một văn bản, nội dung, attribute ,.. vào các thẻ HTML bằng Vue.js.

- Để bind một đoạn văn bản vào trong một tag HTML thì bạn sử dụng cú pháp sau: `{{ avariable }}`
- Trong đó, avariable là tên thuộc tính mà chúng ta đã khai báo ở `vue instance`.
- > Chú ý: Khi bind data dưới dạng này thì dữ liệu được bind sẽ luôn luôn ở dạng thô (tức là văn bản, giống hàm text trong jquery hay innerText trong javascript) mà không được append HTML.

- Ví dụ:

```js
    <div id="app">
        <h1 class="text-red" style="text-align: center">{{ slogan }}</h1>
    </div>
    <script  type="text/javascript"></script>
    <script type="text/javascript">
        var app = new Vue({
            el: '#app',
            data: {
                slogan: 'Chào mừng bạn đã đến với website: toidicode.com'
            },
            
        });
</script>
```

- Và trong cặp dấu `{{}}` thì bạn cõ thể sử dụng các hàm của javascript.
Bind dữ liệu ra tag HTML và đồng thời chuyển đổi data đó thành in hoa.

```js
    <div id="app">
        <h1 class="text-red" style="text-align: center">{{ slogan.toUpperCase() }}</h1>
    </div>
    <script  type="text/javascript"></script>
    <script type="text/javascript">
        var app = new Vue({
            el: '#app',
            data: {
                slogan: 'Chào mừng bạn đã đến với website: toidicode.com'
            },
        });
    </script>
```

**Raw HTML**
- Nếu như bạn muốn hiển thị dữ liệu ra dưới dạng append cả HTML code (như hàm html trong jquery hoặc innerHTML trong javascript) thì bạn có thể sử dụng cú pháp sau: `<tag v-html="data"></tag>`
- Trong đó:
    - tag là các tag trong HTML.
    - data là dữ liệu mà bạn muốn bind vào tag đó (dữ liệu này thường được khai báo trong data scope của vue.js).
VD:  Bind data vào tag h1 đồng thời append các thẻ HTML vào tag h1.

```js
    <div id="app">
        <h1 class="text-red" style="text-align: center" v-html="slogan"></h1>
    </div>
    <script  type="text/javascript"></script>
    <script type="text/javascript">
        var app = new Vue({
            el: '#app',
            data: {
                slogan: 'Chào mừng bạn đã đến với website: <font color="orange">toidicode.com</font>'
            },
        });
    </script>
```

***Attributes**

- Để có thể thêm các attribute vào tag HTML bằng dữ liệu trong vue.js thì bạn sử dụng cú pháp sau: `<tag v-bind:attributeName="data"></tag>`
- Trong đó:
    - `attributeName` là tên của attribute mà bạn muốn thực hiện binding.
    - `data` là data mà bạn đã thiết lập trong vue.js.

Bind class name vào thẻ h1.
```js
    <div id="app">
        <h1 v-bind:class="className" style="text-align: center" v-html="slogan"></h1>
    </div>
    <script  type="text/javascript"></script>
    <script type="text/javascript">
        var app = new Vue({
            el: '#app',
            data: {
                slogan: 'Chào mừng bạn đã đến với website: <font color="orange">toidicode.com</font>',
                className: 'text-red',
            },
        });
    </script>
```

#### 3.2 Directives

>`Directives` trong vue.js là các attribute được thiết lập bằng các tiền tố v-. Giá trị của các attribute này thường là các biểu thức javascript duy nhất, chỉ trừ v-for

VD:
```js
    <div id="app">
        <h1 v-if="publish" v-bind:class="className" v-bind:style="textCenter">Chào mừng các bạn đã đến với website: toidicode.com</h1>
    </div>
    <script  type="text/javascript"></script>
    <script type="text/javascript">
        var app = new Vue({
            el: '#app',
            data: {
                className : "text-red",
                textCenter: "text-align: center",
                publish: true,
            },
        });
    </script>
```

**Arguments**

>Trong một số các directives nó sẽ cho phép các bạn truyền tham số vào. Để truyền tham số vào các directive này thì bạn chỉ cần ngăn giữa directives và tham số bằng dấu : 

VD:
```js
    <div id="app">
        <img v-bind:>
    </div>
    <script  type="text/javascript"></script>
    <script type="text/javascript">
        var app = new Vue({
            el: '#app',
            data: {
                logoUrl : "https://toidicode.com/upload/images/logo.png"
            },
        });
    </script>
```

VD: Thêm sự kiện click vào button
```js
    <div id="app">
        <p>Click vào button để xem kết quả</p>
        <button v-on:click="showAlert">Click</button>
    </div>
    <script  type="text/javascript"></script>
    <script type="text/javascript">
        var app = new Vue({
            el: '#app',
            methods: {
                showAlert : function () {
                    alert('bạn vừa click vào button');
                }
            }
        });
    </script>
```
**Modifiers**

> Directives dạng này cho phép bạn định nghĩa một hành động đặc biệt khi thực hiện directives đó. Các modifiers này được ngăn cách với directives bởi dấu .

VD: Thêm sự kiên submit cho form và kèm theo đó là preventDefault.

```js
    <div id="app">
        <form action="https://toidicode.com" v-on:submit.prevent="submitForm">
            <input type="text" name="name" placeholder="Nhập vào tên">
            <input type="submit" value="Gửi">
        </form>
    </div>
    <script  type="text/javascript"></script>
    <script type="text/javascript">
        var app = new Vue({
            el: '#app',
            methods: {
                submitForm: function () {
                    alert('Bạn vừa submit form');
                }
            }
        });
    </script>
```

**Filters**

- Trong cặp dấu `{{}}` thì chúng ta có thể sử dụng các biểu thức trong JavaScript.
- VD: `{{ name | uppercase }}`
- Bên cạnh điều này thì vue.js còn hỗ trợ filter trong các directive v-bind và cũng sử dụng cú pháp tương tự.
- VD: `<h1 v-bind:class="OldClass | newClass"></h1>`

VD:  filter để chuyển đổi chữ thường thành chữ hoa trong Vue.js.

```js
    <div id="app">
        <h1 class="text-red">{{ name | strUpperCase }}</h1>
    </div>
    <script  type="text/javascript"></script>
    <script type="text/javascript">
        var app = new Vue({
            el: '#app',
            data: {
                name: 'vu thanh tai'
            },
            filters : {
                strUpperCase : function (data) {
                    return data.toUpperCase();
                }
            }
        });
    </script>
```

- Nếu như bạn muốn sử dụng nhiều filters cùng một lúc  thì bạn cứ ngăn cách nó với nhau bởi dấu |.
- VD: `{{ name | strUpperCase | lastWord }}`
- Hoặc bạn cũng có thể truyền thêm các tham số vào trong filter, nhưng tham số truyền vào tiếp theo sẽ được tính từ tham số thứ 2.

```js
    <div id="app">
        <h1 class="text-red">{{ name | strUpperCase |sliceString(9) }}</h1>
    </div>
    <script  type="text/javascript"></script>
    <script type="text/javascript">
        var app = new Vue({
            el: '#app',
            data: {
                name: 'vu thanh tai'
            },
            filters : {
                strUpperCase : function (data) {
                    return data.toUpperCase();
                },
                sliceString : function (data, num) {
                    return data.slice(num); 
                }
            }
        });
    </script>
```

#### 3.3 Shorthands
- Vue.js cũng có cung cấp cho chúng ta 2 cách viết ngắn gọn hơn với 2 dạng directive là v-bind và v-on như sau:
- v-bind:

```js
    <!-- cú pháp đầy đủ -->
    <a v-bind:href="url"></a>
    <!-- cú pháp ngắn gọn -->
    <a :href="url"></a>
```

- v-on:

```js
    <!-- cú pháp đầy đủ -->
    <a v-on:click="doSomething"></a>
    <!-- cú pháp ngắn gọn -->
    <a @click="doSomething"></a>
```

### 4. Computed property và watcher trong Vue.js

#### 4.1 Computed property:

**Ưu điểm:**
-  Sử dụng lại đoạn code trên ở một vài thời điểm khác 

**Cú pháp**
- Để khai báo computed property trong Vue.js thì các bạn cần đặt nó ở trong scope object `computed`.
VD: Mình sẽ khai báo một computed chuyển đổi biến name trong data scope thành in hoa.

```js
var app = new Vue({
    el: '#app',
    data: {
        name: 'Vu Thanh Tai'
    },
    computed : {
        convertToUpper: function (){
            return this.name.toUpperCase();
        }
    }
});
```

- Nếu như trong một vài trường hợp nào đó bạn muốn sử dụng computed property ở ngoài vue instance thì bạn chỉ cần truy xuất theo cú pháp nameObject.property.

- Log ra computed convertToUpper ở trên.
```js
<div id="app">
    <p>F12 để thấy log</p>
    <h1 class="text-red">{{ convertToUpper }}</h1>
</div>
<script  type="text/javascript"></script>
<script type="text/javascript">
    var app = new Vue({
        el: '#app',
        data: {
            name: 'Vu Thanh Tai'
        },
        computed : {
            convertToUpper: function (){
                return this.name.toUpperCase();
            }
        }
    });
    console.log(app.convertToUpper);
</script>
```

***Computed vs Methods***
- `Computed` sẽ khác với `methods` ở chỗ `computed` được lưu vào cache và `computed` sẽ chỉ được thay đổi khi dữ liệu bên trong nó thay đổi.

**Getter and Setter**: bỏ qua

#### 4.2. Watcher

- `Cú pháp`: Để khai báo watcher trong vue.js thì các bạn cần phải tuân thủ các nguyên tắc sau:
    - Tên của watcher phải trùng với tên của data cần theo dõi.
    - Và các watcher phải được đặt trong watch scope.

VD: Khai báo watcher name có nhiệm vụ alert ra nội dung của biến name mỗi khi nó thay đổi.
```js
var app = new Vue({
    el: '#app',
    data: {
        name: 'Vu Thanh Tai' //giá trị ban đầu.
    },
    watch: {
        name : function () {
            alert(this.name);
        }
    }
});
//thiết lập lại giá trị
app.name = "Hoc Lap Trinh Online Toidicode.com";
```

#### 4.3. Computed vs Watcher
- `computed` và `watcher` đều được thực hiện khi chúng ta thay đổi data bên trong nó. 
- cả hai đều có thể áp dụng để thực thi một hành động nào đó khi có 1 biến phụ thuộc thay đổi.

### 5. Class và style bindings trong Vue.js


-----
Note:

v-show `<>` v-if
(Not show: display none) --> v-show

Watch: Theo dõi sự thay đổi của data



---
**Slot trong Vuejs**

- Là 1 cách để tái sử dụng Component trong Vuejs
VD:
```js
<!-- mother.vue -->
Mẹ đặt gạch 2 chỗ header và body cho con nha
<template>
  <div class="card">
    <div class="card-header">
      <slot name="header"></slot>
    </div>
    <div class="card-body">
      <slot name="body"></slot>
    </div>
  </div>
</template>
```

```js
<!-- con.vue:
Cho con dùng 2 chỗ header và body bằng nội dung mới nhé-->
<mother>
	<template slot="header">
	  <h1>Special Features</h1>
	</template>
	<template slot="body">
		<div>
		    <h5>Fish and Chips</h5>
		    <p>Super delicious tbh.</p>
		</div>
	</template>
</mother>
```

Đây là những viên gạch có đặt tên `<slot name="header"/>`

- Đề truyền dữ liệu từ mẹ sang con, chúng ta bind dữ liệu muốn truyền qua slot `<slot :ten-bien="gia-tri"/>`
`<slot name="header" :close="close"></slot>`
Component con sẽ nhận dữ liệu thông qua từ khóa slot-scope
```js
<template #header slot-scope="{close}">
		<h1>{{ close ? ‘Closed’ : ‘Open’ }}</h1>
</template>
```