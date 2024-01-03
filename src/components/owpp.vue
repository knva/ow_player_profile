<script setup>
import { ref, onMounted } from "vue";
import backImg from "../assets/back.png";
//表单  包含用户名，是否ow玩家，ow1段位 ow2段位 游戏模式（主要快速或者主要竞技） 游戏时间
const form = ref({
  battletag: "",
  isOWPlayer: false,
  ow1Rank: "",
  ow2Rank: "",
  gameMode: "",
  gameHour: "",
});
const color = ref("#409eff");
const font = ref("Arial");
const options = [
  {
    value: "Arial",
    label: "Arial",
  },
  {
    value: "Times New Roman",
    label: "Times New Roman",
  },
  {
    value: "serif",
    label: "serif",
  },
  {
    value: "sans-serif",
    label: "sans-serif",
  },
  {
    value: "monospace",
    label: "monospace",
  }];

const canvasWidth = ref(600);
const canvasHeight = ref(760);
const myCanvas = ref();
const herocolor = ref("#fff");
const selectedHero = ref([]);
onMounted(() => {
  const ctx = myCanvas.value.getContext("2d");
  const backgroundImage = new Image();
  backgroundImage.src = backImg;
  backgroundImage.onload = () => {
    // 绘制背景图
    ctx.drawImage(backgroundImage, 0, 0, canvasWidth.value, canvasHeight.value);
  };
});
const getCoordinates = (event) => {
  const rect = myCanvas.value.getBoundingClientRect();
  const x = event.clientX - rect.left +39;
  const y = event.clientY - rect.top +39;
  console.log(`x: ${x}, y: ${y}`);
  // 画空心圆，线条颜色为herocolor
  if (herocolor.value == "#fff") {
    return;
  }
  var select = { x: x, y: y, color: herocolor.value };
  selectedHero.value.push(select);
  drawText();
};

//撤销
const drawHeroClose = () => {
  selectedHero.value.pop();
  drawText();
};
const download = ()=>{
  const link = document.createElement('a');
  link.href = myCanvas.value.toDataURL();
  link.download = 'owpp.png';
  link.click();
  link.remove();

}

const drawHero = (xtype) => {
  let tmpcolor = "#fff";
  let curcolor = 'red'
  if (xtype == "main") {
    // 红色
    tmpcolor = "#ff4949";
   curcolor = '%23ff4949';
  } else if (xtype == "often") {
    // color黄色
    tmpcolor = "#ffcc00";
    curcolor = '%23ffcc00'
  } else if (xtype == "sometimes") {
    // color绿色
    tmpcolor = "#13ce66";
    curcolor = '%2313ce66'
  } else if (xtype == "cango") {
    // color蓝色
    tmpcolor = "#409eff";
    curcolor = '%23409eff'
  } else if (xtype == "bad") {
    // color灰色
    tmpcolor = "#909399";
    curcolor = '%23909399'
  }else{
    tmpcolor = "#fff";
    herocolor.value = tmpcolor;
    // 恢复默认鼠标
    myCanvas.value.style.cursor = "auto";
    return
  }
  let cur = `data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="100" height="100"><circle cx="50" cy="50" r="30" stroke="${curcolor}" stroke-width="5" fill="none"/></svg>`
  myCanvas.value.style.cursor = `url('${cur}') 17 17, auto`;

  herocolor.value = tmpcolor;

};
const drawText = () => {
  const ctx = myCanvas.value.getContext("2d");

  const backgroundImage = new Image();
  backgroundImage.src = backImg;
  backgroundImage.onload = () => {
    // 绘制背景图
    ctx.drawImage(backgroundImage, 0, 0, canvasWidth.value, canvasHeight.value);
    // 129 105 写form.battletag
    ctx.font = "bold 22px "+font.value;
    ctx.fillStyle = color.value;
    ctx.fillText(form.value.battletag, 129, 115);
    // 如果 isOWPlayer 为 true
    if (form.value.isOWPlayer) {
      // 197 136 画对勾
      ctx.beginPath();
      ctx.moveTo(197, 136);
      ctx.lineTo(210, 150);
      ctx.lineTo(230, 130);
      ctx.strokeStyle = color.value;
      ctx.lineWidth = 5;
      ctx.stroke();
      ctx.closePath();
    } else {
      // 270 140 画对勾
      ctx.beginPath();
      ctx.moveTo(270, 140);
      ctx.lineTo(280, 150);
      ctx.lineTo(300, 130);
      ctx.strokeStyle = color.value;
      ctx.lineWidth = 5;
      ctx.stroke();
      ctx.closePath();
    }

    // 112 169 写ow1段位
    ctx.font = "bold 22px "+font.value;
    ctx.fillStyle = color.value;
    ctx.fillText(form.value.ow1Rank, 112, 169);

    //226 167 写ow2段位
    ctx.font = "bold 22px "+font.value;
    ctx.fillStyle = color.value;
    ctx.fillText(form.value.ow2Rank, 226, 169);

    // 150 218 写游戏模式
    ctx.font = "bold 22px "+font.value;
    ctx.fillStyle = color.value;
    ctx.fillText(form.value.gameMode, 112, 198);

    //332 144 写游戏时间，需要识别换行
    // 识别换行
    var gameHour = form.value.gameHour;
    if (gameHour.indexOf("\n") > 0) {
      var gameHourArr = gameHour.split("\n");
      // 332 144 写游戏时间，需要识别换行
      ctx.font = "bold 22px "+font.value;
      ctx.fillStyle = color.value;
      ctx.fillText(gameHourArr[0], 332, 144);
      ctx.fillText(gameHourArr[1], 332, 174);
    } else {
      ctx.font = "bold 22px "+font.value;
      ctx.fillStyle = color.value;
      ctx.fillText(form.value.gameHour, 332, 144);
    }

    // 循环 selectedHero
    selectedHero.value.forEach((item) => {
      ctx.beginPath();
      ctx.arc(item.x, item.y, 35, 0, 2 * Math.PI);
      ctx.strokeStyle = item.color;
      ctx.lineWidth = 5;
      ctx.stroke();
      ctx.closePath();
    });
  };
};
</script>

