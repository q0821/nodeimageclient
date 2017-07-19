<template>
    <div>
      <div class="selectFrameBlock">
        <label>請選擇要套用的外框</label>
        <select id="selectFrame" name="frame" class="form-control" v-model="frame">
          <option value="01">01</option>
          <option value="02">02</option>
          <option value="03">03</option>
          <option value="04">04</option>
          <option value="05">05</option>
        </select>
      </div>
      <slim-cropper :options="slimOptions"/>
      <div class="output">
        <img src='#' />
        <div class="btn-group btn-group-justified">
          <div class="btn-group">
            <a href="#" class="btn btn-default" role="button">長按圖片儲存</a>
          </div>
          <div class="btn-group">
            <a href="#" class="btn btn-default" role="button" v-on:click="reload">再做一張</a>
          </div>
        </div>
      </div>
    </div>
</template>

<script>
import Slim from './slim/slim.vue'
import Vue from 'vue'
import axios from 'axios'
import VueAxios from 'vue-axios'
Vue.use(VueAxios, axios)

// called when upload button is pressed or automatically if push is enabled
function slimService (formdata, progress, success, failure, slim) {
  // const config = { headers: { 'content-type': 'multipart/form-data' } }
  var room = window.location.pathname
  var frame = document.getElementById('selectFrame').value
  axios.post('http://hd.tellustek.com:3000/send' + room + '/' + frame + '/', formdata)
  .then(function (res) {
    console.log(res.data)
    document.querySelector('.output img').setAttribute('src', res.data.path)
    document.querySelector('.output').classList.add('active')
    document.querySelector('.selectFrameBlock').classList.add('hidden')
    document.querySelector('.slim').classList.add('hidden')
    // console.log(document.querySelector('.output img'))
    success(res.data)
  })
  .catch(function (err) {
    console.log(err)
    failure(err)
  })
  console.log(progress, success, failure)
}

export default {
  name: 'index',
  components: {
    'slim-cropper': Slim
  },
  data () {
    return {
      frame: '02',
      slimOptions: {
        ratio: '1375:885',
        // initialImage: require('../assets/test.jpg'),
        push: true,
        service: slimService,
        instantEdit: true,
        label: '點我上傳圖片',
        labelLoading: '讀取中',
        buttonEditLabel: '編輯',
        buttonUploadLabel: '上傳',
        buttonConfirmLabel: '完成',
        buttonRemoveLabel: '刪除',
        buttonCancelLabel: '取消',
        buttonEditTitle: '編輯',
        buttonUploadTitle: '上傳',
        buttonConfirmTitle: '完成',
        buttonRemoveTitle: '刪除',
        buttonCancelTitle: '取消',
        defaultInputName: 'sampleFile'
      }
    }
  },
  methods: {
    reload: function () {
      location.reload()
    }
  }
}
</script>
<style type="text/css" media="screen">
  .slim{
    width: 98vw;
    height: 63vw;
    margin: 0 auto;
  }
  .output{
    opacity: 0;
  }
  .output.active {
    opacity: 1;
    width: 100%;
    margin: 0 auto;
  }
  img{
    max-width: 100%;
  }

</style>
