

后台页面

## 安装vue-element-template

```
# clone the project
git clone https://github.com/PanJiaChen/vue-admin-template.git

# enter the project directory
cd vue-admin-template

# install dependency
npm install
 
# develop
npm run dev
```

## 跨域

### 修改后台配置项

在根目录下的`vue.config.js`

修改proxy

```js
proxy: {
      // change xxx-api/login => mock/login
      // detail: https://cli.vuejs.org/config/#devserver-proxy
      '/api': {
        target: 'http://localhost:3000/api',//自己的后台端口地址
        changeOrigin: true,
        pathRewrite: {//路径重写
          '^/api': ''
        }
      }
    },
```

修改@/utils/request

将baseUrl修改

```js
const service = axios.create({
  baseURL: '/api', // url = base url + request url
  // withCredentials: true, // send cookies when cross-domain requests
  timeout: 5000 // request timeout
})
```

## 登录模块

### 登录

### 获取用户信息

### 登出



## 首页修改

### 替换中文

##用户管理

强制刷新

过滤器

按钮切换

分页

https://www.jianshu.com/p/70facd19ec55

```html
<template>
  <div class="form-content">
    <h1>添加轮播图</h1>
    <el-form ref="form" :model="form" label-width="90px">
      <el-form-item label="标题">
        <el-input v-model="form.name" ></el-input>
      </el-form-item>
      <el-form-item label="跳转链接">
        <el-input v-model="form.date1"></el-input>
      </el-form-item>
      <el-form-item label="排序">
        <el-input v-model="form.date2"></el-input>
      </el-form-item>
      <el-form-item label="上传图片">
        <el-upload
          class="avatar-uploader"
          action="/api/slides/upload"
          :show-file-list="false"
          :on-success="handleAvatarSuccess"
          :before-upload="beforeAvatarUpload">
          <img v-if="imageUrl" :src="imageUrl" class="avatar">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSubmit">立即创建</el-button>
        <el-button>取消</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      form: {
        name: '',
        date1: '',
        date2: ''
      },
      imageUrl: ''
    }
  },
  methods: {
    onSubmit() {
      console.log('submit!')
    },
    handleAvatarSuccess(res, file) {
      this.imageUrl = URL.createObjectURL(file.raw)
    },
    beforeAvatarUpload(file) {
      const isJPG = file.type === 'image/jpeg'
      const isLt2M = file.size / 1024 / 1024 < 2

      if (!isJPG) {
        this.$message.error('上传轮播图只能是 JPG 格式!')
      }
      if (!isLt2M) {
        this.$message.error('上传轮播图大小不能超过 2MB!')
      }
      return isJPG && isLt2M
    }
  }
}
</script>

<style scoped>
.form-content{
  width: 880px;
  margin: 30px;
}
h1{
  margin-bottom: 40px;
}
.avatar-uploader .el-upload {
  border: 2px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}
.avatar-uploader .el-upload:hover {
  border-color: #409EFF;
}
.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 400px;
  height: 178px;
  line-height: 178px;
  text-align: center;
}
.avatar {
  width: 346px;
  height: 178px;
  display: block;
}
</style>

```



## 轮播图管理（上传图片和数据）

### 后台

####轮播图schema

轮播图的骨架如下：

```js
const mongoose = require("mongoose")
const Schema = mongoose.Schema;
const SlideSchema = new Schema({
    title: {
        type: String,
        required: true
    },
  	//标题
    img: {
        type: String,
        required: true
    },
  	// 图片的名字
    order: {
        type: Number,
        required: true
    },
  	//排序
    data: {
        type: Date,
        default: Date.now
    },
  	// 时间（老于...，不想改了）
    imgurl: {
        type: String
    },
  	// 图片在电脑存贮的位置
    status: {
        type: Number,
        default: 1
    },
    // 状态
    newsID: {
        type: String,
        default: '0'
    }
    // 后面有新闻转换成轮播图的功能，所以后来添加了这个关联的ID
})
module.exports = Slide = mongoose.model("slides", SlideSchema)

```

#### formidable

后台采用express，以前req.file的方法基本被放弃了，现在官方推荐采用中间件的方法
首先我们安装`formidable`
`yarn add formidable`或者 `npm install formidable`
在后面实际操作图片的时候还需要用到`fs`，`path`模块，`node`自带的，直接应用即可
下面是基础用法，可以先试着对接口

#### 后台测试代码

```js
const formidable = require('formidable')
router.post('/upload', (req, res, next) => {
    var form = new formidable.IncomingForm()
    form.parse(req, (err, fields, files) => {
        if(err) return next(err)
        console.log(fields) //Object 表单数据
        console.log(files) //上传文件用files.<name>访问
        res.json({ code: 1, message: 'upload success' })
    })
})
//基础用法,先测接口，自己打印出来看看。
```



