<template>
  <v-container class="pa-2" fluid>
    <v-row>
      <v-col>
        <v-card max-width="344" class="mx-auto">
          <v-card-title>哔哩哔哩2019DASH直传压制方案筛选工具web版V1.0</v-card-title>
          <v-card-text>
            <div class="text--primary">
              <!-- context -->
            </div>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
    <v-row>
      <v-col>
        <v-card max-width="344" class="mx-auto">
          <v-card-text>
            <div class="text--primary">{{cardtext}}</div>
          </v-card-text>
          <v-card-text :style="cardindp">
            <v-text-field :label="cardlabel" @keyup="keyup()" v-model="cardinput"></v-text-field>
          </v-card-text>
          <v-card-actions>
            <v-btn text @click="onbtn(1)" :style="cardbtndp">{{cardbtn}}</v-btn>
            <v-btn text @click="onbtn(2)" :style="cardbtndp2">{{cardbtn2}}</v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  data: () => ({
    cardtext: "点击开始运行压制方案",
    cardinput: "",
    cardbtn: "Next",
    cardbtn2: "",
    cardlabel: "",
    cardindp: "display:none",
    cardbtndp: "display:block",
    cardbtndp2: "display:none",
    texts: [],
    step: 0,
    res: [0, 0], //分辨率 [宽,高]
    product: 0,
    interlace: 0,
    fps: 0,
    hfps: 0
  }),
  methods: {
    keyup: function () {
      if (event.keyCode == 13) this.onbtn(1)
    },
    setcard: function (text, btn, step) {
      this.cardtext = text;
      this.switchbtn(btn);
      this.step = step;
      console.log(step)
    },
    switchbtn: function (mode) {
      if (mode == 1) {
        this.cardbtn = "Next";
        this.cardbtndp = "display:block";
        this.cardbtndp2 = "display:none";
        this.cardindp = "display:block";
      } else if (mode == 2) {
        this.cardbtn = "是";
        this.cardbtn2 = "否";
        this.cardbtndp = "display:block";
        this.cardbtndp2 = "display:block";
        this.cardindp = "display:none";
      } else if (mode == 3) {
        this.cardbtn = "重新运行";
        this.cardbtndp = "display:block";
        this.cardbtndp2 = "display:none";
        this.cardindp = "display:none";
      }
    },
    onbtn: function (b) {
      var input = this.cardinput;
      this.cardinput = "";
      var res;
      var newproduct;
      var ratio;
      switch (this.step) {
        case 10000:
        case 9999:
        case 0:
          this.setcard("输入分辨率(宽x高 例如1920x1080)", 1, 1);
          break;
        case 1:
          res = input.split("x");
          res[0] = parseInt(res[0]);
          res[1] = parseInt(res[1]);
          if (res[0] <= 144 || res[1] <= 144) {
            this.setcard("分辨率过小", 3, 9999);
            return;
          }
          if (res[0] % 2 != 0 || res[1] % 2 != 0) {
            this.setcard("分辨率不合法，必须为偶数", 3, 9999);
            return;
          }
          this.res = res;
          this.product = res[0] * res[1];
          this.setcard("请问您的视频是否为交错（隔行扫描）视频？", 2, 2);
          break;
        case 2:
          if (b == 1) {
            this.interlace = 1;
          } else {
            this.interlace = 0;
          }
          if (this.product <= 921600) {
            this.setcard("输入视频帧率", 1, 21);
          } else {
            if (this.product > 2073600) {
              res = this.res;
              newproduct = res[0]*res[1];
              ratio = res[0] / res[1];
              if (res[0] % 4 != 0) {
                res[0] = res[0] - 2;
              }
              while (newproduct > 2073600) {
                res[0] = res[0] - 4;
                res[1] = Math.round(res[0] / ratio);
                newproduct = res[0] * res[1];
              }
              if (res[1] % 4 != 0) {
                res[1] = res[1] - (res[1] % 4);
              }
              if (this.interlace == 1) {
                this.setcard("请输入宽度" + res[0] + "和高度" + res[1] + "，并按照码率B来处理，并按照手册文末问答2执行反交错操作", 3, 10000);
                return;
              } else {
                this.setcard("请输入宽度" + res[0] + "和高度" + res[1] + "，并按照码率B来处理", 3, 10000);
                return;
              }
            } else {
              if (this.interlace == 1) {
                this.setcard("请勾选保持原分辨率，按照码率B处理，并按照手册文末问答2执行反交错操作", 3, 10000);
                return;
              } else {
                this.setcard("请勾选保持原分辨率，并按照码率B处理", 3, 10000);
                return;
              }
            }
          }
          break;
        case 21:
          this.fps = parseInt(input);
          if (this.fps > 60) {
            this.setcard("帧率过高，请使用Handbrake（用法见问6附）或非编软件缩小至60FPS", 3, 9999);
            return;
          } else if (this.fps >= 48) {
            this.hfps = 1;
          } else {
            this.hfps = 0;
          }
          if (this.hfps == 0 && this.interlace == 0) {
            res = this.res;
            newproduct = res[0]*res[1];
            ratio = res[0] / res[1];
            if (res[0] % 4 != 0) {
              res[0] = res[0] + 2;
            }
            console.log('-----------')
            console.log(res)
            console.log(ratio)
            console.log(newproduct)
            console.log('-----------')
            while (newproduct <= 921600) {
              res[0] = res[0] + 4;
              res[1] = Math.round(res[0] / ratio);
              newproduct = res[0] * res[1];
              console.log('-----------')
              console.log(res)
              console.log(ratio)
              console.log(newproduct)
              console.log('-----------')
            }
            if (res[1] % 4 != 0) {
              res[1] = res[1] + (4 - (res[1] % 4));
            }
            this.setcard("请输入宽度" + res[0] + "和高度" + res[1] + "，并按照码率B来处理", 3, 10000);
            return;
          } else {
            if (this.product <= 409920) {
              res = this.res;
              newproduct = res[0]*res[1];
              ratio = res[0] / res[1];
              if (res[0] % 4 != 0) {
                res[0] = res[0] + 2;
              }
              while (newproduct <= 409920) {
                res[0] = res[0] + 4;
                res[1] = Math.round(res[0] / ratio);
                newproduct = res[0] * res[1];
              }
              if (res[1] % 4 != 0) {
                res[1] = res[1] + (4 - (res[1] % 4));
              }
              if (this.interlace == 1) {
                this.setcard("请输入宽度" + res[0] + "和高度" + res[1] + "，并按照码率A来处理，并按照手册文末问答2执行反交错操作", 3, 10000);
                return;
              } else {
                this.setcard("请输入宽度" + res[0] + "和高度" + res[1] + "，并按照码率A来处理", 3, 10000);
                return;
              }
            } else {
              if (this.interlace == 1) {
                this.setcard("请勾选保持原分辨率，按照码率A处理，并按照手册文末问答2执行反交错操作", 3, 10000);
                return;
              } else {
                this.setcard("请勾选保持原分辨率，并按照码率A处理", 3, 10000);
                return;
              }
            }
          }
      }
    }
  }
};
</script>
