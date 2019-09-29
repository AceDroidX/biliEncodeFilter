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
            <v-text-field :label="cardlabel" v-model="cardinput"></v-text-field>
          </v-card-text>
          <v-card-actions>
            <v-btn text @click="run(1)" :style="cardbtndp">{{cardbtn}}</v-btn>
            <v-btn text @click="run(2)" :style="cardbtndp2">{{cardbtn2}}</v-btn>
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
    res: [0, 0],
    product: 0,
    interlace: 0,
    fps: 0,
    hfps: 0
  }),
  methods: {
    switchbtn: mode => {
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
      }
    },
    run: b => {
      var input = this.cardinput;
      this.cardinput = "";
      switch (this.step) {
        case 0:
          this.cardindp = "display:block";
          this.cardtext = "输入分辨率(宽x高 例如1920x1080)";
          var res = input.split("x");
          res[0] = parseInt(res[0]);
          res[1] = parseInt(res[1]);
          if (res[0] <= 144 || res[1] <= 144) {
            this.cardtext = "分辨率过小";
            this.step = 9999;
            return;
          }
          if (res[0] % 2 != 0 || res[1] % 2 != 0) {
            this.cardtext = "分辨率不合法，必须为偶数";
            this.step = 9999;
            return;
          }
          this.res = res;
          this.product = res[0] * res[1];
          this.step = 1;
          break;
        case 1:
          this.switchbtn(2);
          this.cardtext = "请问您的视频是否为交错（隔行扫描）视频？";
          if (b == 1) {
            this.interlace = 1;
          } else {
            this.interlace = 0;
          }
          if (this.product <= 921600) {
            this.step = 21;
          } else {
            this.step = 22;
          }
          break;
        case 21:
          this.switchbtn(1);
          this.cardtext = "输入视频帧率";
          this.fps = parseInt(input);
          if (this.fps > 60) {
            this.cardtext =
              "帧率过高，请使用Handbrake（用法见问6附）或非编软件缩小至60FPS";
            this.step = 9999;
            return;
          } else if (this.fps >= 48) {
            this.hfps = 1;
          } else {
            this.hfps = 0;
          }
          if (this.hfps == 0 && this.interlace == 0) {
            ////////////////////////
          } else {
            if (this.product <= 409920) {
              //////////
            } else {
              if (this.interlace == 1) {
                this.cardtext =
                  "请勾选保持原分辨率，按照码率A处理，并按照手册文末问答2执行反交错操作";
                this.step = 10000;
                return;
              } else {
                this.cardtext = "请勾选保持原分辨率，并按照码率A处理";
                this.step = 10000;
                return;
              }
            }
          }
      }
    }
  }
};
</script>