#### 后台实际代码

```js
const express = require('express')
const Slide = require("../../model/Slide")
const router = express.Router();
const formidable = require('formidable')
const path = require('path')
const fs = require('fs')

router.post('/upload', (req, res, next) => {
    let form = new formidable.IncomingForm()
    form.encoding = 'utf-8' // 编码
    form.keepExtensions = true // 保留扩展名
    form.uploadDir = path.join(__dirname, '../../public/images') //文件存储路径 最后要注意加 '/' 否  则会被存在public下
    form.parse(req, (err, fields ,files) => { // 解析 formData 数据
        if(err) return next(err)
        let title = fields.title //标题
        let order = fields.order //排序
        let jump = fields.jump //跳转链接
        let oldPath = files.file.path //获取文件路径 ~/public/images/<随机生成的文件名>.<扩展名>
        let imgname = files.file.name //前台上传时的文件名 也就是文件原本的名字
        let slideImgname = imgname.replace(/[^.]+/, title) //把扩展名前的文件名给替换掉
        // let imgurl = `file://${oldPath}`
        //我这里为了方便存储 统一将文件名改为 <标题名>.jpg
        let newPath = path.join(path.dirname(oldPath), slideImgname)
        // let imgurl = `file://${newPath}`
        fs.rename(oldPath, newPath, (err) => {
            if(err) return next(err)
            const newSlide = new Slide({
                title: title,
                order: order,
                jump: jump,
                img: slideImgname,
                imgurl: newPath
            })
            newSlide.save((err, data) =>{
                if (err){throw err}
                res.json({
                    code:200,
                    success: true,
                    data: data
                })
            })

        })
    })
})
```

参考链接：

https://www.cnblogs.com/nbh520/p/10692879.html

https://blog.csdn.net/qwe502763576/article/details/79659548

https://www.cnblogs.com/qimeng/p/9552275.html



### 前端代码

#### 前端基础代码

```html
<el-upload 
  :action="$apiURL + '/upload'" <!--后台上传地址，直接写接口地址--> 
  :data="uploadData" <!--需要传到后台的附加数据 例如标题，内容，作者等等-->
  :show-file-list="false" 
  :on-success="getAvatarSuccess" <!--上传成功回调--> 
  :before-upload="beforeAvatarUpload"> <!--上传前调用的钩子--> 
  <img :src="$imageURL + avatar" class="avatar" />
</el-upload>
```

#### 前端实际代码

```html
<template>
  <div class="form-content">
    <h1>添加轮播图</h1>
    <el-form ref="ruleForm" :model="ruleForm" :rules="rules" label-width="100px">
      <el-form-item label="标题" prop="title">
        <el-input v-model="ruleForm.title" style="width: 500px" />
      </el-form-item>
      <el-form-item label="跳转链接" prop="jump">
        <el-input v-model="ruleForm.jump" style="width: 500px" />
      </el-form-item>
      <el-form-item label="排序" prop="order">
        <el-input v-model="ruleForm.order" style="width: 500px" />
      </el-form-item>
      <el-form-item ref="uploadElement" label="上传图片" prop="imageUrl">
        <el-input v-if="false" v-model="ruleForm.imageUrl" />
        <el-upload
          ref="upload"
          class="avatar-uploader"
          :show-file-list="false"
          action="/api/slides/upload"
          :before-upload="beforeUpload"
          :on-change="handleChange"
          :auto-upload="false"
          :data="ruleForm"
        >
          <img v-if="ruleForm.imageUrl" :src="ruleForm.imageUrl" class="avatar" alt="">
          <i v-else class="el-icon-plus avatar-uploader-icon" />
        </el-upload>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="submitForm('ruleForm')">立即创建</el-button>
        <el-button @click="resetForm('ruleForm')">重置</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      ruleForm: {
        title: '',
        order: '',
        jump: '',
        imageUrl: ''
      },
      rules: {
        title: [{
          required: true, message: '请输入活动名称', trigger: 'blur'
        }
        ],
        order: [{
          required: true, message: '请输入排序', trigger: 'blur'
        }
        ],
        jump: [{
          required: true, message: '请输入跳转地址', trigger: 'blur'
        }
        ],
        imageUrl: [{
          required: true, message: '请上传图片', trigger: 'blur'
        }
        ]
      }
    }
  },
  methods: {
    onSubmit() {
      console.log('submit!')
    },
    submitForm(formName) {
      const vm = this
      this.$refs[formName].validate((valid) => {
        if (valid) {
          vm.$refs.upload.submit()
          this.$router.push({ path: '/slide' })
        } else {
          return false
        }
      })
    },
    resetForm(formName) {
      this.$refs[formName].resetFields()
      this.ruleForm.imageUrl = ''
    },

    handleChange(file, fileList) {
      this.ruleForm.imageUrl = URL.createObjectURL(file.raw)
    },

    beforeUpload(file) {
      return true
    }
  }
}
</script>

