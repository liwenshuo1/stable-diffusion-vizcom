<template>
  <div class="aides-container">
    <h3 style="line-height: 2;">prompt</h3>
    <Input v-model="form.prompt" type="textarea" :rows="4" placeholder="您准备生成什么" />

    <h3 style="line-height: 2;">style</h3>
    <h3 style="line-height: 2;">生成的图片数量</h3>
    <RadioGroup v-model="form.batch_size">
      <Radio :label="1"></Radio>
      <Radio :label="2"></Radio>
      <Radio :label="4"></Radio>
      <Radio :label="5"></Radio>
    </RadioGroup>
    <Button type="primary" @click="sure" class="sure-btn">确定生成图片</Button>

  </div>
  <Loading :show="loadingShow"></Loading>
</template>

<script lang="ts"  setup>
import { ImagePreview } from 'view-ui-plus';
import vfe from 'vfe';
const url = '/api'
import axios from 'axios';
const canvasEditor = inject('canvasEditor') as vfe.ICanvas;
import Loading from '@/components/loading.vue'

// {
//   "enable_hr": false,                 // 开启高清hr
//   "denoising_strength": 0,            // 降噪强度
//   "hr_scale": 2,                      // 高清级别
//   "hr_upscaler": "string",
//   "hr_second_pass_steps": 0,
//   "hr_resize_x": 0,
//   "hr_resize_y": 0,
//   "hr_sampler_name": "string",
//   "hr_prompt": "",
//   "hr_negative_prompt": "",
//   "prompt": "",                       // 正向关键字
//   "styles": [
//     "string"
//   ],
//   "seed": -1,                         // 随机种子
//   "subseed": -1,                      // 子级种子
//   "subseed_strength": 0,              // 子级种子影响力度
//   "seed_resize_from_h": -1,
//   "seed_resize_from_w": -1,
//   "sampler_name": "string",
//   "batch_size": 1,                    // 每次生成的张数
//   "n_iter": 1,                        // 生成批次
//   "steps": 50,                        // 生成步数
//   "cfg_scale": 7,                     // 关键词相关性
//   "width": 512,                       // 生成图像宽度
//   "height": 512,                      // 生成图像高度
//   "restore_faces": false,             // 面部修复
//   "tiling": false,                    // 平铺
// init_images: [sourceImage.value],
//   "do_not_save_samples": false,
//   "do_not_save_grid": false,
//   "negative_prompt": "string",        // 反向关键字
//   "eta": 0,                           // 等待时间
//   "s_min_uncond": 0,
//   "s_churn": 0,
//   "s_tmax": 0,
//   "s_tmin": 0,
//   "s_noise": 1,
//   "override_settings": {},             // 覆盖性配置
//   "override_settings_restore_afterwards": true,
//   "script_args": [],                   // lora 模型参数配置
//   "sampler_index": "Euler",            // 采样方法
//   "script_name": "string",
//   "send_images": true,                 // 是否发送图像
//   "save_images": false,                // 是否在服务端保存生成的图像
//   "alwayson_scripts": {}               // alwayson配置
// }


const prompt = ref('')
const loadingShow = ref(false)
const form = reactive({
  prompt: '',
  steps: 10,
  batch_size: 2, //每次张数
  init_images: []
})

function sure() {
  loadingShow.value = true
  setColor('rgba(0,0,0,0)')
  canvasEditor.preview().then((dataUrl) => {
    // const dataUrl = getImgUrl();
    setColor('rgba(255,255,255,1)')
    form.init_images[0] = dataUrl

    axios.post(`${url}/sdapi/v1/img2img`, {
      ...form
    }).then(res => {
      console.log('res, rees', res);
      loadingShow.value = false
      const imgArr = res.data.images.map(item => `data:image/png;base64,${item}`)
      ImagePreview.show({
        // previewList: [`data:image/png;base64,${res.data.images[0]}`],
        previewList: imgArr
      });
    })
    console.log(dataUrl);

  });

  // 背景颜色设置
  function setColor(color) {
    const workspace = canvasEditor.canvas.getObjects().find((item) => item.id === 'workspace');
    workspace.set('fill', color);
    canvasEditor.canvas.renderAll();
  }

}

</script>
<style lang='less' scoped>
.aides-container {

  padding: 10px;
  padding-bottom: 60px;

  .sure-btn {
    // position: absolute;
    margin-top: 30px;
    margin-left: 50%;
    bottom: 20px;
    left: 50%;
    transform: translateX(-50%);
  }
}
</style>