<!--
 * @Author: bigFace2019 599069310@qq.com
 * @Date: 2023-04-09 11:19:07
 * @LastEditors: 秦少卫
 * @LastEditTime: 2023-07-05 00:58:02
 * @FilePath: \vue-fabric-editor\src\components\preview.vue
 * @Description: 预览组件
-->
<template>
  <Button type="text" @click="preview">
    生成图片相关设置
  </Button>
</template>

<script lang="ts" setup>
import { ImagePreview } from 'view-ui-plus';
import vfe from 'vfe';
import axios from 'axios';

const url = '/api'

const canvasEditor = inject('canvasEditor') as vfe.ICanvas;
const preview = () => {
  canvasEditor.preview().then((dataUrl) => {
    // const dataUrl = getImgUrl();

    axios.post(`${url}/sdapi/v1/txt2img`, {
      "prompt": "little cat",
      "steps": 5,
      "batch_size": 2, //每次张数
    }).then(res => {
      console.log('res, rees', res);

      const imgArr = res.data.images.map(item => `data:image/png;base64,${item}`)
      ImagePreview.show({
        // previewList: [`data:image/png;base64,${res.data.images[0]}`],
        previewList: imgArr
      });
    })
    console.log(dataUrl);

  });
};
</script>

<style scoped lang="less"></style>