<style scoped>
.form-content{
  width: 880px;
  margin: 30px;
}
h1{
  margin-bottom: 40px;
}
.avatar-uploader .el-upload {
  border: 2px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}
.avatar-uploader .el-upload:hover {
  border-color: #409EFF;
}
.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 400px;
  height: 178px;
  line-height: 178px;
  text-align: center;
}
.avatar {
  width: 346px;
  height: 178px;
  display: block;
}
.avatar-uploader-icon[data-v-4bf41119] {
  font-size: 28px;
  color: #8c939d;
  width: 400px;
  height: 178px;
  line-height: 178px;
  text-align: center;
  border: 1px solid #DCDFE6;
  border-radius: 5px;
}
</style>

```



## 富文本编辑器

富文本编辑器下载了直接用，链接：

https://github.com/PanJiaChen/vue-element-admin/tree/master/src/components

在组件库下的[Tinymce](https://github.com/PanJiaChen/vue-element-admin/tree/master/src/components/Tinymce)

放在自己的组件库`components`下面

### 修改中文

找到Tinymce/index.js   

代码119行，修改成`language: this.languageTypeList['zh']`就可以了

### 使用

在需要使用的页面引入注册就可以了

```js
import Tinymce from '@/components/Tinymce' //引入
export default {
  components: { Tinymce }, //注册
  data（ ）{
 			return{
  
			} 
	}
}
```

## 新闻编辑页面

```html

```







## icon的优化






## bcrypt

### Usage

Async (recommanded)

```

```

### Check password

```
//First，load your hash from your password DB
bcrypt
```







## body-parser

Node.js body parsing middleware.

用来解析post请求的body，如果没有这个模块解析不了post请求

### 安装(installation)



### 配置

```javascript

const bodyParser = require('body-parser');

app.use(bodyParser.urlencoded({ extended: false }))
app.use(bodyParser.json())
```

## 图片（七牛云）

前端采用element-ui库，vue技术栈，后台采用express

使用七牛云有很多种方法，可以在前端直接上传的七牛云，也可以把数据传到后端，再又后端传到七牛云。

这里我采用的是第一种，在前端直接传送图片到七牛云，然后通过`el-upload`的`onsuccess`钩子直接拿到七牛返回的数据。（具体往后看）

### 七牛云

* 首先注册，实名认证，需要大概一天。
* 进入控制台，选择对象存储，新建仓库，记住存储空间名字，还有存储的区域
* 在新建的存储空间的首页可以看到融合 CDN 测试域名，这个是送的30天外链域名。读图片就从这个域名读

前期准备就这样



### 后台

后台需要做的主要是获取token，前端页面在created的钩子调用就行了

#### 安装七牛

`npm install qiniu` 或`yarn add qiniu`

#### 后台配置获取token

在任何一个子路由下写就可以了。

```js
const express = require('express')
const router = express.Router();
const qiniu = require('qiniu') // 引入七牛

var bucket = 'dj-cloud';  
// 空间名
var imageUrl = 'pxchssngy.bkt.clouddn.com'; 
// 域名名称（我用的是融合 CDN 测试域名，有自己域名也可以绑定）
var accessKey = '5NuGKkcA5uPhIW9SYkyMlLIQ2W4OAfe-pH89W9NT';  //个人中心-密匙管理，每个人都不一样
var secretKey = 'U8siCLyHci4tYtRlZEDqMI8EOv3teD5wtv9Fk2kL';  //个人中心-密匙管理，每个人都不一样
var mac = new qiniu.auth.digest.Mac(accessKey, secretKey);

var options = {
    scope: bucket,
};
var putPolicy = new qiniu.rs.PutPolicy(options);
var uploadToken = putPolicy.uploadToken(mac);

var config = new qiniu.conf.Config();
config.zone = qiniu.zone.Zone_z1;  // 机房区域 华北是z1 具体看官方文档

router.get('/token', (req, res) => {
   res.json({
       code: 200,
       uploadToken
   })
})

```

需要说明的是`accessKey`和`secretKey`，还有`config.zone`都是自己的七牛云的数据，不要直接复制我的。



### 前端

```html
        <el-upload
          ref="upload"
          :multiple="true"
          action="https:///upload-z1.qiniup.com"
          accept="image/jpeg,image/gif,image/png,image/bmp"
          :before-upload="beforeUpload"
          :on-change="handleChange"
          :data="postData"
          :on-success="successupload"
        >
          <img v-if="imageUrl" :src="imageUrl" class="avatar" alt="">
          <i v-else class="el-icon-plus avatar-uploader-icon" />
        </el-upload>