<template>
  <div class="common-layout">
    <el-container>
      <el-aside width="200px">
        <el-form>
          <el-form-item label="文字颜色">
            <el-color-picker v-model="color"></el-color-picker>
          </el-form-item>
          <!--字体选择-->
          <el-form-item label="字体">
            <el-select v-model="font" placeholder="请选择">
              <el-option
                v-for="item in options"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              />
            </el-select>
          </el-form-item>

          <el-form-item label="用户名">
            <el-input
              v-model="form.battletag"
              placeholder="请输入用户名"
            ></el-input>
          </el-form-item>
          <el-form-item label="是否OW玩家">
            <el-switch
              v-model="form.isOWPlayer"
              active-color="#13ce66"
              inactive-color="#ff4949"
            ></el-switch>
          </el-form-item>
          <el-form-item label="OW1段位">
            <el-input
              v-model="form.ow1Rank"
              placeholder="请输入OW1段位"
            ></el-input>
          </el-form-item>
          <el-form-item label="OW2段位">
            <el-input
              v-model="form.ow2Rank"
              placeholder="请输入OW2段位"
            ></el-input>
          </el-form-item>
          <el-form-item label="游戏模式">
            <el-input
              v-model="form.gameMode"
              placeholder="请输入游戏模式"
            ></el-input>
          </el-form-item>
          <el-form-item label="游戏时间">
            <!--textarea-->
            <el-input
              v-model="form.gameHour"
              :rows="2"
              type="textarea"
              placeholder="请输入游戏时间"
            ></el-input>
          </el-form-item>
        </el-form>
        <el-space direction="vertical">
          <el-button type="primary" @click="drawText">生成图片</el-button>

          <el-button type="primary" @click="download">下载图片</el-button>
          <!-- 主要英雄选择按钮-->
          <el-button type="danger" @click="drawHero('main')"
            >主要玩的英雄</el-button
          >
          <!-- 次要玩的英雄-->
          <el-button type="warning" @click="drawHero('often')"
            >次要玩的英雄</el-button
          >
          <!-- 偶尔玩的英雄选择-->
          <el-button type="success" @click="drawHero('sometimes')"
            >偶尔玩的英雄</el-button
          >
          <!-- 勉强能玩的英雄-->
          <el-button type="primary" @click="drawHero('cango')"
            >勉强能玩的英雄</el-button
          >
          <!-- 不能玩的英雄-->
          <el-button type="info" @click="drawHero('bad')"
            >不能玩的英雄</el-button
          >
        <!-- 关闭-->
        <el-button type="primary" @click="drawHero('close')"
          >关闭绘制</el-button
        >
        <!-- 撤销-->
        <el-button type="primary" @click="drawHeroClose">撤销</el-button>


      </el-space>
      </el-aside>
      <el-main>
        <canvas
          ref="myCanvas"
          :width="canvasWidth"
          :height="canvasHeight"
          @click="getCoordinates"
        ></canvas>
      </el-main>
    </el-container>
  </div>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