```

其中 ：

* `action`是你的对象存储空间的[存储区域](https://developer.qiniu.com/kodo/manual/1671/region-endpoint)
* `:data="postData"`在data中定义postData,起什么名字无所谓

```js
      
      data(){
      	return{
      	  postData: {
      		token: ''
    			},
     			imageUrl: ''
      	}
      }

```

* `:on-success="successupload"` 在图片上传之后自动调用的钩子，可以在这里获取七牛云返回的数据

```js
    successupload(response, file, fileList) {
      this.ruleForm.imageUrl = `http://pxchssngy.bkt.clouddn.com/${response.key}`
    },
     //ruleForm是我要提交到数据库的表单。
     //http://pxchssngy.bkt.clouddn.com/这个七牛云送的外链域名，也就是融合 CDN 测试域名
     //response里面存的key就是上传到七牛的图片的名称
     //拼接上自己的测试域名，和key，就可以得到图片的网络地址，然后存到数据库中。
```

* 现在是点击完图片就自动提交，如果要阻止默认时间，就加个`auto-upload=“false”`，然后给`el-upload`标签绑定一个ref，通过触发`this.$refs.upload.submit()`来完成图片的提交，方法可以自己发挥。

#### 前端完整代码（示例）

```html
<template>
  <div class="form-content">
    <h1>添加轮播图</h1>
    <el-form ref="ruleForm" :model="ruleForm" label-width="100px">
      <el-form-item label="标题" prop="title">
        <el-input v-model="ruleForm.title" style="width: 500px" />
      </el-form-item>
      <el-form-item label="跳转链接" prop="jump">
        <el-input v-model="ruleForm.jump" style="width: 500px" />
      </el-form-item>
      <el-form-item label="排序" prop="order">
        <el-input v-model="ruleForm.order" style="width: 500px" />
      </el-form-item>
      <el-form-item ref="uploadElement" label="上传图片" prop="imageUrl">
        <el-input v-if="false" v-model="imageUrl" />
        <el-upload
          ref="upload"
          class="avatar-uploader"
          :show-file-list="false"
          :multiple="true"
          action="https:///upload-z1.qiniup.com"
          accept="image/jpeg,image/gif,image/png,image/bmp"
          :before-upload="beforeUpload"
          :on-change="handleChange"
          :data="postData"
          :on-success="successupload"
        >
          <img v-if="imageUrl" :src="imageUrl" class="avatar" alt="">
          <i v-else class="el-icon-plus avatar-uploader-icon" />
        </el-upload>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="submitForm('ruleForm')">立即创建</el-button>
        <el-button @click="resetForm('ruleForm')">重置</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
import { token, createSlide } from '@/api/slides'

export default {
  data() {
    return {
      ruleForm: {
        title: '',
        order: '',
        jump: '',
        imageUrl: ''
      },
      postData: {
        token: ''
      },
      imageUrl: '',
      rules: {
        title: [{
          required: true, message: '请输入活动名称', trigger: 'blur'
        }
        ],
        order: [{
          required: true, message: '请输入排序', trigger: 'blur'
        }
        ],
        jump: [{
          required: true, message: '请输入跳转地址', trigger: 'blur'
        }
        ],
        imageUrl: [{
          required: true, message: '请上传图片', trigger: 'blur'
        }
        ]
      }
    }
  },
  created() {
    this.gettoken()
  },
  methods: {
    onSubmit() {
      console.log('submit!')
    },
    submitForm(formName) {
      const vm = this
      this.$refs[formName].validate((valid) => {
        if (valid) {
          createSlide(vm.ruleForm)
          this.$router.push({ path: '/slide' })
        } else {
          return false
        }
      })
    },
    resetForm(formName) {
      this.$refs[formName].resetFields()
      this.ruleForm.imageUrl = ''
    },

    handleChange(file) {
      this.imageUrl = URL.createObjectURL(file.raw)
    },

    beforeUpload(file) {
      return true
    },
    successupload(response, file, fileList) {
      this.ruleForm.imageUrl = `http://pxchssngy.bkt.clouddn.com/${response.key}`
    },
    gettoken() {
      token().then((res) => {
        this.postData.token = res.uploadToken
      })
    }
  }
}
</script>

<style scoped>
.form-content{
  width: 880px;
  margin: 30px;
}
h1{
  margin-bottom: 40px;
}
.avatar-uploader .el-upload {
  border: 2px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}
.avatar-uploader .el-upload:hover {
  border-color: #409EFF;
}
.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 400px;
  height: 178px;
  line-height: 178px;
  text-align: center;
}
.avatar {
  width: 346px;
  height: 178px;
  display: block;
}
.avatar-uploader-icon[data-v-4bf41119] {
  font-size: 28px;
  color: #8c939d;
  width: 400px;
  height: 178px;
  line-height: 178px;
  text-align: center;
  border: 1px solid #DCDFE6;
  border-radius: 5px;
}
</style>

```

